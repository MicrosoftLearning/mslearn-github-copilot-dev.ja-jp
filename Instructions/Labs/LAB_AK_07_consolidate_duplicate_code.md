---
lab:
  title: 演習 - GitHub Copilot を使用して重複するコードを統合する
  description: GitHub Copilot のツールを使って、複雑なコードベースを分析し、重複するコード ロジックを統合する方法を学びます。
---

# GitHub Copilot を使用して重複するコードを統合する

似た機能や関連する機能を含むコードベースを開発または拡張するとき、コード ロジックが重複することがよくあります。 それは意図したことではない場合があり、コードを再利用して新しい機能のプロトタイプを作成するように簡単に発生する可能性があります。 時間が経ち、重複したロジックが周囲のコードに合わせて進化すると、問題がより複雑になる可能性があります。 変数名、関数名、コード構造がある場所では変更され、他の場所では変更されないと、重複したロジックがわからなくなる可能性があります。 厳しいスケジュール、稚拙なドキュメント、適切なコード レビューの欠如により、問題が悪化する可能性があります。 最終的に、重複したロジックにより、コードの理解、保守、デバッグ、テストが困難になります。

この演習では、重複したコード ロジックを含む既存のプロジェクトをレビューし、統合のためのオプションを分析し、重複したコード ロジックを統合し、リファクタリングされたコードをテストして意図したとおりに動作することを確認します。 質問モードで GitHub Copilot を使い、既存のコード プロジェクトを理解して、ロジックを統合するためのオプションを調べます。 エージェント モードで GitHub Copilot を使い、重複ロジックを共有ヘルパー メソッドにまとめてコードをリファクタリングします。 元のコードとリファクタリングされたコードをテストして、統合されたロジックが意図したとおりに動作することを確認します。

この演習の所要時間は約 **30** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です: Git 2.48 以降、.NET SDK 9.0 以降、C# 開発キット拡張機能を備えた Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

### ラボ環境を構成する

この演習のラボ環境として、ローカル PC をお使いの場合:

- ローカル PC をラボ環境として構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320147" target="_blank">ラボ環境のリソースを構成する</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習に、ホストされたラボ環境をお使いの場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、次の URL をブラウザーのサイト ナビゲーション バーに貼り付けてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

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

1. サンプル アプリ プロジェクトを含む ZIP ファイルをダウンロードするには、ブラウザーで次の URL を開きます: [GitHub Copilot ラボ - 重複するコードを統合する](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHCopilotEx7LabApps.zip)

    ZIP ファイルの名前は **GHCopilotEx7LabApps.zip** です。

1. **GHCopilotEx7LabApps.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. *GHCopilotEx7LabApps.zip* を右クリックして、**[すべて展開]** を選びます。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. **GHCopilotEx7LabApps** フォルダーを、Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で **GHCopilotEx7LabApps** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**GHCopilotEx7LabApps** を選んでから、**[フォルダーの選択]** を選びます。

1. Visual Studio Code のソリューション エクスプローラー ビューで、次のプロジェクト構造を確認します。

    - GHCopilotEx7LabApps\
        - ECommerceOrderAndReturn\
            - Dependencies\
            - Configuration\
                - AppConfig.cs
            - Models\
                - Order.cs
                - Return.cs
            - Security\
                - SecurityValidator.cs
            - Services\
                - AuditService.cs
                - EmailService.cs
                - InventoryService.cs
            - EXPECTED_OUTPUT.md
            - OrderProcessor.cs
            - Program.cs
            - README.md
            - ReturnProcessor.cs

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 クライアントは、重複コード ロジックの統合について手助けを必要としています。 目標は、既存の機能を維持しながら、コードの保守性を向上させることです。 あなたには次のアプリが割り当てられています。

- E-CommerceOrdersAndReturns:これは、顧客の注文を処理して返品を取り扱う eコマース アプリです。 これには、注文と返品の検証、配送コストの計算、メール通知の送信、監査アクティビティのログ、在庫レベルの管理に関する主要なビジネス ロジックが含まれます。

