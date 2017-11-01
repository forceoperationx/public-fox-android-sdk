[TOP](../../README.md)　>　FAQ・注意事項

这里总结了到目前为止出现的疑难问题，编码安装事的注意事项等。请务必确认。

## FAQ

### 支持对象的Android API Level是多少？

F.O.X Android SDK ver 3.x及以前的SDK支持Android API Level 9及以上。
F.O.X Android SDK ver 4.0.0及以后的SDK支持Android API Level 14及以上。

### 未设置URL SCHEME 进行发布后无法从浏览器跳转至APP

进行Cookie计测启动浏览器时，为了使用URL SCHEME迁移到Application需要设置URL SCHEME。

### URL SCHEME中含有大写字母时，无法正常迁移到APP。

根据运行环境，会出现因为URL SCHEME 的大小写字母不能判定而导致使用URL SCHEME无法正常迁移的情况。请将URL SCHEME全部设置为半角小写英文数字或小数点。

### F.O.X中计测的Install数值会大于Google Play Developer Console的数字

F.O.X结合多种方式来进行终端重复安装的检查。当设置无法进行检查重复时，同一终端的再次安装可能会被F.O.X判定为新的安装。

为提高排查重复的精度，请进行下面的设置。

* [导入Google Play Services SDK来使用广告ID](./doc/google_play_services/README.md)

* [利用外部储存优化重复排除](./doc/external_storage/README.md)

* [使用自动备份功能 Android M](./doc/auto_backup/README.md)


## 注意事项

### 像FOX_APP_ID等这样按不同APP分配的参数，请务必使用指定的值

如果输入了不同的值，在粘贴的时候混入空格或改行这样的字符，可能会发生无法正常计测的现象。

### 如果使用InstallReferrer计测请务必设定INSTALL_REFERRER RECEIVER

InstallReferrer计测的设定请参考如下。

[InstallReferrer计测的设定](../../README.md#setting_installreferrer)

如果有和F.O.X同样的其他监测工具需要使用INSTALL_REFERRER RECEIVER的时候，发生过删掉F.O.X的设置而无法正常计测的事情。
使用多个工具想要让多个INSTALL_REFERRER RECEIVER共存的话请参考如下设置。

[使多个INSTALL_REFERRER RECEIVER共存的设置](../install_referrer/README.md)

### 如果使用ProGuard，请务必将F.O.X的方法排除在外。

如果将F.O.X SDK方法代码混淆了的话，将无法正常执行。详细的设置请参考如下。

[利用ProGuard](../../README.md#setting_proguard)

### APP的全新发布，还有APP更新发布的时候，请一定进行疏通测试，请确认是否能够正常地取得计测结果

到申请到应用商城为止，请在导入了SDK状态下做充分的测试，确保APP的动作没有任何问题。

---
[Top](../../README.md)
