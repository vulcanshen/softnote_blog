---
layout: post
title:  "多專案建置管理"
categories: "gradle"
content-title: "Gradle 多專案建置管理"
marp: true
description: "如何用一個建置檔管理多個專案"
---

> 一個大型應用程式通常會包含很多專案，每一個專案都有自己的 build 檔，管理起來就會有點不方便，
> 此篇記錄如何用一個建置檔來管理多個專案

# 目標

只用一個建置檔處理多專案的建置

# 實作

檔案結構為下

```cmd
\_ project.repo
  \_ src
    \_ main
      \_ java
        \_ MeMethods.java
\_ project.product.a
  \_ src
    \_ main
      \_ java
        \_ HelloProjectA.java
```

Methods.java

```java
public class MyMethods {
    public static void println(String line) {
        System.out.println(line);
    }
}
```

HelloProjectA.java

```java
public class HelloProjectA {
    public static void main(String [] args) {
        MyMethods.println("HelloProjectA");
    }
}
```

## 階段目標一

要在 HelloProjectA.java 中呼叫 MyMethods.java，需要建立 project.product.a 對 project.repo 的依賴

- 在根目錄創建 setting.gradle 和 build.gradle 檔案
    ```cmd
    \_>project.repo
    \_> project.product.a
    \_ build.gradle
    \_ setting.gradle
    ```

  - setting.gradle : 設定要建置的專案

    ```groovy
    include 'project.repo', 'project.product.a'
    ```

  - build.gradle : 設定專案插件、專案依賴

    ```groovy
    subprojects {
        apply plugin: 'java'
    }

    project(':project.repo') {}


    project(':project.product.a') {
        dependencies {
            implementation project(':project.repo')
        }
    }
    ```

    > 在根目錄執行 `gradle build -i` 可以看到建置順序是先建置 project.repo 然後再建置 project.product.a

- HelloProjectA.java 調整程式碼

  ```java
  public class HelloProjectA {
      public static void main(String [] args) {
          MyMethods.println("HelloProjectA"); // output:HelloProjectA
      }
  }
  ```
  
  > 設定專案依賴後，就可以直接呼叫 `MyMethods` 的方法

## 階段目標二

在 project.repo 引入外部函式庫 (apache-common-lang)，創建新方法並且在 ProjectProductA.java 中呼叫

- 調整 build.gradle 中對 `subprojects` 的設定，加入遠端函式庫倉儲 (Maven)

  ```groovy
  subprojects {
      repositories {
          mavenCentral()
      }
      apply plugin: 'java'
  }
  ```

- 調整 build.gradle 中對 `project.repo` 的設定，加入對 apache-common-lang 的依賴

  ```groovy
  project(':project.repo') {
      dependencies {
          implementation group: 'org.apache.commons', name: 'commons-lang3', version: '3.12.0'
      }
  }
  ```

- 調整 MyMethods.java

  ```java
  import org.apache.commons.lang3.StringUtils;
  public class MyMethods {
      public static void println(String line) {
          System.out.println(line);
      }

      public static void printList(Object ... args) {
          System.out.println(StringUtils.joinWith(".", args));
      }
  }
  ```

- 測試 HelloProjectA.java 是否能正常呼叫

  ```java
  public class HelloProjectA {
    public static void main(String [] args) {
      MyMethods.println("HelloProjectA"); // output:HelloProjectA

      MyMethods.printList(1,2,3,4,5); // output:1.2.3.4.5
    }
  }
  ```

  > 注意此時在 HelloProjectA.java 中直接使用 `StringUtils.joinWith` 是會失敗的，因為 project.product.a 並沒有引入外部函式庫，引入的是 project.repo

## 階段目標三

讓 ProjectProductA.java 也能使用 project.repo 引入的外部函式庫

- MyMethods.java 建立新方法回傳 `org.apache.commons.lang3.tuple.Pair`

  ```java
  import org.apache.commons.lang3.tuple.ImmutablePair;
  import org.apache.commons.lang3.tuple.Pair;

  import org.apache.commons.lang3.StringUtils;

  public class MyMethods {
      public static void println(String line) {
          System.out.println(line);
      }

      public static void printList(Object ... args) {
          System.out.println(StringUtils.joinWith(".", args));
      }

      public static <K, V> Pair<K, V> makePair(K key, V value) {
          return new ImmutablePair<>(key, value);
      }
  }
  ```

- HelloProjectA.java 呼叫新方法

  ```java
  public class HelloProjectA {
      public static void main(String [] args) {
          MyMethods.println("HelloProjectA");

          MyMethods.printList(1,2,3,4,5);

          System.out.println(MyMethods.makePair("IntValue", 2));
      }
  }
  ```

  > 這時候會顯示錯誤 `Cannot access org.apache.commons.lang3.tuple.Pair`，因為 project.product.a 沒有引入外部函式庫，當然可以在 build.gradle 裡面的 `project(':project.product.a')` 區塊加入新的 dependencies，但是這樣做並不好，因為既然這個函式庫是由 project.repo 引入的，那就應該由 project.repo 來建立連結，不然以後每個子專案都還要知道上層函式庫引用了那些外部函式庫，然後一一設定，這樣實在太糟糕了

- project.repo 使用 `java-libary` 插件，並且將 `implementation` 範圍改成 `api`

  ```groovy
  project(':project.repo') {
      apply plugin: 'java-library'
      dependencies {
          api group: 'org.apache.commons', name: 'commons-lang3', version: '3.12.0'
      }
  }
  ```

  > 這時候再回去 HelloProjectA.java 就會發現錯誤消失了，因為 project.repo 已經建立 `傳遞型依賴 (Transitive dependiencies)`

