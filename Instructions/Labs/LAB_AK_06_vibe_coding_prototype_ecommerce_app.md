---
lab:
  title: 演習 - GitHub Copilot エージェントを使用して Vibe コーディングを始める
  description: Vibe コーディング プロセスと GitHub Copilot エージェントを使用してプロトタイプ アプリを作成する方法について説明します。
  duration: 30 minutes
  level: 200
  islab: true
  primarytopics:
    - GitHub
    - Visual Studio Code
---

# GitHub Copilot エージェントを使用して Vibe コーディングを始める

Vibe コーディングとは、GitHub Copilot エージェントなどの AI ツールを使用してソフトウェアを生成するプログラミング手法です。 手動でコードを書く代わりに、目的のアプリについて自然言語の説明をユーザーが提供すると、AI が対応するコードを生成します。 この結果、プログラマの役割は、従来のコーディングから AI によって生成された出力の指導、テスト、改良へと移行します。

この演習では、Vibe コーディング プロセスと GitHub Copilot エージェントを使用して、オンライン ショッピング アプリのプロトタイプ バージョンを作成します。 このプロトタイプ アプリには、製品、製品の詳細、ショッピング カート、チェックアウトのページが含まれています。アプリには、ページ間の基本的なナビゲーションと、アプリの機能を説明するために役立つ限定的なデータセットが含まれています。 プロトタイプには、ユーザー認証、支払い処理、データベース統合などのバックエンド機能は含まれていません。

この演習の所要時間は約 **30** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です。

- Visual Studio Code。
- GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

この演習のラボ環境としてローカル PC を使用している場合:

- Visual Studio Code インストーラー ファイルは、次の URL からダウンロードできます: <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code をダウンロードする</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開きます: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習をサポートするホスト ラボ環境を使用している場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーを開き、次の URL をサイト ナビゲーション バーに貼り付けてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

## 演習のシナリオ

あなたは起業家であり、Vibe コーディング プロセスを使用してプロトタイプのショッピング アプリを作成したいと考えています。 最初のプロトタイプでは、ユーザーがオンライン ショッピング アプリに想定する基本的な機能と、あなたが想定している特定の機能を示す必要があります。

開発プロセスを開始するに当たり、次の基本仕様を特定します。

1. HTML、CSS、JavaScript を使用して、クライアント側 Web アプリを作成します。
2. 次の Web ページを含めます: Products、ProductDetails、ShoppingCart、Checkout。
3. ページ間のナビゲーションを可能にします。

この演習には、次のタスクが含まれています。

1. **製品要件を定義する**:GitHub Copilot を使用して、基本仕様をより詳細な製品要件に移行できます。

1. **初期プロトタイプ アプリを作成する**:GitHub Copilot エージェントと製品要件を使用して、初期プロトタイプ アプリを作成します。

1. **プロトタイプ アプリを改良する**:GitHub Copilot エージェントを使用して、一連の反復的な更新を完了し、ユーザー エクスペリエンスを改良して、意図した要件をアプリが満たしていることを確認します。

> **注**:プロトタイプ アプリはアプリケーションの初期段階における対話型モデルであり、アプリケーションのビジュアル デザインとユーザー エクスペリエンスを示すものです。 この演習では、プロトタイプ アプリに基本機能を実装し、少数の上位レベルのユース ケースを満たす必要があります。

## 製品要件を定義する

想定したアプリを AI エージェントが開発できるように、製品要件と意図したユーザー エクスペリエンスを理解してもらう必要があります。 次のいずれかのプロセスを使用して、GitHub Copilot エージェントに意図を伝えることができます。

- **まずコードを作成し、反復して要件を定義する**:このアプローチでは、最小限の基本仕様から始め、すぐにコーディングに進みます。 開発が進むにつれて、反復サイクルを通じてアプリは有機的に進化し、製品の機能とユーザー エクスペリエンスが段階的に形成されます。 このアプローチでは、AI によって実装された機能を検討する過程で、良くも悪くも当初の構想から逸脱するリスクがあります。 AI 主導のプロセスは、特に初期の仕様があいまいな場合や未確定な場合、予想以上に時間がかかり、期待どおりの結果が得られない可能性があります。

