# Add encryption to a Spring Cloud Config

See [here](java/cryto.md) on how to setup a certificate. This section describes how to activate encryption in a Spring Cloud Config server.

*New properties to use*
```
# This can be an external location instead!
# Given by an administration during deployment time
encrypt.keyStore.location=classpath:/server.jks
# These properties can be provided as environment variables (more secure!)
encrypt.keyStore.password=letmein
encrypt.keyStore.alias=mytestkey
encrypt.keyStore.secret=changeme
```

*Encryption endpoint for administrator usage*

This endpoints allows encryption/decryption of single properties:

```
# Decryption
curl -s -X POST http://xxx.xxx.xxx.xxx:8090/admin/config/decrypt -d [ENCRYPTED_TEXT]

# Encryption
curl -s -X POST http://xxx.xxx.xxx.xxx:8090/admin/config/encrypt -d myPassword 
```
