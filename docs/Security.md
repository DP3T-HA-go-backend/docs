[Back to Index](../README.md)

## Security concepts

### Identity and content integrity

*Note: this section describes the implementation coded [here](https://github.com/HiEST/DP3T-backend/blob/master/services/public/exposed/main.go).*

The call `/v1/exposed/:time` is responsible to return to the APP the list of keys from people tested positive. This is a critical interaction between the backend and the client, and trust between the parties is crucial. For this purpose, the client and the server use JWT (JSON Web Tokens) to sign the communications. The data encoding protocol is ProtocolBuffers.

To enable the secure communication, the server contains a ES256 (RFC7518) key pair, from which the server keeps both keys and the client only the public one. When the server generates a response, it includes in the headers a `signature` field that encloses the signature of the message. The signature icludes several fields (like the issuer or the expiracy date), but most importantly, it includes also a SHA-256 digest of the body (encoded in base64) of the response. Therefore, the client can verify the identity of the server (the only entity that has the private key) and also the contents of the payload (the digest value included in the JWT).

An example of the signature header is the following:
```
signature: eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJiYXRjaC1yZWxlYXNlLXRpbWUiOiIxNTg5MTg1OTQwOTEzIiwiY29udGVudC1oYXNoIjoicmlOSFlIbG8vTjJsVytaNGdDUXJ6Yy9QNXhQbm9Lc01sUjFaTGtSTVBKND0iLCJleHAiOiIxNTkxMDAwMzQwIiwiaGFzaC1hbGciOiJzaGEyNTYiLCJpYXQiOiIxNTg5MTg1OTQwIiwiaXNzIjoiZDNwdCJ9.Ppjm5DSlknJP7aKttK8OmaUqgEF9SbZs-rZGMBcHhJdVyDzVXqisGLILjA9Z-pItNXslHR8UZGbvZqLwRUrFjw
```

This signature contains a header:
```
{
  "alg": "ES256",
  "typ": "JWT"
}
```

and a payload:
```
{
  "batch-release-time": "1589185940913",
  "content-hash": "riNHYHlo/N2lW+Z4gCQrzc/P5xPnoKsMlR1ZLkRMPJ4=",
  "exp": "1591000340",
  "hash-alg": "sha256",
  "iat": "1589185940",
  "iss": "d3pt"
}
```

The `content-hash` field is the SHA-256 hash of the payload: in this case, the protobuf encoded array of keys of people tested positive.

Both the header and the payload are then converted to base64 format, and combined and encrypted at the server side using the private key. The output of this process is the signature. A good description of the process is provided [here](https://www.sohamkamani.com/golang/2019-01-01-jwt-authentication/).


### Key generation

To generate the key pair you can run the following commands:

```
openssl ecparam -genkey -name prime256v1 -noout -out ec256-key
openssl ec -in  ec256-key -pubout -out ec256-key.pub
```
This will generate two keys: 
	- Private: ec256-key
	- Public: ec256-key.pub

Examples of the keys:

*Private:*
```
-----BEGIN EC PRIVATE KEY-----
MHcCAQEEIM0eowQhTYwRilHgsKk0eRLwGR8nxZJzA9ErGwi6E80OoAoGCCqGSM49
AwEHoUQDQgAEKzY6N0Eo4L8mhQGvPgEjFWDePpC5Jarts5H+usC0WWLROJ+WQ160
cZTBwHz2/ToVJFb3ltd0MsVCXijFD1rVgQ==
-----END EC PRIVATE KEY-----
```

*Public:*
```
-----BEGIN PUBLIC KEY-----
MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEKzY6N0Eo4L8mhQGvPgEjFWDePpC5
Jarts5H+usC0WWLROJ+WQ160cZTBwHz2/ToVJFb3ltd0MsVCXijFD1rVgQ==
-----END PUBLIC KEY-----
```

The key that is used in the client side (and that is also returned as a header by the server) needs to be encoded in base64.

This can be achieved using the following command:

```
openssl base64 -in ec256-key.pub
```

and resulting in the following string for the public key example shown before:

```
LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUZrd0V3WUhLb1pJemowQ0FRWUlL
b1pJemowREFRY0RRZ0FFS3pZNk4wRW80TDhtaFFHdlBnRWpGV0RlUHBDNQpKYXJ0
czVIK3VzQzBXV0xST0orV1ExNjBjWlRCd0h6Mi9Ub1ZKRmIzbHRkME1zVkNYaWpG
RDFyVmdRPT0KLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tCg==
```

This is the *bucket* public key string used by the APP [code](https://github.com/DP-3T/dp3t-app-android/blob/develop/app/build.gradle).



### Notes

*Note: ES256 is an Asymmetric Key Cryptography algorithm Elliptic Curve Digital Signature Algorithm using P-256 and SHA-256*

*Note: JWT signatures can be decoded online at jwt.io*

[Example signature decoded](https://jwt.io/#debugger-io?token=eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJiYXRjaC1yZWxlYXNlLXRpbWUiOiIxNTg5MTg1OTQwOTEzIiwiY29udGVudC1oYXNoIjoicmlOSFlIbG8vTjJsVytaNGdDUXJ6Yy9QNXhQbm9Lc01sUjFaTGtSTVBKND0iLCJleHAiOiIxNTkxMDAwMzQwIiwiaGFzaC1hbGciOiJzaGEyNTYiLCJpYXQiOiIxNTg5MTg1OTQwIiwiaXNzIjoiZDNwdCJ9.Ppjm5DSlknJP7aKttK8OmaUqgEF9SbZs-rZGMBcHhJdVyDzVXqisGLILjA9Z-pItNXslHR8UZGbvZqLwRUrFjw)