- **コーディング前に要件を明確にする**:このアプローチでは、最初から明確さを重視します。 コードを書く前に、AI と協力して製品要件ドキュメント (PRD) を作成します。 PRD には、アプリの目的、対象ユーザー、主な機能、技術的な制約の概要を記載します。 事前に明確な構想を確立することで、目標に沿ったコードを生成する強固な基盤を AI に与え、あいまいさを減らし、実際に意図したアプリを構築できる可能性を高めます。

このタスクでは、GitHub Copilot を使用して基本仕様を評価し、プロトタイプ アプリの製品要件を開発します。

以下の手順に従って、演習のこのセクションを完了します。

1. Visual Studio Code を開きます。

1. [ファイル] メニューで、**[フォルダーをワークスペースに追加]** を選択します。

1. **[フォルダーをワークスペースに追加]** ダイアログで、見つけやすいフォルダーの場所に移動し、「**VibeCoding-PrototypeApp**」という新しいフォルダーを作成して、**[追加]** を選択します。

    フォルダーの場所は、既存の Git リポジトリの外部にある、見つけやすい場所にすることをお勧めします。 たとえば、Windows PC を使用している場合は、**デスクトップ**または**ドキュメント** ディレクトリに「**VibeCoding-PrototypeApp**」という新しいフォルダーを作成できます。

    このラボ演習を完了したら、コード プロジェクトをアーカイブするか削除できます。

1. GitHub Copilot のチャット ビューを開きます。

    チャット ビューを開くには、Visual Studio Code ウィンドウの上部中央付近の検索テキストボックスのすぐ右にある GitHub Copilot アイコンを選択します。

1. チャット モードが **[質問]** に設定され、**[Auto]** モデルが選択されていることを確認します。

    *[モードの設定]* と *[モデルの選択]* のドロップダウン メニューは、チャット ビューの左下隅にあります。

    **GitHub Copilot のモード**:各チャット モード (質問、編集、エージェント) は機能が重複する部分もありますが、それぞれ特定の目的に合わせて最適化されています。

    - **質問**:このモードを使用して、コードベースについて GitHub Copilot に質問します。 質問モードを使用すると、コードの説明、変更の提案、コードベースに関する情報の提供を依頼できます。
    - **編集**:ワークスペース内の特定のコード ファイルを編集するには、このモードを使用します。 編集モードを使用すると、コードのリファクター、コメントの追加、テストの実装、アプリへの新機能の追加を依頼できます。
    - **エージェント**:このモードを使用して、GitHub Copilot をエージェントとして実行します。 エージェント モードを使用すると、コーディング タスクを自律的に実行できます。

    **サポートされているモデル**:GitHub Copilot は、長所が異なる複数のモデルをサポートしています。 速度とコスト効率が優先されるモデルもあれば、正確性、推論、またはマルチモーダル入力 (画像とコードの組み合わせなど) での動作に最適化されているモデルもあります。

1. チャット ビューで、次のプロンプトを入力します。

    ```plaintext
    I want to create a product requirements document (PRD) for an app that I'll develop using a vibe coding process. I want the PRD to include information about the app's purpose, target audience, features, and technical requirements. I've defined the following high-level parameters for my app: 1 - Use HTML, CSS, and JavaScript to create a client-side web app. 2 - Include the following web pages: Products, ProductDetails, ShoppingCart, and Checkout. 3 - Enable navigation between pages. I want the prototype app to implement basic features and satisfy a small number of high-level use cases. The prototype should implement the following: basic use case functionality, simple navigation, a sample dataset, and basic styling. I'll be adding the PRD to the chat context, then asking GitHub Copilot Agent to create the prototype app. What sections should I include in the PRD to enable GitHub Copilot Agent to create my envisioned prototype?
    ```

