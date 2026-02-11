<!-- ---
lab:
    title: 'Exercise - Integrate an AI Agent into existing apps using GitHub Copilot SDK'
    description: 'Learn how to integrate an AI Agent into existing applications using GitHub Copilot SDK to automate tasks and enhance functionality.'
--- -->

# GitHub Copilot SDK を使用して AI エージェントを既存のアプリに統合する

GitHub Copilot SDK を使用すると、開発者は AI を利用したコードの提案と入力候補をアプリケーションに統合できます。

この演習では、**TBD** を行います

この演習の所要時間は約 **45** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。Git 2.48 以降、.NET SDK 8.0 以降、C# 開発キットと GitHub Copilot チャット拡張機能を備えた Visual Studio Code、SQLite、または SQL Server LocalDB。

ラボ環境を構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2345907" target="_blank">GitHub Spec Kit のラボ環境を構成する</a>。

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 企業は、拡張する必要があります



---

# ラボ演習:AI エージェントを eコマース サポート ポータルに統合する

## ラボのタイトル:インテリジェント カスタマー サポート エージェントと GitHub Copilot SDK の統合

### ラボの目的

このラボでは、GitHub Copilot SDK を使用して AI 搭載のサポート エージェントを統合して、ContosoShop Eコマース サポート ポータルを強化します。 最終的に、[サポートに問い合わせる] ページで、ユーザーが質問をしたり (例: "私の注文はどこにありますか?" または "注文を返品する必要があります")、AI エージェントから役に立つ自動化された回答やアクションを受けたりすることができるようになります。 エージェントは、バックエンドの機能 (注文の状態の確認や返品の開始など) を使用してクエリを解決します。 これにより、AI エージェント スキル (ツール) の定義、ASP.NET Core 環境での Copilot SDK の使用、エージェントとやり取りするための Blazor UI の更新に関する実践的な経験が得られます。

このラボは連続したタスクに分割されており、それぞれがその前のタスクを基盤にしています。

1. プロジェクトのセットアップと前提条件 - 基本アプリが実行され、必要なツールがインストールされていることを確認します。
2. AI エージェントのロールとツールを定義する – エージェントで実行する内容を決定し、必要な機能 (ツール) を特定します (計画段階)。
3. エージェント用のバックエンド ツールを実装する – エージェントのアクション (GetOrderDetails、ProcessRefund、SendCustomerEmail) として機能する関数を Web API で作成します。
4. Copilot SDK エージェントを登録し構成する – バックエンドで Copilot SDK を初期化し、エージェントのペルソナ/目標を定義し、手順 3 の関数をエージェントで使用できるツールとして登録します。
5. エージェント API エンドポイントを公開する – フロントエンドが呼び出すことができる新しい API エンドポイントを作成します。これにより、ユーザーのクエリが Copilot エージェントに送信され、エージェントの応答が返されます。
6. エージェント チャット用 Blazor フロントエンドを更新する – [サポートに問い合わせる] ページに、ユーザーが質問を送信し、AI エージェントの回答をリアルタイムで表示するためのシンプルなチャット インターフェイスをビルドします。
7. エンドツーエンドエクスペリエンスをテストする – アプリケーションを実行し、さまざまなサポート クエリを試し、タスクを実行しているエージェントを観察します (注文の状態の確認や返品処理など)。
8. (省略可能) ログとテレメトリを追加する – エージェントの操作のログを実装し、運用環境でこれを監視する方法について説明します (省略可能ですが、お勧めの手順です)。

ラボ全体で、各ステップが実行される理由と、それが基本アプリの設計をどのように活用しているかを強調します。 最終的には、アプリに動作する AI エージェントだけでなく、他のユース ケースに適用できる基になる統合パターンを明確に理解できるようになります。

## 始める前に – 前提条件とセットアップ

**Azure シミュレーターと資格情報:** このラボには Azure サブスクリプションは必要ありません。すべてがローカルで実行されます。 ただし、GitHub Copilot SDK (テクニカル プレビュー) と、Copilot のアクセス権を持つ GitHub アカウントにアクセスする必要があります。 次のことを行っておきます。

- お使いのアカウントで有効な GitHub Copilot CLI/SDK テクニカル プレビューのアクセス。 (サインアップは GitHub を通じて行われました。アクセス権がある場合は、github-copilot-cli または SDK パッケージを使用できます)。
- Copilot CLI デーモンを使用する場合は、Node.js 16 以上 (SDK をローカルで動作させるため)。 ただし、.NET では、SDK を直接接続できます。NuGet パッケージ アプローチを使用します。

**開発環境:**.NET 8 SDK がインストールされていることを確認します。 確認するには `dotnet --version` を使用します (8.x にする必要があります)。 Visual Studio Code または Visual Studio 2022 で作業します。どちらでもかまいません。 C# 拡張機能がある VS Code は軽量で適切に動作します。GitHub Copilot VSCode 拡張機能がインストール済みの場合も問題ありませんが、SDK の使用には必要ありません。 また、ソリューションが開かれていることを確認し、(前の手順で確認したように) 基本アプリを実行できることを確認します。 まだ完了していない場合は、アプリケーションを実行し、エラーなしで [注文] と [サポート] ページを表示できることを確認します。

**GitHub Copilot SDK パッケージ:**.NET SDK は NuGet パッケージ (GitHub.Copilot.SDK など) として提供されています。 このラボでは、このパッケージをサーバー プロジェクトに追加します。 (エージェントはサーバー側で実行されるため、クライアント プロジェクトには必要ありません)重要: Copilot SDK は、プライベート NuGet フィードにある可能性があります (プレビューの配布方法によって異なります)。 その場合は、認証とインストールの方法に関する手順が提供されます。 このラボのシナリオでは、指定されたローカル パッケージまたはフィードのいずれかによって、それを追加する方法があると仮定します。 使用するパッケージのバージョンは最新のプレビューです ("0.5.0-preview" など。実際のバージョンは異なる場合があります)。

**熟知度のまとめ:** このラボでは、バックエンドとフロントエンドで C# コードを記述します。 次の場所を確認しておきます。

- OrderController.cs (または、OrderService.cs) を開き、返品が処理される場所を改めて確認します。
- Support.razor を開いて、サポートに問い合わせるためのプレースホルダーを確認します。
- 新しいファイルを追加するか (エージェント構成用の場合があります)、既存のファイルを変更します。 後で変更を比較できるように、続行する前に新しい Git ブランチまたはバックアップを作成することをお勧めします。

次に、タスクについて説明します。

## タスク 1:エージェントのロールとツールを定義する (計画)

コーディングを行う前に、AI エージェントが処理するタスクと、それらを実行するために必要なバックエンド機能 (ツール) を計画することが重要です。 シナリオと基本機能から、次の点を特定します。

**エージェントのロール (ペルソナ):** エージェントは、ContosoShop の自動化されたカスタマー サポート アシスタントとして機能します。 注文の状態に関する質問に回答し、注文関連のアクション (返品の開始など) を実行できる必要があります。 カスタマー サービス担当者として、わかりやすい簡潔な方法でやり取りします。 エージェントのシステム プロンプトでこのロールをエンコードします。

**サポートする一般的なユーザー クエリ:**

