---
lab:
  title: 演習 - GitHub Copilot SDK を使用して AI エージェントを既存のアプリに統合する
  description: GitHub Copilot SDK を使用して AI エージェントを既存のアプリケーションに統合し、タスクを自動化し、機能を強化する方法について学びます。
  level: 300
  duration: 60 minutes
---

# GitHub Copilot SDK を使用して AI エージェントを既存のアプリに統合する

GitHub Copilot SDK は、GitHub Copilot CLI の基盤となっているエンジンを、プログラミング可能な SDK として公開しています。 これにより、アプリケーションにエージェント型の AI ワークフローを埋め込むことができます。これには、AI にコードを呼び出させるカスタム ツールも含まれます。

この演習では、AI 搭載のカスタマーサポート エージェントを ContosoShop Eコマース サポート ポータルに統合します。 最終的に、[サポートへのお問い合わせ] ページで、ユーザーが質問をしたり (たとえば、"私の注文はどこにありますか?" または "商品を返品する必要があります")、AI エージェントから役に立つ自動化された回答を受けたりすることができるようになります。 エージェントは、バックエンド ツール (注文の状態の確認や返品の開始など) を使用してクエリを解決します。

この演習の所要時間は約 **60** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。

- Git 2.48 以降。
- .NET SDK バージョン 8.0 以降。
- GitHub Copilot が有効になっている GitHub アカウントへのアクセス。
- C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code。
- GitHub Copilot CLI がインストールされ、お使いの GitHub アカウントで認証済みです。

ラボ環境を構成する方法については、ブラウザーで次のリンクを開いてください: 「<a href="https://go.microsoft.com/fwlink/?linkid=2352210" target="_blank">GitHub Copilot SDK ラボ環境を構成する</a>」。

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 この会社は、クライアント向けに ContosoShop Eコマース サポート ポータル (ASP.NET Core バックエンドを備えた Blazor WebAssembly アプリケーション) を開発しました。 アプリケーション機能を使用すると、ユーザーは注文履歴の確認、発送の追跡、注文の詳細の確認、返品を (手動で) 行うことができます。 あなたは、クライアントから、AI 搭載のカスタマー サポート エージェントを [サポートへのお問い合わせ] ページに追加するように依頼されています。 エージェントは、注文の詳細の検索や返品の開始など、顧客に対して自動化された支援を提供する必要があります。 あなたは、GitHub Copilot SDK を使用して、顧客のクエリを処理し、顧客の代わりにアクションを実行できるカスタム AI エージェントを構築することにしました。

ContosoShop Eコマース サポート ポータル アプリケーションでは、次の 3 つのプロジェクト アーキテクチャを使用します。

- **ContosoShop.Server**:Entity Framework Core、ID 認証、SQLite を備えた ASP.NET Core Web API。
- **ContosoShop.Client**:ブラウザーで実行され、サーバー API を呼び出す Blazor WebAssembly SPA。
- **ContosoShop.Shared**:モデル、DTO、列挙型を含む共有クラス ライブラリ。

このラボ演習では、2 つのユーザー アカウント (Mateo Gomez と Megan Bowen) を使用してアプリケーションをテストします。 合計で 20 件の顧客注文が 2 つのユーザー アカウントに分割されます。 顧客の注文は、さまざまな追跡段階 (処理、発送済み、配達済み、返品済み、一部返品済み) にあります。

この演習には、次のタスクが含まれています。

1. ContosoShop アプリケーションの機能を確認します。
1. GitHub Copilot SDK コンポーネントをインストールします。
1. エージェント ツール サービスを作成します。
1. GitHub Copilot SDK エージェントを構成し、API エンドポイントを公開します。
1. Blazor フロントエンドを更新してエージェントとやり取りします。
1. エンドツーエンドの AI エージェント エクスペリエンスをテストします。

## ContosoShop アプリケーションの機能を確認する

AI カスタマー サポート エージェントを開発する前に、既存のアプリケーション機能について理解しておく必要があります。

そのためには、以下の手順を実行してください。

1. ブラウザー ウィンドウを開いて GitHub.com に移動します。

    次の URL を使用して GitHub アカウントにログインできます。<a href="https://github.com/login" target="_blank">GitHub ログイン</a>。

1. GitHub アカウントにサインインし、[リポジトリ] タブを開きます。

    右上隅にあるプロファイル アイコンをクリックし、**[リポジトリ]** を選択して、[リポジトリ] タブを開きます。

1. [リポジトリ] タブで、**[新規]** ボタンを選択します。

1. **[新しいリポジトリの作成]** セクションで、**[リポジトリのインポート]** を選択します。

1. **[Import your project to GitHub]** ページで、**[Your source repository details]** のソース リポジトリに次の URL を入力します。

    ```plaintext
    https://github.com/MicrosoftLearning/github-copilot-sdk-starter-app
    ```

1. **[新しいリポジトリの詳細]** セクションの **[所有者]** ドロップダウンで、GitHub ユーザー名を選択します。

1. **[リポジトリ名]** フィールドに「**ContosoShop**」と入力します

    GitHub で、リポジトリ名の可用性が自動的にチェックされます。 この名前が既に使用されている場合は、リポジトリ名に一意のサフィックス (イニシャルや乱数など) を追加して一意にします。

1. プライベート リポジトリを作成するには、**[プライベート]** を選択し、**[インポートの開始]** を選択します。

    GitHub では、インポート プロセスを使用して、アカウントに新しいリポジトリを作成します。 インポート プロセスが完了するまでに 1、2 分かかる場合があります。 インポート プロセスが完了するまで待ちます。

    > **重要**:GitHub Copilot Free プランを使用している場合は、リポジトリを **Public** として作成して、GitHub Copilot 機能に確実にアクセスできるようにする必要があります。 Pro、Pro+、Business、または Enterprise サブスクリプションをお持ちの場合は、リポジトリを **Private** として作成できます。

    GitHub には進行状況のインジケーターが表示され、インポートが完了すると通知されます。

1. インポートが完了したら、新しいリポジトリを開きます。

    リポジトリへのリンクが表示されます。 リポジトリは次の場所にあるはずです: `https://github.com/YOUR-USERNAME/ContosoShop`。

    ContosoShop リポジトリのローカル クローンを作成し、プロジェクト ディレクトリ内で GitHub Spec Kit を初期化できます。

1. ContosoShop リポジトリ ページで、**[コード]** ボタンを選択し、HTTPS URL をコピーします。

    URL は次のようになります。`https://github.com/YOUR-USERNAME/ContosoShop.git`

1. 開発環境でターミナル ウィンドウを開き、リポジトリのローカル クローンを作成する場所に移動します。

    次に例を示します。

    ターミナル ウィンドウ (コマンド プロンプト、PowerShell、またはターミナル) を開き、次を実行します。

    ```powershell
    cd C:\TrainingProjects
    ```

    `C:\TrainingProjects` を希望の場所に置き換えます。 書き込みアクセス許可がある任意のディレクトリを使用でき、必要に応じて新しいフォルダーの場所を作成できます。

1. ContosoShop リポジトリをクローンするには、次のコマンドを入力します。

    コマンドを実行する前に、必ず `YOUR-USERNAME` を実際の GitHub ユーザー名に置き換えてください。

    ```powershell
    git clone https://github.com/YOUR-USERNAME/ContosoShop.git
    ```

    クローン操作中に、GitHub 資格情報を使用して認証するように求められる場合があります。 ブラウザーを使用して認証できます。

1. ContosoShop ディレクトリに移動し、Visual Studio Code で開くには、次のコマンドを入力します。

    ```powershell
    cd ContosoShop
    code .
    ```

1. 少し時間を取って、プロジェクト構造を確認してください。

    Visual Studio Code のエクスプローラー ビューでプロジェクト フォルダーを展開します。 次の例のようなフォルダー構造が表示されます。

    ```plaintext
    github-copilot-sdk-starter-app (root)
    ├── ContosoShop.Client/               (Blazor WebAssembly frontend)
    │   ├── Layout/                       (MainLayout, NavMenu)
    │   ├── Pages/                        (Home, Login, Orders, OrderDetails, Support, Inventory)
    │   ├── Services/                     (OrderService, CookieAuthenticationStateProvider)
    │   └── Shared/                       (OrderStatusBadge)
    ├── ContosoShop.Server/               (ASP.NET Core backend)
    │   ├── App_Data/                     (used for the SQLite database file)
    │   ├── Controllers/                  (AuthController, OrdersController, InventoryController)
    │   ├── Data/                         (ContosoContext, DbInitializer, Migrations)
    │   ├── Services/                     (OrderService, InventoryService, EmailServiceDev)
    │   └── Program.cs                    (App configuration and middleware)
    ├── ContosoShop.Shared/               (Shared class library)
    │   ├── DTOs/                         (InventorySummary, ReturnItemRequest)
    │   └── Models/                       (Order, OrderItem, Product, User, etc.)
    └── ContosoShopSupportPortal.slnx     (Solution file)
    ```

1. **ContosoShop.Server/Program.cs** ファイルを開き、アプリケーションの構成を確認します。

    次の主要な構成領域に注目してください。

    - データ アクセス用の SQLite を備えた Entity Framework Core
    - cookie ベース セッションを使用する認証用の ASP.NET Core Identity
    - `IEmailService`、`IInventoryService`、`IOrderService` のサービスの登録
    - 起動時に `DbInitializer.InitializeAsync` を使用するデータベース シード処理
    - CORS、レート制限、クロスサイト リクエスト フォージェリ (CSRF) 保護、セキュリティ ヘッダー ミドルウェア

1. **ContosoShop.Server/Controllers/OrdersController.cs** ファイルを開き、既存の API エンドポイントを確認します。

    注文コントローラーは、注文を管理するための次のエンドポイントを提供します。

    - `GetOrders`:認証済みユーザーのすべての注文を取得する
    - `GetOrder`:特定の注文とアイテムを取得する (所有権を検証する)
    - `ReturnOrderItems`:配達済みの注文のアイテムレベルの返品を処理する

1. **ContosoShop.Server/Services/OrderService.cs** ファイルを開き、`ProcessItemReturnAsync` メソッドを確認します。

    ProcessItemReturnAsync メソッドは、注文アイテムに対する顧客の返品を処理します。 データ整合性を維持し、優れたカスタマー エクスペリエンスを提供しながら、返品が正しく処理されるように、いくつかの重要な操作を実行します。

    主な操作:

    - 注文が存在し、返品可能であることを検証します (Delivered/Returned/PartialReturn 状態)
    - 返品の数量が利用可能な量を超えないことを確認します
    - 払い戻し計算を使用して OrderItemReturn レコードを作成します
    - _inventoryService を使用して在庫のストックを復元します
    - 注文の状態を更新します (アイテムに基づいて Returned または PartialReturn)
    - 払い戻しの詳細を含む確認メールを送信します

1. **ContosoShop.Server** ディレクトリでターミナルを開き、ソリューションをビルドします。

    ```powershell
    cd ContosoShop.Server
    dotnet build
    ```

    > **重要**:プロジェクトでは、既定で .NET 8 が使用されます。 .NET 9 または .NET 10 SDK がインストールされているが、.NET 8 がインストールされていない場合は、インストールした .NET のバージョンを対象とするようにプロジェクトを更新する必要があります。 AI のサポートにより.NET の新しいバージョンに更新するには、GitHub Copilot Chat ビューを開き、プロジェクト ファイルを環境にインストール済みの .NET のバージョンに更新するように、GitHub Copilot に依頼します。 たとえば、次のように依頼できます。"この .NET 8 プロジェクトを .NET 10 をターゲットにするように更新しなければなりません。 次の内容を行ってください。1. すべての .csproj ファイルを net10.0 をターゲットにするように更新。2. すべての NuGet パッケージを .NET 10 互換バージョンに更新。3. global.json の更新 (ある場合)。4. .NET 8 から .NET 10 までの破壊的変更または非推奨の API に対処。5. ソリューション内のすべてのプロジェクトが正常にビルドされていることを確認。 重大な変更や、注意すべき潜在的な問題を説明してください。 プロンプトを入力すると、AI アシスタントによってコードベースが更新され、変更内容が説明されます。

    ビルドはエラーなしで正常に完了します (警告が表示される可能性があります)。

1. サーバー アプリケーションを起動します。

    ```powershell
    dotnet run
    ```

    > **注**:アプリケーションを初めて実行するときは、データベースの移行を適用し、サンプル データを使用してデータベースをシード処理するのに少し時間がかかる場合があります。 データベースが初期化およびシード処理されたことを示すコンソール出力が表示されます。 サーバーが `http://localhost:5266` でリッスンを開始するメッセージも表示されます。