1. GitHub Copilot によって生成された応答を確認します。

    たとえば、応答は次のサンプルのようになります。

    ```md
    To enable GitHub Copilot Agent to create your envisioned prototype app, your Product Requirements Document (PRD) should include the following sections:
    
    1. App Overview / Purpose  
       - Briefly describe what the app does and its main goal.
    
    2. Target Audience  
       - Define who will use the app (e.g., general consumers, online shoppers, etc.).
    
    3. Core Features  
       - List and describe the main features (e.g., view products, view product details, add to cart, checkout).
    
    4. Page Descriptions  
       - Detail each required page (Products, ProductDetails, ShoppingCart, Checkout) and what should be displayed or possible on each.
    
    5. Navigation  
       - Explain how users move between pages (e.g., navigation bar, links, buttons).
    
    6. Sample Data  
       - Describe the sample dataset (e.g., a few example products with name, price, description, image).
    
    7. Technical Requirements  
       - Specify technologies to use (HTML, CSS, JavaScript, client-side only, no backend).
       - Mention any libraries or frameworks (if any) or state "vanilla JS".
    
    8. Styling  
       - Indicate the level of styling (e.g., basic, clean, minimal, responsive).
    
    9. Use Cases / User Flows  
       - List a few high-level use cases (e.g., browse products, view details, add to cart, proceed to checkout).
    
    10. Out of Scope  
        - Clarify what is not required (e.g., user authentication, payment processing, backend integration).
    
    Including these sections will give clear, actionable guidance for building your prototype app.
    ```

    > **注**:応答のセクション タイトルが違っていたり、セクションの順序が違っていたりしても心配しないでください。 AI ツールによって生成される応答がチャット セッションごとに多少異なることはよくあります。 選択した AI モデル、チャット履歴、チャット セッションのコンテキストも応答に影響する可能性があります。

1. 少し時間を取って、PRD の各セクションを完成させるために必要な情報を確認しましょう。

    明確に定義した PRD にすることで、アプリに対するあなたの構想を GitHub Copilot エージェントが明確に理解できるようになります。 要件と意図したユーザー エクスペリエンスを満たすプロトタイプ アプリをエージェントが作成できるように、PRD を詳細に記載する必要があります。 この演習の前半で示した基本仕様に基づいて PRD を構築する必要があります。

    特定のセクションに含めるべき情報がわからない場合は、そのセクションの内容を生成するように GitHub Copilot エージェントに依頼できます。 たとえば、「コア機能」や「ユース ケース」のセクションに何を含めるべきかについて GitHub Copilot にアイデアを聞くことができます。

    > **ヒント**: アプリの要件を自然言語で説明したテキストを提供して、その情報を PRD 形式に整えるように GitHub Copilot に依頼できます。 また、GitHub Copilot を使用して PRD の確認と更新を行い、GitHub Copilot エージェントでプロトタイプを作成するために必要なレベルの詳細を確実に実現することもできます。

1. チャット ビューで、次のプロンプトを入力します。

    ```plaintext
    The PRD sections that you suggested look good. Here's some information that should help you construct the PRD:

    My prototype app targets online shoppers interested in ordering my products. The prototype should include the following:

    - A dynamic user interface that scales automatically to appear correctly on large or small screens (desktop and phone devices).
    - A simple dataset that defines 10 fruit products. The dataset should include: product name, description, price per unit (where unit could be the number of items, ounces, pounds, etc.). If possible, I want to include a simple image (an emoji) that represents the product.
    - A navigation menu on the left side of the screen that allows users to navigate between the Products, ProductDetails, ShoppingCart, and Checkout pages.
    - Basic styling that makes the user interface visually appealing, but it doesn't need to be fully responsive or polished.

    The prototype app won't include any backend functionality, such as user authentication, payment processing, or database integration. It will be a static prototype that demonstrates the basic concepts.

    Here's a description of the user interface:

    - The Products page should display a list of products with basic information such as product name, price per unit, and an image (an emoji). The Products page should also provide a way to select a desired quantity of a product and an option to add selected items to the shopping cart.
    - The ProductDetails page should display detailed information about a product when the product is selected from the Products page. The ProductDetails page should display the product name, a description of the product, the price per unit, and an image (an emoji) representing the product. The ProductDetails page should also provide a way to navigate back to the Products page.
    - The ShoppingCart page should display the list of products added to the cart. The list should include the product name, quantity, and total price for that product. The ShoppingCart page should also provide a way to update the quantity of each product that's in the cart, and an option to remove products from the cart.
    - The Checkout page should display a summary of the products being purchased, including product name, quantity, and price. The total price should be clearly displayed along with the option to "Process Order".
    - The left-side navigation menu should provide basic navigation between pages. The navigation bar should collapse down to display a one or two letter abbreviation when the display width drops below 300 pixels. The navigation bar should allow users to navigate between the app pages.
    ```

    提供した情報に基づいて、GitHub Copilot によって PRD 案を含む応答が生成されます。 応答には、先ほど確認したセクションが含まれ、提供した情報に基づいた各セクションの内容が記載されているはずです。

