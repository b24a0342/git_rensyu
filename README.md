# git_rensyu
gitの練習です。
作成者について
こちらのディレクトリについて

必要な環境
環境構築


第１章　概要

１－１　JavaScriptとは
Webページを動的に制御するためのWebスクリプト言語である。ブラウザ上で動作するプログラミング言語である。
下記のいくつかの技術から構成される。

ECMAScript-JavaScriptの基本的な文法とオブジェクトを規定している。
ブラウザのオブジェクト-ウィンドウ制御などの機能がある。
DOM-HTML文書の要素を操作できる。



１－２　Node.jsとは
JavaScriptの実行環境の1つ。通常ブラウザ上で動作するJavaScriptをサーバーやPC上でも動かせるようにする。
ファイルの操作やWebサーバーの構築などを可能で、バックエンド開発にも使われている。
パッケージ管理ツールとしてはnpmがあり、世界中のオープンソースのライブラリを利用できる。



第２章　環境構築
２－１　WSLインストール、Dockerインストール
環境構築がまだの場合は、こちらを参照して構築する。
URL
vimもURL


２－２　JavaScript環境作成(Node.js)


①プロジェクトディレクトリの作成(ファイルを格納するフォルダ)
mkdir workspace_nodejs && cd workspace_nodejs

---------------------------------------------------------------
このような構成になる想定
workspace_nodejs/
├── Dockerfile
├── server.js
└── src
       ├── index.html
       ├── style.css
       └── main.js

---------------------------------------------------------------

②workspace_nodejs/server.jsを作成する。
---------------------------------------------------------------
const http = require("http");　　　　　←Node.js組み込みのHTTPサーバー機能
const fs = require("fs");　　　　　←ファイルシステム操作用
const path = require("path");　　　　　←OS依存なくパスを作るために使用

const PORT = 3000;   ←コンテナ側のポート番号を指定

const server = http.createServer((req, res) => {　　　　　←createServer()でリクエスト待機用サーバーを作成　引数reqはクライアントからのリクエスト情報、resはレスポンス(HTMLなど)を返すためのオブジェクト
  const filePath = path.join(__dirname, "src", "index.html");　　　　　←index.htmlの絶対パスを作る
  
  fs.readFile(filePath, (err, data) => {　　　　　←fs.readFile()でHTMLを読み込む
    if (err) {
      res.writeHead(500, {"Content-Type": "text/plain"});　　　　　←レスポンスヘッダを設定、Content-TypeはブラウザがHTMLと認識するため
      res.end("500 Internal Server Error");
      return;
    }
    
    res.writeHead(200, {"Content-Type": "text/html"});　　　　　←レスポンスヘッダを設定、200は成功、Content-TypeはブラウザがHTMLと認識するため
    res.end(data);　　　　　←読み込んだHTMLを送信
  });
});

server.listen(PORT, () => {　　　　　←指定したポートで待機、このプロセスが生きている限り、コンテナも止まらない
  console.log(`Server running at http://localhost:${PORT}/`);
});
-----------------------------------------------------------------

③workspace_nodejs/Dockerfileを準備する。→アプリを動かすための環境(OS, 言語, ライブラリ, 設定など)を自動で再現できるようにするファイル
--------------------------------------------------------------------------------
# 1. Node.js公式イメージを使用（バージョンは適宜調整）
FROM node:18

# 2. 作業ディレクトリを作成　　←以降の命令(COPY, RUNなど)はすべて/appディレクトリ内で実行される
WORKDIR /app

# 4. プロジェクトファイルをすべてコピー　ローカルのJSファイルをコンテナにコピー
COPY . .

# 6. ポートを公開　→このコンテナは3000番ポートを使いますよー
EXPOSE 3000

# 5. アプリを起動（必要に応じてCMDは調整）
CMD ["node", "server.js"]
---------------------------------------------------------------------------------

④Dockerでビルドして実行　　テザリングで実行する方がよさそう
--------------------------------------------------------------------------------
# イメージをビルド（名前は任意）
docker build -t nodejs-app .

# コンテナを起動（ポート3000をローカルに公開）
docker run -p 3000:3000 nodejs-app
--------------------------------------------------------------------------------


⑤ブラウザで動作確認
--------------------------------------------------------------------------------
http://localhost:3000
--------------------------------------------------------------------------------

！！終わるとき　別のシェル開く

# コンテナIDや名前を確認
docker ps

# コンテナを停止
docker stop <コンテナIDまたは名前>

# コンテナ削除
docker rm <コンテナIDまたは名前>

# ビルドしたイメージを削除
docker rmi <イメージ名>

(# 起動したコンテナのログ確認
docker logs <コンテナIDまたは名前>
)

次
TODO作成→必要な機能記載する
ECMAScript、DOM、ブラウザオブジェクトなど


作業ディレクトリ作成

buildするときはDockerfileがあるディレクトリ上でする
JSFW見る　足りない分は



