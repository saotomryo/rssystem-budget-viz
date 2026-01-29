# 国家版資金丸見えツール

行政事業レビュー（RSシステム）から取得したCSV（特に 2-2「予算種別・歳出予算項目」）を、ブラウザだけで可視化するための静的Webアプリです。  
GitHub Pagesでの公開を前提に、**CSVは利用者が手動アップロード**して閲覧します。

## 公開（GitHub Pages）
1. このリポジトリをGitHubにpush
2. GitHubの `Settings` → `Pages`
3. `Build and deployment` で `Deploy from a branch` を選択
4. `Branch` を `main` / `/ (root)` に設定

公開URLで `index.html` が起動します。

## 使い方
1. 画面上部の `CSVファイル` から、2-2のCSVを選択（手動アップロード）
2. `分類プリセット` を選択（任意）
3. グラフ（年度別積み上げ棒）と「カテゴリ別 上位事業（トップN）」で確認

補足:
- `会計区分` はデフォルトで `一般会計`。必要に応じて `特別会計` や `（両方）` を選択できます。
- 凡例（チェックボックス）でカテゴリの表示/非表示を切り替えできます。
- 「カテゴリ別 上位事業」は `カテゴリ` / `年度` / `上位表示数` を選択できます（`（全て）` あり）。

## 分類プリセット（JSON）
分類は「事業名 → カテゴリ」のJSONで管理します。プリセットはリポジトリ直下のJSONを `index.html` が `fetch` して読み込みます。

例:
```json
{
  "基礎年金給付に必要な経費": "年金"
}
```

現在のプリセット例（`JSON/` 配下）:
- `JSON/ai_classification_map.json`
- `JSON/ai_classification_map_welfare_split.json`
- `JSON/ai_classification_map_3axis.json`
- `JSON/ai_classification_map_demography.json`
- `JSON/ai_classification_map_region.json`
- `JSON/ai_classification_map_industrial_policy.json`
- `JSON/ai_classification_map_security.json`
- `JSON/ai_classification_map_policy_character.json`

追加する場合:
1. `JSON/ai_classification_map_*.json` を追加
2. `index.html` の `PRESET_JSONS` に追記

## データ（CSV）について
- `データ/` は `.gitignore` 対象です（リポジトリにはコミットしません）。
- RSシステムのCSVは年度や更新で事業名が揺れることがあります。
  - 本アプリは **分類JSONに存在しない事業名は無視**します（誤分類を避けるため）。

## ローカルでの確認（Python）
```bash
python -m http.server 8000
```
ブラウザで `http://localhost:8000/` を開きます。

## AIで分類JSONを生成（任意）
画面下部に「AIで分類JSONを生成」を用意しています。
通常は事業数が多いため、分類JSONの生成には時間がかかります（分割サイズやAPIの混雑/制限でさらに遅くなることがあります）。

重要:
- GitHub Pagesのような静的サイトでAPIキーを入力すると、**キーが露出します**（端末・ブラウザ拡張・ネットワークログ等）。
- API提供側の仕様によっては、ブラウザから直接APIを叩けない（CORS等）可能性があります。

安全にやるなら:
- ローカルでのみ実行する
- もしくはサーバー/Worker（Cloudflare Workers等）を挟んでキーを隠す

## ファイル
- `index.html` : GitHub Pages用のエントリ（メイン画面）
- `JSON/ai_classification_map_*.json` : 分類プリセット
- `仕様策定.md` : 仕様メモ