1. チャット ビューで **[エージェント]** モードを選択します。

    [モードの設定] ドロップダウン メニューは、チャット ビューの左下隅にあります。

1. チャット ビューで、次のプロンプトを入力します。

    ```md
    Create a markdown file named VibeCodingPRD.md using your suggested sections and the inputs that I've provided.
    ```

1. チャット ビューで提案された VibeCodingPRD.md ファイルを保存するには、**[保持する]** を選択します。

1. 少し時間を取って、PRD ファイルを確認しましょう。

    この演習には、提案された PRD ファイルで十分なはずです。 ただし、不足や不一致が見つかった場合、またはさらに情報を追加する場合は、Visual Studio Code でファイルを直接編集できます。 また、追加の情報を生成したり、説明を明確にしたりして PRD を改良するように GitHub Copilot に依頼することもできます。

1. Visual Studio Code のエクスプローラー ビューを開きます。

1. チャット ビューで、次のプロンプトを入力します。

    ```md
    Can you create low-fidelity wireframe diagrams (or text-based layouts) that represent the web pages and navigation bar (expanded and collapsed) for my prototype app? Use the PRD that I've provided as a reference.
    ```

    GitHub Copilot エージェントにより、プロトタイプ アプリのユーザー インターフェイスを表す低忠実度のテキストベースのレイアウトが生成されるはずです。 このような "ワイヤーフレーム図" は、ユーザー インターフェイスと、ユーザーがアプリを操作する方法を定義するのに役立ちます。

    たとえば、応答は次のようになります。

    ```md
    Certainly! Here are text-based, low-fidelity wireframe layouts for each main page of your prototype app, based on your PRD:

    ---

    ### 1. Products Page
    

    +------------------------------------------------------+
    | [Nav] Products | ProductDetails | Cart | Checkout    |
    +----------------+-------------------------------------+
    | 🍎 Apple         $1.00/each   [Qty: - 1 +] [Add]     |
    | 🍌 Banana        $0.50/each   [Qty: - 1 +] [Add]     |
    | 🍊 Orange        $0.80/each   [Qty: - 1 +] [Add]     |
    | ... (other fruits)                                  |
    +------------------------------------------------------+
    
    ---
    
    ### 2. ProductDetails Page
    
    +------------------------------------------------------+
    | [Nav] Products | ProductDetails | Cart | Checkout    |
    +----------------+-------------------------------------+
    | 🍎 Apple                                         |
    | Fresh, crisp apples                              |
    | Price: $1.00/each                               |
    | [Qty: - 1 +] [Add to Cart]                      |
    | [Back to Products]                              |
    +------------------------------------------------------+
    
    ---
    
    ### 3. ShoppingCart Page
    
    +------------------------------------------------------+
    | [Nav] Products | ProductDetails | Cart | Checkout    |
    +----------------+-------------------------------------+
    | Cart Items:                                       |
    | 🍎 Apple   Qty: [ - 2 + ]  $2.00   [Remove]        |
    | 🍌 Banana  Qty: [ - 1 + ]  $0.50   [Remove]        |
    | -------------------------------------------------- |
    | [Update Cart]                                     |
    | [Proceed to Checkout]                             |
    +------------------------------------------------------+
    
    ---
    
    ### 4. Checkout Page
    
    +------------------------------------------------------+
    | [Nav] Products | ProductDetails | Cart | Checkout    |
    +----------------+-------------------------------------+
    | Order Summary:                                     |
    | 🍎 Apple   x2   $2.00                              |
    | 🍌 Banana  x1   $0.50                              |
    | -------------------------------------------------- |
    | Total: $2.50                                      |
    | [Process Order]                                   |
    +------------------------------------------------------+
    
    ---
    
    ### 5. Navigation Bar (Left Side, Collapsible)
    
    +-------------------+      +-----+
    | Products          |      | P   |
    | ProductDetails    |      | D   |
    | Cart              |      | C   |
    | Checkout          |      | CO  |
    +-------------------+      +-----+
    (Full width)             (Collapsed <300px)
    
    ---
    
    These wireframes provide a clear, simple structure for each page and the navigation menu, matching your requirements. If you want these added to your PRD or as a separate markdown file, let me know!
    ```

    > **注**:ワイヤーフレーム図を作成する方法は他にもたくさんあります。 Vibe コーディング プロセスに合わせた AI ベースのアプローチには、Microsoft の M365 Copilot を使用できます。 M365 Copilot にアプリの説明 (PRD の内容) を提供し、低忠実度のワイヤーフレーム図の画像を作成するように AI に依頼するだけです。 手動で作成する高忠実度のワイヤーフレーム図の場合は、Figma などの UI/UX デザイン ツールを使用できます。

