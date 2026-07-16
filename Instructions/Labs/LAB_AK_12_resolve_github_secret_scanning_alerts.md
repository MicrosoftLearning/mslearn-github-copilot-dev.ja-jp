---
lab:
  title: 演習 - GitHub Copilot を使用して GitHub シークレット スキャンのアラートを解決する
  description: GitHub シークレット スキャンのアラートを管理する方法と、GitHub Copilot の質問モードとエージェント モードを使ってハードコーディングされたシークレットを修復する方法について説明します。
  duration: 40 minutes
  level: 300
  islab: true
  primarytopics:
    - GitHub
    - Visual Studio Code
---

# GitHub Copilot を使用して GitHub シークレット スキャンのアラートを解決する

セキュリティ機能である GitHub シークレット スキャンは、API キー、トークン、パスワードなど、コード リポジトリ内の機密情報の露出を特定して防止するのに役立ちます。 シークレットが検出されると、GitHub によってアラートが生成され、リポジトリ管理者と保守管理者は潜在的なセキュリティ リスクについて通知を受け取ります。

この演習では、GitHub と GitHub Copilot を使って、コード リポジトリ内のハードコーディングされたシークレットに関連する GitHub シークレット スキャンのアラートを分析して解決します。

この演習の所要時間は約 **40** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。Git 2.48 以降、.NET SDK 9.0 以降、C# 開発キット拡張機能をインストールした Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

この演習のラボ環境としてローカル PC を使用している場合:

- ローカル PC をラボ環境として構成する方法については、ブラウザーで次のリンクを開いてください。<a href="https://go.microsoft.com/fwlink/?linkid=2320147" target="_blank">ラボ環境のリソースを構成する</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開きます: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習に、ホストされたラボ環境をお使いの場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーのサイト ナビゲーション バーに次の URL を貼り付けてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

- パッケージのダウンロードと復元のソースとして公式の NuGet.org リポジトリを使用するように .NET SDK を確実に構成するには、次の手順を実行します。

    コマンド ターミナルを開き、次のコマンドを実行します。

    ```bash

    dotnet nuget add source https://api.nuget.org/v3/index.json -n nuget.org

    ```

- 自分の名前と電子メール アドレスを使用するように Git を確実に構成するには、次の手順を実行します。

    次のコマンドを自分の情報で更新し、コマンドを実行します。

    ```bash

    git config --global user.name "Julie Miller"

    ```

    ```bash

    git config --global user.email julie.miller@example.com

    ```

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 クライアントは、レガシ アプリケーションからのハードコーディングされたシークレットの削除について支援を必要としています。 あなたは、GitHub シークレット スキャン、GitHub プッシュ保護、GitHub Copilot を使って、ハードコーディングされたシークレットの検出と修復を行う予定です。 開発環境として Visual Studio Code を使用し、開発タスクを支援するために GitHub Copilot を使用します。 あなたには次のレガシ アプリが割り当てられています。

- ContosoOrderProcessor: 注文処理ワークフローを提供する eコマース アプリ。 ワークフローには、顧客の検証、支払い処理、メール通知、データベース操作が含まれます。 コードには、安全な方法で管理する必要があるハードコーディングされたシークレットが含まれています。

この演習には、次のタスクが含まれています。

1. ContosoOrderProcessor リポジトリを自分の GitHub アカウントにインポートします。
1. GitHub でセキュリティ アラートを確認します。
1. Visual Studio Code でコード プロジェクトを確認します。
1. 環境変数を構成し、アプリケーションを実行します。
1. GitHub Copilot の質問モードを使用してシークレット スキャン アラートを分析します。
1. GitHub Copilot のエージェント モードを使用して、シークレット スキャン アラートを修復します。
1. 変更を GitHub にプッシュして、シークレット スキャンのアラートを閉じます。
1. GitHub のプッシュ保護機能をテストします。

### ContosoOrderProcessor リポジトリを自分の GitHub アカウントにインポートする

GitHub Importer を使用すると、独自の GitHub アカウントに既存のリポジトリのコピーを作成し、インポートされたコピーを完全に制御できます。

このタスクでは、GitHub アカウントを使って ContosoOrderProcessor リポジトリをインポートします。

そのためには、以下の手順を実行してください。

1. ブラウザー ウィンドウを開いて GitHub.com に移動します。

1. GitHub アカウントにサインインし、[リポジトリ] タブを開きます。

    右上隅にあるプロファイル アイコンをクリックし、**[リポジトリ]** を選択して、[リポジトリ] タブを開きます。

1. [リポジトリ] タブで、**[新規]** ボタンを選択します。

1. **[新しいリポジトリの作成]** セクションで、**[リポジトリのインポート]** を選択します。

1. **[Import your project to GitHub]** ページで、**[Your source repository details]** のソース リポジトリに次の URL を入力します。

    ```plaintext
    https://github.com/MicrosoftLearning/resolve-github-security-alerts-lab-project
    ```

1. **[新しいリポジトリの詳細]** セクションの **[所有者]** ドロップダウンで、GitHub ユーザー名を選択します。

1. **[リポジトリ名]** フィールドに「**ResolveGitHubSecurityAlerts**」と入力します。

    GitHub で、リポジトリ名の可用性が自動的にチェックされます。 この名前が既に使用されている場合は、リポジトリ名に一意のサフィックス (イニシャルや乱数など) を追加して一意にします。

1. リポジトリが**パブリック**に設定されていることを確認します。

    パブリック リポジトリでは、シークレット スキャンが既定で有効になっています。

1. **[インポートの開始]** ボタンを選択します。

    GitHub では、インポート プロセスを使用して、アカウントに新しいリポジトリを作成します。

    > **注**:インポート プロセスが完了するまでに 1、2 分かかる場合があります。

1. インポート プロセスが完了するまで待ってから、**ResolveGitHubSecurityAlerts** リポジトリを開きます。

    ResolveGitHubSecurityAlerts リポジトリには、ContosoOrderProcessor アプリケーションが含まれています。 ハードコーディングされたシークレットは、トレーニングの目的で含まれています。

### GitHub でセキュリティ アラートを確認する

