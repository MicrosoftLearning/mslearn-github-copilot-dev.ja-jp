<!-- ---
lab:
    title: 'Exercise - Implement performance profiling using GitHub Copilot'
    description: 'Learn how to identify and address performance bottlenecks and code inefficiencies using GitHub Copilot tools.'
--- -->

# GitHub Copilot を使用してパフォーマンス プロファイルを実装する

パフォーマンス プロファイルはソフトウェア開発の重要な側面であり、パフォーマンスのボトルネックとコードの非効率性を特定して対処するのに役立ちます。

この演習では、パフォーマンスが低く非効率的なコードを含む既存のプロジェクトをレビューし、コードのパフォーマンスを向上させるオプションを分析した後、コードをリファクタリングして識別された問題に対処してから、リファクタリング後のコードをテストして、機能と可読性を維持しながらコードのパフォーマンスが向上したことを確認します。 GitHub Copilot を質問モードで使い、既存のコード プロジェクトを理解して、明らかになった問題をリファクタリングするためのオプションを調べます。 GitHub Copilot をエージェント モードで使い、コードをリファクタリングしてパフォーマンスを向上させます。 元のコードとリファクタリング後のコードをテストして、変更の影響を測定します。

この演習の所要時間は約 **30** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です: Git 2.48 以降、.NET SDK 9.0 以降、C# 開発キット拡張機能をインストールした Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

### ラボ環境を構成する

この演習のラボ環境としてローカル PC を使用している場合:

- ローカル PC をラボ環境として構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320147" target="_blank">ラボ環境のリソースを構成する</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習に、ホストされたラボ環境をお使いの場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーのサイト ナビゲーション バーにで次の URL を貼り付けてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

- パッケージのダウンロードと復元のソースとして公式の NuGet.org リポジトリを使用するように .NET SDK を確実に構成するには、次の手順を実行します。

    コマンド ターミナルを開き、次のコマンドを実行します。

    ```bash

    dotnet nuget add source https://api.nuget.org/v3/index.json -n nuget.org

    ```

### サンプル コード プロジェクトをダウンロードする

次の手順のようにして、サンプル プロジェクトをダウンロードし、Visual Studio Code でそれを開きます。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. サンプル アプリ プロジェクトを含む ZIP ファイルをダウンロードするには、ブラウザーで次の URL を開きます: [GitHub Copilot ラボ - パフォーマンス プロファイルを実装する](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHCopilotEx10LabApps.zip)

    ZIP ファイルの名前は **GHCopilotEx10LabApps.zip** です。

1. **GHCopilotEx10LabApps.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **GHCopilotEx10LabApps.zip** を右クリックして、**[すべて展開]** を選びます。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. **GHCopilotEx10LabApps** フォルダーを、Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で **GHCopilotEx10LabApps** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**GHCopilotEx10LabApps** を選んでから、**[フォルダーの選択]** を選びます。

1. Visual Studio Code のソリューション エクスプローラー ビューで、次のプロジェクト構造を確認します。

    - GHCopilotEx10LabApps\
        - ContosoOnlineStore\
            - Benchmarks\
            - Configuration\
            - Exceptions\
            - Services\
            - appsettings.json
            - InventoryManager.cs
            - Orders.cs
            - OrderItem.cs
            - OrderProcessor.cs
            - PERFORMANCE_GUIDE.md
            - Product.cs
            - ProductCatalog.cs
            - Program.cs
            - README.md
        - ContosoOnlineStore.Tests\
            - ContosoOnlineStoreTests.cs
            - Usings.cs
        - DataAnalyzerReporter\
            - data.txt
            - DataAnalyzer.cs
            - FileLoader.cs
            - output.txt
            - Program.cs
            - README.md
            - ReportGenerator.cs

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 クライアントは、レガシ アプリケーションへのパフォーマンス プロファイルの実装について手助けを必要としています。 目標は、可読性と既存の機能を維持しながら、コードのパフォーマンスを向上させることです。 あなたには次のアプリが割り当てられています。

