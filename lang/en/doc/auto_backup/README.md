[TOP](../../README.md)　>　De-duplication feature settings (auto-backup for Android M)

---

# De-duplication feature settings (auto-backup for Android M)

By using the auto-backup feature released since Android M(6.0), the de-duplication feature can be enhanced.
However, these settings are optional.

## Pre-requisites

* Your app targets devices running Android M or higher and backs up data
* The google account used when backing up and restoring data are the same
* For data integrity protection, change your settings as shown below:

![設定画面](./img01.png)

## Settings

> Be sure to check your app's [Backup File Settings](https://developer.android.com/training/backup/autosyncapi.html)

**[When backing up individually specified application data]**

  Ensure the following path is included as part of the back-up.

```
<include domain="file" path="__ADMAGE_RANDOM_DEVICE_ID__" />
```

**[When backing up all application data]**

If your app backs up all data, explicitly exclude the following paths.

```
<exclude domain="file" path="__ADMAGE_WEB_CONVERSION_COMPLETED__" />
<exclude domain="file" path="__ADMAGE_APP_CONVERSION_COMPLETED__" />
```

## Excluding F.O.X data from auto-backup

**[Excluding F.O.X data from auto-backup]**

F.O.X's settings values for offline mode are saved in co.cyberz.fox.xml in Shared Preferences. If you do not want to persist the setting value when reinstalling the application, please follow these exclusion settings.

[Back Up User Data with Auto Backup](https://developer.android.com/guide/topics/data/autobackup.html#IncludingFiles)

**[Example]**

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