1. ブラウザーを開き、 `http://localhost:5266` に移動します。

    アプリケーションが開き、ContosoShop ログイン ページが表示されます。 必要に応じて、localhost 開発証明書の証明書警告を受け入れます。

1. デモ用資格情報を使用してサインインします。

    メールに `mateo@contoso.com` を入力し、パスワードには `Password123!` を入力し、**[ログイン]** を選択します。

1. [自分の注文] ページに注文の一覧が表示されることを確認します。

    さまざまな状態 (処理中、発送済み、配達済み、返品済み) の Mateo の 10 件の注文が表示されます。

1. [自分の注文] ページで、注文 #1004 の **[詳細を表示する]** ボタンを選択します。

    アプリケーションが、選択した注文の注文の詳細ページに遷移するはずです。 このページには、注文の状態、注文日、合計金額、注文内のアイテムの一覧など、注文の概要が表示されます。

1. 少し時間をかけて注文の詳細を確認してから、**[返品]** ボタンを選択します。

    ページが更新され、**返品**列と**返品数** 列が表示されます。 **返品**列にはチェック ボックスがあり、**返品数**列には、返す数量を指定するための入力フィールドが含まれています。

1. **返品**列で、**モニター** アイテムのチェック ボックスをオンにし、対応する **[返品数]** フィールドに「**1**」と入力します。

    この選択は、注文から 1 つのモニター アイテムを返す必要があることを指しています。

1. **[返品 (1 アイテム)]** ボタンを選択します。

    アプリケーションは、返品要求を処理し、成功のメッセージを表示し、注文状態を "一部返品" に更新する必要があります。 モニター アイテムの注文の詳細には "3 点中 1 点返品" というバッジが表示され、返品済みの列には 1 つのモニター アイテムが返品されたことが表示されます。

1. Contoso 製品の在庫を表示するページを開くには、ナビゲーション メニューの **[在庫の表示]** を選択します。

    > **注**:[在庫管理] ページはラボで使用する目的でのみ含まれているため、返品が正しく処理されたことを確認できます。 このページには、利用可能な在庫を含む製品の一覧が表示されます。

1. 前の手順で返品を処理した後、モニター製品の在庫が 1 点補充されていることを確認します。

    **モニター** 製品の**返品済み**列 (アイテム番号:ITM-003) は、1 つのアイテムが返されたことが表示されます。 商品が返品されると、在庫は返品された数だけ補充されます。

1. [カスタマー サポート] ページを開くには、ナビゲーション メニューの **[サポートへのお問い合わせ]** を選択します。

    連絡先情報と、"対話型 AI チャット サポートが近日公開予定" というメッセージが表示されます。 今後のタスクでこの [カスタマー サポート] ページを更新します。 対応するプロジェクト ファイルは次のとおりです:**ContosoShop.Client/Pages/Support.razor**。

1. アプリケーションからログアウトするには、ナビゲーション メニューの **[ログアウト]** を選択します。

    アプリケーションでログアウトすると、[ログイン] ページに戻ります。

1. アプリケーションを停止するには、サーバーが実行されている Visual Studio Code 統合ターミナルに戻り、**Ctrl + C** キーを押します。

    > **注**:次のタスクのために、ターミナルを開いたままにしておくことができます。

## GitHub Copilot SDK コンポーネントをインストールする

このタスクでは、GitHub Copilot SDK NuGet パッケージと Microsoft.Extensions.AI パッケージをサーバー プロジェクトに追加します。 GitHub Copilot SDK は AI エージェントを構築するためのコア コンポーネントを提供しますが、Microsoft.Extensions.AI は、エージェントが呼び出すことができるカスタム ツールを定義するための型を提供します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code の統合ターミナルを開き、**ContosoShop.Server** ディレクトリを表示していることを確認します。

1. ターミナルで、GitHub Copilot CLI がインストールされ、認証されていることを確認するには、次のコマンドを入力します:

    ```powershell
    copilot --version
    ```

    バージョン番号 (`0.0.407` など) が表示されます。 コマンドが見つからない場合は、次の手順に従ってラボ環境の準備を完了します「<a href="https://go.microsoft.com/fwlink/?linkid=2352210" target="_blank">GitHub Copilot SDK ラボ環境を構成する</a>」。

    > **注**:GitHub Copilot SDK は、サーバー モードで Copilot CLI と通信します。 SDK により CLI プロセスライフサイクルが自動的に管理されますが、CLI をインストールして PATH でアクセスできる必要があります。

1. GitHub Copilot SDK NuGet パッケージをプロジェクトに構成するには、次のコマンドを入力します:

    ```powershell
    dotnet add package GitHub.Copilot.SDK --prerelease
    ```

    このコマンドにより、SDK の最新プレビュー バージョンがインストールされます。 SDK により、AI エージェントをビルドするために `CopilotClient`、`CopilotSession`、および関連する種類が提供されます。

    > **注**:GitHub Copilot SDK は Technical Preview の段階ですが、インストールには `--prerelease` フラグが必要です。

1. `Microsoft.Extensions.AI` パッケージをプロジェクトに追加するには、次のコマンドを入力します:

    ```powershell
    dotnet add package Microsoft.Extensions.AI
    ```

    GitHub Copilot SDK では、カスタム ツールを定義するために `Microsoft.Extensions.AI` が使用されます。 このパッケージには、AI エージェントが呼び出すことができるツールを作成するための `AIFunctionFactory` と関連する種類が用意されています。

1. パッケージが正しくインストールされていることを確認するには、プロジェクトをビルドします。

    ```powershell
    dotnet build
    ```

    ビルドはエラーなしで成功します。

## エージェント ツール サービスを作成する

このタスクでは、AI エージェントが注文を検索して返品を処理するために使用するツールを実装する新しいサービス クラスをサーバー プロジェクトに作成します。 このサービスは依存関係の挿入に登録され、ユーザー クエリを処理するときに AI エージェントによって呼び出されます。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューで、**ContosoShop.Server/Services** フォルダーを右クリックし、**[新しいファイル]** を選択します。

    このファイルを使用して、SupportAgentTools サービス クラスを作成します。

1. **SupportAgentTools.cs** ファイルに名前を付けます。

1. **SupportAgentTools.cs** ファイルに次のコードを追加します。

    ```csharp
    using ContosoShop.Server.Data;
    using ContosoShop.Shared.Models;
    using ContosoShop.Shared.DTOs;
    using Microsoft.EntityFrameworkCore;

    namespace ContosoShop.Server.Services;

    /// <summary>
    /// Provides tool functions that the AI support agent can invoke
    /// to look up order information and process returns.
    /// </summary>
    public class SupportAgentTools
    {
        private readonly ContosoContext _context;
        private readonly IOrderService _orderService;
        private readonly IEmailService _emailService;
        private readonly ILogger<SupportAgentTools> _logger;

        public SupportAgentTools(
            ContosoContext context,
            IOrderService orderService,
            IEmailService emailService,
            ILogger<SupportAgentTools> logger)
        {
            _context = context;
            _orderService = orderService;
            _emailService = emailService;
            _logger = logger;
        }

        // add the `GetOrderDetailsAsync` method here


        // add the `GetUserOrdersSummaryAsync` method here


        // add the `ProcessReturnAsync` method here


        // add the `SendCustomerEmailAsync` method here

    }
    ```

    このコードは、依存関係の挿入を使用してクラス スケルトンを設定します。 コンストラクターは、次の 4 つの依存関係を受け取ります:

    - `ContosoContext`: 注文とユーザーに直接クエリを実行するための Entity Framework Core データベース コンテキスト。
    - `IOrderService`: 返品処理ロジック、在庫の更新、メールの確認を処理する既存のサービス。
    - `IEmailService`: フォローアップ メールを顧客に送信するために使用されるサービス。
    - `ILogger<SupportAgentTools>`: 各ツール呼び出しを記録するためのロガー。これは、エージェント動作のデバッグと監視に役立ちます。

    これらの依存関係により、ツールは実際のデータにアクセスし、既存のビジネス ロジックを使うことできるため、それを複製する必要がなくなります。

1. SupportAgentTools クラス内 (コンストラクターの閉じ中かっこの後) に、次の GetOrderDetailsAsync メソッドを追加します。

    ```csharp
    /// <summary>
    /// Gets the status and details of a specific order by order ID.
    /// The AI agent calls this tool when a user asks about their order status.
    /// </summary>
    public async Task<string> GetOrderDetailsAsync(int orderId, int userId)
    {
        _logger.LogInformation("Agent tool invoked: GetOrderDetails for orderId {OrderId}, userId {UserId}", orderId, userId);

        var order = await _context.Orders
            .Include(o => o.Items)
            .FirstOrDefaultAsync(o => o.Id == orderId && o.UserId == userId);

        if (order == null)
        {
            return $"I could not find order #{orderId} associated with your account. Please double-check the order number.";
        }

        var statusMessage = order.Status switch
        {
            OrderStatus.Processing => "is currently being processed and has not shipped yet",
            OrderStatus.Shipped => order.ShipDate.HasValue
                ? $"was shipped on {order.ShipDate.Value:MMMM dd, yyyy} and is on its way"
                : "has been shipped and is on its way",
            OrderStatus.Delivered => order.DeliveryDate.HasValue
                ? $"was delivered on {order.DeliveryDate.Value:MMMM dd, yyyy}"
                : "has been delivered",
            OrderStatus.PartialReturn => "has been partially returned (some items have been returned, others are still with you)",
            OrderStatus.Returned => "has been fully returned and a refund was issued",
            _ => "has an unknown status"
        };

        var itemSummary = string.Join(", ", order.Items.Select(i =>
        {
            var itemInfo = $"{i.ProductName} (Id: {i.Id}, qty: {i.Quantity}, ${i.Price:F2} each";
            if (i.ReturnedQuantity > 0)
            {
                itemInfo += $", {i.ReturnedQuantity} returned, {i.RemainingQuantity} remaining";
            }
            itemInfo += ")";
            return itemInfo;
        }));

        return $"Order #{order.Id} {statusMessage}. " +
                $"Order date: {order.OrderDate:MMMM dd, yyyy}. " +
                $"Total: ${order.TotalAmount:F2}. " +
                $"Items: {itemSummary}.";
    }
    ```

1. 少し時間をかけて、GetOrderDetailsAsync メソッドを確認してみましょう。

    このコード スニペットは、最初のエージェント ツールです。 AI エージェントは、顧客が特定の注文について尋ねると、このメソッドを呼び出します。 このメソッドは、注文 (アイテムを含む) のデータベースに対してクエリを実行し、注文が `userId` を介して認証されたユーザーに属していることを確認し、自然言語応答を構築します。 C# `switch` 式は、一部のアイテムが返された注文の `PartialReturn` など、`OrderStatus` 列挙型を人間が判読できる語句に変換します。 アイテムの概要には、各製品とそのデータベース `Id`、数量、価格が一覧表示されます。一部が返品されたアイテムには、その `ReturnedQuantity` と `RemainingQuantity` も表示されます。 AI エージェントは、部分的な返品のために `process_return` ツールを呼び出すときに使用するため、アイテム `Id` を出力に含めることが重要です。 注文が見つからない場合、メソッドは例外をスローするのではなく、わかりやすいエラー メッセージを返します。AI エージェントによって戻り値が顧客に直接提示されるため、これは重要です。

1. GetOrderDetailsAsync メソッドの下のコード行に、次の GetUserOrdersSummaryAsync メソッドを追加します。

    ```csharp
    /// <summary>
    /// Gets a summary of all orders for a given user.
    /// The AI agent calls this tool when a user asks about their orders
    /// without specifying a particular order number.
    /// </summary>
    public async Task<string> GetUserOrdersSummaryAsync(int userId)
    {
        _logger.LogInformation("Agent tool invoked: GetUserOrdersSummary for userId {UserId}", userId);

        var orders = await _context.Orders
            .Where(o => o.UserId == userId)
            .OrderByDescending(o => o.OrderDate)
            .ToListAsync();

        if (!orders.Any())
        {
            return "You don't have any orders on file.";
        }

        var summaries = orders.Select(o =>
        {
            var status = o.Status switch
            {
                OrderStatus.Processing => "Processing",
                OrderStatus.Shipped => "Shipped",
                OrderStatus.Delivered => "Delivered",
                OrderStatus.PartialReturn => "Partial Return",
                OrderStatus.Returned => "Returned",
                _ => "Unknown"
            };
            return $"Order #{o.Id} - {status} - ${o.TotalAmount:F2} - Placed {o.OrderDate:MMM dd, yyyy}";
        });

        return $"You have {orders.Count} orders:\n" + string.Join("\n", summaries);
    }
    ```

