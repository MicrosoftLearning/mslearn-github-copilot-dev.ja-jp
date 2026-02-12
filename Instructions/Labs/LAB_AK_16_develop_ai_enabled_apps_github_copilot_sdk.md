<!-- ---
lab:
    title: 'Exercise - Integrate an AI Agent into existing apps using GitHub Copilot SDK'
    description: 'Learn how to integrate an AI Agent into existing applications using GitHub Copilot SDK to automate tasks and enhance functionality.'
--- -->

# GitHub Copilot SDK を使用して AI エージェントを既存のアプリに統合する

GitHub Copilot SDK は、GitHub Copilot CLI の基盤となっているエンジンを、プログラミング可能な SDK として公開しています。 これにより、アプリケーションにエージェント型の AI ワークフローを埋め込むことができます。これには、AI にコードを呼び出させるカスタム ツールも含まれます。

この演習では、AI 搭載のカスタマーサポート エージェントを ContosoShop Eコマース サポート ポータルに統合します。 最終的に、[サポートへのお問い合わせ] ページで、ユーザーが質問をしたり (たとえば、"私の注文はどこにありますか?" または "商品を返品する必要があります")、AI エージェントから役に立つ自動化された回答を受けたりすることができるようになります。 エージェントは、バックエンド ツール (注文の状態の確認や返品の開始など) を使用してクエリを解決します。

この演習の所要時間は約 **60** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。

- Git 2.48 以降
- .NET SDK 8.0 以降
- C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code
- GitHub Copilot CLI がインストールされ、PATH に設定されていること






**すること:環境に GitHub Copilot CLI が存在しない場合、インストールする手順を更新してください。**
ラボ環境を構成する方法については、ブラウザーで次のリンクを開いてください: 「<a href="https://go.microsoft.com/fwlink/?linkid=2345907" target="_blank">GitHub Copilot SDK ラボ環境を構成する</a>」。






## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 あなたの会社は、Contoso Corporation という名前のクライアント向けに ContosoShop Eコマース サポート ポータル (ASP.NET Core バックエンドを備えた Blazor WebAssembly アプリケーション) を開発しました。 このアプリケーションには、注文管理、商品の返品、在庫追跡が含まれます。 Contoso から、注文の詳細を検索し、顧客の代わりに返品を開始できる AI 搭載のカスタマーサポート エージェントを使用して、既存の [サポートへのお問い合わせ] ページを強化するよう依頼されています。

ContosoShop のアプリケーションでは、次の 3 つのプロジェクト アーキテクチャを使用します。

- **ContosoShop.Server**:Entity Framework Core、ID 認証、SQLite を備えた ASP.NET Core Web API。
- **ContosoShop.Client**:ブラウザーで実行され、サーバー API を呼び出す Blazor WebAssembly SPA。
- **ContosoShop.Shared**:モデル、DTO、列挙型を含む共有クラス ライブラリ。

このラボでは、さまざまな状態 (処理中、発送済み、配達済み、返品済み) にわたる 20 件のサンプル注文と、2 人のデモ ユーザー (Mateo Gomez と Megan Bowen) を使用してアプリケーションをテストできます。

この演習には、次のタスクが含まれています。

1. スターター アプリケーションを確認し、正常に実行されていることを確認します。
1. GitHub Copilot SDK NuGet パッケージを追加し、エージェント ツール サービスを作成します。
1. Copilot SDK エージェントを構成し、API エンドポイントを公開します。
1. Blazor フロントエンドを更新してエージェントとやり取りします。
1. エンドツーエンドの AI エージェント エクスペリエンスをテストします。

## タスク 1:スターター アプリケーションを確認し、正常に実行されていることを確認する

AI エージェントを統合する前に、既存のコードベースについて理解し、アプリケーションが正しく実行されていることを確認する必要があります。

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

    GitHub では、インポート プロセスを使用して、アカウントに新しいリポジトリを作成します。

    > **注**:インポート プロセスが完了するまでに 1、2 分かかる場合があります。 インポート プロセスが完了するまで待ちます。

    GitHub に進行状況のインジケーターが表示され、インポートが完了すると通知されます。

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

