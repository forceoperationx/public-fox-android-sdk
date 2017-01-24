[TOP](../../README.md)　>　Optout的安装

---

# （可选项）安装optout

广告公司可以让用户选择是否使用定向投放广告。用户在APP启动时弹出的隐私权政策和使用条例对话框中选择Optout的情况时，告知效果测定结果的同时，F.O.X也会通知广告公司用户选择了Optout。

适用Optout的场合，通过以下「[Install计测的安装](../track_install/README.md#track_install_optional)」在安装时使用[`FoxTrackOption`](../sdk_api/README.md#foxtrackoption)将`OptOut`设置为`true`。

```java
// 首次启动的Install计测
FoxTrackOption option = new FoxTrackOption();
option.setRedirectUrl("myapp://top")
			.setBuid(getUserId())
			.setOptOut(true);
Fox.trackInstall(option);
```

---
[Top](../../README.md)