1. 少し時間をかけて、GetUserOrdersSummaryAsync メソッドを確認しましょう。

    このツールは、特定の注文番号を指定せずに顧客が注文について尋ねるケース (たとえば、"私の最近の注文は何ですか" など) を処理することで、`GetOrderDetailsAsync` を補完します。 ユーザーのすべての注文を取得し、日付で降順に並べ替え、各注文を、注文番号、状態、合計、日付を示す簡潔な要約行として書式設定します。 AI エージェントはこの概要を使用して、顧客が関心のある注文を特定する手助けをします。

1. GetUserOrdersSummaryAsync メソッドの下のコード行に、次の ProcessReturnAsync メソッドを追加します。

    ```csharp
    /// <summary>
    /// Processes a return for specific items in a delivered order.
    /// The AI agent calls this tool when a user wants to return items.
    /// Supports returning all items, specific items by ID, or specific quantities.
    /// </summary>
    /// <param name="orderId">The order ID to process returns for</param>
    /// <param name="userId">The authenticated user ID</param>
    /// <param name="orderItemIds">Optional: Specific order item IDs to return (comma-separated, e.g., "123,456"). If empty, returns all unreturned items.</param>
    /// <param name="quantities">Optional: Quantities for each item (comma-separated, e.g., "1,2" for items 123 and 456). Must match orderItemIds length. If empty, returns full remaining quantity for each item.</param>
    /// <param name="reason">Optional: Reason for the return</param>
    public async Task<string> ProcessReturnAsync(
        int orderId, 
        int userId, 
        string orderItemIds = "", 
        string quantities = "",
        string reason = "Customer requested return via AI support agent")
    {
        _logger.LogInformation("Agent tool invoked: ProcessReturn for orderId {OrderId}, userId {UserId}, items: {Items}", 
            orderId, userId, string.IsNullOrEmpty(orderItemIds) ? "all" : orderItemIds);

        var order = await _context.Orders
            .Include(o => o.Items)
            .FirstOrDefaultAsync(o => o.Id == orderId && o.UserId == userId);

        if (order == null)
        {
            return $"I could not find order #{orderId} associated with your account.";
        }

        if (order.Status != OrderStatus.Delivered && order.Status != OrderStatus.Returned && order.Status != OrderStatus.PartialReturn)
        {
            return order.Status switch
            {
                OrderStatus.Processing => $"Order #{orderId} is still being processed and cannot be returned yet. It must be delivered first.",
                OrderStatus.Shipped => $"Order #{orderId} is currently in transit and cannot be returned until it has been delivered.",
                _ => $"Order #{orderId} has a status of {order.Status} and cannot be returned."
            };
        }

        List<ReturnItem> returnItems;

        // Parse specific items if provided
        if (!string.IsNullOrWhiteSpace(orderItemIds))
        {
            var itemIdStrings = orderItemIds.Split(',', StringSplitOptions.RemoveEmptyEntries);
            var itemIds = new List<int>();
            
            foreach (var idStr in itemIdStrings)
            {
                if (int.TryParse(idStr.Trim(), out int itemId))
                {
                    itemIds.Add(itemId);
                }
                else
                {
                    return $"Invalid item ID format: '{idStr}'. Please provide valid item IDs.";
                }
            }

            // Parse quantities if provided
            var itemQuantities = new List<int>();
            if (!string.IsNullOrWhiteSpace(quantities))
            {
                var quantityStrings = quantities.Split(',', StringSplitOptions.RemoveEmptyEntries);
                foreach (var qtyStr in quantityStrings)
                {
                    if (int.TryParse(qtyStr.Trim(), out int qty) && qty > 0)
                    {
                        itemQuantities.Add(qty);
                    }
                    else
                    {
                        return $"Invalid quantity format: '{qtyStr}'. Quantities must be positive numbers.";
                    }
                }

                if (itemQuantities.Count != itemIds.Count)
                {
                    return "The number of quantities must match the number of items.";
                }
            }

            // Build return items for specific items
            returnItems = new List<ReturnItem>();
            for (int i = 0; i < itemIds.Count; i++)
            {
                var orderItem = order.Items.FirstOrDefault(item => item.Id == itemIds[i]);
                if (orderItem == null)
                {
                    return $"Item ID {itemIds[i]} was not found in order #{orderId}.";
                }

                if (orderItem.RemainingQuantity <= 0)
                {
                    return $"{orderItem.ProductName} has already been fully returned.";
                }

                var quantityToReturn = itemQuantities.Count > 0 ? itemQuantities[i] : orderItem.RemainingQuantity;
                
                if (quantityToReturn > orderItem.RemainingQuantity)
                {
                    return $"Cannot return {quantityToReturn} of {orderItem.ProductName}. Only {orderItem.RemainingQuantity} available to return.";
                }

                returnItems.Add(new ReturnItem
                {
                    OrderItemId = orderItem.Id,
                    Quantity = quantityToReturn,
                    Reason = reason
                });
            }
        }
        else
        {
            // Return all unreturned items (original behavior)
            returnItems = order.Items
                .Where(i => i.RemainingQuantity > 0)
                .Select(i => new ReturnItem
                {
                    OrderItemId = i.Id,
                    Quantity = i.RemainingQuantity,
                    Reason = reason
                })
                .ToList();
        }

        if (!returnItems.Any())
        {
            return $"All items in order #{orderId} have already been returned.";
        }

        var success = await _orderService.ProcessItemReturnAsync(orderId, returnItems);

        if (!success)
        {
            _logger.LogError("Failed to process return for orderId {OrderId}, userId {UserId}", orderId, userId);
            return $"I was unable to process the return for order #{orderId}. Please contact our support team for assistance.";
        }

        _logger.LogInformation("Successfully processed return for orderId {OrderId}, userId {UserId}, items: {ItemCount}", 
            orderId, userId, returnItems.Count);

        // Calculate refund amount for the items being returned
        var refundAmount = returnItems.Sum(ri =>
        {
            var item = order.Items.First(i => i.Id == ri.OrderItemId);
            return item.Price * ri.Quantity;
        });

        // Build response message
        var itemsSummary = string.Join(", ", returnItems.Select(ri =>
        {
            var item = order.Items.First(i => i.Id == ri.OrderItemId);
            return $"{item.ProductName} (qty: {ri.Quantity})";
        }));

        return $"I've successfully processed the return for the following items from order #{orderId}: {itemsSummary}. " +
                $"A refund of ${refundAmount:F2} will be issued to your original payment method within 5-7 business days. " +
                $"You will receive a confirmation email shortly. " +
                $"To view the updated return status, please visit the Order Details page for order #{orderId}.";
    }
    ```

1. 少し時間をかけて、ProcessReturnAsync メソッドを確認しましょう。

    このコード スニペットは、全商品の返品と一部の商品の返品の両方をサポートする状態変更操作を行うため、最も複雑なツールです。 メソッドは、次の 3 つの省略可能なパラメーターを受け入れます。`orderItemIds` (返品のコンマ区切りのアイテム ID)、`quantities` (各アイテムのコンマ区切りの数量)、`reason`。 `orderItemIds` が空の場合は、返品されていないすべてのアイテムが返されます (既定の動作)。 特定のアイテム ID が指定されると、それらを解析し、必要に応じてその数量も解析して、各項目が注文に存在し、残りの個数があることを検証し、対象となる `ReturnItem` オブジェクトをビルドします。 このメソッドには、いくつかの検証レイヤーが含まれています。注文が存在し、ユーザーに属していることを確認し、注文の状態が `Delivered`、`PartialReturn`、`Returned` であることを確認し、アイテム ID と数量の形式を検証し、アイテムがまだ完全に返されていないことを確認します。 検証に成功すると、実際の返品処理が既存の `IOrderService.ProcessItemReturnAsync` メソッドに委任されます。 このメソッドは、返品される特定のアイテムに基づいて返金額を計算し、返品されたアイテムの概要を応答に含めます。 各検証エラーは、返品を処理できない理由を説明する、具体的で有用なメッセージを返します。

1. ProcessReturnAsync メソッドの下のコード行に、次の SendCustomerEmailAsync メソッドを追加します。

    ```csharp
    /// <summary>
    /// Sends a follow-up email to the customer regarding their order.
    /// The AI agent calls this tool to send additional information by email.
    /// </summary>
    public async Task<string> SendCustomerEmailAsync(int orderId, int userId, string message)
    {
        _logger.LogInformation("Agent tool invoked: SendCustomerEmail for orderId {OrderId}", orderId);

        var order = await _context.Orders
            .FirstOrDefaultAsync(o => o.Id == orderId && o.UserId == userId);

        if (order == null)
        {
            return $"Could not find order #{orderId} to send an email about.";
        }

        // Get the user's email from Identity
        var user = await _context.Users.FindAsync(userId);
        var email = user?.Email ?? "customer@contoso.com";

        await _emailService.SendEmailAsync(email, $"Regarding your order #{orderId}", message);

        return $"I've sent an email to {email} with the details about order #{orderId}.";
    }
    ```

    このツールを使用すると、AI エージェントはフォローアップ メールを顧客に送信できるようになります。 このメソッドは、注文が存在し、ユーザーに属していることを確認し、ID システムからユーザーのメール アドレスを取得し、`IEmailService` を使用してメールを送信します。 `message` パラメーターは AI エージェント自体によって生成されます。これにより、AI エージェントは会話に基づいて、コンテキストに適したメール コンテンツを作成できます。 ユーザーのメールを取得できない場合に備え、代替メール アドレスが提供されます。

