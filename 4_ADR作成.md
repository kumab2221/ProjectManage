# 4. ADR 作成フェーズ

`docs/design/01_architecture-evaluation.md` を起点に、**設計判断を正式に記録する ADR（Architecture Decision Record）** を作成し、設計〜実装フェーズへ意思決定を引き継ぐ。

ADR は「短く・読みやすく・比較可能」であることが絶対条件。1 つの明確な決定につき 1 つの ADR を作る。

---

## 前提条件

- `docs/requirements/02_system-requirements.md` が凍結済み
- `docs/design/01_architecture-evaluation.md` が凍結済み（Inputs for ADR が整っている）

---

# STEP 1：ADR Backlog Draft（記録すべき決定の棚卸し）

## 目標
アーキテクチャ評価の「7. Inputs for ADR」から、記録すべき決定候補を列挙し、**1 ADR = 1 決定**の単位まで整理する。

## 手順
1. Architecture Evaluation の推奨案・代替案を読み、Problem / Decision / Alternatives / Rationale / Consequences を抜き出す
2. 同じ決定に属する内容を束ねて 1 行のバックログにまとめる
3. 優先順位（アーキテクチャ → データ → UI → テスト など）を付与し、採番順を仮決定する

## バックログ記述例

| Priority | Decision                               | 関連 ID               | 備考                  |
|----------|----------------------------------------|-----------------------|-----------------------|
| 1        | レイヤードアーキテクチャ採用           | REQ-F-001 / ADR-???   | Presentation = MVP    |
| 2        | SQLite をシングルソースとして採用      | REQ-F-002 / REQ-NF-05 | Migration 必要         |
| 3        | PySide6 + MVP を UI パターンとして採用 | REQ-F-003             | 画面数 5 / Binding 必須 |

---

# STEP 2：ADR スコープの確定と採番

## 目標
バックログをもとに、どの決定をどの ADR に落とし込むかを確定し、ファイル命名と採番ルールを定める。

## ルール
- **1 ADR = 1 決定**。複数の大きな決定を 1 ファイルに詰め込まない
- `ADR/ADR-<3桁番号>-<slug>.md` 形式、既存番号があれば最大値 + 1 から採番
- slug は短く・英小文字・ハイフン区切り（例：`layered-architecture`）

## 例
```
ADR-001-layered-architecture.md  ─ レイヤードアーキテクチャ採用
ADR-002-ui-mvp-pattern.md        ─ MVP パターン採用
ADR-003-datastore-sqlite.md      ─ SQLite 採用
```

---

# STEP 3：ADR Draft 生成（/adr-create）

## 目標
決定ごとに ADR のドラフトを生成する。`Commands/adr-create.md`（`.claude/commands/adr-create.md`）を厳守する。

## 使うコマンド
`.claude/commands/adr-create.md`

## Claude への持ち込み例（単体）

```claude
/adr-create

docs/design/01_architecture-evaluation.md を参照し、
「レイヤードアーキテクチャ採用」について ADR を作成してください。

ADR 番号：001
タイトル：layered-architecture
```

## Claude への持ち込み例（複数まとめて）

```claude
/adr-create

docs/design/01_architecture-evaluation.md を参照し、
以下の決定について ADR を作成してください。

1. レイヤードアーキテクチャ採用      ─ ADR-001-layered-architecture
2. MVP パターン採用                    ─ ADR-002-ui-mvp-pattern
3. SQLite をデータストアとして採用     ─ ADR-003-datastore-sqlite

各 ADR は短く・読みやすく保ち、テンプレートに沿ってください。
```

## Draft の注意
- Context / Rationale は 5〜10 行を上限に凝縮する
- Alternatives / Consequences は箇条書きで読みやすく
- Links には `docs/requirements/02_system-requirements.md` と関連 ADR/設計書を必ず記載する

---

# STEP 4：ADR Review & Refine（レビューと差分吸収）

## 目標
生成された ADR をレビューし、内容・構造・整合性を確認して必要な修正を反映する。

## レビューチェックリスト
- [ ] **構造**：テンプレート（Status / Context / Decision / Alternatives / Rationale / Consequences / Links）が崩れていない
- [ ] **一貫性**：採番・タイトル・Links が他 ADR と重複・欠落していない
- [ ] **要件トレーサビリティ**：Context / Rationale に STK / REQ ID が明記されている
- [ ] **具体性**：Decision と Alternatives が 1〜3 行で比較可能、過剰な散文ではない
- [ ] **リスク**：Negative / Risks に具体的なリスクと軽減策が列挙されている

## 修正依頼テンプレート

```claude
ADR-001-layered-architecture.md をレビューしました。
以下を修正してください。

- Context を 5 行以内に凝縮
- Rationale に Performance / Maintainability への影響を追記
- Consequences の Negative にデータ移行コストを追加
```

## ループ
1. レビューコメントをまとめる
2. `/adr-create` にメモを添えて再実行（もしくは対象 ADR 部分のみ差分を適用）
3. 修正後の ADR を再確認

---

# STEP 5：ADR Freeze（承認・配布）

## 目標
レビュー済み ADR を正式に承認し、設計・実装フェーズへ引き継ぐ。

## 承認テンプレート

```claude
以下の ADR を承認します。

- ADR-001-layered-architecture.md  ─ Status: Accepted
- ADR-002-ui-mvp-pattern.md        ─ Status: Accepted
- ADR-003-datastore-sqlite.md      ─ Status: Accepted

これらは本プロジェクトの設計判断として記録され、
以降の設計・実装フェーズで遵守されます。
```

## 管理ルール
- 既存 ADR の直接修正は禁止。変更時は新しい ADR を作り、旧 ADR を `Superseded` / `Deprecated` にする
- 採番は連番で欠番禁止。既存 ADR がある場合は最大番号 + 1
- まだ決まっていない内容は ADR では扱わず、アーキテクチャ検討へ差し戻す

---

# 全体フロー

```
STEP 1：ADR Backlog Draft
  ↓ Inputs for ADR を棚卸し
STEP 2：スコープ確定・採番
  ↓ 1 ADR = 1 決定 の単位に切り出し
STEP 3：ADR Draft 生成（/adr-create）
  ↓ ADR/ADR-XXX-*.md を生成
STEP 4：レビュー & Refine
  ↓ 差分反映・整合性確認
STEP 5：ADR Freeze（承認）
  ↓ Status: Accepted → 設計フェーズへ
```

---

## 重要な注意事項

### 1 ADR = 1 決定
- 「DDD 採用」「UI アーキテクチャ」「データストア選定」などはそれぞれ別 ADR に分割する
- 長大な 1 ファイルに複数の意思決定を詰め込むことは禁止

### ADR は決定の記録
- 検討メモや Open Questions を ADR に残さない
- まだ決まっていない場合は Architecture Evaluation に戻り、判断材料を再整理する

### 要件との整合
- `02_system-requirements.md` に存在しない要件を勝手に前提にしない
- STK / REQ ID を明記し、トレーサビリティを確保する
- レイヤ構造や依存方向など、Architecture Evaluation の決定と矛盾させない

---

## 成果物

- `ADR/ADR-001-*.md`（Status: Accepted）
- `ADR/ADR-002-*.md`（Status: Accepted）
- `ADR/ADR-003-*.md`（Status: Accepted）
- …プロジェクトで必要な分だけ継続

これらの ADR が、次フェーズ「5. 設計」の入力となる。
