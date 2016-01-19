## (任意)Opt-Out的安裝

有些廣告公司會允許用戶選擇不接受瞄準投放的廣告。在APP啟動時，用戶若在顯示隱私條例和使用規範的視窗中選擇不參加的選項，接收到效果測量通知的同時，F.O.X會把此用戶選擇不參加的資訊，發送給廣告公司。

若要對應Opt-Out，請在如同以下「[Install計測安裝](/lang/zh-tw/doc/send_conversion/README.md)」導入sendConversionWithStartPage之前設定。

```java
AdManager ad = new AdManager(this);
// 如果用戶選擇了Opt-Out需要把setOptout設置為有效if(user.optout) {	ad.setOptout(true);}
ad.sendConversion("default");
```

---
[TOP](/lang/zh-tw/README.md)