1. 少し時間をかけて、プロジェクトの構造を確認してみましょう。

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
    │   ├── Models/                       (Order, OrderItem, Product, User, etc.)
    │   └── DTOs/                         (InventorySummary, ReturnItemRequest)
    └── ContosoShopSupportPortal.slnx     (Solution file)
    ```

    > **注**:ContosoShop.Server プロジェクト内の App_Data フォルダーが SQLite データベースに必要なので、ローカル クローンに含まれていることを確認します。 App_Data フォルダーが表示されない場合は、手動で作成します。 アプリケーションを、初めて実行するときに、このフォルダーに SQLite データベース ファイルが作成されます。

1. **ContosoShop.Server/Program.cs** ファイルを開き、アプリケーションの構成を確認します。

    次の主要な構成領域に注目してください。

    - データ アクセス用の SQLite を備えた Entity Framework Core
    - cookie ベース セッションを使用する認証用の ASP.NET Core Identity
    - `IEmailService`、`IInventoryService`、`IOrderService` のサービスの登録
    - 起動時に `DbInitializer.InitializeAsync` を使用するデータベース シード処理
    - CORS、レート制限、CSRF 保護、セキュリティ ヘッダー ミドルウェア

1. **ContosoShop.Server/Controllers/OrdersController.cs** ファイルを開き、既存の API エンドポイントを確認します。

    注文コントローラーは次の内容を提供します。

    - `GetOrders` — 認証済みユーザーのすべての注文を取得する
    - `GetOrder` — 特定の注文と商品を取得する (所有権を検証する)
    - `ReturnOrderItems` — 配達済みの注文の商品レベルの返品を処理する

1. **ContosoShop.Server/Services/OrderService.cs** ファイルを開き、`ProcessItemReturnAsync` メソッドを確認します。

    この既存のメソッドは、注文が配達済みまたは (部分的に) 返品済み状態であることの検証、`OrderItemReturn` レコードの作成、在庫の更新、注文状態の再計算、確認メールの送信を行います。 これからビルドする AI エージェントも、同様のロジックを活用します。

1. **ContosoShop.Client/Pages/Support.razor** ファイルを開きます。

    [サポート] ページには現在、静的な連絡先情報と "AI チャット サポートが近日公開されます" というプレースホルダーが表示されていることに注目してください。 ここに対話型 AI チャット インターフェイスを追加します。

1. **ContosoShop.Server** ディレクトリでターミナルを開き、ソリューションをビルドします。

    ```powershell
    cd ContosoShop.Server
    dotnet build
    ```

    > **重要**:プロジェクトでは、既定で .NET 8 が使用されます。 .NET SDK の新しいバージョン (.NET 9 または .NET 10) がインストール済みで、.NET 8 ではない場合は、インストール済みのバージョンを対象とするようにプロジェクトを更新する必要があります。 .NET の新しいバージョンに更新するには、GitHub Copilot Chat ビューを開き、プロジェクト ファイルを環境にインストール済みの .NET のバージョンに更新するように、GitHub Copilot に依頼します。 たとえば、次のように依頼できます。".NET 10 を対象とするようにプロジェクトを更新してください。 NuGet パッケージやプロジェクト参照など、関連するすべてのリソースを必ず更新してください。 必要なすべての更新を完了したら、すべてのプロジェクトが正常にビルドされていることを確認してください。" AI アシスタントは、ソリューションの更新に役立ちます。

    ビルドはエラーなしで正常に完了するはずです (警告が表示される可能性があります)。

1. サーバー アプリケーションを起動します。

    ```powershell
    dotnet run
    ```

    サーバーは `http://localhost:5266` でリッスンを開始します。

1. ブラウザーを開き、 `http://localhost:5266` に移動します。

    ContosoShop のログイン ページが表示されます。 localhost 開発証明書の証明書警告を受け入れます。