1. 完成した SupportAgentTools.cs ファイルは、次のコードのようになります:

    ```csharp
    using ContosoShop.Server.Data;
    using ContosoShop.Shared.Models;
    using ContosoShop.Shared.DTOs;
    using Microsoft.EntityFrameworkCore;
    
    namespace ContosoShop.Server.Services;
    
    /// <summary>
    /// Provides tool functions that the AI support agent can invoke
    /// to look up order information and process returns.
    /// </summary>
    public class SupportAgentTools
    {
        private readonly ContosoContext _context;
        private readonly IOrderService _orderService;
        private readonly IEmailService _emailService;
        private readonly ILogger<SupportAgentTools> _logger;
    
        public SupportAgentTools(
            ContosoContext context,
            IOrderService orderService,
            IEmailService emailService,
            ILogger<SupportAgentTools> logger)
        {
            _context = context;
            _orderService = orderService;
            _emailService = emailService;
            _logger = logger;
        }
    
        // add the `GetOrderDetailsAsync` method here
        /// <summary>
        /// Gets the status and details of a specific order by order ID.
        /// The AI agent calls this tool when a user asks about their order status.
        /// </summary>
        public async Task<string> GetOrderDetailsAsync(int orderId, int userId)
        {
            _logger.LogInformation("Agent tool invoked: GetOrderDetails for orderId {OrderId}, userId {UserId}", orderId, userId);
    
            var order = await _context.Orders
                .Include(o => o.Items)
                .FirstOrDefaultAsync(o => o.Id == orderId && o.UserId == userId);
    
            if (order == null)
            {
                return $"I could not find order #{orderId} associated with your account. Please double-check the order number.";
            }
    
            var statusMessage = order.Status switch
            {
                OrderStatus.Processing => "is currently being processed and has not shipped yet",
                OrderStatus.Shipped => order.ShipDate.HasValue
                    ? $"was shipped on {order.ShipDate.Value:MMMM dd, yyyy} and is on its way"
                    : "has been shipped and is on its way",
                OrderStatus.Delivered => order.DeliveryDate.HasValue
                    ? $"was delivered on {order.DeliveryDate.Value:MMMM dd, yyyy}"
                    : "has been delivered",
                OrderStatus.PartialReturn => "has been partially returned (some items have been returned, others are still with you)",
                OrderStatus.Returned => "has been fully returned and a refund was issued",
                _ => "has an unknown status"
            };
    
            var itemSummary = string.Join(", ", order.Items.Select(i =>
            {
                var itemInfo = $"{i.ProductName} (Id: {i.Id}, qty: {i.Quantity}, ${i.Price:F2} each";
                if (i.ReturnedQuantity > 0)
                {
                    itemInfo += $", {i.ReturnedQuantity} returned, {i.RemainingQuantity} remaining";
                }
                itemInfo += ")";
                return itemInfo;
            }));
    
            return $"Order #{order.Id} {statusMessage}. " +
                    $"Order date: {order.OrderDate:MMMM dd, yyyy}. " +
                    $"Total: ${order.TotalAmount:F2}. " +
                    $"Items: {itemSummary}.";
        }
    
        // add the `GetUserOrdersSummaryAsync` method here
        /// <summary>
        /// Gets a summary of all orders for a given user.
        /// The AI agent calls this tool when a user asks about their orders
        /// without specifying a particular order number.
        /// </summary>
        public async Task<string> GetUserOrdersSummaryAsync(int userId)
        {
            _logger.LogInformation("Agent tool invoked: GetUserOrdersSummary for userId {UserId}", userId);
    
            var orders = await _context.Orders
                .Where(o => o.UserId == userId)
                .OrderByDescending(o => o.OrderDate)
                .ToListAsync();
    
            if (!orders.Any())
            {
                return "You don't have any orders on file.";
            }
    
            var summaries = orders.Select(o =>
            {
                var status = o.Status switch
                {
                    OrderStatus.Processing => "Processing",
                    OrderStatus.Shipped => "Shipped",
                    OrderStatus.Delivered => "Delivered",
                    OrderStatus.PartialReturn => "Partial Return",
                    OrderStatus.Returned => "Returned",
                    _ => "Unknown"
                };
                return $"Order #{o.Id} - {status} - ${o.TotalAmount:F2} - Placed {o.OrderDate:MMM dd, yyyy}";
            });
    
            return $"You have {orders.Count} orders:\n" + string.Join("\n", summaries);
        }
    
        // add the `ProcessReturnAsync` method here
        /// <summary>
        /// Processes a return for specific items in a delivered order.
        /// The AI agent calls this tool when a user wants to return items.
        /// Supports returning all items, specific items by ID, or specific quantities.
        /// </summary>
        /// <param name="orderId">The order ID to process returns for</param>
        /// <param name="userId">The authenticated user ID</param>
        /// <param name="orderItemIds">Optional: Specific order item IDs to return (comma-separated, e.g., "123,456"). If empty, returns all unreturned items.</param>
        /// <param name="quantities">Optional: Quantities for each item (comma-separated, e.g., "1,2" for items 123 and 456). Must match orderItemIds length. If empty, returns full remaining quantity for each item.</param>
        /// <param name="reason">Optional: Reason for the return</param>
        public async Task<string> ProcessReturnAsync(
            int orderId, 
            int userId, 
            string orderItemIds = "", 
            string quantities = "",
            string reason = "Customer requested return via AI support agent")
        {
            _logger.LogInformation("Agent tool invoked: ProcessReturn for orderId {OrderId}, userId {UserId}, items: {Items}", 
                orderId, userId, string.IsNullOrEmpty(orderItemIds) ? "all" : orderItemIds);
    
            var order = await _context.Orders
                .Include(o => o.Items)
                .FirstOrDefaultAsync(o => o.Id == orderId && o.UserId == userId);
    
            if (order == null)
            {
                return $"I could not find order #{orderId} associated with your account.";
            }
    
            if (order.Status != OrderStatus.Delivered && order.Status != OrderStatus.Returned && order.Status != OrderStatus.PartialReturn)
            {
                return order.Status switch
                {
                    OrderStatus.Processing => $"Order #{orderId} is still being processed and cannot be returned yet. It must be delivered first.",
                    OrderStatus.Shipped => $"Order #{orderId} is currently in transit and cannot be returned until it has been delivered.",
                    _ => $"Order #{orderId} has a status of {order.Status} and cannot be returned."
                };
            }
    
            List<ReturnItem> returnItems;
    
            // Parse specific items if provided
            if (!string.IsNullOrWhiteSpace(orderItemIds))
            {
                var itemIdStrings = orderItemIds.Split(',', StringSplitOptions.RemoveEmptyEntries);
                var itemIds = new List<int>();
                
                foreach (var idStr in itemIdStrings)
                {
                    if (int.TryParse(idStr.Trim(), out int itemId))
                    {
                        itemIds.Add(itemId);
                    }
                    else
                    {
                        return $"Invalid item ID format: '{idStr}'. Please provide valid item IDs.";
                    }
                }
    
                // Parse quantities if provided
                var itemQuantities = new List<int>();
                if (!string.IsNullOrWhiteSpace(quantities))
                {
                    var quantityStrings = quantities.Split(',', StringSplitOptions.RemoveEmptyEntries);
                    foreach (var qtyStr in quantityStrings)
                    {
                        if (int.TryParse(qtyStr.Trim(), out int qty) && qty > 0)
                        {
                            itemQuantities.Add(qty);
                        }
                        else
                        {
                            return $"Invalid quantity format: '{qtyStr}'. Quantities must be positive numbers.";
                        }
                    }
    
                    if (itemQuantities.Count != itemIds.Count)
                    {
                        return "The number of quantities must match the number of items.";
                    }
                }
    
                // Build return items for specific items
                returnItems = new List<ReturnItem>();
                for (int i = 0; i < itemIds.Count; i++)
                {
                    var orderItem = order.Items.FirstOrDefault(item => item.Id == itemIds[i]);
                    if (orderItem == null)
                    {
                        return $"Item ID {itemIds[i]} was not found in order #{orderId}.";
                    }
    
                    if (orderItem.RemainingQuantity <= 0)
                    {
                        return $"{orderItem.ProductName} has already been fully returned.";
                    }
    
                    var quantityToReturn = itemQuantities.Count > 0 ? itemQuantities[i] : orderItem.RemainingQuantity;
                    
                    if (quantityToReturn > orderItem.RemainingQuantity)
                    {
                        return $"Cannot return {quantityToReturn} of {orderItem.ProductName}. Only {orderItem.RemainingQuantity} available to return.";
                    }
    
                    returnItems.Add(new ReturnItem
                    {
                        OrderItemId = orderItem.Id,
                        Quantity = quantityToReturn,
                        Reason = reason
                    });
                }
            }
            else
            {
                // Return all unreturned items (original behavior)
                returnItems = order.Items
                    .Where(i => i.RemainingQuantity > 0)
                    .Select(i => new ReturnItem
                    {
                        OrderItemId = i.Id,
                        Quantity = i.RemainingQuantity,
                        Reason = reason
                    })
                    .ToList();
            }
    
            if (!returnItems.Any())
            {
                return $"All items in order #{orderId} have already been returned.";
            }
    
            var success = await _orderService.ProcessItemReturnAsync(orderId, returnItems);
    
            if (!success)
            {
                _logger.LogError("Failed to process return for orderId {OrderId}, userId {UserId}", orderId, userId);
                return $"I was unable to process the return for order #{orderId}. Please contact our support team for assistance.";
            }
    
            _logger.LogInformation("Successfully processed return for orderId {OrderId}, userId {UserId}, items: {ItemCount}", 
                orderId, userId, returnItems.Count);
    
            // Calculate refund amount for the items being returned
            var refundAmount = returnItems.Sum(ri =>
            {
                var item = order.Items.First(i => i.Id == ri.OrderItemId);
                return item.Price * ri.Quantity;
            });
    
            // Build response message
            var itemsSummary = string.Join(", ", returnItems.Select(ri =>
            {
                var item = order.Items.First(i => i.Id == ri.OrderItemId);
                return $"{item.ProductName} (qty: {ri.Quantity})";
            }));
    
            return $"I've successfully processed the return for the following items from order #{orderId}: {itemsSummary}. " +
                    $"A refund of ${refundAmount:F2} will be issued to your original payment method within 5-7 business days. " +
                    $"You will receive a confirmation email shortly. " +
                    $"To view the updated return status, please visit the Order Details page for order #{orderId}.";
        }
    
        // add the `SendCustomerEmailAsync` method here
        /// <summary>
        /// Sends a follow-up email to the customer regarding their order.
        /// The AI agent calls this tool to send additional information by email.
        /// </summary>
        public async Task<string> SendCustomerEmailAsync(int orderId, int userId, string message)
        {
            _logger.LogInformation("Agent tool invoked: SendCustomerEmail for orderId {OrderId}", orderId);
    
            var order = await _context.Orders
                .FirstOrDefaultAsync(o => o.Id == orderId && o.UserId == userId);
    
            if (order == null)
            {
                return $"Could not find order #{orderId} to send an email about.";
            }
    
            // Get the user's email from Identity
            var user = await _context.Users.FindAsync(userId);
            var email = user?.Email ?? "customer@contoso.com";
    
            await _emailService.SendEmailAsync(email, $"Regarding your order #{orderId}", message);
    
            return $"I've sent an email to {email} with the details about order #{orderId}.";
        }
    }
    ```

    完成した **SupportAgentTools.cs** ファイルの構造は次のとおりです:

    - 先頭の `using` ステートメント、名前空間、クラス宣言
    - 4 つの挿入された依存関係を持つコンストラクター
    - 4 つのパブリック メソッド: `GetOrderDetailsAsync`、`GetUserOrdersSummaryAsync`、`ProcessReturnAsync`、`SendCustomerEmailAsync`

    4 つの方法すべてが、一貫した設計パターンに従っています。セキュリティ検証のために `userId` パラメーターを受け取り、ツールの呼び出しをログに記録し、データベースに対してクエリを実行し、検証を実行し、AI エージェントが直接顧客に提示する人間が判読できる文字列を返します。

1. **ContosoShop.Server/Program.cs** ファイルを開きます。

    Program.cs ファイルを使用して、依存関係の挿入に SupportAgentTools を登録します。

1. 下スクロールして、サービス登録セクションを見つけます。

    次のコード コメントで検索できます: `// Register order business logic service`。

1. `OrderService` の登録に使用するコードの後に空白行を作成します。

1. `SupportAgentTools` サービスを登録するには、次のコードを追加します:

    ```csharp
    // Register AI agent tools service
    builder.Services.AddScoped<SupportAgentTools>();
    ```

1. 更新したファイルを保存します。

1. ContosoShop.Server プロジェクトをビルドし、エラーがないことを確認します。

    たとえば、ターミナルで次のコマンドを入力してプロジェクトをビルドできます:

    ```powershell
    dotnet build
    ```

    ビルドが成功します。 エラーが発生した場合は、コードが上記のコード例と一致していることを確認します。 SupportAgentTools.cs ファイルを確認して、すべての `using` ステートメントと参照が正しいことを確認します。 GitHub Copilot SDK は、定期的な更新があるテクニカル プレビュー フェーズであることに注意してください。 必要に応じて、GitHub Copilot で GitHub Copilot SDK リポジトリ (`https://github.com/github/copilot-sdk`) をポイントし、AI アシスタントに問題のデバッグを依頼することができます。

## GitHub Copilot SDK エージェントを構成し、API エンドポイントを公開する

このタスクでは、`CopilotClient` シングルトンを作成し、依存関係の挿入に登録し、ユーザーの質問を受け付けて AI エージェントの応答を返す新しい API コントローラーを作成します。

そのためには、以下の手順を実行してください。

1. **ContosoShop.Server/Program.cs** ファイルを開きます。

    Program.cs ファイルを使用して、依存関係の挿入で CopilotClient をシングルトンとして登録します。

1. ファイルの先頭の、既存の `using` ステートメントの後に、次の `using` ステートメントを追加します。

    ```csharp
    using GitHub.Copilot.SDK;
    ```

1. サービス登録セクションを見つけます。

    前に追加したコード コメント: `// Register AI agent tools service` で検索できます。

1. SupportAgentTools サービスの登録に使用するコードの後に空白行を作成します。

    ここに、`CopilotClient` シングルトンを登録するコードを追加します。

1. CopilotClient シングルトンを作成して登録するには、次のコードを追加します。

    ```csharp
    // Register GitHub Copilot SDK client as a singleton
    builder.Services.AddSingleton<CopilotClient>(sp =>
    {
        var logger = sp.GetRequiredService<ILogger<CopilotClient>>();
        return new CopilotClient(new CopilotClientOptions
        {
            AutoStart = true,
            LogLevel = "info"
        });
    });
    ```

    `CopilotClient` は、Copilot CLI プロセス ライフサイクルを管理します。 `AutoStart = true` を設定すると、最初のセッションが作成されたときに CLI サーバーが自動的に起動します。

1. 下スクロールして、次のコード行を見つけます。

    ```csharp
    var app = builder.Build();
    ```

1. `var app = builder.Build();` とデータベース初期化ブロックの間に空白のコード行を作成します。

1. GitHub Copilot SDK クライアントを初期化して起動するには、次のコードを追加します:

    ```csharp
    // Ensure CopilotClient is started
    var copilotClient = app.Services.GetRequiredService<CopilotClient>();
    await copilotClient.StartAsync();
    ```

    このコードは、CopilotClient の初期化と開始に加えて、アプリケーションのシャットダウン時に適切に破棄されるようにします。

1. ファイルを保存します。

1. Visual Studio Code のエクスプローラー ビューで、**ContosoShop.Shared/Models** フォルダーを右クリックし、**[新しいファイル]** を選択します。

1. ファイルに **SupportQuery.cs** という名前を付けます。

