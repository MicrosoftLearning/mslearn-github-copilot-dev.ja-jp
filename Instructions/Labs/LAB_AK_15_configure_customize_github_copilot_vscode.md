<!-- ---
lab:
    title: 'Exercise - Configure and customize GitHub Copilot in Visual Studio Code'
    description: 'Learn how to configure a C# project to use custom GitHub Copilot instructions and create custom agents that collaborate through handoffs.'
    level: 300
    duration: 50 minutes
--- -->

# Visual Studio Code で GitHub Copilot を構成してカスタマイズする

GitHub Copilot は、すぐに使える強力な AI 支援によるコーディングを提供しますが、チームの特定のワークフローとプロジェクトの要件に合わせてカスタマイズすると、その真の可能性が発揮されます。 カスタム指示を提供し、特殊なエージェントを作成すると、GitHub Copilot を汎用アシスタントから、コードベースを理解し、規則に従い、マルチステップの開発タスクを処理する、調整された AI コラボレーターのセットに変換できます。

この演習では、カスタムの GitHub Copilot 指示を使用するように ContosoInventory C# Web API プロジェクトを構成し、ハンドオフを通じて共同作業を行い、開発タスクをエンドツーエンドで完了するカスタム エージェントを作成します。

この演習の所要時間は約 **50** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。

- Git 2.48 以降。
- .NET SDK バージョン 8.0 以降。
- GitHub Copilot が有効になっている GitHub アカウントへのアクセス。
- C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code。

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 この会社は、Contoso の IT 部門向けに ContosoInventory Web アプリケーション (ASP.NET Core バックエンドを備えた Blazor WebAssembly アプリケーション) を開発しました。 このアプリケーションは、組織全体で使用される機器を追跡するための在庫カテゴリを管理します。 クライアントには特定のコーディング標準、アーキテクチャ パターン、レビュー プロセスがあり、あなたは、各カテゴリ内で個々の製品を追跡できるようにする、製品在庫の管理機能を追加するよう依頼されました。

あなたは、GitHub Copilot のカスタマイズ機能を使用して開発を加速すると同時に、すべてのコードがクライアントの標準に準拠していることを確認することにしました。

1. すべての AI 生成のコードが確立された規則に従えるように、クライアントのコーディング標準を GitHub Copilot の動作に埋め込むカスタム指示ファイルを作成します。
1. 特定の開発ロール用の次のカスタム エージェントを定義します。実装計画を設計する "Planner"、コードを記述する "Implementer"、コード品質をチェックする "Reviewer" です。
1. ハンドオフを使用してこれらのエージェントを連携させ、計画から実装、レビューまでの構造化されたマルチステップ ワークフローを作成します。

ContosoInventory アプリケーションは、次の 3 つのプロジェクト アーキテクチャを使用します。

- **ContosoInventory.Server**:Entity Framework Core、ID 認証、SQLite を備えた ASP.NET Core Web API。
- **ContosoInventory.Client**:ブラウザーで実行され、サーバー API を呼び出す Blazor WebAssembly SPA。
- **ContosoInventory.Shared**:モデル、DTO、列挙型を含む共有クラス ライブラリ。

このラボ演習では、2 つのユーザー アカウント (Mateo Gomez と Megan Bowen) を使用してアプリケーションをテストします。 Mateo には管理者ロールがあり、カテゴリに対するすべての CRUD 操作を実行できます。 Megan には閲覧者ロールがあり、カテゴリの表示のみができます。

この演習には、次のタスクが含まれています。

1. ContosoInventory アプリケーションの機能を確認します。
1. コーディング標準を適用するためのリポジトリ レベルのカスタム指示を作成します。
1. ターゲット ガイダンス用のパス固有の指示ファイルを作成します。
1. 共通タスク用の再利用可能なプロンプト ファイルを作成します。
1. 読み取り専用ツールを備えた "Planner" カスタム エージェントを定義します。
1. 編集機能を備えた "Implementer" カスタム エージェントを定義します。
1. コード品質チェック用の "Reviewer" カスタム エージェントを定義します。
1. チェーン化されたエージェント ワークフローを実行して、開発タスクをエンドツーエンドで完了します。

## ContosoInventory アプリケーションの機能を確認する

コーディング標準を適用し、開発ワークフローをガイドする GitHub Copilot カスタマイズ ファイルを追加する前に、ContosoInventory アプリケーションをダウンロードして確認する必要があります。

そのためには、以下の手順を実行してください。

1. ブラウザー ウィンドウを開いて GitHub.com に移動します。

    次の URL を使用して GitHub アカウントにログインできます。<a href="https://github.com/login" target="_blank">GitHub ログイン</a>。

1. GitHub アカウントにサインインし、[リポジトリ] タブを開きます。

    右上隅にあるプロファイル アイコンをクリックし、**[リポジトリ]** を選択して、[リポジトリ] タブを開きます。

1. [リポジトリ] タブで、**[新規]** ボタンを選択します。

1. **[新しいリポジトリの作成]** セクションで、**[リポジトリのインポート]** を選択します。

1. **[Import your project to GitHub]** ページで、**[Your source repository details]** のソース リポジトリに次の URL を入力します。

    ```plaintext
    https://github.com/MicrosoftLearning/github-copilot-customization-starter-app
    ```

1. **[新しいリポジトリの詳細]** セクションの **[所有者]** ドロップダウンで、GitHub ユーザー名を選択します。

1. **[リポジトリ名]** フィールドに「**ContosoInventory**」と入力します

    GitHub で、リポジトリ名の可用性が自動的にチェックされます。 この名前が既に使用されている場合は、リポジトリ名に一意のサフィックス (イニシャルや乱数など) を追加して一意にします。

1. プライベート リポジトリを作成するには、**[プライベート]** を選択し、**[インポートの開始]** を選択します。

    GitHub では、インポート プロセスを使用して、アカウントに新しいリポジトリを作成します。 インポート プロセスが完了するまでに 1、2 分かかる場合があります。 インポート プロセスが完了するまで待ちます。

    > **重要**:GitHub Copilot Free プランを使用している場合は、リポジトリを **Public** として作成して、GitHub Copilot 機能に確実にアクセスできるようにする必要があります。 Pro、Pro+、Business、または Enterprise サブスクリプションをお持ちの場合は、リポジトリを **Private** として作成できます。

    GitHub には進行状況のインジケーターが表示され、インポートが完了すると通知されます。

1. インポートが完了したら、新しいリポジトリを開きます。

    リポジトリへのリンクが表示されます。 リポジトリは次の場所にあるはずです: `https://github.com/YOUR-USERNAME/ContosoInventory`。

1. ContosoInventory リポジトリ ページで、**[コード]** ボタンを選択し、HTTPS URL をコピーします。

    URL は次のようになります。`https://github.com/YOUR-USERNAME/ContosoInventory.git`

1. 開発環境でターミナル ウィンドウを開き、リポジトリのローカル クローンを作成する場所に移動します。

    次に例を示します。

    ```powershell
    cd C:\TrainingProjects
    ```

    `C:\TrainingProjects` を希望の場所に置き換えます。 書き込みアクセス許可がある任意のディレクトリを使用でき、必要に応じて新しいフォルダーの場所を作成できます。