GitHub のシークレット スキャン機能は、誤ってリポジトリにコミットされた API キー、トークン、パスワード、その他のシークレットを検出します。 セキュリティ アラートは、シークレットが検出されると生成され、シークレットの種類、コード内の場所、修復についての推奨事項に関する情報が提供されます。

このタスクでは、コード リポジトリを調べて、GitHub シークレット スキャンによって生成されたセキュリティ アラートを確認します。

そのためには、以下の手順を実行してください。

1. 少し時間をかけて、リポジトリの README.md ファイルを確認しましょう。

    README.md ファイルには、意図的に公開されているシークレットの説明や、アプリケーションを実行する手順など、ContosoOrderProcessor アプリケーションの概要が含まれています。

    > **重要**:アプリケーションを実行する前に環境変数を設定する必要があることに注意してください。

1. **[設定]** タブを選択します。

1. 左側のサイドバーで、**[Advanced Security]** を選択します。

    [Advanced Security] ページには、リポジトリに対して有効にできるさまざまなセキュリティ機能が表示されます。

1. ページの一番下までスクロールし、**[シークレット保護]** と **[プッシュ保護]** が有効になっていることを確認します。

    **[Disable]** ボタンは、機能の無効化に使用でき、2 つの機能が現在有効になっていることを示します。

    > **注**:パブリック リポジトリでは、シークレット スキャン (シークレット保護とプッシュ保護) が既定で有効になっています。 プライベート リポジトリでは、シークレット スキャンを手動で有効にする必要があります。

1. ページの上部にある **[セキュリティ]** タブを選択します。

    [セキュリティ] タブには、セキュリティ アドバイザリ、Dependabot アラート、コード スキャン アラート、シークレット スキャン アラートなど、リポジトリのセキュリティの概要が表示されます。

    [セキュリティ] タブは、[リポジトリ] ページの上部に (コード、issue、pull request などとともに) 表示されています。

1. 左側のサイドバーの **[脆弱性アラート]** セクションで、**[シークレット スキャン]** を選択します。

    [Secret scanning alerts] ページには、リポジトリで検出されたシークレットのセキュリティ アラートが表示されます。 各アラートには、シークレットの種類、シークレットが見つかったファイルと行番号、アラートの状態に関する情報が含まれます。

1. 少し時間を取って、シークレット スキャンのアラート ページを確認します。

    次のアラートを含むリストが表示されます。

    - ✓ Square アクセス トークン - PaymentService.cs。
    - ✓ Stripe API キー - PaymentService.cs。
    - ✓ Slack 受信 Webhook URL - AppConfig.cs。
    - ✓ Slack API トークン - AppConfig.cs。
    - ✓ Mailgun API キー - EmailService.cs。
    - など

    > **注**:GitHub のシークレット スキャン機能は、パターン マッチングを使用してコードベース内のシークレットを検出します。 表示されるアラートは、トレーニング目的で ContosoOrderProcessor アプリケーションに意図的に含められたシークレットに基づいています。

1. アラートの 1 つを表示するため、**[Mailgun API Key]** アラートを選びます。

    アラートの詳細ページでは、シークレットに関する次のような情報が提供されます。

    - 公開されているシークレット。
    - 修復手順。
    - シークレットが見つかったファイル パス。
    - コンテキスト内のシークレットを示すコード スニペット。
    - シークレットを導入したコミット。

1. 少し時間を取って、アラートに示されたコード スニペットを確認します。

    アラートが EmailService.cs ファイル (19 行目) を指していることに注目してください。 このコードで、Mailgun API キーが公開されます。

1. シークレット スキャンのアラート ページに戻ります。

1. **[Stripe API キー]** のアラートを選択し、詳細を確認します。

    このアラートは、PaymentService.cs ファイル (24 行目) を指していることに注目してください。 このコードで、Stripe ライブ API キーが公開されます。

1. シークレット スキャンのアラート ページに戻り、他のアラートを簡単に確認します。

    アラートを確認するときは、次の詳細に注意してください。

    - 現在、すべてのアラートは "未対応" 状態であり、対処する必要があることを示します。
    - 各アラートで、シークレットが見つかったファイルと行番号が特定されます。
    - 特定されたシークレットは、次の 3 つのファイルにあります。
        - AppConfig.cs
        - EmailService.cs
        - PaymentService.cs
    - アラートは、解決のためにチーム メンバーに割り当てることができます。

### Visual Studio Code でコード プロジェクトを確認する

ContosoOrderProcessor アプリケーションは、eコマース注文処理ワークフローをシミュレートする C# コンソール アプリケーションです。 ハードコーディングされたシークレットはトレーニングのためにコードに含まれており、シークレットがある場所を示すコメントが添えられています。 他のシークレットは、環境変数から安全に読み込まれます。

このタスクでは、リポジトリをローカル開発環境にクローンし、Visual Studio Code でプロジェクトを確認します。

そのためには、以下の手順を実行してください。

1. リポジトリのルート ページに戻ります。

    ルート ページには、リポジトリ内のファイルとフォルダーの一覧が表示されます。

1. ResolveGitHubSecurityAlerts リポジトリをローカル開発環境にクローンします。

    次に例を示します。

    1. **[コード]** ボタンのドロップダウンを開きます。

    1. **[URL をクリップボードにコピーする]** アイコンを選択して、リポジトリの URL をコピーします。

    1. ターミナルまたはコマンド プロンプトを開きます。

    1. リポジトリをクローンするディレクトリに移動します。

    1. 次のコマンドを実行して、リポジトリを複製します。

        ```bash
        git clone https://github.com/<your-account>/ResolveGitHubSecurityAlerts.git
        ```

        **`<your-account>`** は、ご自身の GitHub ユーザー名に置き換えます。

1. クローンしたリポジトリを Visual Studio Code で開きます。

    次に例を示します。

    1. Visual Studio Code を起動します。

    1. **[ファイル]** メニューの **[フォルダーを開く]** を選択します。

    1. リポジトリをクローンしたディレクトリに移動します。

    1. **ResolveGitHubSecurityAlerts** フォルダーを選択し、**[フォルダーの選択]** を選択します。

