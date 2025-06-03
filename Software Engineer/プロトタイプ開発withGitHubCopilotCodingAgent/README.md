# アプリケーションのプロトタイプ開発 (GitHub Copilot Coding Agent)

要求定義の作成をしたドキュメントを入力として、プロトタイプの開発を行っていきます。


# 作戦

ここでは、ユーザーを早期に巻き込むフィードバックループを目的に以下の順番で進めていきます。

# ツール
GitHub Copilotの Coding AgentのIssueからCoding Agentに作業をしてもらう前提です。

https://github.blog/news-insights/product-news/github-copilot-meet-the-new-coding-agent/

現時点でプレビューです。プロダクション環境での利用には十二分に注意をしてください。Pull Requestをマージするかどうかは、**人**の判断ですので!

# UI作成

サンプルデータと、機能要件のドキュメントを添付します。

```cmd
Webアプリケーションのプロトタイプを作成してください。

添付のドキュメントはソフトウェアの機能要件です。この中の{機能要件の中から1つ}だけを作成します。

{機能要件ドキュメント}

# 範囲
- {機能要件の中から1つ}
- 機能ID（Requirement ID）: {あれば}

# 作成場所
- フォルダー: app

# 技術仕様
- HTML5のみ
- JavaScript

# データ
- 以下のファイルを参照する

{サンプルデータやデータモデルのドキュメント

# ノート
- README.mdは、日本語で作成する
```