1. ContosoInventory リポジトリを複製するには、次のコマンドを入力します。

    コマンドを実行する前に、必ず `YOUR-USERNAME` を実際の GitHub ユーザー名に置き換えてください。

    ```powershell
    git clone https://github.com/YOUR-USERNAME/ContosoInventory.git
    ```

    クローン操作中に、GitHub 資格情報を使用して認証するように求められる場合があります。 ブラウザーを使用して認証できます。

1. ContosoInventory ディレクトリに移動し、Visual Studio Code で開くには、次のコマンドを入力します。

    ```powershell
    cd ContosoInventory
    code .
    ```

1. Visual Studio Code のエクスプローラー ビューでプロジェクト フォルダーを展開します。

    **ContosoInventory** フォルダーの下に、次の 3 つのプロジェクト フォルダーが表示されます。**ContosoInventory.Server**、**ContosoInventory.Client**、**ContosoInventory.Shared**。

1. 少し時間を取って、プロジェクト構造を確認してください。

    プロジェクト フォルダーを展開します。 次のようなフォルダー構造が表示されます。

    ```plaintext
    ContosoInventory/
    ├── ContosoInventory.Server/
    │   ├── App_Data/                           (SQLite database file location)
    │   ├── Controllers/
    │   │   ├── AuthController.cs               (Login, Logout, GetCurrentUser)
    │   │   └── CategoriesController.cs         (CRUD + toggle-active)
    │   ├── Data/
    │   │   ├── InventoryContext.cs             (EF Core DbContext)
    │   │   ├── DbInitializer.cs               (Database seeding)
    │   │   └── Migrations/                     (EF Core migrations)
    │   ├── Models/
    │   │   └── Category.cs                     (Category entity)
    │   ├── Services/
    │   │   ├── ICategoryService.cs             (Category service interface)
    │   │   └── CategoryService.cs              (Category service implementation)
    │   ├── Properties/
    │   │   └── launchSettings.json
    │   ├── appsettings.json
    │   ├── appsettings.Development.json
    │   ├── ContosoInventory.Server.csproj
    │   └── Program.cs
    ├── ContosoInventory.Client/
    │   ├── Layout/
    │   │   ├── MainLayout.razor                (Application shell with nav bar)
    │   │   └── NavMenu.razor                   (Navigation menu component)
    │   ├── Pages/
    │   │   ├── Home.razor                      (Dashboard with category stats)
    │   │   ├── Login.razor                     (Login form)
    │   │   └── Categories.razor                (Category management page)
    │   ├── Services/
    │   │   ├── CategoryApiService.cs           (HTTP client for category API)
    │   │   └── CookieAuthStateProvider.cs      (Custom AuthenticationStateProvider)
    │   ├── wwwroot/
    │   │   ├── css/
    │   │   │   └── app.css                     (Application styles)
    │   │   └── index.html                      (Blazor host page)
    │   ├── _Imports.razor
    │   ├── App.razor                           (Root component with routing)
    │   ├── ContosoInventory.Client.csproj
    │   └── Program.cs
    ├── ContosoInventory.Shared/
    │   ├── DTOs/
    │   │   ├── CategoryResponseDto.cs
    │   │   ├── CreateCategoryDto.cs
    │   │   ├── UpdateCategoryDto.cs
    │   │   ├── LoginDto.cs
    │   │   └── UserInfoDto.cs
    │   ├── ContosoInventory.Shared.csproj
    │   └── GlobalUsings.cs
    └── ContosoInventory.sln
    ```

1. **ContosoInventory.Server/Program.cs** ファイルを開き、アプリケーションの構成を確認します。

    次の主要な構成領域に注目してください。

    - データ アクセス用の SQLite を備えた Entity Framework Core
    - Cookie ベース セッションとアカウント ロックアウトを使用した認証用の ASP.NET Core Identity
    - `ICategoryService` のサービス登録
    - 起動時に `DbInitializer.InitializeAsync` を使用するデータベース シード処理
    - CORS、レート制限、CSRF 保護、セキュリティ ヘッダー ミドルウェア

1. **ContosoInventory.Server/Controllers/CategoriesController.cs** ファイルを開き、既存の API エンドポイントを確認します。

    カテゴリ コントローラーにより、次のエンドポイントが提供されます。

    - `GetAllCategories`:表示順に並べ替えられたすべてのカテゴリを取得する
    - `GetCategoryById`:ID で特定のカテゴリを取得する
    - `CreateCategory`:新しいカテゴリを作成する (管理者のみ)
    - `UpdateCategory`:既存のカテゴリを更新する (管理者のみ)
    - `DeleteCategory`:カテゴリを削除する (管理者のみ)
    - `ToggleActive`:カテゴリのアクティブな状態を切り替える (管理者のみ)

1. **ContosoInventory.Server/Services/CategoryService.cs** ファイルを開き、サービスの実装を確認します。

    使用されている次のパターンに注目します。すべてのデータベース操作の async/await、DTO マッピング、構造化ログ、入力検証、try-catch ブロックを使用したエラー処理。 演習の後半で製品機能を作成するときに、GitHub Copilot エージェントで、これらのパターンが参照されます。

1. Visual Studio Code でターミナルを開き、ソリューションをビルドします。

    ```powershell
    cd ContosoInventory/ContosoInventory.Server
    dotnet build
    ```

    > **重要**:プロジェクトでは、既定で .NET 8 が使用されます。 .NET 9 または .NET 10 SDK がインストールされているが、.NET 8 がインストールされていない場合は、インストールした .NET のバージョンを対象とするようにプロジェクトを更新する必要があります。 AI のサポートにより.NET の新しいバージョンに更新するには、GitHub Copilot Chat ビューを開き、プロジェクト ファイルを環境にインストール済みの .NET のバージョンに更新するように、GitHub Copilot に依頼します。

    ビルドはエラーなしで正常に完了します。

1. サーバー アプリケーションを起動するには、ターミナルで次のコマンドを入力します。

    ```powershell
    dotnet run
    ```

    > **注**:アプリケーションを初めて実行するときは、データベースの移行を適用し、サンプル データを使用してデータベースをシード処理するのに少し時間がかかる場合があります。 データベースが初期化およびシード処理されたことを示すコンソール出力が表示されます。 サーバーが `http://localhost:5240` でリッスンを開始するメッセージも表示されます。

1. ブラウザーを開き、 `http://localhost:5240` に移動します。

    アプリケーションが開き、ContosoInventory ログイン ページが表示されます。 両方のテスト ユーザーのデモ用資格情報を表示した **Lab Exercise — Test Accounts** 情報カードと共にログイン フォームが表示されます。

1. ログイン ページに表示される管理者のデモ用資格情報を使用してサインインします。

1. ホーム ページにカテゴリの統計情報が表示されることを確認します。

    ウェルカム メッセージと統計情報が表示され、カテゴリが合計 9 個あり、アクティブなものが 9 個、非アクティブは 0 個です。