1. 最新バージョンの Visual Studio Code を使用していることと、GitHub Copilot がインストールされ、有効になっていることを確認します。

    Visual Studio Code が最新であることは、Visual Studio Code ウィンドウの左下隅にある [管理] アイコン (歯車アイコン) を選んでから、**[更新プログラムの確認]** を選んで確認できます。

    GitHub Copilot が有効になっていることは、Visual Studio Code ウィンドウの右下隅にある [Copilot] アイコンを選ぶか、チャット ビューを開いてチャット機能がアクティブであることを調べて、確認できます。

1. Visual Studio Code のエクスプローラー ビューを使用して ContosoOrderProcessor フォルダーを展開し、少し時間をかけてプロジェクトの構造を確認します。

    ContosoOrderProcessor アプリケーションは、シンプルな階層化アーキテクチャを採用しています。 これには、次のファイルとフォルダーが含まれています。

    - **Configuration**:アプリケーション全体の構成定数を含む AppConfig.cs ファイル (公開されているシークレットを含む) が格納されています。
    - **モデル**:Customer.cs および Order.cs モデル クラスが含まれています。
    - **セキュリティ**: セキュリティ検証ロジックを含む SecurityValidator.cs ファイルが格納されています。
    - **[サービス]**: データベース アクセス、メール送信、支払い処理用のサービス クラス (DatabaseService.cs、EmailService.cs、PaymentService.cs) が含まれています。
    - **Program.cs**注文処理ワークフローを示すメイン エントリ ポイント。

1. **Program.cs** ファイルを開き、少し時間をかけてコードを確認します。

    この Main メソッドで次のタスクが実行されることに注目してください。

    1. Main メソッドは、最初に、構成オブジェクトを作成して検証します。

        Program.cs ファイルの下部にある ValidateRequiredConfiguration メソッドに注目してください。 このメソッドは、アプリケーションを開始する前に、必要なすべての構成値が存在することを検証します。 appsettings.json 内の構成データと環境変数内のシークレットがチェックされます。 いずれかが存在しない場合は、不足している項目の一覧を表示する書式設定されたエラー メッセージを表示し、セットアップ スクリプトを実行するようユーザーに指示して、エラー コードでアプリケーションを終了します。

        > **注**:構成が正常に検証されない場合、アプリケーションは早期に終了します。 たとえば、環境変数が設定されていない場合です。 これにより、アプリケーションが不完全または正しくない設定で実行されるのを防ぐことができます。

    1. Main メソッドは、構成を検証した後、次に、以下のタスクを含む注文処理ワークフローをシミュレートします。

        - 顧客の取得。
        - 顧客の検証。
        - 注文の作成。
        - 注文の検証。
        - 詐欺の検出。
        - 支払い処理。
        - データベースの永続化。
        - メール通知。
        - 発送通知。

1. Visual Studio Code のエクスプローラー ビューを使用して、**Configuration** フォルダーを展開します。

1. **AppConfig.cs** ファイルを開き、少し時間をかけてコードを確認します。

    AppConfig.cs は、ContosoOrderProcessor のすべてのアプリケーション設定、資格情報、および機能フラグを管理する静的構成クラスです。 これは、デュアル ソース構成パターンを実装します。一部のシークレットはフォールバック チェーンを使用して IConfiguration (環境変数) から安全に読み込まれ、他のシークレットはトレーニングの目的でパブリック定数として意図的にハードコーディングされたままになります。 このクラスでは、初期化、構成検証、資格情報の取得のためのユーティリティ メソッドが提供されます。

1. **Services** フォルダーを展開し、少し時間を取って次のファイルを確認してください。

    - **PaymentService.cs**:このクラスは、支払いゲートウェイを使用して支払い処理を行います。 これには、Stripe と Square 支払いプロバイダーのハードコーディングされたシークレットが含まれています。
    - **EmailService.cs**: このクラスは、Mailgun メール サービスを使ってメール通知を管理します。 これには、ハードコーディングされた Mailgun API キーと SMTP 資格情報が含まれます。

> **注**:ハードコーディングされたシークレットは、トレーニングのためにのみアプリケーションに含まれています。 実際のアプリケーションでは、シークレットをハードコーディングしないでください。

### 環境変数を構成し、アプリケーションを実行する

ContosoOrderProcessor アプリケーションは、注文処理ワークフローを実行する前に、環境変数を含むすべての必要な構成値が存在することを確認します。 環境変数が設定されていない場合、アプリケーションはエラー メッセージを表示して終了します。

アプリケーションを実行してコンソール出力を観察すると、シークレット スキャンのアラートを修復する前に、アプリケーションの意図された動作を理解するのに役立ちます。

> **注**:環境変数を使ってシークレットを管理することはできますが、セキュリティとスケーラビリティについて制限があります。 運用環境のデプロイには、Azure Key Vault やその他のシークレット管理ソリューションをお勧めします。

このタスクでは、環境変数を設定する PowerShell スクリプトを作成し、アプリケーションを実行して、コンソール出力を記録します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューを使って、プロジェクトのルートに **setup-secrets.ps1** という名前のファイルを作成します。

    このスクリプト ファイルを使って、現在の PowerShell セッション用に環境変数を設定します。