この演習には、次のタスクが含まれています。

1. eコマースの注文と返品のコードベースを手作業で確認します。
1. GitHub Copilot Chat (質問モード) を使って重複コードを識別します。
1. GitHub Copilot Chat (エージェント モード) を使って重複ロジックを統合します。
1. リファクタリングされた eコマースの注文と返品のコードをテストします。

### eコマースの注文と返品のコードベースを手作業で確認する

あらゆるリファクタリング作業の最初のステップは、既存のコードベースを確実に理解することです。 コードの構造、ビジネス ロジック、コードの実行時に生成される結果を理解することが重要です。

このタスクでは、eコマースの注文と返品処理プロジェクトの主要なコンポーネントを確認し、コードで重複するコード パターンをスキャンして、コードをテストします。

そのためには、以下の手順を実行してください。

1. 少し時間を取って、ECommerceOrderAndReturn プロジェクトの構造を確認してください。

    コードベースは、エンタープライズ アプリケーションで一般的なレイヤー化アーキテクチャに従っています。 アーキテクチャの主なレイヤーは次のとおりです: モデル、構成、セキュリティ、サービス、処理。

1. 主要な処理クラスを調べます。

    **OrderProcessor.cs** と **ReturnProcessor.cs** を並べて開きます。 これらのクラスは、それぞれ顧客の注文と返品を処理するための主要なビジネス ロジックを表します。

    2 つのクラスは、メソッドのシグネチャと処理パターンが似ていることに注意してください。 これは最も明白な種類の重複ですが、コードベース全体に他にももっと微妙な重複が存在します。

1. サービス レイヤーを確認します。

    **Services** フォルダーに移動し、**EmailService.cs**、**AuditService.cs**、**InventoryService.cs** を調べます。

    メール通知、監査ログ、在庫管理の処理のために、これらのサービスで似たパターンが実装されていることに気付くかもしれません。 各サービスには、似た構造に従っているものの、異なるビジネス プロセス (注文と返品) のために重複しているメソッドがあります。

1. アプリケーションを実行し、コンソールの出力を確認します。

    > **注**:コンソール出力のコピーは、プロジェクト ディレクトリに含まれる EXPECTED_OUTPUT.md ファイルに格納されます。 このファイルを使って、重複コードを統合した後でアプリケーションの動作が変わっていないことを確認します。

    ソリューション エクスプローラー ビューで、**ECommerceOrdersAndReturns** を右クリックし、**[デバッグ]** を選んでから **[新しいインスタンスの開始]** を選んで、アプリケーションを実行できます。

    コンソール出力には次のものが含まれます。

    - 初期在庫レベル
    - 検証、配送計算、支払処理、在庫予約、メール通知、監査ログを含む注文処理
    - 手順は似ているが返品に適応されている返品処理
    - 更新された在庫レベル
    - さまざまな無効入力に関するセキュリティ検証テスト

    アプリケーションは、5 つのテスト シナリオを実行して、処理の成功とセキュリティ検証の失敗の両方を示します。

1. 少し時間を取って、観察した重複するコード パターンを分類してください。

    次に例を示します。

    **重複したメソッド**:OrderProcessor と ReturnProcessor には、同じ `Validate()` メソッドと似た `CalculateShipping()` メソッドがあります。

    **サービス レイヤーでの似たパターン**:各サービスには、似たパターンに従っているものの、異なるビジネス プロセス (注文と返品) のために重複しているメソッドがあります。

変更を行う前に、既存の機能を理解しておくことが重要です。 コードを実行して出力を確認し、リファクタリングによって既存の機能が損なわれないことを検証するために使用できるベースラインを確立します。

### GitHub Copilot Chat (質問モード) を使って重複コードを識別する