1. チャット ビューで、次のプロンプトを入力します。

    ```md
    Save the low-fidelity wireframe diagrams as text files, one file for each web page and one for navigation.
    ```

1. チャット ビューを監視して、すべてのファイルが保存されていることを確認し、**[保持する]** を選択します。

1. 少し時間を取って、ワイヤーフレーム図を確認しましょう。

    修正を必要とする明らかな問題が見つかった場合は、Visual Studio Code でワイヤーフレーム図を直接編集できます。 GitHub Copilot にワイヤーフレーム図の改良を依頼することもできます。

    この演習では、ワイヤーフレーム図 (テキスト レイアウト) は正確である必要はなく、提案されたワイヤーフレームを変更せずにそのまま使用できます。 ただし、演習の後半でワイヤーフレーム図に起因する問題が発生した場合には、GitHub Copilot エージェントに問題の解決を依頼できます。

    > **ヒント**: ワイヤーフレーム図の解釈方法がわからない場合、またはダイアグラムのいずれかが間違っていると思われる場合は、GitHub Copilot にダイアグラムの説明を依頼してください。 たとえば、「ワイヤーフレーム図を確認し、それらを使用して、ユーザー インターフェイスのレイアウトとユーザーがアプリを操作する方法を説明してください」のように GitHub Copilot エージェントに依頼できます。 GitHub Copilot の説明が期待に添わない場合は、意図したユーザー エクスペリエンスに合わせてワイヤーフレーム図を更新するように GitHub Copilot エージェントに依頼できます。

## 初期プロトタイプ アプリを作成する

GitHub Copilot エージェントでは、製品要件とワイヤーフレーム図を使用してプロトタイプ アプリケーションを開発できます。 十分に詳細な製品要件とワイヤーフレーム図を提供すると、アプリに対して意図したユーザー エクスペリエンス、アプリの機能、設計目標をエージェントに理解してもらいやすくなります。

- PRD には、アプリの目的、対象ユーザー、機能、技術要件に関する詳細情報を記載します。
- ワイヤーフレーム図は意図したユーザー インターフェイスを示すものであり、ユーザー操作の説明に役立ちます。

このタスクでは、GitHub Copilot エージェントを使用して、作成した PRD とワイヤーフレーム図に基づいて初期プロトタイプ アプリを作成します。

以下の手順に従って、演習のこのセクションを完了します。

