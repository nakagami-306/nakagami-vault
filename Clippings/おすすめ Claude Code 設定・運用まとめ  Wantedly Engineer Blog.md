---
Title: "おすすめ Claude Code 設定・運用まとめ | Wantedly Engineer Blog"
Source: "https://www.wantedly.com/companies/wantedly/post_articles/981006"
ClippedDate: 2025-06-09
Tags:
  - "clippings"
---
こんにちは。ウォンテッドリーで Enabling チームでバックエンドエンジニアをしている市古（ [@sora\_ichigo\_x](https://x.com/sora_ichigo_x) ）です。

現在、Enablingチームでは技術的な取り組みを社外にも発信すべく、メンバーが週替わりで技術ブログをリレー形式で執筆しています。前回は冨永さんによる [「Pocket終了に備えてObsidian Web Clipperに移行した話」](https://www.wantedly.com/companies/wantedly/post_articles/978503) でした。今回は、実践 Claude Code の話をしたいと思います。

先日、 [Anthropic から Claude 4 がリリースされ](https://www.anthropic.com/news/claude-4) 、それに合わせて Claude Code の一般提供が始まりました。これまで研究プレビューだった期間を経て、誰でも利用できるようになっています。

本記事には Claude Code を知らない方向けの解説も含みますが、実際の設定・運用がすぐに知りたい方は読み飛ばしてください。

## Claude Code

### ターミナル上で動作するコーディングAIエージェント[Claude Code 概要 - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/overview)

[

Anthropicが開発したエージェント型コーディングツールであるClaude Codeについて学びましょう。現在はベータ版として研究プレビュー中です。

](https://docs.anthropic.com/ja/docs/claude-code/overview)

任意のディレクトリで `claude` コマンドを実行すると対話セッションが立ち上がり、コーディングタスクを指示できます。

以下は筆者の dotfiles リポジトリで「このリポジトリについて説明して」と指示した例です。

![](https://images.wantedly.com/i/8sRsFSg?w=1440)

詳細な説明は公式ドキュメントに譲りますが、Cursor や Cline に比べて意図的に低レベルで、生のモデルに近いアクセスを提供している点が Claude Code の特徴です。この特徴により、柔軟でカスタマイズ可能なツールになっている一方で、個々人がベストプラクティスを確立するまでの学習コストが高くなっています。

### インストール方法

以下のドキュメントに従い npm を使ってインストールしましょう。[Claude Codeを使い始める - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/getting-started)

[

Claude Codeのインストール、認証、使用開始方法について学びます。

](https://docs.anthropic.com/ja/docs/claude-code/getting-started)

## 料金

Claude Code には2つの料金体系が存在します。

1. [Anthropic API](https://docs.anthropic.com/en/home) の従量課金で使う  
	料金：モデルによる従量課金（ [公式](https://docs.anthropic.com/ja/docs/claude-code/costs) 曰く、1日あたり平均$6程度と見積もられている）  
	公式URL： [https://docs.anthropic.com/ja/docs/about-claude/pricing](https://docs.anthropic.com/ja/docs/about-claude/pricing)
2. [Claude Max プラン](https://www.anthropic.com/news/max-plan) で使う（レートリミット付きだが無制限）  
	料金：月額 $100  
	公式URL： [https://www.anthropic.com/pricing](https://www.anthropic.com/pricing)

Anthropic API を使う場合は従量課金なのでスモールスタートで検証できますが、$100以上使うようであれば Claude Max プランを契約した方がお得です。

ちなみに現在 **、** 筆者は Anthropic API の方法を使っていますが、個人の意見としては「トークン数をケチらないこと」はバイブコーディングの出力品質を上げるための1つの重要なコツと考えているため、Claude Code を長期的に使っていくのであれば Claude Max プランの契約を視野に入れると思います。

## 前提

はじめに、Claude Code 設定の仕組みを解説します。

### Claude Code 設定の階層構造

Claude Code の設定は3階層で構成されています。それぞれ設定のスコープが異なるため、設定目的に応じて使い分けます。

1. グローバル設定： `~/.claude/` で定義され、すべてのプロジェクトに適用。
2. プロジェクト設定： プロジェクトディレクトリの `.claude` もしくは `CLAUDE.md` で定義され、プロジェクトごとに適用。Git 管理推奨。
3. プロジェクト設定（ローカル）：Git管理非推奨なプロジェクト設定。

公式ドキュメント： [Claude Codeの設定 - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/settings)

組織で利用する場合は上記に加えて、エンタープライズ設定を強制可能です。

### ~/.claude ディレクトリ内のファイル

Claude Code のグローバル設定や内部状態は ~/.claude ディレクトリに保存されます。実際のディレクトリ構成は以下の通りです。

```
~/.claude/
├── CLAUDE.md          （コーディング方針などのインストラクションプロンプト）
├── settings.json      （グローバル設定）
├── commands/          （カスタムスラッシュコマンドの定義）
```
- CLAUDE.md について： [Claudeのメモリを管理する - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/memory)
- - プロジェクト設定の場合は `./CLAUDE.md` なので場所が違うことに注意
- settings.json について： [Claude Codeの設定 - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/settings#%E6%A8%A9%E9%99%90)

## 設定

ここからは筆者が Claude Code を実践利用する上で行なっている設定を解説します。

### なるべくグローバル設定に寄せる

前述の通り、Claude Code ではグローバル設定とプロジェクト設定の2種類のスコープが存在しますが、筆者はなるべくグローバル設定側に寄せる方針を取っています。

理由は「リポジトリ間の重複を避けたい」「Claude Code に渡す情報の一貫性を保ちたい」の2点です。

プロジェクト設定にはプロジェクト特有の事情のみ記載します。

### グローバル設定を dotfiles 管理する

Claude Code のグローバル設定は全て `~/.claude` 配下に保存されているため、これらのファイルはそのまま dotfiles リポジトリに含めて管理できます。

筆者dotfiles： [https://github.com/sora-ichigo/dotfiles/tree/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude](https://github.com/sora-ichigo/dotfiles/tree/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude)

dotfiles に含めておけば、シェルの設定同様、複数環境での一貫した設定の適用が容易になり、履歴管理が安全に行えます。

### 常に日本語で回答させる

筆者は日本語で情報を処理することが最も効率的であると考えているため、Claude Code も日本語で扱えると効率的です。

しかしClaude Code は日本語で話しかけても時々英語で回答することがあるため、 `~/.claude/CLAUDE.md` にルールとして明示しています。

```
# CLAUDE.md

## Conversation Guidelines

- 常に日本語で会話する
```

コード： [https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude/CLAUDE.md](https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude/CLAUDE.md)

### 常に TDD (テスト駆動開発) させる

バイブコーディング全般に言えることですが、バイブコーディングは TDD（テスト駆動開発）と相性が良いです。Claude Code のようなコーディングエージェントは、評価基準が明確であるほど出力の安定性が高まります。期待される入出力を定義した自動テストは、まさにその基準として機能します。

Claude Code に TDD を強制する `ためには ~/.claude/CLAUDE.md にルールとして明示させます。`

```
# CLAUDE.md

## Conversation Guidelines

- 常に日本語で会話する

## Development Philosophy

### Test-Driven Development (TDD)

- 原則としてテスト駆動開発（TDD）で進める
- 期待される入出力に基づき、まずテストを作成する
- 実装コードは書かず、テストのみを用意する
- テストを実行し、失敗を確認する
- テストが正しいことを確認できた段階でコミットする
- その後、テストをパスさせる実装を進める
- 実装中はテストを変更せず、コードを修正し続ける
- すべてのテストが通過するまで繰り返す
```

コード： [https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude/CLAUDE.md](https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude/CLAUDE.md)

ちなみにこのやり方は、Anthropic公式のベストプラクティスにも紹介されています（ [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices) ）

### 許可されたツールリスト (permissions.allow) を育てる

Claude Code では settings.json 内の `permission.allow` に許可するコマンドや操作を登録できます。ここを適切に整備することで、Claude Code が人間に確認せずに自律的に処理できる範囲を広げることができます。

公式ドキュメント： [Claude Codeの設定 - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/settings#%E6%A8%A9%E9%99%90)

Claude Code はデフォルトで、システムを変更する可能性のあるすべてのアクション（ファイルの書き込み、コマンド、MCPツール）に対して承認を求めます。これは安全性を最優先に意図して保守的なアプローチを採用する Claude Code の設計によるところだと筆者は考えています（ [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices) ）。

もちろん破壊的な操作（例: rm -rf /）は慎重に扱うべきですが、日常的に利用している安全なコマンドについては、あらかじめ許可しておくと実運用では快適になります。無駄な確認が減る分、AIの自律性を活かしたバイブコーディングがスムーズに進められます。

こちらも `~/.claude/settings.json` に記載して dotfiles 管理しています。以下は一部抜粋です。

```javascript
{
  "permissions": {
    "allow": [
      "List(*)",
      "Fetch(https://*)",
      "Bash(ls:*)",
      "Bash(rg:*)",
      "Bash(mv:*)",
      ...
      ],
    "deny": []
  }
}
```

コード： [https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude/settings.json](https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.claude/settings.json)

また、設定が適切に行われたかどうかは `/permissions` コマンドで確認できます。

![](https://images.wantedly.com/i/Mq8ZuC4?w=1440)

### MCP サーバーを設定する

外部サービスと連携するための仕組みとして [MCP (Model Context Protocol)](https://modelcontextprotocol.io/introduction) を利用できます。これにより、Claude Code がローカル環境の枠を超えて、各種クラウドサービスや社内ツールと連携しながらコーディングを進めることが可能になります。

Claude Code は [MCP がサポートするトランスポートメカニズム](https://modelcontextprotocol.io/docs/concepts/architecture#transport-layer) である Stdio transport / HTTP with SSE transport の両方に対応しており、それぞれ以下のコマンドで設定可能です。

**① Stdio transport (例: GitHub MCP)**

```bash
$ export GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxxx
$ claude mcp add github \
         -e GITHUB_PERSONAL_ACCESS_TOKEN=$GITHUB_PERSONAL_ACCESS_TOKEN \
         -- docker run -i --rm \
         -e GITHUB_PERSONAL_ACCESS_TOKEN \
         ghcr.io/github/github-mcp-server
```

**② HTTP with SSE transport (例: DevinWiki MCP)**

```bash
$ claude mcp add -t sse -s user devinwiki https://mcp.deepwiki.com/sse
```

注意点として、MCP の設定は他の設定と違い `~/.claude.json` に秘匿情報付きで保存されるため、Git 管理が出来ません。設定自体のGit管理は諦めて、各環境でコマンド実行する必要があります。

現段階で筆者はMCPをフル活用しているというより、GitHub MCP を gh の代わりに使っているレベルですが、今後の MCP の発展を見つつコンテキストに含められる情報をより効果的にできないか注視していくつもりです。

### Git Worktree を効率的に使えるようにエイリアス・シェル関数を作成する

Claude Code で複数のブランチを並行して扱う場合、 [Git Worktree](https://git-scm.com/docs/git-worktree) を活用すると作業が効率化できます。

Git Worktree は、1つのリポジトリから複数の作業ディレクトリ（ワークツリー）を作成し、異なるブランチやコミットを個別のディレクトリで同時に操作できる仕組みです。

Worktree を使うと、同じリポジトリを複数の作業ディレクトリに展開できるため、並行開発や長めのバイブコーディングセッションでも環境を切り分けて進められます。 [Claude Code 公式ドキュメント](https://docs.anthropic.com/ja/docs/claude-code/tutorials#git%E3%83%AF%E3%83%BC%E3%82%AF%E3%83%84%E3%83%AA%E3%83%BC%E3%81%A7%E4%B8%A6%E5%88%97claude-code%E3%82%BB%E3%83%83%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B) でも言及されています。

ただし、Worktree のコマンドはやや冗長になりがちなため、筆者はエイリアスやシェル関数を用意して運用しています。

**① Worktree の作成・削除用エイリアス（.gitconfig）**

```bash
[alias]
  wb = "!f() { WORKTREE_PATH=../$(basename $(pwd))-${1}; git worktree add $WORKTREE_PATH $2; }; f"
  wd = worktree remove .
```
- git wb <branch> で新しい worktree を作成
- git wd で現在の worktree を削除

コード： [https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.gitconfig#L28-L29](https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.gitconfig#L28-L29)

**② Worktree 移動用のシェル関数**

```bash
select_worktree() {
  local worktrees
  worktrees=$(git worktree list --porcelain | awk '/worktree / {print $2}')
  if [[ -z "$worktrees" ]]; then
    echo "No worktrees found."
    return 1
  fi
  local selected
  selected=$(echo "$worktrees" | fzf)
  if [[ -n "$selected" ]]; then
    echo "$selected"
    cd "$selected"
  fi
}
register_keycommand "^j" select_worktree
```
- select\_worktree は既存の worktree を一覧表示し、fzf で選択して移動可能
- ^j にキーバインドを設定している

コード： [https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.zsh/functions.zsh#L46-L60](https://github.com/sora-ichigo/dotfiles/blob/985331d21ac745234208804a1d7ec2eef963aaca/config/.zsh/functions.zsh#L46-L60)

## 運用

続いて、Claude Code 運用面での工夫を紹介していきます。

### Shift+Tab で auto-accept edits を有効にする

Claude Code では、Shift + Tab で auto-accept edits を有効にすると、Claude Code の提案を自動承認できます。

- デフォルトでは auto-accept edits は無効
![](https://images.wantedly.com/i/yQT5CpC?w=1440)

- Shift + Tab を押すと auto-accept edits が有効になる
![](https://images.wantedly.com/i/aE6Sn2E?w=1440)

Anthropic公式のベストプラクティス（ [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices) ）では「人間の適切な介入による早期かつ頻繁な軌道修正 (意訳)」を推奨しており、デフォルトで auto-accepet edits が有効になっていない点に Claude Code の設計思想を感じます。一方で、Cursor や Cline ではタスクが完了するまで承認を必要としない動作が基本です。

筆者としても、できるだけ人間の介入ポイントを減らしてコーディングAIエージェントの自律性を高めたいと考える立場なので、作業中のほとんどは auto-accept edits を有効にしています。修正の必要があれば git から変更を削除するか、次セクションで紹介する esc による作業中断を活用しています。

### esc で中断・esc 2回で前の指示に戻る

Claude Code では、esc キーを活用することでセッション中の作業フローを柔軟にコントロールできます。

公式言及箇所： [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices#:~:text=Press%20Escape%20to,a%20different%20approach.)

- **① esc（1回）**  
	実行中の作業を即座に中断できます。Claude Code は常にコンテキストを保持しているため、中断後にそのまま新たな指示を出すことが可能です。思った方向に進んでいないと感じたときや、途中で仕様を修正したくなったときに有効です。
![](https://images.wantedly.com/i/ayZvH1c?w=1440)

- **esc（2回連続）**  
	前のプロンプト履歴に遡り、入力内容を修正できます。Claude Code では履歴もコンテキストの一部として活用されており、過去の指示を編集し直すことで、不要な積み上げを避けてスムーズに別アプローチへ切り替えることができます。
![]()

Devin のようなリモート AI コーディングエージェントを使わずにあえてローカルでコーディングエージェントを利用するということは、それなりに人間の介入が必要とされるタスクを行なっているケースが多いです。

Cursor や Cline では GUI で直感的に作業介入できるので意識することは少ないですが、人間の介入を適切なタイミングでスムーズに行うことは重要なので esc は意識的に使っていくと良いでしょう。

### claude --continue オプションを使う

`/exit` で Claude Code のセッションを抜けたあとも `claude --continue` で前回のセッションに復帰することができます。

公式ドキュメント： [CLIの使用方法とコントロール - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/cli-usage#cli%E3%83%95%E3%83%A9%E3%82%B0)

これは前述の esc による中断と組み合わせると強力です。

1. 人間の介入が必要と判断したタイミングで esc で中断
2. `/exit` でセッションを抜けて人間がファイル編集
3. `claude --continue` でセッションに復帰して再度指示を出す

また、 `claude --resume` を使えば前々回以前のセッションを選択して復帰することも可能です。

### 「PR」が「GitHub Pull Request」のことだと通じる

Claude Code では、プロンプト内で「PR」という略語をそのまま使っても、 **GitHub Pull Request** の意味として正しく解釈してくれます。Anthropic 公式のベストプラクティス（ [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices#:~:text=%E3%83%97%E3%83%AB%20%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E3%81%AE%E4%BD%9C%E6%88%90%3A%20Claude%20%E3%81%AF%E7%9C%81%E7%95%A5%E5%BD%A2%E3%80%8Cpr%E3%80%8D%E3%82%92%E7%90%86%E8%A7%A3%E3%81%97%E3%81%A6%E3%81%8A%E3%82%8A%E3%80%81%E5%B7%AE%E5%88%86%E3%81%A8%E5%91%A8%E5%9B%B2%E3%81%AE%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E3%81%AB%E5%9F%BA%E3%81%A5%E3%81%84%E3%81%A6%E9%81%A9%E5%88%87%E3%81%AA%E3%82%B3%E3%83%9F%E3%83%83%E3%83%88%20%E3%83%A1%E3%83%83%E3%82%BB%E3%83%BC%E3%82%B8%E3%82%92%E7%94%9F%E6%88%90%E3%81%97%E3%81%BE%E3%81%99%E3%80%82) ）でもこの挙動が明示されています。

たとえば、

> 「この修正内容でPRを作っておいて」

と指示すれば、特に補足しなくても GitHub 上で Pull Request を作成する流れとして処理を進めます。

実際の運用でも、細かなプロンプトを毎回書かなくても済むため、作業テンポが上がります。バイブコーディングの中で PR 作成を頻繁に行う場合は、この略語の自然な解釈が地味に便利です。

![]()

### @ / # /! のショートカットを使う

Claude Code には、作業効率を高めるための専用ショートカットがいくつか用意されています。特に利用頻度が高いのが @、#、! です。

- **@（ファイル選択）**  
	@ を入力すると、現在のプロジェクト内のファイル一覧が表示され、参照するファイルを選択できます。Claude Code は選択されたファイル内容を自動的にコンテキストに含めて処理します。ただし現状では **隠しファイル（ドットファイル）が選択肢に出てこない** 制約があります。ここは今後の改善を期待しています。
![]()

- **#（ルール追加）**  
	\# を使うと、その場で Claude Code に新しいルール（いわゆる memory）を追加できます。たとえばプロジェクト固有の制約やコーディング規約を対話中に柔軟に登録できます。ルールはスコープを指定して、プロジェクト単位・グローバル単位で管理することも可能です。
![]()

- **!（Bash コマンド実行）**  
	! を使うと、Claude Code の外部ツール呼び出しを経由せずに、直接ローカルで Bash コマンドを実行できます。たとえば!git status や!ls のように使用します。自明な操作ではLLMの回答を待たなくて良いので、AIの応答待ちを減らせます。
![]()

### \[余談\] まだ使っていないが試してみたいこと

現時点ではまだ本格的に使い込めていませんが、Claude Code には他にも気になっている機能があります。簡単に整理しておきます。

- [**拡張思考 (“think” をプロンプトに含める)**](https://docs.anthropic.com/en/docs/claude-code/tutorials#use-extended-thinking)  
	拡張思考は、Claude が裏側で補助的な思考プロセスを行う仕組みです。  
	複雑なアーキテクチャ上の決定、難しいバグ、または深い推論を必要とする複数ステップの実装を計画する場合に有効です。
- [**カスタムスラッシュコマンド**](https://docs.anthropic.com/en/docs/claude-code/tutorials#create-custom-slash-commands)  
	プロンプトパターンをスクリプト化しておける仕組みです。例えば、GitHub Issue URL を受け取り、対応する PR を作成する流れを定型化して /create-pr のように定義できます。複雑なプロンプトを書き直す手間を減らせるため、バイブコーディングのオペレーションも標準化しやすくなります。公式ドキュメントでもいくつかユースケース例が紹介されていますが、筆者は現段階で特にスクリプト化したい操作がないため使っていません。
- **claude commit**  
	Claude 自体にコミットの作成を任せる機能です。修正内容の要約や適切なコミットメッセージを生成してくれるため、軽微な修正が積み重なる場面では活用できそうです。現時点では処理時間やオーバーヘッドの大きさが気になって積極利用していませんが、将来的に改善されたら積極的に使いたい機能の一つです。
- [**IDE 統合**](https://docs.anthropic.com/en/docs/claude-code/ide-integrations)  
	現時点では主にターミナル上で利用していますが、Claude Code には VSCode などの IDE 連携プラグインも用意されています。コードの一部範囲を直接選択して指示したり、diff を確認しながら操作したりできるため、コーディング補助の柔軟性は高まります。長時間の実装や大規模な修正では、エディタ連携の方が効率が良い場面も出てくるはずです。

## 現状の不満点

もちろん良い面だけではなく、Claude Code にも現時点でいくつか不満に感じている点があります。

- **応答速度が遅い**  
	長めの編集や複数ファイルにまたがる変更を依頼すると、処理に時間がかかることが少なくありません。モデル自体の性能ではなく、Claude Code のエージェント層での処理コストが効いている印象です。 [開発者コミュニティ](https://discord.com/invite/6PPFFzqPDZ) でも改善要望が頻繁に上がっています。
- **@ ショートカットで隠しファイルが取得できない**  
	ファイル選択の際に @ を使うと隠しファイル（例：.env や.github ディレクトリ）が候補に出てきません。dotfiles などを扱う場面では選択できず不便に感じます

今後のアップデートでこれらの課題がどの程度解消されるかに期待しています。

以上が私の Claude Code の設定・利用Tipsです。まだ使い始めて日が浅いので、これからもアップデートがあると思いますが、その際はまたあらためて発信しようと思います。

Wantedly, Inc.では一緒に働く仲間を募集しています

- [バックエンド / リーダー候補](https://www.wantedly.com/projects/1683783)
- [PdM](https://www.wantedly.com/projects/1635459)
- [Webエンジニア](https://www.wantedly.com/projects/1656122)
- [他28件の職種](https://www.wantedly.com/companies/wantedly/post_articles/#)
- 開発
	- [バックエンド / リーダー候補](https://www.wantedly.com/projects/1683783)
	- [Webエンジニア](https://www.wantedly.com/projects/1656122)
	- [バックエンドエンジニア](https://www.wantedly.com/projects/1605014)
	- [フロントエンドエンジニア](https://www.wantedly.com/projects/1159802)
	- [Webフロントエンドリーダー](https://www.wantedly.com/projects/748998)
	- [エンジニアリングマネージャー](https://www.wantedly.com/projects/507567)
	- [Webエンジニア](https://www.wantedly.com/projects/471335)
	- [iOSアプリエンジニア](https://www.wantedly.com/projects/608265)
	- [Androidアプリエンジニア](https://www.wantedly.com/projects/608258)
	- [リーダー候補](https://www.wantedly.com/projects/471101)
	- [インフラエンジニア](https://www.wantedly.com/projects/522096)
	- [データエンジニア](https://www.wantedly.com/projects/2083485)
	- [データサイエンティスト](https://www.wantedly.com/projects/2021130)
	- [コーポレートエンジニア](https://www.wantedly.com/projects/1641675)
	- [機械学習エンジニア](https://www.wantedly.com/projects/391912)
	- [グラフィックデザイナー](https://www.wantedly.com/projects/2058952)
	- [アートディレクター](https://www.wantedly.com/projects/2026027)
- ビジネス
	- [PdM](https://www.wantedly.com/projects/1635459)
	- [人事 労務 制度 評価 採用](https://www.wantedly.com/projects/2079775)
	- [人事 採用 制度 評価](https://www.wantedly.com/projects/2018331)
	- [経営企画 予実管理 管理会計](https://www.wantedly.com/projects/1904584)
	- [採用担当 / 人事](https://www.wantedly.com/projects/1735889)
	- [新規事業の法人営業](https://www.wantedly.com/projects/2081267)
	- [26卒｜ビジネス職（営業）](https://www.wantedly.com/projects/2031469)
	- [エキスパンションマネージャー](https://www.wantedly.com/projects/1996596)
	- [新規営業部門の統括マネージャー](https://www.wantedly.com/projects/1949584)
	- [セールスオープンポジション](https://www.wantedly.com/projects/1461092)
	- [フィールドセールス](https://www.wantedly.com/projects/1398195)
	- [フィールドセールス（新規営業）](https://www.wantedly.com/projects/1393866)
	- [アカウントセールス / CS](https://www.wantedly.com/projects/1337082)
	- [PRでWantedlyを広める](https://www.wantedly.com/projects/1771351)[22 いいね！](https://www.wantedly.com/companies/wantedly/post_articles/#like-user-dialog)

[![](https://images.wantedly.com/i/vssb73w?h=128&w=128)](https://www.wantedly.com/users/143605753)

市古 空さんにいいねを伝えよう

市古 空さんや会社があなたに興味を持つかも

エントリーが完了しました

通信エラーが発生しました。プロフィールページから再設定可能です