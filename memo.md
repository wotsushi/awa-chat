# はじめに
mBaaS(mobile backend as a Service)の一つであるFirebaseについて紹介します。

# Firebaseとは
[Firebase](https://firebase.google.com/?hl=ja)
* backendの開発コストを激減できる
* 身内で遊ぶ程度ならば、無料でも十分に使える

# mBaaSの比較

## 機能
[あなたに最適なmBaaSはどれ？AWS Mobile HUB / Firebase / ニフクラ mobile backend 三社を比較検証！](https://press.monaca.io/atsushi/2667)
* Firebase
  * Pros: リアルタイムデータベース、ML Kitをはじめとして、機能が豊富
  * Cons: クラウドロジックはNode.jsで記述する必要がある
* AWS Mobile Hub:
  * Pros: クラウドロジックとしてAWS Lambdaを使える（クラウドロジックで使用可能なプログラミング言語が豊富）
  * Cons: 細かな設定はAWSサービス上で行う必要がある（AWS初心者には扱いが難しい）
* ニフクラ mobile backend:
  * Pros: 一つの管理画面で一元的に各機能を設定できる
  * Cons: 分析機能がない

## 料金
* [Firebase](https://firebase.google.com/pricing/?hl=ja)
* [AWS Mobile Hub](https://aws.amazon.com/jp/mobile/pricing/)
* [ニフクラ mobile backend](https://mbaas.nifcloud.com/price.htm)

無料の範囲でどこまで使えるか？

|大項目|小項目|Firebase|AWS Mobile Hub|ニフクラ mobile backend|
|--|--|--|--|--|
|データベース|リクエスト|Write: 2万件/日, Read: 5万件/日|**2億件/月**|100万件/月（他API含む）|
||容量|1GB|**25GB**|5GB（ストレージ、ホスティング含む）|
|ストレージ|リクエスト|**Up: 2万件/日, Down: 5万件/日**|100万件/月|100万件/月（他API含む）|
||容量|5GB|**10GB**|5GB（DB, ホスティング含む）
|ホスティング|リクエスト|10GB分の転送/月|**50GB分の転送/月, 200万件/月**|100万件/月|
||容量|1GB|**5GB**|5GB（DB, ストレージ含む）|
|認証||1万(?)/月|**5万人/月**|100万件/月（他API含む）|
|クラウドロジック|リクエスト|12.5万件/月|**100万件/月**|100件/月（100万ではない！）|
||処理時間|4万GB秒/月|**40万 GB 秒/月**|100秒/月|

* 補足: 処理時間の$x$ GB秒/月について
  * ロジック実行環境のメモリに依存して使用可能な時間が変化することを意味している
  * 例1: メモリが1GBならば、一ヶ月あたり $x$ 秒使える
  * 例2: メモリが2GBならば、一ヶ月あたり $x/2$ 秒使える
* 基本機能をガッツリ使うならばAWS Mobile Hubがよさそう

# 遊んでみる

## 環境情報

### OS
Macはいいぞ

```bash
$ sw_vers
ProductName:	Mac OS X
ProductVersion:	10.14
BuildVersion:	18A391
```

### Git
一応書いておく

```bash
$ git --version
git version 2.17.1 (Apple Git-112)
```

### Node.js
最新版の11.1.0ではなく、推奨版の10.13.0を使っています

```bash
$ node --version
v10.13.0
```

### yarn
yarnはじめました

```bash
$ yarn --version
1.12.3
```

### Vue CLI 3
ビルトイン機能が強い。Vue.jsで開発するならかなりオススメ。
[Vue CLI 3 Installation](https://cli.vuejs.org/guide/installation.html)

```bash
$ vue --version
3.1.3
```

## Vue CLI 3 でアプリケーションを作成する
せっかくなので、TypeScriptに挑戦してみた。

```bash
$ vue create awa-chat
Vue CLI v3.1.3
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, TS, PWA, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript for auto-detected polyfills? Yes
? Pick a linter / formatter config: TSLint
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? No
? Pick the package manager to use when installing dependencies: Yarn


Vue CLI v3.1.3
✨  Creating project in /Users/fujita/awa-chat.
🗃  Initializing git repository...
⚙  Installing CLI plugins. This might take a while...

yarn install v1.12.3
info No lockfile found.
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...

success Saved lockfile.
✨  Done in 145.28s.
🚀  Invoking generators...
📦  Installing additional dependencies...

yarn install v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...

success Saved lockfile.
✨  Done in 8.17s.
⚓  Running completion hooks...

📄  Generating README.md...

🎉  Successfully created project awa-chat.
👉  Get started with the following commands:
```

作成される `awa-chat` ディレクトリは以下

```bash
$ tree --filelimit 10
.
├── README.md
├── babel.config.js
├── node_modules [811 entries exceeds filelimit, not opening dir]
├── package.json
├── postcss.config.js
├── public
│   ├── favicon.ico
│   ├── img
│   │   └── icons [13 entries exceeds filelimit, not opening dir]
│   ├── index.html
│   ├── manifest.json
│   └── robots.txt
├── src
│   ├── App.vue
│   ├── assets
│   │   └── logo.png
│   ├── components
│   │   └── HelloWorld.vue
│   ├── main.ts
│   ├── registerServiceWorker.ts
│   ├── shims-tsx.d.ts
│   └── shims-vue.d.ts
├── tsconfig.json
├── tslint.json
└── yarn.lock

7 directories, 18 files
```

この時点でもなんか動きます。

```bash
$ yarn run serve
yarn run v1.12.3
$ vue-cli-service serve
 INFO  Starting development server...
Starting type checking and linting service...
Using 1 worker with 2048MB memory limit
 98% after emitting CopyPlugin

 DONE  Compiled successfully in 10491ms                                                                                                  22:05:26

No type errors found
No lint errors found
Version: typescript 3.1.6, tslint 5.11.0
Time: 3369ms

  App running at:
  - Local:   http://localhost:8080/
  - Network: http://192.168.100.164:8080/

  Note that the development build is not optimized.
  To create a production build, run yarn build.
```

ブラウザで http://localhost:8080/ を開くと、以下が表示される。
![init.png](/knowledge/open.file/download?fileNo=16)

これで、フロントエンドの開発準備は整いました！

## Firebaseの準備

### Firebaseプロジェクトの作成
ブラウザで https://console.firebase.google.com/ を開く。
「プロジェクトを追加」をクリックし、ええ感じにプロジェクトを作成する。

![image-20181112133925395.png](/knowledge/open.file/download?fileNo=17)

### firebase-toolsのインストール
Firebaseを操作できるCLIです。yarnでインストールできる

```bash
$ yarn global add firebase-tools
yarn global v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
info @google-cloud/functions-emulator@1.0.0-beta.5: The engine "node" is incompatible with this module. Expected version "~6". Got "10.13.0"
info "@google-cloud/functions-emulator@1.0.0-beta.5" is an optional dependency and failed compatibility check. Excluding it from installation.
[3/4] 🔗  Linking dependencies...
warning "@vue/cli > @vue/cli-ui > graphql-tag@2.10.0" has unmet peer dependency "graphql@^0.9.0 || ^0.10.0 || ^0.11.0 || ^0.12.0 || ^0.13.0 || ^14.0.0".
warning "@vue/cli > @vue/cli-ui > graphql-type-json@0.2.1" has unmet peer dependency "graphql@>=0.8.0".
warning "@vue/cli > @vue/cli-ui > vue-cli-plugin-apollo > apollo-link-persisted-queries@0.2.1" has incorrect peer dependency "graphql@^0.11.0 || ^0.12.0 || ^0.13.0".
warning "@vue/cli > @vue/cli-ui > vue-cli-plugin-apollo > apollo-server-express > @apollographql/apollo-upload-server@5.0.3" has incorrect peer dependency "graphql@^0.13.1".
[4/4] 📃  Building fresh packages...
success Installed "firebase-tools@6.0.1" with binaries:
      - firebase
✨  Done in 41.46s.
```

なんかうるさいけど、一旦スルー

### ログイン
ログインすると、自分のプロジェクトに対していろいろ操作できる

```bash
$ firebase login
```

実行後、ブラウザがGoogleアカウントの認証画面を開く。
Googleアカウント認証し、Firebaseを信頼する。

無事にログインできたことを確認するために、プロジェクトの一覧を取得してみる。

```bash
$ firebase list
┌───────────────────┬───────────────────────┬─────────────┐
│ Name              │ Project ID / Instance │ Permissions │
├───────────────────┼───────────────────────┼─────────────┤
│ awa-chat          │ awa-chat              │ Owner       │
├───────────────────┼───────────────────────┼─────────────┤
│ dominion-familiar │ dominion-familiar     │ Owner       │
└───────────────────┴───────────────────────┴─────────────┘
```

### プロジェクトディレクトリ
作成したFirebaseのプロジェクトと開発したアプリケーションのディレクトリを紐付ける必要がある。
Firebaseのプロジェクトに紐付けられたアプリケーションのディレクトリを **プロジェクトディレクトリ** と呼ぶ。
アプリケーションのディレクトリ（今回だと `awa-chat`）をFirebaseのプロジェクトディレクトリにするには、カレントディレクトリをアプリケーションのディレクトリとして、 `firebase init` コマンドを実行すればよい。
* feature: 今回はホスティングとデータベースを使うので、FirestoreとHostingを選択した
  * 同じデータベースでも Realtime DatabaseよりはFirestoreを選んだほうがいいらしい
* Hosing Setup: public directoryはデフォルト値の `public` ではなく `dist` を指定した。ビルド先がdistなので。

```bash
$ firebase init

     ######## #### ########  ######## ########     ###     ######  ########
     ##        ##  ##     ## ##       ##     ##  ##   ##  ##       ##
     ######    ##  ########  ######   ########  #########  ######  ######
     ##        ##  ##    ##  ##       ##     ## ##     ##       ## ##
     ##       #### ##     ## ######## ########  ##     ##  ######  ########

You're about to initialize a Firebase project in this directory:

  /Users/fujita/awa-chat

Before we get started, keep in mind:

  * You are initializing in an existing Firebase project directory

? Which Firebase CLI features do you want to setup for this folder? Press Space to select features, then E
nter to confirm your choices. Firestore: Deploy rules and create indexes for Firestore, Hosting: Configure
 and deploy Firebase Hosting sites

=== Project Setup

First, let's associate this project directory with a Firebase project.
You can create multiple project aliases by running firebase use --add,
but for now we'll just set up a default project.

i  .firebaserc already has a default project, skipping

=== Firestore Setup

Firestore Security Rules allow you to define how and when to allow
requests. You can keep these rules in your project directory
and publish them with firebase deploy.

? What file should be used for Firestore Rules? firestore.rules

Firestore indexes allow you to perform complex queries while
maintaining performance that scales with the size of the result
set. You can keep index definitions in your project directory
and publish them with firebase deploy.

? What file should be used for Firestore indexes? firestore.indexes.json

=== Hosting Setup

Your public directory is the folder (relative to your project directory) that
will contain Hosting assets to be uploaded with firebase deploy. If you
have a build process for your assets, use your build's output directory.

? What do you want to use as your public directory? dist
? Configure as a single-page app (rewrite all urls to /index.html)? No
✔  Wrote dist/404.html
✔  Wrote dist/index.html

i  Writing configuration info to firebase.json...
i  Writing project information to .firebaserc...

✔  Firebase initialization complete!
```

Firestoreはまだ有効化されていませんが、Firebaseを使う準備が整いました。

## 初めてのデプロイ

### アプリケーションのビルド
まずは、アプリケーションをビルドし、デプロイに必要ファイルを生成する。

```bash
$ yarn run build
yarn run v1.12.3
$ vue-cli-service build

⠙  Building for production...Starting type checking and linting service...
Using 1 worker with 2048MB memory limit
⠼  Building for production...

 DONE  Compiled successfully in 8730ms                                                            23:08:14

  File                                      Size             Gzipped

  dist/js/chunk-vendors.35e69482.js         84.88 kb         30.27 kb
  dist/js/app.d667b00e.js                   5.79 kb          2.05 kb
  dist/service-worker.js                    0.94 kb          0.53 kb
  dist/precache-manifest.9a3ccd5901965c1    0.54 kb          0.27 kb
  b0d59bbc8fb4d2fd5.js
  dist/css/app.e62e89db.css                 0.33 kb          0.23 kb

  Images and other types of assets omitted.

 DONE  Build complete. The dist directory is ready to be deployed.
 INFO  Check out deployment instructions at https://cli.vuejs.org/guide/deployment.html

✨  Done in 18.34s.
```

実行後、`dist` にいろいろ作成されていることが確認できる。

```bash
$ tree dist
dist
├── css
│   └── app.e62e89db.css
├── favicon.ico
├── img
│   ├── icons
│   │   ├── android-chrome-192x192.png
│   │   ├── android-chrome-512x512.png
│   │   ├── apple-touch-icon-120x120.png
│   │   ├── apple-touch-icon-152x152.png
│   │   ├── apple-touch-icon-180x180.png
│   │   ├── apple-touch-icon-60x60.png
│   │   ├── apple-touch-icon-76x76.png
│   │   ├── apple-touch-icon.png
│   │   ├── favicon-16x16.png
│   │   ├── favicon-32x32.png
│   │   ├── msapplication-icon-144x144.png
│   │   ├── mstile-150x150.png
│   │   └── safari-pinned-tab.svg
│   └── logo.82b9c7a5.png
├── index.html
├── js
│   ├── app.d667b00e.js
│   ├── app.d667b00e.js.map
│   ├── chunk-vendors.35e69482.js
│   └── chunk-vendors.35e69482.js.map
├── manifest.json
├── precache-manifest.9a3ccd5901965c1b0d59bbc8fb4d2fd5.js
├── robots.txt
└── service-worker.js

4 directories, 25 files
```

### Firebaseへデプロイ

さあ、いよいよFirebaseへのデプロイ。
Firestoreは有効化していないので、ホスティングだけデプロイする。

```bash
$ firebase deploy --only hosting

=== Deploying to 'awa-chat'...

i  deploying hosting
i  hosting[awa-chat]: beginning deploy...
i  hosting[awa-chat]: found 25 files in dist
✔  hosting[awa-chat]: file upload complete
i  hosting[awa-chat]: finalizing version...
✔  hosting[awa-chat]: version finalized
i  hosting[awa-chat]: releasing new version...
✔  hosting[awa-chat]: release complete

✔  Deploy complete!

Project Console: https://console.firebase.google.com/project/awa-chat/overview
Hosting URL: https://awa-chat.firebaseapp.com
```

早速、ブラウザでデプロイされたページを開いてみる。

![image-20181112141551582.png](/knowledge/open.file/download?fileNo=18)

無事にデプロイできていますね。

## 認証機能を追加する

### Firebaseの設定
まず、Firebaseのコンソールを開き、Authの設定ページを開く。

![image-20181113112507505.png](/knowledge/open.file/download?fileNo=19)

ログイン方法を選んでね！　って言われるのでお好みのログイン方法を選ぶ（複数可）。

![image-20181113112849573.png](/knowledge/open.file/download?fileNo=20)

今回は、Googleだけ。
これで、Firebaseの認証に関する設定はおしまい。

### Firebaseパッケージの入手
フロントエンドでFirebaseにアクセスできるようにするため、Firebaseのパッケージを入手する。

```bash
$ yarn add @types/firebase
yarn add v1.12.3
[1/4] 🔍  Resolving packages...
warning @types/firebase@3.2.1: This is a stub types definition for Firebase API (https://www.firebase.com/docs/javascript/firebase). Firebase API provides its own type definitions, so you don't need @types/firebase installed!
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning "@vue/cli-plugin-babel > babel-loader@8.0.4" has unmet peer dependency "webpack@>=2".
warning "@vue/cli-plugin-pwa > workbox-webpack-plugin@3.6.3" has unmet peer dependency "webpack@^2.0.0 || ^3.0.0 || ^4.0.0".
warning "@vue/cli-plugin-typescript > fork-ts-checker-webpack-plugin@0.4.15" has unmet peer dependency "webpack@^2.3.0 || ^3.0.0 || ^4.0.0".
warning "@types/firebase > firebase > @firebase/database@0.3.6" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/functions@0.3.2" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/messaging@0.3.6" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/storage@0.2.4" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/firestore@0.8.7" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/database > @firebase/database-types@0.3.2" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/firestore > @firebase/firestore-types@0.7.0" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/functions > @firebase/messaging-types@0.2.3" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/storage > @firebase/storage-types@0.2.3" has unmet peer dependency "@firebase/app-types@0.x".
warning "@types/firebase > firebase > @firebase/auth > @firebase/auth-types@0.3.4" has unmet peer dependency "@firebase/app-types@0.x".
[4/4] 📃  Building fresh packages...
success Saved lockfile.
success Saved 32 new dependencies.
info Direct dependencies
└─ @types/firebase@3.2.1
info All dependencies
├─ @firebase/app-types@0.3.2
├─ @firebase/app@0.3.4
├─ @firebase/auth-types@0.3.4
├─ @firebase/auth@0.7.9
├─ @firebase/database-types@0.3.2
├─ @firebase/database@0.3.6
├─ @firebase/firestore-types@0.7.0
├─ @firebase/firestore@0.8.7
├─ @firebase/functions-types@0.2.1
├─ @firebase/functions@0.3.2
├─ @firebase/messaging@0.3.6
├─ @firebase/polyfill@0.3.3
├─ @firebase/storage-types@0.2.3
├─ @firebase/storage@0.2.4
├─ @firebase/webchannel-wrapper@0.2.11
├─ @types/firebase@3.2.1
├─ ascli@1.0.1
├─ bytebuffer@5.0.1
├─ colour@0.7.1
├─ dom-storage@2.1.0
├─ encoding@0.1.12
├─ firebase@5.5.8
├─ grpc@1.16.0
├─ isomorphic-fetch@2.2.1
├─ long@3.2.0
├─ node-fetch@1.7.3
├─ optjs@3.2.2
├─ promise-polyfill@7.1.2
├─ protobufjs@5.0.3
├─ whatwg-fetch@2.0.4
├─ window-size@0.1.4
└─ xmlhttprequest@1.8.0
✨  Done in 67.97s.
```

次に、FirebaseのコンソールでAPIキーを確認する。

![image-20181113120947060.png](/knowledge/open.file/download?fileNo=21)

確認したAPIキーを用いることで、JavasScriptでFirebaseのAPIを叩くことができる。
ちなみに、この情報は第三者にバレる前提の情報なので、JavaScript/TypeScriptのスクリプトファイルにコピペして問題なし（小細工は不要）。

### フロントエンドの開発
勝手に生成されたHello Worldアプリにログイン機能を追加してみよう。

HTML

```html
<div class="hello">
  <h1>{{ msg }}</h1>
  <div v-if="user"> {{ user.displayName }} </div>
  <button @click="signin">Sign In</button>
  ...
</div>
```

`user` はログインしているユーザ情報である。ただし、ログインしていない場合は `null` である。
つまり、ログインしている場合、そのユーザ名を表示する。
また、Sign Inボタンも作成した。このボタンをクリックすると次に述べるTypeScriptのメソッドをコールする。
ギミックは以上。
いろいろ手抜きだが気にしないことにする。

TypeScript

```typescript
import { Component, Prop, Vue } from 'vue-property-decorator';
import firebase from 'firebase/app';
import 'firebase/auth';

firebase.initializeApp({
  apiKey: 'AIzaSyCnowN98wh2QAAiJMeeUHJHAE3JPXGSuNY',
  authDomain: 'awa-chat.firebaseapp.com',
  databaseURL: 'https://awa-chat.firebaseio.com',
  projectId: 'awa-chat',
  storageBucket: 'awa-chat.appspot.com',
  messagingSenderId: '426819575153',
});

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  private user: firebase.User = null!;

  private async signin() {
    const result = await firebase.auth().signInWithPopup(
      new firebase.auth.GoogleAuthProvider(),
    );
    if (result.credential) {
        this.user = result.user!;
    }
  }
}
```

`import firebase from 'firebase/app';` でFirebaseのAPIに関するパッケージをインポートする。
`import 'firebase/auth';` でFirebaseの認証機能に関するパッケージをインポートする。
`firebase.initializeApp` でFirebaseにアクセスする準備が整う。
準備はここまで。

メインディッシュは `Sign In` ボタンをクリックするとコールされる `signin` メソッドである。
`signInWithPopup` の引数に認証方式を指定し、認証を実施する。
ちなみにリダイレクトさせて認証する方式もある。
認証に成功すると、認証したユーザの情報にアクセスできる。
[firebase.User](https://firebase.google.com/docs/reference/js/firebase.User?hl=ja)

### 認証を試してみる
Sign In ボタンができているので、それをクリックする。
Googleアカウントの認証画面が出てくる。
![image-20181113134632032.png](/knowledge/open.file/download?fileNo=22)
アカウント情報を入力し認証する。
![image-20181113134826270.png](/knowledge/open.file/download?fileNo=23)
認証できた！

##データベースと連携する

正式版のRealtime Databaseというオプションもありますが、今回はベータ版のFirestoreを使います。
ベータ版ですが、宗教上の理由がなければFirestore一択らしいです。

### Firebaseの設定

まず、Firebaseのコンソールを開き、Databaseの設定ページを開く。

![image-20181118102640525.png](/knowledge/open.file/download?fileNo=24)

「データベースの作成」をクリックすると、セキュリティルールをどうするか聞かれる。

![image-20181118102907113.png](/knowledge/open.file/download?fileNo=25)

とりあえず、遊びなのでテストモードで開始する。
真面目にやるなら、きちんとセキュリティルールを設計する必要あります。

![image-20181118103809336.png](/knowledge/open.file/download?fileNo=26)

データベースができたぞ！（空だけど）

ところで、
* Firestoreはコレクションの集まりである
* コレクションはドキュメントの集まりである
* ドキュメントはデータの集まりである

ここで、データベースを巨大なJSONデータとみなすと、
* データはプリミティブなデータ
* ドキュメントは単一のJSONオブジェクト
* コレクションはJSONオブジェクトの配列
に対応する。

https://firebase.google.com/docs/firestore/data-model?hl=ja

という訳で、最初にコレクションを作成してみる。これは、メッセージの集合に対応することになる。

![image-20181119130229268.png](/knowledge/open.file/download?fileNo=32)

次に、コレクションに入れるドキュメントを作成する。これは、一つのメッセージに対応する。つまり、記念すべき最初のメッセージとなる。

![image-20181119133939451.png](/knowledge/open.file/download?fileNo=35)

それっぽいのができた！

![image-20181119134009588.png](/knowledge/open.file/download?fileNo=36)

### Firebaseパッケージの入手

認証機能の追加でやったので、何もやらなくてよい。
認証機能を入れないけど、データベース使うならこのタイミングでパッケージを入手してください。

### フロントエンドの開発

先程の改造したHello Worldアプリをさらに改造してチャット機能を追加しよう。

HTML

```html
<div class="hello">
  <h1>{{ msg }}</h1>
  <div v-if="user"> {{ user.displayName }} </div>
  <button @click="signin">Sign In</button>
  <div id="input">
    <input v-model="inputMessage" @keyup.enter="send">
  </div>
  <div id="outputs">
      <div v-for="message in messages" :key="message.name">
        {{ message.username }}: {{message.content}}
      </div>
  </div>
</div>
```

`v-model="inputMessage"` の入力フォームは発言したいメッセージを入力する欄である。
Enterキーを押すと、後述の `send` メソッドがコールされて、データベースにメッセージが送信される。
`id="outputs" のdiv要素は発言されたメッセージの一覧である。

TypeScript

```typescript
import { Component, Prop, Vue } from 'vue-property-decorator';
import firebase from 'firebase/app';
import 'firebase/auth';
import 'firebase/firestore';

firebase.initializeApp({
  apiKey: 'AIzaSyCnowN98wh2QAAiJMeeUHJHAE3JPXGSuNY',
  authDomain: 'awa-chat.firebaseapp.com',
  databaseURL: 'https://awa-chat.firebaseio.com',
  projectId: 'awa-chat',
  storageBucket: 'awa-chat.appspot.com',
  messagingSenderId: '426819575153',
});

const firestore = firebase.firestore();
firestore.settings({ timestampsInSnapshots: true });

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  private user: firebase.User = null!;
  private messages: object[] = [];
  private inputMessage: string = '';

  private created() {
    firestore.collection('messages').orderBy('timestamp', 'desc').onSnapshot((querySnapShot) => {
      this.messages = [];
      querySnapShot.forEach((message) => {
        this.messages.push(message.data());
      });
    });
  }

  private async signin() {
    const result = await firebase.auth().signInWithPopup(
      new firebase.auth.GoogleAuthProvider(),
    );
    if (result.credential) {
        this.user = result.user!;
    }
  }

  private send() {
    if (this.inputMessage) {
      firestore.collection('messages').add({
        username: this.user.displayName,
        content: this.inputMessage,
        timestamp: firebase.firestore.FieldValue.serverTimestamp(),
      });
      this.inputMessage = '';
    }
  }
}
```

`import 'firebase/firestore';` でFirestoreに関するパッケージをインポートする。
`firestore.settings({ timestampsInSnapshots: true });` は `Date` の代わりに `Timestamp` 型を使うためのおまじない。ドキュメントが時刻データを持つならとりあえずやっとけー的な（やらないとWarningが出ます）。
詳しくは[これ](https://firebase.google.com/docs/reference/js/firebase.firestore.Settings?hl=ja)みて。
準備は以上。

メインのコードは二つある。
一つは、リアルタイムにチャットのメッセージを取得するようにするためのコードである。
もう一つは、 `inputMessage` に入力したメッセージをデータベースに送信するためのコードである。

前者について。コードは以下である。

```typescript
  private created() {
    firestore.collection('messages').orderBy('timestamp', 'desc').onSnapshot((querySnapShot) => {
      this.messages = [];
      querySnapShot.forEach((message) => {
        this.messages.push(message.data());
      });
    });
  }
```

`created` はVueインスタンスが生成されたあとに実行されるコード。Vueではサーバからデータ取得する処理は `created` に書いたほうがいいかもって誰かが言ってた。
`firestore.collection('messages')` はコレクション`messages` に対する参照である。これは[CollectionReference](https://firebase.google.com/docs/reference/js/firebase.firestore.CollectionReference?hl=ja) 型の値である。
この式だけ評価しても、データ取得のリクエストは送信されない。
`orderBy` メソッドはデータ取得のリクエストに対して、ソートのオプションを付与するイメージ。
`onSnapshot` メソッドはサーバの `messages` ドキュメントが更新されるたびに、引数のリスナをコールさせるようにする（正確には `messages` コレクションを `timestamp` で降順にソートした結果が変化するたびに、引数のリスナがコールされる）。
リスナは `querySnapShot` という引数をとる。これは、クエリの結果を表すオブジェクトと思えばよい。
リスナの処理では、`querySnapShot` に入っているすべてのメッセージをメッセージのリストを表すインスタンス変数 `messages` にセットしている
（本当は `forEach` ではなく追加されたデータのみ得る `docChanges` を使うべきだと思う）。

後者について。コードは以下である。

```
private send() {
    if (this.inputMessage) {
      firestore.collection('messages').add({
        username: this.user.displayName,
        content: this.inputMessage,
        timestamp: firebase.firestore.FieldValue.serverTimestamp(),
      });
      this.inputMessage = '';
    }
  }
```

見ての通り、直観的なコードである。
ただし、 `add` メソッドは非同期な処理であることに注意（今回はaddした結果に興味を持っていないので気にしなくてよい）。

### チャットしてみる
ヌルヌル動いて心地よい。

![image-20181120130458032.png](/knowledge/open.file/download?fileNo=37)

管理コンソールみると、データベースが更新されていることがわかる。

![image-20181120130623134.png](/knowledge/open.file/download?fileNo=38)

という訳でチャットアプリが完成しました！

## 最後のデプロイ
まずはビルドしましょう。ビルドしないと `dist` は更新されないからね。

```bash
$ yarn run build
yarn run v1.12.3
$ vue-cli-service build

⠙  Building for production...Starting type checking and linting service...
Using 1 worker with 2048MB memory limit
⠹  Building for production...

 WARNING  Compiled with 3 warnings                                                                                                       22:13:51

 warning

asset size limit: The following asset(s) exceed the recommended size limit (244 KiB).
This can impact web performance.
Assets:
  js/chunk-vendors.4eecd631.js (647 KiB)

 warning

entrypoint size limit: The following entrypoint(s) combined asset size exceeds the recommended limit (244 KiB). This can impact web performance.
Entrypoints:
  app (652 KiB)
      js/chunk-vendors.4eecd631.js
      css/app.add3802a.css
      js/app.517c1673.js


 warning

webpack performance recommendations:
You can limit the size of your bundles by using import() or require.ensure to lazy load some parts of your application.
For more info visit https://webpack.js.org/guides/code-splitting/

  File                                      Size             Gzipped

  dist/js/chunk-vendors.4eecd631.js         646.60 kb        184.43 kb
  dist/js/app.517c1673.js                   5.44 kb          2.47 kb
  dist/service-worker.js                    0.94 kb          0.53 kb
  dist/precache-manifest.be291ea676f7e82    0.54 kb          0.28 kb
  61add7503de434e3d.js
  dist/css/app.add3802a.css                 0.33 kb          0.23 kb

  Images and other types of assets omitted.

 DONE  Build complete. The dist directory is ready to be deployed.
 INFO  Check out deployment instructions at https://cli.vuejs.org/guide/deployment.html

✨  Done in 45.02s.
```

ちょいちょいwarningsがうるさいが、勘弁。
そして、デプロイ！

```bash
$ firebase deploy

=== Deploying to 'awa-chat'...

i  deploying firestore, hosting
i  firestore: checking firestore.rules for compilation errors...
✔  firestore: rules file firestore.rules compiled successfully
i  firestore: uploading rules firestore.rules...
i  hosting[awa-chat]: beginning deploy...
i  hosting[awa-chat]: found 25 files in dist
✔  hosting[awa-chat]: file upload complete
✔  firestore: released rules firestore.rules to cloud.firestore
i  hosting[awa-chat]: finalizing version...
✔  hosting[awa-chat]: version finalized
i  hosting[awa-chat]: releasing new version...
✔  hosting[awa-chat]: release complete

✔  Deploy complete!

Project Console: https://console.firebase.google.com/project/awa-chat/overview
Hosting URL: https://awa-chat.firebaseapp.com


   ╭───────────────────────────────────────────╮
   │                                           │
   │      Update available 6.0.1 → 6.1.0       │
   │   Run npm i -g firebase-tools to update   │
   │                                           │
   ╰───────────────────────────────────────────╯
```

無事デプロイも完了しました（この後 `firebase-tools` をアップデートした）。
よかったら遊んでみてくれい。
https://awa-chat.firebaseapp.com

# まとめ
* ちょっとしたアプリのバックエンドの管理が面倒な貴方にFirebaseはオススメ
* AWS Mobile Hubも選択肢としてアリかも
* チャットくらいなら楽勝で作成できる
  * 100行に満たない程度の規模