a. "注文 [#] はどこにありますか?" – ユーザーは、特定の注文の状態を知りたいと考えています (注文番号または、発送済みか配送済みかどうかが提供される場合があります)。 エージェントは最新の状態を取得して応答する必要があります。たとえば、"注文 #1002 は 3 月 1 日に発送され輸送中です。 予想される配達日は、3 月 5 日です"。

b. "[アイテム/注文] を返品したいです。" – ユーザーが注文を受け取り、それを返したいと考えています。 エージェントは適格性を確認し (配達済みか、許容時間内か)、次に、返品を処理し (状態を更新し、返金の開始を確認する)、次のような確認の形で応答する必要があります。"注文 #1001 の返品を処理しました。 5 から 7 日以内に、お使いのカードに 59.99 ドルの返金が行われます。"

c. "サポートに問い合わせるにはどうすればよいですか?" などの一般的な質問 – エージェントはこれらを処理するか、受け流すことができます (この場合は、サポート部門が応答しているため、"注文に関するご質問はこちらでお答えします。それ以外のご質問については support@contososhop.com にメールでお問い合わせください。" のように応答する場合があります)。

d. 対象外の質問 (例: "新しいノート PC を購入できますか?" のような、 サポート部門ではなく販売に関する質問、または "ジョークを言ってみて" のような場合) には、サポートの問題に重点を置いていることを丁寧に応答する必要があります。サポートされていないクエリを受け流す (または、シンプルに "こちらでは、注文に関する問題にお答えします。" と応答する) 手順を含めます。

**必要なツール:** 上記のクエリに基づいて、エージェントはサーバーでアクションを実行する必要があります。

a. **GetOrderDetails (注文を検索する)** – 注文 ID を指定すると (または、ユーザーが指定しない場合、エージェントは最新の注文を想定できます)、注文の状態とおもな情報 (配達日など) を取得します。 これは、アプリに既に存在する機能にマップされます (OrderService は ID で注文を取得できます)。 注文の状態の概要を返すツール関数 GetOrderDetails(orderId) を実装します。 "Order 1002 は発送済みです (到着予定 3 月 5 日)" のようなテキスト、 または書式設定できる構造化オブジェクトが返される場合があります。

b. **ProcessRefund (返品を開始する)** – 注文 ID を指定すると、返品済みとしてマークし、返金をログに記録します。 アプリには、この API エンドポイントが既に存在しているので、同じロジックを呼び出すことができます。 既存の返品機能をラップするが、エージェントの使用に合わせて調整されたツールである ProcessRefund(orderId) を実装します (これにより OrderService.ProcessReturn と EmailService が直接呼び出される場合があります)。 エージェントからユーザーに通知するための確認メッセージまたはデータが返されます。

c. **SendCustomerEmail (メールを確認する)** – ProcessRefund ロジックは既に EmailService 経由でメールをトリガーしていますが、エージェントがフォローアップ メールを明示的に送信できるようにするツールが必要な場合があります。 ただし、エージェントは直接やり取りしているため、このコンテキストで個別にメールを送信する必要はないこともあります (ユーザーはすぐに回答を受け取るため)。 エージェントでトランスクリプトや追加情報メールで送信できるようにする場合は、このツールを使用する場合があります。 (当初の計画どおり) 完全性のために、SendCustomerEmail(orderId, message) ツールを作成します。 このラボでは、エージェントでそれが自然に呼び出されないことがありますが、マルチステップの機能を示すためにそれを含めます (たとえば、そのようなユース ケースがある場合、エージェントで "返送用ラベルをメールで送信しました。" と確認できます)。

d. (場合により) **LookupLatestOrder** – ユーザーが "注文はどこにありますか?" と、 どの注文かを特定せずに質問した場合、エージェントは、ユーザーに保留中の注文があるかどうかを確認し、最新の注文について応答できます。 これは GetOrderDetails 内で何らかの規則を使って処理できます (orderId が指定されていない場合は、latest を使うなど)。 わかりやすくするために、クエリで注文番号を指定するか、あいまいな場合は、エージェントがどの注文かを尋ねることを想定しています。 別のツールは実装しませんが、このシナリオに言及します。

**エージェントの知識とコンテキスト:** エージェントはツールを使用して注文情報を取得できますが、ユーザーの ID や検索対象の注文も知っている必要があります。 現在、アプリはシングルユーザーのコンテキストで動作しているため、エージェントは John Doe の注文を常に処理していると想定できます (ユーザー ID を区別する必要はありません)。 ツールがデモ ユーザーのデータを取り込むようにします。 マルチユーザー シナリオでは、ユーザーの ID をエージェントのコンテキストに安全に渡します (シングルユーザーを想定しているため、ここでは行いません)。

**安全性と境界:** エージェントは、これらのツール以外では何も行うべきではありません (ProcessRefund を介してデータベースへの書き込みアクセス権は付与しません。また、これにはチェックする機能があります)。 定義したツールのみに明示的に制限します。 Copilot SDK を使用すると、エージェントで任意のコードを呼び出せないようになり、登録されている関数のみが呼び出されます。 プロンプトでは、注文に関する質問のみに応答する必要があることを強調します。 これであらゆる可能性に備え、エージェントが何かおかしなことを試みないようにします ("削除注文" のような。そのようなツールは提供していませんが)。

計画は次のとおりです。実装する主要なツールは **GetOrderDetails**、**ProcessRefund**、場合によっては、**SendCustomerEmail** も完全性のために含めます。

次に進む前に、次の手順を実行します。IDE でサーバー プロジェクトを開きます。 これらをメソッドとして追加します。 1 つの方法は、エージェント ツール用の専用クラス (SupportAgentService など) を作成することです。 ただし、一部のロジックは OrderService にあるため、そこに統合することもできます。 整理するために、これらのツール メソッドを静的またはインスタンス メソッドとしてホストする新しいクラス SupportAgent (または同様のもの) を作成します。 Copilot SDK を呼び出すには、デリゲート関数またはメソッドが必要なため、それらを Program または Startup に登録します。

次に、実装に進みましょう。

## タスク 2:バックエンド ツールを実装する (GetOrderDetails、ProcessRefund、SendCustomerEmail)

このタスクでは、AI エージェントが呼び出す関数をサーバー上に作成します。 これらは、識別した "ツール" に対応します。 C# で実装し、Web API エンドポイントとは別に呼び出すことができることを確認します (ただし、内部的には同じサービスを使用する場合があります)。

### 手順 2.1:Copilot SDK NuGet パッケージを追加する

最初に、GitHub Copilot SDK をサーバー プロジェクトに追加します。

- CLI を使用して `dotnet add ContosoShop.Server package GitHub.Copilot.SDK --prerelease` を実行します。 (特定のバージョンまたはフィードが必要な場合は、それらの手順に従います)。
- または、Visual Studio の NuGet Manager を使用して、"GitHub.Copilot.SDK" を検索し、最新のプレビューをインストールします。
- インストール後、ソリューションをビルドし、それにより復元とコンパイルが行われることを確認します。 これにより、CopilotClient などの型にアクセスできるようになります。

### 手順 2.2:SupportAgentService クラスを作成する

Server/Services (または必要に応じて新しいフォルダー "AI") に、クラス `SupportAgentService.cs` を追加します。 このクラスは、ツールの実装をカプセル化し、場合によっては Copilot セッションを保持します (ただし、Controller または別のクラスでセッションを管理する場合があります)。 わかりやすくするために、ここではツール メソッドに焦点を当てます。

