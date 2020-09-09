---
layout: post
title:  "代理與斷面"
categories: "Java"
content-title: "從代理機制到 AOP 概念理解"
description: "從 Java 靜態代理到理解 AOP 概念"
---

# 如何檢查每一個函式呼叫所使用的時間?

考慮以下情境 :

```java
public class Application {
    public static void main(String [] args) {
        Applcation app = new Application();
        app.execute();
    }
    public void execute() {
        action1();
        action2();
        actionEnd();
    }
    private void action1() {
        //...
    }
    private void action2() {
        //...
    }
    private void actionEnd() {
        //...
    }
}
```

如果主程式在呼叫 `execute()` 方法時發現異常的久，但是在開發環境又不會發生這種問題，那該怎麼辦? 最直接的方法就是在每個呼叫前紀錄時間，並且在呼叫後結算，例如 :

```java
public void execute() {
    long ts = System.currentTimeMillis();
    action1();
    System.out.println("action1 use:"+System.currentTimeMillis()-ts);
    ts = System.currentTimeMillis();
    action2();
    System.out.println("action2 use:"+System.currentTimeMillis()-ts);
    ts = System.currentTimeMillis();
    actionEnd();
    System.out.println("actionEnd use:"+System.currentTimeMillis()-ts);
}
```

這麼**性情中人**的寫法確實有達到期望的效果啦，但是調整的程式也太多了吧，而且如果呼叫的方法越多要修改的地方也就越多，等問題調整完後又要一一地把這些地方改回來，我忽然覺得頭有點痛。

> 有沒有個方法可以只在一個地方做時間的紀錄? 並且不影響原始程式碼，可以隨時移除或插入?

# 代理模式

`Proxy Pattern`

這裡把實現方法內容的物件稱為**執行物件**，以上述範例就是 `Application` 的物件；而**代理物件**就是代理執行物件函式呼叫的物件，如下 :

```java
public interface ApplicationInterface {
    void execute();
    void action1();
    void action2();
    void actionEnd();
}

public class ApplicationProxy implements ApplicationInterface {
    private final delegate;
    public Proxy(ApplicationInterface target) {
        delegate = target;
    }
    @Override
    void execute() {
        long ts = System.currentTimeMillis();
        action1();
        System.out.println("action1 use:"+System.currentTimeMillis()-ts);
        ts = System.currentTimeMillis();
        action2();
        System.out.println("action2 use:"+System.currentTimeMillis()-ts);
        ts = System.currentTimeMillis();
        actionEnd();
        System.out.println("actionEnd use:"+System.currentTimeMillis()-ts);
    }
    @Override
    void action1() {
        delegate.action1();
    }
    @Override
    void action2() {
        delegate.action2();
    }
    @Override
    void actionEnd() {
        delegate.actionEnd();
    }
}

public class Application implements ApplicationInterface {
    public static void main(String [] args) {
        // Applcation app = new Application();
        ApplicationProxy app = new ApplicationProxy(new Application());
        app.execute();
    }
    @Override
    public void execute() {
        action1();
        action2();
        actionEnd();
    }
    @Override
    private void action1() {
        //...
    }
    @Override
    private void action2() {
        //...
    }
    @Override
    private void actionEnd() {
        //...
    }
}
```

改變了什麼?

1. 在時間計算和打印的地方，從原本的 `Applcation` 類別移到 `ApplicationProxy` 中。
2. 主程式原本直接呼叫 `Application` 的 `execute()` 方法換成 `ApplicationProxy` 的 `execute()` 方法。

效果是?

- 主程式呼叫的是代理物件，由代理物件去呼叫執行物件的方法，所以當問題處理完畢，只需要把主程式中的呼叫代理物件改回程呼叫執行物件的 `execute()` 方法就好，對於執行物件的程式碼完全沒有更動，先解決了事後要重新改回來的災難。

