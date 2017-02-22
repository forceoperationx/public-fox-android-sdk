## sendLtvConversion的詳細

通過在會員登錄，教程突破，付費等任意的成果地點安裝LTV計測處理，能夠測定不同廣告流入的LTV成果。

請按照如下格式安裝LTV計測的代碼。

```java
import jp.appAdForce.android.LtvManager;
// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.sendLtvConversion(成果地点ID);
```

> **成果地點ID(必須)** : 請輸入Force Operation X管理員告知的值。

#### 指定廣告主終端ID(buid)

在APP內部的成果裡可以包含廣告主終端ID（USER ID等），能夠進行以此為基準的成果計測。 請按如下代碼來給LTV成果設置廣告主終端ID。

```java
import jp.appAdForce.android.LtvManager;
// ...
AdManager ad = new AdManager(this);
LtvManager ltv = new LtvManager(ad);
ltv.sendLtvConversion(成果地點ID, 廣告主終端ID);
```

> **広告主端末ID**(任意) : 是指貴公司管理的唯一識別ID(USER ID等)。可以指定64文字以內的半角英文數字。

---
[Top](/lang/zh-tw/README.md)