1. デモ用資格情報を使用してサインインします。

    メールに `mateo@contoso.com` を入力し、パスワードには `Password123!` を入力し、**[ログイン]** を選択します。

1. **[注文]** ページに移動し、注文が表示されることを確認します。

    さまざまな状態 (配達済み、発送済み、処理済み、返品済み) の Mateo の 10 件の注文が表示されます。

1. **[サポートへのお問い合わせ]** ページに移動します。

    "対話型の AI チャット サポートが近日公開されます" プレースホルダーが表示されます。 これが、この後のタスクで強化するページです。

1. ナビゲーション メニューで **[ログアウト]** を選択します。

1. サーバーが実行されているターミナルに戻り、**Ctrl + C** キーを押してアプリケーションを停止します。

1. GitHub Copilot CLI がインストール済みで、認証されていることを確認します。

    ```powershell
    copilot --version
    ```

    バージョン番号 (`0.0.399` など) が表示されます。 コマンドが見つからない場合は、<a href="https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli" target="_blank">Copilot CLI インストール ガイド</a>に従って Copilot CLI をインストールします。

    > **注**:GitHub Copilot SDK は、サーバー モードで Copilot CLI と通信します。 SDK により CLI プロセスライフサイクルが自動的に管理されますが、CLI をインストールして PATH でアクセスできる必要があります。

## タスク 2:GitHub Copilot SDK を追加し、エージェント ツール サービスを作成する

このタスクでは、GitHub Copilot SDK NuGet パッケージをサーバー プロジェクトに追加し、AI エージェントが注文を検索して返品を処理するために使用するツールを実装するサービス クラスを作成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code の統合ターミナルを開き、**ContosoShop.Server** ディレクトリに移動します。

1. GitHub Copilot SDK NuGet パッケージを追加するには、次のコマンドを入力します。

    ```powershell
    dotnet add package GitHub.Copilot.SDK --prerelease
    ```

    これにより、SDK の最新プレビュー バージョンがインストールされます。 SDK により、AI エージェントをビルドするために `CopilotClient`、`CopilotSession`、および関連する種類が提供されます。

    > **注**:GitHub Copilot SDK は Technical Preview の段階ですが、インストールには `--prerelease` フラグが必要です。

1. `Microsoft.Extensions.AI` パッケージを追加するには、次のコマンドを入力します。

    ```powershell
    dotnet add package Microsoft.Extensions.AI
    ```

    GitHub Copilot SDK では、カスタム ツールを定義するために `Microsoft.Extensions.AI` が使用されます。 このパッケージには、AI エージェントが呼び出すことができるツールを作成するための `AIFunctionFactory` と関連する種類が用意されています。

