---
layout: post
title:  "基礎 Java 建置"
categories: "gradle"
content-title: "基礎 Java 建置"
marp: true
description: "基礎 Java 專案建置"
---

> 以 gradle 來製作一個 Java 專案的建置

# 步驟

1. 創立建置檔
2. 加入適當的插件
3. 調整任務和屬性

# Java 相關插件

- `java` : java 的基本插件，有 build、clean、jar...等等任務
- `java-library` : 建立 java 函式庫
- `application` : 繼承 `java` 插件，把專案包成應用程式


# java plugin

編譯與建置 Java 原始碼

### 預設原始碼檔案布局

```plain
src
    \_ main
        \_ java
        \_ resources
    \_ test
        \_ java
        \_ resources
```

> 這套預設布局和 Maven 相同

如果說真的需要調整原始碼的檔案布局，可以參考以下設定

```groovy
sourceSets {
    main {
        java {
            srcDir '客製化目錄'
        }
        resources {
            srcDir '客製化目錄'
        }
    }
}

```


#### 實作

創建一個 HelloJava.java 的檔案，並置於以下目錄結構中

```plain
src
    \_ main
        \_ java
            \_ HelloJava.java
        \_ resources
    \_ test
        \_ java
        \_ resources
```

HelloJava.java

```java
class HelloJava {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

build.gradle

```groovy
plugins {
    id 'java'
}
```

在根目錄執行 `gradle tasks`

可以看到已經可以使用 `build` 這個 task

執行 `gradle build`

成功後檔案結構變為

![image]({{site.baseurl}}/assets/image/hello-java-build.png)

可以看到 `src` 目錄都沒有被更動，編譯後的 class 檔部屬在 `build/classes/java/main` 目錄之下

執行 `java -cp build\classes\java\main HelloJava` 就可以看到打印出

```
$ java -cp build\classes\java\main HelloJava
Hello, Java!
```

的訊息

執行 `gradle clean` 就可以將 `build` 創建出來的東西全部刪除，只剩下最原始的 `src` 目錄

gradle 指令也能接受多個任務，會依序執行，例如 : `gradle clean build`，會先執行 `clean` 然後再執行 `build`

讓我們再深入一點看看 gradle 到底做了什麼

先執行 `gradle clean` 把專案重置，然後執行 `gradle build -i`

最後的 `-i` 表示把建置的資訊 (information) 打印出來

最後顯示

```plain
BUILD SUCCESSFUL in ???ms
2 actionable tasks: 2 executed
```

明明直接指定執行 `build` 任務，為什麼會有兩個任務被執行?

往上爬訊息會發現，每一個任務在執行前都會打印出

```
> Task: 任務名 狀態
```

這種格式

而後面的狀態很多是 `NO-SOURCE` 或 `UP-TO-DATE`，像這種的任務可以從他下方的訊息看到 `skipping task...` 等字樣，表示這個任務其實跳過沒有被執行，同理找到被執行的任務

爬完訊息可以看到執行的是 `Task:classes` 和 `Task:jar` 這兩個任務，具體任務報告也可以從內容中看出來是什麼情況

如果沒有 `clean` 操作，直接再執行一次 build 會怎樣呢?

直接執行 `gradle build -i`

結果

```plain
BUILD SUCCESSFUL in ???ms
2 actionable tasks: 2 up-to-date
```

意思是這個 build 動作應該要執行兩個任務，但是因為原始碼沒有變更，所以這兩個任務已經是最新狀態

往上爬看這兩個任務的訊息，可以看到都有了 `UP-TO-DATE` 的狀態顯示，並且也有 `skipping task...` 的字樣，所以其實這次的建置沒有做任何事情

---

那 jar 檔在哪? 有執行 `Task:jar` 不是應該有 jar 檔嗎? 有喔~ 

jar 檔位置在 `build/libs` 目錄下

那直接執行 `java -jar build/libs/XXX.jar` 會怎樣?

嗶嗶! 跳出 `沒有主要資訊清單屬性` 的錯誤，為什麼? 因為 jar 檔要在 MANIFEST 檔裡面標註主要類別才能執行，那怎麼讓 jar 檔有主要類別的屬性?

應該要去查什麼?

因為用的是 java 插件，jar 任務也是 java 插件提供的任務，所以有這個任務的問題，當然是去查 java 插件的設定方式囉~

...

...

...

然後就發現 [java 插件的網站](https://docs.gradle.org/current/userguide/java_plugin.html)上沒寫...

因為問題不在於 java 這個插件，因為打包 jar 檔是 `Task:jar` 這個任務在做的事情，所以應該要去查 [jar 這個任務](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html)怎麼設定 Main-Class

調整 build.gradle

```groovy
plugins {
	id 'java'
}

jar {
    manifest {
        attributes 'Main-Class': 'HelloJava'
    }
}
```

在執行一次 `gradle build`，建置結束後執行 `java -jar build/libs/XXX.jar`，就能順利打印出 Hello, Java!

# application plugin

如果要用 gradle 直接執行主類別 main 方法，而不需要建置完再手動執行 jar 檔，那 application 插件就是你要的東西

調整 build.gradle

```groovy
plugins {
    id 'java'
    id 'application'
}

jar {
    manifest {
        attributes 'Main-Class': 'HelloJava'
    }
}
```

設定好載入 application 插件後先來看看多了那些任務

執行 `gradle tasks`

發現

```plain
Application tasks
-----------------
run - Runs this project as a JVM application
```

多了 `run` 這個任務，那執行看看 `gradle run`

結果

```
> Task :run FAILED

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':run'.
> No main class specified and classpath is not an executable jar.
```

sh*t! 發生什麼事?

沒有主要類別? 可是剛剛不是已經設定主要類別了嗎?

注意剛剛設定的主要類別是設定在 `Task:jar` 任務上，所以 `run` 這個任務根本不知道主要類別在哪，一樣去找 [application 官方文件](https://docs.gradle.org/current/userguide/application_plugin.html)

調整 build.gradle

```groovy
plugins {
    id 'java'
    id 'application'
}

application {
    mainClass = 'HelloJava'
}

jar {
    manifest {
        attributes 'Main-Class': 'HelloJava'
    }
}
```

設定好後再執行一次 `gradle run`

結果

```
$ gradle run

> Task :run
Hello, Java
```

也就是說執行主類別和打包可執行 jar 檔是兩件事，那如果這樣下指令 `gradle clean run build`

是不是表示會依序執行 (1) 清理掉之前的建置目錄然後 (2) 執行主類別方法 (3) 打包?

YES! 有興趣的可以執行 `gradle clean run build -i` 來看到底執行了什麼

