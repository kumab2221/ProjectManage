# 実装計画・プラン作成コマンド

設計書と ADR をもとに、**実装可能な粒度のタスク一覧とスケジュール**を生成する。プランは「1 機能 = 1 ファイル」の原則で作成し、Implementation フェーズの行動計画として機能させる。

---

## 目標

- `docs/design/*.md` と `ADR/*.md` に沿ったタスクと依存関係を構造化する
- タスクを **1〜1.5 時間粒度** で分割し、実装とテストの進行を制御できるようにする
- リスク・受入条件・成果物を明確にして手戻りを防止する

---

## 入力

- `docs/requirements/02_system-requirements.md`
- `docs/design/*.md`（UI / Domain / Infrastructure / Interface / Module など）
- `ADR/*.md`（Status: Accepted）
- 必要に応じてユーザーからの補足（対象機能名、優先度、関連設計書など）

入力例：

```
対象機能：CSV インポート
優先度：MUST
関連設計書：
- docs/design/02_domain-design.md
- docs/design/03_infrastructure-design.md
- docs/design/04_interface-design.md
```

---

## 出力ポリシー

1. **1 プラン = 1 機能**。機能が大きい場合はサブ機能ごとに別ファイルに分割する。
2. 実装に迷いが出ないよう、背景から受入条件までをテンプレートで固定する。
3. タスクは 1〜1.5 時間粒度で記述し、依存関係と成果物を必ず明記する。

---

## ファイル命名規則

- 保存パス：`plans/`
- 形式：`feature-<機能名>.md`（英小文字＋ハイフン）
  - 例：`feature-csv-import.md`, `feature-chart-display.md`
- 既存プランと重複する場合はバージョン付き（例：`feature-csv-import-v2.md`）

---

## プランテンプレート（固定）

**見出し構造は絶対に変更しない。**

```markdown
# Feature Plan: <機能名>

## 1. Background（背景）
### 1.1 概要
### 1.2 関連ドキュメント

## 2. Objective（目標）

## 3. Scope
### 3.1 In Scope
### 3.2 Out of Scope

## 4. Tasks
### 4.1 基盤タスク（Infrastructure / Domain）
### 4.2 アプリケーションタスク（Application）
### 4.3 プレゼンテーションタスク（Presentation）
### 4.4 テストタスク（Testing）

## 5. Estimation
### 5.1 タスク見積りサマリ
### 5.2 スケジュール案

## 6. Dependencies
### 6.1 環境依存
### 6.2 機能依存

## 7. Risks
### 7.1 技術リスク
### 7.2 組織 / 運用リスク

## 8. Deliverables
### 8.1 実装成果物
### 8.2 テスト成果物
### 8.3 ドキュメント

## 9. Acceptance Criteria

## 10. Notes
```

---

## タスク記述フォーマット

- **タスク ID**：`TASK-<機能略称>-<連番>`（例：`TASK-CSV-001`）
- **タスク名**：具体的な作業内容（例：「CSV パーサークラス実装」）
- **説明**：詳細（1〜2 行）。入力・出力・例外など
- **レイヤ**：Infrastructure / Domain / Application / Presentation / Testing
- **見積り**：0.5h, 1h, 1.5h など。2h を超える場合は分割必須
- **依存**：先行タスク ID。複数ある場合はカンマ区切り
- **成果物**：生成・更新されるファイルパス

例（4.1 基盤タスク）：

| ID           | タスク名                   | 説明                                           | レイヤ        | 見積り | 依存           | 成果物                                  |
|--------------|----------------------------|-----------------------------------------------|---------------|--------|----------------|-----------------------------------------|
| TASK-CSV-001 | CSV パーサーインターフェース定義 | CSV ファイル読み込みの抽象 I/F を定義する        | Domain        | 0.5h   | -              | `src/domain/interfaces/csv_reader.py`   |
| TASK-CSV-002 | CSV パーサー実装           | pandas を使って CSV をパースし、DTO を返す       | Infrastructure| 1.5h   | TASK-CSV-001   | `src/infrastructure/csv_parser.py`      |

