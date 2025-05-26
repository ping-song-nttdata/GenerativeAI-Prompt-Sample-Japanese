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
{アプリケーション名}を作成してください。

# 仕様
- フォルダー: web
- HTML5
- 一切の通信をしない。ローカルでのみ動作
- データモデルは以下を参照。すべてメモリ上で管理。
```