1. **[カテゴリ]** ページに移動し、9 個のカテゴリがすべて一覧表示されていることを確認します。

    ノート PC とデスクトップ、モニターとディスプレイ、ネットワーク機器などのカテゴリが表示されます。 管理者には、[カテゴリの追加]、[編集]、[非アクティブ化/アクティブ化]、[削除] といったアクション ボタンも表示されます。

1. ログアウトして、閲覧者の資格情報を使用してサインインします。

1. **[カテゴリ]** ページに移動し、管理者専用のアクション ボタン ([カテゴリの追加]、[編集]、[非アクティブ化/アクティブ化]、[削除]) が表示されていないことを確認します。

    アプリ ユーザーが閲覧者ロールの場合、ユーザーはカテゴリ一覧を表示できますが、データを変更することはできません。

1. アプリケーションからログアウトします。

1. アプリケーションを停止するには、サーバーが実行されている Visual Studio Code 統合ターミナルに戻り、**Ctrl + C** キーを押します。

    > **注**:次のタスクのために、ターミナルを開いたままにしておくことができます。

1. GitHub Copilot が Visual Studio Code でアクティブであることを確認します。

    Visual Studio Code ウィンドウの下部にあるステータス バーで GitHub Copilot アイコンを探します。 アイコンが表示されていて、警告はありません。 GitHub Copilot がアクティブでない場合は、アクティビティ バーの [アカウント] アイコンを使用して GitHub アカウントにサインインします。

1. GitHub Copilot Chat ビューを開くには、Visual Studio Code ウィンドウの上部にある [チャットの切り替え] アイコンを選択するか、**Ctrl + Alt + I** キーを押します。

    GitHub Copilot Chat が正常に開かれたことを確認します。 この演習では、GitHub Copilot Chat を使用します。

## コーディング標準を適用するためのリポジトリ レベルのカスタム指示を作成する

このタスクでは、プロジェクトの常時有効なガイドラインを GitHub Copilot に提供する `.github/copilot-instructions.md` ファイルを作成します。 これらの指示は、ワークスペース内のすべての GitHub Copilot Chat 要求に自動的に含まれます。

そのためには、以下の手順を実行してください。

1. カスタム指示を手動で作成する前に、`/init` ショートカットを試して、GitHub Copilot で自動的に生成される内容を確認してみてください。

    GitHub Copilot Chat ビューを開き、次のコマンドを入力します。

    ```plaintext
    /init
    ```

    GitHub Copilot は、C# 言語、ASP.NET Core フレームワーク、Entity Framework Core、プロジェクト構造を検出してワークスペースを分析し、見つけた内容に合わせて調整されたスターター `copilot-instructions.md` ファイルを生成します。

    > **注**:GitHub Copilot でコードベースが分析されて指示が生成されるまで数分かかる場合があります。 生成された指示は役立つ出発点ですが、クライアント固有のコーディング標準やアーキテクチャ パターンを反映していません。 次の手順の指示をカスタマイズして、プロジェクトの正確な要件が反映されるようにします。

1. 生成された指示を確認します。

    `/init` で生成されたコンテンツを調べます。 GitHub Copilot は、検出されたテクノロジ スタックの一般的なベスト プラクティスを推測しますが、プライベート フィールドのアンダースコア プレフィックス、リポジトリ パターン要件、xUnit テスト規則など、クライアント固有のコーディング標準については認識していません。 `/init` コマンドは便利な開始点ですが、クライアントの正確な要件が反映されている手動で作成した指示に置き換えます。

1. 生成されたファイルを削除して、最初から作成できるようにします。

    `/init` コマンドにより `.github` フォルダーに `copilot-instructions.md` ファイルが作成された場合は、ファイルを削除して、フォルダーは保持します。

1. Visual Studio Code のエクスプローラー ビューを使用して、プロジェクトのルートに **.github** フォルダーがあることを確認します。

    > **注**:フォルダー名はピリオドで始まる必要があります。 これは、GitHub で構成ファイルに使用される規則であり、ここが GitHub Copilot 指示ファイルの標準的な場所です。

1. **.github** フォルダーを右クリックし、**[新しいファイル]** を選択します。

1. ファイルに、**copilot-instructions.md** という名前を付けます。

1. **copilot-instructions.md** ファイルに次の内容を追加します。

    ```markdown
    # Contoso Inventory API - Coding Standards

    ## Naming Conventions
    - Use PascalCase for class names, public methods, and public properties.
    - Use camelCase for local variables and method parameters.
    - Prefix private fields with an underscore (e.g., _inventoryService).
    - Suffix interface names with the interface prefix "I" (e.g., IInventoryService).

    ## Architecture Patterns
    - Follow the repository pattern for all data access operations.
    - Use dependency injection for all service dependencies. Register services in Program.cs.
    - Separate business logic into service classes. Controllers should only handle HTTP concerns.
    - Use DTOs (Data Transfer Objects) for API request and response payloads. Never expose database entities directly.

    ## Error Handling
    - Use try-catch blocks for all external API calls and database operations.
    - Return appropriate HTTP status codes (200 for success, 400 for bad requests, 404 for not found, 500 for server errors).
    - Log all exceptions using ILogger<T> with structured logging.
    - Include meaningful error messages in API responses.

    ## Documentation
    - Include XML documentation comments on all public methods and classes.
    - Use inline comments only for complex business logic that is not self-explanatory.

    ## Testing
    - Write unit tests using xUnit and Moq.
    - Follow the Arrange-Act-Assert pattern in test methods.
    - Name test methods using the pattern: MethodName_Scenario_ExpectedResult.
    ```

1. ファイルの内容が左揃えになっていることを確認し、ファイルを保存します。

1. VS Code の設定を開きます

    キーボード ショートカット **Ctrl** + **,** を使用して設定を開くか、または、左下隅にある歯車アイコンをクリックしてメニューから **[設定]** を選択することができます。

1. **[設定の検索]** フィールドに、「**指示ファイルを使用する**」と入力します。

1. チェックボックスの [コード生成:指示ファイルを使用] がオンになっていることを確認します。

    この設定 (github.copilot.chat.codeGeneration.useInstructionFiles) は既定で有効になっていますが、以前にオフに切り替えた場合、copilot-instructions.md は読み込まれません。

1. [設定] タブを閉じます。

1. GitHub Copilot でカスタム指示が使用されていることを確認するには、GitHub Copilot Chat ビューを開き、次のプロンプトを入力します。

    ```plaintext
    What naming convention should I use for private fields in this project?
    ```

1. 少し時間を取って、GitHub Copilot からの応答を確認してください。

    GitHub Copilot がカスタム指示を参照し、プライベート フィールドのプレフィックスとしてアンダースコアを付けるというガイダンスで応答します。 GitHub Copilot Chat の応答で、`copilot-instructions.md` を引用する **[参照]** セクションを探して、指示が適用されていることを確認します。

    > **注**:参照されている指示が表示されない場合は、`chat.includeApplyingInstructions` の設定が Visual Studio Code の設定で有効になっていることを確認します。 この設定は既定でオンになっています。