1. Visual Studio Code で、VibeCoding-PrototypeApp フォルダー内に **ShoppingApp** という新しいフォルダーを作成します。

    GitHub Copilot エージェントには、新しいアプリ ファイルのワークスペースとして使用する空のフォルダーが必要です。

    Visual Studio Code のエクスプローラー ビューは次のようになります。

    ```plaintext
    UNTITLED (WORKSPACE)
    └── VibeCoding-PrototypeApp
        ├── ShoppingApp
        ├── VibeCodingPRD.md
        ├── wireframe-checkout.txt
        ├── wireframe-navigation.txt
        ├── wireframe-product-details.txt
        ├── wireframe-products.txt
        └── wireframe-shopping-cart.txt
    ```

1. PRD とワイヤーフレーム図をチャット コンテキストに追加します。

    これらのファイルをチャット コンテキストに追加すると、GitHub Copilot エージェントによる応答の生成時にファイルが参照されるようになります。

    チャット コンテキストにファイルを追加するには、エクスプローラー ビューからチャット ビューにファイルをドラッグ アンド ドロップするか、チャット ビューの左下の領域にある **[コンテキストの追加]** ボタンを使用します。

1. エクスプローラー ビューで **ShoppingApp** フォルダーを選択します。

1. チャット ビューで、次のプロンプトを入力します。

    ```md
    I want you to create a prototype shopping app using the information in my PRD and wireframe diagrams. Create the prototype app in the selected 'ShoppingApp' folder. The prototype should implement the following: basic use case functionality, simple navigation, a sample dataset, and basic styling. After creating the prototype app, add a '.github/copilot-instructions.md' file to the workspace. Add the contents of the PRD and wireframe files to the 'copilot-instructions.md' file.
    ```

    GitHub Copilot エージェントはこのプロンプトを使用し、定義された要件に基づいて初期プロトタイプ アプリを生成します。

    - エージェントにより、**ShoppingApp** フォルダーが空であること、ワークスペースとして使用できる状態であることが確認されます。
    - エージェントにより、PRD とワイヤーフレーム図を使用してプロトタイプ アプリ ファイルが作成されます。 **ShoppingApp** フォルダーに次のファイルが作成されます。

        - **app.js**:製品カタログ、ショッピング カート、ナビゲーションの管理など、アプリの機能を実装する JavaScript コードを含みます。
        - **index.html**:Web アプリケーションのエントリ ポイントとして機能し、基本構造を設定し、スタイルとスクリプトをリンクします。
        - **styles.css**:プロトタイプ Web アプリのビジュアル レイアウトとレスポンシブ デザインを指定します。

    - エージェントにより、ワークスペースに **.github/copilot-instructions.md** ファイルが追加され、PRD ファイルとワイヤーフレーム ファイルの内容が **copilot-instructions.md** ファイルに追加されます。

    > **ヒント**:カスタム指示は、ワークスペースまたはリポジトリの .github/copilot-instructions.md ファイルに保存できます。 カスタム指示を使用すると、共通のガイドラインや規則を記述し、特定のコーディング方法や技術スタックに合う応答を得ることができます。 すべてのチャット クエリにこのコンテキストを手動で含めるのではなく、カスタム指示を使用してこの情報をすべてのチャットリクエストに自動的に組み込むことができます。 このような指示は、ファイルが配置されているワークスペースにのみ適用されます。

1. チャット ビューを監視して、プロトタイプ アプリで作業するエージェントの進行状況を追跡します。

    > **注**:GitHub Copilot エージェントは自律エージェントとしてタスクを実行しますが、特定のタスクを実行するときに支援を求める場合があります。 エージェントを支援するには、チャット ビューに表示されるプロンプトに応答します。 たとえば、ターミナルでコマンドを実行するアクセス許可をエージェントから求められた場合は、**[実行]** を選択してエージェントがコマンドを実行できるようにします。 エージェントから要件の説明を求められた場合は、エージェントが要件を理解するのに役立つ応答を提供します。

1. チャット ビューでプロトタイプ アプリ ファイルを保存するには、**[保持する]** を選択します。

1. **ShoppingApp** フォルダーを展開します。

    フォルダーには次のファイルが含まれているはずです。

    ```plaintext
    ShoppingApp
    ├── .github
    │   └── copilot-instructions.md
    ├── app.js
    ├── index.html
    ├── styles.css
    ```