1. パッケージが正しくインストールされていることを確認するには、プロジェクトをビルドします。

    ```powershell
    dotnet build
    ```

    ビルドはエラーなしで成功します。

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
                OrderStatus.Returned => "has been returned and a refund was issued",
                _ => "has an unknown status"
            };

            var itemSummary = string.Join(", ", order.Items.Select(i =>
                $"{i.ProductName} (qty: {i.Quantity}, ${i.Price:F2} each)"));

            return $"Order #{order.Id} {statusMessage}. " +
                   $"Order date: {order.OrderDate:MMMM dd, yyyy}. " +
                   $"Total: ${order.TotalAmount:F2}. " +
                   $"Items: {itemSummary}.";
        }

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
                    OrderStatus.Returned => "Returned",
                    _ => "Unknown"
                };
                return $"Order #{o.Id} - {status} - ${o.TotalAmount:F2} - Placed {o.OrderDate:MMM dd, yyyy}";
            });

            return $"You have {orders.Count} orders:\n" + string.Join("\n", summaries);
        }

        /// <summary>
        /// Processes a return for specific items in a delivered order.
        /// The AI agent calls this tool when a user wants to return items.
        /// </summary>
        public async Task<string> ProcessReturnAsync(int orderId, int userId)
        {
            _logger.LogInformation("Agent tool invoked: ProcessReturn for orderId {OrderId}, userId {UserId}", orderId, userId);

            var order = await _context.Orders
                .Include(o => o.Items)
                .FirstOrDefaultAsync(o => o.Id == orderId && o.UserId == userId);

            if (order == null)
            {
                return $"I could not find order #{orderId} associated with your account.";
            }

            if (order.Status != OrderStatus.Delivered && order.Status != OrderStatus.Returned)
            {
                return order.Status switch
                {
                    OrderStatus.Processing => $"Order #{orderId} is still being processed and cannot be returned yet. It must be delivered first.",
                    OrderStatus.Shipped => $"Order #{orderId} is currently in transit and cannot be returned until it has been delivered.",
                    _ => $"Order #{orderId} has a status of {order.Status} and cannot be returned."
                };
            }

            // Build return items list for all unreturned items
            var returnItems = order.Items
                .Where(i => i.RemainingQuantity > 0)
                .Select(i => new ReturnItem
                {
                    OrderItemId = i.Id,
                    Quantity = i.RemainingQuantity,
                    Reason = "Customer requested return via AI support agent"
                })
                .ToList();

            if (!returnItems.Any())
            {
                return $"All items in order #{orderId} have already been returned.";
            }

            var success = await _orderService.ProcessItemReturnAsync(orderId, returnItems);

            if (!success)
            {
                return $"I was unable to process the return for order #{orderId}. Please contact our support team for assistance.";
            }

            var refundAmount = order.Items
                .Where(i => i.RemainingQuantity > 0)
                .Sum(i => i.Price * i.RemainingQuantity);

            return $"I've processed the return for order #{orderId}. " +
                   $"A refund of ${refundAmount:F2} will be issued to your original payment method within 5-7 business days. " +
                   $"You will receive a confirmation email shortly.";
        }

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

1. 少し時間をかけて、`SupportAgentTools` クラスのコードを確認しましょう。

    このクラスは、カスタマー サービスの自動化のための AI サポート エージェント ツールを提供します。 主な機能:

    注文情報ツール:

    - **GetOrderDetailsAsync**:自然言語の応答を使用して、特定の注文の状態、発送日、商品の詳細を取得します
    - **GetUserOrdersSummaryAsync**:ユーザーが注文番号を指定しない場合に、そのユーザーのすべての注文を一覧表示します

    注文管理ツール:

    - **ProcessReturnAsync**:配達済みの注文の返品処理を自動化し、注文の状態に基づいて返品の適格性を検証し、返品されていないすべての商品の返品レコードを作成し、払い戻し金額を計算します
    - **SendCustomerEmailAsync**:注文に関する情報を含むフォローアップ メールを顧客に送信します

    設計特性:

    - すべてのメソッドは、AI の会話用に書式設定された、人間が判読できる文字列を返す
    - コンテキスト エラー メッセージを使用した組み込みの検証とエラー処理
    - 既存のサービス (OrderService、EmailService) およびデータベース コンテキストとの統合
    - すべてのツール呼び出しの包括的なログ
    - セキュリティのための userId 検証を使用したユーザー スコープの操作

1. **ContosoShop.Server/Program.cs** ファイルを開きます。

    Program.cs ファイルを使用して、依存関係の挿入に SupportAgentTools を登録します。

1. サービス登録セクションを見つけます (既存の `builder.Services.AddScoped<IOrderService, OrderService>();` 行の後)。

1. 次の行を追加して、`SupportAgentTools` サービスを登録します。

    ```csharp
    // Register AI agent tools service
    builder.Services.AddScoped<SupportAgentTools>();
    ```

1. 更新された 2 つのファイルを保存します。

1. ターミナルで、プロジェクトをビルドします。

    ```powershell
    dotnet build
    ```

    ビルドが成功します。 エラーが発生した場合は、`SupportAgentTools.cs` ファイルを確認して、すべての `using` ステートメントと参照が正しいことを確認します。 必要に応じて、GitHub Copilot を使用してデバッグを行うことができます。

## タスク 3:Copilot SDK エージェントを構成し、API エンドポイントを公開する

