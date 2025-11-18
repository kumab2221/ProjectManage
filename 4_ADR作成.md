# 4. ADR 作成フェーズ

アーキテクチャ検討結果（01_architecture-evaluation.md）を前提に、
**設計判断を正式に記録する ADR（Architecture Decision Record）** を作成するフェーズ。

ADR は「読みやすく・短く・複数に分割」する原則に従い、
**1 つの明確な設計判断につき 1 つの ADR ファイル** を作成する。

---

## 前提条件

以下のファイルが完成していること：

- ✅ `docs/requirements/02_system-requirements.md`（要件凍結済み）
- ✅ `docs/design/01_architecture-evaluation.md`（アーキテクチャ検討完了）

---

# STEP 1：ADR 作成の準備

## 目的
アーキテクチャ検討結果を読み込み、ADR として記録すべき「決定」を整理する。

## 確認事項

`01_architecture-evaluation.md` の「7. Inputs for ADR」セクションを確認し、
以下の情報が揃っているかをチェックする：

1. **Problem（問題）**
   - どのような要求・制約・前提のもとでアーキテクチャを決める必要があるか

2. **Decision（決定）**
   - 採用するアーキテクチャと、その一文要約

3. **Alternatives（代替案）**
   - 検討した候補と、採用しなかった理由

4. **Rationale（根拠）**
   - なぜこの Decision が最も合理的と判断されたか

5. **Consequences（結果・影響）**
   - ポジティブ / ネガティブ両面の影響

---

# STEP 2：ADR の分割方針を決定

## 目的
アーキテクチャ検討結果から、**何個の ADR に分割するか** を決定する。

## 分割の原則

**1 ADR = 1 つの決定**

以下のような大きな決定は、それぞれ別 ADR に分ける：

- **アプリケーションアーキテクチャ**（例：レイヤードアーキテクチャ採用）
- **UI アーキテクチャ**（例：MVP パターン採用）
- **データストア選択**（例：SQLite 採用）
- **外部連携方式**（例：CSV インポート方式採用）
- **テスト戦略**（例：pytest + モック戦略）

## 分割例

`01_architecture-evaluation.md` の推奨案が以下の場合：

> 「レイヤードアーキテクチャ（4層）を採用し、
> Presentation 層は PySide6 + MVP、
> データストアは SQLite を使用する」

→ 以下の 3 つの ADR に分割：

1. `ADR-001-layered-architecture.md`（レイヤードアーキテクチャ採用）
2. `ADR-002-ui-mvp-pattern.md`（MVP パターン採用）
3. `ADR-003-datastore-sqlite.md`（SQLite 採用）

---

# STEP 3：ADR の生成

## 目的
決定した分割方針に従い、ADR ファイルを生成する。

## 使うコマンド
`.claude/commands/adr-create.md`（または `Commands/adr-create.md` を参照）

## Claude への指示例

### 単一の ADR を作成する場合

```claude
/adr-create

docs/design/01_architecture-evaluation.md を参照し、
「レイヤードアーキテクチャ採用」についての ADR を作成してください。

ADR 番号：001
タイトル：layered-architecture
```

### 複数の ADR を一括作成する場合

```claude
/adr-create

docs/design/01_architecture-evaluation.md を参照し、
以下の決定について ADR を作成してください：

1. レイヤードアーキテクチャ採用（ADR-001-layered-architecture）
2. MVP パターン採用（ADR-002-ui-mvp-pattern）
3. SQLite 採用（ADR-003-datastore-sqlite）

各 ADR は「読みやすく・短く」を守り、
過度に長文化しないようにしてください。
```

## このステップで生成されるファイル

`ADR/ADR-XXX-<slug>.md`

以下の構造で出力される：

```markdown
# ADR-XXX: <短く具体的なタイトル>

## Status
Accepted | Rejected | Deprecated | Superseded

## Context
- プロジェクトや機能の簡潔な説明（2〜3 行）
- 関連する要求・要件（STK / REQ-ID を列挙）
- この決定が必要になった背景（2〜4 行）

## Decision
- 採用したアーキテクチャ / 設計判断を 1〜3 行で要約
- 重要なポイントを箇条書きで 3〜7 個列挙

## Alternatives
- 検討した代替案を 2〜4 個まで列挙
- 各代替案について「一文の説明」と「採用しなかった理由」を書く

## Rationale
- なぜこの Decision が最も妥当と判断されたか
- Quality Attributes（性能、保守性、テスト容易性など）との関係を 3〜6 行で説明

## Consequences
### Positive
- 良い影響を箇条書きで 3〜5 個

### Negative / Risks
- 想定されるデメリット・リスクを 3〜5 個
- 必要であれば、簡単な軽減策も併記してよい

## Links
- 関連ドキュメントへのリンク
  - docs/requirements/02_system-requirements.md
  - docs/design/01_architecture-evaluation.md
  - 関連する他の ADR（あれば）
```

---

# STEP 4：ADR のレビュー

## 目的
生成された ADR の内容を確認し、
記録として適切か、要件との整合性が取れているかを検証する。

## レビューポイント