- ContosoOnlineStore:これは、顧客注文を処理する eコマース アプリケーションです。 このアプリケーションには、検索機能を備えた製品カタログ管理、在庫の予約を含む在庫追跡、検証と受理を含む注文処理、メール通知サービス、セキュリティ検証が含まれます。 アプリケーションでは、依存関係の挿入、構造化されたログ、構成管理などの最新の .NET アーキテクチャ パターンが使われていますが、実際のシナリオを反映したパフォーマンスのボトルネックが含まれています。

> **注**:コードのボトルネックには、意図的な非効率性とパフォーマンスの問題、外部依存関係の実際のタイミングを概算するシミュレートされた遅延が含まれます。 シミュレートされた遅延は、"前と後" のパフォーマンスを比較できるように、コードをリファクタリングするときに保持する必要があります。

この演習には、次のタスクが含まれています。

1. ContosoOnlineStore のコードベースを手作業でレビューします。
1. GitHub Copilot Chat (質問モード) を使って、パフォーマンスのボトルネックを特定します。
1. GitHub Copilot Chat (エージェント モード) を使って、パフォーマンスが重要なコードをリファクタリングします。
1. リファクタリング後の ContosoOnlineStore のコードをテストして検証します。

### ContosoOnlineStore のコードベースを手作業でレビューする

どのようなコード リファクタリング作業でも、最初のステップは、プロジェクトの構造やビジネス ロジックなど、既存のコードベースを理解することです。 パフォーマンスの向上に取り組んでいるときは、ベースライン パフォーマンス メトリックを確立することも重要です。

このタスクでは、ContosoOnlineStore プロジェクトの主要なコンポーネントを調べ、アプリケーションを実行してベースライン パフォーマンス メトリックを確立し、最適化の可能性がある領域を特定します。

そのためには、以下の手順を実行してください。

1. ContosoOnlineStore プロジェクトの構造を調べます。これには 1 分かかります。

    コードベースは、関心領域が明確に分離された最新の .NET アーキテクチャ パターンに従っています。 アーキテクチャの主なコンポーネントは次のとおりです。

    - **Configuration**:検証を含む厳密に型指定された構成
    - **[サービス]**: テスト容易性のためのインターフェイスを備えたビジネス サービス  
    - **例外**:カスタム ドメイン固有の例外
    - **ベンチマーク**:BenchmarkDotNet を使用する本格的なパフォーマンス テスト
    - **テスト**:モック フレームワークを使用する単体テスト

1. **ProductCatalog.cs**、**OrderProcessor.cs**、**InventoryManager.cs** の各クラスを確認します。これには数分かかります。

    これらのクラスにはコア ビジネス ロジックが含まれており、パフォーマンス最適化の候補になる可能性があります。

    > **注**:コードベースには、パフォーマンスの問題を特定するのに役立つコメントが含まれています。 意図的に組み込まれた非効率性を明示している "Performance bottleneck" または "Performance issue" とマークされたコメントを探してください。 シミュレートされた遅延は、外部依存関係 (低速クエリ、ネットワーク呼び出しなど) の実際のタイミングの概算にも含まれます。 これらの遅延は、"前と後" のパフォーマンスを比較できるように、コードベースをリファクタリングするときに保持されます。

    - **ProductCatalog.cs**: ProductCatalog クラスは、製品の取得、検索、分類と、検索結果のキャッシュを行うメソッドを提供します。

    - **OrderProcessor.cs**: OrderProcessor クラスは、注文の検証、合計計算、最終処理 (在庫の更新や電子メールによる通知など) を処理します。

    - **InventoryManager.cs**: InventoryManager クラスは、在庫レベル、予約、低在庫アラートを管理します。

1. **Services** フォルダーと **Configuration** フォルダーを展開します。

    これらのフォルダーには、メインのアプリケーション機能をサポートする追加のビジネス ロジックと構成設定が含まれています。