このタスクでは、`CopilotClient` シングルトンを作成し、依存関係の挿入に登録し、ユーザーの質問を受け付けて AI エージェントの応答を返す新しい API コントローラーを作成します。

そのためには、以下の手順を実行してください。

1. **ContosoShop.Server/Program.cs** ファイルを開きます。

    Program.cs ファイルを使用して、依存関係の挿入で CopilotClient をシングルトンとして登録します。

1. ファイルの先頭の、既存の `using` ステートメントの後に、次の `using` ステートメントを追加します。

    ```csharp
    using GitHub.Copilot.SDK;
    ```

1. サービス登録セクション (他の `builder.Services.Add...` 行の近く) を見つけます。 次のコードを追加して、`CopilotClient` シングルトンを作成して登録します。

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

1. アプリケーションのシャットダウン時に `CopilotClient` が適切に破棄されるようにするには、`var app = builder.Build();` 行の後 (ただし、データベース初期化ブロックの前) に次のコードを追加します。

    ```csharp
    // Ensure CopilotClient is started
    var copilotClient = app.Services.GetRequiredService<CopilotClient>();
    await copilotClient.StartAsync();
    ```

1. ファイルを保存します。

1. Visual Studio Code のエクスプローラー ビューで、**ContosoShop.Shared/Models** フォルダーを右クリックし、**[新しいファイル]** を選択します。

1. ファイルに **SupportQuery.cs** という名前を付けます。

1. 次のコードを追加します。

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

1. 少し時間をかけて `SupportQuery` および `SupportResponse` モデルを確認しましょう。

    このファイルは、AI サポート エージェント通信のデータ転送モデルを定義します。

    SupportQuery

    - AI サポート エージェントに送信された顧客の質問を表します
    - 検証を含む Question プロパティが含まれています: 必須、1 から 1000 文字
    - クライアントからサーバーへの要求ペイロードとして使用されます

    SupportResponse

    - 顧客に対する AI エージェントの応答を表します
    - エージェントの応答を含む Answer プロパティが含まれます
    - サーバーからクライアントへの応答ペイロードとして使用されます

    これらは、サポート チャット インターフェイスの軽量 DTO であり、Blazor クライアントと AI 搭載のサポート エンドポイント間の構造化された通信を可能にします。 シンプルな設計では、基本的な入力検証を使用したテキストベースの質問と応答の交換に焦点を当てます。

1. Visual Studio Code のエクスプローラー ビューで、**ContosoShop.Server/Controllers** フォルダーを右クリックし、**[新しいファイル]** を選択します。

1. ファイルに **SupportAgentController.cs** という名前を付けます。

1. 次のコードを追加します。

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
                        async ([Description("The order ID number to return")] int orderId) =>
                            await _agentTools.ProcessReturnAsync(orderId, userId),
                        "process_return",
                        "Process a return for a delivered order. Returns all unreturned items in the order and initiates a refund. Only works for orders with Delivered status."),

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
                            - Process returns for delivered orders using the process_return tool
                            - Send follow-up emails using the send_customer_email tool
                            
                            RULES:
                            - ALWAYS use the available tools to look up real data. Never guess or make up order information.
                            - Be friendly, concise, and professional in your responses.
                            - If a customer asks about an order, use get_order_details with the order number they provide.
                            - If a customer asks about their orders without specifying a number, use get_user_orders to list them.
                            - If a customer wants to return an order, confirm the order number first, then use process_return.
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

