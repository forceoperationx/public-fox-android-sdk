[TOP](../../../README.md)　>　AndroidStudio projects

---

# AndroidStudio projects

## Integrating when using Gradle

When integrating the F.O.X SDK into a gradle project, add the following to your project's build.gradle file.

```
repositories {
    maven {
        url "https://github.com/forceoperationx/public-fox-android-sdk/raw/master/mavenRepo"
    }
}

dependencies {
    implementation 'co.cyberz.fox:track-core:{VERSION}'
}
```

## Manual Installation

These are instructions for adding `AppAdForce.jar` to your Android Studio project

* Insert the `AppAdForce.jar` to your project's libs folder.


![integration01](./img01.png)

Open the `build.gradle` file in your application's module directory and add the path to the `foxtrack-core_{VERSION}.jar` under dependencies.

```
dependencies {
	implementation files('libs/foxtrack-core_{VERSION}.jar')
}
```

![integration02](./img02.png)


---
[Return to Top](../../../README.md)