1. **setup-secrets.ps1** ファイルに次の PowerShell コードを追加します。

    ```powershell
    # This script sets environment variables for the current PowerShell session only.
    #
    # These variables are required to run the application.
    #
    # IMPORTANT NOTES:
    #   • These variables are set for THIS PowerShell session only
    #   • Variables will be lost when you close this window
    #   • All secret values are FICTIONAL for training purposes only
    #    
    Write-Host "════════════════════════════════════════════════════════" -ForegroundColor Cyan
    Write-Host "  Contoso Order Processor - Environment Setup Script   " -ForegroundColor Cyan
    Write-Host "════════════════════════════════════════════════════════" -ForegroundColor Cyan
    Write-Host ""
    
    Write-Host "Setting environment variables for current session..." -ForegroundColor Yellow
    Write-Host ""
    
    # AWS Credentials
    $env:Aws__AccessKeyId = "AKIA1234567890EXAMPLE"
    $env:Aws__SecretAccessKey = "1234567890abcdefghijklmnopqrstuvwxyzABCD"
    Write-Host "✓ AWS credentials configured" -ForegroundColor Green
    
    # SendGrid API Key
    $env:SendGrid__ApiKey = "SG.1234567890abcdefghij.1234567890abcdefghijklmnopqrstuvwxyzABCDEF"
    Write-Host "✓ SendGrid API key configured" -ForegroundColor Green
    
    # PayPal Credentials
    $env:PayPal__ClientId = "AY1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890abcdefghijklmno"
    $env:PayPal__ClientSecret = "EJ1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890abcdefghijklm"
    Write-Host "✓ PayPal credentials configured" -ForegroundColor Green
    
    # Azure Storage Connection String
    $env:Azure__StorageConnectionString = "DefaultEndpointsProtocol=https;AccountName=contosostorageacct;AccountKey=TRAINING-PURPOSE-ONLY-NOT-A-REAL-KEY-1234567890abcdefghijklmnopqrstuvwxyzABCDEFGH==;EndpointSuffix=core.windows.net"
    Write-Host "✓ Azure Storage connection string configured" -ForegroundColor Green
    
    # SQL Server Connection String
    $env:Database__ConnectionString = "Server=tcp:contoso-orders.database.windows.net,1433;Initial Catalog=ContosoOrdersDB;User ID=orderadmin;Password=MyPassword123!;Encrypt=True;TrustServerCertificate=False;MultipleActiveResultSets=False;Connection Timeout=30;"
    Write-Host "✓ SQL Server connection string configured" -ForegroundColor Green
    
    # GitHub Personal Access Token
    $env:GitHub__PersonalAccessToken = "ghp_1234567890abcdefghijklmnopqrstuvwxyzAB"
    Write-Host "✓ GitHub Personal Access Token configured" -ForegroundColor Green
    
    # npm Token
    $env:Npm__Token = "npm_1234567890abcdefghijklmnopqrstuvwxy"
    Write-Host "✓ npm Token configured" -ForegroundColor Green
    
    Write-Host ""
    Write-Host "════════════════════════════════════════════════════════" -ForegroundColor Cyan
    Write-Host "✓ All environment variables configured successfully!" -ForegroundColor Green
    Write-Host "════════════════════════════════════════════════════════" -ForegroundColor Cyan
    Write-Host ""
    ```

    このスクリプトでは、AWS 資格情報、SendGrid API キー、PayPal 資格情報、Azure Storage 接続文字列、SQL Server 接続文字列、GitHub 個人用アクセス トークン、npm トークンの各環境変数を設定します。 すべてのシークレット値は架空のものであり、トレーニング目的にのみ使用されます。

1. Visual Studio Code の統合ターミナルを開きます。

    上部のメニュー バーで **[ターミナル]** を選択し、**[新規ターミナル]** を選択します。

1. 環境変数を構成するには、ターミナルで次のコマンドを入力します。

    ```powershell
    . .\setup-secrets.ps1
    ```

    スクリプトによって次の出力が生成されます。

    ```plaintext
    ════════════════════════════════════════════════════════
      Contoso Order Processor - Environment Setup Script
    ════════════════════════════════════════════════════════
    Setting environment variables for current session...
    ✓ AWS credentials configured
    ✓ SendGrid API key configured
    ✓ PayPal credentials configured
    ✓ Azure Storage connection string configured
    ✓ SQL Server connection string configured
    ✓ GitHub Personal Access Token configured
    ✓ npm Token configured
    ════════════════════════════════════════════════════════
    ✓ All environment variables configured successfully!
    ════════════════════════════════════════════════════════
    ```

    > **注**:この環境変数は一時的なものであり、現在の PowerShell ウィンドウにのみ存在します。 ターミナルを閉じると、それらは失われます。 環境変数は、トレーニング環境を簡略化するために使用されます。 より安全なアプローチには、Azure Key Vault やその他のセキュリティで保護されたシークレット管理ソリューションの使用が含まれます。

1. アプリケーションを実行するには、ターミナルで次のコマンドを入力します。

    ```powershell
    cd ContosoOrderProcessor
    dotnet run
    ```

    アプリケーションは構成を検証してから、シミュレートされたワークフローを実行します。

    注文処理ワークフローの手順には、顧客情報の取得と検証、注文の作成、注文の検証や詐欺リスクの評価などのセキュリティ チェックの実行が含まれます。 注文がこれらのチェックに合格した場合、ワークフローは支払いを処理し、注文をデータベースに保存し、トランザクションをログしてから、確認と発送の通知メールを顧客に送信します。 ワークフローは、全体を通して、各ステップの詳細なコンソール出力を提供し、エラーを適切に処理し、不足している構成または失敗した操作を明確に報告します。

1. 少し時間をかけて、ContosoOrderProcessor アプリケーションによって生成されたコンソール出力を確認します。

    アプリケーションは、構成の詳細や処理手順など、ワークフロー プロセス全体をログします。 一部のメッセージでシークレットが公開されていることに注意してください。 たとえば、接続文字列、API キー、その他の機密情報がコンソールにログされていることがわかります。

1. **OriginalConsoleOutput.txt** という名前のファイルをプロジェクトのルートに追加します。

    エクスプローラー ビューでファイルを右クリックし、**[新しいファイル]** を選択し、**OriginalConsoleOutput.txt** と名前を付けて、ファイルを作成できます。

1. コンソール出力を **OriginalConsoleOutput.txt** ファイルにコピーします。

    この元のコンソール出力を、シークレット スキャン アラート修復後の最終的なコンソール出力と比較します。

### GitHub Copilot の質問モードを使用してシークレット スキャン アラートを分析する

GitHub Copilot の質問モードで提供されるインテリジェントなコード分析は、セキュリティの脆弱性、潜在的な影響、推奨される修復戦略を把握するのに役立ちます。 露出したシークレットを含むセキュリティの問題を体系的に分析すると、修正を実装する前に、問題について包括的な理解を深めることができます。

このタスクでは、GitHub Copilot の Ask モードを使用して、ContosoOrderProcessor アプリケーションでハードコーディングされたシークレットを分析します。 公開されたシークレットに関連するセキュリティ リスクを確認し、修復の提案を収集します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code で GitHub Copilot のチャット ビューが開かれていることを確認します。

    チャット ビューを開くには、Visual Studio Code ウィンドウの上部にある [チャットの切り替え] ボタンを選択するか、`Ctrl+Alt+I` キー (Windows/Linux) または `Cmd+Option+I` キー (Mac) を押します。

