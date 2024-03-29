---
layout: post
title:  "認識設計模式"
categories: "DesignPattern"
content-title: "準備"
marp: true
description: "設計模式是什麼東西? 為什麼要學這東西?"
---

> 每一個模式都在描述某種一再出現的問題，並描述解決方案的核心，讓你能據以變化出各種招式，解決上萬個類似的問題。
>
> -- Christopher Alexander

# 設計模式在講什麼?

在深入研究有哪些設計模式、那些設計模式是在解決怎樣的情況之前，先來聊聊 

- _設計模式_ 到底是什麼東西? 
- 為什麼會有這個東西的存在? 
- 為什麼需要學習這東西?


## 為什麼需要設計模式

##### 最根本的難題
> 程式設計師面臨一個基本價值的難題。有著數年以上經驗的開發者，都知道之前的爛程式會降低他們的效率。
> 然而，開發者都感受過**截止期限的壓力**，所以只好產生一些爛程式來達到目標。
> 總之，他們並沒有花時間來讓開發速度變得更快。
> 
> 真正的專家會知道難題的第二個部分是錯的。你製造了爛的程式**並不會**因此趕上截止日期，
> 事實上，爛程式只會馬上讓你的開發速度變得很慢，並導致你錯過截止日期。
> 在截止日期前完成工作的**唯一**方法 --讓開發速度變快的**唯一**方法-- 就是隨時隨地，
> 都確保程式碼盡可能的整齊潔淨
>
> -- [Clean Code](#參考)


面對需求或問題，撰寫出解決問題或滿足需求的程式，最後的結果程式，就是一種 **模式**，而在思考和安排程式怎麼寫的過程，就是在 **設計**。

所以基本上，所有為了解決需求或問題而設計的程式寫法，都可以說是一種 **模式**，但是這些自己土炮的模式，是否在每一次需求調整時又發現之前設計的內容有需要調整，但是一調整又會牽一髮動全身，遇到種種 **卡住** 感覺的問題，最後又只能怪罪於 **一開始的模式沒設計好**。

為了避免這種尷尬的情況，所以在撰寫程式的時候，就會特別注意 `增加情況改變時程式調整的彈性`；而要做到這點，同時就需要考慮 `提高程式的可再利用性`，當然最後也希望保持 `程式的易讀性`。

#### 說白了，就是要程式**好維護**

那怎樣算是 **好維護**? 個人認為要符合以下幾點 : 

1. **好看** : 表示程式結構清楚明瞭，無論是變數名稱設計或者是註解寫得好或者各種方法，就是要讓人`輕易`看得懂
1. **好改** : 需求隨時更改的時候，程式碼相對應的調整可以很快速的變動，要做到這點**理所當然**必須要先符合前一點，不然看不懂是要怎麼改?
1. **改的時候很安全** : 需求改變改的很快很好，但是不能改 A 壞 B 啊，這種程式改的人也會改得瑟瑟發抖，除非是一次性程式，不然就算符合以上兩點，這點沒做到了話，那真的是前功盡棄

---

實際上，**一開始就設計好的模式** 並不存在，沒有人會知道未來會發生什麼事，或者會遇到怎樣的需求改變。

在這種情況下，我們會希望能在撰寫程式時，保持程式的彈性，讓以後好改、改的安心、新功能也好開發。

#### 那如何在 `未來未知` 的條件下又寫出 `讓以後好改、改的安心、新功能也好開發` 的程式?

物件導向的程式已經行之有年，很多專家把一些常發生的問題及設計模式的方法做了歸納和整理，而這些被整理好的模式，就是現在看到所謂 **XXX 模式** 的寫法。

每一個模式都在解決某些情況，學習這些模式有助於不知該如何設計時，能從這些專家歸納的模式中尋找一些方向或靈感。

> 每個人遇到的狀況不一定會和某個模式解決的狀況相同(甚至很難完全相同)，但是可以從學習這些模式解決的問題和解決的方法中，去設計出合適的模式，來解決當前的情況。

> 模式是可以組合使用的，不是一個模式打到底。例如常見的 MVC 模式以 `Observer`、`Composite`、`Strategy` 三個設計模式為主體，用 `Factory Method` 指定 view 的預設 controller 物件、用 `Decorator` 替 view 添加卷軸。

> 這些模式就像是各種不同的招式，當面對實際的問題時，可以依照學過的招式隨意排列組合，把問題解決，這才是學習設計模式的目的。
> 
> 並不是學了模式後所有程式都要使用模式解決，或者不用模式就很 Low；設計模式不是錘子，程式問題也從來不是釘子。

# 設計模式的描述

大神們有訂出一套描述設計模式的格式。將模式分成幾個部分來分析，會比較容易學習、比較、使用。

|:--|:--|
|名字與分類|點出模式的精神又簡潔的名稱|
|目的|這個模式是幹什麼的? 緣由和目的是什麼? 針對哪一種特殊設計事項或問題?|
|別名|其他常見的名稱|
|動機|一段真實情境，說明面臨的設計問題，以此模式的結構能怎樣解決，讓人容易體會接下來更抽象的敘述|
|時機|那些情況可運用此模式? 此模式可以匡正那些劣質設計? 該如何辨識出這些適用情況?|
|結構|以 UML 方式描繪模式裡類別之間的靜態關係和物件之間的互動關係|
|參與者|參與此模式的類別或物件，以及彼此的關係|
|合作方式|參與者如何協作完成職責|
|效果|此模式如何達成目標? 有哪些取捨及後果? 系統結構的哪一環節可單獨挑出來修改?|
|實作|實作該注意些陷阱、建議事項、技巧|
|範例|示範程式內容|
|案例|模式的真實使用案例|
|相關模式|此模式和那些模式有怎樣的關聯? 有哪些差別? 該怎麼和其他模式憶起使用?|

# 模式的分類

可以依照 **目的** 和 **範疇** 將模式分類

## 目的(Purpose)

- Creational(生成) : 物件的生成
- Structural(結構) : 類別和物件的合成
- Behavioral(行為) : 類別或物件之間的互動方式和責任分配

## 範疇(Scope)

- Class(類別) : 主要套用在類別上，處理類別與子類別之間的關係，這種基於繼承機制的關係是靜態的(在編譯時期就決定了)
- Object(物件) : 主要套用在物件上，處理物件之間的關係，此種關係比較動態(在執行時期仍可變動)

依照上述定義，就可以將現有的模式做一個簡單的分類 : 

```
+----------------+----------------------------------------------------------+
|                |                          Purpose                         |
|                +------------------+------------+--------------------------+
|                | Creational       | Structural | Behavioral               |
+-------+--------+------------------+------------+--------------------------+
| Scope | Class  | Factory Method   | Adapter    | Interpreter              |
|       |        |                  |            | Template Method          |
|       +--------+------------------+------------+--------------------------+
|       | Objcet | Abstract Factory | Adapter    | Chain of Responsiblility |
|       |        | Builder          | Bridge     | Command                  |
|       |        | Prototype        | Composite  | Iterator                 |
|       |        | Singleton        | Decorator  | Mediator                 |
|       |        |                  | Facade     | Memento                  |
|       |        |                  | Flyweight  | Observer                 |
|       |        |                  | Proxy      | State                    |
|       |        |                  |            | Strategy                 |
|       |        |                  |            | Visitor                  |
+-------+--------+------------------+------------+--------------------------+
```

---

# 小結

- 設計模式從來都不是唯一的答案，它可以說是基礎設計的基石，熟練這些基石，在實際面臨各種困難的情境時，可以更有方向的設計出相對優良的程式結構。

- 沒有說用了設計模式就很高大上，沒用就很 Low，只要能滿足`好看好改又安全`的條件，就是好程式。

- 如果用了模式會造成維護性降低，那表示模式用錯了，或者需要針對問題客製化微調，更或者要解決的問題根本不需要使用模式，Over Design 的結局大多會淪為 `技術債`。

---

# 延伸閱讀

## 如何挑選模式

真的碰到問題時，要如何選擇使用哪種模式，或者以哪種模式為主體?

可以參考下列方向 :

- 思考模式如何解決設計問題
    透過決定物件的規模大小、制定物件的介面、制定物件實作、再利用機制、委託。。。等等的設計方針，用排除法慢慢過濾掉不合適的模式
- 將 **目的** 一欄整個掃描一遍
    把所有的模式處理的 **目的** 都掃過一遍，挑出幾個候選對象，再根據實際細節縮小範圍
- 研究模式之間的關係
    描繪模式之間的關係可引領你找到正確的**模式群**
- 研究目的相近的模式群
    對於目的類似的模式，研究它們之間的差異，也可以幫助釐清選擇
- 審視設計的原因
    思考哪些因素讓你想要使用模式
- 思考設計之中有哪些可以變動的地方
    依照希望那些地方是可變動的，來挑選可以符合需求的模式


#### 設計模式允許自由更動的層面

|    目的    | 模式                    | 可變動層面                                       |
|:----------:|-------------------------|--------------------------------------------------|
| Creational | Abstract Factory        | 成品物件群                                       |
|            | Builder                 | 複合物件的生成                                   |
|            | Factory Method          | 具體的子類別物件                                 |
|            | Prototype               | 具體的類別物件                                   |
|            | Singleton               | 類別的唯一物件個體                               |
| Structural | Adapter                 | 物件的介面                                       |
|            | Bridge                  | 物件的實作                                       |
|            | Composite               | 物件的結構及組成                                 |
|            | Decorator               | 物件的權責(在未使用子類別繼承的前提下)           |
|            | Facade                  | 子系統的介面                                     |
|            | Flyweight               | 物件耗用的儲存空間                               |
|            | Proxy                   | 物件的存取方式；物件所在位置                     |
| Behavioral | Chain of Responsibility | 可回應訊息要求的物件                             |
|            | Command                 | 何時、如何回應訊息要求                           |
|            | Interpreter             | 語言的語法及詮釋                                 |
|            | Iterator                | 如何存取及尋訪集合中的各個元素                   |
|            | Mediator                | 那些物件會被彼此互動、如何互動                   |
|            | Memento                 | 物件的那些資訊會在何時另存他處                   |
|            | Observer                | 被多少其他物件相依；他們如何維持在最新的狀態     |
|            | State                   | 物件狀態                                         |
|            | Strategy                | 演算法                                           |
|            | Template Method         | 演算法的步驟                                     |
|            | Visitor                 | 可在不改變物件所屬類別的前提下施於物件身上的操作 |

## 如何使用模式

1. 將此模式整個讀過一遍，求得全貌。特別注意 **時機** 和 **效果**，確定此模式真的能解決你的問題
2. 再次確定 **結構**、**參與者**、**合作方式**
3. 研究 **範例**
4. 替模式取個合乎你的情境的名字。例如使用 Strategy 模式，可以把類別取為 `XXXStrategy` 之類的
5. 定義類別，宣告介面及繼承關係
6. 根據模式所指示的操作名稱來定義應用程式識別字，最好參酌應用程式的性質、每個操作的職責及互動來命名，盡量維持一致的命名慣例，例如 `factory method` 就會用 `Create-` 這種固定的前置詞。
7. 參考 **實作** 及 **範例** 將模式中各操做的職責及互動具體實做出來

---

# 參考 

- [物件導向 程式設計 (可再利用物件導向軟體之要素) / Design Pattern, Gamma Johnson & Helm Vlissides 著；葉秉哲譯 -- 台灣培生教育，2016.11](https://www.tenlong.com.tw/products/9789572054116)

- [無暇的程式碼：敏捷軟體開發技巧守則 / Clean Code, Robert C. Martin 著；戴于晉，博碩文化編譯 -- 新北市：博碩文化，2013.03](https://www.tenlong.com.tw/products/9789862017050?list_name=srh)