1. 少し時間をかけて `SupportAgentController` コードを確認しましょう。

    このコントローラーは、GitHub Copilot SDK を使用して AI 搭載のカスタマーサポート エージェントを実装します。 主な機能:

    AI エージェントの統合:

    - CopilotClient を使用して GPT-4.1 で AI セッションを作成する
    - エージェントのロールを ContosoShop サポート アシスタントとして定義するカスタム システム プロンプトを構成する
    - AI エージェントの明示的なルールと機能を提供する

    カスタム ツール (関数呼び出し):

    - get_order_details - ID で特定の注文を検索します
    - get_user_orders - 現在のユーザーのすべての注文を一覧表示します
    - process_return - 配達済みの注文の返品処理を自動化します
    - send_customer_email - 顧客にフォローアップ メールを送信します

    セキュリティとユーザー コンテキスト:

    - [承認] が必要です - 認証されたユーザーのみがアクセスできます
    - 要求から userId を抽出して、そのユーザーの範囲内の操作を確保します
    - すべてのツール呼び出しは、認証されたユーザーの ID にバインドされます

    セッション管理:

    - Copilot SDK を使用してイベントドリブン セッションを作成します
    - AssistantMessageEvent、SessionIdleEvent、SessionErrorEvent を処理します
    - 実行時間の長い要求に対し 30 秒のタイムアウト保護を適用します
    - 包括的なエラー処理とログ

    API の設計:

    - 単一エンドポイント:POST /api/supportagent/ask
    - SupportQuery を受け入れ、SupportResponse を返します
    - エラー時にわかりやすいエラー メッセージを返します

    これにより、お客様が自然言語で質問することができる会話型 AI サポートが可能になり、エージェントは、ユーザーを支援するために呼び出すツールを自律的に決定できます。

1. **ContosoShop.Server/Program.cs** ファイルを開きます。

1. CORS 構成セクションで、先ほど作成した API エンドポイントで必要な `GET` および `POST` メソッドが許可されていることを確認します。

   既存の構成では、`GET` および `POST` メソッドで十分です。

    ```csharp
    .WithMethods("GET", "POST") // Only required methods
    ```

1. プロジェクトをビルドするには、ターミナルで次のコマンドを入力します。

    ```powershell
    dotnet build
    ```

    ビルドはエラーなしで成功します。 `GitHub.Copilot.SDK` の種類に関連するエラーが表示される場合は、NuGet パッケージが正しくインストールされていることを確認します。

## タスク 4:Blazor フロントエンドを更新してエージェントとやり取りする

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

    これは、AI サポート エージェント バックエンドとやり取りするクライアント側 HTTP サービスです。 主な機能:

    シンプルな API ラッパー:

    - 単一メソッド AskAsync(string question) - サポート エージェント API エンドポイントにユーザーの質問を送信します
    - サーバー上の POST /api/supportagent/ask に POST を送信します

    通信処理:

    - SupportQuery DTO で質問をラップします
    - HttpClient.PostAsJsonAsync を使用して JSON の自動シリアル化を行います
    - 応答を SupportResponse オブジェクトに逆シリアル化します

    エラー管理:

    - HTTP 状態コードのエラーを確認します
    - 成功していない応答に関する詳細なエラー情報を含む HttpRequestException をスローします
    - 応答解析が失敗した場合にフォールバック メッセージを提供します

    設計パターン:

    - サービス層パターンに従ったシン クライアント ラッパー
    - テスト容易性と適切な有効期間管理のために挿入された HttpClient
    - Blazor コンポーネント (Support.razor など) で使用され、HTTP の詳細を直接処理せずに AI エージェントとやり取りします

    このサービスは HTTP 通信の複雑さを抽象化し、Blazor コンポーネントが AI サポート エージェントに質問するためのクリーンなインターフェイスを提供します。

1. **ContosoShop.Client/Program.cs** ファイルを開きます。

1. ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using ContosoShop.Client.Services;
    ```

1. サービス登録セクションを見つけます (既存の `builder.Services.AddScoped...` 行の近く)。 次の行を追加します。

    ```csharp
    builder.Services.AddScoped<SupportAgentService>(sp =>
        new SupportAgentService(sp.GetRequiredService<HttpClient>()));
    ```

1. ファイルを保存します。

### 手順 4.3:Support.razor ページをチャット インターフェイスに更新する

1. **ContosoShop.Client/Pages/Support.razor** ファイルを開きます。

1. ファイルの内容全体を次のコードに置き換えます。

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

1. 少し時間をかけて更新された `Support.razor` コードを確認しましょう。

    この Blazor ページでは、AI 搭載のチャット サポート インターフェイスを顧客に提供します。 主な機能:

    チャット インターフェイス:

    - ユーザーの質問と AI エージェントの応答を表示するメッセージ履歴を含むリアルタイムの会話型 UI
    - エージェントの処理中に読み込みインジケーター ("考えています....") を表示
    - 高さが 300 から 500 ピクセルの自動スクロール チャット領域
    - 素早いメッセージ送信のための Enter キーのサポート
    - チャットが空の場合に役立つプロンプトの例

    ユーザー操作:

    - 送信ボタンを含む入力フィールド (読み込み中は無効)
    - バッジラベルの付いたメッセージを含む会話履歴の表示 (あなた/エージェント)
    - 送信後に入力をクリア
    - ユーザー フレンドリなメッセージでのエラー処理

    その他のサポート オプション:

    - 連絡先情報カード - メール (support@contososhop.com) と電話番号 (1-800-CONTOSO) 応答時間の詳細を含む
    - クイック リンク カード - 注文、返品情報、追跡の詳細を表示するためのリンク

    状態管理:

    - Q&A ペアの一覧で会話履歴を保持
    - 送信の重複を防ぐため読み込み状態を追跡
    - セッション中はチャット履歴を保持

    セキュリティ:

    - [承認] 属性 - 認証が必要
    - セキュリティで保護されたバックエンド通信のため SupportAgentService と統合

    UI/UX:

    - info/primary の配色を使用したブートストラップ スタイル
    - AI チャット用ロボット アイコン、連絡先情報用ヘッドセット アイコン
    - 大きな画面に中央揃えの列があるレスポンシブ レイアウト
    - 複数行のエージェント応答に対して事前書式設定済みのテキストをサポート

    このページは、自律 AI アシスタンスと従来の連絡方法を組み合わせた、完全なカスタマーサポート ハブとして機能します。

1. ターミナルで ContosoShop.Server ディレクトリを開き、次のコマンドを入力します。

    ```powershell
    dotnet build
    ```

    ビルドはエラーなしで成功します。

## タスク 5:エンドツーエンドの AI エージェント エクスペリエンスをテストする

このタスクでは、アプリケーションを実行し、さまざまなサポート クエリを使用して AI エージェントをテストし、それが正しく機能することを確認します。

そのためには、以下の手順を実行してください。

1. ターミナルからサーバー アプリケーションを起動するには、次のコマンドを入力します。

    ```powershell
    dotnet run
    ```

    起動時にエラーが発生した場合は、コンソールの出力を確認します。 HTTPS および HTTP ポートでリッスンしているアプリケーションが表示されます。

1. ブラウザーを開き、 `http://localhost:5266` に移動します。

1. デモ用の資格情報でサインインします。

    メールに `mateo@contoso.com` を入力し、パスワードには `Password123!` を入力し、**[ログイン]** を選択します。

1. **[サポートへのお問い合わせ]** ページに移動します。

    "近日公開予定" プレースホルダーではなく、対話型の AI チャット サポート インターフェイスが表示されているはずです。 チャット領域には、作業の開始に役立つプロンプトの例が表示されます。

1. エージェントの、**注文の状態を確認する**機能をテストするには、次の質問を入力し、**[送信]** を選択します (または Enter キーを押します)。

    ```plaintext
    What's the status of order #1001?
    ```

    エージェントは、注文 #1001 に関する詳細を応答する必要があります。これには状態、注文日、商品、合計金額が含まれます。 応答には、データベース内の実際のデータが反映されている必要があります。

    応答が注文 #1001 の [注文] ページに表示される内容と一致していることを確認します。

1. エージェントの、**すべての注文を一覧表示する**機能をテストするには、次の質問を入力します。

    ```plaintext
    Show me all my orders
    ```

    エージェントは、`get_user_orders` ツールを使用し、10 件の Mateo の注文の要約リスト、その状態、金額を返す必要があります。