```csharp
using ContosoShop.Server.Data;  // for ContosoContext
using ContosoShop.Shared.Models; // for Order, OrderStatus
using System.Text;               // for building messages

public class SupportAgentService
{
    private readonly ContosoContext _context;
    private readonly IEmailService _emailService;
    private readonly OrderService _orderService;

    public SupportAgentService(ContosoContext context, IEmailService emailService, OrderService orderService)
    {
        _context = context;
        _emailService = emailService;
        _orderService = orderService;
    }

    // Tool 1: Get order details/status summary
    public string GetOrderDetails(int orderId)
    {
        // Fetch the order (including items)
        var order = _orderService.GetOrderById(orderId);
        if (order == null)
        {
            return $"I'm sorry, I cannot find an order with ID {orderId}.";
        }
        // Build a status message
        string statusMsg = order.Status switch
        {
            OrderStatus.Processing => "is still being processed.",
            OrderStatus.Shipped => $"was shipped on {order.ShipDate:MMM dd, yyyy} and is on its way.",
            OrderStatus.Delivered => $"was delivered on {order.DeliveryDate:MMM dd, yyyy}.",
            OrderStatus.Returned => $"was returned on {order.ReturnDate:MMM dd, yyyy} and refunded.",
            _ => "has an unknown status."
        };
        string result = $"Order {order.Id} {statusMsg}";
        return result;
    }

    // Tool 2: Process a refund (return order)
    public string ProcessRefund(int orderId)
    {
        var order = _orderService.GetOrderById(orderId);
        if (order == null)
        {
            return $"Order {orderId} not found. I cannot process a return.";
        }
        if (order.Status != OrderStatus.Delivered)
        {
            return $"Order {orderId} is not delivered yet (current status: {order.Status}). It cannot be returned at this time.";
        }
        // Process the return
        order.Status = OrderStatus.Returned;
        order.ReturnDate = DateTime.Now;
        _context.SaveChanges();  // persist the status change
        // Send confirmation email (using dev email service)
        string emailBody = $"We have initiated a return for your Order #{order.Id}. You will be refunded {order.TotalAmount:C} to your original payment method within 5-7 business days.";
        _emailService.SendEmail(order.UserEmail, $"Return processed for Order {order.Id}", emailBody);
        // Return a confirmation message for the agent to use
        return $"I've processed the return for order {order.Id}. You will receive a refund of {order.TotalAmount:C} within a week.";
    }

    // Tool 3: Send an email to customer (if the agent wants to send additional info)
    public string SendCustomerEmail(int orderId, string message)
    {
        var order = _orderService.GetOrderById(orderId);
        string email = order?.UserEmail ?? "customer";
        // In our demo, we have a single user whose email we can assume (or we stored on Order.UserEmail).
        _emailService.SendEmail(email, $"Message regarding Order {orderId}", message);
        return $"I've sent an email to the customer with the details.";
    }
}
```

**説明:**

- ContosoContext、IEmailService、OrderService を挿入します。 OrderService.GetOrderById が状態とキーの日付を含む Order を返すものとします。 OrderService が存在しない場合は、直接 _context.Orders.Include(i=>i.Items) のクエリを実行できます。 ただし、既存のロジック (OrderService のビジネス ルールなど) を活用する方が適しています。

- **GetOrderDetails(int orderId):**
  - 該当する注文がない場合は、謝罪のメッセージを返します (エージェントがこの内容をユーザーに伝えます)。
  - 見つかった場合は、わかりやすい状態メッセージを作成します。 状態には switch 式を使用します。 Shipped/Delivered (発送済み/配達済み) の場合、日付を含めます (Order に ShipDate、DeliveryDate などが含まれていると仮定します。データに含まれている可能性があります。存在しない場合は、メッセージに "配達済み" とだけ表示されるように調整します)。
  - "注文 1002 は 2026 年 3 月 1 日に発送され、配達中です。" のような文を作成します。 ユーザー フレンドリな言い回しにします。
  - この文字列を返します。 (Copilot SDK が、エージェントにこの返品をキャプチャさせ、その全体的な回答に組み込むようにします)。

- **ProcessRefund(int orderId):**
  - 注文が存在し、状態が Delivered であることを確認します。
  - 配達されていない場合は、"まだ返品できません" のようなメッセージを返します。 (エージェントはユーザーにこれを通知します)。
  - 配達された場合は、返品を実行します。
    - 状態を Returned (返品済み) に設定し、ReturnDate を設定します。
    - DB に保存します。
    - EmailService を使用して、返金確認メールを送信します。 order.UserEmail を使用します (Order または関連する User オブジェクトにメールがあることを前提としています。シード データでは、それを入力するか、デモ用の定数メールに既定で設定できます)。
    - メールの内容は定義されています。 (実際のシナリオでは、これには返品手順などが含まれる場合があります。ここでは、返金額とタイムラインを確認します)。
  - エージェントが読み上げる確認メッセージ "返品が処理されました。返金額は X です。" を返します。

- **SendCustomerEmail(int orderId, string message):**
  - 注文を見つけます (おもにメールを受け取るか、存在を確認するためです)。
  - EmailService を使用して、指定されたメッセージをユーザーにメールとして送信します (注文を参照する件名を含めます)。
  - エージェントに対して確認文字列を返します。 アイデアは次のとおりです。エージェントが長い説明または返品ラベルのリンクをメールで送信する必要があると判断した場合は、ユーザーにそれを通知した後にこのツールを呼び出す場合があります。
  - 実際には、ユーザーが促さなければ、エージェントが単独でこれを呼び出さないでしょう。 ただし、このツールを用意して、マルチツールの使用方法としくみを示します (エージェントは短く回答した後で、このツールを使用してから、"詳細をメールで送信しました" と説明します)。

### 手順 2.3:DI に SupportAgentService を登録する

`Program.cs` (または、以前のスタイルを使用している場合は Startup.cs) を開きます。 次の項目を追加します。

```csharp
builder.Services.AddScoped<SupportAgentService>();
```

これにより、コントローラー (または、これを使用する場所) は、必要な依存関係が挿入されたインスタンスを取得できます。

### 手順 2.4:これらの関数をサポートするように OrderService または Data を更新する

OrderService.GetOrderById(int) がある場合は、関連するデータが含まれていて、ユーザーのメールを取得できるようにすることを確認します。 場合によっては、UserEmail を Order に追加する必要があります (わかりやすくするために、ユーザーのメールが注文に保存されている場合があります)。 そうでない場合は、定数を格納するか、User テーブルから取得できます。 このシナリオはユーザーが 1 人であるため、ハードコーディングした可能性があります。 現時点で、まだ存在しない場合は、UserEmail プロパティを Order モデルに追加して、シーディング時に入力します (例: "john@contoso.com")。 これにより、EmailService の呼び出しが簡単になります。

(時間がある場合は、OnModelCreating で Order をシーディングするときに UserEmail = "john@example.com" を設定します。 または、スキップして EmailService で、常に config から固定アドレスに送信します)。

また、Order に ShipDate、DeliveryDate、ReturnDate (DateTime?) などのフィールドがあることを確認します。 ない場合は、次をシミュレートします。

- Delivered の注文の場合は、DeliveryDate を DB またはコンピューティングに格納します (OrderDate + 日数など)。
- Return の場合は、上記のようにコードで ReturnDate を設定します。