各レイヤ（Application / Presentation / Testing）でも同じ形式の表を用意する。

---

## Estimation / Dependencies / Risks / Deliverables / Acceptance

- **Estimation**：タスク件数と総工数をレイヤ別に集計し、Day 単位のスケジュール案を提示する
- **Dependencies**：環境依存（ライブラリ、ツール、環境構築）と機能依存（他プラン、他機能）を区別して列挙
- **Risks**：Impact / Probability / Mitigation を併記した表を 7.1 / 7.2 で記述
- **Deliverables**：実装ファイル、テストファイル、ドキュメントなどをリスト化
- **Acceptance Criteria**：チェックリスト形式で検証可能な条件を 4〜6 項目並べる（例：テストカバレッジ 80%、UI でキャッチできるエラー表示、など）

---

## コマンド実行例

### 単一プラン

```claude
/create-plan

docs/design/02_domain-design.md
docs/design/03_infrastructure-design.md
docs/design/04_interface-design.md
ADR ディレクトリ内の全 ADR

対象機能：CSV インポート
優先度：MUST
出力：plans/feature-csv-import.md
```

### 複数プラン

```claude
/create-plan

docs/design/*.md と ADR を参照し、以下のプランを作成してください。

1. CSV インポート ─ plans/feature-csv-import.md
2. チャート表示     ─ plans/feature-chart-display.md
3. 設定管理         ─ plans/feature-settings-management.md

各プランは 1〜1.5h 粒度のタスクと依存関係を含めてください。
```

---

## レビューと完了条件

### レビューチェックリスト

- [ ] 1 プラン = 1 機能 が守られている
- [ ] タスク ID / レイヤ / 見積り / 依存 / 成果物がすべて埋まっている
- [ ] タスク粒度が 1〜1.5h 以内（超えていたら分割されている）
- [ ] リスクに Impact / Probability / Mitigation が記載されている
- [ ] 受入条件がチェックリスト形式で検証可能
- [ ] 設計書 / ADR に存在しない要素を前提にしていない

### 完了宣言テンプレート

```claude
以下のプランを凍結します。

- plans/feature-csv-import.md
- plans/feature-chart-display.md

各プランは 1〜1.5h 粒度のタスク、依存関係、リスク、受入条件を満たしています。
以降、このプランを前提に実装フェーズへ進んでください。
```

---

## 禁止事項

- 複数の大きな機能を 1 つのプランに詰め込むこと
- テンプレート構造（10 セクション）を削除・並び替えすること
- 2 時間を超えるタスクを分割せずに記載すること
- 設計書や ADR に存在しないクラス・API を勝手に追加すること
- 依存関係やリスクを曖昧な文章のままにすること

---

## ベストプラクティス

### レイヤ分解

1. **Domain / Infrastructure**：インターフェース定義、永続化、外部連携
2. **Application**：ユースケース / サービス / バリデーション
3. **Presentation**：画面 / ViewModel / コントローラ
4. **Testing**：単体 / 統合 / E2E

### 見積りの目安

- インターフェース定義：0.5〜1h
- クラス実装（小）：1h、（中）：1.5h
- UI 画面 1 枚：1〜1.5h（View + ViewModel）
- 単体テスト：0.5〜1h、統合テスト：1h
- 2h を超えるタスクは分割する

### リスク / 受入条件の書き方

- リスクは Impact（影響）、Probability（発生確率）、Mitigation（軽減策）をセットで記載
- 受入条件は「誰が・何を・どの基準で確認するか」を含めたチェックリスト形式

---

## 参考

- `Commands/adr-create.md` と同様に **短く・読みやすい構造**を守る
- 迷った場合は `docs/design/01_architecture-evaluation.md` や ADR の Links を参照し、要件とのトレーサビリティを担保する
