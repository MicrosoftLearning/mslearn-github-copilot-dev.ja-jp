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

- コマンド ターミナルを開き、次のコマンドを実行します。

    Visual Studio Code が正しいバージョンの .NET を使用するように構成されていることを確認するには、次のコマンドを実行します。

    ```bash
    dotnet nuget add source https://api.nuget.org/v3/index.json -n nuget.org
    ```

    Git が自分の名前とメール アドレスを使用するように構成されていることを確認するには、次のコマンドを自分の情報で更新してから、コマンドを実行します。

    ```bash
    git config --global user.name "John Doe"
    ```

    ```bash
    git config --global user.email johndoe@example.com
    ```

### サンプル コード プロジェクトをダウンロードする

次の手順のようにして、サンプル プロジェクトをダウンロードし、Visual Studio Code でそれを開きます。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. サンプル アプリ プロジェクトを含む ZIP ファイルをダウンロードするには、ブラウザーで次の URL を開きます: [GitHub Copilot ラボ - パフォーマンス プロファイルを実装する](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHCopilotEx10LabApps.zip)

    ZIP ファイルの名前は **GHCopilotEx10LabApps.zip** です。

1. **GHCopilotEx10LabApps.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. *GHCopilotEx10LabApps.zip* を右クリックして、**[すべて展開]** を選びます。

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

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 クライアントは、レガシ アプリケーションへのパフォーマンス プロファイルの実装について手助けを必要としています。 目標は、可読性と既存の機能を維持しながら、コードのパフォーマンスを向上させることです。 あなたには次のアプリが割り当てられています。

- ContosoOnlineStore:これは、現実的なビジネスの複雑さを伴う顧客注文を処理する eコマース アプリケーションです。 このアプリケーションには、検索機能を備えた製品カタログ管理、在庫の予約を含む在庫追跡、検証と受理を含む注文処理、メール通知サービス、セキュリティ検証が含まれます。 アプリケーションでは、依存関係の挿入、構造化されたログ、構成管理などの最新の .NET アーキテクチャ パターンが使われていますが、実際のシナリオを反映したパフォーマンスのボトルネックが含まれています。

この演習には、次のタスクが含まれています。

1. ContosoOnlineStore のコードベースを手作業でレビューします。
1. GitHub Copilot Chat (質問モード) を使って、パフォーマンスのボトルネックを特定します。
1. GitHub Copilot Chat (エージェント モード) を使って、パフォーマンスが重要なコードをリファクタリングします。
1. リファクタリング後の ContosoOnlineStore のコードをテストして検証します。

### ContosoOnlineStore のコードベースを手作業でレビューする

どのようなコード リファクタリング作業でも、最初のステップは、プロジェクトの構造やビジネス ロジックなど、既存のコードベースを理解することです。 パフォーマンスの向上に取り組んでいるときは、ベースライン パフォーマンス メトリックを確立することも重要です。

このタスクでは、ContosoOnlineStore プロジェクトの主要なコンポーネントを調べ、アプリケーションを実行してベースライン パフォーマンス メトリックを確立し、最適化の可能性がある領域を特定します。

そのためには、以下の手順を実行してください。

1. 少し時間を取って、ContosoOnlineStore プロジェクトの構造を確認してください。

    コードベースは、関心領域が明確に分離された最新の .NET アーキテクチャ パターンに従っています。 アーキテクチャの主なコンポーネントは次のとおりです。

    - **Configuration**:検証を含む厳密に型指定された構成
    - **[サービス]**: テスト容易性のためのインターフェイスを備えたビジネス サービス  
    - **例外**:カスタム ドメイン固有の例外
    - **ベンチマーク**:BenchmarkDotNet を使用する本格的なパフォーマンス テスト
    - **テスト**:モック フレームワークを使用する単体テスト

1. 主要なビジネス ロジック クラスを調べます。

    **ProductCatalog.cs**、**OrderProcessor.cs**、**InventoryManager.cs** を開きます。 これらのクラスにはコア ビジネス ロジックが含まれており、パフォーマンス最適化の候補になる可能性があります。

    製品データ リスト (カテゴリと説明を含む 20 製品)、複雑な注文処理ワークフロー、在庫予約を含む在庫管理に注目してください。

    > **注**:コードベースには、パフォーマンスの問題を特定するのに役立つコメントが含まれています。 意図的に組み込まれた非効率性を明示している "Performance bottleneck" または "Performance issue" とマークされたコメントを探してください。