1. **Program.cs** ファイルと **AppSettings.cs** ファイルを確認します。これには数分かかります。

    Program.cs ファイルと AppSettings.cs ファイルの関係を調べます。 Program.cs ファイルにより、AppSettings 構成が初期化され、アプリケーションのサービスに挿入されて、アプリケーションの動作を一元的かつ柔軟に制御できるようなることに注意してください。 アプリケーション構成は、起動時に厳密に型指定され、検証されます。これにより、必要なすべての設定が存在し、正しく書式設定されることが保証されます。

1. **EmailService.cs** ファイルと **SecurityValidationService.cs** ファイルを確認します。これには数分かかります。

    これらのサービスの実装を調べます。 これらのサービスにより、構成可能なタイムアウト、セキュリティ検証ルール、電子メールによる通知のワークフローを含むビジネス ロジックが提供されることに注意してください。 サービスでは、エンタープライズ開発パターンに従って、依存関係の挿入とログが使われています。

1. アプリケーションを実行して、ベースライン パフォーマンスを観察します。

    Visual Studio Code 統合ターミナルでプロジェクト フォルダーに移動し、次の .NET CLI コマンドを実行して、アプリケーションを実行できます。

    ```bash
    dotnet run
    ```

    アプリケーションは、以下を含む包括的なパフォーマンス テストを実行します。

    - タイミング測定を使用した注文処理。
    - 製品カタログの操作 (検索、探索、カテゴリのフィルター処理)。
    - 在庫管理の操作。
    - 同時操作のテスト。
    - 電子メールによる通知のシミュレーション。

1. ベースライン パフォーマンス メトリックを **baseline_metrics.txt** という名前のファイルに格納します。

    エクスプローラー ビューを使用して、Benchmarks フォルダーに baseline_metrics.txt という名前のテキスト ファイルを作成し、コンソール出力を baseline_metrics.txt ファイルにコピーします。

1. baseline_metrics.txt ファイルを確認します。

    "実行パフォーマンス分析" セクションに表示されるタイミング情報に注意してください。** 主要なパフォーマンス メトリックは次のとおりです。

    - 製品検索のパフォーマンス。
    - 検索のパフォーマンス。
    - 注文処理のパフォーマンス。
    - 在庫操作のパフォーマンス。
    - 同時操作のパフォーマンス。

    アプリケーションでは、さまざまな操作をテストして時間計測の詳細を報告する包括的なパフォーマンス分析スイートも実行されます。

1. 少し時間を取って、OrderProcessingBenchmarks.cs ファイルによって提供されるパフォーマンス ベンチマーク機能を調べてください。

    このアプリケーションには、BenchmarkDotNet を使用する本格的なベンチマークが含まれています。 以下を実行して、詳細なパフォーマンス ベンチマークを実行できます。

    ```bash
    dotnet run -c Release -- benchmark
    ```

    このコマンドを実行すると、メモリ割り当てのパターンや統計分析などの詳細なパフォーマンス レポートが生成されます。 必要に応じて、詳細なパフォーマンス ベンチマーク レポートを保存して後で比較することができます。

既存のアーキテクチャとベースライン パフォーマンス メトリックを理解すると、最適化の機会を特定するための準備が整います。

### GitHub Copilot Chat (質問モード) を使って、パフォーマンスのボトルネックを特定する

GitHub Copilot Chat の質問モードは、複雑なコードベースを分析してパフォーマンスのボトルネックを明らかにするための優れたツールです。 質問モードの Copilot は、コード パターンを分析し、効率の悪いアルゴリズムを特定して、ベスト プラクティスに基づく最適化戦略を提案できます。