1. 新しいまたはクリーンなチャット セッションで開始していることを確認します。

    現在のチャット セッションでコードベースを分析または更新した場合は、新しく始めることをお勧めします。 **[新しいチャット]** ボタン (チャット パネルの上部にある **[+]** アイコン) を選択して、新しい会話を開始します。 新しいチャット セッションを使用すると、以前のコンテキストが GitHub Copilot の分析に影響を与える可能性はないことが保証されます。

1. [エージェントの設定] ボタンを使用して **[質問]** モードを選択し、[モデルの選択] ボタンを使用して **[Auto]** モデルを選択します。

1. コード エディターで **Services/PaymentService.cs** ファイルを開きます。

1. GitHub Copilot に、PaymentService クラスで公開されているシークレットの特定、シークレットの分析、セキュリティ リスクの説明を依頼します。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Analyze the PaymentService class. Identify secrets exposed in the code. Analyze the secret types and providers. Explain the security risks associated with the identified secrets.
    ```

1. 少し時間をかけて、GitHub Copilot の分析を確認しましょう。

    GitHub Copilot によって、PaymentService クラスのハードコーディングされたシークレットを示す詳細な分析が提供されます。 シークレットには、Stripe の API キーと Square のアクセス トークンが含まれているはずです。 説明では、シークレットの種類、そのプロバイダー、コード内の場所、関連するセキュリティ リスクが示されています。

    分析は次の例のようになります。

    ```md
    The `PaymentService` class exposes two hard-coded secrets:
    
    1. **Stripe API Key**
       - **Type:** API Key
       - **Provider:** Stripe
       - **Location:** `private const string StripeApiKey = "sk_live_51MqxYzABC123def456GHI789jklMNO012pqrSTU345vwxYZ678abcDEF901ghiJKL234mnoPQR567stuVWX890yzABC";`
       - **Risk:** Anyone with access to this key can make authenticated requests to Stripe, potentially initiating fraudulent transactions, accessing payment data, or causing financial loss.
    
    2. **Square Access Token**
       - **Type:** Access Token
       - **Provider:** Square
       - **Location:** `private const string SquareAccessToken = "EAAAEOuLQavTvyym5PByGZrGdRLWiL_RB0n8YF0gELTxFqLhNp6bKHRhA6P7Uv5F";`
       - **Risk:** Exposure allows attackers to access Square APIs, potentially viewing sensitive transaction data, issuing refunds, or manipulating payment flows.
    
    **Security Risks:**
    - **Credential Leakage:** Hard-coded secrets are easily discoverable in source code, especially if the repository is public or shared.
    - **Unauthorized Access:** Attackers can use these credentials to impersonate your service, access customer data, or perform malicious transactions.
    - **Compliance Violations:** Storing secrets in code might violate PCI DSS, GDPR, or other regulatory requirements.
    - **Lack of Rotation:** Hard-coded secrets are difficult to rotate, increasing the risk if they are ever compromised.
    
    **Best Practices:**
    - Remove hard-coded secrets from code.
    - Store secrets in secure configuration (environment variables, secret managers).
    - Audit and rotate exposed credentials immediately.
    
    You can find these exposed secrets in `PaymentService.StripeApiKey` and `PaymentService.SquareAccessToken`.
    ```

1. GitHub Copilot に、Stripe の API キーの具体的な修復ガイダンスを求めます。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    What's the best practice for storing API keys like Stripe in a C# application? How should I refactor this code to remove the hard-coded Stripe API key?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    GitHub Copilot により、環境変数やシークレット管理サービスの使用など、API キーを安全に保存するためのベスト プラクティスが推奨されます。 また、シークレットをハードコーディングするのではなく、安全な場所から Stripe の API キーを読み取るように PaymentService クラスをリファクタリングする方法を示すコード スニペットも提供されるはずです。

    GitHub Copilot は質問モードの会話を使って、ユーザーが次のタスクでエージェント モードを使って実行する修復手順を示します。 GitHub Copilot の推奨事項をメモすることもできます。 分析情報は修復のガイドとして役立ちます。

1. GitHub Copilot に、Square アクセス トークンの修復戦略を提案するように依頼します。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Suggest remediation strategies for the Square access token exposed in the PaymentService class. How can I securely manage this secret? Should I use the same approach to manage both the Square access token and the Stripe API key?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    GitHub Copilot により、Square アクセス トークンと Stripe API キーの両方を管理するための一貫した戦略の使用が推奨されます。 GitHub Copilot により、環境変数またはシークレット管理サービスの使用が提案されます。

1. コード エディターで **Services/EmailService.cs** ファイルを開きます。

1. GitHub Copilot に、EmailService クラスの分析、シークレットの特定、修復戦略の提案を依頼します。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Analyze the EmailService class. Identify secrets exposed in the code. Suggest remediation strategies for securely managing secrets.
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    GitHub Copilot により、ハードコーディングされた Mailgun API キーと、GitHub シークレット スキャンで認識されなかったいくつかの SMTP 資格情報が特定されます。 シークレット スキャン ツールでは、すべての種類のシークレットが検出されないことがあり、既知のパターンと一致しない場合は特にそうです。

    GitHub Copilot により、シークレットを安全に管理するために環境変数またはシークレット管理サービスの使用が推奨されます。

1. GitHub シークレット スキャンで SMTP 資格情報が検出されなかった理由を GitHub Copilot に問い合わせます。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Why didn't GitHub Secret Scanning detect the SMTP credentials in the EmailService class? Explain the limitations of secret scanning tools.
    ```

1. GitHub Copilot の応答を確認します。

    シークレット スキャン ツールはパターン マッチングに依存しており、すべての種類のシークレットが検出されるとは限らず、既知のパターンまたは形式と一致しない場合は特にそうであることが、GitHub Copilot で説明されるはずです。 また、コード レビューや静的分析ツールなど、複数の層のセキュリティを使用して潜在的な脆弱性を特定することの重要性も強調されます。

1. **Configuration/AppConfig.cs** ファイルをコード エディターで開きます。