GitHub Copilot Chat の質問モードは、複雑なコードベースを分析して、分かりにくい場合がある微妙な重複パターンを識別するための優れたツールです。 質問モードの Copilot はインテリジェントなコード レビュー担当者として機能し、複数のファイルを同時に分析して、明らかな重複と隠れた (コード ロジックの) 重複の両方を明らかにできます。

このタスクでは、GitHub Copilot を使って、eコマース アプリケーション内のさまざまな種類の重複コード パターンを体系的に識別します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot チャット ビューを開き、質問モードと GPT-4.1 モデルを構成します。

    チャット ビューをまだ開いていない場合は、Visual Studio Code ウィンドウの上部にある **[チャット]** アイコンを選びます。 チャット モードが **[質問]** に設定されていて、**GPT-4.1** モデルを使っていることを確認します。

    GPT-4.1 モデルは、GitHub Copilot Free プランで使用でき、複雑なタスクを処理するように設計されており、インテリジェントなコードの分析と提案を提供します。

1. エディターで開かれているすべてのファイルを閉じます。

    GitHub Copilot は、エディターで開かれているファイルを使ってコンテキストを確立します。 不要なファイルのタブを閉じると、分析のノイズを減らすことができます。

1. OrderProcessor と ReturnProcessor ファイルをチャットのコンテキストに追加します。

    ドラッグ アンド ドロップ操作を使って、ソリューション エクスプローラーからチャット コンテキストに **OrderProcessor.cs** と **ReturnProcessor.cs** ファイルを追加します。

    チャット コンテキストにファイルを追加すると、プロンプトを分析するときにそのファイルをコンテキストに含めるよう GitHub Copilot に指示されます。

    ソリューション エクスプローラーではなく既定のフォルダー ビューをお使いの場合は、ファイルを右クリックして **[ファイルをチャットに追加]** を選びます。 コード エディターでファイルを開いて、コンテキストを確立することもできます。

1. 選んだファイルで重複するコード パターンを識別するよう GitHub Copilot に指示します。

    たとえば、主要な重複を分析するには次のプロンプトを送信します。

    ```text
    What duplicate code exists between OrderProcessor.cs and ReturnProcessor.cs? Identify specific methods and logic that are duplicated between these classes. Describe opportunities to consolidate duplicate code.
    ```

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

    GitHub Copilot により、`Validate()` メソッドの重複と、`CalculateShipping()` メソッドでの類似パターンが特定されている必要があります。 また、監査ログ、エラー処理、支払いと払い戻しの処理に関連する類似パターンが示される可能性もあります。

1. チャット コンテキストを更新して、**EmailService.cs** ファイルを指定します。

    エディターで **EmailService.cs** を開きます。 ドラッグ アンド ドロップ操作を使って、**EmailService.cs** ファイルをチャット コンテキストに追加することもできます。 コンテキストから他のファイルをすべて削除します。

1. EmailService クラスの重複を明らかにするよう GitHub Copilot に指示します。

    次に例を示します。

    ```text
    Analyze the EmailService class. What duplicate logic exists within this service for handling order confirmations versus return confirmations? Describe opportunities to consolidate duplicate code.
    ```

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

    GitHub Copilot によって、注文確認と返品確認の処理に関する重複ロジックが特定されている必要があります。 これには、メールの準備と送信に関するテンプレート化されたアプローチが含まれます。 GitHub Copilot では、ヘルパー メソッドとコンソール出力に関連する重複パターンが特定される可能性もあります。

1. チャット コンテキストを更新して、**AuditService.cs** と **InventoryService.cs** ファイルを指定します。

    **AuditService.cs** と **InventoryService.cs** をエディターで開きます。 ドラッグ アンド ドロップ操作を使って、これらのファイルをチャット コンテキストに追加することもできます。 コンテキストから他のファイルをすべて削除します。

