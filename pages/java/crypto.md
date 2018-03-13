# Use the cryptographic extensions in Java

*Installation*

Download from [here](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html) and copy to the local Java home folder.

*Create a certificate*

This is an easy way to generate a self-signed certificate. Not a recommended production setup!
```
$ keytool -genkeypair -alias mytestkey -keyalg RSA \ 
          -dname "CN=WebServer,OU=Unit,O=Organization,L=City,S=State,C=US" \ 
          -keypass changeme -keystore server.jks -storepass letmein
```