1. 指示の別の側面をテストするには、チャットで次のプロンプトを入力します。

    ```plaintext
    How should I structure data access in this project? What pattern should I follow?
    ```

1. 少し時間を取って、GitHub Copilot からの応答を確認してください。

    GitHub Copilot から、リポジトリ パターンに従うように応答があるはずです。

> **注**:Visual Studio Code では、`AGENTS.md` ファイルと `CLAUDE.md` ファイルも常に有効な指示として認識されます。 ワークスペースのルートに配置された `AGENTS.md` ファイルは、`copilot-instructions.md` と同様に機能し、複数の AI ツールを操作し、それらのすべてで認識される 1 つの指示セットが必要な場合に便利です。 さらに、入れ子になった `AGENTS.md` ファイルをサブディレクトリに配置して、GitHub Copilot がそのディレクトリまたはその子の中のファイルを操作する場合にのみ適用されるコンテキスト固有の指示を提供できます。 この演習では、GitHub Copilot カスタマイズの標準的なアプローチである `.github/copilot-instructions.md` 規則を使用します。

## ターゲット ガイダンス用のパス固有の指示ファイルを作成する

このタスクでは、コードベースの特定の部分を対象としたコーディング ガイドラインを提供する `.instructions.md` ファイルを作成します。 これらのファイルを使用すると、プロジェクトのコントローラー、サービス、およびその他の領域に対してさまざまなルールを定義できます。

そのためには、以下の手順を実行してください。

1. **.github** フォルダーに、**instructions** という名前の新しいフォルダーを作成します。

