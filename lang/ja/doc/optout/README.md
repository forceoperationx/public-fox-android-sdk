## （オプション）オプトアウトの実装

広告会社によってターゲティング広告に利用されないことをユーザーに選択させることが可能です。アプリケーションの起動時において、プライバシーポリシーや利用規約を表示するダイアログでユーザーがオプトアウトを選択した場合、効果測定の結果の通知と共に、F.O.Xが広告会社に対してそのユーザーがオプトアウトを選択したことを通知します。
オプトアウトに対応する場合は、以下の通り「[インストールの計測の実装](/lang/ja//doc/send_conversion/README.md)」で実装した sendConversionより前に設定を行ってください。

```java
AdManager ad = new AdManager(this);
// ユーザーがオプトアウトを選択した場合に setOptout を有効にするif(user.optout) {	ad.setOptout(true);}
ad.sendConversion("default");
```

---
[トップ](/lang/ja/README.md)