### 4.1 構造の妥当性
- [ ] **見出し構造（Status / Context / Decision / Alternatives / Rationale / Consequences / Links）が守られているか**
- [ ] 各セクションの文章量が適切か（過度に長文化していないか）
- [ ] 1 つの ADR に複数の大きな決定が詰め込まれていないか

### 4.2 内容の具体性
- [ ] Context が簡潔で分かりやすいか
- [ ] Decision が明確か（曖昧な表現がないか）
- [ ] Alternatives の「採用しなかった理由」が具体的か
- [ ] Rationale が Quality Attributes と結びついているか

### 4.3 要件との整合性
- [ ] `02_system-requirements.md` に存在する要件のみを参照しているか
- [ ] REQ-ID / STK-ID が正確に記載されているか
- [ ] 要件に無い機能を勝手に前提にしていないか

### 4.4 ADR 間の整合性
- [ ] 複数の ADR 間で矛盾がないか
- [ ] 依存関係がある ADR 同士が Links セクションで相互参照されているか

## レビュー後の対応

### 修正が必要な場合

```claude
ADR-001-layered-architecture.md をレビューしました。
以下の点を修正してください：

- Context が長すぎる（2〜3 行に凝縮）
- Rationale が抽象的（Quality Attributes との関係を具体的に）
- Consequences の Negative が楽観的すぎる（リスクを明示）
```

### 問題なければ承認

```claude
ADR-001-layered-architecture.md のレビューが完了しました。
内容は妥当で、設計判断の記録として適切です。

Status を "Accepted" に設定し、次の ADR（または次フェーズ）に進んでください。
```

---

# STEP 5：ADR の承認と管理

## 目的
ADR を正式に承認し、プロジェクトの設計判断として記録する。

## Claude への宣言（必須）

```claude
以下の ADR を承認します：

- ADR-001-layered-architecture.md（Status: Accepted）
- ADR-002-ui-mvp-pattern.md（Status: Accepted）
- ADR-003-datastore-sqlite.md（Status: Accepted）

これらは本プロジェクトの設計判断として記録され、
以降の設計・実装フェーズで遵守されます。

次のフェーズ「5. 設計」に進んでください。
```

## ADR の管理ルール

### ADR の更新・廃止

ADR は一度作成されたら原則として変更しない。
変更が必要な場合は、以下のルールに従う：

1. **既存 ADR を修正してはならない**
   - 新しい ADR を作成し、古い ADR の Status を `Superseded` に変更する
   - Links セクションで相互参照する

2. **ADR の廃止**
   - Status を `Deprecated` に変更する
   - 廃止理由を Context に追記する

3. **ADR の拒否**
   - 検討したが採用しなかった案も ADR として記録してよい
   - Status を `Rejected` に設定する

### ADR 番号の管理

- ADR 番号は **連番** とし、欠番や再利用を禁止する
- 既存 ADR がある場合は、最大番号 + 1 から採番する
- 形式：`ADR-<3桁番号>-<短いスラグ>.md`

---

# 全体フロー（ADR 作成フェーズ）

```
STEP 1：ADR 作成の準備
  ↓（01_architecture-evaluation.md から情報を抽出）
STEP 2：ADR の分割方針を決定
  ↓（1 ADR = 1 つの決定）
STEP 3：ADR の生成（adr-create コマンド）
  ↓（ADR/ADR-XXX-*.md 生成）
STEP 4：ADR のレビュー
  ↓（構造・内容・整合性を確認）
STEP 5：ADR の承認と管理
  ↓（Status: Accepted）
→ 次フェーズ「5. 設計」へ
```

---

## 重要な注意事項

### ⚠️ 1 ADR = 1 つの決定

- **複数の大きな決定を 1 つの ADR に詰め込まない**
- 「DDD 採用」「UI アーキテクチャ」「データストア選択」などは、それぞれ別 ADR に分ける
- 画面スクロールが必要な長大 ADR を 1 つだけ作ることは禁止

### ⚠️ ADR は「決定の記録」

- ADR は「検討中のメモ」ではなく、**正式な決定の記録** である
- 曖昧な部分は ADR 内で「Open Questions」として残さない
- まだ決まっていない論点は、アーキテクチャ検討フェーズに戻って解消する

### ⚠️ 要件との整合性

- `02_system-requirements.md` に存在しない要求・要件を勝手に前提にしない
- 必要なら関連する REQ-ID / STK-ID を明示する
- `01_architecture-evaluation.md` がある場合は、そこに書かれた評価・候補を優先的に参照する

### ⚠️ ADR の変更禁止

- 既存 ADR を直接修正してはならない
- 変更が必要な場合は、新しい ADR を作成し、古い ADR を Superseded にする

---

## 成果物

このフェーズ完了時点で、以下のファイルが完成している：

- ✅ `ADR/ADR-001-*.md`（Status: Accepted）
- ✅ `ADR/ADR-002-*.md`（Status: Accepted）
- ✅ `ADR/ADR-003-*.md`（Status: Accepted）
- ✅ ...（必要に応じて）

これらの ADR は、次フェーズ「5. 設計」で参照され、
設計の一貫性と変更履歴を保証する。