# 1. 必要な環境
以下の環境構築が未実施の場合のみ実施します。

WSL上にlinuxを構築する。
- [Practice-WSL](https://github.com/Tech-Share-Working-Group/Practice-WSL/tree/main/01_Install_WSL)
- [Practice-Docker](https://github.com/Tech-Share-Working-Group/Practice-Docker/tree/main/01_Install_Docker)

ファイルの作成などにvimも使用します。こちらに使い方が書かれています。
- [04_Use_Vim](https://github.com/Tech-Share-Working-Group/Practice-Editor/tree/main/04_Use_Vim)

</br></br>

# 2. JavaScriptの環境構築

JavaScriptの実行環境として、Node.jsを使用します。
Node.jsは、通常ブラウザ上で動作するJavaScriptをサーバーやPC上でも動かせるようにします。

PowerShellでUbuntuを起動します。

```powershell
# windows/powershell

wsl
```

## 2-1. プロジェクトディレクトリの作成

プロジェクトファイルを格納するフォルダを作成します。

```bash
# Ubuntu/bash

mkdir workspace_nodejs && cd workspace_nodejs
```

</br>

## 2-2. Dockerfileの作成

アプリを動かすための環境(OS、言語、ライブラリ、設定など)を自動で再現できるようにするファイル
vimを使って以下のファイルを作成する

```Dockerfile
# Dockerfile

# 1. Node.js公式イメージを使用（バージョンは適宜調整）
FROM node:18

# 2. 作業ディレクトリを作成
WORKDIR /app

# 3. プロジェクトファイルをすべてコピー
COPY . .

# 4. アプリのポートを公開
EXPOSE 3000

# 5. アプリを起動（必要に応じてCMDは調整）
CMD ["node", "server.js"]
```

</br>

## 2-3. server.jsの作成

最初に実行されるJavaScriptファイルを作成する。
HTTPサーバーを立ち上げる
vimを使って以下のファイルを作成する

```server.js
# server.js

// Node.js組み込みのHTTPサーバー機能
const http = require("http");
// ファイルシステム操作用
const fs = require("fs");
// OS依存なくパスを作るために使用
const path = require("path");

// コンテナ側のポート番号を指定
const PORT = 3000;

// createServer()でリクエスト待機用サーバーを作成　引数reqはクライアントからのリクエスト情報、resはレスポンス(HTMLなど)を返すためのオブジェクト
const server = http.createServer((req, res) => {
  // index.htmlの絶対パスを作る
  const filePath = path.join(__dirname, "src", "index.html");
  
  // fs.readFile()でHTMLを読み込む
  fs.readFile(filePath, (err, data) => {
    if (err) {
      // レスポンスヘッダを設定、Content-TypeはブラウザがHTMLと認識するため
      res.writeHead(500, {"Content-Type": "text/plain; charser=utf-8"});
      res.end("500 Internal Server Error");
      return;
    }
    
    // レスポンスヘッダを設定、200は成功、Content-TypeはブラウザがHTMLと認識するため
    res.writeHead(200, {"Content-Type": "text/html; charser=utf-8"});
    // 読み込んだHTMLを送信
    res.end(data);
  });
});

// 指定したポートで待機、このプロセスが生きている限り、コンテナも止まらない
server.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}/`);
});

```

</br>

## 2-4. index.htmlの作成

ブラウザに表示するためのhtmlファイルを作成する
srcフォルダを作成して、格納します

```bash
# Ubuntu/bash

mkdir src && cd src
```

任意の文字列を表示してください
srcフォルダ直下に作成してください

```index.html
# index.html
<!DOCTYPE html>
<html lang="ja">
<head>
        <meta charser="UTF-8">
        <title>JavaScript練習</title>
</head>
<body>
        JavaScriptの練習です
</body>
</html>
```

</br>

## 2-4. コンテナをビルドして実行する

Dockerでビルドして実行します
テザリングで実行する。
Dockerfileが存在するディレクトリに移動する

```bash
# Ubuntu/bash

# イメージをビルド（名前は任意）
docker build -t nodejs-app .

# コンテナを起動（ポート3000をローカルに公開）
docker run -p 3000:3000 nodejs-app
```

## 2-4. ブラウザで動作確認

「[http://localhost:3000](http://localhost:3000)」にアクセス

</br>

## 2-4. 終了する

もう１つPowerShellを立ち上げて、Ubuntuを起動します。

```powershell
# windows/powershell

wsl
```

コンテナの停止、削除、ビルドしたイメージを削除します

```bash
# Ubuntu/bash

mkdir workspace_nodejs && cd workspace_nodejs
```

# 3. TODOリスト画面の作成

## 3-1. コンテナをビルドして実行する


CSS反映されないこと確認！！！


Ctrl+C　3回押さないと終わらない
起動時のコマンド
DockerFileの下直す

EditorとしてVSCodeをインストールする
code .でソース見れる
