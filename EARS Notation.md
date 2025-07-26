---
tags:
  - Spec-Driven-Development
---
> Source: [[Kiroに触れてみた]]

ざっくり以下の記法パターンがあることがわかった。

| パターン名                            | キーワード     | 構文                                               | 目的と使用場面                                       | 典型的な例                                                                                 |
| -------------------------------- | --------- | ------------------------------------------------ | --------------------------------------------- | ------------------------------------------------------------------------------------- |
| **遍在的要件** (Ubiquitous)           | なし        | The <システム名> shall <応答>.                          | システムが常に満たすべき普遍的な特性、制約、品質を記述する。                | The system shall comply with standard XYZ.                                            |
| **状態駆動要件** (State-Driven)        | While     | While <状態>, the <システム名> shall <応答>.              | システムが特定の状態にある間、継続的に実行すべき振る舞いを記述する。            | While in standby mode, the system shall monitor for incoming commands.                |
| **イベント駆動要件** (Event-Driven)      | When      | When <トリガー>, the <システム名> shall <応答>.             | 特定のイベントが発生した瞬間に、システムが実行すべき応答を記述する。            | When the user clicks 'Save', the system shall store the data.                         |
| **オプション機能要件** (Optional Feature) | Where     | Where <機能>, the <システム名> shall <応答>.              | 特定の機能やコンポーネントがシステムに存在する場合にのみ適用される要求を記述する。     | Where the system has a GPS module, the system shall provide location services.        |
| **非推奨挙動要件** (Unwanted Behaviour) | If...Then | If <望ましくないトリガー>, then the <システム名> shall <応答>.    | エラーや障害など、望ましくない状況に対する例外処理や安全策を記述する。           | If the sensor fails, then the system shall enter a safe mode.                         |
| **複合要件** (Complex)               | 複数のキーワード  | While <状態>, When <トリガー>, the <システム名> shall <応答>. | 複数の条件（状態、イベント、オプション等）が組み合わさった、より複雑な振る舞いを記述する。 | While the system is armed, when motion is detected, the system shall sound the alarm. |

ユーザーストーリーとどう関連するかとか、BDDで使うGerkhin記法とかと近いなとか考えていたけど、用途はちょっと違うよね。というまとめかたをされていた。

| 項目 | ユーザーストーリー (User Story) | EARS (Easy Approach to Requirements Syntax) | ガーキン (Gherkin / Given-When-Then) |
| --- | --- | --- | --- |
| **主な目的** | ユーザーの視点から機能の **価値** と **意図** を伝える。対話のきっかけ。 | システムが満たすべき **要求** を、曖昧さなく、一貫性・検証可能性を持って記述する。 | システムの具体的な **振る舞い** を、テスト可能なシナリオとして記述する。 |
| **典型的な形式** | As a <役割>, I want <目標>, so that <便益>. | While/When/If..., the <システム> shall <応答>. | Given <前提>, When <アクション>, Then <結果>. |
| **長所** | 価値に焦点を当てる、非技術者にも分かりやすい、柔軟性が高い | 曖昧さ・多義性の排除、一貫性と検証可能性の向上、軽量で習得が容易 | 実行可能な仕様（自動テスト）、具体的なシナリオベース、開発者とテスターの共通理解 |
| **短所** | それ自体では仕様として不十分、詳細が欠落しがち、解釈がばらつく可能性がある | 非常に複雑な条件の記述には不向き、創造性をやや制約する可能性がある | 全ての要求をシナリオで書くのは冗長、要求の「ルール」そのものは表現しない |
| **最適な使用フェーズ** | プロダクトバックログの作成、イテレーション計画（ **What/Why** の定義） | 要求仕様書（SRS）の作成、ユーザーストーリーの **受け入れ基準** の定義 | テスト仕様の作成、 **ビヘイビア駆動開発（BDD）** におけるテストケースの記述 |