1. **SupportQuery.cs** ファイルに次のコードを追加します。

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace ContosoShop.Shared.Models;

    /// <summary>
    /// Represents a support question submitted by the user to the AI agent.
    /// </summary>
    public class SupportQuery
    {
        /// <summary>
        /// The user's question or message for the AI support agent.
        /// </summary>
        [Required]
        [StringLength(1000, MinimumLength = 1)]
        public string Question { get; set; } = string.Empty;
    }

    /// <summary>
    /// Represents the AI agent's response to a support query.
    /// </summary>
    public class SupportResponse
    {
        /// <summary>
        /// The AI agent's answer to the user's question.
        /// </summary>
        public string Answer { get; set; } = string.Empty;
    }
    ```

1. 少し時間をかけて、**SupportQuery** と **SupportResponse** モデルを確認しましょう。

    このファイルは、AI サポート エージェント通信のデータ転送モデルを定義します。

    **SupportQuery**

    - AI サポート エージェントに送信された顧客の質問を表します
    - 検証を含む Question プロパティが含まれています: 必須、1 から 1000 文字
    - クライアントからサーバーへの要求ペイロードとして使用されます

    **SupportResponse**

    - 顧客に対する AI エージェントの応答を表します
    - エージェントの応答を含む Answer プロパティが含まれます
    - サーバーからクライアントへの応答ペイロードとして使用されます

    これらのモデルは、サポート チャット インターフェイスの軽量 DTO であり、Blazor クライアントと AI 搭載のサポート エンドポイント間の構造化された通信を可能にします。 シンプルな設計では、基本的な入力検証を使用したテキストベースの質問と応答の交換に焦点を当てます。

1. Visual Studio Code のエクスプローラー ビューで、**ContosoShop.Server/Controllers** フォルダーを右クリックし、**[新しいファイル]** を選択します。

1. ファイルに **SupportAgentController.cs** という名前を付けます。

1. **SupportAgentController.cs** ファイルに次のコードを追加します:

    ```csharp
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.AI;
    using GitHub.Copilot.SDK;
    using ContosoShop.Server.Services;
    using ContosoShop.Shared.Models;
    using System.ComponentModel;
    using System.Security.Claims;

    namespace ContosoShop.Server.Controllers;

    /// <summary>
    /// API controller that handles AI support agent queries.
    /// Accepts user questions, creates a Copilot SDK session with custom tools,
    /// and returns the agent's response.
    /// </summary>
    [ApiController]
    [Route("api/[controller]")]
    [Authorize]
    public class SupportAgentController : ControllerBase
    {
        private readonly CopilotClient _copilotClient;
        private readonly SupportAgentTools _agentTools;
        private readonly ILogger<SupportAgentController> _logger;

        public SupportAgentController(
            CopilotClient copilotClient,
            SupportAgentTools agentTools,
            ILogger<SupportAgentController> logger)
        {
            _copilotClient = copilotClient;
            _agentTools = agentTools;
            _logger = logger;
        }

        /// <summary>
        /// Accepts a support question from the user and returns the AI agent's response.
        /// POST /api/supportagent/ask
        /// </summary>
        [HttpPost("ask")]
        public async Task<IActionResult> AskQuestion([FromBody] SupportQuery query)
        {
            if (query == null || string.IsNullOrWhiteSpace(query.Question))
            {
                return BadRequest(new SupportResponse { Answer = "Please enter a question." });
            }

            // Get the authenticated user's ID from claims
            var userIdClaim = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
            if (!int.TryParse(userIdClaim, out int userId))
            {
                return Unauthorized(new SupportResponse { Answer = "Unable to identify user." });
            }

            _logger.LogInformation("Support agent query from user {UserId}: {Question}", userId, query.Question);
        }
    }

    ```

1. 少し時間をかけて SupportAgentController コードを確認しましょう。

    このコードは、コントローラーのスケルトンを確立します。 このコードの主な設計上の決定事項:

    - `[Authorize]` 属性を使用すると、認証されたユーザーのみがエンドポイントに到達できるようになります。エージェントがユーザー固有の注文データにアクセスするため、これは重要です。
    - `[ApiController]` と `[Route("api/[controller]")]` 属性は、`POST /api/supportagent/ask` でエンドポイントを構成します。
    - コンストラクターは、`CopilotClient` (AI セッションを作成するための SDK クライアント)、`SupportAgentTools` (前に作成したツール サービス)、診断用の `ILogger` という 3 つの依存関係を挿入します。
    - このメソッドは、入力を検証し、認証されたユーザーの ID をクレームから抽出することから始まります。 `userId` は 1 回抽出され、各ツール呼び出しに渡されます。 このプロセスにより、エージェントは現在のユーザーのデータにのみアクセスでき、ユーザー間のデータ漏洩を防ぐことができます。

1. `AskQuestion` メソッド内で、logging ステートメントの後に次のコードを追加します:

    > **注**:次のコードには、`try` ブロック全体は含まれていません。次の手順でコードをさらに追加します。

    ```csharp

    try
    {
        // Define the tools the AI agent can use
        var tools = new[]
        {
            AIFunctionFactory.Create(
                async ([Description("The order ID number")] int orderId) =>
                    await _agentTools.GetOrderDetailsAsync(orderId, userId),
                "get_order_details",
                "Look up the status and details of a specific order by its order number. Returns order status, items, dates, and total amount."),

            AIFunctionFactory.Create(
                async () =>
                    await _agentTools.GetUserOrdersSummaryAsync(userId),
                "get_user_orders",
                "Get a summary list of all orders for the current user. Use this when the user asks about their orders without specifying an order number."),

            AIFunctionFactory.Create(
                async (
                    [Description("The order ID number")] int orderId,
                    [Description("Optional: Specific order item IDs to return (comma-separated, e.g. '123,456'). Leave empty to return all items.")] string orderItemIds = "",
                    [Description("Optional: Quantities for each item (comma-separated, e.g. '1,2'). Must match orderItemIds count. Leave empty to return full quantity.")] string quantities = "",
                    [Description("Optional: Reason for return")] string reason = "Customer requested return via AI support agent") =>
                    await _agentTools.ProcessReturnAsync(orderId, userId, orderItemIds, quantities, reason),
                "process_return",
                "Process a return for specific items from a delivered order. Can return all items, specific items by ID, or specific quantities of items. Accepts comma-separated item IDs and quantities. Works for orders with Delivered, PartialReturn, or Returned status."),

            AIFunctionFactory.Create(
                async (
                    [Description("The order ID number")] int orderId,
                    [Description("The email message content")] string message) =>
                    await _agentTools.SendCustomerEmailAsync(orderId, userId, message),
                "send_customer_email",
                "Send a follow-up email to the customer with additional information about their order.")
        };

    ```

1. 少し時間をかけて、追加したツール定義を確認しましょう。

    ここのコード スニペットにより、AI エージェントの機能が定義されます。 このコードでは、`Microsoft.Extensions.AI` の `AIFunctionFactory` を使用して、呼び出し可能な AI ツールとして各 `SupportAgentTools` メソッドがラップされます。 `AIFunctionFactory.Create` の各呼び出しにより、AI モデルが呼び出すことができるツールとして `SupportAgentTools` メソッドがラップされます。 ツールごとに、次の情報を提供します:

    - 対応するメソッドを呼び出す**ラムダ デリゲート**。 `userId` は外側のスコープからキャプチャされるため、AI モデルでユーザーの ID を認識したり推測したりする必要はないことに注意します。
    - 呼び出すツールを決定するときにモデルが使用する**ツール名** (`"get_order_details"` など)。
    - モデルがツールを使用するタイミングと使用方法を理解するのに役立つ**説明**。
    - 提供する値をモデルに指示するパラメーターの `[Description]` 属性。

    `get_user_orders` ツールは、モデルからパラメーターを取得しません (`userId` は自動的にキャプチャされます)。 `process_return` ツールには 3 つのオプションのパラメーター (`orderItemIds`、`quantities`、`reason`) があり、返す項目と数を指定して部分的な返品ができるようにします (これらを省略するとすべての項目が返品されます)。 `send_customer_email` ツールは、2 つのモデル指定パラメーター (`orderId` と `message`) を受け取ります。 この設計では、さまざまな返品シナリオを処理し、メールのコンテンツを作成する柔軟性をモデルに与えながら、ユーザー コンテキストを安全に保ちます。

1. ツール定義のすぐ下に、システム プロンプトとツールを使用して Copilot SDK セッションを作成するための、次のコードを追加します。

    ```csharp

    // Create a Copilot session with the system prompt and tools
    await using var session = await _copilotClient.CreateSessionAsync(new SessionConfig
    {
        Model = "gpt-4.1",
        SystemMessage = new SystemMessageConfig
        {
            Mode = SystemMessageMode.Replace,
            Content = @"You are ContosoShop's AI customer support assistant. Your role is to help customers with their order inquiries.
            
                CAPABILITIES:
                - Look up order status and details using the get_order_details tool
                - List all customer orders using the get_user_orders tool
                - Process returns for delivered orders using the process_return tool (supports full or partial returns)
                - Send follow-up emails using the send_customer_email tool

                RETURN PROCESSING WORKFLOW:
                1. When customer wants to return an item, first call get_order_details to see items and their IDs
                2. Parse the customer's request carefully:
                   - Extract the product name they mentioned (e.g., 'Headphones', 'Desk Lamp', 'Monitor')
                   - Check if they specified a quantity (e.g., '1 Desk Lamp', '2 monitors', 'one laptop')
                   - Number words: 'one'=1, 'two'=2, 'three'=3, etc.
                3. From the order details returned by get_order_details, find the item(s) that match the product name:
                   - Match by ProductName field (case-insensitive, partial match is OK)
                   - AUTOMATICALLY extract the Id field from the matching OrderItem - this is the item ID you need
                   - NEVER ask the customer for an item ID - they don't have this information
                4. Determine the return quantity:
                   - If customer specified quantity in their request: use that quantity
                   - Else if remaining quantity is 1: automatically return that 1 item
                   - Else if remaining quantity is more than 1 and no quantity specified: ask how many they want to return
                5. Call process_return with the extracted item ID and quantity:
                   - Pass orderItemIds as the Id value from the OrderItem (e.g., '456')
                   - Pass quantities as the number to return (e.g., '1')
                6. After successful return, tell customer to view Order Details page to see the updated status
                
                IMPORTANT RULES FOR RETURNS:
                - NEVER ask the customer for an item ID - extract it automatically from get_order_details response
                - Match product names flexibly (e.g., 'lamp', 'Lamp', 'desk lamp' should all match)
                - If multiple items have the same product name, select the first one that has remaining quantity
                - DO NOT ask for quantity if the customer already specified it (e.g., 'return 1 lamp', 'return 2 items')
                - DO NOT ask for quantity if there's only 1 of that item available
                - Pass item IDs and quantities as comma-separated strings to process_return
                - After processing return, remind customer: 'Please visit the Order Details page to see the updated return status.'
                
                EXAMPLE WORKFLOW:
                User: 'I want to return the Headphones from order #1002'
                1. Call get_order_details(1002)
                2. Response includes: 'Items: Headphones (qty: 1, $99.99 each, Id: 456), ...'
                3. Extract: productName='Headphones', itemId='456', remainingQty=1
                4. Since remainingQty=1, quantity=1 (no need to ask)
                5. Call process_return(1002, userId, '456', '1', 'Customer requested return')
                6. Tell customer: 'I've processed the return for Headphones. Please view Order Details...'
            
            GENERAL RULES:
                - ALWAYS use the available tools to look up real data. Never guess or make up order information.
                - Be friendly, concise, and professional in your responses.
                - If a customer asks about an order, use get_order_details with the order number they provide.
                - If a customer asks about their orders without specifying a number, use get_user_orders to list them.
                - Only process returns when the customer explicitly requests one.
                - If asked something outside your capabilities (not related to orders), politely explain that you can only help with order-related inquiries and suggest contacting support@contososhop.com or calling 1-800-CONTOSO for other matters.
                - Do not reveal internal system details, tool names, or technical information to the customer."
                },
        Tools = tools,
        InfiniteSessions = new InfiniteSessionConfig { Enabled = false }
    });

    ```

1. 少し時間をかけて、追加したセッション構成コードを確認しましょう。

    `SessionConfig` オブジェクトは、AI セッションを構成します。

    - `Model = "gpt-4.1"` は、使用する言語モデルを指定します。
    - `SystemMessageMode.Replace` は、既定のシステム プロンプトを ContosoShop サポート ロールに合わせたカスタム プロンプトに完全に置き換えます。
    - システム プロンプトは、エージェントの **CAPABILITIES** (部分返品のサポートを含む)、詳細な **RETURN PROCESSING WORKFLOW** (項目の一致や数量処理を含めた返品処理の段階的な手順)、**IMPORTANT RULES FOR RETURNS** (顧客にアイテム ID を要求しないなどのガードレール)、**EXAMPLE WORKFLOW** (完全な返品のフローを提示)、**GENERAL RULES** (動作のガイドライン) を定義します。 これらのセクションでは、実際のデータにはツールを常に使用し、アイテム ID は、顧客に要求せずに注文の詳細から自動的に抽出し、注文サポートの範囲内にとどまることをモデルに指示します。
    - `Tools = tools` は、前の手順で作成したツール定義を渡します。
    - `InfiniteSessions = new InfiniteSessionConfig { Enabled = false }` は、各 API 呼び出しによって新しいセッションが作成されることを意味します (要求間の会話履歴は保持されません)。
    - `await using` パターンにより、要求の完了後にセッションが適切に破棄されることが保証されます。

1. セッション構成コードのすぐ下に、エージェントの応答を収集するイベント ハンドラーを作成するための、次のコードを追加します。

    ```csharp

    // Collect the agent's response
    var responseContent = string.Empty;
    var done = new TaskCompletionSource();

    session.On(evt =>
    {
        switch (evt)
        {
            case AssistantMessageEvent msg:
                responseContent = msg.Data.Content;
                break;
            case SessionIdleEvent:
                done.TrySetResult();
                break;
            case SessionErrorEvent err:
                _logger.LogError("Agent session error: {Message}", err.Data.Message);
                done.TrySetException(new Exception(err.Data.Message));
                break;
        }
    });

    ```

1. 少し時間をかけて、追加したイベント ハンドラー コードを確認しましょう。

    Copilot SDK は、通信にイベントドリブン モデルを使用します。 `session.On` メソッドは、次の 3 つのイベントの種類を処理するコールバックを登録します:

    - `AssistantMessageEvent`:AI モデルが応答を生成するときに発生します。 メッセージの内容は `responseContent` でキャプチャされます。
    - `SessionIdleEvent`:セッションの処理 (ツール呼び出しを含む) が完了したときに発生します。 これは、`TaskCompletionSource` を解決することで応答が完了したことを示します。
    - `SessionErrorEvent`:セッション中に問題が起きた場合に発生します。 エラーはログに記録され、`done.TrySetException` 経由で例外として伝達されます。

    `TaskCompletionSource` パターンは、イベントドリブンのフローを待機可能なタスクに変換し、コントローラーが HTTP レスポンスを返す前にエージェントの完了を待機できるようにします。

1. イベント ハンドラー コードのすぐ下に、ユーザーの質問を送信し、タイムアウトで応答を待機して結果を返すための、次のコードを追加します。

    ```csharp

    // Send the user's question
    await session.SendAsync(new MessageOptions { Prompt = query.Question });

    // Wait for the response with a timeout
    var timeoutTask = Task.Delay(TimeSpan.FromSeconds(30));
    var completedTask = await Task.WhenAny(done.Task, timeoutTask);

    if (completedTask == timeoutTask)
    {
        _logger.LogWarning("Agent session timed out for user {UserId}", userId);
        return Ok(new SupportResponse
        {
            Answer = "I'm sorry, the request took too long. Please try again or contact our support team."
        });
    }

    // Rethrow if the task faulted
    await done.Task;

    _logger.LogInformation("Agent response for user {UserId}: {Answer}", userId, responseContent);

    return Ok(new SupportResponse { Answer = responseContent });

    ```

1. 少し時間をかけて、AI エージェントとの通信を管理するコードを確認しましょう。

    このコードは、顧客の質問を送信し、非同期応答を処理します。

    - `session.SendAsync` はユーザーの質問を AI モデルにディスパッチします。これにより、最終的な応答が作成される前に、ゼロ個以上のツールが呼び出される可能性があります。
    - **30 秒のタイムアウト**により、実行時間の長い要求を防止します。 エージェントで時間がかかりすぎている場合 (たとえば、複数のツール呼び出しやネットワーク遅延が原因の場合)、要求が無期限に待機状態になるのではなく、ユーザーにわかりやすいタイムアウト メッセージが表示されます。
    - `Task.WhenAny` は、エージェントの完了とタイムアウトを競合させます。 `done.Task` が最初に完了すると、`await done.Task` が再度呼び出され、`SessionErrorEvent` によって設定された可能性のある例外が伝達されます。
    - 成功した応答は、`SupportResponse` DTO にラップされ、HTTP 200 として返されます。

1. AI エージェントとの通信を管理するコードのすぐ下に、`try-catch` ブロックを完了するための次のコードを追加します。

    ```csharp

    }

    catch (Exception ex)
    {
        _logger.LogError(ex, "Error processing support agent query for user {UserId}", userId);
        return StatusCode(500, new SupportResponse
        {
            Answer = "I'm sorry, I encountered an error processing your request. Please try again or contact our support team at support@contososhop.com."
        });
    }

    ```

1. 少し時間をかけて、`catch` ブロック内のエラー処理コードを確認しましょう。

    `catch` ブロックは、Copilot SDK からのエラー、ツールの実行エラー、ネットワークの問題など、あらゆるハンドルされない例外に対するセーフティ ネットを提供します。 顧客に生のエラーを公開するのではなく、デバッグ用の完全な例外をログに記録し、代替連絡先オプション付きのわかりやすいエラー メッセージを返します。 このエラー処理により、内部で起きた問題の内容に関係なく、API から常に有効な `SupportResponse` が返されることが保証されます。

1. 完成した **SupportAgentController.cs** ファイルは、次のコードのようになります:

    ```csharp
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.AI;
    using GitHub.Copilot.SDK;
    using ContosoShop.Server.Services;
    using ContosoShop.Shared.Models;
    using System.ComponentModel;
    using System.Security.Claims;
    
    namespace ContosoShop.Server.Controllers;
    
    /// <summary>
    /// API controller that handles AI support agent queries.
    /// Accepts user questions, creates a Copilot SDK session with custom tools,
    /// and returns the agent's response.
    /// </summary>
    [ApiController]
    [Route("api/[controller]")]
    [Authorize]
    public class SupportAgentController : ControllerBase
    {
        private readonly CopilotClient _copilotClient;
        private readonly SupportAgentTools _agentTools;
        private readonly ILogger<SupportAgentController> _logger;
    
        public SupportAgentController(
            CopilotClient copilotClient,
            SupportAgentTools agentTools,
            ILogger<SupportAgentController> logger)
        {
            _copilotClient = copilotClient;
            _agentTools = agentTools;
            _logger = logger;
        }
    
        /// <summary>
        /// Accepts a support question from the user and returns the AI agent's response.
        /// POST /api/supportagent/ask
        /// </summary>
        [HttpPost("ask")]
        public async Task<IActionResult> AskQuestion([FromBody] SupportQuery query)
        {
            if (query == null || string.IsNullOrWhiteSpace(query.Question))
            {
                return BadRequest(new SupportResponse { Answer = "Please enter a question." });
            }
    
            // Get the authenticated user's ID from claims
            var userIdClaim = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
            if (!int.TryParse(userIdClaim, out int userId))
            {
                return Unauthorized(new SupportResponse { Answer = "Unable to identify user." });
            }
    
            _logger.LogInformation("Support agent query from user {UserId}: {Question}", userId, query.Question);
    
            try
            {
                // Define the tools the AI agent can use
                var tools = new[]
                {
                AIFunctionFactory.Create(
                    async ([Description("The order ID number")] int orderId) =>
                        await _agentTools.GetOrderDetailsAsync(orderId, userId),
                    "get_order_details",
                    "Look up the status and details of a specific order by its order number. Returns order status, items, dates, and total amount."),
    
                AIFunctionFactory.Create(
                    async () =>
                        await _agentTools.GetUserOrdersSummaryAsync(userId),
                    "get_user_orders",
                    "Get a summary list of all orders for the current user. Use this when the user asks about their orders without specifying an order number."),
    
                AIFunctionFactory.Create(
                    async (
                        [Description("The order ID number")] int orderId,
                        [Description("Optional: Specific order item IDs to return (comma-separated, e.g. '123,456'). Leave empty to return all items.")] string orderItemIds = "",
                        [Description("Optional: Quantities for each item (comma-separated, e.g. '1,2'). Must match orderItemIds count. Leave empty to return full quantity.")] string quantities = "",
                        [Description("Optional: Reason for return")] string reason = "Customer requested return via AI support agent") =>
                        await _agentTools.ProcessReturnAsync(orderId, userId, orderItemIds, quantities, reason),
                    "process_return",
                    "Process a return for specific items from a delivered order. Can return all items, specific items by ID, or specific quantities of items. Accepts comma-separated item IDs and quantities. Works for orders with Delivered, PartialReturn, or Returned status."),

    
                AIFunctionFactory.Create(
                    async (
                        [Description("The order ID number")] int orderId,
                        [Description("The email message content")] string message) =>
                        await _agentTools.SendCustomerEmailAsync(orderId, userId, message),
                    "send_customer_email",
                    "Send a follow-up email to the customer with additional information about their order.")
            };
    
                // Create a Copilot session with the system prompt and tools
                await using var session = await _copilotClient.CreateSessionAsync(new SessionConfig
                {
                    Model = "gpt-4.1",
                    SystemMessage = new SystemMessageConfig
                    {
                        Mode = SystemMessageMode.Replace,
                        Content = @"You are ContosoShop's AI customer support assistant. Your role is to help customers with their order inquiries.
                
                    CAPABILITIES:
                    - Look up order status and details using the get_order_details tool
                    - List all customer orders using the get_user_orders tool
                    - Process returns for delivered orders using the process_return tool (supports full or partial returns)
                    - Send follow-up emails using the send_customer_email tool

                    RETURN PROCESSING WORKFLOW:
                    1. When customer wants to return an item, first call get_order_details to see items and their IDs
                    2. Parse the customer's request carefully:
                       - Extract the product name they mentioned (e.g., 'Headphones', 'Desk Lamp', 'Monitor')
                       - Check if they specified a quantity (e.g., '1 Desk Lamp', '2 monitors', 'one laptop')
                       - Number words: 'one'=1, 'two'=2, 'three'=3, etc.
                    3. From the order details returned by get_order_details, find the item(s) that match the product name:
                       - Match by ProductName field (case-insensitive, partial match is OK)
                       - AUTOMATICALLY extract the Id field from the matching OrderItem - this is the item ID you need
                       - NEVER ask the customer for an item ID - they don't have this information
                    4. Determine the return quantity:
                       - If customer specified quantity in their request: use that quantity
                       - Else if remaining quantity is 1: automatically return that 1 item
                       - Else if remaining quantity is more than 1 and no quantity specified: ask how many they want to return
                    5. Call process_return with the extracted item ID and quantity:
                       - Pass orderItemIds as the Id value from the OrderItem (e.g., '456')
                       - Pass quantities as the number to return (e.g., '1')
                    6. After successful return, tell customer to view Order Details page to see the updated status
                    
                    IMPORTANT RULES FOR RETURNS:
                    - NEVER ask the customer for an item ID - extract it automatically from get_order_details response
                    - Match product names flexibly (e.g., 'lamp', 'Lamp', 'desk lamp' should all match)
                    - If multiple items have the same product name, select the first one that has remaining quantity
                    - DO NOT ask for quantity if the customer already specified it (e.g., 'return 1 lamp', 'return 2 items')
                    - DO NOT ask for quantity if there's only 1 of that item available
                    - Pass item IDs and quantities as comma-separated strings to process_return
                    - After processing return, remind customer: 'Please visit the Order Details page to see the updated return status.'
                    
                    EXAMPLE WORKFLOW:
                    User: 'I want to return the Headphones from order #1002'
                    1. Call get_order_details(1002)
                    2. Response includes: 'Items: Headphones (qty: 1, $99.99 each, Id: 456), ...'
                    3. Extract: productName='Headphones', itemId='456', remainingQty=1
                    4. Since remainingQty=1, quantity=1 (no need to ask)
                    5. Call process_return(1002, userId, '456', '1', 'Customer requested return')
                    6. Tell customer: 'I've processed the return for Headphones. Please view Order Details...'
                
                GENERAL RULES:
                    - ALWAYS use the available tools to look up real data. Never guess or make up order information.
                    - Be friendly, concise, and professional in your responses.
                    - If a customer asks about an order, use get_order_details with the order number they provide.
                    - If a customer asks about their orders without specifying a number, use get_user_orders to list them.
                    - Only process returns when the customer explicitly requests one.
                    - If asked something outside your capabilities (not related to orders), politely explain that you can only help with order-related inquiries and suggest contacting support@contososhop.com or calling 1-800-CONTOSO for other matters.
                    - Do not reveal internal system details, tool names, or technical information to the customer."
                    },
                    Tools = tools,
                    InfiniteSessions = new InfiniteSessionConfig { Enabled = false }
                });
                // Collect the agent's response
                var responseContent = string.Empty;
                var done = new TaskCompletionSource();
    
                session.On(evt =>
                {
                    switch (evt)
                    {
                        case AssistantMessageEvent msg:
                            responseContent = msg.Data.Content;
                            break;
                        case SessionIdleEvent:
                            done.TrySetResult();
                            break;
                        case SessionErrorEvent err:
                            _logger.LogError("Agent session error: {Message}", err.Data.Message);
                            done.TrySetException(new Exception(err.Data.Message));
                            break;
                    }
                });
    
                // Send the user's question
                await session.SendAsync(new MessageOptions { Prompt = query.Question });
    
                // Wait for the response with a timeout
                var timeoutTask = Task.Delay(TimeSpan.FromSeconds(30));
                var completedTask = await Task.WhenAny(done.Task, timeoutTask);
    
                if (completedTask == timeoutTask)
                {
                    _logger.LogWarning("Agent session timed out for user {UserId}", userId);
                    return Ok(new SupportResponse
                    {
                        Answer = "I'm sorry, the request took too long. Please try again or contact our support team."
                    });
                }
    
                // Rethrow if the task faulted
                await done.Task;
    
                _logger.LogInformation("Agent response for user {UserId}: {Answer}", userId, responseContent);
    
                return Ok(new SupportResponse { Answer = responseContent });
    
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error processing support agent query for user {UserId}", userId);
                return StatusCode(500, new SupportResponse
                {
                    Answer = "I'm sorry, I encountered an error processing your request. Please try again or contact our support team at support@contososhop.com."
                });
            }
        }
    }
    ```

    完成した **SupportAgentController.cs** ファイルの構造は次のとおりです:

    - 上部の `using` ステートメントと名前空間
    - `[ApiController]`、`[Route]`、`[Authorize]` 属性を持つ `SupportAgentController` クラス
    - `CopilotClient`、`SupportAgentTools`、`ILogger`
    - 次の 1 つの `AskQuestion` アクション メソッド (`[HttpPost("ask")]`):
      - 入力を検証し、ユーザー ID を抽出する
      - `AIFunctionFactory.Create` を使用して 4 つの AI ツールを定義する
      - システム プロンプトとツールを使用して Copilot セッションを作成する
      - 応答、アイドル、エラー イベントのイベント ハンドラーを登録する
      - 質問を送信し、30 秒のタイムアウト付きで応答を待機する
      - 応答または適切なエラー メッセージを返す

1. **ContosoShop.Server/Program.cs** ファイルを開きます。

1. CORS ポリシーを構成するコードを見つけます。

    次のコード コメントで検索できます: `// Configure CORS`。

