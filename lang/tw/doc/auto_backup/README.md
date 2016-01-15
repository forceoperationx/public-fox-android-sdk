# 利用自動備份功能增強重複排除功能

使用從Android M(6.0)開始追加的自動備份功能可以增強重複排除功能。此選項為任意。

## 利用條件

* 從Android M移動終端向Android M以上配置的移動終端復原備份數據的時候。
* 備份和復原時登錄到移動終端的Google帳號必須是相同的。
* 用戶需要像下圖那樣設定移動終端來繼承數據。

![設定画面](./img01.png)

## 設定

  必須結合APP的備份設定文件的狀況來進行設定。

> [参考設定](https://developer.android.com/training/backup/autosyncapi.html)

**[個別指定備份APP數據的時候]**

  請做設定以使下面文件包含在備份對象裡

```
<include domain="file" path="__ADMAGE_RANDOM_DEVICE_ID__" />
```

**[備份全部APP數據的時候]**

  如果想要設定成備份全部APP數據，請設定成把下面文件排除掉的形式。

```
<exclude domain="file" path="__ADMAGE_WEB_CONVERSION_COMPLETED__" />
<exclude domain="file" path="__ADMAGE_APP_CONVERSION_COMPLETED__" />
```

---
[TOP](/lang/tw/README.md)
