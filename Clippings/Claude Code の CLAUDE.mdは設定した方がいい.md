---
Title: "Claude Code の CLAUDE.mdは設定した方がいい"
Source: "https://syu-m-5151.hatenablog.com/entry/2025/06/06/190847"
ClippedDate: 2025-06-13
Tags:
  - "clippings"
  - "ClaudeCode"
---
**\[社内共有版「 [Claude Code、どこまでも](https://kddi-agile.connpass.com/event/357862/) 」\]**

---

## はじめに

**Claude Codeを使い始めて1週間。私の開発スタイルは完全に変わった。**

きっかけは3ヶ月前に書いた [「生成AIといっしょ: 動作するきれいなコードを生成AIとつくる」](https://syu-m-5151.hatenablog.com/entry/2025/03/19/201025) という記事だった。当時はAIとの協業について考察していたが、正直なところ、まだ私が「運転席」に座っているつもりでいた。AIはあくまで「 [副操縦士](https://d.hatena.ne.jp/keyword/%C9%FB%C1%E0%BD%C4%BB%CE) 」だと。

[syu-m-5151.hatenablog.com](https://syu-m-5151.hatenablog.com/entry/2025/03/19/201025)

**現実は違った。**

実際にClaude Codeを使ってみて最初に感じたのは、自分の開発スタイルとAIの特性のミスマッチだった。私は根っからの「とりあえずコード書いてみよう」タイプ。設計書？計画？そんなものは書きながら考えればいい—それが私の流儀だった。

ところが、AIは違う。指示に対して忠実すぎるのだ。「認証機能を実装して」と曖昧に伝えれば、私の意図とは全く違う方向に突き進んでしまう。かといって、毎回細かく指示を出すのは面倒すぎる。

この問題を解決したのが **CLAUDE.md** という設定ファイルだった。

プロジェクトの文脈、コーディング規約、よく使うコマンド—すべてをAIが理解できる形で記述しておける。これにより、3ヶ月前に理論として描いていた「助手席からの開発」が現実のものとなった。私が大まかな方向を示せば、AIが詳細を埋めてくれる。計画嫌いの私にとって、これほど相性の良いツールはなかった。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/s/syu-m-5151/20250606/20250606185142.png)

[先日の記事](https://syu-m-5151.hatenablog.com/entry/2025/06/05/134147) でClaude Codeの基本的な使い方は紹介したが、今回はCLAUDE.mdに焦点を当てて深掘りしたい。実際のプロジェクトで使っている設定を公開し、どのようなワークフローで開発しているかを具体的に示す。

[syu-m-5151.hatenablog.com](https://syu-m-5151.hatenablog.com/entry/2025/06/05/134147)

もしまだClaude Codeを触ったことがないなら、 [公式チュートリアル](https://docs.anthropic.com/ja/docs/claude-code/tutorials) から始めることをお勧めする。また、Anthropicの [ベストプラクティス](https://www.anthropic.com/engineering/claude-code-best-practices) も必読だ。本記事はこれらの内容を前提として、より実践的な活用方法を掘り下げていく。

[docs.anthropic.com](https://docs.anthropic.com/ja/docs/claude-code/tutorials)

[www.anthropic.com](https://www.anthropic.com/engineering/claude-code-best-practices)

[全体的に疲れている時の~/.claude/settings.json と~/.claude/CLAUDE.md · GitHub](https://gist.github.com/nwiizo/8b7eb992875fc67a89368062d42d501e) 私の標準の設定もしているのでぜひ、読んでみてもらいたいです。

※この記事は社内勉強会で発表した内容をベースに、外部公開用に再構成したものです。

## CLAUDE.mdとは何か

毎回Claude Codeを起動するたびに「うちのプロジェクトはTypeScriptで、ESLintはこの設定で、テストはVitestを使っていて...」と説明するのは面倒だ。チームメンバーが同じプロジェクトで作業する時、全員が同じ説明を繰り返すのも非効率的だ。

この問題を解決するのが **CLAUDE.md** という特別なファイルだ。何も考えたくなければとりあえず、起動して `/init` と入力すれば良い。それで終わり。

CLAUDE.mdは、Claude Codeが起動時に自動的に読み込む設定ファイルで、プロジェクトの文脈をAIに伝える役割を持つ。 [アーキテクチャ](https://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3) の説明、コーディング規約、よく使うコマンドなど、プロジェクトで必要な情報をすべて記載しておける。一度書けば、毎回の説明が不要になる。

[docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/memory)

実は、CLAUDE.mdには配置場所によって3つの種類がある。

最も基本的なのは、プロジェクトルート（`./CLAUDE.md` ）に配置する **プロジェクトメモリ** だ。これはGit管理してチーム全体で共有する。プロジェクトの [アーキテクチャ](https://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3) 、使用している技術スタック、開発フローなど、チーム全員が守るべきルールを記載する。私の経験では、ここに書く内容がプロジェクトの品質を大きく左右する。

次に、ホーム [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リ（ `~/.claude/CLAUDE.md` ）に配置する **ユーザーメモリ** がある。これは個人的な設定で、すべてのプロジェクトに適用される。例えば「console.logではなく必ずloggerを使う」といった個人的なコーディングスタイルや、よく使う [スニペット](https://d.hatena.ne.jp/keyword/%A5%B9%A5%CB%A5%DA%A5%C3%A5%C8) を登録しておける。私はここに「コミットメッセージは必ず日本語で書く」という設定を入れている。

3つ目の`./CLAUDE.local.md` （プロジェクトメモリ・ローカル）は現在非推奨となっており、代わりにインポート機能を使うことが推奨されている。

Claude Codeがこれらのファイルをどう探すかも理解しておくと便利だ。現在の [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リから上位に向かって [再帰](https://d.hatena.ne.jp/keyword/%BA%C6%B5%A2) 的に探索し、見つかったものをすべて読み込む。さらに、サブ [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リ内のCLAUDE.mdも、その [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リのファイルを扱う時に自動的に参照される。つまり、モジュールごとに固有の設定を持たせることも可能だ。

これらのメモリファイルは `/memory` コマンドで確認・編集できる。ただ、複数のCLAUDE.mdを一度に確認したい場合もあるので、そのためのツール（ [ccat](https://github.com/nwiizo/ccat) ）も作成した。プロジェクトが大きくなるとCLAUDE.mdも複雑になるので、こういったツールがあると管理が楽になる。

[github.com](https://github.com/nwiizo/ccat)

## 探索・計画・コード・コミットのワークフロー

Anthropicのベストプ [ラク](https://d.hatena.ne.jp/keyword/%A5%E9%A5%AF) ティスでは、このワークフローが推奨されている。最初は「面倒くさそう」と思ったが、実際にやってみると驚くほど効果的だった。

### 計画が苦手な私がこのワークフローを採用する理由

私の開発スタイルは昔から一貫している。ア [イデア](https://d.hatena.ne.jp/keyword/%A5%A4%A5%C7%A5%A2) が浮かんだらすぐコードを書き始める。設計？後から考えればいい。ドキュメント？動いてから書けばいい。

このスタイルで10年以上やってきた。そして正直、それなりにうまくいっていた。

でもClaude Codeは違った。曖昧な指示を与えると、想像もしない方向に突き進む。「ユーザー認証を実装して」と伝えたら、JWT [トーク](https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF) ンを使った本格的なOAuth2.0実装を始めてしまった。私が欲しかったのは、シンプルなセッション認証だったのに。

そこで気づいた。AIは私の頭の中を読めない。当たり前だが、これが想像以上に大きな問題だった。

だからこそ、このワークフローが必要なのだ。探索→計画→実装→コミットという流れは、私の頭の中を整理し、AIに正確に伝えるための仕組みだった。面白いことに、AIのために始めたこの習慣が、私自身のコードの質も向上させた。

### なぜこのワークフローが効果的なのか

「とりあえずコードを書く」スタイルの最大の問題は、全体像が見えないまま進むことだ。気づいたら収拾がつかなくなっている。 [リファクタリング](https://d.hatena.ne.jp/keyword/%A5%EA%A5%D5%A5%A1%A5%AF%A5%BF%A5%EA%A5%F3%A5%B0) しようにも、影響範囲が分からない。

このワークフローはその問題を解決する。各段階を明確に分けることで、思考が整理される。そして何より、AIが各段階で最適な支援をしてくれる。

### ステップ1: 探索（関連ファイルの読み込み）

最初にやるのは現状把握だ。変更したいコードがどこでどう使われているか、依存関係はどうなっているか。これを理解せずに始めると、後で必ず痛い目を見る。

私がよく使うコマンド：

```
@src/services/UserService.ts を読んで、まだコードは書かないで
```

「まだコードは書かないで」という制約が重要だ。これを付けないと、AIは親切心から勝手に実装を始めてしまう。

依存関係を調べるときは：

```
UserServiceが依存している他のサービスも確認して
```

複雑なプロジェクトでは、サブエージェントを使うこともある：

```
サブエージェントで、UserServiceのメソッドがどこから呼ばれているか調査して
```

この探索フェーズで全体像を掴む。 [急がば回れ](https://d.hatena.ne.jp/keyword/%B5%DE%A4%AC%A4%D0%B2%F3%A4%EC) 、というやつだ。

### ステップ2: 計画（think モードの活用）

探索が終わったら、次は計画だ。ここでClaude Codeの「思考モード」が威力を発揮する。

問題の複雑さに応じて使い分ける：

```
このアーキテクチャをthinkで分析して、改善計画を立てて
```

より複雑な問題には：

```
この認証システムの問題をthink hardで検討して、複数の解決策を提示して
```

システム全体に関わる変更なら：

```
システム全体への影響をthink harderで評価して
```

最近は日本語でも「深く考えて」で動作するようになったらしい。個人的には英語の方が確実だと思うが。

[zenn.dev](https://zenn.dev/fbbp/articles/7aa9a46518a609)

計画ができたら必ず文書化する：

```
作成した計画をarchitecture-decisions/001-user-service-refactoring.mdに保存して
```

この文書化が後で自分を救う。「なぜこの設計にしたんだっけ？」という疑問に即答できる。

### ステップ3: 実装（検証を含む）

計画ができたら、いよいよ実装だ。でも、一気に全部作らない。小さく始めて、段階的に拡張する。

```
計画に従って、まずUserServiceの基本的なリファクタリングを実装して
```

実装したら必ず検証：

```
実装した部分のユニットテストを実行して、既存の機能が壊れていないか確認して
```

エッジケースも忘れずに：

```
nullやundefinedの場合の処理を追加して、エラーハンドリングを強化して
```

途中で問題に気づいたら、軌道修正を恐れない：

```
この実装だと循環依存が発生しそう。別のアプローチを検討して
```

私の経験では、この段階的な実装が品質を大きく左右する。テストリストの作成などもここで行います。一気に作ると、どこで問題が起きたか分からなくなる。

### ステップ4: コミットとPR作成

最後の仕上げがコミットとPR作成だ。ここも手を抜かない。

コミットは論理的な単位で分ける：

```
変更をリファクタリング、機能追加、テスト追加の3つのコミットに分けて
```

コミットメッセージはConventional Commitsに従う：

```
feat: ユーザーサービスに新しい認証メソッドを追加
refactor: UserServiceの内部構造を改善
test: UserServiceの新機能に対するテストを追加
```

PRの説明は詳細に：

```
PRを作成して。以下を含めて：
- 変更の背景と目的
- 実装アプローチの説明
- テスト方法
- 破壊的変更の有無
- レビュアーへの注意点
```

最後にドキュメントの更新も忘れずに、これらは `CLAUDE.md` に記載してもよい

```
README.mdとCLAUDE.mdも更新して、新しい機能とその使い方を記載して
```

このワークフローを続けた結果、コードの品質が明らかに向上した。何より、「なんとなく動く」コードから「なぜ動くか説明できる」コードになった。計画嫌いの私でも、このワークフローの価値は認めざるを得ない。

## テスト駆動開発（TDD）ワークフローの深掘り

TDDについて正直に話そう。3ヶ月前の記事では理想論を書いた。でも現実は全然違う。

### 正直なところ、TDDはいつも使うわけじゃない

私のTDD使用率は、せいぜい10%くらいだ。5%ぐらいかもしれない。

なぜそんなに低いのか。理由は単純で、私は「作りながら考える」タイプだから。最初から仕様が決まっていることなんて、実際に私がやっているような開発ではほとんどない。要求があるだけです。顧客も「動くものを見てから判断したい」と言うし、私も「とりあえず動かしてみないと分からない」と思っている。

でもClaude Codeを使い始めて、面白い発見があった。 **AIこそがTDDを必要としている** のだ。

「また生き返ったのかTDD」と思うかもしれない。でも今回は違う。人間のためのTDDではなく、AIのためのTDDだ。

### TDDがAIコーディングで特に重要な理由

AIの問題は「親切すぎる」ことだ。テストがないと、頼んでもいない機能まで実装してしまう。「ユーザー認証を実装して」と言ったら、ログイン履歴機能やパスワードリセット機能、二要素認証まで作り始める。

テストがあれば違う。「このテストが通ればOK」という明確なゴールがある。AIは迷わない。過剰な実装もしない。これが快適だ。

### 私がTDDを使う「よっぽど決まっているとき」

では、具体的にどんな時にTDDを使うのか。

**1\. [API](https://d.hatena.ne.jp/keyword/API) のインターフェースが確定したとき**

OpenAPI仕様書がある場合は迷わずTDDだ。リク [エス](https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9) トとレスポンスの型が決まっていて、エラーケースも定義されている。こういう時は最初にテストを書く方が早い。

**2\. 既存機能の [リファクタリング](https://d.hatena.ne.jp/keyword/%A5%EA%A5%D5%A5%A1%A5%AF%A5%BF%A5%EA%A5%F3%A5%B0)**

「動作を変えずに内部構造を改善する」という明確な目標がある。現在の動作をテストで固定してから、安心して [リファクタリング](https://d.hatena.ne.jp/keyword/%A5%EA%A5%D5%A5%A1%A5%AF%A5%BF%A5%EA%A5%F3%A5%B0) できる。

**3\. バグ修正**

「このバグ、二度と出したくない」という強い決意がある時。再現手順が明確で、期待される動作も分かっている。テストを書いてから修正すれば、同じバグは二度と起きない。

つまり、 **ゴールが明確な時だけ** TDDを使う。探索的な開発では使わない。これが私の現実的なアプローチだ。

### ステップ1: テストファースト

AIとTDDを組み合わせる時、最初のテスト作成が肝心だ。

例えば、ユーザー認証機能を作る場合：

```
UserService.authenticateメソッドのテストを作成して。
以下のケースをカバー：
- 正常な認証成功
- パスワード不一致
- ユーザーが存在しない
- アカウントがロックされている
- 連続失敗によるロック
```

ポイントは「網羅的に書く」こと。人間なら「まあこれくらいでいいか」と手を抜くところも、AIは真面目に全部実装してくれる。

あと、個人的にはモックを使わない派だ：

```
実際のデータベース接続を使用してテストを作成。モックは使わない
```

モックを使うと、実際の動作と乖離することがある。開発環境でDockerを使えば、本物のデータベースでテストできる。遅い？確かに。でも「動くと思ったのに本番で動かない」よりマシだ。 あと同時に大切なのが本番環境を絶対に触らせないことです。

### ステップ2: RED - 失敗の確認

テストを書いたら、必ず失敗することを確認する。これ、意外と重要。

```
npm test -- UserService.test.ts
```

失敗を見たら、AIに分析してもらう：

```
テストの失敗理由を分析して。以下の観点で：
- コンパイルエラーか実行時エラーか
- 期待値と実際の値の差異
- 未実装による失敗か、バグによる失敗か
```

なぜわざわざ失敗を確認するのか。「最初から成功するテスト」は信用できないからだ。それはテストが甘いか、既に実装されているかのどちらかだ。

### ステップ3: GREEN - 最小限の実装

ここでAIの「親切心」と戦う必要がある。

```
テストが通る最小限の実装を作成して。
過度な最適化や追加機能は含めない
```

それでもAIは余計なことをしたがる。だから明示的に制約する：

```
IMPORTANT: テストケース以外の機能は実装しない
YOU MUST: 各実装ステップ後にテストを実行して確認
```

段階的に進めるのもコツだ：

```
まず最も単純なケース（正常な認証）から実装を始めて
```

一気に全部作らせると、どこで問題が起きたか分からなくなる。

### ステップ4: REFACTOR - コードの改善

テストが通ったら、ようやく [リファクタリング](https://d.hatena.ne.jp/keyword/%A5%EA%A5%D5%A5%A1%A5%AF%A5%BF%A5%EA%A5%F3%A5%B0) だ。ここでAIの本領発揮。

```
テストが通ることを確認しながら、以下の観点でリファクタリング：
- 重複コードの除去
- 可読性の向上
- パフォーマンスの最適化
- ドキュメントの記載(README.md,CLAUDE.md,etc)
- コメントの記載
```

個人的には、このタイミングでドキュメントを書いてもらうことが多い。実装が終わってからだと、細かい仕様を忘れてしまうから。

時には複数の改善案を比較することも：

```
このコードの問題点を指摘して、改善案を3つ提示して
```

AIは客観的に問題点を指摘してくれる。人間のレビュアーと違って、遠慮がない。

## 便利なショートカットとツール

Claude Codeには知らないと損するショートカットがたくさんある。

[docs.anthropic.com](https://docs.anthropic.com/ja/docs/claude-code/cli-usage)

### @ ファイル選択の効果的な使い方

最も使うのが `@` によるファイル選択だ。

基本形：

```
@src/services/UserService.ts のcreateUserメソッドを改善して
```

でも、本当の威力は複数ファイルを扱う時に発揮される：

```
@src/services/UserService.ts と @src/models/User.ts を見て、データフローを説明して
```

AIが関連ファイルを横断的に分析してくれる。人間だと「えーと、このファイルとあのファイルを開いて...」となるところが、一瞬で終わる。

[ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リ全体を見ることも：

```
@src/services/ ディレクトリのすべてのサービスの概要を説明して
```

私のお気に入りは [ワイルドカード](https://d.hatena.ne.jp/keyword/%A5%EF%A5%A4%A5%EB%A5%C9%A5%AB%A1%BC%A5%C9) ：

```
@**/*Service.ts すべてのサービスファイルで共通のパターンを見つけて
```

[リファクタリング](https://d.hatena.ne.jp/keyword/%A5%EA%A5%D5%A5%A1%A5%AF%A5%BF%A5%EA%A5%F3%A5%B0) の時、これで共 [通化](https://d.hatena.ne.jp/keyword/%C4%CC%B2%BD) できる部分を見つけてもらう。

### 通知設定

これ、本当に知らない人が多い。Claude Codeは長時間のタスクも黙々とこなしてくれるが、通知設定をしていないと完了に気づけない。

[docs.anthropic.com](https://docs.anthropic.com/ja/docs/claude-code/settings#%E9%80%9A%E7%9F%A5%E8%A8%AD%E5%AE%9A)

私は「タスク完了時に音を鳴らす」設定にしている。コーヒーを飲みながら待てる。

### \# ルール追加の戦略的活用

その場限りのルールを追加したい時は `#` を使う：

```
#このプロジェクトではzodでバリデーション。yupは使わない
#エラーメッセージは必ず日本語で記述
#APIレスポンスは必ずcamelCaseで統一
```

CLAUDE.mdに書くほどでもない、一時的なルールに便利だ。例えば「今日は英語のコメントで統一」みたいな時に使う。

ルールの優先順位は： 1. セッション中の `#` コマンド（最優先） 2. プロジェクトのCLAUDE.md 3. ユーザーのCLAUDE.md（~/.claude/）

この階層を理解していると、柔軟にルールを管理できる。

## スクショを使う、CleanShot Xを購入せよ私のTDD使用率

コードだけでなく、ビジュアルでの確認も重要だ。特にUI開発では必須。

### なぜCleanShot Xなのか

[macOS](https://d.hatena.ne.jp/keyword/macOS) の標準 [スクリーンショット](https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A1%BC%A5%F3%A5%B7%A5%E7%A5%C3%A5%C8) も悪くない。でもCleanShot Xは別次元だ。

[cleanshot.com](https://cleanshot.com/)

何が違うか： - 撮影後すぐに注釈を追加できる（矢印、テキスト、モザイク） - スクロールキャプチャで長いページも1枚に - GIF録画で操作手順を記録 - [クラウド](https://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9) にアップロードしてURLで共有

特に「注釈」機能が神。「ここのマージンがおかしい」とか「このボタンの色を変えて」とか、視覚的に伝えられる。

### Claude Codeとの連携テクニック

私のワークフロー：

1. CleanShot Xで [スクリーンショット](https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A1%BC%A5%F3%A5%B7%A5%E7%A5%C3%A5%C8) （Cmd+Shift+4）
2. 問題箇所に赤丸や矢印で注釈
3. Claude Codeに [ドラッグ&ドロップ](https://d.hatena.ne.jp/keyword/%A5%C9%A5%E9%A5%C3%A5%B0%26amp%3B%A5%C9%A5%ED%A5%C3%A5%D7)

例えば：

```
このデザインモックアップに基づいてコンポーネントを実装して
```

画像を見せながら指示すると、AIの理解度が格段に上がる。「左側のサイドバーの幅を...」とか説明するより、画像1枚の方が早い。

バグ報告でも威力を発揮：

```
このエラー画面が表示される原因を調査して修正して
```

エラーメッセージだけでなく、画面全体の状態を伝えられる。

## セッション管理とコンテキストの継続性

「昨日の続きから作業したいけど、どこまでやったっけ？」

この問題、Claude Codeなら解決できる。でも意外と知られていない。

### 継続的な開発フローの構築

朝一番のコマンド：

```bash
$ claude --continue
```

これで前回のセッションの続きから始められる。AIは前回の作業内容を覚えている。

特定のセッションを選びたい時：

```bash
$ claude --resume
```

複数のプロジェクトを並行して進めている時に便利。

プロンプト履歴の編集:

```
[Esc][Esc] → 前のプロンプトを編集 → Enter
```

ダブルEscapeで過去のプロンプトを編集可能。異なるアプローチを試すときに便利。具体的な指示を心がける。

私の [日課](https://d.hatena.ne.jp/keyword/%C6%FC%B2%DD) は、1日の終わりに：

```
今日の作業内容を要約して、明日やるべきことをリストアップして
```

これをやっておくと、翌日スムーズに始められる。AIが秘書みたいに働いてくれる。

### コンテキストの最適化

長時間作業していると、コンテキストがゴチャゴチャしてくる。そんな時は：

```
/clear
```

でリセット。その後：

```
@CLAUDE.md を読んで、プロジェクトのコンテキストを復元して
```

これで必要な情報だけを再読み込みできる。

「お前は公式ドキュメントを読んでないな！？」と言いたくなるくらい、みんなこの機能を知らない。もったいない。

## 3ヶ月前の理論が現実になって

3ヶ月前、私は生成AIとの未来について妄想を書いた。「助手席での開発」「レッドボックス」「バイブスコーディング」...正直、半分くらいは願望だった。

[speakerdeck.com](https://speakerdeck.com/watany/the-end-of-the-special-time-granted-to-engineers)

でも、Claude Codeを1週間使った今、それらは全て現実になっている。いや、想像以上だった。

### 助手席での開発が意外と楽しい

「運転席を譲る」ことへの恐怖があった。エンジニアとしての [アイデンティティ](https://d.hatena.ne.jp/keyword/%A5%A2%A5%A4%A5%C7%A5%F3%A5%C6%A5%A3%A5%C6%A5%A3) が揺らぐような気がして。

でも実際は違った。助手席は助手席で、やることがたくさんある。

**私の役割：**

- 目的地を決める（何を作るか）
- ルートを提案する（ [アーキテクチャ](https://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3) ）
- 危険を察知する（セキュリティ、パフォーマンス）

**Claudeの役割：**

- 実際の運転（コーディング）
- 交通ルールの遵守（言語仕様、ベストプ [ラク](https://d.hatena.ne.jp/keyword/%A5%E9%A5%AF) ティス）
- 効率的なルート選択（ [アルゴリズム](https://d.hatena.ne.jp/keyword/%A5%A2%A5%EB%A5%B4%A5%EA%A5%BA%A5%E0) 、最適化）

この役割分担が心地いい。特に「計画は苦手だけどア [イデア](https://d.hatena.ne.jp/keyword/%A5%A4%A5%C7%A5%A2) は豊富」な私にとって、理想的なパートナーだ。

### レッドボックスとの遭遇

実際にあった話。Claude Codeがこんなコードを生成した：

```typescript
// Claudeが生成した謎のTypeScript型パズル
type DeepPartial<T> = T extends object ? {
  [P in keyof T]?: DeepPartial<T[P]>;
} : T;

type RecursiveRequired<T> = T extends object ? {
  [P in keyof T]-?: RecursiveRequired<T[P]>;
} : T;
```

正直、5秒見つめても理解できなかった。これが「レッドボックス」だ。

でも大丈夫。CLAUDE.mdに追加すればいい：

```markdown
## 理解困難なコードへの対処
- IMPORTANT: 複雑な型定義には必ず使用例とコメントを追加
- YOU MUST: 生成したコードの動作原理を説明できること
```

これで次からは、AIが勝手に説明を追加してくれる。

### バイブスコーディングの実践

これが一番楽しい発見だった。曖昧な指示でも、AIは文脈を読んでくれる：

```
なんか認証周りがイケてない気がする。もっとスマートにして
```
```
このUIのレイアウト、もうちょっとモダンな感じにして
```
```
パフォーマンスがビミョーだから、なんとかして
```

「ビミョー」で伝わるAI。これがCLAUDE.mdの威力だ。プロジェクトの文脈を理解しているから、曖昧な指示でも適切に解釈してくれる。

## 実践的なCLAUDE.md設定例

理論はもういい。実際のCLAUDE.mdを見せよう。

私が開発している [cctx](https://github.com/nwiizo/cctx) プロジェクトから、効果的な部分を抜粋する。

### プロジェクト概要：読みやすさの工夫

```markdown
# 🔄 CLAUDE.md - cctx Project Documentation

## 📋 Project Overview

**cctx** (Claude Context) is a fast, secure, and intuitive command-line tool 
for managing multiple Claude Code \`settings.json\` configurations. 
Built with Rust for maximum performance and reliability.

## 🏗️ Architecture

### 🎯 Core Concept
- **🔧 Context**: A saved Claude Code configuration stored as a JSON file
- **⚡ Current Context**: The active configuration (\`~/.claude/settings.json\`)
- **📁 Context Storage**: All contexts stored in \`~/.claude/settings/\`
- **📊 State Management**: Current and previous context tracked in \`.cctx-state.json\`
```

絵文字を使っているのは、人間（つまり私）が見た時に分かりやすいから。AIは絵文字なくても理解するが、私が理解できない。

### AIへの具体的な指示：成功の秘訣

曖昧な指示より具体的な指示の方が成功率が大幅に向上します。曖昧さは、AIには毒だ。

```markdown
## 📚 Notes for AI Assistants

When working on this codebase:

1. **Always run \`cargo clippy\` and fix warnings** before suggesting code
2. **Test your changes** - don't assume code works
3. **Preserve existing behavior** unless explicitly asked to change it
4. **Follow Rust idioms** and best practices
5. **Keep the kubectx-inspired UX** - simple, fast, intuitive
6. **Maintain predictable defaults** - user should never be surprised
7. **Document any new features** in both code and README
8. **Consider edge cases** - empty states, missing files, permissions

Remember: This tool is about speed and simplicity. 
Every feature should make context switching faster or easier, not more complex. 
**Predictability beats cleverness.**
```

最後の一文が効いている。「賢いより予測可能」。AIは時々、賢すぎる解決策を提案してくる。でもユーザーが求めているのは、予測可能な動作だ。

### 開発ガイドライン：チェックリストの威力

```markdown
### Testing Checklist

When testing changes, verify:

- [ ] \`cctx\` lists all contexts correctly
- [ ] \`cctx <n>\` switches context
- [ ] \`cctx -\` returns to previous context
- [ ] Error messages are clear and helpful
- [ ] State persistence works across sessions
```

チェックリスト形式にすると、AIもチェックしながら作業してくれる。レビュー時も楽。

## プロンプト改善のテクニック

Anthropic公式が推奨する強調表現、実は3段階ある。使い分けが重要だ。

### 強調レベルの使い分け

**NEVER（絶対禁止）：**

```
NEVER: パスワードやAPIキーをハードコーディングしない
NEVER: ユーザーの確認なしにデータを削除しない
NEVER: テストなしで本番環境にデプロイしない
```

これは本当にやってはいけないこと。AIは素直なので、明示的に禁止しないとやってしまう可能性がある。

**YOU MUST（必須事項）：**

```
YOU MUST: すべての公開APIにドキュメントを記載
YOU MUST: エラーハンドリングを実装
YOU MUST: 変更前に既存テストが通ることを確認
```

必ずやってほしいこと。でも、状況によっては例外もありえる。

**IMPORTANT（重要事項）：**

```
IMPORTANT: パフォーマンスへの影響を考慮
IMPORTANT: 後方互換性を維持
IMPORTANT: セキュリティベストプラクティスに従う
```

考慮してほしいこと。判断はAIに委ねる。

この3段階を使い分けることで、AIの行動を適切にコン [トロール](https://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB) できる。

## 高度な活用：並行開発とCI/CD

### カスタムスラッシュコマンドで定型作業を自動化

**公式例： [GitHub](https://d.hatena.ne.jp/keyword/GitHub) Issue対応の自動化**

`.claude/commands/fix-github-issue.md`:

```markdown
Please analyze and fix the GitHub issue: $ARGUMENTS.

Follow these steps:

1. Use \`gh issue view\` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR

Remember to use the GitHub CLI (\`gh\`) for all GitHub-related tasks.
```

使用方法：

```
> /project:fix-github-issue 1234
```

### Git Worktreeで複数タスクを同時進行

これは上級テクニック。でも覚えると手放せなくなる。

[git-scm.com](https://git-scm.com/docs/git-worktree)

例えば、機能開発しながらバグ修正もしたい時：

```bash
# 機能開発用worktree
$ git worktree add ../project-feature-auth feature/auth

# バグ修正用worktree  
$ git worktree add ../project-bugfix-api bugfix/api-error

# 各worktreeで独立したClaude Codeセッション
$ cd ../project-feature-auth && claude
$ cd ../project-bugfix-api && claude
```

それぞれの [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リで独立したClaude Codeセッションが動く。コンテキストが混ざらない。最高。 というか人の業の深さを感じれてよい…。

### CI/CDへの統合

Claude Codeは [コマンドライン](https://d.hatena.ne.jp/keyword/%A5%B3%A5%DE%A5%F3%A5%C9%A5%E9%A5%A4%A5%F3) ツールなので、CI/CDにも組み込める。

[docs.anthropic.com](https://docs.anthropic.com/ja/docs/claude-code/cli-usage#%E3%81%AF%E3%81%98%E3%82%81%E3%81%AB)

[GitHub](https://d.hatena.ne.jp/keyword/GitHub) Actionsの例：

```yaml
- name: Claude Code Review
  run: |
    claude -p "このPRの変更をレビューして、以下の観点で問題を指摘：
    - セキュリティ脆弱性
    - パフォーマンス問題
    - コーディング規約違反" \
    --output-format json > review.json
```

自動化できることは自動化する。人間はもっとクリエイティブなことに時間を使うべきだ。

### permissions.allowの推奨設定

セッション中に「Always allow」を選択するか、 `/permissions` コマンドで追加できるがsetting.[json](https://d.hatena.ne.jp/keyword/json) でも追加できる。

[syu-m-5151.hatenablog.com](https://syu-m-5151.hatenablog.com/entry/2025/06/05/134147)

公式が推奨する基本的な許可リスト：

```json
{
  "permissions": {
    "allow": [
      "List(*)",
      "Fetch(https://*)",
      "Edit(*)",
      "Bash(git:*)",
      "Bash(npm:*)",
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(mkdir:*)",
      "Bash(mv:*)"
    ]
  }
}
```

## まとめ

Claude Codeを1週間使い込んで確信した。 **CLAUDE.mdは単なる設定ファイルじゃない。AIとの共通言語だ。**

![](https://www.youtube.com/watch?v=iw_1zU2MSc8)[youtu.be](https://youtu.be/iw_1zU2MSc8?si=Ftiid7vDxTh8xu1P)

3ヶ月前、私は理想を語った。「生成AIとの協業で『動作するきれいなコード』を実現する」と。正直、半分は願望だった。

[syu-m-5151.hatenablog.com](https://syu-m-5151.hatenablog.com/entry/2025/03/19/201025)

でも今、それは現実になっている。私は「助手席」に座り、AIが「運転席」でコードを書く。最初は違和感があったが、今では心地いい。むしろ、なぜ今まで全部自分でやろうとしていたのか不思議にさえ思う。

[www.oreilly.com](https://www.oreilly.com/library/view/beyond-vibe-coding/9798341634749/)

### 3ヶ月で変わったこと、変わらなかったこと

**変わったこと：**

- 理論が実践になった
- 曖昧な指示でも伝わるようになった
- コードの品質が向上した
- 開発速度が圧倒的に上がった

**変わらなかったこと：**

- 計画を立てるのは相変わらず苦手
- TDDへの抵抗感は残っている
- 「とりあえず動かしてみる」精神は健在
- コードを書く楽しさは失われていない

最後の点が重要だ。AIに仕事を奪われたのではない。つまらない部分を任せて、楽しい部分に集中できるようになった。

効果的なCLAUDE.mdを書くコツ：

1. **明確なプロジェクト概要** - AIも人間も理解できるように
2. **具体的なワークフロー** - 探索→計画→実装→コミット
3. **実用的なコマンド集** - よく使うものを網羅
4. **AIへの明示的な指示** - NEVER、YOU MUST、IMPORTANTを使い分ける

これらを押さえれば、AIは最高のパートナーになる。

**「予測可能性は賢さに勝る」** - この原則を胸に、CLAUDE.mdを育てていこう。

3ヶ月後、私はまた記事を書いているだろう。タイトルは分からない。でも一つ確実なのは、私はまだ助手席に座っているということ。そして、それを楽しんでいるということだ。

なぜなら、助手席こそが最も楽しい席だから。他社も同じぐらいのプランをいずれ出すので [Codex CLI](https://openai.com/codex/) や [jules](https://jules.google/) が楽しみです。

[« 技術的負債の変質について](https://syu-m-5151.hatenablog.com/entry/2025/06/10/091446) [Introducing cctx: A Context Switcher fo… »](https://syu-m-5151.hatenablog.com/entry/2025/06/05/232126)