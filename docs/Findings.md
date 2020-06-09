[Back to Index](../README.md)

## Findings

- builld.graddle in "/app" folder of the App repository contains:
  BUCKET_PUBLIC_KEY

- Frontend strings contained at:
```
app/src/main/res/values/strings.xml
```


- When I want to get my updates for exposed:
  - https://github.com/DP-3T/dp3t-app-android/blob/dc75d3f8f94cebea1beaad4740f97add7ec116f6/app/src/main/java/org/dpppt/android/app/MainApplication.java#L54

- The reporting service is called here:
  - https://github.com/DP-3T/dp3t-sdk-android/blob/0916c5046bc541b63010dccfec69536a73ff8a73/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/BackendReportRepository.java#L39
  - https://github.com/DP-3T/dp3t-sdk-android/blob/0916c5046bc541b63010dccfec69536a73ff8a73/dp3t-sdk/sdk/src/main/proto/exposed.proto
  - https://github.com/DP-3T/dp3t-sdk-android/blob/0916c5046bc541b63010dccfec69536a73ff8a73/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/ReportService.java


- The bucket service is called here:
   - https://github.com/DP-3T/dp3t-sdk-android/blob/0916c5046bc541b63010dccfec69536a73ff8a73/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/SyncWorker.java#L142
   - https://github.com/DP-3T/dp3t-sdk-android/blob/0916c5046bc541b63010dccfec69536a73ff8a73/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/BucketService.java


- Call when I report I'm infected:
  - APP: https://github.com/DP-3T/dp3t-app-android/blob/dc75d3f8f94cebea1beaad4740f97add7ec116f6/app/src/main/java/org/dpppt/android/app/inform/InformFragment.java#L103
  - SDK: https://github.com/DP-3T/dp3t-sdk-android/blob/0916c5046bc541b63010dccfec69536a73ff8a73/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/DP3T.java#L167

```
		DP3T.sendIAmInfected(getContext(), onsetDate,
				new ExposeeAuthMethodJson(authCode), new ResponseCallback<Void>(){}
```


- HTTP caching was avoiding the correct operation of the application. Had to add cache policy to:
  - BucketService: https://github.com/DP-3T/dp3t-sdk-android/blob/master-alpha/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/BucketService.java
  - DiscoveryService: https://github.com/DP-3T/dp3t-sdk-android/blob/master-alpha/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/DiscoveryService.java
  - ReportService: https://github.com/DP-3T/dp3t-sdk-android/blob/master-alpha/dp3t-sdk/sdk/src/main/java/org/dpppt/android/sdk/internal/backend/ReportService.java
