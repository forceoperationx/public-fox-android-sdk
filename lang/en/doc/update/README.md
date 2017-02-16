## Updating to latest version of F.O.X SDK

Please check following implementation if you haven't implemented these yet.
* [(Optional) Setting of Google Play Services SDK for using Advertising ID](../../doc/google_play_services/README.md)
* [(Optional) Setting of deduplication using external storage](../../doc/external_storage/README.md)

Don’t forget testing tracking when update SDK.
After updating the SDK, be sure to conduct the test and check that there is no problem with the tracking and application operation.

## Update SDK from before v2.10.4g

When upgrading Android SDK before v2.10.4g, need to call updateFrom2_10_4g() before generate AdManager instance for downward compatibility of ID like below. If you do it, you can track LTV or Access Analytics continuously.

```java
AdManager.updateFrom2_10_4g(); // must call before “new AdManager”
AdManager ad = new AdManager(this);
ad.sendConversion("default");
```

> ※If it have been released SDK before v2.10.4g, ― for example, v2.10g → v2.10.5g → v2.10.6g ― do not remove updateFrom2_10_4g method even if update from after v2.10.4g

This process need to be followed if app have been implemented SDK which mentioned below.

* v2.7u / v2.7.1u / v2.7.2u
* v2.8u / v2.8.1u
* v2.9g / v2.9.1g / v2.9.2g
* v2.10g / v2.10.1g / v2.10.4g

> ※It is not necessary to follow this process if there is ‘r’ or ‘g’ at the end of SDK version

---
[TOP](/lang/en/README.md)