このタスクでは、GitHub Copilot を使って ContosoOnlineStore アプリケーションを体系的に分析し、パフォーマンス向上の具体的な機会を特定します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot チャット ビューを開き、**質問**モードと **GPT-4o** モデルを構成します。

    チャット ビューを開くには、Visual Studio Code ウィンドウの上部にある **[チャットの切り替え]** アイコンを選択します。

    > **注**:GPT-4o モデルは優れたコード分析機能を提供し、GitHub Copilot Free プランに含まれています。 別のモデルを選択すると、得られる結果が異なる場合があります。

1. エディターで開いているすべてのファイルを閉じます。

    GitHub Copilot は、エディターで開かれているファイルを使ってコンテキストを確立します。 対象のファイルのみを開くと、最適化したいコードに分析を集中できます。

1. **InventoryManager.cs**、**OrderProcessor.cs**、**ProductCatalog.cs** ファイルをチャット コンテキストに追加します。

    ドラッグ アンド ドロップ操作を使って、ソリューション エクスプローラーからチャット コンテキストに **InventoryManager.cs**、**OrderProcessor.cs**、**ProductCatalog.cs** を追加します。

    チャット コンテキストにファイルを追加することで、プロンプトを分析するときにこれらのファイルを含めるよう GitHub Copilot に指示します。これにより、分析の正確さと関連性が向上します。

1. ProductCatalog クラスでパフォーマンスのボトルネックを特定して最適化を提案するよう、GitHub Copilot に指示します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Analyze the ProductCatalog class for performance bottlenecks. Focus on the GetProductById, SearchProducts, and GetProductsByCategory methods. What are the main inefficiencies and how could they be optimized?
    ```

1. ProductCatalog クラスに関して GitHub Copilot によって生成された分析を確認します。

    分析により、次のような問題が特定されます。

    - 特定の条件に対する GetProductById での線形の検索パフォーマンス。
    - SearchProducts での非効率的なキャッシュ キーの生成。
    - GetProductsByCategory でのカテゴリ フィルター処理に最適化されたデータ構造がない。
    - いくつかのメソッドでの意図的な遅延を伴うシーケンシャル処理。

1. 潜在的なリスクに対して提案された最適化を評価するよう GitHub Copilot に指示します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Do any of the suggested optimizations include security risks or introduce other adverse effects?
    ```

    > **重要**:よく考えずにコード リファクタリングの提案を採用すると、セキュリティ リスクやその他の問題が発生する可能性があります。 提案された最適化を評価し、潜在的な問題を特定することが重要です。 たとえば、キャッシュや並列処理を伴う最適化では、スレッド セーフに関する懸念事項やデータ整合性の問題が発生する可能性があります。 AI が提案する最適化によってセキュリティや機能が侵害されないようにするには、手動によるコードのレビューをお勧めします。 セキュリティの定期的なレビューとテストをコード リファクタリング作業に含める必要があります。

1. GitHub Copilot によって生成されたリスク分析を確認します。

    リスク分析では、潜在的なセキュリティの脆弱性、または提案された最適化に関連するその他の問題が強調表示されます。 この情報は、コードをリファクタリングする際に実装する最適化について情報に基づいた意思決定を行うのに役立ちます。

1. OrderProcessor クラスでパフォーマンスの問題を特定して最適化を提案するよう、GitHub Copilot に指示します。

    たとえば、次のプロンプトを送信します。

    ```text
    Examine the OrderProcessor class, particularly the CalculateOrderTotal and FinalizeOrderAsync methods. What performance problems do you see and what optimization strategies would you recommend?
    ```

1. OrderProcessor クラスに関して GitHub Copilot によって生成された分析を確認します。

    分析により、次のような問題が特定されます。

    - ループでの個々の製品の検索 (N+1 クエリ パターン)。
    - 税金と配送の冗長な計算。
    - 注文明細のシーケンシャル処理。
    - 非同期に行われる可能性があるブロック操作。

