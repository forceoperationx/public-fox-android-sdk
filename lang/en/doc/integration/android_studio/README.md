### How to install to AndroidStudio project

#### Integrate F.O.X SDK by using Gradle

Add the following settings in the build.gradle.

```
repositories {
    maven {
        url "https://github.com/cyber-z/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    compile 'co.jp.cyberz.fox:sdk-android:{VERSION}'
}
```


#### Integrate F.O.X SDK manually

* Add the AppAdForce.jar to `app/libs` directory.


![integration01](./img01.png)

Add the compile dependency with the AppAdForce.jar in the build.gradle file.

```
dependencies {
	compile files('libs/AppAdForce_{VERSION}.jar')
}
```

![integration02](./img02.png)


---
[TOP](/lang/en/README.md)
