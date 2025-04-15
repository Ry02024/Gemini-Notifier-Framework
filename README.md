# 🧠 Gemini Notifier Framework

## 📌 概要

このプロジェクトは、Google Gemini API を用いて、Web 上の情報を収集・要約し、通知媒体（LINE, Slackなど）へ自動投稿するフレームワークです。
プロンプトファイルや設定値を切り替えることで、**求人情報、展示情報、ニュース要約、知識提供などさまざまな分野に応用可能**です。

## 🔧 ディレクトリ構成

```
project-root/
├── config/
│   ├── summary_prompt.txt        # 要約投稿用プロンプト（必須）
│   └── detail_prompt.txt         # 詳細投稿用プロンプト（任意）
├── data/
│   ├── summary_result.txt        # Geminiの直近summary出力
│   └── archive.json              # summaryのアーカイブ（日付キー）
├── src/
│   ├── main.py                   # CLI引数に応じた制御
│   ├── summary.py                # summaryモジュール（検索 + 要約 + 保存 + 通知）
│   └── detail.py                 # detailモジュール（深掘り + 要約 + 通知）
├── .github/workflows/
│   ├── summary.yml               # GitHub Actions - 毎朝 summary 実行
│   └── detail.yml                # GitHub Actions - detailを毎時実行
├── .env                          # 環境変数定義（GitHub Secretsに対応）
└── README.md
```

## 🧠 使用方法

### 1. 環境変数の設定
`.env` ファイル または GitHub Secrets で以下を定義します：
```
GEMINI_API_KEY=your_gemini_api_key
LINE_CHANNEL_ACCESS_TOKEN=...
LINE_GROUP_ID=...
SLACK_BOT_TOKEN=...
SLACK_CHANNEL_ID=...
```

### 2. 実行方法
```bash
python src/main.py --mode summary     # summary取得・通知・保存
python src/main.py --mode detail      # 要約済みの中から1件を選び詳細通知
```

## 🔁 GitHub Actions

### summary.yml
```yaml
on:
  schedule:
    - cron: '0 0 * * *'  # 毎日9時JST実行
  workflow_dispatch:
jobs:
  summary:
    ...
    run: python src/main.py --mode summary
```

### detail.yml
```yaml
on:
  schedule:
    - cron: '0 1-5 * * *'  # 毎時10〜14時JSTに実行
  workflow_dispatch:
jobs:
  detail:
    ...
    run: python src/main.py --mode detail
```

## 💡 プロンプト構成（config 配下）

- `summary_prompt.txt`: 必須。検索対象・取得内容を定義します。
- `detail_prompt.txt`: 任意。summary取得結果のうち1件を深掘りする指示を定義。

例：
- 美術館 summary → 今日の無料で障がい者が観覧できる東京の展示を5件
- 美術館 detail → 1件の展示を選び、その魅力を200字で説明
- 求人 summary → 今日の障がい者向けデータサイエンス系求人を5件要約
- 求人 detail → 企業概要と募集背景を200字でまとめて

## 💾 保存機能について

- `summary_result.txt`：最新のsummary結果（LINEやSlack送信用）を保存
- `archive.json`：時系列で要約結果を自動アーカイブ

## 📚 応用例

### ✅ 求人通知ボット
- `summary_prompt.txt`: 求人一覧の収集
- `detail_prompt.txt`: 採用企業の社会的背景を解説
- 通知先: Slack

### ✅ 美術館展示ガイド
- `summary_prompt.txt`: 無料展示のリスト
- `detail_prompt.txt`: 展示の特徴をわかりやすく要約
- 通知先: LINE

### ✅ ニュース要約・学習支援
- 任意のトピックで時事要約
- 量子コンピュータなど、日替わり知識ボットにも応用可能

## 🌍 このアプリの意義

本フレームワークは、**毎日自動で情報を収集・要約・通知し、生活者の「知る」体験を拡張するパーソナルAIパートナー**です。

### 特徴：
- 情報収集→要約→通知→保存の一連フローを自動化
- プロンプト切替でジャンル横断的に再利用可能
- JSONアーカイブによる振り返りや時系列分析にも活用可能

### 想定ユーザー：
- 福祉領域：視覚的に情報取得が難しい方への情報支援
- 教育機関：自動ニュース要約、テーマ別学習補助
- 企業内ナレッジ収集：特定分野の動向収集と要約

---

## 🔧 今後の拡張案
- 多言語対応プロンプト
- 分野別分類 + トピック傾向のクラスタリング
- 要約の品質フィードバック（Good/Bad）による強化学習的な改善

ぜひ fork して、あなたの分野で活用してください！