これらのフィールドは機能に厳密に必要ではありませんが、応答が豊富になります。 モデルにまだ含まれていない場合は、それらを追加して DB を更新します (これは開発データベースであるため、手動で追加しても問題ありません)。 ただし、スキップする場合は、よりシンプルなメッセージ (日付を含まない) で応答できます。

それらがあるか、少なくとも DeliveryDate はあると見なします。

### ステップ 2.5: (省略可能) エージェント外でこれらのメソッドをテストする

多くの場合、ツール ロジックを分離してテストし、想定どおりに動作するかどうかを確認することをお勧めします。

- 既知の注文 ID で SupportAgentService.GetOrderDetails を呼び出すクイック デバッグ スニペットまたは単体テストを記述し、文字列を確認できます。
- または、手動で実行するためにテスト コントローラー エンドポイントを介して一時的に公開します。 (簡潔にするために、これをスキップできますが、開発シナリオでは役に立ちます)。

この時点で、コア アクションが実装されました。 次に、これらのアクションを使用するために Copilot SDK を組み込みます。

## タスク 3:GitHub Copilot SDK エージェントを登録し構成する

次に、ツール関数を AI エージェント ランタイムに接続します。 GitHub Copilot SDK を使用してエージェントを作成し、プロンプトを与え、C# メソッドの呼び出しを許可します。

### 手順 3.1:CopilotClient とセッションを初期化する

アプリケーションで Copilot エージェントを設定するためのロジックを追加します。 これを設計するには、いくつかの方法があります。

- 起動時に読み込まれるアプリケーション全体にシングルトン エージェントを作成し、すべてのユーザー クエリを処理させることができます (一度に 1 つ)。
- または、受信要求ごとに新しいエージェント セッションを作成します (ステートレス アプローチ)。

サポート クエリは有効期間が短く、長期間にわたるマルチターンではない可能性があるため、API 呼び出しごとに新しいセッションを作成できます (これはよりシンプルであり、メモリの蓄積を回避できます)。 Copilot SDK は、セッションの生成が比較的軽量であり、保持することを選択しない限り、毎回新しいコンテキストが確保されます。

わかりやすくするために後者を実装します。API エンドポイント (タスク 4 で作成します) で、Copilot セッションがインスタンス化され、それを介してクエリが実行されて破棄されます。

ただし、エージェントが呼び出せるように、ツールを Copilot SDK に登録する必要があります。 つまり、GetOrderDetails などについて Copilot に伝え、それらのデリゲートを提供する必要があります。

この登録は、(すべての呼び出しではなく、オーバーヘッドを節約するために) 1 回だけ行うのが理想的です。 サービスの開始時に、これを行うことができます。CopilotClient を作成し、ツールを定義し、準備を整えておきます。

### 手順 3.2:SupportAgentController を作成する (AI 用 API)

Server/Controllers に新しいコントローラー `SupportAgentController.cs` を追加します。

```csharp
using Microsoft.AspNetCore.Mvc;
using GitHub.Copilot.SDK;  // assume this is the correct namespace for Copilot classes

[ApiController]
[Route("api/agent")]
public class SupportAgentController : ControllerBase
{
    private readonly SupportAgentService _agentService;
    private readonly CopilotClient _copilotClient;

    public SupportAgentController(SupportAgentService agentService, CopilotClient copilotClient)
    {
        _agentService = agentService;
        _copilotClient = copilotClient;
    }

    [HttpPost("ask")]
    public async Task<IActionResult> AskQuestion([FromBody] SupportQuery query)
    {
        if (query == null || string.IsNullOrWhiteSpace(query.Question))
            return BadRequest("Query cannot be empty.");

        // Create a session for this query
        var session = await _copilotClient.CreateSessionAsync(new SessionOptions { Model = "gpt-4" });
        
        // Register tools (functions)
        session.RegisterFunction(
            "get_order_details",
            "Get status of an order by order ID. Parameter: order_id (int).",
            (int order_id) => _agentService.GetOrderDetails(order_id)
        );
        session.RegisterFunction(
            "process_refund",
            "Initiate a return/refund for an order by ID. Parameter: order_id (int).",
            (int order_id) => _agentService.ProcessRefund(order_id)
        );
        session.RegisterFunction(
            "send_email",
            "Send an email to customer with a message. Parameters: order_id (int), message (string).",
            (int order_id, string message) => _agentService.SendCustomerEmail(order_id, message)
        );

        // Craft system prompt
        string systemPrompt = 
            "You are ContosoShop's virtual assistant. " +
            "You can help the customer with order inquiries. " +
            "You have tools to lookup order details (use get_order_details) and process returns (use process_refund). " +
            "Always use the tools for factual information or to perform actions. " +
            "Respond in a friendly tone. " +
            "If question is unrelated to orders or you cannot help, politely refer them to human support.";
        
        // Initialize conversation
        await session.SendMessageAsync(new Message(Role.System, systemPrompt));
        await session.SendMessageAsync(new Message(Role.User, query.Question));

        // Get response (the SDK may handle tool use internally when generating this)
        Message assistantReply = await session.GetResponseAsync();
        string answer = assistantReply.Content;
        
        // Optionally, you might also accumulate the conversation or ensure tools invoked
        // For now, assume the answer is final.

        // End the session (optional, as disposing session or client can be done at controller dispose)
        await session.DisposeAsync();

        // Return the assistant's answer
        return Ok(new { answer });
    }
}

public class SupportQuery
{
    public string Question { get; set; }
}
```

**重要なポイントと前提条件:**

- CopilotClient を挿入しました。 シングルトンの CopilotClient も DI を介して利用できるようにする必要があります。 Program.cs で個別に作成します。 起動後にシングルトンとして登録することもできます。

- AskQuestion アクションで、次の操作を実行します。
  - 本文で SupportQuery を受け取ります。 Blazor クライアントから JSON {"question": "...ユーザーの質問..."} が送信されます。
  - GPT-4 とのセッションを作成します。 (GPT-4 が使用できない場合は、"model: gpt-3.5-turbo" を使用できます。 ただし、より良い品質のため、GPT-4 を想定しています。 実際には、お使いの Copilot サブスクリプションで GPT-4 要求が許可されていることを確認してください)。
  - RegisterFunction は仮定の API です。実際の SDK では、少し異なるメソッド シグネチャが使用される場合があります。 これが、C# デリゲート (ラムダ) を受け取ることができると仮定します。 _agentService メソッドを正確にマップします。 send_email については、2 つのパラメーター (int、string) を示していることに注目してください。
  - システム プロンプトは、最初のメッセージ (Role.System) として指定されます。
  - 次に、ユーザーの質問を User メッセージとして送信します。
  - 次に、GetResponseAsync() で会話とツールが処理されます。Copilot SDK のエージェントは、ユーザーの質問を分析し、場合によっては関数の 1 つに呼び出しを出力します。 SDK は、C# 関数をインターセプトして呼び出し、結果を取得し、モデルにフィードし直します。これにより、より多くの関数呼び出しまたは最終的な回答が生成されます。 このループは、Copilot SDK のセッションによって内部的に処理されます (OpenAI 関数呼び出しのしくみとよく似ていますが、マルチステップで処理されます)。
  - 最後のアシスタント応答は assistantReply でキャプチャされます。
  - セッションを破棄してリソースを解放します。
  - 回答を JSON として返します (Blazor クライアントでそれが取得され、表示されます)。

