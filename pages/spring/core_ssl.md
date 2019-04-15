Built-in HTTPS support
----------------------

Spring-Boot has a built-in support for HTTPS that can be enabled/disabled via settings:

```
server:
  port: 8446
  # SSL settings
  ssl:
    enabled: true
    key-store-type: ${SERVER_KEYSTORE_TYPE:PKCS12}
    key-store: ${SERVER_KEYSTORE_PATH:classpath:test_cert.p12}
    key-store-password: ${SERVER_KEYSTORE_PASSWORD:letmein}
    key-alias: ${SERVER_KEY_ALIAS:test_cert}
```

*Note: `Eureka` settings must be updated as well.*

```
eureka:
  instance:
	# This needs to be disable when using HTTPS
    nonSecurePortEnabled: false
	# This needs to be enabled when using HTTPS
    securePortEnabled: true
	# Thess settings needs to reflect HTTPS in the URL + the port used
    homePageUrl: https://${eureka.hostname}:8446/
    statusPageUrl: https://${eureka.hostname}:8446/info
```

Using HTTPS with RestTemplate
-----------------------------

Once we are exposing our endpoint through HTTPS, we need to adapt the calls made to them.

Spring Boot `RESTTemplate` is easily configurable to use HTTPS when invoking an endpoint:

```	
// Build a SSLConnectionSocketFactory with the certificate details
SSLConnectionSocketFactory socketFactory = new SSLConnectionSocketFactory(
		new SSLContextBuilder()
				// Currently it seems that we can work with self-signed certificates
				// for inter-service communication
				.loadTrustMaterial(null, new TrustSelfSignedStrategy())
				.loadKeyMaterial(keyStore(ssl), ssl.getKeyStorePassword().toCharArray())
				.build(),
		NoopHostnameVerifier.INSTANCE);

// Create the RESTTemplate with the previous SSLConnectionSocketFactory
HttpClient httpClient = HttpClients.custom().setSSLSocketFactory(socketFactory).build();
ClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory(httpClient);

RestTemplate restTemplate = new RestTemplate(requestFactory);
```	
	
This method is used to load the `KeyStore` details (taken from the configuration properties)
```		
	private KeyStore keyStore(Ssl ssl) throws KeyStoreException, NoSuchAlgorithmException, CertificateException, FileNotFoundException, IOException {
		KeyStore keyStore = KeyStore.getInstance(ssl.getKeyStoreType());
		// FIXME Is this really necessary?
		Resource keyStoreResource = resourceLoader.getResource(ssl.getKeyStore());
		keyStore.load(keyStoreResource.getInputStream(),ssl.getKeyStorePassword().toCharArray());
		return keyStore;
	}
```	