1. 潜在的なリスクに対して提案された最適化を評価するよう GitHub Copilot に指示します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Do any of the suggested optimizations include security risks or introduce other adverse effects?
    ```

1. GitHub Copilot によって生成されたリスク分析を確認します。

1. InventoryManager クラスでパフォーマンスの問題を特定して最適化を提案するよう、GitHub Copilot に指示します。

    たとえば、次のプロンプトを使って在庫の操作を調べます。

    ```text
    Review the InventoryManager class, especially the GetLowStockProducts and UpdateStockLevels methods. What are the performance concerns and how could the inventory operations be improved?
    ```

1. InventoryManager クラスに関して GitHub Copilot によって生成された分析を確認します。

    分析により、次のような問題が特定されます。

    - ループでの個々のデータベース クエリ シミュレーション。
    - ブロック操作を伴う非効率的なログの実装。
    - バッチ操作のサポートがない。
    - 在庫レベル チェックでの不要なスレッド遅延。

1. 潜在的なリスクに対して提案された最適化を評価するよう GitHub Copilot に指示します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Do any of the suggested optimizations include security risks or introduce other adverse effects?
    ```

1. GitHub Copilot によって生成されたリスク分析を確認します。

1. EmailService クラスでパフォーマンスの問題を特定して最適化を提案するよう、GitHub Copilot に指示します。

    たとえば、次のプロンプトを送信してメール サービスを分析します。

    ```text
    Analyze the EmailService class for performance issues. How does the email sending process impact overall application performance and what improvements could be made?
    ```

1. EmailService クラスに関して GitHub Copilot によって生成された分析を確認します。

    分析により、次のような問題が特定されます。

    - ブロック操作を伴うシーケンシャルなメールの内容の生成。
    - メール テンプレート内での個々の製品の検索。
    - 同期検証操作。
    - 複数の受信者に対する並列化の機会がない。

1. 潜在的なリスクに対して提案された最適化を評価するよう GitHub Copilot に指示します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Do any of the suggested optimizations include security risks or introduce other adverse effects?
    ```

1. GitHub Copilot によって生成されたリスク分析を確認します。

GitHub Copilot の分析機能を使用して、ContosoOnlineStore アプリケーションのパフォーマンスのボトルネックを特定しました。 この分析により、アルゴリズムの改善、キャッシュ戦略、非同期処理パターンに焦点を当てた最適化作業のロードマップが提供されます。 AI が提案するコード最適化を分析すると、潜在的なパフォーマンスの向上に関連するリスクの特定に役立ちます。 手動によるコードのレビュー、セキュリティのレビュー、テストをコード リファクタリング作業に含める必要があります。

### GitHub Copilot Chat (エージェント モード) を使って、パフォーマンスが重要なコードをリファクタリングする

GitHub Copilot のエージェント モードでは、プログラミング タスクを支援する自律エージェントが提供されます。 開発者は、高度なタスクをエージェントに割り当てた後、エージェント コード編集セッションを開始してタスクを完了します。 GitHub Copilot エージェントは、必要な作業を自律的に評価し、関連するファイルとコンテキストを決定して、タスクの完了方法を計画します。 エージェントにより、コードの変更、テストの実行、さらにはアプリケーションのデプロイまで行うことができます。

エージェント モードの GitHub Copilot は、最適化されたコードの実装を生成し、アーキテクチャの改善を提案して、パフォーマンスの強化の実装を支援できます。

このタスクでは、GitHub Copilot のエージェント モードを使って、前のタスクで明らかになったパフォーマンスのボトルネックに体系的に対処します。

そのためには、以下の手順を実行してください。

1. エージェント モード用に GitHub Copilot Chat を構成します。

    チャット ビューで、モードを **[質問]** から **[エージェント]** に変更します エージェント モードでは、対象がより限定されたコード生成と変更の機能が提供されます。

1. **ProductCatalog.cs** ファイルを開き、**GetProductById** メソッドを選択します。

1. GetProductById メソッドを最適化するタスクをエージェントに割り当てます。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Review the current chat session. Optimize the GetProductById method to improve performance. Consider using a dictionary lookup instead of linear search and implement proper caching mechanisms. Retain any existing artificial/simulated delays for "before and after" performance comparisons. Ensure that the refactored code doesn't introduce security vulnerabilities or other issues.
    ```

