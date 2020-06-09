[Back to Index](../README.md)

# General Concepts

- Main APP & SDK Services:
	- Discovery: Find applications and corresponding backends (GET)
		- Endpoint: http://<host:port>/covid/discovery/discovery.json
		- Endpoint: http://<host:port>/covid/discovery/discovery_dev.json (developer env)
		- Body: --
	- Bucket: Get the Keys of all infected people since a given start date (GET)
		- Endpoint: http://<host:port>/covid/api/v1/exposed/{batchReleaseTime}
		- Body: initial time for the batch provided as an URL parameter
	- Report: I've been infected and send the private Key of the first time I was contagious (POST)
		- Endpoint: http://<host:port>/covid/api/v1/exposed
		- Body: ExposeeRequest (org.dpppt.android.sdk.internal.backend.models.ExposeeRequest). Authdata is the code entered in the app.
		```
		{"authData":{"value":"112235841284"},"fake":0,"key":"V8QE/CpnApN2/QEq0/wmD7zJk3cqRePBpEhRrGeJOX4=","keyDate":1589068800000}
		```

