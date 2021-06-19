---
layout: post
title:  "依賴管理"
categories: "gradle"
content-title: "Gradle Dependency Management"
marp: true
description: "了解 Gradle Dependency Management"
---

> 專案會使用各種函式庫，可以從本地端或線上載入函式庫，也可以設定不同情境使用不同的函式庫。
> 被專案使用的函式庫稱為專案的依賴 (dependencies)，以下記錄設定依賴的相關知識

# 函式庫倉儲

> 以下將 Library 中文稱為函式庫，而 Repository 中文稱為函式庫倉儲，或者簡稱倉儲

函式庫倉儲就是函式庫的來源，或者說就是存放一堆函式庫的地方

倉儲的來源可以分為 : 其他專案、本地端檔案系統、Maven Repo、Ivy Repo

gradle 以 `repositories` 關鍵字來設定函式庫倉儲


## 本地端檔案系統作為倉儲

假設檔案結構如下 :

```cmd
\_ build.gradle
\_ lib
    \_ log4j-1.2.8.jar
    \_ jaxb-api.2.3.1.jar
    \_ junit-3.8.1.jar
```

可以這樣設定依賴

```groovy
dependencies {
    implementation files ('lib/log4j-1.2.8.jar', 'lib/jaxb-api.2.3.1.jar')
    testImplementation files ('lib/junit-3.8.1.jar')
}
```

這樣的設定方式，就是直接指定函式庫，而沒有使用倉儲

可以把 `/lib` 設為函式庫倉儲，則在依賴函式庫的設定就可以直接使用函式庫的屬性，而不需要指定檔案名稱，如下 :


```groovy
repositories {
    flatDir {
        dirs 'lib'
    }
}

dependencies {
    implementation 'log4j:log4j:1.2.8'
    implementation group: 'javax.xml.bind', name: 'jaxb-api', version: '2.3.1'
    testImplementation 'junit:junit:3.8.1'
}
```

函式庫宣告方式可以使用簡寫 `群組:函式庫名稱:版本` 的格式宣告，像是 `log4j:log4j:1.2.8` 來表示，

也可以寫完整資訊如 : `group: 'javax.xml.bind', name: 'jaxb-api', version: '2.3.1'`

## 遠端來源作為倉儲

gradle 有內建指定 maven 和 jcenter 的遠端倉儲方法，但是也能直接指定 url

- Maven : 使用內建 `mavneCentral()` 方法
    ```groovy
    repositories {
        mavenCentral()
    }
    ```
- jcenter : 使用內建 `jcenter()` 方法
    ```groovy
    repositories {
        jcenter()
    }
    ```
- 指定 url : 直接使用指定 url 來指定 jcenter
    ```groovy
    repositories {
        url "http://jcenter.bintray.com/"
    }
    ```
    > 這樣使用和直接呼叫 `jcenter()` 方法意思相同
- 客製化 : 如果內部網路中有架設 maven 或 ivy 的函式庫倉儲，那可以這樣覆蓋設定
    ```groovy
    repositories {
        maven {
            url "http://內部 maven 函式庫來源網址"
        }

        ivy {
            url "http://內部 ivy 函式庫來源網址"
        }
    }
    ```

# 依賴範圍

依賴生效的範圍可以分為 : 編譯期 (Compilation)、執行期 (Runtime)、測試編譯期 (Test compilation)、測試執行期 (Test runtime)

## 範圍關鍵字

- `implementation` : 編譯期 (`compileOnly`) + 執行期 (`runtimeOnly`)
- `testImplementation` : 測試編譯期 (`testCompileOnly`) + 測試執行期 (`tesRuntimeOnly`)

> 所有 implementation 有用的都可以被 testImplementation 使用，不用再另外宣告

# 函式庫的依賴

某些函式庫會依賴其他的函式庫，這種情況稱為 `傳遞型依賴 (Transitive dependiencies)`

gradle 會自動載入這些傳遞型的函式庫，不需要額外再處理這些依賴

指令 : 

```cmd
gradle -q dependencies
```

可以列出目前設定中依賴的函式庫

或者

```cmd
gradle -q dependencies --configuration implementation
```

只列出 `implementation` 範圍下的依賴

## 實作

宣告以下依賴

```groovy
repositories {
    jcenter()
}

dependencies {
    implementation group: 'log4j', name: 'log4j', version: '1.2.17'
    implementation group: 'javax.xml.bind', name: 'jaxb-api', version: '2.3.1'
    testImplementation 'junit:junit:4.12'
}
```

執行 : `gradle -q dependencies`

結果 :

```cmd
compileClasspath - Compile classpath for source set 'main'.
+--- log4j:log4j:1.2.17
\--- javax.xml.bind:jaxb-api:2.3.1
     \--- javax.activation:javax.activation-api:1.2.0

implementation - Implementation only dependencies for source set 'main'. (n)
+--- log4j:log4j:1.2.17 (n)
\--- javax.xml.bind:jaxb-api:2.3.1 (n)

runtimeClasspath - Runtime classpath of source set 'main'.
+--- log4j:log4j:1.2.17
\--- javax.xml.bind:jaxb-api:2.3.1
     \--- javax.activation:javax.activation-api:1.2.0

testCompileClasspath - Compile classpath for source set 'test'.
+--- log4j:log4j:1.2.17
+--- javax.xml.bind:jaxb-api:2.3.1
|    \--- javax.activation:javax.activation-api:1.2.0
\--- junit:junit:4.12
     \--- org.hamcrest:hamcrest-core:1.3

testImplementation - Implementation only dependencies for source set 'test'. (n)
\--- junit:junit:4.12 (n)

testRuntimeClasspath - Runtime classpath of source set 'test'.
+--- log4j:log4j:1.2.17
+--- javax.xml.bind:jaxb-api:2.3.1
|    \--- javax.activation:javax.activation-api:1.2.0
\--- junit:junit:4.12
     \--- org.hamcrest:hamcrest-core:1.3
```

可以看到 gradle 幫我們把傳遞型的依賴也通通下載下來了

如果只想列出編譯期載入的函式庫，執行 `gradle -q dependencies --configuration compileClasspath`

結果 :

```cmd
compileClasspath - Compile classpath for source set 'main'.
+--- log4j:log4j:1.2.17
\--- javax.xml.bind:jaxb-api:2.3.1
     \--- javax.activation:javax.activation-api:1.2.0
```

就只會印出 `compileClasspath` 範圍依賴的函式庫