> OK! 這樣看似解決了修改到原始程式碼的問題，但是那段計算時間和打印的程式一直重複沒有解決阿!
>
> 而且這樣搞不就變成每一個類別都要配一個該類別的代理類別，然後又要多補上一個介面讓兩個類別實作，這樣的**犧牲**是不是太大了? 變相提高了專案的維護難度。

上述這種代理的寫法稱為**靜態代理**

#### 靜態代理
每個執行類別搭配一個代理類別，而兩個類別實現同一個介面

代理類別會儲存執行類別的物件 (delegate)，外部主程式呼叫所有介面的函式都是經過代理呼叫

所以代理可以在每一個執行物件執行方法時做任何調整

> 在物件導向設計原則(SOLID)中有提到依賴反轉(Dependency inversion principle, DIP)原則，所以雖然要創建執行類別的介面，個人覺得這點還可以接受。
>
> 而其他 POJO 類別其實根本不需要代理，也不會有這種困擾。
>
> 不過每個執行類別都要搭配一個代理類別然後實現方法要寫兩次，這實在不太能接受。

# 動態代理

`Dynamic Proxy`

從 JDK 1.3 之後加入了可協助開發動態代理功能的 API，讓我們不必再為了類別撰寫搭配的代理類別。

代理類別可以轉為功能導向，而不只是執行類別的附屬，例如上述例子是要做執行時間的計算並打印出來，可以創建一個專門是做打印的代理，而不用每個執行類別都搞一個代理類別。

考慮以下情境 :

```java
public class LogHandler implements InvocationHandler {
    private Object delegate;
    public Object bind(Object target) {
        this.delegate = target;
        return Proxy.newProxyInstance(
            target.getClass().getClassLoader(),
            target.getClass().getInterface(),
            this);
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object result = null;
        try {
            long ts = System.currentTimeMillis();
            result = method.invoke(delegate, this);
            System.out.println(method.getName()+" use:"+System.currentTimeMillis()-ts);
        } catch(Throwable t) {
            t.printStackTrace();
        }
        return result;
    }
}

public interface ApplicationInterface {
    void execute();
    void action1();
    void action2();
    void actionEnd();
}

public class Application implements ApplicationInterface {
    public static void main(String [] args) {
        LogHandler handler = new LogHandler();
        ApplicationInterface proxy = (ApplicationInterface) handler.bind(new Application());
        proxy.action1();
        proxy.action2();
        proxy.actionEnd();
    }
    @Override
    public void execute() {
        action1();
        action2();
        actionEnd();
    }
    @Override
    private void action1() {
        //...
    }
    @Override
    private void action2() {
        //...
    }
    @Override
    private void actionEnd() {
        //...
    }
}
```

原理和靜態代理差不多，代理也是需要儲存執行類別的物件，在情境中就是 `bind()` 方法。

`bind()` 方法回傳一個代理物件，而這個代理物件是依照目標類別創建的，並且擁有該類別介面的方法，並註冊該 Handler 給該物件。

主程式中把 `bind()` 回傳的物件強制轉型成 `ApplicationInterface`，經過 `proxy` 物件的所有方法呼叫都會經由 Handler 物件的 `invoke()` 方法利用反射來呼叫執行物件的方法。

所以只要在 `invoke()` 方法中做時間計算和打印就等於處理了所有方法，不過主程式中從原本的只呼叫 `execute()` 方法改成個別方法呼叫，是因為都得經過代理來呼叫方法，才可以有時間計算和打印的效果。如果直接呼叫 `execute()`，`execute()` 直接會執行 delegate 中的方法，不會經過代理，那就無法抓到個別方法的時間計算和打印。只會記錄到 `execute()` 方法的狀況。


> 這種就像是在每個方法的前後**中斷插入**程式碼，而執行物件本身並**不知道**自己被**插斷**了。

# 斷面 (AOP)

AOP 全名是 Aspect-Oriented Programming，是一種程式設計的概念。中文有些翻作**斷面程式設計**。