1. CORS 構成セクションでは、先ほど作成した API エンドポイントで必要な `GET` および `POST` メソッドを使用できます。

   既存の構成では、`GET` および `POST` メソッドで十分です。

    ```csharp
    .WithMethods("GET", "POST") // Only required methods
    ```

1. プロジェクトをビルドするには、ターミナルで次のコマンドを入力します。

    ```powershell
    dotnet build
    ```

    ビルドはエラーなしで成功します。 `GitHub.Copilot.SDK` の種類に関連するエラーが表示される場合は、NuGet パッケージが正しくインストールされていることを確認します。

## Blazor フロントエンドを更新してエージェントとやり取りする

このタスクでは、エージェント API を呼び出すクライアント側サービスを作成し、対話型チャット インターフェイスを使用して Support.razor ページを更新します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューで、**ContosoShop.Client/Services** フォルダーを右クリックし、**[新しいファイル]** を選択します。

1. ファイルに **SupportAgentService.cs** という名前を付けます。

1. 次のコードを追加します。

    ```csharp
    using System.Net.Http.Json;
    using ContosoShop.Shared.Models;

    namespace ContosoShop.Client.Services;

    /// <summary>
    /// Client-side service for communicating with the AI support agent API.
    /// </summary>
    public class SupportAgentService
    {
        private readonly HttpClient _http;

        public SupportAgentService(HttpClient http)
        {
            _http = http;
        }

        /// <summary>
        /// Sends a question to the AI support agent and returns the response.
        /// </summary>
        /// <param name="question">The user's question</param>
        /// <returns>The agent's response text</returns>
        public async Task<string> AskAsync(string question)
        {
            var query = new SupportQuery { Question = question };

            var response = await _http.PostAsJsonAsync("api/supportagent/ask", query);

            if (!response.IsSuccessStatusCode)
            {
                var errorText = await response.Content.ReadAsStringAsync();
                throw new HttpRequestException(
                    $"Support agent returned {response.StatusCode}: {errorText}");
            }

            var result = await response.Content.ReadFromJsonAsync<SupportResponse>();
            return result?.Answer ?? "I'm sorry, I didn't receive a response. Please try again.";
        }
    }
    ```

