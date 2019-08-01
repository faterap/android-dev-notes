# sdk version

## 如何正确理解几个 SDK Version?

### compileSdkVersion

The `compileSdkVersion` is the version of the API the app is compiled against. This means you can use Android API features included in that version of the API (as well as all previous versions, obviously). If you try and use API 16 features but set `compileSdkVersion` to 15, you will get a compilation error. If you set `compileSdkVersion` to 16 you can still run the app on a API 15 device as long as your app's execution paths do not attempt to invoke any APIs specific to API 16.

### targetSdkVersion

The `targetSdkVersion` **has nothing to do with how your app is compiled** or what APIs you can utilize. The `targetSdkVersion` is supposed to indicate that you have tested your app on (presumably up to and including) the version you specify. This is more like a certification or sign off you are giving the Android OS as a hint to how it should handle your app in terms of OS features.

## 项目中应该如何设置？

```
minSdkVersion (lowest possible) <= targetSdkVersion == compileSdkVersion (latest SDK)
```