[Back to Index](../README.md)

## Potinting to the your custom backend

- Backend HTTP logs can be found in the login node, in the folder:
```
/var/log/nginx/covid-access.log
```


- Everytime that the SDK library is updated, the following steps MUST be followed:

```
rm -rf dp3t-app-android/sdk-production-debug/build
cp dp3t-sdk-android/dp3t-sdk/sdk/build/outputs/aar/sdk-production-debug.aar dp3t-app-android/sdk-production-debug/
```

After that, go back to the APP environment in Android Studia and click "Sync Project with Gradle Files"

- SDK file dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/DiscoveryRepository.java contains:
  baseUrl("https://<host:port>/covid/discovery/") where the discovery service is located

  - https://<host:port>/covid/discovery/discovery.json
  - https://<host:port>/covid/discovery/discovery_dev.json


```
{
    "applications": [{
        "appId": "<appID here>",
        "description": "This is the BSC testing app of the DP-3T SDK",
        "bucketBaseUrl": "https:/<host:port>/covid/api/",
        "reportBaseUrl": "https://<host:port>/covid/api/",
        "contact": "Barcelona Supercomputing Center (BSC-CNS)"
    }]
}
```

- The discovery service is currently serving at http://<host:port>/discovery.json:
https://github.com/DP-3T/dp3t-sdk-android/blob/0916c5046bc541b63010dccfec69536a73ff8a73/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/DiscoveryService.java
<br>This results in an error until certificates are not added.
https://stackoverflow.com/questions/21047414/javax-net-ssl-sslhandshakeexception-java-security-cert-certpathvalidatorexcepti

- The appId should be set for the application:
  - https://github.com/DP-3T/dp3t-app-android/blob/dc75d3f8f94cebea1beaad4740f97add7ec116f6/app/src/main/java/org/dpppt/android/app/MainApplication.java#L46

- If HTTP is used instead of HTTPs, the domains allowed for plain use must be listed in `APP/res/xml/network_security_config`:

```
<network-security-config>
	<domain-config cleartextTrafficPermitted="true">
		<domain includeSubdomains="true"><host:port></domain>
		<domain includeSubdomains="true"><host:port></domain>
	</domain-config>
	<base-config>
		<trust-anchors>
			<certificates src="system" />
			<!-- Trust user added CAs while debuggable only -->
			<certificates src="user" />
		</trust-anchors>
	</base-config>
</network-security-config>
```
