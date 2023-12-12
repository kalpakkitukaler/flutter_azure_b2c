# flutter_azure_b2c
Changes int dependency 

## Installation

Add flutter_azure_b2c to your pubspec:
```yaml
    dependencies:
        flutter_azure_b2c: any # or the latest version on Pub
```

### Android

* Configure your app to use the INTERNET and ACCESS_NETWORK_STATE permission in the manifest file located in <project root>/android/app/src/main/AndroidManifest.xml:
```xml
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
```

* Add also an intent filter in the manifest file to capture redirect from MSAL service:
```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="<YOUR_PACKAGE_NAME>"
                android:path="<YOUR_BASE64_URL_ENCODED_PACKAGE_SIGNATURE>" />
        </intent-filter>
    </activity>
```
For more information see https://github.com/AzureAD/microsoft-authentication-library-for-android.



* Prepare a JSON configuration file (named `auth_config.json` in the example code) for AzureB2C initialization(`AzureB2C.init("auth_config")`) in <project root>/android/app/main/res/raw/ following this template:
```json
    {
        "client_id" : "<application (client) id>",
        "redirect_uri" : "msauth://<YOUR_PACKAGE_NAME>/<YOUR_BASE64_URL_ENCODED_PACKAGE_SIGNATURE>",
        "account_mode" : "<MULTIPLE|SINGLE>",
        "broker_redirect_uri_registered": false,
        "authorities": [
            {
                "type": "B2C",
                "authority_url": "https://<youractivedirectoryname>.b2clogin.com/<youractivedirectoryname>.onmicrosoft.com/<sign_in_up_policy_name>/",
                "default": true
            },
            {
                "type": "B2C",
                "authority_url": "https://<youractivedirectoryname>.b2clogin.com/<youractivedirectoryname>.onmicrosoft.com/<other_policy e.g. reset_pass>/"
            }
        ],
        "default_scopes": [
            "https://<youractivedirectoryname>.onmicrosoft.com/<application (server) id>/<API name>"
        ]
    }
```
See https://docs.microsoft.com/en-us/azure/active-directory/develop/tutorial-v2-android for information about how to configure your B2C application and generate <YOUR_BASE64_URL_ENCODED_PACKAGE_SIGNATURE>.

* Your app might crash in release, if this is the case you need to add these two lines of code in your app's build.gradle file (thanks to users euphoria3k and emaborsa for providing this fix):
```gradle
buildTypes {
        release {
            ...
            minifyEnabled false
            shrinkResources false
        }
    }
```

### IOS

* Prepare a JSON configuration file (named `auth_config.json` in the example code) for AzureB2C initialization(`AzureB2C.init("auth_config")`) in <project root>/ios/Resources following this template:
```json
    {
        "client_id" : "<application (client) id>",
        "redirect_uri" : "msauth://<YOUR_PACKAGE_NAME>/<YOUR_BASE64_URL_ENCODED_PACKAGE_SIGNATURE>",
        "account_mode" : "<MULTIPLE|SINGLE>",
        "broker_redirect_uri_registered": false,
        "authorities": [
            {
                "type": "B2C",
                "authority_url": "https://<youractivedirectoryname>.b2clogin.com/<youractivedirectoryname>.onmicrosoft.com/<sign_in_up_policy_name>/",
                "default": true
            },
            {
                "type": "B2C",
                "authority_url": "https://<youractivedirectoryname>.b2clogin.com/<youractivedirectoryname>.onmicrosoft.com/<other_policy e.g. reset_pass>/"
            }
        ],
        "default_scopes": [
            "https://<youractivedirectoryname>.onmicrosoft.com/<application (server) id>/<API name>"
        ]
    }
```
See https://github.com/AzureAD/microsoft-authentication-library-for-objc for information about how to configure your B2C application.

### Web

* Add a CDN dependecy in your index.html file:
```html
  <script type="text/javascript" src="https://alcdn.msauth.net/browser/<MSAL_VERSION>/js/msal-browser.min.js"></script>
```
Web implementation depends from the package msal_js (for more information see https://pub.dev/packages/msal_js), depending on the version imported follow the package documentation in order to select the correct <MSAL_VERSION>.

For more information about MSAL web see https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser#usage.


* Prepare a JSON configuration file (named `auth_config.json` in the example code) for AzureB2C initialization(`AzureB2C.init("auth_config")`) in <project root>/web/asset/ following this template:
```json
    {
        "client_id" : "<application (client) id>",
        "redirect_uri" : "<your app domain>",
        "cache_location": "<localStorage|sessionStorage>",
        "interaction_mode": "<popup|redirect>",
        "authorities": [
            {
                "type": "B2C",
                "authority_url": "https://<youractivedirectoryname>.b2clogin.com/<youractivedirectoryname>.onmicrosoft.com/<sign_in_up_policy_name>/",
                "default": true
            },
            {
                "type": "B2C",
                "authority_url": "https://<youractivedirectoryname>.b2clogin.com/<youractivedirectoryname>.onmicrosoft.com/<other_policy e.g. reset_pass>/"
            }
        ],
        "default_scopes": [
            "https://<youractivedirectoryname>.onmicrosoft.com/<application (server) id>/<API name>"
        ]
    }
```

## Run the example

In <root>/example/lib/main.dart there is a simple demonstration app. In order to test your setting you can follow these next steps:

* Configure a B2C app following Microsoft documentation (see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-overview).

* Prepare a configuration file using previous templates to match the init (e.g. `AzureB2C.init("auth_config")`):
    * Android: 
        * path: android/app/main/res/raw/
    * IOS:
        * path: ios/Resources/
    * Web:
        * path: web/assets/

* launch the application:
    * Android: 
        * flutter launch
        * choose an android emulator or device
    * IOS: 
        * flutter launch
        * choose an ios emulator or device
    * Web:
        * flutter launch -d chrome --web-port <port>
        * Note: choose port number according to the redirect uri registered in the B2C app.

In VS Code you can create a launch configuration like the one below:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "touchscreen",
            "request": "launch",
            "type": "dart",
            "args": ["--web-port", "<REDIRECT_PORT>"]
        },
        {
            "name": "touchscreen (profile mode)",
            "request": "launch",
            "type": "dart",
            "flutterMode": "profile",
            "args": ["--web-port", "<REDIRECT_PORT>"]
        },
        {
            "name": "touchscreen (release mode)",
            "request": "launch",
            "type": "dart",
            "flutterMode": "release",
            "args": ["--web-port", "<REDIRECT_PORT>"]
        }
    ]
}
```