1. 1 分をかけて、GitHub Copilot によって提案された編集内容を確認した後、変更を受け入れます。

    最適化されたバージョンには次のものが含まれます。

    - O(1) のパフォーマンスに関するディクショナリ ベースの製品検索。
    - キャッシュの適切な初期化と管理。
    - 冗長な操作の削減。

    コード エディターで個々の編集内容を確認して受け入れる (または拒否する) か、チャット ビューで **[Keep]** を選択して一度にすべての変更を受け入れることができます。

    > **注**:演習のこのセクションを完了したら、前のタスクで特定されたセキュリティの脆弱性とその他の問題について検討してください。 開発者は、最適化プロセス中に新しい脆弱性が導入されないようにする必要があります。 運用環境では、手動によるコードレビュー、セキュリティのレビュー、テストをプロセスに含める必要があります。

1. コード エディターで、**SearchProducts** メソッドを選択します。

1. SearchProducts メソッドの効率を高めるタスクをエージェントに割り当てます。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Review the current chat session. Refactor the SearchProducts method to eliminate performance bottlenecks. Optimize the search algorithm while maintaining search functionality. Retain any existing artificial/simulated delays for "before and after" performance comparisons. Ensure that the refactored code doesn't introduce security vulnerabilities or other issues.
    ```

1. 1 分をかけて、GitHub Copilot によって提案された編集内容を確認した後、変更を受け入れます。

    最適化されたバージョンには次のものが含まれます。

    - 効率的な文字列照合アルゴリズム。
    - 複数の検索条件の並列処理。
    - 最適化されたキャッシュ キーの生成。

1. **ProductCatalog.cs** ファイルを保存して閉じます。

1. **OrderProcessor.cs** ファイルを開き、**CalculateOrderTotal** メソッドを選択します。

1. CalculateOrderTotal メソッドのパフォーマンスを向上するタスクをエージェントに割り当てます。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Review the current chat session. Optimize the CalculateOrderTotal method to reduce redundant product lookups and improve calculation performance. Consider batch operations and caching strategies. Retain any existing artificial/simulated delays for "before and after" performance comparisons. Ensure that the refactored code doesn't introduce security vulnerabilities or other issues.
    ```

1. 1 分をかけて、GitHub Copilot によって提案された編集内容を確認した後、変更を受け入れます。

    最適化されたバージョンには次のものが含まれます。

    - N+1 クエリ パターンを使わない製品のバッチ検索。
    - 注文処理中の製品情報のキャッシュ。
    - 税金と配送の計算の最適化。

1. コード エディターで、**FinalizeOrderAsync** メソッドを選択します。

1. FinalizeOrderAsync メソッドのパフォーマンスを向上するタスクをエージェントに割り当てます。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Review the current chat session. Refactor the FinalizeOrderAsync method to improve async performance. Focus on parallel processing where possible and optimizing await patterns. Retain any existing artificial/simulated delays for "before and after" performance comparisons. Ensure that the refactored code doesn't introduce security vulnerabilities or other issues.
    ```

1. 1 分をかけて、GitHub Copilot によって提案された編集内容を確認した後、変更を受け入れます。

    最適化されたバージョンには次のものが含まれます。

    - 独立した操作の並列処理
    - 最適化された非同期と待機の使用
    - 非同期コンテキストでのより良い例外処理

1. **OrderProcessor.cs** ファイルを保存して閉じます。

1. **InventoryManager.cs** ファイルを開き、**UpdateStockLevels** メソッドを選択します。

1. UpdateStockLevels メソッドのパフォーマンスを向上するタスクをエージェントに割り当てます。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Review the current chat session. Optimize the UpdateStockLevels method to support batch operations and reduce individual update overhead. Implement efficient logging, but retain any existing artificial delays for performance comparison. Ensure that the refactored code doesn't introduce security vulnerabilities or other issues.
    ```

