---
layout: post
title:  "VSCODE+FISH"
categories: "IDE"
content-title: "WINDOWS+VSCODE+FISH"
marp: true
description: "在 windows vscode 上整合 fish 終端機"
---

希望能在 windows 上的 vscode 整合終端機中使用 fish shell 並且可以支援 omf emoji-powerline theme

# 想法

- 安裝 Ubuntu-wsl-2
- 在 Ubuntu-wsl-2 上安裝 fish 並且設定預設 shell 為 fish
- 在 vscode 上的終端機嵌入 Ubuntu-wsl-2
- 在 vscode 上的 Ubuntu-wsl-2 終端機安裝 omf 並且選用 emoji-powerline theme

# 第一步 : WSL 2

選擇所在客體電腦適合的方法

- [Windows 10 2004 版和更高版本 (組建19041和更新版本) 或 Windows 11](https://docs.microsoft.com/zh-tw/windows/wsl/install)
- [較舊版本的 WSL 手動安裝步驟](https://docs.microsoft.com/zh-tw/windows/wsl/install-manual)

###  確認

打開 windows powershell 輸入 : `wsl --status` 確認預設版本為 : 2

```cmd
PS> wsl --status
預設版本: 2
```

### 實際情況

因為之前安裝過 wsl-1，這次不管怎麼安裝都裝不到 wsl-2，一直都是 wsl-1，最後安裝 [較舊版本的 WSL 手動安裝步驟](https://docs.microsoft.com/zh-tw/windows/wsl/install-manual) 資源中的 [WSL2 Linux 核心更新套件 (適用於 x64 電腦)](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) 才解決

---

# 第二步 : 安裝 Ubuntu

打開 windows store 搜尋 `Ubuntu`，擇一安裝

安裝完成後啟動程式，會打開一個 Ubuntu 的終端機

並進行作業系統初始化作業

設定登入者的名稱密碼後看到命令提示字元跳出來就算完成了

![image]({{site.baseurl}}/assets/image/wsl2-ubuntu-20-install-completed.png)


### 確認

在 powershell 中輸入 : `wsl -l -v`

確認 Ubuntu 的版本是 2

```cmd
PS> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-20.04    Running         2
```

### 實際情況

我選擇的 Ubuntu 版本是 20.04 LTS，理論上應該都可以，雖然看分數有點抖

---

# 第三步 : 安裝 fish

打開 ubuntu-wsl 終端機

- 更新 apt : `sudo apt update`
- 安裝 fish : `sudo apt install fish`

### 確認

安裝完畢後輸入 `fish` 正常要進入 fish shell 的狀態

```cmd
$ fish
Welcome to fish, the friendly interactive shell
Type `help` for instructions on how to use fish
>
```

### 實際情況

一開始忘記做 `apt update`，直接安裝 fish 會顯示找不到 package

用 `apt-get` 或是 `apt` 是看 Ubuntu 版本的差異，我安裝 20.04 直接用 `apt` 就好

---

# 第四步 : 安裝 omf

依照 [omf 步驟說明](https://github.com/oh-my-fish/oh-my-fish)

先回到 bash 環境，然後輸入 :

```cmd
curl https://raw.githubusercontent.com/oh-my-fish/oh-my-fish/master/bin/install | fish
```

### 確認

執行完畢後會顯示

```cmd
$ curl https://raw.githubusercontent.com/oh-my-fish/oh-my-fish/master/bin/install | fish
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 19035  100 19035    0     0  35184      0 --:--:-- --:--:-- --:--:-- 35119
Checking for a sane environment...
Installing Oh My Fish to /home/???/.local/share/omf...
Using release channel "stable".
Cloning master from https://github.com/oh-my-fish/oh-my-fish.git...
Writing bootstrap to /home/???/.config/fish/conf.d/omf.fish...
Setting up Oh My Fish configuration...
Updating https://github.com/oh-my-fish/packages-main master... Done!
Installing package default
✔ default successfully installed.
Installation successful!
Welcome to fish, the friendly interactive shell Type `help` for instructions on how to use fish 16:26:44   
                     16:26:44⋊>
 ```

剛裝完指標會跑版按幾下 enter 就好了

### 實際情況

執行完指標跑版的挺嚴重，不過按 enter 重置後就好了

---

# 第五步 : 安裝 emoji-powerline theme

在 fish 環境下輸入 : `omf install emoji-powerline`

### 確認

執行完畢後會直接使用 emoji-powerline theme

### 實際情況

使用是使用了，但是全部變口口字

![image]({{site.baseurl}}/assets/image/fish-emoji-powerline-miss-code.png)

猜測應該是某些 Unicode 字形不支援，找了一款字形 [Fira Code](https://github.com/tonsky/FiraCode)

下載後解壓縮

雙擊 `ttf/FiraCode-Regular.ttf` 打開字形檔後點擊視窗左上角的 "安裝" (只是打開沒有安裝喔)

![image]({{site.baseurl}}/assets/image/install-fira-code-font.png)

為了方便指定字形，另外安裝了 Windows Terminal 來啟動 Ubuntu-wsl

#### 安裝 Windows Terminal

在 Windows Store 搜尋 "Terminal" 就可以找到

![image]({{site.baseurl}}/assets/image/windows-terminal.png)

啟動後預設使開啟 PowerShell

在右上角的下拉選單中可以看到 Ubuntu 的選項

這時候打開 Ubuntu 並進入 fish 還是一樣是口口字

![image]({{site.baseurl}}/assets/image/wt-fish-miss-encoding.png)

#### 設定 Ubuntu-wsl 字體

在 Windows Terminal 右上角的下拉選單選擇 "設定"

![image]({{site.baseurl}}/assets/image/choose-windows-terminal-setting.png)


依序選擇

- 左側選擇 Ubuntu
- 右側上方分頁選擇 "外觀"
- 在字體的位置選擇 "Fira Code"

![image]({{site.baseurl}}/assets/image/set-wt-ubuntu-font.png)

實際情形是沒有看到 Fira Code 的選項

然後點選 "顯示所有字形" 再找一次還是沒有

確認一下是否字形有正確安裝 (打開 ttf 確定要按 "安裝" 才有)

然後重啟 Windows Terminal 就有了

再次啟動 Ubuntu-wsl 並進入 fish 就正常顯示

![image]({{site.baseurl}}/assets/image/fish-emoji-powerline-success-display.png)

# 第六步 : 在 vscode 內嵌 fish

啟動 vscode 打開終端機

這時候在終端機選項裡可以看到 Ubuntu WSL 的選項

![image]({{site.baseurl}}/assets/image/vscode-terminal-list-ubuntu.png)

啟動後輸入 : `fish`

結果是這個樣子 orz

![image]({{site.baseurl}}/assets/image/vscode-fish-miss-encoding.png)

進入 vscode 的工作區設定檔 `setting.json`

執行 : `ctrl + shift + p`
在命令字元 `>` 生效的條件下輸入 `json` 可以直接開啟設定檔

![image]({{site.baseurl}}/assets/image/vscode-open-setting-file.png)

將終端機置型指定為 "Fira Code"

```json
{
  "terminal.integrated.fontFamily":"Fira Code"
}

```

這時候終端機應該會刷新並且正常顯示特殊字元

最後將 ubuntu-wsl 設定 fish 為主要 shell 就完成了

在 ubuntu-wsl:bash 環境下輸入 `chsh -s /usr/bin/fish`

關閉終端機分頁後重啟 Ubuntu-wsl 終端機

就會自動進入 fish 環境並且正常顯示

![image]({{site.baseurl}}/assets/image/success-embeded-fish-in-vscode.png)

好的完畢!!