1. 少し時間をかけて `SupportAgentService` コードを確認しましょう。

    このコード スニペットは、AI サポート エージェント バックエンドとやり取りするクライアント側 HTTP サービスです。 主な機能:

    シンプルな API ラッパー:

    - 単一メソッド AskAsync(string question) - サポート エージェント API エンドポイントにユーザーの質問を送信します
    - サーバー上の POST /api/supportagent/ask に POST を送信します

    通信処理:

    - SupportQuery DTO で質問をラップします
    - HttpClient.PostAsJsonAsync を使用して JSON の自動シリアル化を行います
    - 応答を SupportResponse オブジェクトに逆シリアル化します

    エラー管理:

    - HTTP 状態コードのエラーを確認します
    - 成功していない応答に関する詳細なエラー情報を含む HttpRequestException をスローする
    - 応答解析が失敗した場合にフォールバック メッセージを提供します

    設計パターン:

    - サービス層パターンに従ったシン クライアント ラッパー
    - テスト容易性と適切な有効期間管理のために挿入された HttpClient
    - Blazor コンポーネント (Support.razor など) で使用され、HTTP の詳細を直接処理せずに AI エージェントとやり取りします

    このサービスは HTTP 通信の複雑さを抽象化し、Blazor コンポーネントが AI サポート エージェントに質問するためのクリーンなインターフェイスを提供します。

1. **ContosoShop.Client/Program.cs** ファイルを開きます。

1. ファイルのサービス登録セクションを見つけます。

    `// Register application services` を検索できます

1. 既存のサービス登録の下に空白のコード行を作成し、次のコードを追加します。

    ```csharp

    // Register AI support agent service
    builder.Services.AddScoped<SupportAgentService>(sp =>
        new SupportAgentService(sp.GetRequiredService<HttpClient>()));

    ```

    このコードは、Blazor の依存関係挿入コンテナーにスコープ付きサービスとして `SupportAgentService` を登録し、コンポーネントに挿入できるようにします。 `HttpClient` はサービス コンストラクターに挿入され、適切な有効期間の管理と構成が保証されます。 `using ContosoShop.Client.Services;` ステートメントは、ファイルの先頭に既に存在している必要があります。

1. ファイルを保存します。

1. **ContosoShop.Client/Pages/Support.razor** ファイルを開きます。

    このファイルの既存のコンテンツを置き換えて、AI エージェントとやり取りする新しいサポート チャット インターフェイスを作成します。

1. ファイルの既存のコンテンツを選択して削除します。

1. 新しいファイルの構築を開始するには、次のコードを追加します:

    > **注**:Support.razor ファイルを段階的にビルドしていきます。 すべてのコード スニペットを追加するまで、ファイルをオートフォーマット (ドキュメントの書式設定) しないでください。

    ```cshtml
    @page "/support"
    @using ContosoShop.Shared.Models
    @using ContosoShop.Client.Services
    @attribute [Microsoft.AspNetCore.Authorization.Authorize]
    @inject SupportAgentService AgentService

    <PageTitle>Contact Support - ContosoShop Support Portal</PageTitle>

    <div class="container mt-4">
        <div class="row">
            <div class="col-lg-8 mx-auto">
                <h2 class="mb-4">Contact Support</h2>

                <!-- AI Chat Card -->
                <div class="card mb-4 border-info">
                    <div class="card-header bg-info text-white">
                        <h5 class="mb-0">
                            <i class="bi bi-robot me-2"></i>AI Chat Support
                        </h5>
                    </div>
                    <div class="card-body">
                        <!-- Chat Messages Area -->
                        <div class="border rounded p-3 mb-3" style="min-height: 300px; max-height: 500px; overflow-y: auto;" id="chatMessages">
                            @if (!conversations.Any())
                            {
                                <div class="text-center text-muted py-4">
                                    <i class="bi bi-chat-dots display-4 mb-2"></i>
                                    <p>Ask me about your orders! For example:</p>
                                    <ul class="list-unstyled">
                                        <li><em>"What is the status of order #1001?"</em></li>
                                        <li><em>"Show me all my orders"</em></li>
                                        <li><em>"I want to return order #1005"</em></li>
                                    </ul>
                                </div>
                            }
                            @foreach (var entry in conversations)
                            {
                                <div class="mb-3">
                                    <div class="d-flex align-items-start mb-1">
                                        <span class="badge bg-primary me-2">You</span>
                                        <span>@entry.Question</span>
                                    </div>
                                    @if (!string.IsNullOrEmpty(entry.Answer))
                                    {
                                        <div class="d-flex align-items-start ms-2">
                                            <span class="badge bg-info me-2">Agent</span>
                                            <span style="white-space: pre-line;">@entry.Answer</span>
                                        </div>
                                    }
                                </div>
                            }
                            @if (isLoading)
                            {
                                <div class="d-flex align-items-start ms-2">
                                    <span class="badge bg-info me-2">Agent</span>
                                    <span class="text-muted"><em>Thinking...</em></span>
                                </div>
                            }
                        </div>

    ```

1. 少し時間をかけて、追加した HTML コードを確認しましょう。

    この最初のセクションでは、ページ構造を確立します:

    - `@page "/support"` ディレクティブは、このコンポーネントを `/support` URL ルートにマップします。
    - `@attribute [Authorize]` は、認証されたユーザーのみがページにアクセスできるようにします。
    - `@inject SupportAgentService AgentService` は、前の手順で作成したクライアント側のサービスを挿入し、AI エージェント API にページへのアクセス権を付与します。
    - チャット メッセージ領域は、会話履歴を表示するスクロール可能な `div` (高さ 300 から 500px) です。 メッセージがまだない場合は、ユーザーをガイドする役立つプロンプトの例が表示されます。 各会話エントリには、ユーザーの質問に "自分" バッジが付き、エージェントの応答には "エージェント" バッジが表示されます。 `white-space: pre-line` スタイルでは、エージェントの応答の改行が保持されます (たとえば、複数の注文を一覧表示する場合)。 "考えています..." インジケーターは、エージェントの要求の処理中に表示されます。

1. チャット メッセージ領域の `</div>` の後に、入力領域を追加して AI チャット カードを閉じるには、次のコードを入力します。

    ```cshtml

            <!-- Input Area -->
            <div class="input-group">
                <input type="text"
                        class="form-control"
                        placeholder="Type your question..."
                        @bind="currentQuestion"
                        @bind:event="oninput"
                        @onkeydown="HandleKeyDown"
                        disabled="@isLoading" />
                <button class="btn btn-info text-white"
                        @onclick="SubmitQuestion"
                        disabled="@(isLoading || string.IsNullOrWhiteSpace(currentQuestion))">
                    <i class="bi bi-send me-1"></i>Send
                </button>
            </div>

            @if (!string.IsNullOrEmpty(errorMessage))
            {
                <div class="alert alert-danger mt-2 mb-0">
                    <i class="bi bi-exclamation-triangle me-1"></i>@errorMessage
                </div>
            }
        </div>
    </div>

    ```

1. 少し時間をかけて、追加した入力領域のコードを確認しましょう。

    入力領域では、送信ボタンが付いたクリーン テキスト フィールドに Bootstrap の `input-group` が使用されます。 主要な操作の詳細:

    - `@bind="currentQuestion"` と `@bind:event="oninput"` は、リアルタイムの両方向のバインドを提供します。`currentQuestion` 変数は、(フォーカスが外れた時だけではなく) ユーザーの入力に応じて更新されます。
    - `@onkeydown="HandleKeyDown"` では、質問を送信するための Enter キー ショートカットが有効になります。
    - `isLoading` が true の間は入力とボタンの両方が無効になり、エージェントの処理中に送信が重複するのを防ぎます。
    - このボタンは、入力が空の場合も無効になります (`string.IsNullOrWhiteSpace(currentQuestion)`)。
    - `errorMessage` が設定されると、エラー アラートが入力の下に条件に応じて表示され、問題が発生した場合にわかりやすいフィードバックが提供されます。

