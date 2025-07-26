---
Title: "GitHub Actions入門"
Source: "https://zenn.dev/praha/articles/9e561bdaac1d23"
ClippedDate: 2025-07-25
Tags:
  - "clippings"
---
[PrAha](https://zenn.dev/p/praha) [Publicationへの投稿](https://zenn.dev/faq#what-is-publication)

258

15[tech](https://zenn.dev/tech-or-idea)

## GitHub Actionsとは？

GitHub Actionsは、GitHubが提供する「スクリプトを動かしてあげるよ～」なサービスです。

具体的に言うと、リポジトリに以下のようなディレクトリを作って、その中に「こういうときにこれを実行してね」なYAMLファイルを設置しておくだけで、GitHubが「お！これを実行したらええんやな！」と認識してくれます。

```
📁.github
　└📁workflows
   　└📄hoge.yml　👈これの中身が実行される
```

### 料金（2024年05月現在）

publicリポジトリだと無料で使い放題です。すごい。

privateリポジトリでも無料枠が2,000分/月もあります。

## その前に：YAMLの書き方を予習する

先にYAMLの予習をしておくのがオススメです。

というのも、先にYAMLの書き方が分かっていないと「これはGitHub Actionsの機能なの？YAMLの機能なの？」と迷うからです。

学習としてオススメなのが、JSONでいろいろなデータを表現してみたあとに「これをYAMLで表現するならどう書けばいいか」をやってみることです👇

![YAMLとJSONの相互変換による練習](https://storage.googleapis.com/zenn-user-upload/cf96c8cdd52f-20240512.gif) *▲こんな感じで「このYAMLをJSONにするとどうなるか？」を想像する→JSONにしてみるを繰り返すだけでいい練習になります*

## 基本的な書き方

まず以下のようなディレクトリと `hoge.yml` というファイルを作ってください。

```
📁.github
　└📁workflows
   　└📄hoge.yml
```

ちなみにファイル名は何でも大丈夫ですが、`.github` や `workflows` といったディレクトリ名は1文字でも違っていると動作しません！お気をつけください！

`hoge.yml` には、以下のように書きます。

```yml
name: GitHub Actions Demo

on: [push]

jobs:
  echo_hello_world:
    runs-on: ubuntu-latest
    steps:
      - run: echo "hello world!!"
```

内容は「pushされたらechoしてね」になっています。  
（詳しい内容は後から説明します）

実際にリポジトリを作ってmainブランチにpushすると、GitHubが「これを実行したらいいんだな」と解釈してくれて、勝手に実行してくれます👇

![GitHub Actionsの実行ログ](https://storage.googleapis.com/zenn-user-upload/13364851b1d5-20240511.png) *▲GitHubが実行してくれるGitHub Actionsのログは、リポジトリの `Actions` というタブから確認できます。*

## 何を実行するか

`echo "hello world!!"` の部分は、コマンドなら基本何でも書けます。

ただし `run-on` の部分で指定したOSで使えるコマンド限定です。この場合、Ubuntuを指定しているので、UNIX系のコマンドなら基本使えます。

たとえば👇のように `ls` コマンドを実行したりもできますし

```yaml
jobs:
  echo_hello_world:
    runs-on: ubuntu-latest
    steps:
      - run: ls
```

👇のように、「特定のURLにリクエストを送る」とかもできます。

```yaml
jobs:
  echo_hello_world:
    runs-on: ubuntu-latest
    steps:
      - run: curl http://exmaple.com
```

## 複数コマンドを実行する

stepsの中にrunを増やしていくと、複数のコマンドを実行できます👇

```yml
jobs:
  echo_hello:
    runs-on: ubuntu-latest
    steps: 
      - run: echo "Nice to meet you!!"
      - run: echo "My Name is Taro!!"
      - run: echo "I love programming!!"
```

また、それぞれに `name:`をつけることもできます👇

```yml
jobs:
  echo_hello:
    runs-on: ubuntu-latest
    steps: 
      - name: "挨拶"
        run: echo "Nice to meet you!!"

      - name: "名前を言う"
        run: echo "My Name is Taro!!"

      - name: "趣味を話す"
        run: echo "I love programming!!"
```

stepsの中に書く1つ1つの要素のことを「ステップ」と呼びます👇  
![GitHub Actionsのステップ](https://storage.googleapis.com/zenn-user-upload/024c7f58f411-20240511.png)

基本的には👆のように「1つのステップ＝1つのコマンドと名前」にすると分かりやすいです。

ちなみに、最初のコードは👇のようにも書けますが（これはYAMLの文法です）

```yml
jobs:
  echo_hello_world:
    runs-on: ubuntu-latest
    steps: 
      - name: "自己紹介"
        run: |
          echo "Nice to meet you!!"
          echo "My Name is Taro!!"
          echo "I love programming!!"
```

このように👆1つのステップで複数のコマンドを実行してしまうと、失敗したときに原因を特定するのが面倒になるので、基本的に「1ステップ＝1コマンド」にするのが良いかもです。

## 用語

ここからは用語ベースで書きます。

![ジョブ、ワークフロー、トリガーの違い](https://storage.googleapis.com/zenn-user-upload/6a7b61dcd747-20240512.png) *▲こういうイメージ*

### ワークフロー🟩

「こういうときにこれを実行してね」なYAMLファイルに書かれた処理を **ワークフロー** と呼びます。

「YAMLファイル＝1つのワークフロー」という理解で良いと思います。

### ジョブ🟦

複数のコマンドをまとめた単位のことを **ジョブ** といいます。

ジョブは複数定義することができます。  
たとえば、👇のようにジョブを2つ書くこともできます。

```yaml
jobs:
  echo_hello:
    runs-on: ubuntu-latest
    steps: 
      - name: "挨拶"
        run: echo "Nice to meet you!!"

      - name: "名前を言う"
        run: echo "My Name is Taro!!"

      - name: "趣味を話す"
        run: echo "I love programming!!"

  echo_goodbye:
    runs-on: ubuntu-latest
    steps: 
      - name: "別れの挨拶をする"
        run: echo "Good Bye!!"
```

ここで重要なのが、 **ジョブの中のステップは直列で実行されるが、それぞれのジョブは並列で実行されるという点です。**

この場合、 `echo_hello` と `echo_goodbye` はヨーインドン！で同時に並列で実行されます。

![ジョブを増やすとGitHub Actionsの画面でも増える](https://storage.googleapis.com/zenn-user-upload/7dec68e1f2f3-20240511.png) *▲Actionsタブで見ても、ジョブが2つになっている*

なので、たとえば「別れの挨拶をする」というステップは「趣味を話す」のステップの後に実行してほしいんだよな～という場合は、👇のように1つのジョブの中に書く必要があります。

```yaml
jobs:
  echo_hello:
    runs-on: ubuntu-latest
    steps: 
      - name: "挨拶"
        run: echo "Nice to meet you!!"

      - name: "名前を言う"
        run: echo "My Name is Taro!!"

      - name: "趣味を話す"
        run: echo "I love programming!!"

      - name: "別れの挨拶をする"
        run: echo "Good Bye!!"
```

逆に、ステップAとステップBに依存関係がない限り、ステップAとステップBは別々のジョブとして定義したほうが良いです。その分だけ早く終わるからです。

また、ワークフローもジョブと同じでヨーインドン！で同時に並列に実行されます。

たとえば、👇のように複数のワークフローを作った場合、条件に合致するすべてのワークフローがヨーインドン！で同時に処理がスタートします。

```
📁.github
　└📁workflows
 　　├🗒1.yml
 　　├🗒2.yml
   　└🗒3.yml
```

### トリガー🟪

「どういうタイミングで実行するか」のことを **トリガー** と呼びます。

ymlファイルの `on` の部分でトリガーを指定できます。

たとえば👇のように書くと「15分ごとに実行してね」になります。

```yml
on:
  schedule:
    - cron: '*/15 * * * *'
```

👇のように書くと「プルリクエストが作られたら実行してね」になります。

```yml
on:
  pull_request:
```

👇のように書くと「手動で実行できるようにしてね」になります。

```yml
on:
  workflow_dispatch:
```

![workflow_dispatchを指定すると手動で設定できるところを説明している画像](https://storage.googleapis.com/zenn-user-upload/0a37ae57a298-20240512.png)  
*▲workflow\_dispatchを指定すると、Actionsタブから手動で実行できるようになる*

👇のように書くと「developブランチにpushされたら実行してね」になります。

```yml
on:
  push:
    branches:
      - develop
```

👇のように書くと「appsディレクトリ内のファイルに変更があって、かつ、developブランチにpushされたら実行してね」になります。

```yml
on:
  push:
    branches:
      - develop
    paths:
      - 'apps/**'
```

ほかにも色々なタイミングを指定できます。  
公式ページにどういうタイミングを設定できるのか全部書いています👇

[Workflow syntax for GitHub Actions - GitHub Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

### アクション

「誰かか作った処理をそのままワークフローの一部として使っちゃおう」ができる機能を **アクション** と呼びます。

話は変わりますが、GitHub Actions上では、リポジトリのソースはデフォルトでは含まれていません。  
たとえばAというリポジトリで、GitHub Actionsを動かしたとしても、その実行環境の中ではAというリポジトリのソースは存在しません。自分で用意する必要があります。

え？じゃあ毎回 `git clone` する必要があるの？面倒くさ～！と思うかもですが、そういう面倒なことをしなくても良いように、GitHub公式が「これを使えば特定のリポジトリのソースを持ってこれるよ～」なアクションを用意してくれています👇

[actions/checkout](https://github.com/actions/checkout)

さらに、GitHub Actionsでは、デフォルトでNode.jsが使えないので、Node.jsをインストールするためのアクションも用意されています👇

[actions/setup-node](https://github.com/actions/setup-node)

これらのアクションは、 `uses: アクション名` という形で使用できます。

実際に、この2つを使って「GitHub Actionsで自作のindex.jsというスクリプトを動かしたい！」という場合は、👇のような感じで書けます。

```yaml
name: Run JavaScript in GitHub Actions

on:
  push:
    branches:
      - main

jobs:
  setup_and_run:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4  #👈リポジトリのソースを持ってくる

      - name: Set up Node.js
        uses: actions/setup-node@v3  #👈Node.jsを使えるようにする
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm install

      - name: Run script
        run: node index.js
```

### CI/CD

GitHub Actionsのようなサービスで、「このテストを実行してね」な処理を書くことを **CI** と呼びます。

「ここにデプロイしてね」な処理を書くことを **CD** を呼びます。  
両方書くことを **CI/CD** と呼びます。

用語なんてどうでもいいのですが、開発の現場では当たり前のように「CIが～」「CI/CDが～」みたいに使われるので、覚えておいたほうが良いです。

ちなみに、日本語にすると👇のような意味になるらしいです。

- CI：Continuous Integration（継続的インテグレーション）
- CD：Continuous Delivery & Deployment（継続的デリバリー&デプロイ）

## 実務でどうやって使うか？

ここまでは簡単な例だったので、実際に実務でどうやって使えばいいのか？について書きます。

弊社でOSSとして公開している「 [LooksToMe](https://looks-to.me/) 」というアプリがあるのですが、こちらのアプリを例に紹介します！

![LooksToMeというアプリで作成できる画像の例](https://storage.googleapis.com/zenn-user-upload/1d4363ce6c1b-20240329.webp) *▲画像をアップして、こういう文字を合成できるアプリです*

たとえば、CIは👇のコードで実現しています。

具体的な内容を説明すると、👇の4つのジョブを含むワークフローが

- Build（Next.jsなどのアプリのビルドが成功するか？）
- Test（書いたテストが全部通るか？）
- Lint（ESLintのチェックが全部通るか？）
- Type（tsxの型チェックが全部通るか）

👇のときにトリガーされます。

- プルリクがオープンしたとき
- プルリクが再オープンしたとき
- プルリクにpushされたとき

そして「これらのすべてのジョブが成功✅で終わらなかった場合、マージさせないぞ」という設定にしています👇  
![リポジトリのSettings→Branchesからマージさせない設定ができる](https://storage.googleapis.com/zenn-user-upload/cf93fe2691ce-20240512.png) *▲リポジトリのSettings→Branchesから設定できる*

ちなみにOSSとして公開しているので、だれでもGitHub Actionsの実行ログを確認できます！👇  
![GitHub Actionsのログ。誰でも見れる。](https://storage.googleapis.com/zenn-user-upload/d538b09a50ac-20240511.png)

弊社では、全プロダクトでこのようなCI/CDを構築しています。

また、👇のような便利なOSSを公開していたりもします。  
[ブランチ名をもとにPRに自動でラベルを貼るGitHub Actions](https://zenn.dev/praha/articles/11acc8f530078a)

良さげな会社だな～と思った方は、ぜひカジュアル面談にご応募ください～！🙏

## ＼ PR ／

弊社では「中級エンジニアになろう！」なブートキャンプのメンバーを募集しています！

「3人1組でたくさんの課題に挑戦しよう！」なやつで、名前を「プラハチャレンジ」といいます。

期間限定募集です！

- 応募締め切り： **5月17日(金)の18時まで**
- 費用：9,800円/月（税抜き）

データベース設計やドメイン駆動設計と言った課題など色々あります。  
GitHub Actionsの課題もあります！

興味のある方はぜひ～！！👇

🔹 [PrAha Challenge | 中級エンジニアを育てるブートキャンプ](https://praha-challenge.com/)

258

15