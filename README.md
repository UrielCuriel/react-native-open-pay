# Openpay React Native ![CI status](https://img.shields.io/badge/build-passing-brightgreen.svg)

openpay-react-native Generates the device_session_id and tokenize the credit cards with Openpay.

![Openpay React Native](https://image.ibb.co/h6Ac89/screenshot.png)

## Features
* Generates the device_session_id (https://openpay.mx/docs/fraud-tool.html)
* Tokenize the credit cards (https://openpay.mx/docs/openpayjs.html)

## Requirements
* npm >= 6

## Installation

### Via npm
`$ npm install rn-openpay --save`

### Via yarn
`$ yarn add rn-openpay`

## Usage

```js
import Openpay, {createDeviceSessionId} from 'rn-openpay';

<Openpay 
    isProductionMode={false} 
    merchantId="YOUR_MERCHANT_ID" 
    publicKey="YOUR_PUBLIC_KEY" 
    address={address} 
    successToken={this.successToken} 
    failToken={this.failToken} 
    loading={this.state.loading}
    buttonText="Some text"
/>

// Note: address prop is optional.
```
And then on your successToken or failToken handler:

```js
state = {
    loading: false
}

successToken = (response) => {        
    const deviceSessionId = createDeviceSessionId();
    console.log('createDeviceSessionId', deviceSessionId);
    console.log('successToken', response);
    this.setState(() => ({loading: false}))
}

failToken = (response) => {
    console.log('failToken', response);
}
```

## Props
| Property | Type | Description | Required |
| --- | --- | --- | --- |
|isProductionMode | Boolean | Defines the Openpay environment| Yes |
|merchantId | String | Openpay Merchant ID | Yes |
|publicKey | String | Openpay Public Key | Yes |
|successToken | Function | It will receive the card token and here you will need to add your logic | Yes |
|failToken | Function | It will receive the error if something wrong happen | Yes |
|loading | Boolean | Adds a spinner to the button when the user clicks on it | Yes |
|address | Object | You can add the address of your customer | No |
|buttonText | String | Button's text | No |


## Methods
### createDeviceSessionId
This method creates the device session id.
```js
const deviceSessionId = createDeviceSessionId();
```

## Example
```js
import React, {Component} from 'react';
import Openpay, {createDeviceSessionId} from 'openpay-react-native';

export default class OpenpayScreen extends Component {
    state = {
        loading: false
    }

    successToken = (response) => {        
        const deviceSessionId = createDeviceSessionId();
        const token = response.id;
        this.setState(() => ({loading: false}));

        // Make the call to your server with your charge request
    }

    failToken = (response) => {
        console.log('failToken', response);
    }

    render() {
        const address = {
            "city":"Querétaro",
            "country_code":"MX",
            "postal_code":"76900",
            "line1":"Av 5 de Febrero",
            "line2":"Roble 207",
            "line3":"Col Carrillo",
            "state":"Queretaro"
        };

        return (
            <Openpay 
                isProductionMode={false} 
                merchantId="YOUR_MERCHANT_ID" 
                publicKey="YOUR_PUBLIC_KEY" 
                address={address} 
                successToken={this.successToken} 
                failToken={this.failToken} 
                loading={this.state.loading}
            />
        );
    }
}
```

## Future Improvements
* Customize credit card image
* Customize button style
* Add unit tests

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## know issues
config react-native-device-info

<details>
    <summary>iOS (via CocoaPods) RN <= 59 </summary>

RN <= 59: [`Bug`](https://github.com/react-native-community/react-native-device-info/issues/748)

```
mv ios/Podfile .
react-native link react-native-device-info
mv Podfile ios
```

Do _not_ append `pod 'RNDeviceInfo', :path => '../node_modules/react-native-device-info'` to the Podfile

</details>

<details>
    <summary>iOS (via CocoaPods) RN >= 60</summary>

Add the following lines to your build targets in your `Podfile`

```ruby
pod 'React', :path => '../node_modules/react-native'

# Explicitly include Yoga if you are using RN >= 0.42.0
pod 'yoga', :path => '../node_modules/react-native/ReactCommon/yoga'

pod 'RNDeviceInfo', :path => '../node_modules/react-native-device-info'

# React-Native is not great about React double-including from the Podfile
post_install do |installer|
  installer.pods_project.targets.each do |target|
    if target.name == "React"
      target.remove_from_project
    end

    # It removes React & Yoga from the Pods project, as it is already included in the main project.
    targets_to_ignore = %w(React yoga)
    if targets_to_ignore.include? target.name
      target.remove_from_project
    end
  end
end
```

Then run `pod install`

</details>

<details>
    <summary>iOS (without CocoaPods)</summary>

In XCode, in the project navigator:

- Right click _Libraries_
- Add Files to _[your project's name]_
- Go to `node_modules/react-native-device-info/ios`
- Add the file `RNDeviceInfo.xcodeproj`

In XCode, in the project navigator, select your project.

- Add the `libRNDeviceInfo.a` from the _deviceinfo_ project to your project's _Build Phases ➜ Link Binary With Libraries_
- Click `.xcodeproj` file you added before in the project navigator and go the _Build Settings_ tab. Make sure _All_ is toggled on (instead of _Basic_).
- Look for _Header Search Paths_ and make sure it contains both `$(SRCROOT)/../react-native/React` and `$(SRCROOT)/../../React`
- Mark both as recursive (should be OK by default).

Run your project (Cmd+R)

(Thanks to @brysgo for writing the instructions)

</details>

<details>
    <summary>Android</summary>

- **_optional_** in `android/build.gradle`:

```gradle
...
  ext {
    // dependency versions
    googlePlayServicesVersion = "<Your play services version>" // default: "16.1.0" - pre-AndroidX, override for AndroidX
    compileSdkVersion = "<Your compile SDK version>" // default: 28
    targetSdkVersion = "<Your target SDK version>" // default: 28
  }
...
```

- in `android/app/build.gradle`:

```diff
dependencies {
    ...
    implementation "com.facebook.react:react-native:+"  // From node_modules
+   implementation project(':react-native-device-info')
}
```

- in `android/settings.gradle`:

```diff
...
include ':app'
+ include ':react-native-device-info'
+ project(':react-native-device-info').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-device-info/android')
```

#### With React Native 0.29+

- in `MainApplication.java`:

```diff
+ import com.learnium.RNDeviceInfo.RNDeviceInfo;

  public class MainApplication extends Application implements ReactApplication {
    //......

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
+         new RNDeviceInfo(),
          new MainReactPackage()
      );
    }

    ......
  }
```

#### With older versions of React Native

- in `MainActivity.java`:

```diff
+ import com.learnium.RNDeviceInfo.RNDeviceInfo;

  public class MainActivity extends ReactActivity {
    ......

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
+       new RNDeviceInfo(),
        new MainReactPackage()
      );
    }
  }
```

NOTE: If you faced with this error: `Could not resolve all files for configuration ':react-native-device-info:debugCompileClasspath'.`, in `build.gradle` put `google()` in the first line (according to https://stackoverflow.com/a/50748249)

- in `android/build.gradle`:

```diff
allprojects {
    repositories {
+       google()
        ...
    }
}
```

(Thanks to @chirag04 for writing the instructions)

</details>

<details>
    <summary>Windows</summary>

You can either use autolinking on react-native-windows 0.63 and later or manually link the module on earlier realeases.

#### Manual installation on RNW >= 0.62
- `npm install react-native-device-info --save`
- Open your solution in Visual Studio 2019 (eg. `windows\yourapp.sln`)
- Right-click Solution icon in Solution Explorer > Add > Existing Project...
- Add `node_modules\react-native-device-info\windows\RNDeviceInfoCPP\RNDeviceInfoCPP.vcxproj`
- Right-click main application project > Add > Reference...
- Select `RNDeviceInfoCPP` in Solution Projects
- In app `pch.h` add `#include "winrt/RNDeviceInfoCPP.h"`
- In `App.cpp` add `PackageProviders().Append(winrt::RNDeviceInfoCPP::ReactPackageProvider());` before `InitializeComponent();`:

```diff
App::App() noexcept
{
...
    PackageProviders().Append(make<ReactPackageProvider>()); // Includes all modules in this project
+   PackageProviders().Append(winrt::RNDeviceInfoCPP::ReactPackageProvider());

    InitializeComponent();
}
```
</details>



### WebView error
check this article to config webview
https://engineering.brigad.co/demystifying-react-native-modules-linking-964399ec731b


Native module RNCWebView tried to override RNCWebViewModule for module name

```
  @Override    
  public boolean canOverrideExistingModule() {        
    return true;    
  }
```
to `node_modules/react_native_webview/android/src/main/java/com/reactnativecommunity/webview/RNCWebViewModule.java`

## License
[MIT](https://choosealicense.com/licenses/mit/)