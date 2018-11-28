[TOP](../../README.md)　>　Enabling opt-out feature

---

# （Optional）Enabling opt-out feature

It is possible for users to opt out of targeted advertisements by advertising companies. When the user selects opt-out in the dialog displaying the privacy policy and the usage agreement at the time of starting the application FOX notifies the advertising company that the user has decided to opt out along with advertisement measurement results.

For more information on how to enable opt-out functionality see the [`FoxTrackOption`](../sdk_api/README.md#foxtrackoption) section of 「[Install tracking](../track_install/README.md#track_install_optional)」 and run the `setOptOut` method with the argument, `true` as shown below.

```java
// First time launch install tracking
FoxTrackOption option = new FoxTrackOption();
option.setRedirectUrl("myapp://top")
			.setBuid(getUserId())
			.setOptOut(true);
Fox.trackInstall(option);
```

---
[Return to Top](../../README.md)