1. AppConfig.cs ファイルをレビューし、ContosoOrderProcessor アプリケーション内にあるハードコーディングされたすべてのシークレットの管理に使う必要がある修復戦略を提案するよう、GitHub Copilot に指示します。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Review the AppConfig class and identify exposed secrets. Consider the secrets previously identified in the PaymentService and EmailService classes. Should the application's existing approach for securely managing secrets be extended for all secrets in the ContosoOrderProcessor application? Explain your reasoning and suggest a remediation strategy.
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    GitHub Copilot は、ContosoOrderProcessor アプリケーション内のすべてのシークレットを安全に管理するために環境変数を使用する既存のアプローチを拡張するよう (または Azure Key Vault のようなシークレット マネージャーを使うよう) 推奨するはずです。 このアプローチによって、シークレット管理の一元化、露出のリスクの軽減、安全なアプリケーション開発のためのベスト プラクティスへの準拠が実現されることが説明されます。

### GitHub Copilot のエージェント モードを使用してシークレット スキャン アラートを修復する

GitHub Copilot のエージェント モードは、ハードコーディングされたシークレットをコード ファイル内の安全な代替手段に直接置き換えて、セキュリティの修正を実装するのに役立ちます。 エージェント モードは、分析を行うだけでなく、セキュリティのベスト プラクティスを使ってコード ファイルを積極的に編集します。

このタスクでは、GitHub Copilot のエージェント モードを使って、前のタスクで分析したシークレット スキャン アラートのいくつかを修復します。 質問モードでの分析の過程で明らかになった修復戦略を適用します。 GitHub のプッシュ保護機能をテストするため、一部のシークレットは意図的に修正しないでおきます。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエディターで開かれているすべてのファイルを閉じます。

    エージェント モードでは、コードベースと割り当てられたタスクが確認され、コンテキストが確立されて、修復が必要な領域が特定されます。

1. チャット ビューで、GitHub Copilot の**エージェント** モードに切り替えます。

    チャット ビューの左下隅にある [エージェントの設定] ドロップダウンを使って、**[エージェント]** モードを選びます。

1. PaymentService クラスに関連するセキュリティ アラートを修復するタスクを GitHub Copilot に割り当てます。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Review the current conversation and then review the PaymentService.cs file. Notice how some secrets are managed using environment variables and others are hard-coded. I need you to remove hard-coded secrets from the PaymentService class and implement environment variables that securely manage the secrets. Update the setup-secrets.ps1 script for the new environment variables, and ensure that the formatting matches the existing environment variable declarations. Ensure that the suggested updates don't introduce errors and run a build task to verify the app builds correctly after the updates.
    ```

1. タスクを実行し、エージェントの進行状況を監視します。

    エージェントは、PaymentService.cs ファイルの分析と、ハードコーディングされたシークレットを削除するためのコード変更の提案を始めます。 進行状況がチャット ビューで報告されます。 GitHub Copilot エージェントは、コードの更新をコード ファイル内で直接提案します。

    > **注**:エージェントは、特定のファイルにアクセスしたり、特定のアクションを実行したりするためのアクセス許可を求める場合があります。 エージェントがタスクを完了できるよう、必要に応じてアクセス許可を付与します。

1. しばらく時間を取って、GitHub Copilot エージェントによって提案された変更を検討してください。

    エディターで PaymentService.cs と setup-secrets.ps1 ファイルを確認します。

    コード エディターで各編集を個別に確認できます。 編集内容を手動でスクロールすることも、[チャットの編集] ナビゲーション バーを使って提案された変更を上下に移動することもできます。 編集内容は、質問モードでの分析の間に明らかになった修復戦略と一致しているはずです。

1. 変更を適用し、更新されたファイルを保存します。

    GitHub Copilot によって提案された編集内容を常に確認してください。

    提案された変更が正しいと考えられ、分析からの修復戦略と一致している場合は、**[保持]** を選んで PaymentService.cs と setup-secrets.ps1 ファイルに編集を適用します。 チャット ビューの **[保持]** ボタンを使うと、提案されたすべての変更 (すべてのファイル) を受け入れることができます。

    編集を受け入れると、ハードコーディングされたシークレットがソース コードから削除され、シークレットが環境変数を使って安全に管理されるようになります。

    > **注**:提案された変更に問題がある場合は、**[元に戻す]** を選んで編集を拒否できます。 必要に応じて、コードを手作業で変更することもできます。 変更を受け入れた後で問題が見つかった場合は、チャット ビューの **[最後の要求を元に戻す]** 機能を使って、最新の変更を元に戻すことができます。 Visual Studio Code の元に戻す機能を使って、コードの変更を元に戻すこともできます。

1. EmailService クラスに関連するセキュリティ アラートを修復するタスクを GitHub Copilot に割り当てます。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Now review the EmailService.cs file. Remove hard-coded Mailgun and SMTP secrets from the EmailService class and implement environment variables that securely manage the secrets. Update the setup-secrets.ps1 script for the new environment variables, and ensure that the formatting matches the existing environment variable declarations. Ensure that the suggested updates don't introduce errors and run a build task to verify the app builds correctly after the updates.
    ```

1. タスクを実行し、エージェントの進行状況を監視します。

    エージェントは、EmailService.cs ファイルの分析と、ハードコーディングされたシークレットを削除するためのコード変更の提案を始めます。 エージェントから特定のファイルにアクセスするための支援またはアクセス許可を求められた場合は、必要に応じてアクセス許可を付与し、エージェントがタスクを完了できるようにします。

1. しばらく時間を取って、提案された変更を検討してから、更新を受け入れてください。

    編集内容は、質問モードでの分析の間に明らかになった修復戦略と一致しているはずです。 提案された変更が正しいと考えられ、分析からの修復戦略と一致している場合は、**[保持]** を選んで EmailService.cs と setup-secrets.ps1 ファイルに編集を適用します。 ハードコーディングされたシークレットがソース コードから削除され、環境変数を使って安全に管理されるようになるはずで。