1. サービス レイヤーと構成を確認します。

    **Services** フォルダーに移動して、**EmailService.cs** と **SecurityValidationService.cs** を調べます。 **Configuration/AppSettings.cs** ファイルも確認します。

    これらのサービスでは、構成可能なタイムアウト、セキュリティ検証ルール、メール通知ワークフローを含む、現実的なビジネス ロジックが実装されていることがわかります。 サービスでは、エンタープライズ開発パターンに従って、依存関係の挿入とログが使われています。

1. アプリケーションを実行して、ベースライン パフォーマンスを観察します。

    Visual Studio Code 統合ターミナルでプロジェクト フォルダーに移動し、次の .NET CLI コマンドを実行して、アプリケーションを実行できます。

    ```bash
    dotnet run
    ```

    アプリケーションは、以下を含む包括的なパフォーマンス テストを実行します。

    - 時間測定を含む注文処理
    - 製品カタログの操作 (検索、探索、カテゴリのフィルター処理)
    - 在庫管理の操作
    - 同時実行操作のテスト
    - メール通知のシミュレーション

1. ベースライン パフォーマンス メトリックを **baseline_metrics.txt** という名前のファイルに格納します。

    baseline_metrics.txt という名前のテキスト ファイルを作成します。 コンソール出力を baseline_metrics.txt ファイルにコピーします。

    コンソール出力に表示される次のような時間計測情報に注意してください。

    - 注文処理時間 (通常、1 回の注文で 2000 - 3000 ミリ秒)
    - 製品検索のパフォーマンス (個々の操作の時間計測)
    - 検索操作の所要時間
    - 在庫チェックの時間計測
    - 同時実行操作のパフォーマンス

    アプリケーションでは、さまざまな操作をテストして時間計測の詳細を報告する包括的なパフォーマンス分析スイートも実行されます。

1. 少し時間を取って、OrderProcessingBenchmarks.cs ファイルによって提供されるパフォーマンス ベンチマーク機能を調べてください。

    このアプリケーションには、BenchmarkDotNet を使用する本格的なベンチマークが含まれています。 以下を実行して、詳細なパフォーマンス ベンチマークを実行できます。

    ```bash
    dotnet run -c Release -- benchmark
    ```

    これにより、メモリ割り当てパターンと統計分析を含む詳細なパフォーマンス レポートが生成されます。

既存のアーキテクチャとベースライン パフォーマンス メトリックを理解すると、最適化の機会を特定するための基礎が得られます。 このアプリケーションでは、後続のタスクで対処する現実的な eコマース パフォーマンスの課題が示されています。

### GitHub Copilot Chat (質問モード) を使って、パフォーマンスのボトルネックを特定する

GitHub Copilot Chat の質問モードは、複雑なコードベースを分析してパフォーマンスのボトルネックを明らかにするための優れたツールです。 質問モードの Copilot は、コード パターンを分析し、効率の悪いアルゴリズムを特定して、ベスト プラクティスに基づく最適化戦略を提案できます。

このタスクでは、GitHub Copilot を使って ContosoOnlineStore アプリケーションを体系的に分析し、パフォーマンス向上の具体的な機会を特定します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot チャット ビューを開き、質問モードと GPT-4o モデルを構成します。

    チャット ビューをまだ開いていない場合は、Visual Studio Code ウィンドウの上部にある **[チャット]** アイコンを選びます。 チャット モードが **[質問]** に設定されていて、**GPT-4o** モデルを使っていることを確認します。

    > **注**:GPT-4o モデルは優れたコード分析機能を備えており、このパフォーマンス分析タスクに推奨されます。

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

    GitHub Copilot の分析を確認します。次のような問題が示されているはずです。

    - 特定の条件に対する GetProductById での線形の検索パフォーマンス。
    - SearchProducts での非効率的なキャッシュ キーの生成。
    - GetProductsByCategory でのカテゴリ フィルター処理に最適化されたデータ構造がない。
    - いくつかのメソッドでの意図的な遅延を伴うシーケンシャル処理。

1. OrderProcessor クラスでパフォーマンスの問題を特定して最適化を提案するよう、GitHub Copilot に指示します。

    たとえば、次のプロンプトを送信します。

    ```text
    Examine the OrderProcessor class, particularly the CalculateOrderTotal and FinalizeOrderAsync methods. What performance problems do you see and what optimization strategies would you recommend?
    ```

    GitHub Copilot によって次のような問題が特定されるはずです。

    - ループでの個々の製品の検索 (N+1 クエリ パターン)。
    - 税金と配送の冗長な計算。
    - 注文明細のシーケンシャル処理。
    - 非同期に行われる可能性があるブロック操作。

