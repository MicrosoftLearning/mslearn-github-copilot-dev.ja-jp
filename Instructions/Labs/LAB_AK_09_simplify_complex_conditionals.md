---
lab:
  title: 演習 - GitHub Copilot を使用して複雑な条件を簡略化する
  description: GitHub Copilot ツールを使用して、C# コードベースで複雑な条件付きロジックをリファクタリングする方法を学びます。
---

# GitHub Copilot を使用して複雑な条件を簡略化する

ビジネス アプリケーションの条件付きロジックは、多くの場合、時間の経過に伴って複雑で入れ子が深くなり、コードの読み取り、保守、テストが困難になります。 残念ながら、コードの保守を困難にするのと同じ複雑さが、リファクタリングを困難にする可能性もあります。 コードがビジネス ロジックと密接に結び付いていると、難易度が上がります。

この演習では、GitHub Copilot を使用して、深い入れ子の条件付きロジックを含むコードを分析し、コード ロジックをリファクタリングしてから、リファクタリング後のコードをテストして意図したとおりに動作することを確認します。 GitHub Copilot を質問モードで使用して、コードを理解し、ロジックを簡略化するオプションを調べることができます。 GitHub Copilot をエージェント モードで使用して、複雑な条件付きロジックをより小さい、焦点を絞ったヘルパー メソッドに抽出してコードをリファクタリングし、入れ子を減らします。 複雑な条件を簡略化すると、コードの読み取り、保守、テストが簡単になります。

この演習の所要時間は約**30** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です: Git 2.48 以降、.NET SDK 9.0 以降、C# 開発キット拡張機能をインストールした Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

### ラボ環境を構成する

この演習のラボ環境としてローカル PC を使用している場合:

- ローカル PC をラボ環境として構成する方法については、ブラウザーで次のリンクを開いてください。<a href="https://go.microsoft.com/fwlink/?linkid=2320147" target="_blank">ラボ環境のリソースを構成する</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開きます:<a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習に、ホストされたラボ環境をお使いの場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーのサイト ナビゲーション バーに次の URL を貼り付けてください:<a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

- パッケージのダウンロードと復元のソースとして公式の NuGet.org リポジトリを使用するように .NET SDK を確実に構成するには、次の手順を実行します。

    コマンド ターミナルを開き、次のコマンドを実行します。

    ```bash

    dotnet nuget add source https://api.nuget.org/v3/index.json -n nuget.org

    ```

### サンプル コード プロジェクトをダウンロードする

サンプル プロジェクトをダウンロードして Visual Studio Code で開くには、次の手順に従います。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. サンプル アプリ プロジェクトを含む zip ファイルをダウンロードするには、ブラウザーで次の URL を開きます:[GitHub Copilot ラボ - コード機能の開発](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHCopilotEx9LabApps.zip)

    zip ファイルの名前は**GHCopilotEx9LabApps.zip** です。

