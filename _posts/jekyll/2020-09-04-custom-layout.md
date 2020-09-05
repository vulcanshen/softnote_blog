---
layout: post
title:  "自訂外觀布局"
categories: "Jekyll"
content-title: "Theme and Layout"
description: "不滿足於 Jekyll 內建的 minima 外觀 - 如何手工或者套用其他網路上模板, 本站如何套用 SB Admin 這套 Bootstrap 4 的模板, 基本 Liquid 語法"
---

# 外觀 (Theme)

外觀是一整套風格統一然後擁有不同布局的統稱, 像是預設的 minima 就有四種布局, 所以其實外觀是由布局組成的, 重點在於布局

以 minima 為例子

minima 中的 `_layouts/default.html` `_layouts/page.html` `_layouts/post.html` `_layouts/home.html` 都是 minima 的布局

這四個風格統一的布局整合為一個 theme 叫做 minima

在這篇文章撰寫的時候我只自訂了一個布局, 然後通通使用這個布局, 之後需不需要其他布局, 那之後再說了~

# 布局 (Layout)

布局本身就是一個 html 檔, 放在 `_layouts/` 目錄中, 檔案名稱就是這個布局的名稱

原生自帶 minima 不太滿足我想要的 UI 介面需求, 所以我去找了[這個布局](https://github.com/StartBootstrap/startbootstrap-sb-admin)

我並沒有刪掉原本自帶 minima theme 的設定, 而是創建了一個新的檔案 `_layouts/base.html`, 然後將文章和首頁都套用這個布局

這個布局詳細就請自己看原始碼 (點擊右上方頭像), 基本上就是把想要的東西留下不要的東西砍掉, 有幾點要注意

## 套用模板

左邊可以看到我依照文章的類別來做分類, 然後點擊展開類別的文章, 

首先每一篇文章可以有多個類別, 而我自己定義我每篇文章只屬於一個類別, 所以才這樣分類

所以我要做的就是拿到 categories 的陣列, 然後再依照每一個 category 裡面的文章去做 html 的盤版就可以了

Jekyll 中使用 [Liquid 語法](https://shopify.github.io/liquid/), 有興趣可以研究看看, 這邊只展示目前使用到的

### 取值

語法 : `{%raw%}{{變數名稱}}{%endraw%}`

以下語法就可以打印出這個站所有的類別

{%raw%}
```liquid
{{site.categories}}
```
{%endraw%}

回傳 :
> {{site.categories}}

可以看到取出來的是以類別名稱當 key 然後所屬文章陣列當 value 的 Map 型別變數,

每一篇文章是一種 `Jekyll::Document` 的物件, 詳細變數和物件可以查詢 [Jekyll 變數](https://jekyllrb.com/docs/variables/)

### For 迴圈

{%raw%}
```liquid
{% for category in site.categories %}
{{category.first}}
{% endfor %}
```
{%endraw%}

回傳 :
> {% for category in site.categories %}
> {{category.first}}
> {% endfor %}

`first` 可以拿到 category 物件的 key, 也就是類別名稱, 而 `last` 就可以拿到 value, 也就是文章物件的陣列

所以我把類別名稱當左方選單的標題, 接下來我要把每個類別的文章依照時間盤敘後當成展開的元素

{%raw%}
```liquid
{% for category in site.categories %}
    {% assign sortedPosts = category.last | sort: 'date' %}
    {% for post in sortedPosts %}
        url={{site.baseurl}}{{post.url}}, title={{ post.title }}
    {% endfor %}
{% endfor %}
```
{%endraw%}

> {% for category in site.categories %}
>     {% assign sortedPosts = category.last | sort: 'date' %}
>     {% for post in sortedPosts %}
>         url={{site.baseurl}}{{post.url}}, title={{ post.title }}
>     {% endfor %}
> {% endfor %}

拿到了 url 和文章的標題, 有這些資訊加上模板設計好的 css, 直接套用就會出現效果了

可以看到實際上文章的 url 和編輯時的檔案結構不太相同, 那些是 Jekyll 做的, 我們都不用管 (喔耶!)

用這種引擎就可以把注意力專注在內容上, 用 markdown 就好了, 至於外觀或編排那些東西, 懶得搞就用預設吧

我是有特別想做的才搞了一個自己的布局, 不過因為打算整個站都用這個布局, 所以也願意花時間在這個功夫上

要特別注意的是 `site.baseurl`

### site.baseurl

這個東西是在 `_config.yml` 中的一個屬性, 預設創建專案時這後面會帶個解釋 `# the subpath of your site, e.g. /blog`

是什麼意思呢?

意思是, 這個專案上去網站後的 `根目錄`

如果直接打 domain 後就是這個網站, 以我為例就是 `https://shenyun2304.github.io/`

那這個 `site.baseurl` 就可以設定為空字串

但是我並沒有要把我 github 帳號的根目錄以這個部落格代替, 我想要分專案來做

所以我的設定就是 `/softnote_blog`, 剛好對應到的是這個專案的 repo 名稱

想完整了解可以參考 [github pages 官方解說](https://youtu.be/2MsN8gpT6jY)

### 展示文章內容

當一切外觀布局準備就緒, 要呈現文章內容只需要簡單的

{%raw%}
```liquid
{{content}}
```
{%endraw%}

以我這個站為例, 就是在這個模板的

```html
<main>
    <div class="container-fluid">
        <h1 class="mt-4">這邊會顯示文章標題</h1>
        <div class="mb-4">
            這邊會顯示文章內容
        </div>
    </div>
</main>
```

這兩個地方換成每篇文章對應的值

而我的寫法是

{%raw%}
```html
<main>
    <div class="container-fluid">
        <h1 class="mt-4">{{ page.content-title }}</h1>
        <div class="mb-4">
            {{content}}
        </div>
    </div>
</main>
```
{%endraw%}

其中 `page.content-title` 是我在每一篇文章上方 `Front Matter` 中定義的屬性,

我希望左方選單顯示的標題和實際頁面上的標題不同, 所以自己另外定義了 `content-title` 這個屬性

而 {%raw%}`{{content}}`{%endraw%} 就可以拿到文章經過 Jekyll 轉換後的 html 碼了


# 文章前綴物

都用好以後, 只要在文章最上方的前綴物設定好自己定義的屬性值, 一切就準備上路了

例如這篇文章的前綴物是

```yml
layout: base
title:  "自訂外觀布局"
categories: "Jekyll"
content-title: "Theme and Layout"
```

- 使用布局 : `base.html`
- 左方選單顯示字眼 : `自訂外觀布局`
- 左方選單所屬分類 : `Jekyll`
- 文章內容顯示大標題 : `Theme and Layout`

> 可以看到我其實並沒有指定 `date` 屬性, 但是在排序的時候卻還是可以依照 `date` 來排序
>
> 那是因為如果不設定 `date` 屬性了話 Jekyll 會以檔案創建的時間當作 `date` 值
>
> 而我暫時沒有特別要改變順序的需求, 就直接這樣用了
>
> 以後如果真的有需要指定順序, 可能考慮再用一個自訂屬性 `order` 來當排序依據吧~

以上

附上相關參考連結 :

- [Liquid 語法](https://shopify.github.io/liquid/)
- [github pages 官方解說](https://youtu.be/2MsN8gpT6jY)
- [SB admin 模板](https://github.com/StartBootstrap/startbootstrap-sb-admin)