1. 監査サービスと在庫サービスのファイルでの重複を明らかにするよう GitHub Copilot に指示します。

    次に例を示します。

    ```text
    Analyze the AuditService and InventoryService classes. Identify the methods that contain duplicate logic patterns that could be consolidated. Describe opportunities to consolidate duplicate code.
    ```

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

    GitHub Copilot によって、AuditService での監査エントリの作成/検証/保存と、InventoryService での在庫の検証/更新/ログのようなパターンが特定されている必要があります。 GitHub Copilot では、ヘルパー メソッドに関連する重複パターンも特定される可能性があります。

1. GitHub Copilot に包括的な重複分析の実行を指示します。

    次に例を示します。

    ```text
    @workspace Analyze the entire ECommerceOrderAndReturn codebase and identify all forms of code duplication, including method-level, service-level, and architectural pattern duplications. Prioritize the duplications by impact and refactoring difficulty. Describe an approach for consolidating this code.
    ```

    特定のファイルを分析した後は、コードベース全体を分析に含めるよう GitHub Copilot に指示して、さらに広範囲に確認できます。 範囲を広げると、複数のファイルまたはレイヤーでの似たエラー処理、ログ、検証ロジックなど、さらに多くの重複パターンが明らかになる可能性があります。 リファクタリング戦略について通知して優先順位を付ける単一の応答を得ると、役に立つことがよくあります。

    > **注**:`@workspace` と `#codebase` キーワードは、分析のコンテキストにコードベース全体を含めるよう GitHub Copilot に指示します。 `@workspace` キーワードは、質問モードでの GitHub Copilot の使用時にのみ使用できます。 `#codebase` キーワードは、どのモード (質問、編集、エージェント) でも使用できます。

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

    応答では、すべての重複パターンの包括的な分析が提供され、重複コードを統合するためのアプローチが提案されている必要があります。 運用シナリオでは、応答の各セクションを分析し、フォローアップ プロンプトを使って特定の領域を詳しく調べることを検討する必要があります。

    このトレーニング演習では、GitHub Copilot が提供できる情報の種類を理解することが重要ですが、リファクタリング戦略を検討するときは要約セクションに注目できます。

    次に例を示します。

    ```md
    
    **Summary**

    The codebase contains significant method-level and service-level duplication, especially in validation, shipping calculation, audit logging, email notification, and inventory management. The highest priority and easiest wins are consolidating validation and shipping logic. Service-level helper methods should be refactored next. The processor workflow pattern can be abstracted for further maintainability, though this is more complex.

    Start with shared services for validation and shipping, then refactor service helpers, and finally consider architectural abstraction for processors.

    ```

1. 人手によるコード レビューで GitHub Copilot の分析を検証します。

    GitHub Copilot の結果と独自の観察を相互参照します。 何が重複しているかだけでなく、これらのパターンが存在する理由と、ビジネス ロジックの整合性を維持しながらそれらを統合する方法を確実に理解します。

GitHub Copilot の質問モードは、単純なコピー貼り付けシナリオに留まらない微妙な重複を特定する場合に特に威力を発揮します。 似た論理パターン、実装方法が異なる同等のビジネス ルール、複数のファイルにまたがるアーキテクチャの重複を認識できます。

> **注**:このタスクの間に生成された分析を使って、次のセクションで実装するリファクタリング戦略が通知されます。

### GitHub Copilot Chat (エージェント モード) を使って重複ロジックを統合する

GitHub Copilot のエージェント モードを使うと、複数のファイルとアーキテクチャ レイヤーにまたがる複雑な複数ステップのリファクタリング タスクを割り当てることができます。 エージェントは、新しいファイルを自律的に作成したり、既存のコードを変更したり、進行状況をユーザーに常に知らせながら包括的なリファクタリング戦略を実装したりできます。

このタスクでは、GitHub Copilot エージェントを使って、前のタスクで明らかになった重複コード パターンを体系的に除去します。最も単純な重複から始めて、より複雑なサービス レイヤーの統合に進みます。

そのためには、以下の手順を実行してください。

