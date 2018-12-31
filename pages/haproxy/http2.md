# How to Enable HTTP2 in HAProxy

*Source*

Based on this [blog post](https://ops.tips/blog/haproxy-http2/).

*Create a certificate*

This is an easy way to generate a self-signed certificate. Not a recommended production setup!
```
$ openssl req   \
          -x509 \
		  -sha256 \
		  -newkey rsa:4096 \
		  -keyout "test.com.key" 
		  -out "test.com.pem" \
		  -days 730 \
		  -nodes \
		  -subj //C=BR/ST=SaoPaulo/L=SaoPaulo/O=TestOrg/OU=TestUnit/CN=test.com
```

Concatenate both private and public keys and pass them to HAProxy
```
cat ./test.com.pem ./test.com.key > haproxy_test.com
```

Dockerfile

```
FROM haproxy
COPY haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg
COPY haproxy_test.com /usr/local/etc/haproxy/certs/
COPY 404.http /tmp/sample/errorfiles/
```

Build and run the Docker container
```
docker build .
docker run -d --name haproxy-https -p 443:443 -p 80:80 [IMAGE_ID]
```

Add test.com to the etc/hosts file

haproxy.cfg
```
global
# Setting the maximum size of the Diffie-hellman parameters
# used in the TLS negotiation such that HAProxy won't warn us 
# about the low default value of 1024.
                tune.ssl.default-dh-param   2048


defaults
# As we're sticking with using HTTP/1.1 all the way
# we can set the mode in the `defaults` section and
# have it applied everywhere.
#
# If you set only in the frontend, then haproxy will
# complain about letting backend be tcp.
                mode http


frontend        https
# Binding to port 443 forces us to have higher privileges
# when launching HAProxy, but it allows us not to have to
# set the `:<port>` in the URL when connecting to it using
# a browser (as `https` will imply port 443).
#
# The certificate set here is the one that contains both the
# private key and the actual `.pem` that we generated using
# openssl.
#
# ps.: if you have *a bunch* of certificates to serve, then 
# you should switch to `crt-list` as HAProxy has a limit on
# the size of each line in this config file.
                bind                        *:443  ssl crt /usr/local/etc/haproxy/certs/haproxy_test.com alpn h2,http/1.1

# Simple ACL to send traffic to our custom tailored web server.
#
# This way we can exercise a "not found" when not having `HOST`
# properly set.
#
# This also demonstrates how HAProxy is indeed decyphering the
# traffic (otherwise it wouldn't catch the Host header).
                acl                         host_matches hdr_dom(host) test.com
                use_backend                 desired_backend if host_matches
                default_backend             not_found


# Our server that serves `/tmp/sample/www` using python's 
# SimpleHTTPServer module.
backend         desired_backend
                server                      myserver 192.168.1.134:8000/hello



# A dumb "not-found" auto-responder using HAProxy's errorfile
# directive.
#
# As the ACL that send traffic to this backend is the default
# (and least prioritized), when we reach this backend, it means
# that we didn't find a desired backend, thus we should serve
# a 404 instead of the generic 503 that haproxy gives back to
# clients when something goes wrong.
backend         not_found
                errorfile                   503 /tmp/sample/errorfiles/404.http
```				