1. **.github/instructions/** フォルダーに、**controllers.instructions.md** という名前の新しいファイルを作成します。

1. **controllers.instructions.md** に次のコンテンツを追加します。

    ```markdown
    ---
    name: 'API Controller Standards'
    description: 'Coding standards for ASP.NET Core API controllers'
    applyTo: '**/Controllers/**/*.cs'
    ---
    # API Controller Standards

    - Inherit from ControllerBase and apply the [ApiController] attribute.
    - Use attribute routing with [Route("api/[controller]")] on the class.
    - Keep controller methods thin: delegate business logic to service classes.
    - Use action-specific attributes: [HttpGet], [HttpPost], [HttpPut], [HttpDelete].
    - Return IActionResult or ActionResult<T> from all action methods.
    - Include [ProducesResponseType] attributes to document response types.
    - Validate model state using data annotations on request DTOs.
    - Inject services through the constructor, not directly in action methods.
    ```

1. ファイルの内容が左揃えになっていることを確認し、ファイルを保存します。

1. **.github/instructions/** フォルダーに、**services.instructions.md** という名前の新しいファイルを作成します。

1. **services.instructions.md** に次のコンテンツを追加します。

    ```markdown
    ---
    name: 'Service Layer Standards'
    description: 'Coding standards for business logic service classes'
    applyTo: '**/Services/**/*.cs'
    ---
    # Service Layer Standards

    - Define a corresponding interface for every service class (e.g., IProductService for ProductService).
    - Use async/await for all I/O-bound operations (database calls, HTTP requests, file I/O).
    - Accept and return DTOs, not database entities.
    - Include comprehensive input validation at the start of each public method.
    - Throw specific exception types (ArgumentException, InvalidOperationException) rather than generic Exception.
    - Log significant operations using ILogger<T> with structured logging parameters.
    ```

1. ファイルの内容が左揃えになっていることを確認し、ファイルを保存します。

1. 指示ファイルが正しく読み込まれているかどうかを確認するには、Chat ビューで右クリックし、**[Diagnostics]** を選択します。

1. GitHub Copilot Chat で、現在のセッションに関する診断情報を含む新しいエディター タブが開かれることに注目します。

1. 少し時間をかけて、チャット カスタマイズ診断レポートを確認してみましょう。

    [指示] セクションには、ワークスペースの指示、copilot-instructions.md などで見つかったすべての指示ファイルが一覧表示されます。現在適用されているかどうかは関係ありません。 "3 個のファイルが読み込まれました" と表示されます。つまり、Copilot で 3 つの指示ファイルが検出され、そのレジストリに読み込まれています。

    > **注**:GitHub Copilot で `applyTo` パターンに一致するファイルが処理される場合、パス固有の指示がリポジトリ全体の指示と自動的にマージされます。 `description` フィールドはセマンティック マッチングにも使用されるため、一致するファイルが開かれていない場合でも、GitHub Copilot にコントローラーまたはサービスについて質問すると、これらの指示が含まれる場合があります。

    エディターで "ContosoInventory/ContosoInventory.Server/Controllers/CategoriesController.cs" ファイルを開き、GitHub Copilot にコーディング ガイダンスを依頼する場合は、応答で `copilot-instructions.md` と `controllers.instructions.md` の両方が参照されている必要があります。 サービス ファイルを開くと、`copilot-instructions.md` と `services.instructions.md` が参照されているはずです。 これは、パス固有の指示がファイル コンテキストに基づいて正しく適用されていることを示しています。

## 共通タスク用の再利用可能なプロンプト ファイルを作成する

このタスクでは、API エンドポイントのドキュメントを生成するための再利用可能なスラッシュ コマンドを定義するプロンプト ファイルを作成します。 プロンプト ファイルは共通タスクを標準化し、チームでの実行に一貫性が得られます。

そのためには、以下の手順を実行してください。

1. **.github** フォルダーに、**prompts** という名前の新しいフォルダーを作成します。

1. **.github/prompts/** フォルダーに、**generate-api-docs.prompt.md** という名前の新しいファイルを作成します。

1. **generate-api-docs.prompt.md** に次のコンテンツを追加します。

    ```markdown
    ---
    description: 'Generate API documentation for the active controller file'
    agent: 'copilot'
    tools: ['search', 'read']
    ---
    # Generate API Documentation

    Analyze the controller in ${file} and generate comprehensive API documentation.

    For each endpoint in the controller:
    1. List the HTTP method and route.
    2. Describe the purpose of the endpoint.
    3. Document the request parameters, body, and query string inputs.
    4. Document the possible response status codes and their meanings.
    5. Provide an example request and response in JSON format.

    Format the output as a Markdown document suitable for a developer wiki.
    ```

    `${file}` 構文は **prompt ファイル変数**であり、エディターで現在開いているファイルに自動的に解決されます。 コントローラー ファイルを開いてこのプロンプトを実行すると、GitHub Copilot はそのファイルの完全な内容をコンテキストとして受け取ります。 プロンプト ファイルでは、`${selection}` (現在選択されているテキスト) とカスタム変数もサポートされており、プロンプトの実行時にユーザーに入力を求めます。

    frontmatter の `agent` フィールドは、スラッシュ コマンドが呼び出されたときにプロンプトを処理するエージェントを指定します。 ここでは `copilot` (既定のエージェント) に設定されていますが、カスタム エージェント名に設定できます。たとえば、この演習の後半で Reviewer エージェントを作成した後、これを `agent: 'reviewer'` に変更して、Reviewer のレンズを通じてドキュメントを生成することができます。 frontmatter では、推奨される AI モデルを指定するためのオプションの `model` フィールドもサポートされています。

1. ファイルの内容が左揃えになっていることを確認し、ファイルを保存します。

1. プロンプト ファイルがスラッシュ コマンドとして登録されていることを確認するには、GitHub Copilot Chat ビューを開き、チャット入力に「`/`」と入力します。

    使用可能なスラッシュ コマンドの一覧に `generate-api-docs` が表示されます。

    > **注**:プロンプト ファイルがスラッシュ コマンド リストに表示されない場合は、`chat.promptFiles` 設定が Visual Studio Code 設定で有効になっていることを確認します。 これを確認するためには、[設定] (**Ctrl** + **,**) を開き、"プロンプト ファイル" を検索します。

1. プロンプト ファイルをテストするには、エディターで **ContosoInventory.Server/Controllers/CategoriesController.cs** ファイルを開き、GitHub Copilot Chat で次のコマンドを入力します。

    ```plaintext
    /generate-api-docs
    ```

1. 少し時間を取って、GitHub Copilot からの応答を確認してみましょう。

    GitHub Copilot で CategoriesController が分析され、6 つのエンドポイントすべて (GET all、GET by ID、POST、PUT、DELETE、toggle-active) の書式設定された API ドキュメントが作成されます。 これは、既存のコントローラーに、異なる HTTP メソッド、承認レベル、応答の種類を持つ複数のエンドポイントがあるため、プロンプト ファイルによりチーム全体で共通タスクが標準化され、意味のある出力が表示される方法を示しています。

1. `${file}` 変数が正しく解決されていることに注目します。

    GitHub Copilot の応答の上部近くにあるドキュメントでは、エディターで開いたファイル `CategoriesController.cs` を具体的に参照する必要があります。 `${file}` 変数は、コントローラーの内容をコンテキストとしてプロンプトに提供しました。 すべてのエディター タブを閉じて `/generate-api-docs` をもう一度実行すると、GitHub Copilot には分析する特定のコントローラーが存在しないため、より汎用的または精度の低い応答が生成されます。これは、変数が意味のあるコンテキストを提供することを示しています。

## 読み取り専用ツールを備えた "Planner" カスタム エージェントを定義する

このタスクでは、計画アシスタントとして機能するカスタム エージェントを作成します。 Planner エージェントは、機能の要件を分析し、コードを記述または編集することなく実装計画を生成します。 コードベースからコンテキストを収集するための、読み取り専用ツールのみを使用します。

そのためには、以下の手順を実行してください。

1. **.github** フォルダーに、**agents** という名前の新しいフォルダーを作成します。

1. **.github/agents/** フォルダーに、**planner.agent.md** という名前の新しいファイルを作成します。

1. **planner.agent.md** に次のコンテンツを追加します。

    ```markdown
    ---
    description: Analyzes feature requirements and generates implementation plans without writing code
    tools: ['search', 'read', 'fetch']
    handoffs:
      - label: Start Implementation
        agent: implementer
        prompt: "Implement the plan outlined above. Follow the project's custom instructions for coding standards. Create all necessary files including models, DTOs, services, interfaces, and controllers."
        send: false
      - label: Write Tests First
        agent: implementer
        prompt: "Before implementing the feature, write unit tests based on the plan outlined above. Use xUnit and Moq following the project's testing conventions. Create test classes that cover the service methods and controller actions described in the plan. Do not implement the production code yet—only the tests."
        send: false
    ---
    # Planner

    You are a senior software architect working on a C# ASP.NET Core Web API project. When the user describes a feature or change, analyze the request and generate a detailed implementation plan.

    Before creating a plan, always search the existing codebase to understand the current project structure, coding patterns, and dependencies already in place.

    Your plan MUST include:
    1. **Summary**: A brief overview of the feature and its purpose.
    2. **Files to create or modify**: A complete list of files that need to be created or changed, with their full paths.
    3. **Implementation steps**: Step-by-step tasks in logical dependency order. Each step should specify what to do and which file to work in.
    4. **Models and DTOs**: Define the data structures needed, including property names and types.
    5. **Service interface and implementation**: Outline the service methods needed and their signatures.
    6. **Controller endpoints**: List the API endpoints to create, including HTTP methods, routes, request/response types, and status codes.
    7. **Dependency injection**: Specify any service registrations needed in Program.cs.
    8. **Risks and considerations**: Highlight potential issues, edge cases, or decisions that need input.

    IMPORTANT RULES:
    - Do NOT write or modify any code. Focus on planning only.
    - Do NOT create files. Your role is advisory.
    - Follow the project's established architecture patterns and coding standards.
    - Ask clarifying questions if the requirements are ambiguous.
    - Reference existing code patterns in the project for consistency.
    ```

1. ファイルの内容が左揃えになっていることを確認し、ファイルを保存します。

1. GitHub Copilot Chat の [エージェント] ドロップダウンに [Planner] エージェントが表示されることを確認します。

    GitHub Copilot Chat ビューで、[エージェント] ドロップダウンを開きます (通常、"GitHub Copilot" または現在のエージェントの名前が表示されます)。 使用可能なエージェントの一覧に **planner** が表示されます。

    > **ヒント**:GitHub Copilot Chat に「`/agents`」と入力して、使用可能なすべてのエージェントをすばやく表示し、切り替えることもできます。

1. **planner** エージェントを選択します。

    チャットの入力プレースホルダー テキストが "機能要件を分析し、コードを記述せずに実装計画を生成します" に変わることに注目します。これは、エージェントの frontmatter で定義した説明です。

    > **注**:Planner エージェントは、次の **2 つのハンドオフ**を定義します。"実装を開始する" と "最初にテストを記述する" です。 どちらも Implementer エージェントを対象としますが、プロンプトは異なります。1 つは実稼働コードを記述し、もう 1 つは最初にテストを記述するためのプロンプトです。 これにより、計画を確認した後、開発アプローチ (実装優先またはテスト駆動型) を柔軟に選択できます。 Planner が後のタスクで応答を生成した後に、両方のハンドオフ ボタンが表示されます。

    > **ヒント**:各ハンドオフ エントリでは、ワークフローのそのステージに対して異なる AI モデルを指定するオプションの `model` フィールドもサポートされています。 形式は `model: "Model Name (vendor)"` です (`model: "GPT-4.1 (OpenAI)"` や `model: "Claude Sonnet 4 (Anthropic)"` など)。 これは、さまざまなワークフロー ステージがさまざまなモデルの機能の恩恵を受ける場合に便利です。たとえば、計画には推論モデルを、実装には高速モデルを使用します。 使用可能なモデルは、お使いの GitHub Copilot サブスクリプションによって異なります。

1. Planner エージェントをテストするには、次のプロンプトを入力します。

    ```plaintext
    Analyze the project structure and describe the current architecture, including any existing models, services, and controllers. Identify the patterns used in the Category feature implementation.
    ```

1. 少し時間を取って、Planner エージェントからの応答を確認してみましょう。

    Planner エージェントは、`search` と `read` ツールを使用してプロジェクト ファイルを調べ、現在のアーキテクチャの詳細な概要を提供します。 カテゴリ モデル、CategoryService、CategoriesController、DTO パターン、依存関係挿入のセットアップ、3 つのプロジェクトアーキテクチャ全体を識別します。 エージェントには読み取り専用ツールしかないため、ファイルの変更は試行されません。

1. [エージェント] ドロップダウンを開き、**[Agent]** を選択して、既定の GitHub Copilot エージェントに戻ります。

## 編集機能を備えた "Implementer" カスタム エージェントを定義する

このタスクでは、計画または指示に基づいてコード変更を実装するカスタム エージェントを作成します。 Implementer エージェントには編集機能があり、コーディング標準に関するプロジェクトのカスタム指示に従います。

そのためには、以下の手順を実行してください。

1. **.github/agents/** フォルダーに、**Implementer.agent.md** という名前の新しいファイルを作成します。

1. **Implementer.agent.md** に次のコンテンツを追加します。

    ```markdown
    ---
    description: Implements code changes based on plans, following the project coding standards
    tools: ['search', 'read', 'edit', 'terminal']
    handoffs:
      - label: Review Code
        agent: reviewer
        prompt: "Review the code changes made in the implementation above. Check for bugs, security issues, naming convention violations, and adherence to the project's coding standards defined in the custom instruction files."
        send: false
    ---
    # Implementer

    You are an expert C# developer working on an ASP.NET Core Web API project. Your role is to implement code changes based on plans, feature requests, or bug fix descriptions.

    WORKFLOW:
    1. Read the plan or request carefully before writing any code.
    2. Search the existing codebase to understand current patterns, naming conventions, and dependencies.
    3. Implement changes following the project's established patterns and the coding standards defined in the custom instruction files.
    4. Create files in the correct project directories.
    5. After completing the implementation, provide a summary of all files created or modified.

    IMPLEMENTATION RULES:
    - Follow the repository pattern for data access. Create an interface and implementation for each service.
    - Use dependency injection. Register new services in Program.cs.
    - Use DTOs for API request and response payloads. Never expose database entities directly.
    - Include XML documentation comments on all public methods and classes.
    - Prefix private fields with an underscore.
    - Use PascalCase for public members, camelCase for local variables.
    - Include proper error handling with try-catch blocks for database and external API calls.
    - Use async/await for I/O-bound operations.
    - Return appropriate HTTP status codes from controller actions.
    ```

1. ファイルの内容が左揃えになっていることを確認し、ファイルを保存します。

1. **implementer** エージェントがエージェントのドロップダウンに表示されることを確認します。

    GitHub Copilot Chat の [エージェント] ドロップダウンを開きます。 これで、**planner** と **implementer** の両方が一覧表示されているはずです。

1. Planner エージェントをもう一度開き、ハンドオフ構成を確認します。

    Planner エージェントには、次の 2 つのハンドオフ ボタンが含まれていることに注目してください。**"実装を開始する"** と **"最初にテストを記述する"** です。 どちらのハンドオフも Implementer エージェントを対象としますが、プロンプトは異なります。 "実装を開始する" ハンドオフは、Implementer に実稼働コードの記述を指示します。一方、"最初にテストを記述する" では、実稼働コードの前に単体テストを記述するように指示します。 これは、開発者が状況に基づいて適切な次のステップを選択できるようにする**複数のハンドオフ**を 1 つのエージェントが提供する方法を示しています。

## コード品質チェック用の "Reviewer" カスタム エージェントを定義する

このタスクでは、バグ、セキュリティの問題、コーディング標準への準拠についてコードを確認するカスタム エージェントを作成します。 Reviewer エージェントは読み取り専用ツールのみを使用するため、分析中に誤ってコードが変更されないようになっています。

そのためには、以下の手順を実行してください。

1. **.github/agents/** フォルダーに、**reviewer.agent.md** という名前の新しいファイルを作成します。

1. **reviewer.agent.md** に次のコンテンツを追加します。

    ```markdown
    ---
    description: Reviews code for bugs, security issues, and coding standards compliance
    tools: ['search', 'read']
    handoffs:
      - label: Fix Issues
        agent: implementer
        prompt: "Fix the issues identified in the code review above. Address each finding in order of severity, starting with Critical and High issues first."
        send: false
    ---
    # Code Reviewer

    You are an experienced code reviewer specializing in C# and ASP.NET Core applications. When asked to review code, examine it thoroughly for issues across the following categories:

    ## Review Checklist
    1. **Bugs and logical errors**: Look for null reference risks, off-by-one errors, race conditions, and incorrect logic.
    2. **Security vulnerabilities**: Check for SQL injection, missing input validation, hardcoded secrets, missing authentication/authorization, and insecure data handling.
    3. **Naming convention violations**: Verify adherence to the project's naming standards (PascalCase for public members, underscore prefix for private fields, etc.).
    4. **Architecture compliance**: Confirm the code follows the repository pattern, uses dependency injection, and separates concerns properly.
    5. **Error handling**: Ensure try-catch blocks are present for external calls, appropriate HTTP status codes are returned, and exceptions are logged.
    6. **Missing documentation**: Flag public methods or classes that lack XML documentation comments.
    7. **Performance issues**: Identify unnecessary allocations, missing async/await, or inefficient queries.

    ## Output Format
    Present your findings as a structured review:
    - Group findings by severity: **Critical**, **High**, **Medium**, **Low**
    - For each finding, include:
      - The file and location
      - A description of the issue
      - A suggested fix
    - End with an **Overall Assessment** summarizing the code quality and any patterns of concern.

    IMPORTANT: Do NOT modify any files. Your role is advisory only.
    ```

1. ファイルの内容が左揃えになっていることを確認し、ファイルを保存します。

1. [エージェント] ドロップダウンに 3 つのエージェントがすべて表示されていることを確認します。

    GitHub Copilot Chat の [エージェント] ドロップダウンを開きます。 **planner**、**implementer**、**reviewer** が組み込みエージェントと共に一覧表示されます。

1. 少し時間を取って、構成したエージェント チェーンを確認しましょう。

    ハンドオフ構成では、次のワークフローが作成されます。

    - **Planner** → (実装の開始) → **Implementer**:Planner で計画が作成されたら、Implementer に渡すとコードが記述されます。
    - **Implementer** → (コードの確認) → **Reviewer**:Implementer でコードが記述されたら、Reviewer に渡すとコードの品質が確認されます。
    - **Reviewer** → (問題の修正) → **Implementer**:Reviewer で問題が見つかった場合は、Implementer に戻って修正プログラムを適用できます。

    各ハンドオフでは `send: false` が使用されます。つまり、送信する前に確認し、編集するためのプロンプトが事前に入力されています。 これにより、ワークフローのすべてのステップを制御できます。

    > **注**:`send` フィールドは、ハンドオフ プロンプトが自動的に送信されるかどうかを制御します。 `false` (既定値) に設定すると、送信前に確認したり、必要に応じて編集したりすることができるように、プロンプトがチャット入力に事前入力されます。 `true` に設定すると、プロンプトがすぐに送信され、ターゲット エージェントは承認を待たずにすぐに作業を開始します。 この演習のすべてのハンドオフでは、`send: false` を使用しており、それぞれの切り替えを制御できます。 エージェント チェーンが監督なしで動作することを信頼する、完全に自動化されたパイプラインでは、`send: true` がワークフローを合理化できますが、ループ内の人間によるチェックポイントがなくなります。

    > **注**:既定では、カスタム エージェントはクライアント (Visual Studio Code 内) で実行されます。 機能全体の構築や包括的なテスト スイートの実行など、実行時間の長いタスクの場合は、エージェントの YAML frontmatter に `target: cloud` を設定して、エージェントをリモートで実行できます。 クラウド エージェントは、エージェントがバックグラウンドで処理している間、ローカルの Visual Studio Code インスタンスを解放します。 バックグラウンド エージェントは関連する概念です。チャット インターフェイスをブロックせずに独立して実行されるため、エージェントがタスクを完了している間も作業を続けられます。 使用可能な実行環境は、GitHub Copilot サブスクリプションによって異なります。

## チェーン化されたエージェント ワークフローを実行して、開発タスクをエンドツーエンドで完了する

このタスクでは、カスタム エージェントを使用して完全な計画 → 実装 → レビューのワークフローを実行し、新しい在庫管理機能をプロジェクトに追加します。 これは、エージェントのチェーンが構造化されたマルチステップの開発プロセスを作成する方法を示しています。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat ビューを開き、[エージェント] ドロップダウンから **planner** エージェントを選択します。

1. 次のプロンプトを入力して、機能計画を要求します。

    ```plaintext
    I need to add a Product Inventory management feature to this Web API, following the same patterns used by the existing Category feature. The feature should support:
    - A Product model with: Id (int), Name (string), Sku (string), Description (string), Price (decimal), StockQuantity (int), CategoryId (int, foreign key to Category), CreatedDate (DateTime), LastUpdatedDate (DateTime).
    - CRUD operations for products (Create, Read by ID, Read all with optional filtering by CategoryId, Update, Delete).
    - A restock endpoint that increases the StockQuantity for a specific product.
    - Input validation on all create/update operations.
    - Authorization: Admin role required for create, update, delete, and restock operations. All authenticated users can read.
    - Use Entity Framework Core with the existing SQLite database (add Product to the DbContext and create a migration).
    - Follow the same service interface, DTO, and controller patterns used in the Category feature.
    ```

    Planner エージェントは、既存のコードベースを検索し、カテゴリ実装パターン (モデル、DTO、サービス インターフェイス、サービス実装、コントローラー) を分析し、確立されたアーキテクチャを反映する詳細な実装計画を作成する必要があります。

1. 少し時間をかけて、Planner エージェントによって生成されたプランを確認しましょう。

    計画が次の点を満たしていることを確認します。

    - 作成するすべてのファイル (製品モデル、製品 DTO、IProductService インターフェイス、ProductService 実装、ProductsController) を一覧表示する。
    - カスタム指示の名前付け規則に従う (PascalCase、プライベート フィールドのアンダースコア プレフィックス)。
    - 既存の CategoryService で確立されたのと同じサービス レイヤー パターンを使用する。
    - エンティティを直接公開するのではなく、API ペイロード用の DTO を含める。
    - Product と Category の間の CategoryId 外部キー リレーションシップを認識する。
    - Program.cs で依存関係挿入の登録を指定する。
    - Product テーブルを SQLite データベースに追加するための移行手順を含める。
    - 認可要件に言及する (書き込み操作の場合は管理者のみ)。

    > **注**:計画がカスタム指示または既存のカテゴリ パターンと一致しない場合は、Planner に変更を依頼できます。 例:"個別の CreateProductDto、UpdateProductDto、ProductResponseDto 型など、カテゴリ機能で使用されているのと同じ DTO パターンに従うように計画を修正してください。"

1. 計画に問題がなければ、**[実装の開始]** ハンドオフボタンを選択します。

    Planner の応答の最後に、次の 2 つのハンドオフ ボタンが表示されます。**"実装を開始する"** と **"最初にテストを記述する"** です。 **"実装を開始する"** を選択して、実装優先のアプローチに進みます。 選択すると、GitHub Copilot Chat は次のように動作します。

    - **Implementer** エージェントに切り替わります。
    - 完全な計画を含めた会話履歴を引き継ぎます。
    - プロンプト テキストに事前入力します。"上記に説明されている計画を実装してください..."

    > **注**:テスト駆動型の開発アプローチを希望する場合は、[最初にテストを記述する] ボタンを使用できます。 実稼働コードの前に単体テストを作成することに重点を置く別のプロンプトが Implementer に送信されます。 この演習では、"実装を開始する" を使用してメイン ワークフローを続行します。

1. 入力済みのプロンプトを確認し、**[送信]** を選択して (または **Enter**キーを押して) 送信します。

    Implementer エージェントは、Planner の計画に基づいてコードの記述を開始します。 モデル、DTO、サービス インターフェイス、サービス実装、コントローラーを作成します。

1. Implementer エージェントが実装を進める間、更新をチャットで監視します。

    ファイルが作成され、カスタム指示ファイルのコーディング標準に従っていることを確認します。

    > **注**:計画の複雑さによっては、実装に数分かかる場合があります。 Implementer エージェントは、`edit` および `terminal` ツールを使用してファイルを作成し、ビルド コマンドを実行する可能性があります。

1. Implementer が完了したら、作成されたファイルの概要を確認します。

    Implementer は、次のようなファイルを作成します (正確な名前と場所は、計画によって異なる場合があります)。

    - **ContosoInventory.Server/Models/Product.cs**:CategoryId 外部キーを持つ製品エンティティ。
    - **ContosoInventory.Shared/DTOs/ProductResponseDto.cs** (または同様):応答 DTO。
    - **ContosoInventory.Shared/DTOs/CreateProductDto.cs** (または同様):検証属性を持つ作成用 DTO。
    - **ContosoInventory.Shared/DTOs/UpdateProductDto.cs** (または同様):検証属性を持つ更新用 DTO。
    - **ContosoInventory.Server/Services/IProductService.cs**:サービス インターフェイス。
    - **ContosoInventory.Server/Services/ProductService.cs**:EF Core を使用したサービスの実装。
    - **ContosoInventory.Server/Controllers/ProductsController.cs**:CRUD、再ストック、認可を備えた API コントローラー。
    - 更新された **ContosoInventory.Server/Data/InventoryContext.cs**:`DbSet<Product>` とモデルの構成を追加。
    - 更新された **ContosoInventory.Server/Program.cs**:依存関係の挿入によるサービスの登録。
    - Product テーブルの新しい EF Core 移行。

1. プロジェクトをビルドして、生成されたコードがコンパイルされることを確認します。

    ターミナルで、**ContosoInventory.Server** ディレクトリにいることを確認してから、次のように入力します。

    ```powershell
    dotnet build
    ```

    ビルドにエラーがある場合は、Implementer エージェントに修正を依頼できます。 例:"ビルドに次のエラーがあります。 修正してください。" 次に、エラー出力を貼り付けます。

1. **[コードのレビュー]** ハンドオフ ボタンを選択して、Reviewer エージェントに切り替えます。

    Reviewer エージェントは、計画と実装を含む完全な会話コンテキストを受け取ります。 事前入力されたプロンプトにより、コードの変更を確認するように求められます。

1. 入力済みのプロンプトを確認し、**[送信]** を選択して送信します。

    Reviewer エージェントが、作成されたすべてのファイルを調べ、バグ、セキュリティの問題、名前付け規則違反、プロジェクトのコーディング標準への準拠を確認します。 重大度別にグループ化された結果が表示されます。

1. 少し時間をかけて、Reviewer の調査結果を確認しましょう。

    レビューでは、次の点を確認する必要があります。

    - 名前付け規則が、カスタム指示と一致している (パブリック メンバーの場合は PascalCase、プライベート フィールドの場合はアンダースコア プレフィックス)。
    - サービス レイヤー パターンが、既存の CategoryService と一貫している (インターフェイス/実装の分離)。
    - DTO は、エンティティではなく API ペイロードに使用されている。
    - エラー処理に、try-catch ブロックと適切な HTTP 状態コードが含まれている。
    - 公開メソッドに XML ドキュメントのコメントが存在する。
    - 入力の検証は、作成/更新操作に実装される。
    - 認可属性が正しく適用されている (書き込み操作の場合は管理者のみ)。
    - CategoryId 外部キーリレーションシップが正しく構成されている。

    > **注**:Reviewer は、Implementer が見逃した問題を特定できる場合があります。 これは想定内であり、専門化されたエージェントが互いの作業をレビューする価値を示しています。 Reviewer は、新しい製品コードを既存のカテゴリ実装と直接比較して一貫性を保つことができます。

1. Reviewer で問題が特定され場合は、**[問題の修正]** ハンドオフ ボタンを選択します。

    これにより、レビューで特定された問題を修正するためのプロンプトが表示されるとともに、Implementer エージェントに再び引き継がれます。 Implementer は、各検索を重大度順に処理します。

1. 修正が適用されたら、プロジェクトが正常にビルドされたことを確認します。

    **ContosoInventory.Server** ディレクトリにいることを確認し、次のように入力します。

    ```powershell
    dotnet build
    ```

1. 必要に応じて、アプリケーションを実行して API をテストします。

    アプリケーションを起動します。

    ```powershell
    dotnet run
    ```

    アプリケーションの起動に失敗した場合は、Implementer エージェントに問題のデバッグを依頼できます。 例:"アプリケーションが起動に失敗し、次のエラーが発生します。 修正する方法を教えてください。" 次に、エラー出力を貼り付けます。

    アプリケーションが正常に起動した場合は、Swagger UI または Postman などのツールを使用して、新しい Product エンドポイントをテストできます。

    ブラウザーを開き、`http://localhost:5240/swagger` の Swagger UI に移動します。 既存の Category エンドポイントと共に、新しい Product エンドポイントをテストできます。 まず、`mateo@contoso.com` / `Password123!` で **POST /api/auth/login** を使用して管理者ユーザーとして認証し、次をテストします。

    - **POST /api/products**:JSON 本文を使用して新しい製品を作成します。 既存のカテゴリの有効な `CategoryId` を使用します (例: "ノート PC とデスクトップ" の場合は 1)。
    - **GET /api/products**:すべての製品を一覧表示します (サポートされている場合は、CategoryId でフィルター処理してみてください)。
    - **GET /api/products/{id}**:特定の製品を取得します。
    - **PUT /api/products/{id}**:製品を更新します。
    - **DELETE /api/products/{id}**:製品を削除します。
    - **POST /api/products/{id}/restock** (または同様):製品の在庫を補充します。

    テストが完了したら、ターミナルで **Ctrl + C** キーを押してアプリケーションを停止します。