1. InventoryManager クラスでパフォーマンスの問題を特定して最適化を提案するよう、GitHub Copilot に指示します。

    たとえば、次のプロンプトを使って在庫の操作を調べます。

    ```text
    Review the InventoryManager class, especially the GetLowStockProducts and UpdateStockLevels methods. What are the performance concerns and how could the inventory operations be improved?
    ```

    分析によって次のことが明らかになるはずです。

    - ループでの個々のデータベース クエリ シミュレーション。
    - ブロック操作を伴う非効率的なログの実装。
    - バッチ操作のサポートがない。
    - 在庫レベル チェックでの不要なスレッド遅延。

1. EmailService クラスでパフォーマンスの問題を特定して最適化を提案するよう、GitHub Copilot に指示します。

    たとえば、次のプロンプトを送信してメール サービスを分析します。

    ```text
    Analyze the EmailService class for performance issues. How does the email sending process impact overall application performance and what improvements could be made?
    ```

    GitHub Copilot によって次のことが明らかになるはずです。

    - ブロック操作を伴うシーケンシャルなメールの内容の生成。
    - メール テンプレート内での個々の製品の検索。
    - 同期検証操作。
    - 複数の受信者に対する並列化の機会がない。

GitHub Copilot の分析機能を使って、ContosoOnlineStore アプリケーションの主なパフォーマンスのボトルネックを特定しました。 この分析により、アルゴリズムの改善、キャッシュ戦略、非同期処理パターンに焦点を当てた最適化作業のロードマップが提供されます。

### GitHub Copilot Chat (エージェント モード) を使って、パフォーマンスが重要なコードをリファクタリングする

GitHub Copilot のエージェント モードでは、プログラミング タスクを支援する自律エージェントが提供されます。 開発者は、上位レベルのタスクを割り当ててから、エージェント型のコード編集セッションを開始してタスクを実行します。 エージェント モードでは、Copilot は必要な作業を自律的に計画し、関連するファイルとコンテキストを決定します。 エージェントはコードの変更、テストの実行、さらにはアプリケーションのデプロイまで行うことができます。

エージェント モードの GitHub Copilot は、最適化されたコードの実装を生成し、アーキテクチャの改善を提案して、パフォーマンスの強化の実装を支援できます。

このタスクでは、GitHub Copilot のエージェント モードを使って、前のタスクで明らかになったパフォーマンスのボトルネックに体系的に対処します。

そのためには、以下の手順を実行してください。

1. エージェント モード用に GitHub Copilot Chat を構成します。

    チャット ビューで、モードを **[質問]** から **[エージェント]** に変更します エージェント モードでは、対象がより限定されたコード生成と変更の機能が提供されます。

1. ProductCatalog クラスの GetProductById メソッドを最適化するタスクをエージェントに割り当てます。

    **ProductCatalog.cs** を開いて **GetProductById** メソッドを選びます。 チャットで次のプロンプトを使います。

    ```text
    Optimize this GetProductById method to improve performance. Consider using a dictionary lookup instead of linear search and implement proper caching mechanisms.
    ```

    GitHub Copilot が提案する改善点を確認し、変更を実装します。 最適化されたバージョンには次のものが含まれます。

    - O(1) のパフォーマンスに関するディクショナリ ベースの製品検索。
    - キャッシュの適切な初期化と管理。
    - 冗長な操作の削減。

1. SearchProducts メソッドの効率を高めるタスクをエージェントに割り当てます。

    **ProductCatalog.cs** で **SearchProducts** メソッドを選んで、次のプロンプトを使います。

    ```text
    Refactor the SearchProducts method to eliminate performance bottlenecks. Optimize the search algorithm and remove unnecessary delays while maintaining search functionality.
    ```

    GitHub Copilot の提案を適用して実装します。

    - 効率的な文字列照合アルゴリズム。
    - 複数の検索条件の並列処理。
    - 最適化されたキャッシュ キーの生成。

1. OrderProcessor クラスの CalculateOrderTotal メソッドのパフォーマンスを向上させるタスクをエージェントに割り当てます。

    **OrderProcessor.cs** を開いて **CalculateOrderTotal** メソッドを選びます。 次のプロンプトを送信します。

    ```text
    Optimize the CalculateOrderTotal method to reduce redundant product lookups and improve calculation performance. Consider batch operations and caching strategies.
    ```

    提案された改善点を実装します。これには次のものが含まれます。

    - N+1 クエリ パターンを使わない製品のバッチ検索。
    - 注文処理中の製品情報のキャッシュ。
    - 税金と配送の計算の最適化。

1. FinalizeOrderAsync メソッドを最適化します。

    **OrderProcessor.cs** で **FinalizeOrderAsync** メソッドを選んで、次のプロンプトを使います。

    ```text
    Refactor the FinalizeOrderAsync method to improve async performance. Focus on parallel processing where possible and optimizing await patterns.
    ```

    提案された変更を適用して、以下を実現します。

    - 独立した操作の並列処理
    - 最適化された非同期と待機の使用
    - 非同期コンテキストでのより良い例外処理