1. GitHub Copilot チャット ビューをエージェント モードに切り替えます。

    エージェント モードを使うと、GitHub Copilot はコードベースを自律的に変更できます。 エージェント モードは、複雑な複数ステップのリファクタリング タスクに特に役立ちます。

    モードを変更するには、モード セレクター (通常はチャット ビューの左下隅) を見つけて、**[エージェント]** を選びます。

1. 少し時間を取って、リファクタリング戦略を計画します。

    GitHub Copilot エージェントにタスクを割り当てる前に、リファクタリングの論理的な順序を検討します。 前のタスクの分析を使って、決定を通知します。

    次に例を示します。

    GitHub Copilot が次のように提案した場合:

    "検証と配送に関する共有サービスから始めて、次にサービス ヘルパーをリファクタリングし、最後にプロセッサのアーキテクチャの抽象化を検討します。"

    次の段階的なアプローチを使用できます。

    - **フェーズ 1**:コア ビジネス ロジックの重複 (検証と配送の計算)
    - **フェーズ 2**:サービス レイヤーの重複 (メール、監査、在庫サービス)
    - **フェーズ 3**:横断的な問題とアーキテクチャの改善

    この段階的アプローチに従うと、変更を管理しやすくし、段階的にテストできます。

1. GitHub Copilot エージェントに、OrderProcessor クラスと ReturnProcessor クラスの検証ロジックを統合するよう指示します。

    たとえば、次のタスクを GitHub Copilot エージェントに送信します。

    ```text
    Create a shared ValidationService class that consolidates the duplicate Validate() method logic from OrderProcessor and ReturnProcessor. The service should handle ID validation for both orders and returns while maintaining all existing security checks, business rules, and logging. Update both processor classes to use the new shared service and remove the duplicate private methods. Build and test the code to ensure the functionality remains intact. Continue working until the validation service is fully integrated.
    ```

1. エージェントの進行状況をチャット ビューで監視します。

    エージェントが割り当てられたタスクを実行する進行状況をチャットで確認できます。

    エージェントが行っているタスクの処理を支援するため、続行するためのアクセス許可を付与したり、必要に応じて追加のコンテキストを指定します。 たとえば、エージェントからアプリケーションのビルドまたは実行のアクセス許可を求められたら、**[続行]** を選びます

    エージェントは、このタスクの間に次のことを行います。

    - Services フォルダーに新しい **ValidationService.cs** ファイルを作成します
    - 再利用可能なメソッドに検証ロジックを抽出します
    - 新しいサービスを使うように両方のプロセッサ クラスを更新します
    - 重複するプライベート検証メソッドを削除します
    - 正常なビルドとテストの実行で機能を検証します

1. 検証サービスの変更を確認して受け入れます。

    コード エディターのタブを使って、エージェントによって提案された変更を調べます。 チャットの編集をスクロールして、特定のコードの変更を確認できます。 現在の編集を受け入れるには、エディターのタブで **[保持する]** を選びます。 エディターのタブで **[元に戻す]** を選ぶと、編集を拒否できます。

    チャット ビューで **[保持する]** または **[元に戻す]** を選んで、すべての変更を受け入れるか拒否します

    新しい検証サービスでは、単一の再利用可能な実装を提供しながら、既存のすべての検証ロジックが維持されている必要があります。 変更が正しそうであれば、それを受け入れます。

    > **注**:運用コードの作業を行っているときは、重要なリファクタリング操作の後でコードを徹底的にテストすることが重要です。 これには、アプリケーションのビルドとテストを行って、機能が意図したとおりに動作し、単体テストが成功し、出力が元の動作と一致していることを検証することが含まれます。 このトレーニング演習中の時間を節約するため、増分テストの実行をエージェントに依存しています。 追加の (手動検証) テストは、すべてのリファクタリング タスクが完了した後で行われます。

