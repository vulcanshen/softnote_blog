---
layout: base
title:  "從零開始"
categories: "Jekyll"
content-title: "開始使用 Jekyll"

---

為什麼使用 jekyll 呢?

其實一開始就打算把這東西放到 github 上，又希望能用 markdown 去寫文章，不想自己搞 html 和 css，看了 github pages 首頁拉到最下面就看到 Jekyll 的連結，也沒多想就開始了

# 下載

[Jekyll 官方安裝頁面](https://jekyllrb.com/docs/installation/)

我是在 Windows 系統上開始這個東西的，聽說用 MacOS 會更簡單

首先要安裝 Ruby 然後要安裝 Devkit 模組，在 Ruby 官方有很貼心的把這兩個東西包在一起的安裝包

[Ruby Installer](https://rubyinstaller.org/downloads/)

我選的是他網頁自動偵測後建議的那個有 `=>` 符號的版本 (Ruby+Devkit 2.6.6.1 (x64))

# 安裝

Windows 安裝程式就是無限的 `下一步`

安裝到開啟終端機的時候會跳出選項讓選，每個選項最後都會有 `If unsure press ENTER` 的敘述，反正我是不知道要怎麼選，通通直接按 `ENTER`

![image]({{site.baseurl}}/assets/image/ruby-installation.png)


# 創建

要建立一個 Jekyll 結構的文件很簡單，在安裝完之後會有一個程式叫做 `Start Command Prompt with Ruby`，這就是個會自動載入 Ruby 的終端機程式，打開這個程式，切換到想要創建 Jekyll 專案的目錄，然後輸入 : `jekyll new demo`

之後就會在當前的目錄之下創建一個 `demo` 資料夾，裡面就是 Jekyll 的結構

`demo` 是專案也是目錄的名稱，可以隨意調整

# 啟動

切換到 Jekyll 目錄之下，然後終端機輸入 : `jekyll server`

就可以看到開始啟動伺服器，預設會啟動再 `4000` port 上，啟動完成之後開啟瀏覽器並連結
`http://127.0.0.1:4000`，就可以看到 Jekyll 的歡迎畫面了

![demo-start]({{site.baseurl}}/assets/image/jekyll-demo-start.png)

# 開發環境

這樣開個終端機再另外開個文字編輯器的開發方式有點麻煩，我選擇使用 [Visual Studio Code](https://code.visualstudio.com/) (以下簡稱 vscode)，然後將 windows 的終端機載入剛剛的 `Start Command Prompt with Ruby`

在使用者的 terminal 設定中對 windows 的設定加上啟動參數

```json
"terminal.integrated.shell.windows": "C:\\WINDOWS\\System32\\cmd.exe",
"terminal.integrated.shellArgs.windows": [
    "/E:ON",
    "/K",
    "C:\\Ruby26-x64\\bin\\setrbvars.cmd"
],
```

這樣就可以在 vscode 裡面開啟可以執行 jekyll 的指令

如果每一次調整檔案內容都要重新啟動一次伺服器那也很麻煩，只要在啟動程式的時候加上 `--watch` 的參數，就會自動偵測專案內容是否有改變，只要有存檔動作就會自動重啟

指令 : `jekyll server --watch`

# 目錄結構

一開始 Jekyll 自動產生的目錄結構是這樣

```
.jekyll-cache/
_posts/
_site/
.gitignore
_config.yml
404.html
about.markdown
Gemfile
Gemfile.lock
index.markdown
```

- `_posts/` : 放所有文章的目錄，可以有任意子目路，所有文章都放這裡
- `_site/` : markdown 檔經過 jekyll 轉換成 html 檔後的**整個站**都放在這裡，裡面的檔案都是自動產生的，不需要去動
- `_config.yml` : 全域設定檔，這很重要，幾乎所有設定都寫在這

Jekyll 自動產生的 markdown 檔案副檔名都是 `.markdown`，實際測過使用 `.md` 也是可以的

預設情況下 `_posts/` 目錄中會自動產生一篇文章，這也是為什麼一開始首頁就有一篇文章的原因，

Jekyll 文章的檔案命名是有一定規則的，就如預設的那樣 `四位數年-兩位數月-兩位數日-名稱.md`，

所有的文章都要這樣命名，Jekyll 中檔名和檔案連結關係不是絕對的，不需要在這個地方考慮文章的連結位置

# 前綴物 (Front Matter)

打開預設的文章可以看到在檔案最上方有個用 `---` 包起來的區塊，這個區塊內的設定稱為**前綴物**(Front Matter - 其實這是我亂翻譯的，懶得中英切換)

```yml
---
layout: post
title:  "Welcome to Jekyll!"
date: 2020-08-30 16:49:16 +0800
categories: jekyll update
---
```

- `layout` : 指定這篇文章要使用哪個外觀布局
- `title` : 這篇文章的標題
- `date` : 指定日期，所以其實檔案名稱的日期不一定是網頁上實際呈現的日期
- `categories` : 文章類別，這個分類是自定義的，想怎麼分就怎麼分，要注意的是空白分隔並沒有階層關係，範例中 `jekyll update` 表示此篇文章同時屬於 `jekyll` 和 `update` 兩個類別中

> 所有的前綴物都可以當作是文章的屬性，可以從 [Jekyll Variable](https://jekyllrb.com/docs/variables/) 中取得

# 外觀與布局 (Theme And Layout)

Jekyll 預設使用 `minima` 外觀 (theme)，這個設定可以從 `_config.yml` 裡看到

```yml
theme: minima
```

這個外觀有四種布局 (layout) : `default`、`home`、`page`、`post`

都是走統一風格，內容稍微不同而已，具體內容有興趣可以到 `minima` 的[官方](https://github.com/jekyll/minima)看看

所有的布局檔都放在 `_layouts` 目錄中，預設沒有是因為預設已經指定 `minima` 做外觀，如果想更換外觀了話，搜尋 `jekyll theme` 就會找到一大堆

要注意的是，每一種外觀的布局都不同，布局名稱也不同，像是 `minima` 有四種布局，其他外觀就不一定，所以當切換外觀後，如果發現頁面顯示不出來，可以先檢查看看是不是新的外觀沒有某個設定的布局

例如沒有 `post`，那所有設定 `layout: post` 的檔案都會壞掉，啟動 jekyll 的時候可以看到錯誤訊息

像是 [hacker theme](https://github.com/pages-themes/hacker)，它只有 `default` 和 `post` 的布局，如果直接換掉 `_config.yml` 中的 `theme` 設定，那原本布局設定是 `page` 和 `home` 的頁面就會壞掉