1. 少し時間を取って、各コード ファイルを確認しましょう。

    - **index.html** ファイルは、Web アプリケーションのエントリ ポイントとして機能します。 アプリの基本構造を設定し、スタイルとスクリプト ファイルをリンクするものです。
    - **styles.css** ファイルには、プロトタイプ Web アプリのビジュアル レイアウトとレスポンシブ デザインを指定します。
    - **app.js** ファイルには、製品カタログ、ショッピング カート、ナビゲーション、UI レンダリングを管理する JavaScript コードが含まれています。

    時間に余裕がある場合は、GitHub Copilot に各ファイルの詳細な説明を生成するよう依頼することを検討してください。

1. Visual Studio Code エディターで **index.html** ファイルを開きます。

1. **[実行]** メニューの **[デバッグなしで実行]** を選択します。

    プロンプトが表示されたら、アプリを実行するブラウザーを選択します。

1. プロトタイプ アプリをブラウザーで開いた状態で、PRD に記載されているユース ケースをテストし、想定する機能をプロトタイプ アプリが提供していることを確認します。

    ユース ケースは、プロトタイプ アプリで実装する必要がある基本的な機能を記述したものです。 次に例を示します。

    - ユーザーは、フルーツ製品の一覧を参照できます。
    - ユーザーは、選択した製品の詳細情報を表示できます。
    - ユーザーは、ショッピング カートに商品を追加し、数量を調整することができます。
    - ユーザーは、チェックアウト前にカートを確認して更新できます。
    - ユーザーである場合、注文の概要を表示し、"それを処理" することができます (実際の取引は行われません)。
    - ユーザーは、Products、ProductDetails、ShoppingCart、Checkout のページ間を移動できます。

1. ユース ケースを確認した後、ブラウザー ウィンドウのサイズを変更してアプリの動的な動作をテストします。

    プロトタイプ アプリはデスクトップと電話デバイスでの表示に合わせて自動的に拡大縮小されるため、動的なユーザー インターフェイスを備えている必要があります。

1. 折りたたまれたナビゲーション バーをテストしてみましょう。

    ページ幅が 300 ピクセル未満になるとナビゲーション バーが折りたたまれるように指定しました。 折りたたまれたナビゲーション バーには、アプリ内の各 Web ページを表す 1 文字または 2 文字が表示されます。

    > **注**:ほとんどのデスクトップ ブラウザー (Microsoft Edge を含む) では、最小ウィンドウ幅が 300 ピクセル以上 (多くの場合 320 から 400 ピクセル程度) に制限されています。 つまり、ナビゲーション バーの折りたたみがトリガーされるほどブラウザー ウィンドウのサイズを手動で小さくすることができない可能性があります。

1. (省略可能) プロトタイプ アプリが想定どおりであることを確認するために、追加のテストを実行します。

    必要に応じて、テスト中にメモを取ります。 次のタスクでメモを使用して、プロトタイプ アプリを改良することができます。

1. ブラウザー ウィンドウを閉じるか、Visual Studio Code でアプリを停止します。

## プロトタイプ アプリを改良する

初期プロトタイプ アプリでは、製品要件の基本的な実装が既に提供されているはずです。 ただし、改良や改善の余地があり、意図したユーザー エクスペリエンスを完全には実現していない可能性もあります。

このタスクでは、GitHub Copilot エージェントを使用してプロトタイプ アプリの機能と動作を改良します。

以下の手順に従って、演習のこのセクションを完了します。

1. チャット ビューで、折りたたまれたナビゲーション バーのブレークポイントを調整するには、次のプロンプトを入力します。

    ```md
    #codebase Refactor the prototype app to use a higher breakpoint for the collapsed navigation bar. Change from 300 to 600px. Update the copilot-instructions.md file to explain the updated 600px requirement.
    ```

    画面が狭くなると方向が変わる (縦型から横型に切り替わる) ナビゲーション バーをエージェントが実装した場合は、次のコマンドを使用してナビゲーション バーの動作を更新します。

    ```md
    #codebase Refactor the code to ensure that the navigation bar stays on the left-side of the app for all devices types and sizes. The navigation bar should be responsive and maintain its position, in either an expanded or collapsed mode.
    ```

