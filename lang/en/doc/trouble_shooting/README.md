[TOP](../../README.md)　>　FAQ・Notes

The following is a compilation of common issues, questions, and important information to note.

### What Android API levels does F.O.X support?

F.O.X Android SDK ver 3.x and below supports Android API Level 9 and above.
F.O.X Android SDK ver 4.0.0 and above supports Android API level 14 and above.

### We released our app without specifying a URL scheme, and now we can't return from the browser to our app.

When performing Cookie tracking the URL scheme is used to transition from the browser to your app. As such, it is necessary to have a custom URL scheme specified.


### Our URL scheme contains uppercase letters, preventing the browser from transitioning to our app.

Depending on your production environment, there are cases where capitalized URL schemes are not recognized. In order to avoid this, please set your URL scheme in lower case alphanumeric characters.


### The number of installs displayed on the F.O.X management portal is greater than the number displayed on the Google Play Developer Console.

F.O.X undertakes multiple measures in detecting duplicate installs.
If de-duplication feature settings are not properly calibrated, then F.O.X will count installs occurring on the same device as new installs.

For more information on improving F.O.X's de-duplication features, change the following settings.

* [Setting Up Google Play Services](../google_play_services/README.md)

* [De-duplication feature settings (external storage permissions)](../external_storage/README.md)

* [De-duplication feature settings (auto-backup for Android M)](../auto_backup/README.md)


## NOTES

### Make sure you set each parameter you are issued to the relevant location in your app

There have been cases where tracking information was incomplete/incorrect when these parameters included a space or carriage return or were incorrectly specified.

### If you are using install referrer tracking, make sure you use the `INSTALL_RECEIVER` receiver.

See the following link for proper settings with install referrer tracking.

[Install Referrer Settings](../../README.md#setting_installreferrer)

There have been cases where an app needed to use the `INSTALL_REFERRER` receiver class with a tool besides F.O.X, where the settings for F.O.X were changed. Because of this, F.O.X was unable to properly perform any tracking.
For information on how to enable multiple receiver classes see the following.

[Install Referrer Settings](../install_referrer/README.md)

### If your app uses Proguard, make sure F.O.X is whitelisted

F.O.X will not function properly if its methods are obfuscated by Proguard. See below for more details.

[ProGuard Settings](../../README.md#setting_proguard)

### Whenever your app has a new release or update, make sure you perform the connection tests on our management portal to ensure tracking is functioning properly.

Be sure to test thoroughly with our SDK implemented into your app and make sure there are no operational issues before releasing your app to the marketplace.

---
[Return to Top](../../README.md)