- エラーになるケース:質問が空の場合は、400 になります。 エージェントの呼び出し (例外など) で問題が発生した場合は、堅牢なアプリでキャッチして処理します ("Agent error" というエラー メッセージを含む 500 が返されることがあります)。 簡潔にするために、上記で紹介していませんが、try-catch でコア ロジックをラップし、必要に応じて BadRequest または StatusCode(500) をメッセージと共に返すことができます。

### 手順 3.3:Program.cs で CopilotClient シングルトンを登録する

サーバー プロジェクトで `Program.cs` を開きます。 サービスを構築した後は、次の操作を行います。

```csharp
// Create and start CopilotClient as a singleton
var copilotClient = new CopilotClient();
copilotClient.StartAsync().GetAwaiter().GetResult();  // synchronous start during startup
builder.Services.AddSingleton(copilotClient);
```

`var app = builder.Build();` の前に配置します。 これにより、コントローラーが構築されると、同じ実行中の CopilotClient が取得されます。

また、`builder.Services.AddControllers();` が呼び出されていることを確認します (呼び出されていない場合は、新しいコントローラーが認識されるように追加しますが、テンプレートから既に存在している可能性があります)。

ここで、Copilot SDK で認証などの構成が必要になる場合があることを考慮します。

- ご自分の GitHub アカウントの資格情報を使用するか、ログイン済みの Copilot CLI エージェントを使う可能性があります。 プレビューでは、環境から GitHub 認証が取得される場合があります (たとえば、github-copilot-cli auth を実行した場合)。 環境変数の設定や copilotClient.Login(token) の呼び出しなどの、追加の手順が必要な場合は、ここで行います。 Copilot SDK のドキュメントを確認してください。これはマネージド環境であるため、Copilot がお使いの GitHub アカウントに関連付けられているため接続できると想定しています (最初の実行時にログインが表示されるか、既存の VS Code ログインが使用されることがあります)。
- このラボ シナリオでは、Copilot アクセス権があり、SDK で認証が処理されることを前提としています (一部のプレビューではローカル CLI デーモンの evalai を実行する必要がありましたが、GitHub のプレビューでは必要ではない可能性があります。 必要な場合は、実行されていることを確認する必要があります)。

Microsoft Learn ラボの場合、Copilot SDK が動作するか、それをエミュレートする事前プロビジョニング環境が存在する可能性があります。 github-copilot-cli をバックグラウンドで実行する手順が記載されている場合があります。 スムーズな操作を想定しています。

### 手順 3.4:ツールの登録構文を確認する

記述されたコードは、実際の SDK に対してテストされません (この環境での仮定のコードのため)。 SDK で少し異なる API が使用されている場合は、それに応じて次のように調整します。

- たとえば、RegisterFunction がなく、代わりに Session によって session.RegisterTool(new FunctionDefinition("name", ...), delegate) のようなものが公開される可能性があります。
- または、copilotClient.RegisterFunctionGlobally を使用し、セッションでそれらが認識される場合があります。
- ここでは正確なライブラリの詳細がないため、これが実際のラボの場合は、ドキュメントを参照するでしょう。 ロジックはそのままなので、テキスト コマンドを C# メソッドに結び付けます。

### 手順 3.5:(省略可能) ツール内のログ

ツールが呼び出されたタイミングと返された内容をログに記録すると、デバッグに役立つ場合があります。

- たとえば、GetOrderDetails では `_logger.LogInformation("Agent invoked GetOrderDetails for order {orderId}", orderId);` を行います。
- これには、そのサービスに ILogger を挿入する必要があります。 時間がある場合は、それを追加できます。これは、テレメトリで関数の使用状況を確認するのに役立ちます。
- また、監査用に、ユーザーの質問とエージェントからの最終的な回答をコントローラーにログします: `logger.LogInformation("Q: {q} -> A: {a}", query.Question, answer);`。

このログは、エージェントのパフォーマンスを監視し、誤った情報がある場合は特定するために、後で Azure App Insights に接続できます。

これで、エージェントを設定できました。 次の手順では、フロントエンドを接続して、この新しい API エンドポイントを使用します。

## タスク 4:エージェント クエリ UI の Blazor フロントエンドを更新する

ユーザーが質問を入力し、エージェントからの回答を確認できるように、Support.razor ページを変更します。

### 手順 4.1:エージェント API のサービス プロキシを作成する

クライアント プロジェクトで、新しいサービス クラス (たとえば、`SupportAgentServiceClient.cs`) を作成します。

```csharp
public class SupportAgentServiceClient
{
    private readonly HttpClient _http;
    public SupportAgentServiceClient(HttpClient http) { _http = http; }

    public async Task<string> AskAgentAsync(string question)
    {
        var payload = new { question = question };
        var response = await _http.PostAsJsonAsync("api/agent/ask", payload);
        if (!response.IsSuccessStatusCode)
        {
            // You could read error and throw or handle
            string error = await response.Content.ReadAsStringAsync();
            throw new Exception($"Agent API error: {response.StatusCode} - {error}");
        }
        // The response is expected to be { answer: "text" }
        var result = await response.Content.ReadFromJsonAsync<AgentAnswer>();
        return result?.Answer ?? string.Empty;
    }
}

public class AgentAnswer
{
    public string Answer { get; set; }
}
```

このサービスは HttpClient を使用して POST /api/agent/ask を呼び出します。 これを、プロパティの回答を含む JSON を返すように定義しました。 それを小さな AgentAnswer クラスにマップします。

これを `Program.cs` (クライアント) に登録します。

```csharp
builder.Services.AddScoped<SupportAgentServiceClient>();
```

(これは OrderService を登録した方法に似ています)。

### 手順 4.2:Support.razor UI の更新

Support.razor を開きます。 シンプルなチャットのようなインターフェイスを作成します。

- 会話を表示する複数行のテキスト ボックスまたはリスト (最初に、質問と回答を 1 つだけ表示する場合があります)。
- 新しい質問用の入力ボックスと送信ボタン。

現在、このシナリオにはマルチターンは含まれていないため (エージェントは各質問を個別に処理するので)、チャット履歴全体を保持しない可能性があります。 ただし、チャット UI を表示するためだけに保持することはできます。 UX のために、コンポーネント状態に Q&A ペアの一覧を保持しましょう。

コンポーネント コードを変更します。