1. GitHub Copilot エージェントに配送計算ロジックを統合するよう指示します。

    たとえば、次のタスクを使って配送計算を統合します。

    ```text
    Create a shared ShippingCalculationService that consolidates the similar CalculateShipping() logic from OrderProcessor and ReturnProcessor. The service should handle both order shipping (with free shipping thresholds) and return shipping (with processing fees) while maintaining the different business rules for each type. Update both processor classes to use the new shared service. Build and test the code to ensure the functionality remains intact. Continue working until the shipping calculation service is fully integrated.
    ```

    エージェントは、注文と返品で異なるビジネス ルールを維持しながら、両方のシナリオを処理する配送サービスを作成する必要があります。

    > **注**:エージェントは、コード リファクタリング プロセスの間に問題が発生した場合、ガイダンスのために元のプロセッサ クラスを参照する必要があり、配送計算サービスが完全に統合されるまで、コードの更新とテストを続ける必要があります。 エージェントが割り当てられたタスクの実行に失敗した場合、またはエージェントが解決不可能なコード修正のループに陥った場合は、エージェントを停止して編集を元に戻します。 チャット セッションには、問題のトラブルシューティングとタスク (プロンプト) の更新に十分なコンテキストが含まれている必要があります。 また、問題が解決されるように割り当てられたタスクを更新する支援を GitHub Copilot に指示することもできます。

1. 変更を確認して受け入れます。

1. GitHub Copilot エージェントに、EmailService の重複をリファクタリングするよう指示します。

    たとえば、次のタスクを使って、メール サービスの重複を統合します。

    ```text
    Refactor the EmailService class to eliminate duplicate logic in the helper methods. Create a unified approach for template building, subject formatting, email sending, and activity logging that can handle both order and return confirmations. The public methods SendOrderConfirmation and SendReturnConfirmation should remain, but they should use shared private helper methods. Build and test the code to ensure the functionality remains intact. Continue working until the unified approach is fully integrated.
    ```

1. 変更を確認して受け入れます。

1. GitHub Copilot エージェントに、AuditService の重複を統合するよう指示します。

    たとえば、次のタスクを使って、監査サービスの重複を統合します。

    ```text
    Refactor the AuditService class to consolidate the duplicate logic in LogOrderActivity and LogReturnActivity. Create shared helper methods for audit entry creation, validation, storage, and compliance checking. The public methods should remain but use common underlying logic. Build and test the code to ensure the functionality remains intact. Continue working until the shared helper methods are fully integrated.
    ```

1. 変更を確認して受け入れます。

1. GitHub Copilot エージェントに、InventoryService の重複に対処するよう指示します。

    たとえば、次のタスクを使って、在庫サービスの重複を処理します。

    ```text
    Refactor the InventoryService class to eliminate duplicate logic between ReserveOrderInventory and RestoreReturnInventory. Create shared helper methods for inventory validation, level updates, and transaction logging while maintaining the different business logic for reservations versus restorations. Build and test the code to ensure the functionality remains intact. Continue working until the shared helper methods are fully integrated.
    ```

1. GitHub Copilot エージェントに、コードベースに残っている重複を統合するよう指示します。

    たとえば、次のタスクを使って、残りの重複に対処します。

    ```text
    Analyze the entire ECommerceOrderAndReturn codebase and identify any remaining duplicate code patterns that should be consolidated. Focus on cross-cutting concerns like payment processing, status updates, and error handling. Create shared services or helper methods as needed to eliminate these duplications while maintaining existing functionality. Build and test the code to ensure the functionality remains intact. Continue working until all identified duplications are fully integrated.
    ```

    このような "残っているすべての問題を処理する" タスクの GitHub Copilot エージェントへの割り当ては、リファクタリング プロセスの最後にのみ行う必要があり、必要な場合にだけ使用する必要があります。 この種の包括的な分析を試みるのが早すぎると、予期しない結果やタスクの失敗が発生し、ロールバックが必要になる可能性があります。 計画的なアプローチを作成し、段階的なプロセスを使って優先順位に基づいて改訂に対処するのが最善です。

    > **注:**  GitHub Copilot が "残っている重複コード パターンを統合" した後でも、さらに統合を行う機会がある可能性があります。 ただし、実装した計画的アプローチにより、コードベースのすべての主要な重複が統合される必要があります。 問題がある場合は、分析とリファクタリングのプロセスを繰り返すことができます。 GitHub Copilot を正式なコード レビュー プロセスの代わりに使用してはならないことに注意してください。

