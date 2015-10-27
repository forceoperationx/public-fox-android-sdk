# オートバックアップ機能を用いた重複排除機能の向上

Android M(6.0)より追加されたオートバックアップを用いることで重複排除機能を向上させることが可能です。
尚、当対応は任意となります。

## 利用可能条件

* Android M端末からAndroid M以上の端末にバックアップデータを復元した場合となります。
* バックアップ時と復元時で端末ログインしているGoogleアカウントが同じである必要があります。
* データを引き継ぐには下図のように端末の設定がユーザーによって有効となっていることが必須となります。

![設定画面](./img01.png)

## 設定

　ご利用の際、アプリのバックアップ設定ファイルの状況に合わせて設定頂く必要があります。

> [参考設定](https://developer.android.com/training/backup/autosyncapi.html)

**[バックアップするアプリデータを個別に指定する場合]**

　以下のファイルがバックアップの対象にふくまれるように設定をしてください。

```
<include domain="file" path="__ADMAGE_RANDOM_DEVICE_ID__" />
```

**[全アプリデータのバックアップを行う場合]**

　アプリデータ全てがバックアップの対象となるよう設定されている場合、以下のファイルは除外されるように設定をしてください。

```
<exclude domain="file" path="__ADMAGE_WEB_CONVERSION_COMPLETED__" />
<exclude domain="file" path="__ADMAGE_APP_CONVERSION_COMPLETED__" />
```
