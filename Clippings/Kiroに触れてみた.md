---
Title: "Kiroに触れてみた"
Source: "https://zenn.dev/unipos/articles/250a764e2bc68f"
ClippedDate: 2025-07-25
Tags:
  - "clippings"
---
12[tech](https://zenn.dev/tech-or-idea)

公開の翌日とかくらいに調べて社内向けにラフに書いたものを公開向けに修正したものなので、情報が少し古いかもしれません。

## Kiroについて

> Kiro は単一プロンプトから要件を展開します。「製品のレビューシステムを追加」と入力すると、レビューの表示、作成、フィルタリング、評価というユーザーストーリーが生成されます。各ユーザーストーリーには、開発者が基本的なユーザーストーリーから構築する際に通常処理するエッジケースをカバーする EARS（Easy Approach to Requirements Syntax）記法の受け入れ基準が含まれます。これによりプロンプトの前提が明示的になり、Kiro が求めているものを構築していることがわかります。

EARS記法というものがあるらしい。

確かに "EARS notation" で検索したほうが情報多い。

要求記述のための記法らしいということがわかり、それ以外はあまりわからなかったのでGeminiに調べさせた。

ナナメよみした結果、ざっくり以下の記法パターンがあることがわかった。

| パターン名 | キーワード | 構文 | 目的と使用場面 | 典型的な例 |
| --- | --- | --- | --- | --- |
| **遍在的要件** (Ubiquitous) | なし | The <システム名> shall <応答>. | システムが常に満たすべき普遍的な特性、制約、品質を記述する。 | The system shall comply with standard XYZ. |
| **状態駆動要件** (State-Driven) | While | While <状態>, the <システム名> shall <応答>. | システムが特定の状態にある間、継続的に実行すべき振る舞いを記述する。 | While in standby mode, the system shall monitor for incoming commands. |
| **イベント駆動要件** (Event-Driven) | When | When <トリガー>, the <システム名> shall <応答>. | 特定のイベントが発生した瞬間に、システムが実行すべき応答を記述する。 | When the user clicks 'Save', the system shall store the data. |
| **オプション機能要件** (Optional Feature) | Where | Where <機能>, the <システム名> shall <応答>. | 特定の機能やコンポーネントがシステムに存在する場合にのみ適用される要求を記述する。 | Where the system has a GPS module, the system shall provide location services. |
| **非推奨挙動要件** (Unwanted Behaviour) | If...Then | If <望ましくないトリガー>, then the <システム名> shall <応答>. | エラーや障害など、望ましくない状況に対する例外処理や安全策を記述する。 | If the sensor fails, then the system shall enter a safe mode. |
| **複合要件** (Complex) | 複数のキーワード | While <状態>, When <トリガー>, the <システム名> shall <応答>. | 複数の条件（状態、イベント、オプション等）が組み合わさった、より複雑な振る舞いを記述する。 | While the system is armed, when motion is detected, the system shall sound the alarm. |

ユーザーストーリーとどう関連するかとか、BDDで使うGerkhin記法とかと近いなとか考えていたけど、用途はちょっと違うよね。というまとめかたをされていた。

| 項目 | ユーザーストーリー (User Story) | EARS (Easy Approach to Requirements Syntax) | ガーキン (Gherkin / Given-When-Then) |
| --- | --- | --- | --- |
| **主な目的** | ユーザーの視点から機能の **価値** と **意図** を伝える。対話のきっかけ。 | システムが満たすべき **要求** を、曖昧さなく、一貫性・検証可能性を持って記述する。 | システムの具体的な **振る舞い** を、テスト可能なシナリオとして記述する。 |
| **典型的な形式** | As a <役割>, I want <目標>, so that <便益>. | While/When/If..., the <システム> shall <応答>. | Given <前提>, When <アクション>, Then <結果>. |
| **長所** | 価値に焦点を当てる、非技術者にも分かりやすい、柔軟性が高い | 曖昧さ・多義性の排除、一貫性と検証可能性の向上、軽量で習得が容易 | 実行可能な仕様（自動テスト）、具体的なシナリオベース、開発者とテスターの共通理解 |
| **短所** | それ自体では仕様として不十分、詳細が欠落しがち、解釈がばらつく可能性がある | 非常に複雑な条件の記述には不向き、創造性をやや制約する可能性がある | 全ての要求をシナリオで書くのは冗長、要求の「ルール」そのものは表現しない |
| **最適な使用フェーズ** | プロダクトバックログの作成、イテレーション計画（ **What/Why** の定義） | 要求仕様書（SRS）の作成、ユーザーストーリーの **受け入れ基準** の定義 | テスト仕様の作成、 **ビヘイビア駆動開発（BDD）** におけるテストケースの記述 |

## 使ってみる

いまはWaitlistができててダウンロードできないらしいけど、試したタイミングではHomebrewでインストールできた。

```bash
brew install kiro
```

## 仕様駆動開発

要件定義→設計→タスクの書き出し→実装 という（コーディングエージェントに仕事をさせるにあたっての黄金パターンとされてるであろう）フローがアプリケーションの機能として実装されている。

![](https://storage.googleapis.com/zenn-user-upload/83b50d154478-20250724.png)

対話しながら、設計の段階で要件の変更を含む修正指示すると要件定義書までさかのぼって修正→新要件に合致するように設計を修正 みたいにやってくれる。

![](https://storage.googleapis.com/zenn-user-upload/ac3cd1106986-20250724.png)

タスクの生成が完了すると、それぞれのタスクの上にボタン（ `Start task` てやつ）が生えてて、それをポチするとタスクをはじめる。

![](https://storage.googleapis.com/zenn-user-upload/7205b0c1e166-20250724.png)

ここでは英語を話しはじめるので、日本語にしたいならなんかKiroへのグローバル指示みたいなの与える必要があるんだろうな。（調べてはない）

現状はモデルへのリクエストがちょこちょこ失敗する。無料でSonnet 4は太っ腹だと思うなのでやむなしか。  
![](https://storage.googleapis.com/zenn-user-upload/1af2cefadcb3-20250724.png)

タスク出しまでKiroにやらせてClaude Codeにまかせるみたいなことはできそう [^1] 。

## その他

### コマンドの信頼設定

どのレベルで許可するか？結構細かく選べる。  
![](https://storage.googleapis.com/zenn-user-upload/38341097f15d-20250724.png)

チャット欄に渡せるContext集に最初から一部のライブラリdocがいる。もろもろ設定しなくてもこういうことができるように設定されてるのは便利かもしれない。Amazon製だけあってAWS関連のものが多い。  
![](https://storage.googleapis.com/zenn-user-upload/f187ba342145-20250724.png)

## 所感

現状のコーディングエージェントのよい活用方法とされているものを仕組みで強制させるのははじめての一歩に使うものとしてはよいのかもと感じた。が、やりたいことの雑な表現とかからカッチリした要求定義作って、設計出させて、みたいなフロー自体はほかのコーディングエージェントでも作れそうだと思う。

EARS記法とかに限らずに、要求定義や設計に必要な道具・知識をたくさん持たせたうえで要求定義・仕様策定からコーディングエージェントにどんどんやらせていく、みたいなプロダクト開発の世界観はありそうだなと思った。

脚注

12

[^1]: 実際、Zennへの投稿までにいろいろ手法が出てきましたね