```razor
@page "/support"
@inject SupportAgentServiceClient SupportAgentClient

<h3>Contact Support</h3>

<div class="mb-3">
    <p>You can ask our virtual support assistant about your orders.</p>
</div>

<div class="card p-3" style="min-height: 200px;">
    @if(conversations.Count == 0)
    {
        <p class="text-muted">Ask a question about your order to get started.</p>
    }
    @foreach(var entry in conversations)
    {
        <div class="mb-2">
            <strong>You:</strong> @entry.Question
            <br />
            <strong>Assistant:</strong> @entry.Answer
        </div>
    }
    @if(isLoading)
    {
        <div class="mb-2"><em>Assistant is typing...</em></div>
    }
</div>

<EditForm Model="@queryModel" OnValidSubmit="@SubmitQuestion">
    <div class="mb-2">
        <InputText @bind-Value="queryModel.Question" 
                   class="form-control" placeholder="Type your question..." />
    </div>
    <button type="submit" class="btn btn-primary" disabled="@isLoading">Send</button>
    @if(!string.IsNullOrEmpty(errorMessage))
    {
        <div class="text-danger mt-2">@errorMessage</div>
    }
</EditForm>

@code {
    class ConversationEntry { public string Question { get; set; } public string Answer { get; set; } }

    private SupportQueryModel queryModel = new SupportQueryModel();
    private List<ConversationEntry> conversations = new();
    private bool isLoading = false;
    private string errorMessage;

    async Task SubmitQuestion()
    {
        errorMessage = string.Empty;
        if(string.IsNullOrWhiteSpace(queryModel.Question)) return;
        var userQuestion = queryModel.Question;
        conversations.Add(new ConversationEntry { Question = userQuestion, Answer = "" });
        var entryIndex = conversations.Count - 1; // index of this question in list
        try
        {
            isLoading = true;
            // Call the agent service
            string answer = await SupportAgentClient.AskAgentAsync(userQuestion);
            conversations[entryIndex].Answer = answer;
        }
        catch(Exception ex)
        {
            conversations[entryIndex].Answer = ""; // ensure it's empty since we got no answer
            errorMessage = "Sorry, something went wrong. Please try again later.";
            Console.Error.WriteLine($"Agent error: {ex.Message}");
        }
        finally
        {
            isLoading = false;
        }
        queryModel.Question = string.Empty;
        StateHasChanged();
    }

    class SupportQueryModel { public string Question { get; set; } = ""; }
}
```

行った内容を説明しましょう。

- 静的サポート情報を対話型 UI に置き換えました。
- 会話リストは、各 Q&A を追跡します。 最初は空です。
- ユーザーが送信するときに、質問を受け取り、その質問と空の回答を含む新しい ConversationEntry を追加します ("あなた: 質問" と、回答のプレースホルダーが表示されるようにします)。
- isLoading=true とマークすると、"アシスタントが入力しています..." フィードバックが表示されます。
- サーバーを呼び出す SupportAgentClient.AskAgentAsync(userQuestion) を呼び出します。
- 成功した場合は、会話エントリで回答を設定します。
- エラーの場合は、一般的なエラー メッセージが表示されます (例外の詳細はユーザーに直接表示されません)。 開発者用のコンソールにログインします。
- 入力ボックスをクリアします (queryModel.Question をリセットします)。
- 会話の UI: 各エントリをループ処理し、"あなた: ..." および "アシスタント: ..." と表示します。
  - 回答がまだ設定されていない場合 (空の文字列と isLoading が true)、現在はループの外側で "アシスタントが入力しています..." と表示されます。 これを行うには、isLoading を個別に確認します。 (もう 1 つの方法: エントリ自体にプレースホルダーを配置します)。
- また、errorMessage が存在する場合は表示します。

これにより、シンプルなチャット ログが生成されます。

**スタイル:** パディングと最小限の高さを持つカードを使用して、チャット ウィンドウのように表示されるようにしました。 これは改善できます (メッセージが多い場合はスクロールできますが、少ない場合は問題ありません)。 シンプルな状態を維持します。

### 手順 4.3:UI をテストする (必要に応じてスタブを使用する)

この時点で、UI からエージェントへの完全な接続が確立されています。 すべてが正しく接続ワイヤードされている場合は、アプリを実行できます。

- [サポート] ページに移動します。
- 次の質問をします:"注文 1001 はどこにありますか?"
- UI で API が呼び出され、エージェントで GetOrderDetails(1001) が呼び出されて、状態文字列を取得し、それを使って応答します。 その後、UI により "アシスタント:" の下にそれが表示されます。

ただし、Copilot SDK または接続で何かが正しく構成されていない場合、エラーが発生するおそれがあります (この UI では "問題が発生しました" と表示されます)。

Copilot SDK が実際に機能しない可能性があるシナリオ (オフラインの場合) のテストでは、SupportAgentClient を一時的にスタブして、既知の質問に対して事前に用意されている応答を返すこともできます。 しかし、環境が AI 接続用に適切に設定されていると仮定して、続行しましょう。

### 手順 4.4:マルチターンの考慮事項

現在の実装では、各クエリを個別に処理しています。

- システム プロンプトは毎回再送信され、エージェントには現在の質問以外の以前の会話のメモリがありません。 ユーザー参照のために Q&A を UI に追加しますが、各呼び出しは独立しています。
- 現時点で、"注文 1002 はどこにありますか?" などのクエリは 1 ターンで完全に回答できるので、許容できます。
- ユーザーが "これは返品できますか?" などのフォローアップを要求した場合、次の呼び出しに会話履歴を提供しない限り、エージェントで "これ" のコンテキストが認識されません。 これは高度です (セッションを維持するか、前の Q&A をプロンプトにフィードする必要があります)。
- このラボでは、独立した質問、または、エージェント自体が 1 つのセッション内で説明するための質問ができると仮定しています。 実際、エージェントで説明が必要な場合、どのように機能するでしょうか? 設計から考えると、エージェントは "どの注文のことを指していますか?" のような内容を アシスタントの回答として出力できます。 その後、ユーザーはもう一度指定することを依頼する必要があります。 1 回の回答の後にセッションを閉じるので、完全なチャット ループではありません。
- 実際のマルチターンを許可するには、sessionId またはセッション オブジェクトをユーザーごとに静的に、またはキャッシュに格納すると、複数の呼び出しにわたってセッションを維持できます。 これは今回の範囲外の内容ですが、考慮する必要があります。
- 運用サポート ボットでは、フォローアップのために会話コンテキストを維持することが重要です。 Copilot SDK では、1 つのセッションで複数の対話をストリーミングできる場合があります。 しかし、わかりやすくするために、ここでは 1 つの質問に 1 つの回答を示します。

### 手順 4.5:UI/UX の調整 (省略可能)

- 読み込み中は、入力を無効にして別のクエリが同時に送信されないようにすることができます (disabled=@isLoading を使用して送信ボタンを無効にしました)。
- 非同期ストリーミング (タイピングの効果を表示するためにトークンごとに更新するなど) が必要な場合、Copilot SDK ではストリーミング モードが許可される場合があります。 そのためには、イベントをフックするか、部分的な出力を読み取る必要があります。 これは高度です。このラボではストリーミングを実装しません。 代わりに、完全な回答の準備ができるまでは、シンプルに "アシスタントが入力しています..." と表示されます。
- 会話リストは無期限に増加しますが、実際のアプリでは、制限するか "クリア チャット" ボタンを提供できます。 現時点では必要ありません。

この時点で、次のすべてが実装されました。バックエンド ツール、AI 統合、フロントエンド。 次に、統合ソリューションを実際に実行してテストします。

## タスク 5:AI エージェントの統合をテストする

アプリケーション (サーバーとクライアント) を実行します。 次のテスト シナリオを使用して検証します。

1. **注文状態の確認クエリ:**[サポートに問い合わせる] に移動し、次の質問をします。"注文 1002 はどこにありますか?"
   - 想定される動作:アシスタントは、"注文 1002 は [date] に発送され、配達中です。" のような応答を返す必要があります (注文 1002 に対して GetOrderDetails から返されるものなら構いません)。 データベースに基づく正しい答えのはずです。 これが #1002 の [注文] ページに表示される内容と一致することを確認します。 一致していれば成功です。 エージェントがあいまいな答えや間違った答えを出した場合は、問題があるかもしれません (エージェントがツールを呼び出さずに推測した場合など)。 プロンプトと関数の使用は、関数が確実に呼び出されるのが理想的です。
   - 回答がなく、エラー メッセージのみが表示される場合は、サーバー ログを参照してください。 CopilotClient を接続できなかった可能性があります。GitHub CLI にログインしていて、インターネットが使用可能であることを確認してください。 これは環境に固有です。 このラボでは、動作するように構成されているとします。

