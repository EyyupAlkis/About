---
layout: post
title: Version Catalog
categories:
  - "Android"
  - "Build"
  - "Notes to myself"
lang: en
---
![](/img/version-catalog/version-catalog.png)

If you, as an Android developer, want to speed up your builds, you need to consider your dependency management approach. Before going through a massive architectural refactor or starting to make your app multi-module, you need to try version catalogs first. You will see certain improvements for the sake of development, and the team will save some time. In the meantime, you are free to make big changes to your project to improve your build times.

<!--more-->

Version catalog provides ability to keep and manage your dependencies in same place. Since Gradle 7.0 version, it is possible to use in our projects. A file called ***libs.versions.toml***, which should be located in the **/gradle** folder in a project. With this file we can define our dependencies for the project.

<p align="center">
  <img src="/img/version-catalog/file-location.png" />
</p>


At the moment now android studio only supports version catalogs in TOML format. But it is easy to catch up. Basically this toml file consists of 4 sections. 

```toml
[versions]

[libraries]

[plugins]

[bundles]
#libs.versions.toml
```
The `[versions]` section is for declaring versions which can be referenced by dependencies. Also you can define your build constants such as `targetSdk`, `versionCode` etc. Also you can see warnings about available new versions. 
<p align="center">
  <img src="/img/version-catalog/toml-1.png" />
</p>

The `[libraries]` part is for declaring the aliases for dependencies. As shown below, `version.ref` is for getting reference of version from `[versions]` part. Also there are 2 different namings for referencing dependencies; `group` and `module`. `module` is full definition of library without seperating two parts. And `group` can be used some libraries that share same root, and `name` part differs them each other. 

```toml
[versions]
retrofit2 = "2.9.0"
coroutinesCore = "1.7.3"

[libraries]
retrofit-core = { group = "com.squareup.retrofit2", name ="retrofit", version.ref = "retrofit2" }
retrofit-gson = { group = "com.squareup.retrofit2", name="converter-gson", version.ref = "retrofit2" }
coroutines = { module = "org.jetbrains.kotlinx:kotlinx-coroutines-core", version.ref = "coroutinesCore" }
#libs.versions.toml
```

```gradle
  implementation ("com.squareup.retrofit2:retrofit:2.9.0")
  implementation ("com.squareup.retrofit2:converter-gson:2.9.0")
  implementation ("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.3")
//build.gradle.kts
```

Example usage of catalogs;

```gradle
dependencies {
    implementation(libs.retrofit.core)
    implementation(libs.retrofit-gson)
    implementation(libs.coroutines)
}//build.gradle.kts
```

the `[plugins]` part is for declaring plugins.

```toml
[plugins]
com-android-application = { id = "com.android.application", version.ref = "agp" }
org-jetbrains-kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "org-jetbrains-kotlin-android" }
org-jetbrains-kotlin-jvm = { id = "org.jetbrains.kotlin.jvm", version.ref = "org-jetbrains-kotlin-jvm" }
com-android-library = { id = "com.android.library", version.ref = "agp" }
#libs.versions.toml
```

Plugins can be referred as:

```gradle
plugins {
    alias(libs.plugins.com.android.application) apply false
    alias(libs.plugins.org.jetbrains.kotlin.android) apply false
    alias(libs.plugins.org.jetbrains.kotlin.jvm) apply false
    alias(libs.plugins.com.android.library) apply false
}//build.gradle.kts
```

The `[bundles]` part is for grouping dependencies that have similar contexts. The most known example is retrofit library. We need moshi or gson for retrofit for most of times. So we can create bundle for that and we can define once for all places can be used. 
  
```toml
[libraries]
retrofit-core = { group = "com.squareup.retrofit2", name ="retrofit", version.ref = "retrofit2" }
retrofit-gson = { group = "com.squareup.retrofit2", name="converter-gson", version.ref = "retrofit2" }

[bundles]
retrofit = ["retrofit-core", "retrofit-gson"]
#libs.versions.toml
```

usage of bundles;

```gradle
dependencies {
  implementation(libs.bundles.retrofit)
}//build.gradle.kts
```

>FUN FACT: If your application is multi-module, then you can group each module's dependencies via bundles then you can manage them all in the same line. You can see example usage down below: 

```toml
[libraries]
core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "core-ktx" }
junit = { group = "junit", name = "junit", version.ref = "junit" }
androidx-test-ext-junit = { group = "androidx.test.ext", name = "junit", version.ref = "androidx-test-ext-junit" }
espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espresso-core" }
appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }

[bundles]
feature-login = ["core-ktx","appcompat","material","constraintlayout"]
feature-login-test = ["junit"]
feature-login-android-test = ["androidx-test-ext-junit", "espresso-core"]
#libs.versions.toml
```

these bundles can be referenced from build.gradle file in login module as: 

```gradle
dependencies {
    implementation(libs.bundles.feature.login)
    testImplementation(libs.bundles.feature.login.test)
    androidTestImplementation(libs.bundles.feature.login.android.test)
}//build.gradle.kts
```

### Pros & Cons

- Version Catalog is better than a buildSrc module for our dependencies. This is because any change within buildSrc would invalidate the entire cache and require the entire project to be recompiled. This causes longer build times. But in the version catalog, changing a dependency version would only effect the parts that use that dependency. This may not be a problem for small projects but its impact would be huge when dealing with projects with multiple modules
- You do not need to create another module for dependencies like buildSrc. It is convenient to use in multi-module applications. All variables in version catalog will be accessible for each gradle file in the application.
- You do not have to give much attention and time as `ext` blocks. 
- Flexible and easy to use. You can also extend the use of dependencies via bundles.
- It is easier to maintain than other methods. It gives you hints about version updates of libraries. 
  



