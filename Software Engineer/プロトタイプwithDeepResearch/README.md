# アプリケーションのプロトタイプ開発 (GitHub Copilot Coding Agent / GitHub Copilot for Azure)

要求定義の作成をしたドキュメントを入力データとして、プロトタイプの開発を行っていきます。

# プロトタイプの位置づけ

ここでは、ユーザーを早期に巻き込むフィードバックループを目的に以下の順番で進めていきます。

# ツール
GitHub Copilot の **Coding Agent**のIssueからCoding Agentに作業をしてもらう前提です。

https://github.blog/news-insights/product-news/github-copilot-meet-the-new-coding-agent/

## 必読ドキュメント!!!

Copilot を使用してタスクに取り組むためのベスト プラクティス

https://docs.github.com/ja/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks


- 現時点でプレビューです。プロダクション環境での利用には十二分に注意をしてください。Pull Requestをマージするかどうかは、**人**の判断ですので!
- AzureとCDで連携した後は、CopilotがPRを実行している際にGitHub Actionsでのジョブ実行を行います。その際に私たち人によるApprovalが必要です。ただ、そのGitHub Actionsのジョブがエラーになることがあります。

![Job Errorの例](/Software%20Engineer/プロトタイプwithDeepResearch//images/image.png)

- その際には、PRのコメントに以下の様な指示をして、GitHub Copilotに修正をさせるのも手です。

```cmd
reviewのチェックで、github actionsのジョブがエラーになりました。原因をリストアップして、解決策を考えて、修正をしてください。
@copilot 
```


Microsoft Azure のSDKを使う場合は、GitHub Copilot for Azure を使います。

https://learn.microsoft.com/ja-jp/azure/developer/github-copilot-azure/introduction


# 0. 業務要件などビジネス面のドキュメントの整備

こちらのドキュメントを参考にしてください。

ここでおススメのツールは上記とは**別**で、Microsoft 365 Copilot Researcherです。

[要求定義の作成](/Software%20Engineer/プロトタイプwithDeepResearch/要求定義作成.md)

# 1. GitHubのRepositoryの作成

GitHubのRepositoryを作成します。GitHub Copilot Coding Agentが作業をするためのリポジトリーです。


# 2. Custom Instructionsの作成

GitHubのRepositoryに、GitHub Copilot Coding Agentがより正確にタスクを実行できるようなcopilot-instructions.mdファイルを作成します。

以下、例、です。

Microsoft DocsとAzureのMCP Serverの指定がある点に注意してください。不要な場合は削除してください。

```cmd
## Microsoftの公式ドキュメントの検索

あなたは **MicrosoftDoc** という MCP サーバーにアクセスすることができます。このツールを使用することで、Microsoft の最新の公式ドキュメントを検索することが可能であり、その情報は、あなたの学習データセットに含まれている内容よりも詳細であったり、新しいものである可能性があります。

Microsoft Azure、C#、F#、ASP.NET、Microsoft.Extensions、NuGet、など、Microsoft のネイティブ技術の取り扱いに関する質問に対応する際には、特に具体的または限定的な内容に関する場合、このツールを調査目的で使用するようにしてください。

## Microsoft Azureのリソースの検索

あなたは **Azure** という MCP サーバーにアクセスすることができます。このツールを使用することで、Azure Model Context Protocol (MCP) サーバーには、自然言語プロンプトを使用して Azure サービスを操作するために既存のクライアントから使用できる多くのツールが公開されています。 たとえば、Azure MCP サーバーを使用して、Visual Studio Code の GitHub Copilot エージェント モードまたは次のようなコマンドを使用して他の AI エージェントから Azure リソースと対話できます。

- "すべてのリソース グループを表示する"
- "'documents' という名前のストレージ コンテナー内の BLOB を一覧表示する"
- "アプリ構成の 'ConnectionString' キーの値は何ですか?
- "過去 1 時間のエラーをログ分析ワークスペースに照会する"
- "すべての Cosmos DB データベースを表示する"


## リポジトリ構成

- `internal/`: 他の GitHub サービスとの連携に関するロジックを含みます。  
- `lib/`: 主要なライブラリパッケージを格納します。  
- `admin/`: 管理者向けインターフェースのコンポーネントを含みます。  
- `config/`: 設定ファイルおよびテンプレートを格納します。  
- `docs/`: ドキュメントを格納します。  
- `test/`: テスト用のヘルパーおよびフィクスチャを格納します。  
- `labs/`: 実験的なコードやプロトタイプを格納します。
- `app/`: Webアプリケーションのコードを格納します。
- `api/`: apiのコードを格納します。
- `infra/`: インフラストラクチャのコードを格納します。

## アーキテクチャとデザイン原則

### マイクロサービスアーキテクチャ
- すべての新しいアプリケーションとAPIは、マイクロサービスパターンで設計してください
- 各サービスは疎結合で、独立してデプロイ可能であることを確保してください
- サービス間の通信はHTTP REST API、メッセージキュー、またはイベントドリブンパターンを使用してください

### フォルダ構造とプロジェクト構成
- アプリケーションとAPIは専用のフォルダに分離して作成してください
- 各プロジェクトのルートには適切なREADME.mdファイルを含めてください
- Infrastructure as Code (IaC) ファイルは `infra/` フォルダに配置してください
- 設定ファイルやドキュメントは適切なサブフォルダに整理してください

## Microsoft Azure開発ガイドライン

### Azure サービスの仕様参照
Microsoft Azureの各サービスの仕様や最新情報は、以下の公式ドキュメントから必ず参照してください：
- Azure ドキュメント: https://learn.microsoft.com/ja-jp/azure/?product=popular

### 認証とセキュリティ
- **絶対に資格情報をハードコーディングしないでください**
- Azure Key Vaultを使用して機密情報を管理してください
- 可能な限りマネージド アイデンティティを使用してください
- 最小権限の原則に従ってRBACを設定してください
- データの暗号化と安全な接続を有効にしてください

### インフラストラクチャ as コード
- Azure Bicepを優先してIaCファイルを作成してください
- Terraformを使用する場合は、Azure用のベストプラクティスに従ってください
- すべてのリソース名にresource tokenを使用してください
- タグ付けとリソースの整理を適切に行ってください

### エラーハンドリングと信頼性
- 一時的な障害に対して指数バックオフでリトライロジックを実装してください
- 適切なログ記録と監視を追加してください
- 必要に応じてサーキット ブレーカー パターンを含めてください
- リソースの適切なクリーンアップを確保してください

### パフォーマンスとスケーリング
- データベース接続プーリングを使用してください
- 同時操作とタイムアウトを設定してください
- 戦略的にキャッシュを実装してください
- リソース使用量を監視してください
- バッチ操作を最適化してください

## コーディング標準

### 言語固有のベストプラクティス
各プログラミング言語のベストプラクティスに必ず従ってください：

#### Python
- PEP 8 スタイルガイドに従ってください
- 型ヒント（Type Hints）を使用してください
- docstringsで関数とクラスを文書化してください
- pytest を使用してテストを記述してください

#### JavaScript/TypeScript
- ESLint とPrettierを使用してコードの品質を保ってください
- TypeScriptを優先して使用してください
- 適切なエラー処理とログ記録を実装してください
- Jest または Vitest を使用してテストを記述してください

#### C#/.NET
- .NET のコーディング規約に従ってください
- nullable reference types を有効にしてください
- ConfigureAwaitを適切に使用してください
- xUnit を使用してテストを記述してください

### ドキュメント
- すべてのプロジェクトに包括的なREADME.mdを含めてください
- APIドキュメントを自動生成してください（OpenAPI/Swagger）
- インラインコメントで複雑なロジックを説明してください
- アーキテクチャの決定事項を記録してください

## AI とプロンプトエンジニアリング

### プロンプト設計
- 明確で具体的な指示を提供してください
- コンテキストと例を含めてください
- 段階的な思考プロセスを促進してください
- 出力形式を明確に指定してください

### セキュリティ考慮事項
- プロンプトインジェクション攻撃を防ぐための対策を実装してください
- 機密情報の漏洩を防ぐためのガードレールを設定してください
- ユーザー入力の適切な検証とサニタイゼーションを行ってください

## デプロイメントとCI/CD

### Azure Developer CLI (azd)
- 可能な限り `azd` コマンドを使用してください
- `azd up` でのデプロイ前に `azd provision --preview` で検証してください
- `azure.yaml` ファイルを適切に設定してください

### GitHub Actions
- セキュアなCI/CDパイプラインを設定してください
- Azure サービス プリンシパルまたはOIDCを使用してください
- 環境固有の設定を管理してください
- 自動テストとセキュリティスキャンを含めてください

## データ管理

### データベース操作
- パラメータ化クエリを使用してください
- 適切なインデックス戦略を実装してください
- 接続管理を適切に処理してください
- 暗号化を有効にしてください
- クエリパフォーマンスを監視してください

### ストレージ操作
- ファイルサイズに応じて適切な方法を選択してください（100MB未満は単純、以上は並列）
- 複数ファイルにはバッチ操作を使用してください
- 適切なアクセス層を設定してください
- 同時実行性を管理してください

## 品質保証

### テスト戦略
- 単体テスト、統合テスト、E2Eテストを含む包括的なテスト戦略を実装してください
- テストカバレッジ80%以上を目標にしてください
- モックとスタブを適切に使用してください
- テストデータの管理を適切に行ってください

### コード品質
- 静的コード解析ツールを使用してください
- コードレビューのガイドラインに従ってください
- 技術債務を定期的に評価し、対処してください
- パフォーマンステストを実施してください

## 特記事項

このプロジェクトは日本語環境での生成AI活用を主目的としているため：
- 日本語のコメントとドキュメントを優先してください
- 日本の法規制とコンプライアンス要件を考慮してください
- 日本語特有の自然言語処理の課題を理解してください
- 文化的な文脈とビジネス慣行を反映してください

```

Copilot を使用してタスクに取り組むためのベスト プラクティス:

https://docs.github.com/ja/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository


# 3. UI 作成

**サンプルデータ**と、**機能要件のドキュメント**を添付します。

GitHub Copilot Coding AgentのIssueとして使います。Copilot君にIssueをAssignして、Issueのコメントに以下の様な内容を書いてください。

## 3.1. Webアプリケーションのプロトタイプ作成

```cmd
Webアプリケーションのプロトタイプを作成してください。

添付のドキュメントはソフトウェアの機能要件です。この中の{範囲}で指定した機能要件だけを作成します。

{機能要件ドキュメント}

# 範囲
- {機能要件の中から1つ}
- 機能ID（Requirement ID）: {あれば}

# 作成フォルダー
- {app}

# 技術仕様
- HTML5のみ
- JavaScript

# データ
- 以下のファイルを参照する

{サンプルデータやデータモデルのドキュメント

# ノート
- README.md は、作成フォルダーの中に日本語で作成する。
```

完成したら、Azure Static Web Appsにデプロイをします。これはAzureのPortalから行うのが簡単です。プロトタイプだという事もありまして。

クイック スタート: 静的 Web アプリを初めてビルドする:

https://learn.microsoft.com/ja-jp/azure/static-web-apps/get-started-portal?tabs=vanilla-javascript&pivots=github

- 最初の[リポジトリを作成する]は**不要**です。私たちはすでにGitHubのリポジトリを持っていますので。

## 3.2. 複数のWeb画面を一度に表示

複数のWeb画面を一度に表示させる際には、以下の様なIssueを作成して、GitHub Copilot Coding Agentに作業をしてもらいます。

```cmd
以下のアプリケーションの画面を一度に見る事ができる、[生産調整アプリケーション]を作成する

# 作成フォルダー
 - /

# 技術仕様
- 参照されるアプリケーション
  - [生産調整エージェント]
    - フォルダー名: adjustment-agent
  - [KPIモニタリングアプリケーション]
    - フォルダー名: kpi-monitoring-app
- HTML5のみで、それぞれの画面を参照する。独自の画面は作らない
- 画面上部にアプリケーションの一覧名が表示されているタブを作成してください。タブを切り替えると画面の下に、そのアプリケーションが表示されるようにしてください。
- 各アプリケーション参照のURLは、ルートからの絶対パスで指定をしてください。
- Azure Static Web Appsにデプロイできるようにする
- 現在あるすべてのGitHub Actionsは削除する

# ノート
- README.md は、作成フォルダーの中に日本語で作成する。
```

## 3.3. マルチエージェントのグループチャットのサンプル

マルチエージェントのグループチャットのサンプルです。

```cmd
[KPIモニタリングアプリケーション]と連携して、AIエージェントが生産の変更が可能かどうかを議論して、その議論の経過状況を確認できる画面を作成してください。

# 作成フォルダー
- app/adjustment-agent

# 技術仕様
- デモ用。サンプルデータを作成して、それを使う
- HTML5のみ
- 画面はチャットの形式
- アクターとしてAIエージェントがグループチャットをする。JP工場エージェント、US工場エージェント、販売総合管理エージェント、販売ローカル管理エージェント、畠山さん(人)、榎並さん(人)
  - 工場エージェントは、生産の調整が可能かどうかを現在の生産実績と、生産能力をもとにコメントする
  - 販売エージェントは、店舗での売り上げをもとにしてコメントする
  - 人は、工場と販売のエージェントの会話に割り込んで、人らしい生産量の調整をする
- 生産量の調整が必要なビジネス上の理由
  - JPで梅雨が通常より長く続いており、アイスAが昨年より需要下振れが予想され、スナックBが売られている
  - JP向けにスナックBを生産していた工場Aは増産枠がなく工場振替生産が必要
  - JPのアイスA の販売店では工場Dのライン連続生産を担保できないため、他事業間での調整が必要
- 画面に変更調整の指示をするというボタンを追加。そのボタンを押したら次の処理を実行
- その後、実行計画を作成する。実行計画は、画面に動的に作成したステップを順番に実行していく
- そのボタンを押したら、複数のエージェントが自分がどのデータを変更したらいいのかを画面でコメントする
- 調整後に、調整結果の要約を作成して画面に表示する

# ノート
- README.md は、作成フォルダーの中に日本語で作成する。
```

# 4. REST API 作成

Github Copilot Coding Agentに**API**のプロトタイプを作成してもらいます。ただし、Azure FunctionsのSDKのコードは不正確なことが多いです。
ひな形まで作ってもらって。
そのあとは、Visual Studio Codeなどで、GitHub Copilot for Azureを使って、実装を進めます。

GitHub Copilot Coding AgentのIssueです。

終了後は、Visual Studio Codeで、**自分で実行確認をする**ことを強くお勧めします。GitHub Codespaceだと、GitHub Copilotで幾つか使えるモデルが少なかったり、SDKのインストールが追加で必要だったりするので、開発を継続することを考えると、**自分のPCやMacのVisual Studio Code**での開発・テストの実施をお勧めします。

Azure Functions でサポートされている言語:
https://learn.microsoft.com/ja-jp/azure/azure-functions/supported-languages?tabs=isolated-process%2Cv4&pivots=programming-language-csharp


Azure Functionsのプロトタイプを作成するためのIssueです。
```cmd
Azure Functionsで動作するREST APIのアプリケーションのプロトタイプを作成してください。
このAPIは以下のアプリケーションの中のJavaScriptのコードからREST APIで呼び出されます。

# 作成フォルダー
- {api}

# 呼び出し元のWebアプリケーション
- アプリケーション名: {app}
- JavaScriptのファイルの場所: {app}/js
	- それぞれの.jsファイルから、新規に作成するAPIを呼び出す

# 技術仕様
- Azure Functions v4
- C#
- .NET9.0
- Trigger: HTTP
- Bind: inもoutもHTTP

# タスク
- APIが完成してAzure Functionsにデプロイをした後で、Webアプリケーション側のコードの変更を行う
- 単体で呼び出せるようにする
- 日本語の詳細なセットアップ手順書を作成する。手順書は作成フォルダーの中に作成する

# APIに実装する機能
- {APIの分類の例}を参考にして、JavaScriptの関数をフロントエンドとバックエンドに分類して実装する
- バックエンドのAPIのみを、Azure Functionsで実装する
- サンプルデータは、全てデータ管理のAPIの中に実装する

# APIの分類の例
## フロントエンド（Front）で主に実装・担当する関数

1. **イベントハンドラー**
   - 画面上のユーザー操作（クリック、入力、送信など）に反応する関数
   - 例: `onClickButton()`, `onInputChange()`

2. **UI操作・DOM操作**
   - 画面の表示・非表示、要素の追加・削除・更新など
   - 例: `showModal()`, `updateListView()`

3. **バリデーション（クライアント側）**
   - 入力値の形式チェックや必須項目の確認など
   - 例: `validateEmailFormat()`, `checkRequiredFields()`

4. **状態管理（クライアント側）**
   - 画面の状態や一時的なデータの保持
   - 例: `setCurrentTab()`, `updateLocalState()`

5. **初期化・セットアップ**
   - ページロード時の初期化処理
   - 例: `initPage()`, `loadInitialView()`

6. **エラーハンドリング（UI通知）**
   - エラー発生時の画面表示やユーザー通知
   - 例: `showErrorMessage()`

7. **ユーティリティ（フロント専用）**
   - 画面表示用のフォーマット変換など
   - 例: `formatDisplayDate()`

## バックエンド（Backend）で主に実装・担当する関数（将来REST API化する候補）

1. **データアクセス・データ取得**
   - データベースや外部サービスとのやりとり
   - 例: `fetchUserList()`, `saveOrderData()`

2. **データ加工・変換（ビジネスロジック）**
   - データの集計、フィルタリング、計算など
   - 例: `calculateTotalAmount()`, `filterActiveUsers()`

3. **バリデーション（サーバー側）**
   - セキュリティや整合性のための入力チェック
   - 例: `validateUserInputOnServer()`

4. **状態管理（サーバー側）**
   - ユーザーセッションやアプリ全体の状態管理
   - 例: `updateUserSession()`

5. **API通信**
   - REST APIとして外部公開する関数
   - 例: `getUserDataAPI()`, `postOrderAPI()`

6. **エラーハンドリング（サーバー側）**
   - サーバーでのエラー処理やログ記録
   - 例: `logError()`, `returnErrorResponse()`

7. **ユーティリティ（サーバー専用）**
   - サーバー側でのみ必要な共通処理
   - 例: `hashPassword()`, `generateToken()`

# ノート
- README.md は、作成フォルダーの中に日本語で作成する。
```

Coding Agentの作業が終了するまで待ちます。終了後のbranchは**削除せずに**残しておいてください**。後で、Visual Studio Codeでの開発に使います。

- Visual Studio CodeやGitHub Desktopなどを使って、Coding Agentが作成したコードをローカルにクローンします。
- Visual Studio Codeで、Azure Functions Toolsなどを使って、ローカルでの動作確認をします。

クイックスタート: Visual Studio Code を使用して Azure に C# 関数を作成する:

https://learn.microsoft.com/ja-jp/azure/azure-functions/create-first-function-vs-code-csharp

## 4.1. Azure Functionsへデプロイ

Azure Functionsへデプロイをします。Visual Studio Codeから行うのが簡単です。

クイックスタート: Visual Studio Code を使用して Azure に C# 関数を作成する:

https://learn.microsoft.com/ja-jp/azure/azure-functions/create-first-function-vs-code-csharp

開発言語に応じて、周辺からドキュメントを探してみてください。

# 5. HTMLの画面とREST APIの連携
GitHub Copilot Coding Agentに、HTMLの画面とAPIの連携を作成してもらいます。

## 5.1. APIの一覧取得

Visual Studio Code の **GitHub Copilot for Azure**を使って、APIの一覧取得のコードを作成します。
```
@azure Azure Functionsの関数一覧を作成してください。関数名、FQDN付のURL、関数の説明、入出力の詳細な説明を含めてください。Markdownの書式で作成してください。
```

## 5.2. REST APIの呼び出し

タスクとしてGitHubのIssueとして管理したい事もあり。GitHub Copilot Coding AgentにAPIの呼び出しを作成してもらいます。

```cmd
既存のJavaScriptのコードの中から、Azure Functions上で動作しているREST APIを呼び出すように修正をしてください。

# 変更対象
- Webアプリケーションの場所: {app}
  - JavaScriptのファイルの場所: {app}/js

それぞれの.jsファイルから、最適なAzure Functions上のAPIを呼び出すように修正をしてください。

# Azure Functions REST API 仕様書
{5.1. APIの一覧取得で取得したAPIの仕様書をここに貼り付ける}
```

# 6. データを永続保存

データをAzure Cosmos DBへ保存をします。

## 6.1. Azure Cosmos DBの作成

Azure Portalから、Azure Cosmos DBを作成します。NoSQL APIを選択してください。
Serverlessプランを選択するのがおススメです。

クイックスタート: Azure portal を使用して Azure Cosmos DB for NoSQL アカウントを作成する

https://learn.microsoft.com/ja-jp/azure/cosmos-db/nosql/quickstart-portal

(Work in progresss)

## 6.1. Azure Functionsのアカウントから、Azure Cosmos DBのNoSQL APIへ接続するための
認証・認可設定 - マネージドIDの設定

Azure Functionsから、Azure Cosmos DBへの認証を、マネージドIDを使って行います。

Azure Cosmos DBの


チュートリアル: マネージ ID と SQL バインドを使用して Azure SQL に関数アプリを接続する:

https://learn.microsoft.com/ja-jp/azure/azure-functions/functions-identity-access-azure-sql-with-managed-identity

- このドキュメントは、Azure SQL Databaseを使っていますが、Azure Cosmos DBのNoSQL APIでも同様の手順で設定が可能です。


Azure Functions 2.x 以降での Azure Cosmos DB のトリガー:

https://learn.microsoft.com/ja-jp/azure/azure-functions/functions-bindings-cosmosdb-v2-trigger?tabs=python-v2%2Cisolated-process%2Cextensionv4%2Cnodejs-v4&pivots=programming-language-csharp#identity-based-connections



## 6.2. Azure Comsmos DBへのデータ登録処理の実装

Azure FunctionsのREST APIの中に、Azure Cosmos DBへデータを登録する処理を実装します。

GitHub Copilot Coding AgentのIssueです。

```cmd
Azure FunctionsのREST APIの中に、Azure Cosmos DBのNoSQL APIへデータを登録する処理を実装してください。

# Applicationの場所
- api/{api-name}

# タスク
- 既存のコードの中のデータモデルで定義されているデータ構造を参照します。Azure Cosmos DBのNoSQL APIのドキュメントを参照して、データ登録の処理を実装してください。
- 適切なデータベース、コンテナの作成もしてください。
- 既存のサンプルデータ作成の処理はコメントアウトしてください。

# Azure Cosmos DBのNoSQL APIのURI
- {Azure Cosmos DBのNoSQL APIのURIをここに貼り付ける}

# 参考ドキュメント:
- https://learn.microsoft.com/ja-jp/azure/cosmos-db/nosql/quickstart-dotnet
```