2. **返品依頼クエリ:** 次の質問をします:"注文 1001 を返品したいです。"
   - エージェントで、注文 1001 の状態が確認される必要があります。 #1001 がシード データで配達済みの場合は、ProcessRefund の呼び出しに進む必要があります。 これで DB が更新され、メールがログされます。 エージェントの回答は次のようになります。"注文 1001 の返品を処理しました。 1 週間以内に X.XX ドルが返金される予定です。" ツールが返すとおりです。
   - [注文] ページで、更新 (または移動して戻る) を行うと、注文 1001 に "返品済み" の状態が表示されることを確認します。 これで、process_refund ツールが実行され、DB が更新されたことが確認できます。
   - また、サーバー コンソールで EmailServiceDev からのメール ログを確認します (返金メールの内容が出力されているはずです)。 これは、その部分が機能したことを示しています。
   - セッションが閉じられた後で、ユーザーが、"注文 1002 も返品できますか?" などのフォローアップを要求した場合、 ユーザーは新しいメッセージで質問する必要があります。 現時点では問題ありません。

3. **不明な注文:** 次の質問をします:"注文 9999 はどこにありますか?" (ID が DB に存在しない場合を想定しています)。 GetOrderDetails 経由のエージェントは "注文が見つかりません" と返します。 エージェントの応答では、丁寧に伝える必要があります。 "申し訳ありませんが、ID 9999 の注文が見つかりません。" という返された文字列が直接使用される場合があります。 – これで既に丁寧な応答です。 可。
   - エージェントが、ハルシネーションを起こし、データがない場合に "配達されました" のように回答していないことを確認します。 ツールが適切に使用されている限りは、これは起きないはずです。 このようなことが起きたときは、ツールが使用されていない可能性があります。つまり、システム プロンプトをより厳格にする必要がある場合があります。 常にツールを使用するようにプロンプトを微調整できます。

4. **トピック外の質問:** 次の質問をします:"天気はどうですか?" – エージェントにはこの質問に対するツールを持つべきではありません。関係のないクエリに対しては、人間のサポートに任せるようにシステム プロンプトで伝えました。 考えられる応答:"申し訳ございません。ご注文に関する質問についてのみお答えしています。" のような内容が表示される場合があります。 無関係な内容を答えようとしたり、作り話をしたりする場合は、さらにプロンプトを強化する必要があります。 しかし、指示を受け取った場合は、適切に拒否するはずです。 これは限界を知る適切なテストです。
   - 同様に、"ジョークを言ってみて" のような質問は、受け流すか丁寧に断る必要があります。

5. **マルチステップのシナリオ:** 両方のツールが必要な質問をしてみます。 例:"注文 1001 はどこにありますか? また、それを返品できますか?" – これは注意が必要です。ユーザーは、状態チェックとアクションを組み合わせました。 エージェントの応答は次のようになる可能性があります。
   - get_order_details を使用します (配達済みであることがわかります)。次に、2 番目の要求を満たすために自動的に process_refund を呼び出し、おそらく次のように組み合わせて応答します。"注文 1001 は 1 月 5 日に配達済みです。 返品を処理しました。ご返金は...."。
   - これにより、エージェントが 2 つの関数を順番に使用する能力がテストされます。 Copilot SDK セッションは、マルチステップの計画を処理できます。 両方が実際に処理されたかどうかを確認してみてください。 (後でデータベースを見てください。両方が処理されていれば、注文 1001 が返品済みとマークされているはずです)。
   - 最初の部分にしか答えていない場合や混乱した場合は、マルチステップを促すようにプロンプトを調整できます。 しかし、ツールを提供したので、おそらく処理できます。
   - 会話のコンテキストに基づいて動作できることが、このようなエージェントの主な利点です。

すべてのテストが完了しましたか? 素晴らしい。

**トラブルシューティングのヒント:**

- エージェントの応答がオフに見える場合、または必要なときにツールを使用していない場合は、システム プロンプトをより明示的に調整することを検討してください。 たとえば、次を追加します。"情報を取得するには関数を使用する必要があります。 回答を作り出さないでください。 関数から取得したデータでのみ応答してください。" Copilot SDK のエージェントは、関数の登録方法が原因で、それを使用するように既に偏っている場合があります (説明文が役に立ちます)。
- コントローラーで例外などのエラーが発生した場合は、出力またはログを参照してください。 一般的な問題を次に示します。
  - CopilotClient が開始されていないか、null: Program.cs でシングルトンが正しく追加されていることを確認してください。
  - 要求がハングするか、非常に遅い場合:Copilot の呼び出しに時間がかかっていることがあります (AI モデルに送信しています)。 GPT-4 は数秒かかることがあります。 これをカバーするため、UI に "入力中です..." と表示されます。 15 秒以上かかる場合は、問題が発生しているかもしれません。
  - エージェント API から 401 を取得した場合: Copilot SDK が認証されていない可能性があります。 ログインするには、copilot-cli auth を実行する必要があることがあります。 ラボ環境では、処理されている場合があります。

## タスク 6:(省略可能) ログと監視機能を追加する

(このタスクは省略可能ですが、実際の使用に向けた準備としてお勧めします)。

統合が機能したので、ログを追加して AI エージェントのアクティビティを監視することを検討してください。

**サーバー側のログ:** 各ツールが実行されたタイミングとそれが返した内容のログについては、既に触れました。 こうすれば、エージェントから正しくない回答が提供された場合、ツールが正しい情報を提供したのか、エージェントが無視したのかを確認できます。 追加の例:

```csharp
_logger.LogInformation("GetOrderDetails called for orderId {orderId}, result: {result}", orderId, result);
```

Q&A もログします。

```csharp
_logger.LogInformation("User question: {question}, Agent answer: {answer}", query.Question, answer);
```

後で Azure 上の Application Insights と統合する場合、これらのログをクエリ可能なテレメトリ イベントに変換できます (たとえば、応答が AI によって処理される頻度と、ユーザーが状態の質問をする頻度をカウントします)。

**フロントエンド ログ:** 必要に応じて、検知されたエラー (errorMessage など) をテレメトリに送信できます。 ただし、フロントエンドは、主にサーバー ログに依存しています。

**AI の使用状況に関するテレメトリ:** 別の方法:GitHub には、Copilot SDK の使用に関するレート制限または監査が含まれる場合があります。 呼び出し数を常に確認します。 これを数千人のユーザーに増やした場合は、繰り返し尋ねられる同じ質問の結果をキャッシュすることを検討してください (各質問は個人的であるため、ここでは必要ないかもしれません)。

**ユーザー エクスペリエンスのログ:** 各エージェント クエリにかかる時間 (AskAgentAsync の周辺で Stopwatch) をログして、平均待機時間を把握します (将来の最適化のため、または必要に応じてより高速なモデルを選択するため)。

**クラウドの監視:** Azure にデプロイする場合は、Application Insights を有効にすると、上記のすべてのログとメトリックが一元的にキャプチャされます。 これはラボの範囲を少し超えていますが、組み込みの ILogger の基礎は既に構築されています。