## まとめ

この演習では、Visual Studio Code で ContosoInventory C# Web API プロジェクト用に GitHub Copilot を正常に構成し、カスタマイズしました。 あなた: 

- **ContosoInventory スターター アプリケーションをインポートして確認し** 既存の 3 つのプロジェクト アーキテクチャ (サーバー、クライアント、共有)、カテゴリ機能の実装、ロールベースの承認によるセキュリティ構成を理解しました。
- **スターター指示ファイルを自動生成するための `/init` ショートカットを調べ**、クライアント固有のコーディング標準 (名前付け規則、アーキテクチャ パターン、エラー処理、ドキュメント要件) を含む**リポジトリレベルのカスタム指示 (`.github/copilot-instructions.md`) を作成**し、GitHub Copilot Chat のすべての対話に埋め込みました。
- **コントローラーとサービスのターゲット ガイダンスを提供するパス固有の指示ファイル** (`.github/instructions/*.instructions.md`) を作成し、`applyTo` glob パターンを使用して特定のファイルの場所に一致させました。
- **API ドキュメントの生成をスラッシュ コマンドとして標準化する再利用可能なプロンプト ファイル** (`.github/prompts/generate-api-docs.prompt.md`) を作成し、`${file}` 変数を使用してアクティブなエディター ファイルをコンテキストとして渡し、既存の CategoriesController に対してテストしました。
- **3 つのカスタム エージェント** (Planner、Implementer、Reviewer) を定義し、各開発ロールに合わせた指示、ツールのアクセス許可、動作ガイドラインを定義しました。
- 実装優先とテスト優先の両方のパスを提供する Planner エージェントで**複数のハンドオフ**を含むエージェント間の**ハンドオフを構成**し、各切り替え時に開発者の監視を含む構造化された計画 → 実装 → レビューのワークフローを作成しました。
- 既存のカテゴリ機能を参照アーキテクチャとして使用して、**完全なチェーン ワークフローを実行し**、製品在庫機能をエンドツーエンドで追加しました。 エージェントは、CategoryId 外部キー、DTO、サービス インターフェイスと実装、認可を持つコントローラーを使用して製品モデルを作成するために共同作業を行いました。いずれも、プロジェクトの確立されたコーディング標準に従っていました。

チームの知識を指示ファイルに埋め込み、ハンドオフを通じて専門化されたエージェントを調整するこのパターンは、あらゆる開発プロジェクトに適用できます。 これらの手法は、テスト駆動開発 (Test Writer → Implementer)、デバッグ ワークフロー (Debugger → Patcher → Tester)、移行プロジェクト (Analyzer → Upgrader → Reviewer) などの他のシナリオに適応できます。

## クリーンアップ

演習が完了したので、少し時間を取って環境をクリーンアップしましょう。

- アプリケーションがまだ実行中の場合は停止します (ターミナルで **Ctrl + C** キーを押します)。
- 必要に応じて、プロジェクト ディレクトリをアーカイブするか削除します。
- ご自分の GitHub アカウントでプライベート ContosoInventory リポジトリを作成した場合は、リポジトリの **[Settings]** タブに移動し、ページの下部にある **[Delete this repository]** を選択すると削除できます。