1. **GHCopilotEx9LabApps.zip** ファイルからファイルを抽出します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **GHCopilotEx9LabApps.zip** を右クリックし、**[すべて展開]** を選択します。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. **GHCopilotEx9LabApps** フォルダーを、Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で**GHCopilotEx9LabApps** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、**[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**GHCopilotEx9LabApps** を選択してから、**[フォルダーの選択]** を選択します。

1. Visual Studio Code のソリューション エクスプローラー ビューで、次のプロジェクト構造を確認します。

    - GHCopilotEx9LabApps\
        - ECommercePricingEngine\
            - Dependencies\
            - ECommercePricingDemo.cs
            - Output-ECommercePricingEngine.txt
            - SecurityTest.cs
        - LoanApprovalWorkflow\
            - Dependencies\
            - LoanApprovalDemo.cs
            - Output-LoanApprovalWorkflow.txt
            - SecurityTest.cs

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 クライアントは、複雑な条件付きロジックをリファクタリングしてコードの可読性と保守性を高めるための支援を必要としています。 あなたには次のアプリが割り当てられています。

- eコマースの価格設定エンジン: 1 つ目のアプリは、さまざまなビジネス ルールに基づいて動的な価格を計算する eコマース価格設定エンジンです。 条件には、メンバーシップ レベル、注文額、クーポン コード、製品カテゴリ、配送ルールが含まれます。
- ローン承認ワークフロー: 2 つ目のアプリは、ローン申請をさまざまな要因に基づいて評価するローン承認ワークフローです。 条件には、収入、雇用状況、債務比率、担保、信用履歴が含まれます。

この演習には、次のタスクが含まれています。

1. eコマース価格設定エンジンのコードベースを確認します。
1. GitHub Copilot を使用して、eコマース価格設定コードでのリファクタリングの機会を特定します。
1. GitHub Copilot エージェントを使用して eコマース価格設定コードをリファクタリングします。
1. リファクタリング後の eコマース価格設定コードをテストします。
1. (省略可能) LoanApprovalWorkflow デモ アプリの複雑な条件を簡略化します。

### eコマース価格設定エンジンのコードベースを確認する

あらゆるリファクタリング作業の最初の手順は、既存のコードベースを確実に理解することです。

このタスクでは、eコマース価格設定エンジン プロジェクトを開き、GitHub Copilot を使用して複雑な条件付きロジックを分析します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code で GHCopilotEx9LabApps フォルダーが開かれていることを確認してください。

    サンプル コード プロジェクトをダウンロードしていない場合は、「**開始する前に**」セクションを参照してください。

1. **ECommercePricingEngine** コード プロジェクトのビルドが成功することを確認します。

    たとえば、ソリューション エクスプローラー ビューで**ECommercePricingEngine** を右クリックし、**[ビルド]** を選択します。

    "null リテラルを null 非許容参照型に変換できません" という警告が、 プロジェクトをビルドするときに表示されますが、エラーは発生しません。 この演習の目的上、この警告は無視してかまいません。

1. GitHub Copilot チャット ビューを開きます。

    チャット ビューがまだ開いていない場合は、Visual Studio Code ウィンドウ上部の [検索] テキスト ボックスのすぐ右側にある **[チャット]** アイコンを選択して開くことができます。

1. チャット ビューで、チャット モードが **[質問]** に設定され、モデルが **[GPT-4.1]** に設定されていることを確認します。

    これらの設定は、チャット ビューの左下隅に表示されます。 GitHub Copilot の**質問**モードは、一般的なコーディングに関する質問をしたり、コード関連の説明を生成したりするために使用します。 GitHub Copilot Free プランに含まれている**GPT-4.1** モデルは、コードのリファクタリングに関連したコード分析、説明、ガイダンスの用途に適しています。

    この演習の後半で GitHub Copilot の**エージェント** モードを使用しますが、ここではコードの分析と説明のために**質問**モードを使用します。

    > [!NOTE]
    > 特定のタスクに対して、他のモデルよりも適しているモデルがあります。 どのモデルを選択するかが、GitHub Copilot によって生成される応答に影響する可能性があります。 推奨設定を使用してこのラボの演習を完了したら、別のモデルを使用して演習を繰り返し、結果を比較することをお勧めします。

1. Visual Studio Code で、**ECommercePricingDemo.cs** ファイルを開きます。

    このファイルには、次のクラスが含まれています。

    - ユーザー:メンバーシップ レベル、購入履歴、特別なステータス (学生、従業員、企業など) のプロパティを使用して、顧客を表します。 さまざまな割引と特典の対象を決定するために使用されます。
    - クーポン: コード、有効性、種類 (割引または送料)、価額のプロパティを使用して、割引または送料クーポンを表します。 価格計算で使用され、追加の割引または無料配送を適用します。
    - 品目:名前、カテゴリ、価格を使用して、注文内の製品を表します。 注文を作成し、小計とカテゴリ固有の割引を計算するために使用されます。
    - 注文:顧客の注文を表し、品目、配送先地域、クーポン、イベント、支払い方法、その他の注文固有のフラグの一覧を含みます。 小計を計算し、カテゴリの存在を確認し、注文の特性 (高価額、混合カテゴリなど) を決定するメソッドを提供します。
    - PricingEngine: 注文の最終的な価格を計算するための主要なロジックが含まれています。 ユーザーのステータス、注文の詳細、クーポン、カテゴリ固有のルールに基づいて割引を適用します。 セキュリティ チェックを処理し、割引と価格を安全な範囲内に維持します。
    - Program: エントリ ポイント。 テスト ユーザー、クーポン、注文を作成し、上記のクラスを使用して一連の複雑な価格設定シナリオを実行します。 さまざまな状況で価格設定エンジンのロジックがどのように適用されるかを示します。

    各クラスは、eコマース価格設定システムで実際のエンティティまたはプロセスをモデル化するものであり、Program クラスでは、それらの相互作用によって価格計算のシミュレーションとテストを行います。 たとえば、User、Order、Coupon のインスタンスは PricingEngine.CalculateFinalPrice に渡され、適用可能なすべての割引を適用して最終的な価格を計算し、表示します。

1. **PricingEngine** クラスを見つけて、**CalculateFinalPrice** メソッド全体を選択します。

    CalculateFinalPrice メソッドには、複雑な深い入れ子の条件付きロジックが含まれています。これにより、メンバーシップ レベル、季節イベント、企業アカウント、サブスクリプション サービスに加えて、さまざまな割引シナリオを評価します。

1. 少し時間を取って、**CalculateFinalPrice** メソッドの条件付きロジックをスクロールしてみてください。

    このメソッドは、異なる割引シナリオを処理する複数の入れ子になった条件を使用しており、複雑で読みにくくなっています。 この複雑さは、ユーザーのステータス、注文の詳細、クーポン コードに基づいて適用する必要がある、さまざまなビジネス ルールから生じるものです。

1. 選択したコードを右クリックし、**[Copilot]** >**[説明]** を選択します。

    CalculateFinalPrice メソッドが GitHub Copilot で分析され、コードの動作の詳細な説明が表示されます。

1. 少し時間をかけて、GitHub Copilot による説明を確認しましょう。

    この説明では、階層メンバーシップ、クーポン適用、まとめ買いインセンティブ、カテゴリ固有のルールなど、主な割引カテゴリが強調されているはずです。

    次に例を示します。

    ```md
    The CalculateFinalPrice method is a comprehensive pricing engine for an e-commerce scenario. It starts by validating its inputs, ensuring that both the user and order objects are not null and that the order data is valid. This prevents null reference exceptions and guards against invalid or malicious input. The method then calculates the base total of the order and checks that it falls within a reasonable range, aborting the calculation if the total is suspiciously low or high.
    
    Next, the method initializes variables for the discount percentage, shipping cost, and a list to track all applied discounts. The core of the function is a series of nested conditional blocks that determine which discounts the user is eligible for. These are based on membership level (Premium, Gold, Silver), user attributes (such as being a student, employee, or first-time buyer), order characteristics (like high value, bulk purchase, or mixed categories), and special events (such as Black Friday or Back to School). Each eligible discount is added using the SafeAddDiscount helper, which ensures the total discount does not exceed a maximum cap and records a description of the discount.
    
    The method also handles coupon codes, applying either a percentage discount or free shipping, with additional enhancements for certain membership levels or order types. For example, Premium members may receive a boosted coupon value, and further stacking is possible during special events or for corporate accounts.
    
    After all discounts are considered, the method applies additional bulk purchase incentives if the order contains a large number of items. It then enforces a final cap on the total discount to prevent excessive reductions. The final price is calculated using ApplyCategorySpecificDiscounts, which applies category-based discount caps (e.g., electronics discounts are limited to 15%). The shipping cost is added, and the final price is ensured to be above a minimum threshold.
    
    Finally, the method outputs a summary of the calculation, including the base total, all applied discounts, the total discount percentage (with a note about category caps), the shipping cost, and the final price. This approach ensures transparency and traceability in how the final price is determined, while also enforcing business rules and security best practices.
    ```

    時間を取って既存のコードを理解することは、この演習の後半でリファクタリングのオプションを見分けて選択するのに役立ちます。

1. チャット ビューで、計算プロセスの詳細な分析を取得するには、次のプロンプトを入力します:

    ```plaintext
    @workspace Explain the business logic flow in the CalculateFinalPrice method. What are the different discount paths and how do they interact with each other? What are the key business rules that govern pricing calculations?
    ```

    この分析は、さまざまな割引カテゴリがどのように相互作用し、価格計算の各ステップでどのようなビジネス ルールが適用されるのかを理解するために役立ちます。

1. 少し時間をかけて、GitHub Copilot によって生成された説明を確認しましょう。

    応答では、主要な割引の経路と、それらがどのように相互作用するかが識別されているはずです。 次に例を示します。

    ```md
    The `CalculateFinalPrice` method in `ECommercePricing.PricingEngine` implements a layered, rule-driven approach to pricing calculation for an e-commerce order. Here’s how the business logic flows and how the discount paths interact:
    
    ### 1. **Input Validation**
    - The method first validates that both `user` and `order` are not null and that the order data is valid (e.g., no negative prices, all required fields present).
    - It checks that the order subtotal is within allowed bounds (greater than zero and less than $1,000,000).
    
    ### 2. **Base Discount Initialization**
    - The method initializes the discount percentage (`discountPercent`) to 0 and calculates the base shipping cost.
    
    ### 3. **Membership-Based Discount Paths**
    - The first major decision point is the user's membership level:
      - **Premium:** Starts with a 15% discount, then applies additional discounts for high-value orders, seasonal events, corporate accounts, subscriptions, loyalty (years as member), lifetime spending, and express shipping. Each condition is nested, so deeper benefits require all previous conditions to be met.
      - **Gold:** Starts with a 12% discount, then adds bonuses for seasonal events, bulk purchases, category diversity, employee status, pre-orders, and alternative payment methods. These are also nested, but less deeply than Premium.
      - **Silver:** Starts with 8%, with further bonuses for students, back-to-school events, bulk purchases, electronics focus, gift wrap, and express shipping. These are nested within the student path.
      - **First-Time Buyer:** If the user is a first-time buyer (and not a member), a 10% discount is applied, with further bonuses for seasonal events, order size, payment method, high-value orders, premium shipping regions, and express shipping.
    
    ### 4. **Coupon Application**
    - If a coupon is present:
      - **Valid Percent Coupon:** The coupon value is capped at 50%. Premium and Gold members get a multiplier (Premium: 1.3x up to 50%, Gold: 1.2x up to 40%). Additional stacking occurs for Premium members on Black Friday, corporate accounts, and bulk orders, with each step further capping the discount.
      - **Valid Shipping Coupon:** Shipping is set to zero for domestic orders or Premium members.
      - **Expired Coupon:** No discount is applied, and a message is logged.
    
    ### 5. **Bulk Purchase Incentives**
    - Additional discounts are applied for orders with 10+ items (5%) or 20+ items (8%).
    
    ### 6. **Discount Capping**
    - The total discount percentage is capped at 95% to prevent excessive reductions.
    
    ### 7. **Category-Specific Discount Rules**
    - The final discount is applied with category-specific caps:
      - **Electronics:** Discount capped at 15%.
      - **Clothing:** Discount capped at 20% (25% during Back-to-School).
      - **Accessories/Other:** Full discount applies.
    
    ### 8. **Final Price Calculation**
    - The final price is the sum of all discounted category subtotals plus shipping, with a minimum enforced price of $0.01.
    
    ### 9. **Result Output**
    - The method prints the base total, all applied discounts, the total discount percentage (with a note about the electronics cap), shipping cost, and the final price.
    
    ---
    
    ## **Discount Path Interactions**
    - **Nesting:** Many discounts are nested, meaning deeper discounts require all previous conditions to be true (e.g., Premium members with high-value orders, during Black Friday, who are also corporate accounts, etc.).
    - **Stacking:** Discounts from different sources (membership, coupons, bulk, etc.) are added together, but always subject to the overall cap.
    - **Category Caps:** Even if the total discount is high, certain categories (like electronics) have stricter caps, so the effective discount for those items may be lower than the overall discount.
    
    ---
    
    ## **Key Business Rules**
    - **Validation:** Orders and users must be valid; otherwise, pricing is aborted.
    - **Membership Drives Discounts:** Higher membership levels unlock deeper and more complex discount paths.
    - **Coupons Are Enhanced by Membership:** Premium and Gold members get more value from percent-off coupons.
    - **Bulk and Seasonal Bonuses:** Large orders and seasonal events can trigger additional discounts.
    - **Category Protection:** Electronics and clothing have maximum discount caps to protect margins.
    - **Shipping Can Be Discounted:** Shipping can be free with certain coupons or for Premium members.
    - **Discount Caps:** No combination of discounts can exceed 95% off the order.
    - **Transparency:** All applied discounts are tracked and displayed for auditability.
    
    For more details, see the full implementation in ECommercePricingDemo.cs, especially the `ECommercePricing.PricingEngine.CalculateFinalPrice` method.
    ```

1. GitHub Copilot の説明と、`CalculateFinalPrice` メソッドの複雑な条件付きロジックに関するあなた自身の観察結果を比較します。

    あなた自身はおそらく、観察によって、メンバーシップ レベル、クーポン コード、まとめ買いインセンティブなど、主要な割引のソースを特定したでしょう。 GitHub Copilot の説明は、あなたの分析を補強し、これらの割引経路がどのように相互作用するかについてや、価格計算を制御する主要なビジネス ルールについて、さらなる分析情報を提供するはずです。

    - さまざまなメンバーシップ レベル (Premium、Gold、Silver) で割引が適用されるしくみと、初回購入者の取り扱い方法に注目してください。
    - クーポンの有効性の評価と適用の方法、およびクーポン割引とメンバーシップ割引の相互作用に注目してください。
    - 数量ベースの割引が品目数に基づいてどのように適用されるかに注目してください。

    入れ子になった条件を通じてビジネス ルールが適用され、ユーザーのステータス、注文の詳細、クーポン コードに基づいて割引が確実に正しく適用されます。 これらのルールについて、リファクタリング プロセスを進める必要があります。

1. ECommercePricingEngine プロジェクトを実行し、出力を確認します。

    出力には、ユーザー、注文、クーポンのさまざまな組み合わせの基本価格と割引価格が表示されます。 この演習の最後に、リファクタリング後のコードで生成される結果は、元のコードと同じである必要があります。

    出力には、無効な入力と悪意のある価格操作の試行に対する、基本的なセキュリティ テストが含まれていることがわかります。 これらのテストは、考えられるすべての攻撃ベクトルや運用アプリで必要なテストのレベルを表しているわけではありませんが、コードの品質とセキュリティを確保することが必要条件であることを示す役割を果たします。 **SecurityTest.cs** ファイルは ECommercePricingEngine プロジェクトの一部です。

    > [!NOTE]
    > 出力のコピーは、eCommercePricingEngine フォルダーに含まれる**Output-ECommercePricingEngine.txt** ファイルにあります。 結果を比較する場合、またはサンプル データを変更する場合は、自分自身の出力ファイルを作成できます。 この演習の最後まで進んだら、出力ファイルを使用して、リファクタリング後のコードで元のコードと同じ結果が生成されることを確認します。

    プロジェクトを実行するには: Visual Studio Code エディターで ECommercePricingDemo.cs ファイルを開いている場合は、エディター ウィンドウの右上隅にある [実行] ボタン (このファイルに関連付けられているプロジェクトの実行) を選択して、プロジェクトを実行できます。 ソリューション エクスプローラー ビューからプロジェクトを実行するには、**ECommercePricingEngine** を右クリックし、**[デバッグ]** を選択してから、**[新しいインスタンスの開始]** を選択します。

### GitHub Copilot を使用して、eコマース価格設定コードでのリファクタリングの機会を特定する

GitHub Copilot は、複雑なコードを分析し、コードのリファクタリングの機会を特定するための優れたツールです。

このタスクでは、GitHub Copilot を使用して特定のリファクタリング機会を特定し、複雑な条件を簡略化するヘルパー メソッドを提案します。 前のタスクでの観察結果を使用して、GitHub Copilot に与えるプロンプトを作成します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot チャット ビューが**質問**モードで開いていて、**GPT-4.1** モデルが選択されていることを確認します。

1. ドラッグ アンド ドロップ操作を使用して、チャット コンテキストに**ECommercePricingDemo.cs** ファイルを追加します。

    ECommercePricingDemo.cs は Visual Studio Code エディターで既に開かれていますが、チャット コンテキストに追加すると、GitHub Copilot でコード ファイル全体を分析することが促されるため、より正確な提案が得られます。 GitHub Copilot を使用するときは、プロンプトに**@workspace** または **#codebase** タグを含めた場合でも、関連するファイルをチャット コンテキストに追加することがベスト プラクティスです。

1. 主要な割引経路に関連したコードのモジュール性を向上させるリファクタリングの機会を特定するように GitHub Copilot に求めるプロンプトを送信します。

    プロンプトを作成するときは、次の項目について考慮してください。

    - GitHub Copilot に、**CalculateFinalPrice** メソッドと、それの入れ子になった条件に焦点を合わせるように指示します。
    - 複雑なロジックをより管理しやすい単一責任のヘルパー メソッドに移行するオプションを提案するよう、GitHub Copilot に依頼します。
    - ヘルパー メソッドへの抽出で考慮する必要がある主な割引経路を一覧表示します。
    - コードの構造を簡素化しながらもビジネス ロジックを維持することの重要性を強調します。

    多くの場合、コードを分析するときに GitHub Copilot でコードの相互作用が考慮されるようにすると効果的です。 **@workspace** または **#codebase** タグを使用して、コードベース全体を分析に含めるように GitHub Copilot に指示できます。

    次に例を示します。

    ```plaintext
    @workspace Analyze the CalculateFinalPrice method and suggest refactoring opportunities. Include options that move the nested conditional logic into more manageable, single-responsibility helper methods. Focus on the main discount paths: membership discounts, coupon discounts, and bulk discounts. Maintain the business logic while simplifying the code structure.
    ```

    GitHub Copilot はメソッドを分析し、特定のリファクタリングの機会を提案し、ヘルパー メソッドに抽出できる入れ子になった条件のパターンを特定します。

1. 少し時間を取って、GitHub Copilot の提案を確認します。

    応答では、主要な割引経路をヘルパー メソッドに抽出することや、入れ子を減らすための 1 つ以上のアプローチなど、コードの管理性とモジュール性を向上させる機会が特定されるはずです。

    次に例を示します。

    ```md
    Collecting workspace informationThe `ECommercePricing.PricingEngine.CalculateFinalPrice` method is functionally rich but contains deeply nested conditional logic, especially for membership, coupon, and bulk discounts. This makes the code harder to read, maintain, and test. Here are refactoring opportunities and suggestions:
    
    ## **Refactoring Opportunities**
    
    ### 1. **Extract Membership Discount Logic**
    Move the entire membership-based discount logic into a helper method, e.g., `ApplyMembershipDiscounts`. This method would encapsulate all the nested membership, event, and user-attribute checks, returning the updated discount and applied discounts.
    
    ### 2. **Extract Coupon Discount Logic**
    Move coupon handling into a method like `ApplyCouponDiscounts`. This would handle coupon validation, membership-based coupon boosts, and shipping coupon logic.
    
    ### 3. **Extract Bulk Discount Logic**
    Move the bulk purchase logic into a method such as `ApplyBulkDiscounts`.
    
    ### 4. **Reduce Nesting**
    Within each helper, use early returns or guard clauses where possible to reduce nesting.
    
    ### 5. **Encapsulate Discount State**
    Consider using a small struct or class (e.g., `DiscountContext`) to pass and update the discount percent and applied discounts list, reducing parameter clutter.
    ```

1. CalculateFinalPrice メソッドにおいて、複雑な条件付きロジックを簡略化し、入れ子のレベル数を減らす方法を GitHub Copilot に質問するフォローアップ プロンプトを送信します。

    フォローアップ プロンプトは、分析を調整し、GitHub Copilot からより具体的な提案を得るための優れた方法です。 ここでは、引き続き CalculateFinalPrice メソッドの複雑な条件のコンテキストにおいて、入れ子のレベル数を減らすための追加情報が必要です。 また、フォローアップ プロンプトを使用して、ベスト プラクティスを求めたり、提案された変更がコードに与える影響を理解したりすることもできます。

    次に例を示します。

    ```plaintext
    How can I simplify the complex conditional logic and reduce nesting levels in the CalculateFinalPrice method? For example, the method has multiple nested conditionals that apply different membership discounts based on user status and order details. What are some best practices for reducing nesting levels and improving readability? Explain the benefit of each approach when applied to the CalculateFinalPrice method.
    ```

1. 少し時間を取って、条件付きロジックを簡略化し、入れ子のレベル数を減らすための、GitHub Copilot の提案を確認してください。

    GitHub Copilot から、条件付きロジックを簡略化し、入れ子のレベル数を減らすための提案の一覧が提供されます。 提案の一部は、以前の提案の繰り返しである可能性がありますが、これは想定内のことです。 ただし、入れ子のレベル数の削減と可読性の向上に関連した、次のような新しい提案が表示されるはずです。

    - 早期リターンまたはガード句を使用して、ロジックをフラット化し、インデントを削減し、フローを明確にします。
    - ローカル ヘルパー関数を使用して、重複を削除し、意図を明確にします。
    - スイッチ式またはパターン マッチングを使用して、メンバーシップ ロジックを明確にし、保守しやすくします。
    - メンバーシップごとに小さなメソッドを使用してロジックをモジュール化し、テストと更新を容易にします。
    - 条件にブール変数を使用して、複雑なチェックの可読性を向上させます。

> [!NOTE]
> 演習の次のセクションでは、分析の結果を使用して、GitHub Copilot エージェントに割り当てるタスクを作成します。

### GitHub Copilot エージェントを使用して eコマース価格設定コードをリファクタリングする

GitHub Copilot には、**質問**、**編集**、**エージェント**の 3 つのモードがあります。 エージェント モードで実行すると、GitHub Copilot は自律 AI エージェントとして機能します。

エージェント モードでは:

- プロンプトで、GitHub Copilot エージェントに割り当てるタスクを指定します。
- GitHub Copilot は、タスク情報とコードベースを使用して、関連するコード ファイルを識別し、タスクのコンテキストを確立します。
- GitHub Copilot は、タスクを実行するために使用できるプロセスを作成します。 エージェントは、反復的なアプローチとコード レビューを使用して、タスクが正常に完了したことを確認します。
- GitHub Copilot は、割り当てられたタスクの作業を進めながら、チャット ビューを使用して情報を随時更新します。 また、行われている変更の説明や正当な理由を提供することもできます。
- GitHub Copilot は、タスクの実行に役立てるため、またはコードの変更が正しく機能していることを確認するためにツールを呼び出すことができます。
- GitHub Copilot がタスクの途中で一時停止し、サポートや明確化を求めてくる場合があります。 チャットを監視し、自律エージェントを支援するように求められたら応答することが重要です。
- GitHub Copilot は、Visual Studio Code エディターでコード ファイルを更新します。 タスクが完了したら、GitHub Copilot によって行われた変更を確認してから、コードベースに (個別に、またはまとめて) 適用する必要があります。

演習のこのセクションでは、GitHub Copilot エージェントを使用して PricingEngine クラスをリファクタリングし、CalculateFinalPrice メソッドの複雑な条件付きロジックを簡略化します。 GitHub Copilot エージェントに割り当てるタスクは、コード分析 (前のタスク) 中に GitHub Copilot によって提供された提案に基づきます。

そのためには、以下の手順を実行してください。

1. Visual Studio Code で GitHub Copilot チャット ビューが開かれていることを確認します。

1. チャット ビューで、**エージェント** モードを選択します。

    **[モードの設定]** ドロップダウンは、チャット ビューの左下隅にあります。 **[エージェント]** を選択すると、GitHub Copilot はエージェント モードに切り替わり、割り当てたタスクで自律的に作業できるようになります。

1. 少し時間を取って、GitHub Copilot エージェントに割り当てるタスクの要件を特定します。

    目標を説明し、その目標を達成するために GitHub Copilot エージェントが使用できる手順を示したタスクを記述する必要があります。

    ここでは、目標には次の項目が含まれます。

    - 複雑な条件付きロジックを簡略化し、入れ子のレベル数を減らします。
    - コードの保守性と可読性を向上させます。
    - 既存の機能を保持し、リファクタリング後のコードが元のコードと同じ出力を生成することを確認します。

    前のタスクでは、コードを分析し、リファクタリングの機会を特定するように GitHub Copilot に依頼しました。 2 つの提案セットを生成しました。1 つは、モジュール性を向上させるためにヘルパー メソッドに複雑なロジックを抽出すること、もう 1 つは、入れ子のレベル数を減らし、コードの可読性を向上させることです。

    前のタスクで識別されたリファクタリングの機会には、次のものが含まれます。

    - メンバーシップ レベルの割引ロジックをヘルパー メソッドに抽出します。
    - クーポンの検証と適用のロジックをヘルパー メソッドに抽出します。
    - 数量ベースの割引ロジックをヘルパー メソッドに抽出します。
    - 早期リターンまたはガード句を使用して、ロジックをフラット化し、インデントを削減し、フローを明確にします。
    - ローカル ヘルパー関数を使用して、重複を削除し、意図を明確にします。
    - スイッチ式またはパターン マッチングを使用して、メンバーシップ ロジックを明確にし、保守しやすくします。
    - メンバーシップごとに小さなメソッドを使用してロジックをモジュール化し、テストと更新を容易にします。
    - 条件にブール変数を使用して、複雑なチェックの可読性を向上させます。

    GitHub Copilot エージェント用に作成するタスクには、目標ステートメントと、特定したリファクタリングの機会を組み合わせる必要があります。

    タスクは次の組織構造を実装している必要があります。

    1. 目標ステートメント: タスクの全体的な目標を説明します。
    1. コード構造/モジュール性の更新: モジュール性を向上させるために抽出できる大規模なコード セクションを特定します。
    1. 小規模または詳細なコードの改善: 入れ子のレベル数を減らし、可読性を向上させる具体的な方法を説明します。
    1. 結果:リファクタリング プロセスの予想される結果を説明します。

1. 要件を満たすタスクを作成します。

    タスクには、提案された組織構造を使用して配置された、目標と提案されたリファクタリングの機会が含まれている必要があります。

    次に例を示します。

    ```plaintext
    I need to simplify the complex conditional logic and reduce nesting levels in the CalculateFinalPrice method. The main areas of complexity are associated with membership discounts, coupon processing, and bulk discounts. Each of these areas could be moved into separate helper methods that are called from CalculateFinalPrice. There are several options to reduce nesting levels in the helper methods. Use early returns or guard clauses to flatten logic, reduce indentation, and clarify flow. Use local helper functions to remove duplication and clarify intent. Use switch expressions or pattern matching to make membership logic explicit and maintainable. Use smaller methods per membership to modularize logic and make it easier to test and update. Use Boolean variables for conditions to improve readability of complex checks. The goal is to improve code maintainability and readability while preserving the existing functionality and generated output.
    ```

    このタスクでは、自然言語テキストを使用して、目標および主要なリファクタリングの機会と、入れ子のレベル数を減らし、可読性を向上させるための特定のアプローチを記述します。 このタスクでは、リファクタリング プロセスの予想される結果についても説明します。

1. チャット ビューを使用して、GitHub Copilot エージェントにタスクを割り当てます。

    GitHub Copilot エージェントはタスクとコードベースを評価して、CalculateFinalPrice のリファクタリングと、新しいヘルパー メソッドへのメンバーシップ割引ロジックの抽出を行うためのアプローチを開発します。 エージェントは、さまざまな段階でのコードの更新をテストして、リファクタリングが成功し、ビジネス ロジックがそのまま保持されていることを確認します。

    タスクを送信すると、GitHub Copilot エージェントがタスクの作業を開始します。 チャット ビューで進行状況を監視できます。

    > [!NOTE]
    > タスクを変更する必要がある場合は、チャット ビューでテキストを編集して再送信できます。 GitHub Copilot エージェントはタスクを再評価し、作業を続行します。

1. GitHub Copilot エージェントによってタスクの作業が進められている間、チャット ビューを監視してください。

    エージェントにより、発生した課題とその対処方法など、進行状況に関する最新情報が提供されます。 また、必要な場合は明確化や追加情報を求められることもあります。 必要に応じて、チャット ビューでエージェントのプロンプトに応答してサポートを提供します。

1. リファクタリング タスクが完了したら、推奨された更新内容を Visual Studio Code エディターで確認します。

    GitHub Copilot によって推奨された変更を受け入れる前に、必ず確認してください。 更新内容が、意図したビジネス ロジック、アプリの機能、コーディング標準に合致していることを確認します。

    リファクタリング後のコードには、大幅に簡略化された**CalculateFinalPrice** メソッドが含まれているはずです。これは主に、メンバーシップ割引、クーポン割引、まとめ買い割引を管理するヘルパー メソッドへのメソッド呼び出しで構成されています。 元のメソッドの複雑さが大幅に軽減され、読みやすく保守しやすくなります。

    メンバーシップ割引ヘルパー メソッドを、メンバーシップ レベルに関する複雑なロジックを処理するようにリファクタリングすることも必要です。これには各レベル (Premium、Gold、Silver、初回購入者) に対して追加のヘルパー メソッドを使用します。

1. チャット ビューですべての編集を受け入れるには、**[保持]** を選択します。

    提案された変更が期待を満たしていない場合は、拒否することも、変更のサブセットを受け入れて他の変更を拒否することもできます。 GitHub Copilot を使用してコードをリファクタリングすることは、多くの場合、意図した結果を得るためのプロンプトの調整を含む反復的なプロセスです。

    GitHub Copilot の提案された更新内容に同意した後、提案によって今後の解決が困難な問題が発生したことを認識した場合は、チャット ビューで **[前回の要求を元に戻す]** を選択するか、Visual Studio Code の元に戻す機能を使用して変更を元に戻すことができます。

1. GitHub Copilot に、実装されたコード リファクタリングの更新について説明するように依頼します。

    次に例を示します。

    ```plaintext
    Explain the refactoring updates that you implemented to simplify the complex conditional logic, reduce nesting levels, and improve code maintainability and readability.
    ```

    GitHub Copilot から、複雑な条件付きロジックがどのように簡略化され、コード構造がどのように改善されたかなど、加えられた変更の説明が提供されます。

    次に例を示します。

    ```md
    Here’s an explanation of the refactoring updates made to simplify the complex conditional logic, reduce nesting, and improve maintainability and readability in the `CalculateFinalPrice` method:
    
    ### 1. **Separation of Concerns with Helper Methods**
    - The main discount logic was split into three focused helper methods: `ApplyMembershipDiscounts`, `ApplyCouponDiscounts`, and `ApplyBulkDiscounts`.
    - Each helper method is responsible for a single aspect of the discount calculation, making the main method concise and easy to follow.
    
    ### 2. **Flattened Membership Discount Logic**
    - The deeply nested membership discount logic was moved into `ApplyMembershipDiscounts`, which uses a `switch` statement to dispatch to smaller, membership-specific methods (`ApplyPremiumDiscounts`, `ApplyGoldDiscounts`, `ApplySilverDiscounts`, `ApplyFirstTimeBuyerDiscounts`).
    - Each of these methods uses guard clauses (early returns) and Boolean variables to flatten the logic, reducing indentation and clarifying the flow.
    
    ### 3. **Guard Clauses and Early Returns**
    - Instead of multiple nested `if` statements, each helper method uses guard clauses to exit early when a condition is not met. This keeps the main logic path at the top level and reduces cognitive load.
    
    ### 4. **Switch Expressions and Pattern Matching**
    - The use of `switch` in `ApplyMembershipDiscounts` makes the membership logic explicit and easy to extend or modify.
    - This approach also makes it clear which logic applies to which membership type.
    
    ### 5. **Boolean Variables for Readability**
    - Boolean variables (e.g., `altPayment`, `premiumPayment`) are used to clarify complex conditions, making the code more self-documenting and easier to read.
    
    ### 6. **Local Helper Methods for Duplication and Intent**
    - Smaller, focused methods encapsulate repeated or complex logic, such as safe discount addition and category percentage calculation.
    - This reduces code duplication and clarifies the intent of each operation.
    
    ### 7. **Preserved Business Logic and Output**
    - All business rules, discount caps, and output formatting are preserved, ensuring that the refactoring does not change the program’s behavior.
    
    ## **Benefits**
    - **Readability:** The main method is now a clear sequence of high-level steps.
    - **Maintainability:** Each discount rule is isolated, making it easier to update or test.
    - **Extensibility:** Adding or modifying discount rules is straightforward.
    - **Testability:** Each helper method can be unit tested independently.
    
    This refactoring transforms a complex, deeply nested method into a modular, maintainable, and readable pricing engine, while preserving all business requirements and output.
    ```

1. 少し時間を取って、PricingEngine クラスのリファクタリング後のコードを確認します。

1. フォローアップ プロンプトを送信して、リファクタリング後のコードの特定の部分を説明することや、元のバージョンとリファクタリング後のバージョンを比較することを GitHub Copilot に求めます。

    次に例を示します。

    ```plaintext
    Compare the original PricingEngine class with the new refactored version of the PricingEngine class. Explain the relationship between methods in the two versions. Explain the implementation of business rules in the two versions.
    ```

### リファクタリング後の eコマース価格設定コードをテストする

テストをすることは、リファクタリングによってビジネス ロジックの動作が変更されていないことを確認するために重要です。 さまざまなシナリオでコードを実行して、計算に一貫性があることを確認します。

そのためには、以下の手順を実行してください。

1. プロジェクトをビルドして、コンパイル エラーがないことを確認します。

    たとえば、ソリューション エクスプローラー ビューで**ECommercePricingEngine** を右クリックし、**[ビルド]** を選択します。

    コンパイル エラーがある場合は、リファクタリング後のコードを確認して問題を修正します。 GitHub Copilot は、必要に応じてコンパイル エラーを解決するのに役立ちます。

1. アプリケーションを実行して、リファクタリング後の価格設定ロジックをテストします。

    Visual Studio Code エディターで ECommercePricingDemo.cs ファイルを開いている場合は、エディター ウィンドウの右上隅にある [実行] ボタン (このファイルに関連付けられているプロジェクトの実行) を選択して、プロジェクトを実行できます。 ソリューション エクスプローラー ビューからプロジェクトを実行するには、**ECommercePricingEngine** を右クリックし、**[デバッグ]** を選択してから、**[新しいインスタンスの開始]** を選択します。

    アプリケーションがエラーなく実行され、さまざまなテスト シナリオの価格計算が表示されるはずです。

    > [!IMPORTANT]
    > リファクタリング後のコードでランタイム エラーが発生した場合は、GitHub Copilot エージェントによって行われた変更を確認し、GitHub Copilot を使用して問題を解決します。 必要に応じて、チャット ビューの **[最後の要求を元に戻す]** オプションを使用して直前の変更セットを元に戻してから、GitHub Copilot エージェントに割り当てられたタスクを更新できます。 AI 対応ツールを使用する場合は、プロンプトやタスクを反復処理して結果を改善するのが標準的な方法です。

1. リファクタリング後のコードによって生成された出力を元の出力と比較するように GitHub Copilot に依頼します。

    元の出力である**Output-ECommercePricingEngine.txt** は、ECommercePricingEngine フォルダーに含まれています。

    2 つ目の出力ファイルを作成し、GitHub Copilot に 2 つのファイル間の違いを特定するように依頼できます。

    出力は同じである必要があり、コードの保守性と可読性が向上した一方で、ビジネス ロジックは変更されずに維持されていることを確認します。

### (省略可能) LoanApprovalWorkflow デモ アプリの複雑な条件を簡略化する

時間が許す場合は、ローン承認ワークフロー サンプル アプリの価格設定ロジックを簡略化するために使用したのと同じプロセスを使用して、LoanApprovalWorkflow デモ アプリの複雑な条件を簡略化します。

この省略可能なタスクでは、eコマース価格設定エンジンで使用したのと同じ手法を適用して、ローン承認ワークフローのデモ アプリの複雑な条件付きロジックをリファクタリングします。 ローン承認コードを分析してリファクタリングの機会を特定した後、GitHub Copilot エージェントを使用して、複雑な条件をより小さな焦点を絞ったヘルパー メソッドに抽出します。

> [!IMPORTANT]
> このタスクの手順には、大まかなプロセスのステップが示されていますが、推奨されるプロンプトや詳細な説明は含まれていません。 プロンプトを作成し、GitHub Copilot を効果的に使用する方法の例については、この演習の前のタスクを参照してください。

そのためには、以下の手順を実行してください。

1. **LoanApprovalWorkflow** プロジェクト フォルダーを展開し、**LoanApprovalDemo.cs** ファイルを開いて、コードを確認します。

1. GitHub Copilot を使用して、**LoanApprovalDemo.cs** コードを説明します。これには LoanEvaluator クラスの Evaluate メソッドにある複雑な条件付きロジックが含まれています。

1. LoanApprovalWorkflow プロジェクトを実行して初期ローン承認ロジックをテストし、出力のレコードを作成します。

1. GitHub Copilot を使用して、LoanEvaluator クラスの複雑な条件を簡略化し、入れ子のレベル数を減らし、可読性と保守性を向上させるコード リファクタリングの機会を特定します。

1. LoanEvaluator クラスの複雑な条件付きロジックを簡略化し、入れ子のレベル数を減らし、可読性と保守性を向上させるタスクを GitHub Copilot エージェントに割り当てます (Evaluate メソッドをリファクタリングします)。

    たとえば、GitHub Copilot エージェントに、クレジット スコアの評価、収入と雇用の検証、財務比率の計算、政府プログラムの適格性、ローン条件の決定のためのヘルパー メソッドを作成するように依頼できます。

1. GitHub Copilot エージェントを使用して、複雑な条件付きロジックを簡略化し、ヘルパー メソッドの入れ子のレベル数を減らします。

1. リファクタリング後のコードをテストし、ローン承認デモ アプリケーションが元の実装と同じ結果を生成することを確認します。

## まとめ

この演習では、GitHub Copilot を使用して、コードベースの複雑な条件付きロジックを簡略化する方法について学習しました。 eコマース価格設定エンジンとローン承認ワークフローのデモ アプリを調べてリファクタリングの機会を特定し、GitHub Copilot エージェントを使用して、複雑な条件をより小さな焦点を絞ったヘルパー メソッドに抽出しました。 また、同じビジネス ロジックを維持しながら可読性を向上させるために、コード内の入れ子のレベル数を減らす方法についても学習しました。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認してください。 変更を行った場合は、必要に応じてそれを元に戻します。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したサンプル プロジェクト フォルダーをアーカイブまたは削除できます。
