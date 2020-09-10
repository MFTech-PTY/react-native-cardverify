# CardVerify

CardVerify React Native installation guide

Visit our website at https://www.getbouncer.com for examples. We also have support for native Android and iOS libraries.

CardVerify is closed source, and requires a license agreement. See the [license](#license) section for details.

## Installation
### 1. Install the CardVerify SDK into your app

#### iOS

The following steps outline the basic installation procedure. More details can be found in the native [integration guide](https://docs.getbouncer.com/card-verify/ios-integration-guide).

##### 1.1 Request credentials
If you don't already have them, request repository credentials from [license@getbouncer.com](mailto:license@getbouncer.com) and create an [API key](https://api.getbouncer.com/console).

##### 1.2 Add dependencies
CardVerify is available through [CocoaPods](https://cocoapods.org). To install
it, simply add the following line to your Podfile:

```ruby
pod 'CardVerify', :http => 'https://api.getbouncer.com/v1/downloads/sdk/card_verify/<API_SECRET>/cardverify-ios-1.0.5028.tgz'
```

Next, install the new pod. From a terminal, run:

```
pod install
```

#### Android

The following steps outline the basic installation procedure. More details can be found in the native [integration guide](https://docs.getbouncer.com/card-verify/android-integration-guide#integration).

##### 1.1 Request credentials
If you don't already have them, request repository credentials from [license@getbouncer.com](mailto:license@getbouncer.com).

##### 1.2 Add the repository
Add the CardVerify repository to the android section of your react-native project. In your `android/build.gradle` file, append the following to repositories:
```gradle
repositories {
    ...
    maven {
        url "https://bouncerpaid.bintray.com/cardverify-ui-android"
        credentials {
            username "<FILL_IN_YOUR_USERNAME>"
            password "<FILL_IN_YOUR_PASSWORD>"
        }
    }
}
```

##### 1.3 Add dependencies
Add the cardverify dependencies to the android section of your react-native project. In your `android/app/build.gradle` file, append the following to dependencies:
```gradle
dependencies {
    implementation 'com.getbouncer:cardverify-ui:2.0.0030'
}
```

_Note: You will need a username and password to set up these repositories. Please contact [license@getbouncer.com](mailto:license@getbouncer.com) to request credentials._

### 2. Install the [react-native-cardverify](https://www.npmjs.com/package/react-native-cardverify) package from NPM

```
$ npm install react-native-cardverify
```

### 3. For RN 0.59 and below: Link native dependencies

#### Automatic

```
$ react-native link react-native-cardverify
```

#### Manual

##### iOS

1. In XCode, in the project navigator, right click `Libraries` ➜ `Add Files to [your project's name]`
2. Go to `node_modules` ➜ `react-native-cardverify` and add `RNCardVerify.xcodeproj`
3. In XCode, in the project navigator, select your project. Add `libRNCardVerify.a` to your project's `Build Phases` ➜ `Link Binary With Libraries`
4. Run your project `Cmd+R`

##### Android

1. Insert the following lines inside the dependencies block in `android/app/build.gradle`:
    ```
    implementation project(':react-native-cardverify')
    ```
1. Append the following lines to `android/settings.gradle`:
    ```
    include ':react-native-cardverify'
    project(':react-native-cardverify').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-cardverify/android')
    ```
1. Open `android/app/src/main/java/[...]/MainApplication.java`
    - Add `import com.getbouncer.RNCardVerifyPackage;` to the imports at the top of the file
    - Add `new RNCardVerifyPackage()` to the list returned by the `getPackages()` method


### 4. Configure CardVerify SDK

#### iOS
The podfile in your `~/ios/Podfile` in your project should look similar to:
```ruby
platform :ios, '10.0'
  target 'Your App' do
  ...
  pod 'CardScan'
  pod 'CardVerify', :http => 'https://api.getbouncer.com/v1/downloads/sdk/card_verify/<API_SECRET>/cardverify-ios-1.0.5028.tgz'
  pod 'react-native-cardverify', :path => '../node_modules/react-native-cardverify/react-native-cardverify.podspec'
end
```

Configure the library when your application launches:
##### Swift
```swift
import UIKit
import CardVerify

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    	 Bouncer.configure(apiKey: "YOUR API KEY") 
        // do any other necessary launch configuration
        return true
    }
}
```
##### Obj-c
```objective-c
#import "AppDelegate.h"
...
@import CardVerify;

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  ...
  [Bouncer configureWithApiKey:@"YOUR_API_KEY"];

  return YES;
}
```

#### Android

To configure your [API key](https://api.getbouncer.com/console), open up `android/app/src/main/java/[...]/MainApplication.java` and add the following

```java
import com.getbouncer.RNCardVerifyModule;
...

...
public void onCreate() {
  ...
  RNCardVerifyModule.apiKey = "<YOUR_API_KEY_HERE>";
  RNCardVerifyModule.enableExpiryExtraction = false; // set to true for experimental expiry extraction
  RNCardVerifyModule.enableNameExtraction = false; // set to true for experimental name extraction
}
```

## Using CardVerify (React Native)

**Check device support**

```javascript
import CardVerify from 'react-native-cardverify';

CardVerify.isSupportedAsync()
  .then(supported => {
    if (supported) {
      // YES, show scan button
    } else {
      // NO
    }
  })
```

**Scan card**

```javascript
import CardVerify from 'react-native-cardverify';

CardVerify.scan(last4, iin)
  .then(({action, payload, canceled_reason}) => {
    if (action === 'scanned') {
      const { number, expiryMonth, expiryYear, issuer, legalName } = payload;
      // Display information
    } else if (action === 'canceled') {
      if (canceled_reason === 'enter_card_manually') {
        // the user elected to enter a card manually
      } else if (canceled_reason === 'camera_error') {
        // there was an error with the camera
      } else if (canceled_reason === 'fatal_error') {
        // there was an error during the scan
      } else if (canceled_reason === 'user_canceled') {
        // the user canceled the scan
      } else {
        // the scan was canceled for an unknown reason
      }
    } else if (action === 'skipped') {
      // User skipped
    } else if (action === 'unknown') {
      // Unknown reason for scan canceled
    }
  })
```

## Example app

Inside `example`, you can find an example React Native project that you can run.

To run the example app, do the following:
- Navigate to `example`
- `npm install`
- Update API key in `android/app/src/main/java/com/example/MainApplication.java` for Android and `ios/example/AppDelegate.m` for iOS.
- Point the android app to the SDK: create a file `example/android/local.properties` with a line
  ```
  sdk.dir=<full_path_to_android_sdk>
  ```
- To run Android app: `react-native run-android`
- To run iOS app: `react-native run-ios`

## Troubleshooting

#### iOS

##### `ld: warning: Could not find auto-linked library 'swiftFoundation'`
* A workaround with XCode 11 is to add a bridge header https://stackoverflow.com/a/56176956

##### `error: Failed to build iOS project. We ran "xcodebuild" command but it exited with error code 65`
* A workaround with a newer XCode build system is to switch to the legacy build https://stackoverflow.com/a/51089264

#### Android

##### `Error: spawnSync /.../Andoroid/sdk/platform-tools/adb ENOENT`

* react-native was unable to start the app after installing it. To start it manually, run the following command:
```bash
adb reverse tcp:8081 tcp:8081 && adb shell am start -n com.example/com.example.MainActivity
```

## Authors

Adam Wushensky, Jaime Park, Sam King, Zain ul Abi Din, and Stefano Suryanto

## License

This library is available under paid and free licenses. See the [LICENSE](LICENSE) file for the full license text.

### Quick summary
In short, this library will remain free forever for non-commercial applications, but use by commercial applications is limited to 90 days, after which time a licensing agreement is required. We're also adding some legal liability protections.

After this period commercial applications need to convert to a licensing agreement to continue to use this library.
* Details of licensing (pricing, etc) are available at [https://cardscan.io/pricing](https://cardscan.io/pricing), or you can contact us at [license@getbouncer.com](mailto:license@getbouncer.com).

### More detailed summary
What's allowed under the license:
* Free use for any app for 90 days (for demos, evaluations, hackathons, etc).
* Contributions (contributors must agree to the [Contributor License Agreement](Contributor%20License%20Agreement))
* Any modifications as needed to work in your app

What's not allowed under the license:
* Commercial applications using the license for longer than 90 days without a license agreement.
* Using us now in a commercial app today? No worries! Just email [license@getbouncer.com](mailto:license@getbouncer.com) and we’ll get you set up.
* Redistribution under a different license
* Removing attribution
* Modifying logos
* Indemnification: using this free software is ‘at your own risk’, so you can’t sue Bouncer Technologies, Inc. for problems caused by this library

Questions? Concerns? Please email us at [license@getbouncer.com](mailto:license@getbouncer.com) or ask us on [slack](https://getbouncer.slack.com).
