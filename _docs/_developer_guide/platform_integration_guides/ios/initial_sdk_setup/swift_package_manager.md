---
nav_title: Swift Package Manager
platform: iOS
page_order: 1
description: "This tutorial covers how to install the SDK using Swift Package Manager"
---

# Swift Package Manager Integration

## Requirements

Installing the iOS SDK via [Swift Package Manager][apple_initial_setup_1] automates the majority of the installation process for you. Before beginning this process please ensure that you are using Xcode 12 or greater.

> Note that tvOS and Push Stories support is not yet available via _Swift Package Manager_.\
> Push Stories are available through a side-by-side integration with Cocoapods.

## Step 1: Adding the dependency to your project

Open your project and navigate to your project's settings. Select the tab named _Swift Packages_ and click on the add button (_+_) at the bottom left.

![Swift Package Manager: Menu 1][apple_initial_setup_2]

Enter the url of our iOS SDK repository (`https://github.com/Appboy/appboy-ios-sdk`) in the text field and click _Next_:

![Swift Package Manager: Menu 2][apple_initial_setup_3]

On the next screen, select the SDK version and click _Next_.

> Versions 3.29.0 and higher are compatible with _Swift Package Manager_.

![Swift Package Manager: Menu 3][apple_initial_setup_4]

Select the package that best fits your needs and click _Finish_:
- `AppboyUI`
  - Best suited if you plan to use UI components provided by Braze.
  - Includes `AppboyKit` automatically.
- `AppboyKit`
  - Best suited if you don't need to use any of the UI components provided by Braze (e.g. Content Cards, In-App Messages, etc.).
  
> Make sure you select **either** `AppboyKit` **or** `AppboyUI`. Including both packages can lead to undesired behavior.

![Swift Package Manager: Menu 4][apple_initial_setup_5]

## Step 2: Configuring your project

Navigate to your project build settings and add the `-ObjC` flag to the _Other Linker Flags_ setting.

![Swift Package Manager: Menu 5][apple_initial_setup_6]

Edit the scheme of the target including the Appboy package (_Product > Scheme > Edit Scheme_ menu item):
- Click the expand ▶︎ next to _Build_ and select _Post-actions_. Press _+_ and select _New Run Script Action_.
- In the dropdown next to _Provide build settings from_, select your app's target.
- Copy this script into the open field:
```sh
rm -rf "${TARGET_BUILD_DIR}/${PRODUCT_NAME}.app/Frameworks/libAppboyKitLibrary.a"
rm -rf "${TARGET_BUILD_DIR}/${PRODUCT_NAME}.app/Plugins/libAppboyKitLibrary.a"
```

![Swift Package Manager: Menu 7][apple_initial_setup_7]

## Step 3: Updating your App Delegate

{% tabs %}
{% tab OBJECTIVE-C %}

Add the following line of code to your `AppDelegate.m` file:

```objc
#import "AppboyKit.h"
```

Within your `AppDelegate.m` file, add the following snippet within your `application:didFinishLaunchingWithOptions` method:

```objc
[Appboy startWithApiKey:@"YOUR-APP-IDENTIFIER-API-KEY"
         inApplication:application
     withLaunchOptions:launchOptions];
```

{% endtab %}
{% tab swift %}

Add the following line of code to your `AppDelegate.swift` file:

```swift
import AppboyKit
```

For more information about using Objective-C code in Swift projects, please see the [Apple Developer Docs](https://developer.apple.com/library/ios/documentation/swift/conceptual/buildingcocoaapps/MixandMatch.html).

In `AppDelegate.swift`, add following snippet to your `application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool`:

```swift
Appboy.start(withApiKey: "YOUR-APP-IDENTIFIER-API-KEY", in:application, withLaunchOptions:launchOptions)
```

__Note__: Braze's `sharedInstance` singleton will be `nil` before `startWithApiKey:` is called, as that is a prerequisite to using any Braze functionality.

{% endtab %}
{% endtabs %}

{% alert important %}
Be sure to update `YOUR-APP-IDENTIFIER-API-KEY` with the correct value from your App Settings page. For more information on where to find your App Identifier API key, check out our [API documentation](https://www.braze.com/docs/api/api_key/#the-app-identifier-api-key).
{% endalert %}

{% alert warning %}
Be sure to initialize Braze in your application's main thread. Initializing asynchronously can lead to broken functionality.
{% endalert %}


## Step 4: Specify Your Data Cluster

{% alert note %}
Note that as of December 2019, custom endpoints are no longer given out, if you have a pre-existing custom endpoint, you may continue to use it. For a list of our available endpoints, <a href="{{site.baseurl}}/api/basics/#endpoints">click here</a>.
{% endalert %}

### Compile-time Endpoint Configuration (Recommended)

If given a pre-existing custom endpoint...
- Starting with Braze iOS SDK v3.0.2, you can set a custom endpoint using the `Info.plist` file. Add the Appboy dictionary to your `Info.plist` file. Inside the `Appboy` dictionary, add the `Endpoint` string subentry and set the value to your custom endpoint URL's authority (for example, `sdk.iad-01.braze.com`, not `https://sdk.iad-01.braze.com`).

Your Braze representative should have already advised you of the [correct endpoint]({{site.baseurl}}/user_guide/administrative/access_braze/sdk_endpoints/).

### Runtime Endpoint Configuration

If given a pre-existing custom endpoint...
- Starting with Braze iOS SDK v3.17.0+, you can override set your endpoint via the `ABKEndpointKey` inside the `appboyOptions` parameter passed to `startWithApiKey:inApplication:withLaunchOptions:withAppboyOptions:`. Set the value to your custom endpoint URL's authority (for example, `sdk.iad-01.braze.com`, not `https://sdk.iad-01.braze.com`).

{% alert important %}
To find out your specific cluster, please ask your Customer Success Manager or reach out to our support team.
{% endalert %}

### Implementation Example

See the [`AppDelegate.m`][apple_initial_setup_8] file in the Stopwatch sample app.

[apple_initial_setup_1]: https://swift.org/package-manager/
[apple_initial_setup_2]: {% image_buster /assets/img/ios/spm/image1.png %}
[apple_initial_setup_3]: {% image_buster /assets/img/ios/spm/image2.png %}
[apple_initial_setup_4]: {% image_buster /assets/img/ios/spm/image3.png %}
[apple_initial_setup_5]: {% image_buster /assets/img/ios/spm/image4.png %}
[apple_initial_setup_6]: {% image_buster /assets/img/ios/spm/image5.png %}
[apple_initial_setup_7]: {% image_buster /assets/img/ios/spm/image6.png %}
[apple_initial_setup_8]: https://github.com/Appboy/appboy-ios-sdk/blob/master/Example/Stopwatch/Sources/AppDelegate.m