1. AI チャット カードの後に連絡先情報カードを追加するには、次のコードを入力します。

    ```cshtml

    <!-- Contact Information Card -->
    <div class="card mb-4">
        <div class="card-header bg-primary text-white">
            <h5 class="mb-0">
                <i class="bi bi-headset me-2"></i>Get in Touch
            </h5>
        </div>
        <div class="card-body">
            <div class="row">
                <div class="col-md-6 mb-3">
                    <h6 class="text-muted">Email Support</h6>
                    <p class="mb-0">
                        <i class="bi bi-envelope me-2"></i>
                        <a href="mailto:support@contososhop.com">support@contososhop.com</a>
                    </p>
                    <small class="text-muted">Response time: 24-48 hours</small>
                </div>
                <div class="col-md-6 mb-3">
                    <h6 class="text-muted">Phone Support</h6>
                    <p class="mb-0">
                        <i class="bi bi-telephone me-2"></i>
                        <a href="tel:1-800-266-8676">1-800-CONTOSO</a>
                    </p>
                    <small class="text-muted">Mon-Fri 9AM-5PM EST</small>
                </div>
            </div>
        </div>
    </div>

    ```

1. 少し時間をかけて、追加した連絡先情報カードのコードを確認しましょう。

    このカードは、AI エージェントが顧客の問題を完全に解決できない場合の代替として、従来の連絡方法を提供します。 2 列のレイアウト (Bootstrap のグリッドを使用) では、メールと電話のサポートが、それぞれ応答時間の想定付きで、中以上の画面に左右に並べて表示されます。 これは、前に構成したシステム プロンプトと一致します。これにより、顧客に順序不一致の問題について `support@contososhop.com` または `1-800-CONTOSO` するよう AI エージェントに指示します。

1. 連絡先情報カードの後に、ページ レイアウトのクイック リンク カードと終了 `</div>` タグを追加するには、次のコードを追加します。

    ```cshtml

                <!-- Quick Links -->
                <div class="card">
                    <div class="card-header">
                        <h5 class="mb-0">
                            <i class="bi bi-question-circle me-2"></i>Need Help With Your Order?
                        </h5>
                    </div>
                    <div class="card-body">
                        <ul class="list-unstyled mb-0">
                            <li class="mb-2">
                                <a href="/orders" class="text-decoration-none">
                                    <i class="bi bi-box-seam me-2"></i>View Your Orders
                                </a>
                            </li>
                            <li class="mb-2">
                                <i class="bi bi-arrow-return-left me-2"></i>
                                <span>Return a delivered order from the Order Details page</span>
                            </li>
                            <li class="mb-0">
                                <i class="bi bi-info-circle me-2"></i>
                                <span>Track shipment status and delivery updates</span>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>

    ```

1. 少し時間をかけて、追加したクイック リンク カードのコードを確認しましょう。

    クイック リンク カードは、アプリケーションの他の部分へのナビゲーション ショートカットを提供します。 "注文の表示" リンクは、`/orders` ページに移動します。ここで顧客は完全な注文リストを表示できます。 残りのアイテムは、アプリ内の他の場所で使用可能なセルフサービス アクションを表します。 3 つの終了 `</div>` タグは、ページ レイアウト全体をラップする `col-lg-8`、`row`、`container` 要素を閉じます。

1. HTML コードの下の行に、コンポーネントの状態管理とイベント処理ロジックを含む `@code` ブロックを追加するには、次のコードを入力します。

    ```cshtml

    @code {
        private class ConversationEntry
        {
            public string Question { get; set; } = string.Empty;
            public string Answer { get; set; } = string.Empty;
        }

        private List<ConversationEntry> conversations = new();
        private string currentQuestion = string.Empty;
        private bool isLoading = false;
        private string errorMessage = string.Empty;

        private async Task HandleKeyDown(KeyboardEventArgs e)
        {
            if (e.Key == "Enter" && !isLoading && !string.IsNullOrWhiteSpace(currentQuestion))
            {
                await SubmitQuestion();
            }
        }

        private async Task SubmitQuestion()
        {
            if (string.IsNullOrWhiteSpace(currentQuestion) || isLoading)
                return;

            errorMessage = string.Empty;
            var question = currentQuestion.Trim();
            currentQuestion = string.Empty;

            var entry = new ConversationEntry { Question = question };
            conversations.Add(entry);

            try
            {
                isLoading = true;
                StateHasChanged();

                var answer = await AgentService.AskAsync(question);
                entry.Answer = answer;
            }
            catch (Exception ex)
            {
                errorMessage = "Sorry, something went wrong. Please try again or contact our support team.";
                Console.Error.WriteLine($"Agent error: {ex.Message}");
            }
            finally
            {
                isLoading = false;
                StateHasChanged();
            }
        }
    }
    ```

1. 少し時間をかけて、追加した `@code` ブロックを確認しましょう。

    `@code` ブロックには、コンポーネントのすべてのロジックが含まれています。

    - `ConversationEntry` は、各ユーザーの質問とエージェントの回答を組み合わせてチャット履歴を形成するシンプルな内部クラスです。
    - コンポーネントの状態は、`conversations` (完全なチャット履歴)、`currentQuestion` (テキスト入力バインド)、`isLoading` (重複する送信を防ぎ、"考えています..." インジケーターを表示する)、`errorMessage` (入力の下にエラーを表示する) の 4 つのフィールドで構成されます。
    - `HandleKeyDown` によって、Enter キーを押すと質問を送信できるようになります。送信ボタンと同じガード (読み込み中ではない、空ではない) がチェックされます。
    - `SubmitQuestion` は完全な送信フローを調整します。エラー状態をクリアし、入力テキストをキャプチャしてクリアし、新しい会話エントリをすぐに追加してから (ユーザーの質問がすぐに表示されるように)、`AgentService.AskAsync` を呼び出してエージェントの応答を取得します。 `StateHasChanged()` 呼び出しは、Blazor に UI の再レンダリングを強制します。"考えています..." の場合に 1 回表示され、応答が到着したときやエラーが発生したときにも表示されます。 `try/finally` パターンにより、API 呼び出しが失敗した場合でも、`isLoading` が常にリセットされることが保証されます。

1. 完了した **Support.razor** ファイルが次の構造であることを確認します:

    - 上部のページ ディレクティブ (`@page`、`@using`、`@attribute`、`@inject`)
    - 中央揃えの列を含むコンテナー レイアウト
    - 3 枚のカード:AI チャット サポート (メッセージ領域、入力領域、エラー表示)、連絡先情報 (メールと電話)、クイック リンク (ナビゲーション ショートカット)
    - `ConversationEntry` クラス、状態フィールド、`HandleKeyDown` および `SubmitQuestion` メソッドを含む `@code` ブロック

1. ターミナルで ContosoShop.Server ディレクトリを開き、次のコマンドを入力します。

    ```powershell
    dotnet build
    ```

    ビルドはエラーなしで成功します。

## エンドツーエンドの AI エージェント エクスペリエンスをテストする

このタスクでは、アプリケーションを実行し、さまざまなサポート クエリを使用して AI エージェントをテストし、それが正しく機能することを確認します。

そのためには、以下の手順を実行してください。

1. ターミナルからサーバー アプリケーションを起動するには、次のコマンドを入力します。

    ```powershell
    dotnet run
    ```

    起動時にエラーが発生した場合は、コンソールの出力を確認します。 HTTP ポートでリッスンしているアプリケーションが表示されます。

    エラーが表示された場合は、前のタスクの各手順を完了していること、およびコードを正しく入力したことを確認します。 コードの確認後もエラーが解決しない場合は、GitHub Copilot で GitHub Copilot SDK リポジトリ (`https://github.com/github/copilot-sdk`) をポイントし、AI アシスタントに問題のデバッグを依頼できます。

1. ブラウザーを開き、指定した HTML ポートに移動します。

    たとえば、`Now listening on: http://localhost:5266` のような出力が表示される場合があります。 その場合は、ブラウザー ウィンドウで `http://localhost:5266` を開きます。 ContosoShop Eコマース サポート ポータルのログイン ページが表示されます。

1. デモ用の、Mateo の資格情報を使用してログインします。

    メールに `mateo@contoso.com` を入力し、パスワードには `Password123!` を入力し、**[ログイン]** を選択します。

1. **[サポートへのお問い合わせ]** ページに移動します。

1. 少し時間を取って、ページを確認してください。

    "近日公開予定" プレースホルダーではなく、対話型の AI チャット サポート インターフェイスが表示されているはずです。 チャット領域には、作業の開始に役立つプロンプトの例が表示されます。

1. エージェントの、**注文の状態を確認する**機能をテストするには、次のプロンプトを入力し、**[送信]** を選択します (または Enter キーを押します)。

    ```plaintext
    What's the status of order #1001?
    ```

    エージェントは、配達日や配達済みアイテムなど、注文 #1001 に関する詳細を応答するはずです。 応答には、データベース内の実際のデータが反映されている必要があります。

    応答が注文 #1001 の [注文] ページに表示される内容と一致していることを確認します。

1. エージェントの、**すべての注文を一覧表示する**機能をテストするには、次のプロンプトを入力します。

    ```plaintext
    Show me all my orders
    ```

    エージェントは、`get_user_orders` ツールを使用し、10 件の Mateo の注文の要約リスト、その状態、金額を返す必要があります。

1. エージェントの、**返品を処理する**機能をテストするには、次のプロンプトを入力します。

    ```plaintext
    I want to return order #1008
    ```

    エージェントは、注文 #1008 ("配達済み") の返品を処理し、払い戻し金額を確認する必要があります。

    AI 応答が表示された後:

    - **[注文]** ページに移動して、今度は注文 #1008 に "返品済み" 状態が表示されていることを確認します。

1. エージェントの、**注文内の 1 つのアイテムの返品を処理する**機能をテストするには、次のプロンプトを入力します:

    ```plaintext
    I want to return 1 Desk Lamp from order #1005
    ```

    エージェントで、注文 #1005 内で指定されたアイテムの返品が処理され、払い戻し金額が確認されます。

    AI 応答が表示された後:

    - **[注文]** ページに移動して、今度は注文 #1005 に "一部返品" 状態が表示されていることを確認します。
    - 注文 #1005 の注文の詳細を開き、1 つのデスク ランプに対して "返品済み" 状態が表示されていることを確認します。

1. エージェントの、**返品できない注文を処理する**機能をテストするには、次のプロンプトを入力します。

    ```plaintext
    I want to return order #1010.
    ```

    注文 #1010 の状態は "処理中" なので、返品はできません。 エージェントは、注文を返品する前に配達する必要があることを説明する必要があります。

1. エージェントの、**存在しない注文を処理する**機能をテストするには、次のプロンプトを入力します。

    ```plaintext
    Where is my order #9999?
    ```

    エージェントは、ユーザーのアカウントに関連付けられている注文 #9999 が見つからなかったことを応答するはずです。

1. エージェントの、**対象外の質問を処理する**機能をテストするには、次のプロンプトを入力します。

    ```plaintext
    What's the weather like today?
    ```

    エージェントは、注文関連の問い合わせのみを支援し、他のチャネルを介したサポートへの連絡を提案できることを、丁寧に説明する必要があります。

1. テストが完了したら、ターミナルに戻り、**Ctrl + C** キーを押してアプリケーションを停止します。

## まとめ

この演習では、GitHub Copilot SDK を使用して AI 搭載のカスタマーサポート エージェントを ContosoShop Eコマース サポート ポータルに正常に統合しました。 あなた: 

- 既存のアプリケーション サービスを使って、AI エージェントが呼び出して注文を検索し、返品を処理できる**バックエンド ツールを作成しました** (`SupportAgentTools`)。
- `CopilotClient` シングルトンを使用して **Copilot SDK を構成**し、`AIFunctionFactory.Create` を使用してカスタム システム プロンプトとツール定義を使用してセッションを作成しました。
- ユーザーの質問を受け付け、エージェント セッションを作成し、AI 生成の応答を返す **API エンドポイント** (`SupportAgentController`) をビルドしました。
- **Blazor フロントエンドを更新し**、対話型チャット インターフェイスをサポート ページに搭載しました。
- 注文の検索、返品、エラー処理、対象外のトピックの誘導など、実際のシナリオで**統合をテストしました**。

ビジネス ロジックをツールとして定義し、それを AI エージェント ランタイムに登録し、API を介してエージェントを公開する、というこのパターンは、eコマース サポート以外にも、数多くの分野に適用できます。 IT ヘルプデスクの自動化、CRM アシスタント、または AI エージェントがユーザーの代わりにアクションを実行する必要があるあらゆるシナリオにも、同じアプローチを適用できます。

## クリーンアップ

演習が完了したので、少し時間を取って環境をクリーンアップしましょう。

- サーバー アプリケーションがまだ実行中の場合は停止します (ターミナルで **Ctrl + C** キーを押します)。
- GitHub アカウントまたは GitHub Copilot サブスクリプションに、保持したくない変更を加えていないことを確認します。
- 必要に応じて、リポジトリのローカル クローンをアーカイブするか削除します。