1. InventoryManager バッチ操作を強化します。

    **InventoryManager.cs** を開いて **UpdateStockLevels** メソッドを選びます。 次のプロンプトを使います。

    ```text
    Optimize the UpdateStockLevels method to support batch operations and reduce individual update overhead. Implement efficient logging and remove unnecessary delays.
    ```

    次のような改善を実装します。

    - 在庫レベルのバッチ更新
    - 効率的なログ戦略
    - ブロック操作の削減

1. EmailService の非同期処理を改善します。

    **Services/EmailService.cs** を開いて、メール送信メソッドを選びます。 次のプロンプトを送信します。

    ```text
    Optimize the email service to support parallel email processing and improve async performance. Consider implementing email queuing and batch operations.
    ```

    以下に関する最適化の提案を適用します。

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

    アプリケーションを実行して、パフォーマンスの向上を測定します。

    ```bash
    dotnet run
    ```

    新しいパフォーマンス メトリックと、最初のタスクで得られたベースライン測定値を比較します。 次のことがわかるはずです。

    - 注文処理時間の大幅な短縮 (2000 - 3000 ミリ秒から 500 ミリ秒未満)
    - 製品検索操作の高速化
    - 検索パフォーマンスの向上
    - 在庫管理の応答時間の向上

1. 包括的なベンチマーク スイートを実行します。

    詳細なパフォーマンス ベンチマークを実行して、正確な測定値を取得します。

    ```bash
    dotnet run -c Release -- benchmark
    ```

    BenchmarkDotNet のレポートを確認します。次のような詳細な統計情報が提供されます。

    - 信頼区間での平均実行時間
    - メモリ割り当てパターン
    - スループット測定値
    - 改善の統計的有意性

1. 機能の正しさを検証します。

    最適化によって機能の回帰が発生していないことを確認します。

    - 注文の合計が正しく計算されることを確認する
    - 在庫レベルが正しく更新されることを確認する
    - メール通知が正常に送信されることをテストする
    - セキュリティ検証が引き続き機能することを検証する

1. 単体テスト スイートを実行します。

    既存の単体テストを実行して、コードの正しさを確認します。

    ```bash
    dotnet test
    ```

    すべてのテストが合格し、リファクタリング後のコードが期待される動作を維持していることを確認する必要があります。

1. パフォーマンスの向上を文書化します。

    GitHub Copilot を使って、変更の内容を文書化します。 チャットで次のプロンプトを送信します。

    ```text
    Help me create a summary of the performance optimizations implemented in this ContosoOnlineStore project. Include before/after metrics and the main optimization strategies used.
    ```

    次のものを含むパフォーマンス向上レポートを作成します。

    - ベースラインと最適化後のパフォーマンス メトリック
    - 適用された主要な最適化手法
    - 最も重要な改善点
    - 今後の機能強化のための推奨事項

テストと検証のプロセスにより、パフォーマンス最適化作業が成功したことが確認されます。 ContosoOnlineStore アプリケーションは、その機能要件とアーキテクチャの整合性を維持しながら、大幅に向上したパフォーマンスを示すようになりました。

この演習では、GitHub Copilot をパフォーマンス分析と最適化のための強力なツールとして使う方法を学び、複雑なパフォーマンスの課題に対処する際の AI 支援開発の価値を見てきました。

## まとめ

この演習では、GitHub Copilot を使って、複雑な eコマース アプリケーションのパフォーマンスのボトルネックの特定と解決に成功しました。 主な成果は次のとおりです。

- **パフォーマンス分析**:GitHub Copilot の質問モードを使ってコードを体系的に分析し、ボトルネックを明らかにしました
- **戦略的最適化**:N+1 クエリ パターン、非効率的なアルゴリズム、ブロック操作への対処に対象を絞って最適化を適用しました
- **コラボレーション リファクタリング**:GitHub Copilot のエージェント モードを活用してパフォーマンスの改善を実装しました
- **検証**:包括的なテストにより、パフォーマンスの向上と機能の正しさの両方を確認しました

最適化された ContosoOnlineStore では、コードの品質とアーキテクチャのベスト プラクティスを維持しながら、パフォーマンスが大幅に向上しています。 このアプローチは、AI 搭載の開発ツールを使うと、パフォーマンス最適化作業をいっそう速く行うことができ、開発者が複雑なアプリケーションに対してデータ駆動型の改善を行うのに役立つことを示しています。