GitHub Copilot エージェントは、ビジネス ロジックとアーキテクチャのパターンを理解する必要がある複雑な複数ファイルのリファクタリング タスクに優れています。 リファクタリングを論理的なフェーズに分割し、段階的にテストすることで、統合の際にシステムの整合性が維持されると共に、コードの品質、保守容易性、拡張性、再利用性が大幅に向上します。

### リファクタリングされた eコマースの注文と返品のコードをテストする

人手でテストと検証を行って、リファクタリングされたコードで意図されるビジネス ロジックと機能が維持されているのを確認することが重要です。 成功したリファクタリング プロセスでは、元の実装と同じ動作を生成しながら、意図した目標 (重複するコード ロジックの統合など) が達成されている必要があります。

このタスクでは、リファクタリングされたコードで元の機能がすべて維持されていることと、統合が成功したことを確認します。

そのためには、以下の手順を実行してください。

1. リファクタリングされたプロジェクトをビルドして、コンパイル エラーを調べます。

    コンパイル エラーがある場合は、リファクタリング後のコードをレビューして問題を解決します。 GitHub Copilot を使って、リファクタリング プロセスから発生する問題を診断して修正できます。

1. リファクタリング後のアプリケーションを実行して、出力をキャプチャします。

    アプリケーションでは、5 つのテスト シナリオがすべて前とまったく同じように実行される必要があります。

    - 初期在庫の表示
    - 完全なワークフローでの有効な注文処理
    - 完全なワークフローでの有効な返品処理
    - 更新された在庫レベル
    - 無効な入力でのセキュリティ検証テスト

1. リファクタリング後のコードによって生成された出力を元の出力と比較するよう GitHub Copilot に指示します。

    次に例を示します。

    ```text
    Run the app and compare the generated output with the contents of the EXPECTED_OUTPUT.md file. Does the current output match the stored output? Explain any differences.
    ```

    元の出力 **EXPECTED_OUTPUT.md** は、ECommerceOrderAndReturn フォルダーに含まれています。

    2 つ目の出力ファイルを作成し、2 つのファイル間の違いを特定するよう GitHub Copilot に指示できます。

    出力は同じである必要があります。これにより、重複コード ロジックを統合するときに、ビジネス ロジックが変更されていないことが確認されます。

1. 最終的なコード レビューを実行します。

    リファクタリング後のコードベースをレビューして、次のことを確認します。

    - **コードの品質**:メソッドが適切な名前を付けられており、一貫したパターンに従っています
    - **保守性**:ビジネス ルールを変更するときに更新する必要がある場所が 1 つのみになっています
    - **読みやすさ**:コードの構造が明確で論理的です
    - **再利用性**:将来の要件に合わせて共有サービスを簡単に拡張できます
    - **拡張性**:既存のコードへの影響を最小限に抑えて新機能を追加できます

人手によるテストで、統合作業が意図した目標 (システムの機能を維持しながら重複するコードを除去する) を達成したことを確認します。 このアーキテクチャは、将来の開発のための保守性の高い基盤を備えるようになりました。ビジネス ルールを変更するときは、複数の重複する実装を更新する必要はなく、1 つの場所で実装できます。

## まとめ

この演習では、GitHub Copilot を使ってアプリケーション内の重複するコードを統合する方法を学びました。 eコマースの注文と返品の処理システムを調査して、重複するコード パターンを明らかにし、GitHub Copilot を使ってコードベースをリファクタリングして、保守性と読みやすさを向上させました。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認してください。 変更を行った場合は、必要に応じてそれを元に戻します。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したサンプル プロジェクト フォルダーをアーカイブまたは削除できます。