## タスク 7:クラウド デプロイに関する考慮事項 (ディスカッション)

(ここではコーディングはしません。知識をまとめるだけです)。

これで、AI 強化サポート ポータルをローカルで使用できるようになったので、これを Azure にデプロイし、AI エージェントが引き続き機能することを確認する方法について説明します。

**Web アプリと API のデプロイ:** ASP.NET Core Server (Blazor を含む) を Azure App Service に公開できます。 これは統合プロジェクト (ホストされたモデル) であるため、1 つの App Service で API と Blazor WASM の両方にサービスを提供できます。 別の方法として、これらを分けることもできます。App Service 上で API をホストし、Azure Static Web Apps または CDN 上で Blazor クライアントをホストします。 わかりやすくするために、サーバー プロジェクトを実行する 1 つの App Service インスタンス (クライアントにも提供します) が簡単です。

- Azure に、dotnet publish を使用してデプロイするか、Visual Studio の発行プロファイルを使用します。
- 接続文字列が更新されていることを確認します。Azure の運用環境には SQLite を使用したくない場合があるためです。 Azure SQL に切り替え、その Azure SQL インスタンスで EF Core 移行を実行します (または、デモには EnsureCreated() を一時的に使用します)。
- あるいは、Azure での簡単なデモに SQLite を使用する場合、Azure App Service は .db ファイルを処理できますが、更新プログラムのデプロイは慎重に行ってください。 小規模または内部のツールでもかまいません。

**Azure Copilot SDK:** これは新しい領域です。 GitHub Copilot SDK は、Azure App Service では、特にプレビュー段階では、まだ公式にサポートされていない可能性があります。 潜在的な問題:

- 認証:サーバーは、Copilot を使用するために GitHub に対して認証する必要があります。 ローカルでは、資格情報が使用されました。 Azure では、対話型ログインができません。 おそらく、GitHub では、ヘッドレス シナリオの場合、Copilot の PAT (個人用アクセス トークン) が許可されますが、これは利用できません (Copilot では OAuth が使用されます)。
- Copilot SDK では、ログインした状態の GitHub ID が必要になる場合があります。 1 つのアイデアとして、資格情報のローカル キャッシュに依存している場合は、何らかの方法でそれを App Service に提供する必要があります (理想的ではありません)。
- **代替プラン:Azure OpenAI Service を使用する:** Copilot SDK の代わりに、運用環境では、Azure OpenAI の API を使用して独自のプロンプト オーケストレーションに置き換えることができます。 Azure OpenAI .NET SDK などのライブラリを使用して、会話 (システム + ユーザー) を送信し、回答を取得できます。 ただし、関数呼び出しの場合は、ループを実装する必要があります。応答を取得した後、アクションが必要かどうかを確認します (プロンプトを設計して [[CALL:get_order_details:1001]] などの形式を埋め込むか、それまでに Azure で使用可能になっていれば OpenAI の関数呼び出しを使用できます)。 これはカスタム ソリューションですが、実行できます。 このパターンは、Copilot の動作を模倣しますが、より制御が可能です。
- このラボの範囲では、デモ向けに Copilot SDK がローカルで実行されていることを前提としています。 実際の製品の場合は、Azure OpenAI を使用する傾向があります (データがテナント内にとどまるようにするため)。

**拡張性:** Azure では、App Service をスケールアウトして複数の要求を処理できます。 Copilot モデル呼び出しは外部であり、同時クエリが多すぎるとボトルネックになるおそれがあります (大量のクエリが発生した場合、GitHub で Copilot の使用量がレート制限される可能性があります)。 パフォーマンスに注意します。 各質問はユーザー固有であるため、特定の回答のキャッシュはあまり適切ではありません。 ただし、スケールアウトとは、複数のインスタンスがそれぞれ Copilot SDK を実行する可能性を意味します。SDK がユーザーにロックされたローカル リソースを使用しようとする場合は注意してください。 選択したステートレス アプローチが、役立ちます (各呼び出しが依存しない)。

**コスト:** 運用アプリで GitHub Copilot を使用すると、使用条件に違反するおそれがあります (Copilot は、エンドユーザー向けの実行時の質問応答ではなく、コード提案を目的としています)。 また、使用量が多いケースの API 呼び出しごとの価格モデルがない可能性もあります。 これを実際に実装する場合は、呼び出しごとの支払いを含む Azure OpenAI の方が適しています。 何千もの要求にスケーリングする場合は、AI API 呼び出しのコストを常に考慮してください。 たとえば、Azure OpenAI の GPT-4 では、1,000 トークンごとにコストが発生します。 いくつかの文を生成する使用状況では、1 回の呼び出しにつき数百トークンになり、1 トークンは数セントと思われます。管理は可能ですが、監視する必要があります。

**エージェント スキルの拡張:** 将来的には、エージェントにより多くのツールを提供できます (たとえば、業務で許可されていれば、出荷 API を使用して注文を追跡したり、ユーザーに割引クーポンを提供したりするツールです)。 アーキテクチャでは準備ができています。関数を実装して登録するだけです。 新しいツールを使用するタイミングをエージェントに伝えるように、システム プロンプトを更新してください。

**ユーザー フィードバックとハンドオフ:** 実際のデプロイでは、エージェントをトレーニングまたは改善するためにユーザー フィードバックを収集します。

- ユーザーに "この回答は役に立ちましたか? はい/いいえ" と質問して、成功の度合いを確認するとよいかもしれません。
- エージェントが失敗したか、またはユーザーが不満を持っている場合 ("これは問題を解決していません" という意見の場合) は、人間に引き継ぐ必要があります。 機能を統合できます。ユーザーが 「人間」と入力した場合、またはエージェントが知らない内容の場合は、チケットまたは人間のエージェントとのライブ チャットを作成します。 エージェントまたはコードで、特定のフォールバック条件が検出される可能性があります。
- (ユーザーの同意を得て個人情報に注意しながら) 会話をログすると、エージェントの動作を確認し、プロンプトまたは機能を調整できます。

## お疲れさまでした。

AI エージェントが、正常に ContosoShop アプリケーションに統合されました。 サポート ページを対話形式でスマートにしただけでなく、エンタープライズ アプリの設定で (GitHub Copilot SDK を使用して)、ユース ケースと機能の特定や実装から、すべてのエンド ツー エンドの接続まで、AI を活用する方法も学習しました。

このラボでは、バックエンドの機能に対してユーザー サポートを自動化するために動作する AI "エージェント" を使用する強力なパターンを紹介しました。 eコマース以外にもこのパターンを適用できます。たとえば、IT ヘルプデスクタスクを自動化する AI エージェント (パスワードのリセット、ツールを使用したアカウントのロック解除)、または、販売の質問に答えるためにさまざまなシステムからデータをプルする顧客関係管理のエージェントなど、可能性は膨大です。 大切なことは、ここで実践したように、エージェントの能力とセーフガードを慎重に設計することです。

さらに自由に実験してみてください。エージェントのプロンプトを調整してトーンや詳細度を変更したり、Q&A ペアを追加してその限界をテストしたり、騙してみてエッジ ケースの処理方法を確認したりすることもできます。 これにより AI の動作について、また、実際の信頼性のためにエンタープライズ AI エージェントを改善する方法について、より深い分析情報が得られます。

**ラボの終了**
