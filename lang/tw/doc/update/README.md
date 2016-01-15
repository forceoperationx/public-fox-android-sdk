## 有關更新到最新版本

已經導入了舊版本F.O.X SDK的APP需要更新到最新的SDK，這時請按下面的步驟來進行。

1. 如果開發項目裡含有舊版本AppAdForce.jar，請先刪除。
1. 遵照「2. SDK導入步驟」追加最新版本的AppAdForce.jar到開發項目裡
1. 執行Eclipse的「Product」→「Clean」

※還沒執行「[（任意）導入Google Play Services SDK來使用廣告ID](../../google_play_services/tw/)」的話請先執行。

※還沒執行「[（任意）利用外部存儲設定重複排除](../../external_storage/tw/)」的話請先執行。

更新完SDK以後，請一定去做疏通測試，以確保計測和APP動作沒有發生問題。


## 更新v2.10.4g及以前的舊版本

如果是從Android SDK v2.10.4g以前的海外版／世界版SDK（版本號末尾為u或g）更新到最新版，為了ID的向下兼容，請按照下面那樣，請一定在生成AdManger實例之前的位置調用updateFrom2_10_4g()方法。
依靠這個方法，安裝了v2.10.4g及以前版本的移動終端也能夠繼續LTV計測和流量分析計測。


```java
AdManager.updateFrom2_10_4g(); // 一定要在new AdManager之前調用
AdManager ad = new AdManager(this);
ad.sendConversion("default");
```

> ※導入了v2.10.4g及以前版本並發佈過的APP，比如v2.10g→v2.10.5g→v2.10.6之後要更新到最新版，或者從v2.10.4g之後的版本更新到最新版本時，請不要去掉updateFrom2_10_4g方法。


對於以前導入過下面版本SDK的APP，需要執行這個步驟。

* v2.7u / v2.7.1u / v2.7.2u
* v2.8u / v2.8.1u
* v2.9g / v2.9.1g / v2.9.2g
* v2.10g / v2.10.1g / v2.10.4g

> ※更新版本號末尾沒有u或g的時候不需要這個步驟。


## 有關更新v.2.14.6g及以前的版本


在「[Install計測的安裝](/lang/tw/doc/send_conversion/README.md)」裡，從v2.14.7g開始通過URL Scheme啟動APP的成果計測使用的方法名變成了下面那樣

<table>
  <tr>
    <td>変更後</td>
    <td>sendReengagementConversion(Intent)</td>
  </tr>
  <tr>
    <td>変更前</td>
    <td>setUrlScheme(Intenet)</td>
  </tr>
</table>

如果是使用setUrlScheme方法進行的安裝，請替換成調用sendReengagementConversion方法。

> ※現在的版本可以使用setUrlScheme方法，但將來版本升級時可能刪除此方法。此外，sendReengagementConversion方法和setUrlScheme方法的內部處理是相同的。

---
[TOP](/lang/tw/README.md)