1. Program.cs と AppConfig.cs で構成設定の検証に使われているコードを更新するタスクを、GitHub Copilot に割り当てます。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    I need you to ensure that the app's configuration is validated correctly and that the app runs as expected. Review the Program.cs and AppConfig.cs files. Update the ValidateRequiredConfiguration method to include validation for all environment variables before starting the workflow. Update the ValidateRequiredConfiguration method to include error messages for any missing environment variable. Update console logging in Program.cs to use safe placeholder values for secrets. Also, update the AppConfig class to include static properties for the new secrets, loaded from configuration/environment variables. The validation in AppConfig.cs should also be updated to check all environment variables. Ensure that the suggested updates don't introduce errors and run a build task to verify the app builds correctly after the updates.
    ```

1. タスクを実行し、エージェントの進行状況を監視します。

    エージェントは、Program.cs と AppConfig.cs ファイルの分析と、構成検証ロジックを更新するためのコード変更の提案を始めます。 進行状況がチャット ビューで報告されます。 GitHub Copilot によって提案されたコードの編集が、コード エディターに表示されます。

    > **注**:エージェントは、セットアップ スクリプト (setup-secrets.ps1) とアプリケーションを同じ PowerShell セッションで実行できない場合があります。 (セットアップ スクリプトを使わずに) アプリケーションを実行して、検証ロジックの動きを見ることはできるはずです。

1. 少し時間を取って、提案された変更を確認して適用した後、更新されたファイルを保存します。

    シークレットを管理するために追加された新しい環境変数を検証するように、Program.cs ファイルが更新されているはずです。 構成の検証に失敗したときに表示されるエラー メッセージも、ないシークレットを含めるように更新されています。

    AppConfig.cs ファイルは、構成と環境変数から読み込まれた新しいシークレットのための静的プロパティを含むように、更新されているはずです。 プロパティを追加すると、シークレットへのアクセスが一元化され、保守できるようになります。 AppConfig.cs の更新により、適切な検証、より簡単な構成管理、向上したセキュリティ プラクティスが実現されます。

    提案された変更が正しいと考えられ、分析からの修復戦略と一致する場合は、**[保持]** を選んで編集を適用します。 ファイルを保存します。

1. 変更後のアプリケーションが正常に動作していることを確認するには、ターミナルで次のコマンドを入力します。

    統合ターミナルを開いて、次のコマンドを実行します。

    ```powershell
    . .\setup-secrets.ps1
    cd ContosoOrderProcessor
    dotnet run
    ```

    アプリケーションで構成が検証され、シミュレートされた注文処理ワークフローがエラーなしで実行される必要があります。

1. コンソール出力を、プロジェクトのルートにある **RemediatedConsoleOutput.txt** という名前のファイルに保存します。

    このファイルを作成するには、エクスプローラー ビューで右クリックして **[新しいファイル]** を選び、**RemediatedConsoleOutput.txt** という名前を付けてから、コンソール出力をそのファイルにコピーします。

1. **OriginalConsoleOutput.txt** と **RemediatedConsoleOutput.txt** ファイルを比べて、シークレット スキャンのアラートを修復した後もアプリケーションの動作が変わっていないことを確認します。

    コンソール出力では、修復前と修復後のどちらも、足りない構成またはシークレットに関するエラーなしで、アプリケーションが正常に動作していることが示されている必要があります。

    RemediatedConsoleOutput.txt ファイルでは、修復されたシークレットが表示されなくなっている必要があります。

1. 2 つのコンソール出力ファイルを比較し、シークレット スキャンのアラートを修復した後もアプリケーションの動作が同じであることを確認するよう、エージェントに指示します。

    たとえば、チャットに次のプロンプトを入力します。

    ```plaintext
    Compare the OriginalConsoleOutput.txt and RemediatedConsoleOutput.txt files. Verify that the application behavior remains consistent after remediating the secret scanning alerts. Highlight any differences in behavior or output.
    ```

1. GitHub Copilot による 2 つのコンソール出力ファイルの比較を確認します。

    シークレット スキャンのアラートを修復した後も、アプリケーションの動作が同じであることが、GitHub Copilot によって確認されている必要があります。 どちらの場合も注文処理ワークフローが正常に実行され、足りない構成やシークレットに関連するエラーがないことが明示されている必要があります。 出力の違いはすべて、コンソール ログからの機密情報の削除に関連するものである必要があります。

1. 2 つの出力ファイルと setup-secrets.ps1 スクリプト ファイルを削除します。

    これらのファイルは、シークレット スキャンのアラートを修復する前と後にアプリケーションを実行して検証するためにのみ必要でした。 リポジトリをきれにしておくために、これらを削除する必要があります。

### 変更を GitHub にプッシュして、シークレット スキャンのアラートを閉じる

ハードコーディングされたシークレットを削除した後は、変更をコミットして GitHub にプッシュする必要があります。 これらのアクションを使用すると、セキュリティ アラートが、修復されたコードに基づいて GitHub でどのように更新されるかを確認できます。

このタスクでは、コードの更新をコミットしてプッシュしてから、GitHub シークレット スキャンのセキュリティ アラートを閉じるプロセスを確認します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code でソース管理ビューを開きます。

    左側のアクティビティ バーから [ソース管理] アイコンを選択するか、`Ctrl+Shift+G` (Windows/Linux) または `Cmd+Shift+G` (Mac) キーを押します。

1. 少し時間を取って、ファイルの変更を確認してください。

    **[変更]** の下に、修正されたファイル (Program.cs、AppConfig.cs、EmailService.cs、PaymentService.cs) の一覧が表示されます。 AppConfig.cs には、プッシュ保護をテストするために意図的に修正しなかったハードコーディングされたシークレットが、まだいくつか含まれています。

1. コミット メッセージとステージを作成し、変更をコミットします。

    AI で自動化された **[コミット メッセージの生成]** 機能を使ってみるか、カスタム コミット メッセージを入力します。

    独自のコミット メッセージを入力することもできます。 次に例を示します。

    ```plaintext
    Replace hard-coded secrets with environment variables in PaymentService and EmailService classes. Update configuration validation in Program.cs and AppConfig.cs. 
    ```

    コミット メッセージが作成されたら、**[コミット]** ボタン (チェックマーク アイコン) を選択します。 変更をステージングするように求められたら、**[はい]** を選びます。

1. 変更を GitHub にプッシュ (または同期) します。

    [ソース管理] ビューの **[変更の同期]** ボタンまたは **[プッシュ]** オプションを選択します。 コマンド ラインを使用している場合は、以下を実行できます。

    ```bash
    git push origin main
    ```

    > **注**:リポジトリに対してプッシュ保護が既に有効になっている場合は、シークレットを追加するのではなく、コードから削除しているためブロックされません。

1. プッシュまたは同期が完了するまで待ちます。

1. GitHub でリポジトリを開きます。

1. [セキュリティ] タブを開きます。

    リポジトリ ページの上部にある **[セキュリティ]** タブを選択します。

1. 左側のサイド バーから **[シークレット スキャン]** を選択します。

1. シークレット スキャンで同じシークレットにまだフラグが設定されていることに注意してください。

    シークレット スキャンでは、最新のコミットでシークレットを削除した後でも、過去のコミットでシークレットにフラグを設定し続けます。 この動作は仕様であり、公開されたシークレットが適切に修復されたことを確認するためです。

    セキュリティ アラートの管理に関するベスト プラクティスを次に示します。

    1. アラートの詳細を確認する: セキュリティ アラートを開いて検出されたシークレットを確認し、最新のコミットにそれらが存在しなくなったことを確認します。

    1. シークレットを無効にする (まだ行っていない場合): 公開されたシークレットが機密性の高いシステムのものである場合は (API キー、資格情報など)、ローテーションまたは無効化します。 シークレットをコードから削除しても、侵害されてしまっている場合は保護されません。

    1. 対処を文書化する: 修復について説明するコメントをアラートに含めます (コミット SHA や日付など)。 これは、監査と将来の参照に役立ちます。

    1. アラートを抑制または無視する: シークレットが完全に修復 (削除およびローテーション) された場合は、GitHub でそのセキュリティ アラートを無視できます。 適切な理由 ("失効" や "テストで使用" など) を選択して、他のユーザーがコンテキストを理解できるようにします。

    1. 履歴を保護する (必要に応じて): パブリック リポジトリや機密性の高いケースでは、Git 履歴を書き直してシークレットを完全に削除することを検討してください。 **重要**: Git 履歴を書き換えると、共同作成者に対して再クローンが強制され、フォークが中断される可能性があります。

    1. 将来のリークを監視する: リポジトリでのシークレット スキャンを有効にしたままにします。 将来、シークレットが予期せずコミットされるのを防ぐため、プッシュ保護を有効にすることを検討します。

    概要:シークレットを削除してローテーションした後でのみ、アラートを抑制または無視します。 対応を文書化し、可能であればチームに連絡します。 注意: 履歴を書き換えない限り、無視されたシークレットも過去のコミットに表示されます。

1. 修復されたシークレットに対するシークレット スキャンのアラートを閉じます。

    修復された各アラートを閉じるには、次の手順のようにします。

    1. アラートを開いて詳細を確認します。

    1. 関連付けられているコード ファイルを開き、現在のコミットではシークレットがコードから削除されていることを確認します。

    1. アラートに戻り、**[Close as]** を選んでから、適切な理由 (**[Revoked]** や **[Used in test]** など) を選びます。 選んだ理由は、他のユーザーがコンテキストを理解するのに役立ちます。

    1. 行った修復手順を要約したコメントを入力します。 コミット番号と日付を含めます。

    1. **[Close alert]** を選びます。

    Slack と Twilio のシークレットは修復されませんでした。 次のタスクで GitHub のプッシュ保護機能をテストするため、それらのアラートは未対応のままにします。

### GitHub のプッシュ保護機能をテストする

GitHub のプッシュ保護機能により、シークレットが誤ってリポジトリにプッシュされるのを防ぐことができます。 有効にすると、既知のシークレット パターンを探してコミットがスキャンされ、シークレットが検出された場合はプッシュがブロックされ、リポジトリ履歴に入る前に削除する機会が提供されます。

このタスクでは、リポジトリに対してプッシュ保護を有効にし、ダミー シークレットを含むコミットをプッシュしてテストします。

そのためには、以下の手順を実行してください。

1. Visual Studio Code で ContosoOrderProcessor アプリに戻ります。

1. **Configuration/AppConfig.cs** ファイルをコード エディターで開きます。

1. 下にスクロールして、**SlackBotToken** 定数の宣言ステートメントを見つけます。

1. 割り当てられている SlackBotToken の 1 桁を別の値に変更します。

    このアクションは、開発者がハードコーディングされた新しいシークレットを誤ってコードに追加する場合をシミュレートします。

1. AppConfig.cs ファイルを保存します。

1. ソース管理ビューを開き、変更をステージングしてコミットします。

    たとえば、コミット メッセージを入力し、**[コミット]** を選んでから、メッセージが表示されたら **[はい]** を選んで変更をステージングします。

1. コミットの同期またはプッシュを試みます。

    たとえば、**[変更の同期]** を選んでから、**[OK]** を選んでコミットをプッシュします。

1. GitHub プッシュ保護機能によってプッシュがブロックされることに注意してください。

    ダイアログ ボックスに "参照仕様をリモートにプッシュできません" というメッセージが表示されます。

1. ダイアログ ボックスで **[Git ログを開く]** を選びます。

    ログでは、シークレットが検出されたためにプッシュが拒否されたことが示されているはずです。 エラー メッセージには、検出されたシークレット パターンが一覧表示され、続行方法に関する指示が示されます。

1. ソース管理ビューを使ってコミットを修正し、AppConfig.cs で SlackBotToken の変更を元に戻します。

    たとえば、ソース管理ビューで次のようにします。**[前回のコミットを元に戻す]** を選び、**[すべての変更のステージング解除]** を選んで、**[変更の破棄]** を選びます。

プッシュ保護機能により、シークレットがリポジトリにプッシュされるのを防ぐことができます。 実際のシナリオでは、この機能は、API キー、トークン、パスワード、その他の機密情報の誤ったコミットを、リポジトリ履歴の一部になる前にキャッチします。

## クリーンアップ

演習が完了したので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに保持したくない変更が加えられていないか確認します。 たとえば、ResolveGitHubSecurityAlerts リポジトリを削除することが必要になる可能性があります。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したリポジトリのローカル クローンをアーカイブするか、削除することができます。
