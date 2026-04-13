# Trello連携ガイド（proposal-strategy）

このガイドは、`proposal-strategy` で作成した提案戦略を Trello で実行管理するための実務手順です。

## 1. 連携の前提

### 必要な情報
- Trello API Key
- Trello API Token
- Board ID
- List ID（例: `To Do`, `In Progress`）

> API Key / Token は Trello の開発者ページから取得します。機密情報はリポジトリに保存せず、環境変数で管理してください。

### 推奨環境変数
```bash
export TRELLO_API_KEY="xxxxx"
export TRELLO_API_TOKEN="xxxxx"
export TRELLO_BOARD_ID="xxxxx"
export TRELLO_LIST_ID_TODO="xxxxx"
```

## 2. 提案戦略→Trelloカードの変換ルール

`templates/strategy_output.md` の章立てを以下のようにマッピングします。

- **提案シナリオ（案A/B/C）**  
  - カード種別: `Scenario`
  - 例: `【Scenario A】短期成果重視シナリオ`
- **フェーズ分割**  
  - カード種別: `Phase`
  - 例: `【Phase 1】現状可視化と課題確定`
- **主要リスク**  
  - カード種別: `Risk`
  - 例: `【Risk:高】業務部門の稼働逼迫で定着が遅延`
- **確認論点**  
  - カード種別: `Question`
  - 例: `【確認】KPIの最終責任者の確定`

### カード説明（Description）推奨フォーマット
```md
## 背景
（要約）

## 目的 / 期待効果
（要約）

## 完了条件
- [ ] 条件1
- [ ] 条件2

## 関連スコープ
- 対象部署:
- 対象業務:
- 対象システム:
```

## 3. APIでカードを作成する

### 単体カード作成例
```bash
curl -X POST "https://api.trello.com/1/cards" \
  --data-urlencode "idList=${TRELLO_LIST_ID_TODO}" \
  --data-urlencode "name=【Phase 1】現状可視化と課題確定" \
  --data-urlencode "desc=提案戦略で定義したPhase 1の実行管理カード" \
  --data-urlencode "key=${TRELLO_API_KEY}" \
  --data-urlencode "token=${TRELLO_API_TOKEN}"
```

### 複数カード作成の運用メモ
- シナリオ比較用カードを先に作る
- 推奨シナリオに紐づくフェーズカードを作る
- リスクカードはラベル `risk-high` / `risk-mid` / `risk-low` を付与
- 確認論点は期限を短く設定し、初期フェーズで解消する

## 4. 運用ルール（推奨）
- 1週間単位でカードレビューし、提案時の仮説との差分を更新する
- 顧客と合意した内容のみ `Definition of Done` を更新する
- 非スコープ事項は別リスト（例: `Out of Scope`）で可視化する

## 5. セキュリティ注意
- API Token を提案資料や議事録に貼らない
- 共有端末では環境変数をセッション終了時に削除する
- 必要であれば Trello の権限を最小化し、Board単位で制御する
