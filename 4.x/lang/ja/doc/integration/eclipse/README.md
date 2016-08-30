[TOP](/4.x/lang/ja/README.md)　>　Eclipseプロジェクトへの導入

---

# Eclipseプロジェクトへの導入

Eclipseプロジェクトへfoxtrack-core_{VERSION}.jarを外部jarとして追加する方法は以下の通りです。

* アプリケーションプロジェクトの「libs」フォルダー配下にfoxtrack-core_{VERSION}.jarをコピー


![integration01](./img01.png)


* アプリケーションを選択し、右クリック>「プロパティ」を選択するとプロパティウィンドウが立ち上がる
* プロパティウィンドウの左のメニューから「Javaのビルド・パス」を選択
* プロパティウィンドウの右側の「ライブラリー」タブを選択し、「Jar追加」を選択します
* アプリケーションの「libs」フォルダに配置した「foxtrack-core_{VERSION}.jar」を選択する


![integration02](./img02.png)

---
[トップ](../../../README.md)