1. 少し時間を取って、プロンプトに応じて GitHub Copilot エージェントによって生成されるコードの更新を確認しましょう。

1. チャット ビューで **[保持する]** を選択し、更新されたプロトタイプ アプリ ファイルを保存します。

1. アプリケーションを再度実行し、幅が 600 ピクセル未満のときにナビゲーション バーが折りたたまれることを確認します。

1. ブラウザー ウィンドウを閉じるか、Visual Studio Code でアプリを停止します。

1. チャット ビューで次のプロンプトを入力し、エージェントの進行状況を監視します。

    ```md
    #codebase Update the prototype app to display an emoji in the nav bar for each of the web pages. Ensure that the emoji is centered horizontally in the nav bar when the nav bar is collapsed. Update the copilot-instructions.md file to include this product requirement.
    ```

1. 少し時間を取って、コードの更新を確認しましょう。

1. 更新されたプロトタイプ アプリ ファイルを保存するには、チャット ビューで **[保持する]** を選択します。

1. アプリケーションを再度実行し、ナビゲーション バーに絵文字が正しく表示されていることを確認します。

    ナビゲーション バーには、各 Web ページを表す絵文字が表示されます。 ナビゲーション バーを折りたたむと、絵文字は横方向に中央寄せで配置されます。

    ナビゲーション バーに関して追加の問題が見つかった場合は、GitHub Copilot エージェントに依頼して、ナビゲーション バーの動作を改良することができます。 たとえば、「#codebase コードをリファクターして、ナビゲーション バーが常に表示され、展開と折りたたみの 2 ステージのみになるようにしてください」とエージェントに依頼できます。

1. ブラウザー ウィンドウを閉じるか、Visual Studio Code でアプリを停止します。

1. チャット ビューで、追加の改善の機会を特定するには、次のプロンプトを入力します。

    ```md
    #codebase Review the product requirements and wireframe diagrams in the copilot-instructions.md file. Are there any features or requirements that are missing from the implementation? Are there obvious opportunities to improve the user experience?
    ```

1. GitHub Copilot エージェントからの応答を確認します。

    実装したい改善案を 3 つ以上特定します。

1. 実装したい改善内容を説明するプロンプトを作成します。

    GitHub Copilot の提案と作成したテスト メモを使用して、改善を実施します。 たとえば、次の変更の実装を GitHub Copilot エージェントに依頼できます。

    ```md
    #codebase Implement the following improvements to the prototype app:

    - Replace alert() popups with in-app notification banners or toasts.
    - Add a confirmation/thank you message after processing an order.
    - Add a visual indicator (badge) for the number of items in the cart on the nav bar.

    Ensure that the copilot-instructions.md file is updated to reflect any changes to the product features, technical requirements, user experience, or other measurable characteristics.
    ```

    > **ヒント**:GitHub Copilot の応答から情報をコピーして、新しいプロンプトの作成に役立てることができます。 プロンプト内で前の応答のセクションを参照することもできます。

1. 時間に余裕があれば、GitHub Copilot の提案と自分のアイデアを活用してアプリの改良を続けます。

1. [ファイル] メニューで、**[名前を付けてワークスペースを保存]** を選択します。

1. ワークスペース構成ファイル (VibeCoding-PrototypeApp.code-workspace) を **VibeCoding-PrototypeApp** フォルダーに保存するには、**[保存]** を選択します。

    このファイルを使用すると、同じフォルダー構造と設定でワークスペースを保存し、再度開くことができます。

## まとめ

この演習では、GitHub Copilot エージェントを使用して、Vibe コーディング プロセスでプロトタイプ アプリを作成する方法を学びました。 製品要件を定義し、初期プロトタイプ アプリを作成し、意図したユーザー エクスペリエンスと機能をより適切に満たすようにプロトタイプ アプリを改良しました。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認してください。 変更を加えた場合は、必要に応じて元に戻します。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したプロトタイプ アプリ フォルダーをアーカイブするか削除できます。
