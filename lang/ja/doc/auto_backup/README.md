[TOP](../../README.md)　>　オートバックアップ機能を用いた重複排除機能の向上

---

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

## F.O.Xの設定値をオートバックアップ対象外にする

**[F.O.Xの設定値をオートバックアップ対象外にする]**

オフラインモードなどF.O.Xの設定値はShared Preferencesのco.cyberz.fox.xmlに保存されます。アプリの再インストール時に設定値を引き継ぎたくない場合などは、以下を参考に除外設定を行ってください。

[Back Up User Data with Auto Backup](https://developer.android.com/guide/topics/data/autobackup.html#IncludingFiles)

**[実装例]**

manifest.xml
```
<application ...
    android:fullBackupContent="@xml/my_backup_rules"
</app>
```

my_backup_rules.xml
```
<?xml version="1.0" encoding="utf-8"?>
<full-backup-content>
    <exclude domain="sharedpref" path="co.cyberz.fox.xml"/>
</full-backup-content>

```


---
[トップ](../../README.md)