1. エージェントの、**返品を処理する**機能をテストするには、次の質問を入力します。

    ```plaintext
    I want to return order #1005
    ```

    エージェントは、注文 #1005 (状態は配達済み) の返品を処理し、払い戻し金額を確認する必要があります。 応答の後:

    - **[注文]** ページに移動して、今度は注文 #1005 に "返品済み" 状態が表示されていることを確認します。
    - サーバー コンソールの出力で、`EmailServiceDev` からのメール通知ログを確認します。

1. エージェントの、**返品できない注文を処理する**機能をテストするには、次の質問を入力します。

    ```plaintext
    Can I return order #1010?
    ```

    注文 #1010 の状態は "処理中" であり、返品はできません。 エージェントは、注文を返品する前に配達する必要があることを説明する必要があります。

1. エージェントの、**存在しない注文を処理する**機能をテストするには、次の質問を入力します。

    ```plaintext
    Where is my order #9999?
    ```

    エージェントは、ユーザーのアカウントに関連付けられている注文 #9999 が見つからなかったことを応答する必要があります。

1. エージェントの、**対象外の質問を処理する**機能をテストするには、次の質問を入力します。

    ```plaintext
    What's the weather like today?
    ```

    エージェントは、注文関連の問い合わせのみを支援し、他のチャネルを介したサポートへの連絡を提案できることを、丁寧に説明する必要があります。

1. テストが完了したら、ターミナルに戻り、**Ctrl + C** キーを押してアプリケーションを停止します。

**トラブルシューティングのヒント:**

- エージェントの応答が奇妙と思われる場合、またはツールを使用していない場合は、サーバー コンソールでエラー メッセージを確認します。 次のような一般的な問題があります。
  - **CopilotClient の接続エラー**:Copilot CLI がインストール済みで、サインインしていることを確認します。 `copilot --version` を実行して確認します。
  - **認証エラー**:Copilot CLI にログインしていることを確認します。 `copilot auth status` を実行して確認します。
  - **タイムアウト エラー**:エージェントのタイムアウトは 30 秒です。 応答が遅い場合は、ネットワーク接続を確認します。
  - **ツール呼び出しエラー**:サーバー ログで `SupportAgentTools` からのメッセージを確認します。 ログ出力には、呼び出されているツールと返されるデータが表示されます。

## クリーンアップ

演習が完了したので、少し時間を取って環境をクリーンアップしましょう。

- サーバー アプリケーションがまだ実行中の場合は停止します (ターミナルで **Ctrl + C** キーを押します)。
- GitHub アカウントまたは GitHub Copilot サブスクリプションに、保持したくない変更を加えていないことを確認します。
- 必要に応じて、リポジトリのローカル クローンをアーカイブするか削除します。

## まとめ

この演習では、GitHub Copilot SDK を使用して AI 搭載のカスタマーサポート エージェントを ContosoShop Eコマース サポート ポータルに正常に統合しました。 あなた: 

- 既存のアプリケーション サービスを活用して、AI エージェントが呼び出して注文を検索し、返品を処理できる**バックエンド ツール** (`SupportAgentTools`) を作成しました。
- `CopilotClient` シングルトンを使用して **Copilot SDK を構成**し、`AIFunctionFactory.Create` を使用してカスタム システム プロンプトとツール定義を使用してセッションを作成しました。
- ユーザーの質問を受け付け、エージェント セッションを作成し、AI 生成の応答を返す **API エンドポイント** (`SupportAgentController`) をビルドしました。
- **Blazor フロントエンドを更新し**、対話型チャット インターフェイスをサポート ページに搭載しました。
- 注文の検索、返品、エラー処理、対象外のトピックの誘導など、実際のシナリオで**統合をテストしました**。

ビジネス ロジックをツールとして定義し、それを AI エージェント ランタイムに登録し、API を介してエージェントを公開するこのパターンは、eコマース サポート以外にも、数多くの分野に適用できます。 IT ヘルプデスクの自動化、CRM アシスタント、または AI エージェントがユーザーの代わりにアクションを実行する必要があるあらゆるシナリオにも、同じアプローチを適用できます。