1. 1 分をかけて、GitHub Copilot によって提案された編集内容を確認した後、変更を受け入れます。

    最適化されたバージョンには次のものが含まれます。

    - 在庫レベルのバッチ更新
    - 効率的なログ戦略
    - ブロック操作の削減

1. **OrderProcessor.cs** ファイルを保存して閉じます。

1. **EmailService.cs** ファイルを開きます。

1. メール送信方法のパフォーマンスを向上するタスクをエージェントに割り当てます。

1. EmailService の非同期処理を改善します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Review the current chat session. Optimize the email service methods to support parallel email processing and improve async performance. Consider implementing email queuing and batch operations. Retain any existing artificial/simulated delays for "before and after" performance comparisons. Ensure that the refactored code doesn't introduce security vulnerabilities or other issues.
    ```

1. 1 分をかけて、GitHub Copilot によって提案された編集内容を確認した後、変更を受け入れます。

    最適化されたバージョンには次のものが含まれます。

    - メールの内容の並列生成
    - メールの非同期送信操作
    - エラー処理と再試行ロジックの改善

このリファクタリング プロセス全体を通じて、GitHub Copilot のエージェント モードはユーザーのコラボレーション パートナーとして機能し、コードの改善と最適化に関する具体的な戦略を提供します。 大事なのは、各提案を慎重に検討し、特定の要件とコーディング標準に合わせて調整することです。

### リファクタリング後の ContosoOnlineStore のコードをテストして検証する

パフォーマンスの最適化を実装した後は、変更によって機能が正しく維持されながら、パフォーマンスが向上することを検証することが重要です。 このタスクでは、包括的なテストとパフォーマンスの測定に焦点を当てます。

そのためには、以下の手順を実行してください。

1. リファクタリングされたアプリケーションをビルドしてテストします。

    Visual Studio Code ターミナルで次のコマンドを実行して、アプリケーションが正常にビルドされることを確認します。

    ```bash
    dotnet build
    ```

    リファクタリング プロセスの間に発生した可能性があるコンパイル エラーに対処します。

1. パフォーマンス テスト スイートを実行します。

    Visual Studio Code ターミナルで次のコマンドを実行して、リファクタリングされたコードのパフォーマンス メトリックを生成します。

    ```bash
    dotnet run
    ```

1. 新しいパフォーマンス メトリックを **optimized_metrics.txt** という名前のファイルに保存します。

    エクスプローラー ビューを使用して、Benchmarks フォルダーに optimized_metrics.txt という名前のテキスト ファイルを作成し、コンソール出力を optimized_metrics.txt ファイルにコピーします。

1. 最適化されたパフォーマンス メトリックと最初のタスクで得られたベースライン測定値を手動で比較します。これには 1 分かかります。

    次のパフォーマンスの向上が見られるはずです。

    - 製品検索操作の速度の大幅な向上。
    - 検索パフォーマンスの向上。
    - 注文処理時間の短縮。
    - 同時操作のパフォーマンスの向上。

    リファクタリングされたコードの機能の正確性を確認します。

    - 注文の合計が正しく計算されることを確認する。
    - 在庫レベルが正しく更新されることを確認する。
    - 電子メールによる通知が正常に送信されることを確認する。
    - セキュリティ検証が引き続き機能することを確認する。

1. 単体テスト プロジェクトをビルドして実行します。

    たとえば、Visual Studio Code ターミナルで、**ContosoOnlineStore.Tests** フォルダーに移動し、次のコマンドを実行します。

    ```bash
    dotnet test
    ```

    すべてのテストが合格し、リファクタリング後のコードが期待される動作を維持していることを確認する必要があります。

    出力は次のようになります。

    ```plaintext
    Restore complete (0.6s)
      ContosoOnlineStore succeeded (0.6s) → C:\Users\cahowd\Desktop\GHCopilotEx10LabApps\ContosoOnlineStore\bin\Debug\net9.0\ContosoOnlineStore.dll
      ContosoOnlineStore.Tests succeeded (0.2s) → bin\Debug\net9.0\ContosoOnlineStore.Tests.dll
    [xUnit.net 00:00:00.00] xUnit.net VSTest Adapter v2.4.5+1caef2f33e (64-bit .NET 9.0.9)
    [xUnit.net 00:00:02.94]   Discovering: ContosoOnlineStore.Tests
    [xUnit.net 00:00:03.02]   Discovered:  ContosoOnlineStore.Tests
    [xUnit.net 00:00:03.02]   Starting:    ContosoOnlineStore.Tests
    [xUnit.net 00:00:03.18]   Finished:    ContosoOnlineStore.Tests
      ContosoOnlineStore.Tests test succeeded (4.7s)
    
    Test summary: total: 16, failed: 0, succeeded: 16, skipped: 0, duration: 4.7s
    Build succeeded in 7.0s
    ```

1. パフォーマンスの向上の分析を支援するように GitHub Copilot に指示します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```text
    Compare the baseline_metrics.txt and optimized_metrics.txt files. Summarize the performance improvements achieved through the optimizations. Review the codebase and calculate the time associated with simulated delays for each performance test. Subtract the time associated with simulated delays from the performance data and summarize the impact of code optimizations.
    ```

1. GitHub Copilot によって生成された (パフォーマンスの向上) 分析を確認します。これには 1 分かかります。

    この分析では、シミュレートされた遅延の影響を除いて、最適化によって達成された特定のパフォーマンスの向上が強調表示されます。 これにより、コード変更の効果をより明確に把握できます。

1. 省略可能 - この演習の開始時に詳細なパフォーマンス ベンチマーク スイートを実行して結果を保存した場合は、詳細なパフォーマンス ベンチマークをもう一度実行し、GitHub Copilot に結果を比較させることができます。

    詳細なパフォーマンス ベンチマークを実行するには、Visual Studio Code ターミナルで次のコマンドを実行します。

    ```bash
    dotnet run -c Release -- benchmark
    ```

    BenchmarkDotNet レポートの確認を支援するように GitHub Copilot に指示します。

テストと検証のプロセスにより、パフォーマンス最適化作業が成功したことが確認されます。 これで、ContosoOnlineStore アプリケーションは、その機能要件とアーキテクチャの整合性を維持しながら、向上したパフォーマンスを示すようになりました。

## まとめ

この演習では、GitHub Copilot を使って、複雑な eコマース アプリケーションのパフォーマンスのボトルネックの特定と解決に成功しました。 主な成果は次のとおりです。

- **パフォーマンス分析**:GitHub Copilot の質問モードを使ってコードを体系的に分析し、ボトルネックを明らかにしました
- **戦略的最適化**:N+1 クエリ パターン、非効率的なアルゴリズム、ブロック操作への対処に対象を絞って最適化を適用しました
- **コラボレーション リファクタリング**:GitHub Copilot のエージェント モードを活用してパフォーマンスの改善を実装しました
- **検証**:包括的なテストにより、パフォーマンスの向上と機能の正しさの両方を確認しました

最適化された ContosoOnlineStore では、コードの品質とアーキテクチャのベスト プラクティスを維持しながら、パフォーマンスが大幅に向上しています。 このアプローチは、AI 搭載の開発ツールを使うと、パフォーマンス最適化作業をいっそう速く行うことができ、開発者が複雑なアプリケーションに対してデータ駆動型の改善を行うのに役立つことを示しています。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認してください。 変更を行った場合は、必要に応じてそれを元に戻します。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したサンプル プロジェクト フォルダーをアーカイブまたは削除できます。
