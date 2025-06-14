---
Title: "Supabase MCPサーバーが公式から出てたので使ってみよう | DevelopersIO"
Source: "https://dev.classmethod.jp/articles/supabase-mcp-yattaze/"
ClippedDate: 2025-06-10
Tags:
  - "clippings"
---
Supabaseは、オープンソースのバックエンドサービスで、PostgreSQLを基盤とし、以下のような機能を提供することで、アプリケーション開発を簡単にするツールです。

- データベース: PostgreSQLを使用したリレーショナルデータベースで、SQLクエリが利用可能。トリガーやストアドプロシージャなど高度なデータ操作が可能。
- 認証: GoogleやFacebookなどのソーシャルログイン、メール認証、電話認証など多様な方法をサポート。
- リアルタイム機能: データ変更をリアルタイムで通知する機能を備え、チャットアプリやダッシュボードに最適。
- ストレージ: 画像や動画などの大容量ファイルを効率的に保存・管理可能。
- サーバーレス関数（Edge Functions）: カスタムバックエンドロジックを簡単に実装できる。

私自身は個人開発でよく利用しています。便利ですとても。

[そのSupabaseからMCPサーバーが提供されるようになった](https://supabase.com/blog/mcp-server) ので、実際に接続して使ってみます。

## Supabase MCPサーバーを接続

SupabaseはのMCPサーバーはAIツールをSupabaseに接続し、データベースの起動、テーブルの管理、構成の取得、データのクエリなどのタスクを実行できるようにします。

### 設定方法

接続に必要なものとして、

SupabaseのPersonal access token、MCPクライアントの設定ファイル　があります。

1. Personal access token  
	[Supabase settings](https://supabase.com/dashboard/account/tokens) ページで作成します。

![スクリーンショット 2025-04-08 13.48.45](https://devio2024-media.developers.io/image/upload/v1744087743/2025/04/08/tijx9qjedtpx6vju3h4a.png)

Generate new tokenから新規作成します。

1. MCPクライアントの設定ファイル

CursorやClaudeデスクトップなどのMCPクライアントが提供されているツールで、以下のJSONフォーマットで設定ファイルを作成して接続できます。

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": [
        "-y",
        "@supabase/mcp-server-supabase@latest",
        "--access-token",
        "<personal-access-token>"
      ]
    }
  }
}
```

今回はClaudeデスクトップでやってみます。

設定->開発者項目から

![スクリーンショット 2025-04-08 14.02.18](https://devio2024-media.developers.io/image/upload/v1744088546/2025/04/08/pcydqfhrhgjow7czewva.png)

構成を編集　を押すと設定ファイルを選択した状態でフォルダが開きますので、そのファイルを開いて上記の設定を追加します。

設定後、Claudeデスクトップを再起動し、接続が成功していれば `利用可能なMCPツール` が増えていると思います。

![スクリーンショット 2025-04-08 14.09.39](https://devio2024-media.developers.io/image/upload/v1744088987/2025/04/08/ihizk7wapc2mfcqqjiky.png)

![スクリーンショット 2025-04-08 14.09.54](https://devio2024-media.developers.io/image/upload/v1744089002/2025/04/08/cwcdig7jguyugoblmqjn.png)

SupabaseのMCPサーバーで提供されているツールが表示されていますね、

## 使ってみる。

(ツール)\[[https://github.com/supabase-community/supabase-mcp/tree/main?tab=readme-ov-file#tools](https://github.com/supabase-community/supabase-mcp/tree/main?tab=readme-ov-file#tools)\]

SupabaseのMCPサーバーでは、2025.4.8時点で以下のツールが提供されています。

### Project Management

- list\_projects: ユーザのすべてのSupabaseプロジェクトをリストアップする
- get\_project: プロジェクトの詳細を取得する
- create\_project: 新しいSupabaseプロジェクトを作成する
- pause\_project: プロジェクトを一時停止する
- restore\_project: プロジェクトを復元する
- list\_organizations: ユーザーが所属する組織を一覧表示します
- get\_organization: 組織の詳細を取得する

チャットからプロジェクト管理系のツールを動かしてもらってみました。

![スクリーンショット 2025-04-08 14.21.29](https://devio2024-media.developers.io/image/upload/v1744089696/2025/04/08/ewo5ljhlbxuiaua09yhp.png)

![スクリーンショット 2025-04-08 14.24.14](https://devio2024-media.developers.io/image/upload/v1744089861/2025/04/08/zn1cxordj7w87myde18j.png)

プロジェクト作ってと言えば、コストの確認もしてくれましたね、便利。

### Database Operations

- list\_tables: 指定したスキーマ内のすべてのテーブルをリストアップします
- list\_extensions: データベース内のすべての拡張モジュールをリストします
- list\_migrations: データベース内のすべてのマイグレーションを一覧表示します
- apply\_migration: データベースにSQLマイグレーションを適用します。このツールに渡された SQL はデータベース内で追跡されるので、LLM は DDL 操作 (スキーマの変更) にこれを使うべきです
- execute\_sql: データベース内で生のSQLを実行する。LLMはスキーマを変更しない通常のクエリにこれを使うべきです
- get\_logs: Supabase プロジェクトのログをサービスタイプ (api, postgres, edge functions, auth, storage, realtime) ごとに取得します。LLMはこれを使用して、デバッグやサービスパフォーマンスの監視を行うことができます

データベースの操作を行えます。

試しに、株価データを保存するためのテーブルを作ってもらいました。

![スクリーンショット 2025-04-08 14.53.02](https://devio2024-media.developers.io/image/upload/v1744091591/2025/04/08/nzxsalu9tehkqocyld4m.png)

apply\_migrationのツールが実行されて作成されるようですね、

サンプルデータも追加してもらいます。

![スクリーンショット 2025-04-08 14.58.22](https://devio2024-media.developers.io/image/upload/v1744091923/2025/04/08/jrldglqxwsdhht4gpa4h.png)

execute\_sqlのツールが実行されていますね。

### Project Configuration

- get\_project\_url: プロジェクトの API URL を取得します
- get\_anon\_key: プロジェクトの匿名APIキーを取得します

APIのURLを聞いてみました。

![スクリーンショット 2025-04-08 15.24.41](https://devio2024-media.developers.io/image/upload/v1744093489/2025/04/08/is1eodho4juzcefk0din.png)

### Branching (Experimental, requires a paid plan)

- create\_branch: プロダクションブランチからのマイグレーションを含む開発用ブランチを作成します
- list\_branches: すべての開発ブランチを一覧表示します
- delete\_branch: 開発ブランチを削除します
- merge\_branch: 開発ブランチを削除します： マイグレーションとエッジ関数を開発ブランチから本番環境にマージします
- reset\_branch: 開発ブランチのマイグレーションを以前のバージョンにリセットします
- rebase\_branch: マイグレーションドリフトを処理するために開発ブランチを本番環境にリベースします

ブランチ操作ができるようですが、有料プランに加入しないといけませんでした。

![スクリーンショット 2025-04-08 15.29.23](https://devio2024-media.developers.io/image/upload/v1744093771/2025/04/08/ye870vzj4rcaelqmfq8u.png)

コストの確認を行なった後、create\_branchのツールを実行しましたが、私のアカウントがFreeプランのため実行できませんでした。

### Development Tools

- generate\_typescript\_types: データベーススキーマに基づいてTypeScriptの型を生成する。LLMはこれをファイルに保存し、コードで使用することができる

![スクリーンショット 2025-04-08 15.38.11](https://devio2024-media.developers.io/image/upload/v1744094299/2025/04/08/oojikg2ecbbpwuuw9uwq.png)

型定義の生成をgenerate\_typescript\_typesツールによって行われていますね。

Claudeでさらにこの生成した型定義のファイルを使ったサンプルコードの作成も行なってくれています。

### Cost Confirmation

get\_cost: 組織の新規プロジェクトやブランチのコストを取得する  
confirm\_cost: ユーザーが新規プロジェクトやブランチのコストを理解しているか確認します。新規プロジェクトやブランチを作成する際に必要です

作成したプロジェクトにかかっているコストを調べてもらいます。

![スクリーンショット 2025-04-08 15.54.30](https://devio2024-media.developers.io/image/upload/v1744095279/2025/04/08/r8qpmp7wt835jazvs3r9.png)

get\_costツールを実行してコストを返してくれますね。

---

ClaudeデスクトップとSupabaseのMCPサーバーを接続して使用してみました。  
ソフトウェアエンジニアの人たちだとCursorから繋げて、AIアシスタントと対話しながら開発を行うことができるようになるので便利そうですね。

公式ブログの(Whats Next)\[[https://supabase.com/blog/mcp-server#whats-next](https://supabase.com/blog/mcp-server#whats-next)\]によると、  
Edge関数を作成してデプロイする、ネイティブ認証、ファイルストレージの操作もテキストプロンプトから可能になる予定とのことです。

ネイティブ認証は今回使ったPersonal Access tokenを使わず、OAuth2のログインフローを使って認証されるようになるので設定が楽になりそうです。

## 関連記事

[![SupabaseでテーブルデータをCSV Exportする2つの方法](https://images.ctfassets.net/ct0aopd36mqt/wp-thumbnail-378edbf54dfdd4960afac1aa9f4d723f/11b887ce702c6af6c3e22ac8b769d4f3/supabase-1.png)

SupabaseでテーブルデータをCSV Exportする2つの方法

](https://dev.classmethod.jp/articles/supabase-csv-export/)

2025.02.23