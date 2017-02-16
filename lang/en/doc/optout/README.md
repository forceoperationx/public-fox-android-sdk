## (Optional) Implement opt out

Users are enabled to select not being used for targeting Ads by advertising company. If a user selects opt out on dialog showing privacy policy or terms of service at activation, F.O.X notice the company that the user has selected opt out with result of measurement.

If you want to enable opt out, call `setOptout` before `sendConversion` as follows.

```java
AdManager ad = new AdManager(this);
// In case of user's selecting opt out, enable setOptoutif(user.optout) {	ad.setOptout(true);}
ad.sendConversion("default");
```

---
[TOP](/lang/en/README.md)
