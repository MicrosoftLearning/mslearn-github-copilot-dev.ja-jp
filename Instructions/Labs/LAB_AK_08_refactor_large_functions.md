---
lab:
  title: 演習 - GitHub Copilot を使用して大きな関数をリファクタリングする
  description: GitHub Copilot のツールを使って、複雑なコードを分析し、大きな関数をより小さくて限定的なメソッドにリファクタリングする方法を学びます。
---

# GitHub Copilot を使用して大きな関数をリファクタリングする

大きな関数は、理解、保守、テストするのが困難な場合があります。 多くの場合、複数の機能が含まれており、一目で理解するのが難しいことがあります。 大きな関数をより小さくて限定的な関数にリファクタリングすると、コードの可読性と保守性が向上します。

この演習では、大きな関数を含む既存のプロジェクトをレビューし、より小さな単一機能の関数にするためのオプションを分析して、大きな関数をより小さな関数にリファクタリングした後、リファクタリング後のコードをテストして意図したとおりに動作することを確認します。 質問モードで GitHub Copilot を使い、既存のコード プロジェクトを理解して、ロジックをリファクタリングするためのオプションを調べます。 エージェント モードで GitHub Copilot を使い、大きな関数からコード セクションを抽出して小さな関数を作成することでコードをリファクタリングします。 元のコードとリファクタリング後のコードをテストして、リファクタリング後のコードが意図したとおりに動作することを確認します。

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

1. サンプル アプリ プロジェクトを含む ZIP ファイルをダウンロードするには、ブラウザーで次の URL を開きます: [GitHub Copilot ラボ - 大きな関数をリファクタリングする](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHCopilotEx8LabApps.zip)

    ZIP ファイルの名前は **GHCopilotEx8LabApps.zip** です。

1. **GHCopilotEx8LabApps.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **GHCopilotEx8LabApps.zip** を右クリックして、**[すべて展開]** を選びます。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. **GHCopilotEx8LabApps** フォルダーを、Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で **GHCopilotEx8LabApps** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**GHCopilotEx8LabApps** を選んでから、**[フォルダーの選択]** を選びます。

1. Visual Studio Code のソリューション エクスプローラー ビューで、次のプロジェクト構造を確認します。

    - GHCopilotEx8LabApps\
        - ECommerceOrderProcessing\
            - src\
                - ECommerce.ApplicationCore\
                    - Entities\
                    - Exceptions\
                    - Interfaces\
                    - Services\
                        - OrderProcessor.cs
                - ECommerce.Console\
                    - order_audit_log.txt
                    - Program.cs
                - ECommerce.Infrastructure\
                    - Services\
        - ServerLogAnalysisUtility\

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 クライアントは、レガシ アプリケーションの大きな関数のリファクタリングについて支援を必要としています。 目標は、既存の機能を維持しながら、コードの可読性と保守性を向上させることです。 あなたには次のアプリが割り当てられています。

- E-CommerceOrderProcessing:この eコマース アプリは、顧客の注文の処理に使われます。 このプロセスには、注文の検証、在庫管理、支払処理、配送調整、顧客通知が含まれます。 このアプリケーションでは、レイヤー構造を持つクリーン アーキテクチャの原則が使われていますが、**OrderProcessor** クラスには複数の機能を処理する大きなメソッドが含まれており、より小さくて限定的なメソッドにリファクタリングする必要があります。

この演習には、次のタスクが含まれています。

1. eコマースの注文処理のコードベースを手作業で確認します。
1. GitHub Copilot Chat (質問モード) を使用して、リファクタリングの機会を明らかにします。
1. GitHub Copilot Chat (エージェント モード) を使用して、大きな関数をより小さく管理しやすい関数にリファクタリングします。
1. リファクタリング後の eコマース注文処理コードをテストします。

### eコマースの注文処理のコードベースを手作業で確認する

あらゆるリファクタリング作業の最初のステップは、既存のコードベースを確実に理解することです。 コードの構造、ビジネス ロジック、コードの実行時に生成される結果を理解することが重要です。

このタスクでは、eコマース注文処理プロジェクトの主要なコンポーネントをレビューし、アプリを実行してその機能を確認します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code で GHCopilotEx8LabApps フォルダーが開かれていることを確認します。

    サンプル コード プロジェクトをダウンロードしていない場合は、「**開始する前に**」セクションを参照してください。

1. 少し時間を取って、ECommerceOrderProcessing プロジェクトの構造を確認してください。

    このコードベースはレイヤー化アーキテクチャ パターンに従っており、次の 3 つの主要なプロジェクトが含まれます。

    - **ECommerce.ApplicationCore**:ドメイン エンティティ、ビジネス ロジック インターフェイス、メインの OrderProcessor サービスが含まれます。
    - **ECommerce.Console**:コンソール アプリケーションのエントリ ポイントと依存関係の挿入のセットアップが含まれます。
    - **ECommerce.Infrastructure**:外部統合 (支払い、配送、在庫など) のためのサービスの実装が含まれます。

    この構造はクリーン アーキテクチャの原則を使って実際の .NET アプリケーションを表しており、ビジネス ロジックがインフラストラクチャの関心領域から分離されています。

1. GitHub Copilot チャット ビューを開きます。

    チャット ビューをまだ開いていない場合は、Visual Studio Code ウィンドウの上部にある **[チャット]** アイコンを選んで開くことができます。

1. チャット ビューで、チャット モードが **[質問]** に設定され、モデルが **[GPT-4o]** に設定されていることを確認します。

    これらの設定は、チャット ビューの左下隅に表示されます。 GitHub Copilot の**質問**モードは、コーディングに関する一般的な質問や、コード関連の説明の生成に使われます。 GitHub Copilot Free プランに含まれる **GPT-4o** モデルは、コード分析とリファクタリングのガイダンスに適しています。

    この演習では後ほど GitHub Copilot の**エージェント** モードを使いますが、ここではコードの分析と説明のために**質問**モードを使います。

    > **注**:GitHub Copilot の応答は、選んだモデルによって異なる場合があります。 このラボ演習を行うときは、指定されたモデルを使うことをお勧めします。 違いを確認したい場合は、別のモデルで演習を繰り返すことができます。

1. ソリューション エクスプローラー ビューを使って、**OrderProcessor.cs** ファイルを見つけます。

    **OrderProcessor.cs** ファイルは **src/ECommerce.ApplicationCore/Services** フォルダーにあります。

1. コード エディターで **OrderProcessor.cs** ファイルを開きます。

    OrderProcessor クラスは顧客注文の処理を担当します。 これには、検証、支払処理、通知など、注文処理の主要なビジネス ロジックが含まれています。

1. 少し時間を取って、**OrderProcessor** クラスを確認してください。

    ProcessOrder メソッドに注意してください。 このメソッドは、顧客注文を処理するための主要なビジネス ロジックを表します。 複数の異なる操作が処理されていることに注意してください。 ProcessOrder メソッドは、ビジネス ロジックが時間と共に蓄積されて理解、テスト、保守が難しくなる実際のシナリオを見てもらうため、意図的に大きく複雑にしてあります。

1. **ProcessOrder** メソッドを右クリックして、**[Copilot]** > **[説明]** を選びます。

    **[囲む範囲を選択して説明する]** と表示されたら、**ProcessOrder** を選びます。

    GitHub Copilot によって ProcessOrder メソッドが分析され、コードの動作の詳細な説明が提供されます。これは、リファクタリング オプションを調べる前にビジネス ロジックを理解するのに役立ちます。

1. 少し時間を取って、GitHub Copilot の説明を確認してください。

    この説明では、包括的な検証手順、セキュリティ リスク評価、複数サービスの調整、ロールバック機能でのエラー処理など、主要な処理手順とビジネス ルールが明確に示されている必要があります。

1. アプリケーションを実行して、現在の動作を理解します。

    アプリケーションを実行するには、いくつかのオプションがあります。 次に例を示します。

    ソリューション エクスプローラー ビューで **ECommerce.Console** プロジェクトを右クリックし、**[デバッグ]** を選んでから、**[新しいインスタンスの開始]** を選びます。 または、**Program.cs** ファイルを Visual Studio Code で開いている場合は、エディターの上にある実行ボタンを選択できます。

    ターミナルで **src/ECommerce.Console** フォルダーに移動し、次の .NET CLI コマンドを入力することもできます。

    ```bash
    dotnet run
    ```

1. アプリケーションの実行時に生成されるコンソール出力を確認します。

    アプリケーションは、4 つのテスト ケースに対して出力を生成します。 各テスト ケースは異なるシナリオを示しています。

    - **テスト 1**:複数の品目を含む有効な注文処理。
    - **テスト 2**:無効なメール アドレス検証。
    - **テスト 3**:拒否された支払い処理。
    - **テスト 4**:疑わしい注文セキュリティ チェック。

    各テスト ケースの出力では、検証メッセージ、在庫チェック、支払処理、配送スケジュール、通知などの詳細な処理手順が示されます。 出力では、適切なエラー メッセージとクリーンアップ手順によってさまざまなエラー シナリオがどのように処理されるかも示されています。

1. 少し時間を取って、ProcessOrder メソッドのリファクタリングの機会を確認してください。

    ProcessOrder メソッドには、複数の異なる機能が含まれます。 対応する各コード セクションを個別のメソッドに抽出できます。

既存の機能を理解し、リファクタリングの機会を明らかにすると、コードの構造を改善しながらビジネス ロジックを維持するリファクタリング戦略を作成するのに役立ちます。 レイヤー化アーキテクチャによって関心領域は既にプロジェクト レベルで適切に分離されていますが、大きな ProcessOrder メソッドには注意が必要です。

### GitHub Copilot Chat (質問モード) を使用して、リファクタリングの機会を明らかにする

GitHub Copilot Chat の質問モードは、複雑なコードを分析し、大きなメソッドをリファクタリングする機会を明らかにするための優れたツールです。 質問モードの Copilot は、コードの構造を分析し、モノリシックなメソッドをより小さくて限定的なメソッドに分割する方法を提案できます。

このタスクでは、GitHub Copilot を使って ProcessOrder メソッドを評価し、コードの構造を改善しながらビジネス ロジックを維持するリファクタリングの機会を明らかにします。

そのためには、以下の手順を実行してください。

1. GitHub Copilot チャット ビューが**質問**モードで開かれていて、**GPT-4o** モデルが選ばれていることを確認します。

1. OrderProcessor.cs 以外のファイルを開いている場合は、それらを閉じます。

    GitHub Copilot は、エディターで開かれているファイルを使ってコンテキストを確立します。 対象のファイルのみを開くと、リファクタリングしたいコードに分析を集中し、GitHub Copilot で最も関連性の高い提案が提供されるのに役立ちます。

1. OrderProcessor.cs ファイルをチャット コンテキストに追加します。

    ドラッグ アンド ドロップ操作を使って、ソリューション エクスプローラーからチャット コンテキストに **src/ECommerce.ApplicationCore/Services/OrderProcessor.cs** ファイルを追加します。 チャット コンテキストにファイルを追加すると、プロンプトを分析するときにそのファイルを含めるよう GitHub Copilot に指示され、分析の精度が向上します。

1. ProcessOrder メソッドでリファクタリングの機会を分析するよう GitHub Copilot に指示します。

    ProcessOrder メソッドを分析して特定の改善領域を明らかにするよう GitHub Copilot に指示するプロンプトを送信します。 リファクタリングで達成したいことの詳細を含めるようにします。

    次に例を示します。

    ```text
    Analyze the ProcessOrder method in the OrderProcessor class. This method handles multiple responsibilities. Identify opportunities to break this large method into smaller, more focused methods. What specific functions could be extracted, and what would be the benefits of doing so?
    ```

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

    GitHub Copilot によって、ProcessOrder メソッド内のさまざまな機能が特定され、それらを個別のメソッドに抽出する方法が提案されている必要があります。 分析により、検証ロジック、セキュリティ評価、在庫操作、支払処理、配送調整、通知処理、注文完了など、個別のメソッドにすることができる個々の論理セクションが特定されている必要があります。

    たとえば、GitHub Copilot によって次のものが識別される可能性があります。

    - 抽出の候補としての入力検証とセキュリティ チェック。
    - グループ化できる在庫管理操作。
    - 支払処理ロジックとその独自のエラー処理。
    - 個別の関心領域としての配送と通知のロジック。
    - 分離できる注文完了手順。

1. GitHub Copilot に詳細なリファクタリング計画を提供するよう指示します。

    次に例を示します。

    ```text
    Create a detailed refactoring plan for the ProcessOrder method. Show me what the ProcessOrder method would look like after refactoring and provide a list of the methods that should be extracted. I'd like to keep the input validation and security checks together in a single method. Include suggestions for method signatures and return types that would maintain the current error handling behavior.
    ```

    このようなフォローアップ プロンプトを使って追加の洞察を得ることができますが、目標に合わないプロンプトは避ける必要があります。 チャットの会話から逸脱すると、GitHub Copilot の応答に影響を与える可能性があります。 明確なチャット履歴が重要です。

1. 少し時間を取って、GitHub Copilot のリファクタリング計画を確認してください。

    GitHub Copilot によって、ProcessOrder メソッドを大きいモノリシックなメソッドから一連の小さくて限定的なメソッド呼び出しに変換する方法を示す明確なアウトラインが提供されている必要があります。 この計画では、コードの構造と可読性を向上させながら、既存のビジネス ロジックを維持する必要があります。

    応答には次のものが含まれている必要があります。
    - 主な手順を個別のメソッド呼び出しとして示す概要レベルのフロー。
    - 抽出されたメソッドに対して提案されるメソッド名とシグネチャ。
    - メソッド間でエラーを一貫して処理する方法に関するガイダンス。
    - リファクタリング後の構造で保守性がどのように向上するかについての説明。

1. エラー処理パターンに関する追加のガイダンスを求めます。

    エラー処理プロセスを理解することは、ProcessOrder メソッドをリファクタリングするときに既存の動作を維持するために重要です。 GitHub Copilot に現在のエラー処理戦略を分析させて、既存の動作を維持または改善する方法を提案させることができます。

    次に例を示します。

    ```text
    In the current ProcessOrder method, there are multiple error scenarios with specific cleanup procedures (like releasing inventory on payment failure). In the refactored version, how should I handle errors consistently across the extracted methods? Should each method return an OrderResult object, throw exceptions, or use another pattern to maintain the existing error handling behavior?
    ```

1. 少し時間を取って、エラー処理に関する提案を確認してください。

    GitHub Copilot によって、リファクタリング後のメソッド間で一貫したエラー処理パターンを維持するためのガイダンスが提供されている必要があります。 現在のメソッドには、維持する必要があるロールバック手順を含む複雑なエラー処理があるため、これは重要なことです。

    提案では次のことが対応されている必要があります。
    - 現在のロールバック動作を維持する方法 (支払失敗時の在庫の解放など)。
    - エラーの通知に戻り値、例外、または結果オブジェクトを使うかどうか。
    - リファクタリングされたメソッド全体で監査ログを保持する方法。
    - エラー シナリオでクリーンアップ手順が引き続き実行されるようにする方法。

GitHub Copilot の質問モードは、複雑なコード構造を分析し、リファクタリングのための戦略的なガイダンスを提供することに関して優れています。 この分析からの洞察で示される特定のリファクタリング アプローチを次のセクションで実装すると、より適切なコード編成を実現しながらビジネス ロジックの整合性を維持できます。

### GitHub Copilot を使用して大きな関数をリファクタリングする (エージェント モード)

エージェント モードでは、複雑なコード リファクタリング タスクを GitHub Copilot に割り当てることができます。 割り当てられるタスクには、複数のファイルの作成や更新を含めることができます。 GitHub Copilot エージェントはタスクを自律的に処理し、更新の動作をテストしてデバッグし、チャット ビューでその進行状況を報告してユーザーに最新情報を提供します。

このタスクでは、GitHub Copilot エージェントを使って、既存のビジネス ロジックとエラー処理動作を保ちながら、より小さくて限定的なメソッドを抽出することで、ProcessOrder メソッドを体系的にリファクタリングします。

そのためには、以下の手順を実行してください。

1. Visual Studio Code で GitHub Copilot チャット ビューが開かれていることを確認します。

1. チャット ビューで **[エージェント]** モードを選びます。

    **[モードの設定]** ドロップダウンは、チャット ビューの左下隅にあります。 **エージェント** モードの GitHub Copilot は、割り当てられたタスク (プロンプト) を自律的に処理します。

1. 少し時間を取って、リファクタリング戦略を検討してください。

    前のタスクの分析を使って、ProcessOrder メソッドをリファクタリングするための戦略を作成します。 アプローチでは、段階的なテストをサポートする必要があります。

    たとえば、次のような段階的リファクタリング戦略を検討します。

    - **フェーズ 1**:OrderProcessor クラス内にスタブ メソッドを作成します。
    - **フェーズ 2**:入力検証とセキュリティ評価のロジックを抽出します。
    - **フェーズ 3**:在庫管理操作 (チェックと予約) を抽出します。
    - **フェーズ 4**:不正検出とエラー処理を含む支払処理を抽出します。
    - **フェーズ 5**:配送調整と追跡管理を抽出します。
    - **フェーズ 6**:通知とコミュニケーションのロジックを抽出します。
    - **フェーズ 7**:注文の最終処理と完了の手順を抽出します。

    この段階的アプローチに従うと、変更を管理しやすくなり、更新を段階的にテストできます。 リファクタリング後のコードでは、元のメソッドと同じビジネス ロジックとエラー処理が維持されている必要があります。

1. コード エディターで、OrderProcessor クラスの一番下までスクロールし、ProcessOrder メソッドの右中かっこの後に次のコード コメントを作成します。

    ```csharp

        }
    
        // Add stub methods here
        
    }

    ```

    このコード コメントは、ProcessOrder メソッドの最後の右中かっこの後で、OrderProcessor クラスの右中かっこの前に配置する必要があります。

    OrderProcessor クラス内に新しい単一目的のメソッドを作成するときにこの場所を使うよう、GitHub Copilot に指示します。

1. 抽出されたコードの保持に使用できるスタブ メソッドを作成するよう、GitHub Copilot エージェントに指示します。

    次に例を示します。

    ```text
    Review the current conversation. I want to start by creating stub code for the new single-purpose methods that will be used when refactoring the ProcessOrder method. Use the method declarations that you proposed in this conversation. Create the new stub methods below the "Add stub methods here" comment in the OrderProcessor class. Ensure that you're using appropriate method parameters and return types. Do not extract any code from the ProcessOrder method, just create the stub methods. After the stub methods are created, open the terminal, navigate to the "ECommerceOrderProcessing/src/ECommerce.Console" directory, then run a "dotnet build" command. Ensure that there are no build errors.
    ```

    最初にスタブ メソッドを作成すると、確実に新しいメソッドを正しく定義して既存のコード構造に統合するのに役立ちます。 この方法では、ProcessOrder からコードを完全に抽出する前に、各メソッドの機能を段階的にテストおよび検証できます。

1. エージェントの進行状況を監視し、必要に応じて補助します。

    GitHub Copilot エージェントは、指定された場所に新しいメソッドを作成してから、ターミナルでビルド コマンドを実行するアクセス許可を求めます。 メッセージが表示されたら、**[続行]** ボタンを選びます (エージェントの続行を許可します)。

    エージェントは、問題が発生すると、ユーザーに通知してから、自動的に問題の修正を試みます。 必要に応じて引き続き補助します。

    ビルド タスクを実行した後、エージェントから、新しいメソッドが正しく定義され、構文エラーがないことが通知される必要があります。

1. 編集を受け入れるには、**[保持する]** を選びます。

    コード エディターで編集を個別に受け入れる (または拒否する) ことも、GitHub Copilot チャット インターフェイスで一度にすべてを受け入れる (または拒否する) こともできます。

1. ProcessOrder メソッドのリファクタリングを GitHub Copilot エージェントに指示します。

    大きなメソッドのリファクタリングは、タスクを管理しやすい複数のステージに分割できる場合に最もうまく機能します。 この場合、各ステージは、既に特定されている単一プロセスのメソッドと一致します。 エージェントを使ってコードをリファクタリングする場合も、同じ方法を適用する必要があります。 一度に 1 つのセクションをリファクタリングし、更新をテストしてから次のセクションに進むよう、エージェントに指示するタスクを記述します。

    ただし、GitHub Copilot エージェントを使用するということは、一連の割り当てを独立して作業できる開発者がいるようなものです。 この場合の一連の割り当ては、各コード セクションをリファクタリングし、更新をテストしてから次のセクションに進む、というものです。 つまり、各コード セクションをリファクタリングし、単一目的の各メソッドをテストしてから、次のセクションのリファクタリングに進むよう GitHub Copilot エージェントに指示する、1 つのタスク (プロンプト) を記述できます。

    次に例を示します。

    ```text
    Review the current conversation. Examine the ProcessOrder method and identify the code sections that should be extracted into the single-purpose stub methods that you already created. Move the identified code sections into the associated single-purpose stub methods, constructing and testing the methods in the suggested order. Replace the extracted code sections with a call to the associated single-purpose method. Use local variables of the associated return value type to ensure that the ProcessOrder method maintains the same error handling behavior that's provided by the original code. As each method is updated, use a "dotnet run" command to ensure that the code features and error handling processes work correctly (including rollback features, like releasing inventory on payment failure). Also verify that the four test case scenarios generate the expected console output when the app is run (all test cases should pass). Continue extracting code into the new single-purpose methods (and testing the app) until all methods are complete and the application generates the expected console output for the four test case scenarios. Don't stop working on this task until all methods are constructed and tested. Display the step-by-step approach that you'll use to complete this task and then begin.
    ```

    > **注**:運用コードの作業を行っているときは、重要なリファクタリング操作の後でコードを徹底的にテストすることが重要です。 これには、アプリケーションのビルドとテストを行って、機能が意図したとおりに動作し、単体テストが成功し、出力が元の動作と一致していることを検証することが含まれます。 このトレーニング演習中の時間を節約するため、増分テストの実行をエージェントに依存しています。 コード リファクタリング タスクが完了した後、追加の (手動検証) テストを行います。

1. エージェントの進行状況を監視し、必要に応じて補助します。

    GitHub Copilot エージェントは、ProcessOrder メソッドの各セクションをリファクタリングするための計画を記述することから始める必要があります。 この計画には、ProcessOrder メソッドから対応する単一プロセス メソッドへのコードの移動、ProcessOrder で抽出されたコードのメソッド呼び出しへの置き換え、リファクタリング後のコードが意図したとおりに動作することを確認するためのアプリのテストなど、各コード セクションに対するステップ バイ ステップのアプローチが含まれる必要があります。

    また、エージェントは、発生した問題など、進行状況を説明する更新をチャット ビューで提供します。 ユーザーは、エージェントと対話して、指示を明確にしたり、必要に応じて追加のコンテキストを提供したりできます。

    GitHub Copilot エージェントは、通常、リファクタリング プロセスの間にアプリケーションをビルドまたは実行するためのアクセス許可を求めます。 これが発生したら、チャット ビューで **[続行]** ボタンを選んで、エージェントの続行を許可します。

    > **重要**:ProcessOrder メソッドのすべてのセクションがリファクタリングされる前に、GitHub Copilot エージェントが割り当てられたタスクの処理を停止した場合は、リファクタリング タスクを続けるようエージェントに指示するプロンプトを入力します。

1. リファクタリング プロセスが完了したら、変更を受け入れます。

    チャット ビューで **[保持する]** ボタンを選んで、エージェントによって行われたすべての変更を受け入れます。

1. 少し時間を取って、更新された OrderProcessor クラスを確認してください。

    エージェントがリファクタリング タスクを完了した後、OrderProcessor クラスには、注文処理の特定の側面を処理する、より小さくて限定的なメソッドがいくつか含まれているはずです。 ProcessOrder メソッドはずっと短くなっていっそう理解しやすくなり、注文処理ワークフローの各ステップは独自のメソッドで明確に定義されているはずです。

    たとえば、更新後の ProcessOrder メソッドは次のようになります。

    ```csharp
    public OrderResult ProcessOrder(Order order)
    {
        // Log the start of order processing for audit trail
        _auditLogger.LogOrderProcessingStarted(order.Id, order.CustomerEmail);

        try
        {
            // Validate order and perform security checks
            if (!ValidateOrderAndSecurity(order, out string? validationFailure))
            {
                return OrderResult.Failure(validationFailure ?? "Validation failed for unknown reasons");
            }

            Console.WriteLine($"Processing Order {order.Id} for {_securityValidator.MaskEmail(order.CustomerEmail)}...");
            Console.WriteLine($"Order contains {order.Items.Count} items, Total: ${order.TotalAmount:F2}");

            // Check inventory and reserve stock
            if (!CheckAndReserveInventory(order, out string? inventoryFailure))
            {
                return OrderResult.Failure(inventoryFailure ?? "Inventory check failed for unknown reasons");
            }
            Console.WriteLine("Inventory reserved successfully.");
            _auditLogger.LogInventoryReserved(order.Id, order.Items.Count);

            // Payment Processing with Enhanced Security
            Console.WriteLine("Processing payment...");
            // Process payment
            if (!ProcessPayment(order, out string? paymentFailure))
            {
                return OrderResult.Failure(paymentFailure ?? "Payment processing failed for unknown reasons");
            }

            // Shipping and Logistics Management
            Console.WriteLine("Scheduling shipping...");
            // Schedule shipping
            if (!ScheduleShipping(order, out string? shippingFailure))
            {
                return OrderResult.Failure(shippingFailure ?? "Shipping scheduling failed for unknown reasons");
            }

            // Customer Communication and Notifications
            Console.WriteLine("Sending notifications...");
            // Send notifications
            SendNotifications(order);

            // Order Finalization and Data Recording
            Console.WriteLine("Finalizing order...");
            order.Status = OrderStatus.Completed;
            order.CompletionDate = DateTime.UtcNow;
            order.ProcessingDuration = DateTime.UtcNow - order.OrderDate;

            // In a real app, this would update the order record in a database
            // _orderRepository.UpdateOrder(order);

            Console.WriteLine($"Order {order.Id} completed successfully in {order.ProcessingDuration.TotalSeconds:F1} seconds.");
            _auditLogger.LogOrderCompleted(order.Id, order.TotalAmount);

            // Finalize the order
            FinalizeOrder(order);

            return OrderResult.Success(order.Id, order.TrackingNumber ?? "");
        }
        catch (Exception ex)
        {
            HandleUnexpectedError(order, ex);
            return OrderResult.Failure("An unexpected error occurred during order processing");
        }
    }

    ```

GitHub Copilot エージェントは、コード フロー、ビジネス ロジック、エラー処理パターンを理解する必要がある体系的なリファクタリング タスクに優れています。 リファクタリングを論理的なフェーズに分割することで、各変更の管理とテストが可能になり、元のシステム動作が維持される一方で、コードの編成と保守性が大幅に向上します。

### リファクタリング後の eコマース注文処理コードをテストする

人手でテストと検証を行って、リファクタリング後のコードで意図されるビジネス ロジックと機能が維持されていることを確認します。 成功したリファクタリング プロセスでは、元の実装と同じ動作を生成しながら、コードの構造が改善されている必要があります。

このタスクでは、リファクタリング後のコードをテストして、すべてのビジネス ロジックが維持されていること、および保守性と可読性の向上というリファクタリングの目標が達成されていることを確認します。

そのためには、以下の手順を実行してください。

1. リファクタリング後のアプリケーションを実行して、想定される動作を確認します。

    その出力を、リファクタリングの前に観察した動作と比較します。 次のように、コンソールの出力が同じである必要があります。

    - **テスト 1 (有効な注文)**:支払処理、配送スケジュール、通知を含めて正常に完了する必要があります
    - **テスト 2 (無効なメール)**:同じエラー メッセージで検証が失敗する必要があります
    - **テスト 3 (支払い拒否)**:支払い処理が失敗し、在庫のロールバックがトリガーされる必要があります
    - **テスト 4 (疑わしい注文)**:セキュリティ評価によってフラグが設定されて拒否される必要があります

    リファクタリング後のコードでは、まったく同じ結果が生成され、リファクタリング プロセス全体でビジネス ロジックが保持されていることが示される必要があります。

1. さらにエッジ ケース シナリオを作成してテストし、堅牢性を確認します。

    追加のテスト シナリオを作成して、さまざまなエッジ ケースでエラー処理が正しく動作することを確認します。 **Program.cs** のテスト ケースを一時的に変更して、追加のシナリオをテストできます。

    たとえば、テストの概要を表示するコードの前に、次のコード スニペットを追加できます。

    ```csharp
    // Test with empty items list (should fail validation)
    System.Console.WriteLine("\n--- Test Case 5: Empty Order ---");
    var emptyOrder = CreateSampleOrder("ORD-EMPTY", "test@example.com", "123 Test St",
        new List<OrderItem>(),
        new PaymentInfo { CardNumber = "4111111111111111", CardCVV = "123", CardHolderName = "Test User", ExpiryMonth = "12", ExpiryYear = "2025", BillingAddress = "123 Test St" });

    var result5 = processor.ProcessOrder(emptyOrder);
    testResults.Add($"Test 5: {(result5.IsSuccess ? "FAILED" : "PASSED")} - Should reject empty order");

    // Test with invalid shipping address (should fail validation)
    System.Console.WriteLine("\n--- Test Case 6: Invalid Shipping Address ---");
    var invalidAddressOrder = CreateSampleOrder("ORD-ADDR", "user@example.com", "",
        new List<OrderItem> { new() { ProductId = "BOOK-001", Quantity = 1, Price = 15.99m } },
        new PaymentInfo { CardNumber = "4111111111111111", CardCVV = "123", CardHolderName = "Test User", ExpiryMonth = "12", ExpiryYear = "2025", BillingAddress = "123 Test St" });

    var result6 = processor.ProcessOrder(invalidAddressOrder);
    testResults.Add($"Test 6: {(result6.IsSuccess ? "FAILED" : "PASSED")} - Should reject invalid shipping address");
    ```

    これらの追加テストは、リファクタリング後の検証ロジックでエッジ ケースが正しく処理され、エラー メッセージが元の実装と一致していることを確認するのに役立ちます。

1. アプリケーションを実行してテスト結果を確認します。

    アプリケーションを実行すると、各テスト ケースが合格か不合格かを示すテスト結果がコンソールに表示されます。 テストの実行中に生成されるエラー メッセージまたはログに注意してください。

    たとえば、上記のテスト 5 とテスト 6 のシナリオを追加した場合、新しいテスト出力と更新された概要は次のようになります。

    ```text

    --- Test Case 5: Empty Order ---
    [AUDIT] 2025-08-20 18:28:11.099 UTC | ORDER_PROCESSING_STARTED | Order: ORD-EMPTY | Started processing order for t***@example.com
    [AUDIT] 2025-08-20 18:28:11.100 UTC | VALIDATION_FAILURE | Order: ORD-EMPTY | Empty order items
    
    --- Test Case 6: Invalid Shipping Address ---
    [AUDIT] 2025-08-20 18:28:11.101 UTC | ORDER_PROCESSING_STARTED | Order: ORD-ADDR | Started processing order for u***@example.com
    [AUDIT] 2025-08-20 18:28:11.101 UTC | VALIDATION_FAILURE | Order: ORD-ADDR | Invalid shipping address
    
    === TEST SUMMARY ===
    Test 1: PASSED - ORD-001
    Test 2: PASSED - Should reject invalid email
    Test 3: PASSED - Should reject declined payment
    Test 4: PASSED - Should flag suspicious order
    Test 5: PASSED - Should reject empty order
    Test 6: PASSED - Should reject invalid shipping address

    ```

1. もう一度アプリケーションを実行して、監査ログが引き続き正しく動作することを確認します。

    **order_audit_log.txt** ファイルを調べて、リファクタリングされたメソッド全体で監査ログがまだ正常に機能していることを確認します。 最新のイベントはファイルの末尾にあります。

    監査証跡が完全で、抽出されたすべてのメソッドでログ記録が保持されていることを示している必要があります。

    > **ヒント**:order_audit_log.txt ファイルは、アプリケーションの現在の作業ディレクトリで作成または更新されます。 ECommerce.Console プロジェクトの実行方法によっては、作業ディレクトリが "src/ECommerce.Console" ディレクトリではなく "src/ECommerce.Console/bin/Debug/net9.0" ディレクトリになる場合があります。 監査ファイルを "src/ECommerce.Console" ディレクトリに生成するには、.NET CLI コマンドを使ってターミナルからアプリケーションを実行します。

    次のイベントの種類を注文監査ログ ファイルに格納できます。

    - 注文処理イベント:
        - LogOrderProcessingStarted(string orderId, string email)
        - LogOrderCompleted(string orderId, decimal amount)
    - セキュリティ イベント:
        - LogSecurityEvent(string eventType, string details)
    - 検証イベント:
        - LogValidationFailure(string orderId, string reason)
    - 在庫イベント:
        - LogInventoryIssue(string orderId, string productId, string issue)
        - LogInventoryReserved(string orderId, int itemCount)
    - 支払イベント:
        - LogPaymentProcessed(string orderId, decimal amount, string reference)
        - LogPaymentFailure(string orderId, string reason)
    - 配送イベント:
        - LogShippingScheduled(string orderId, string trackingNumber)
        - LogShippingFailure(string orderId, string reason)
    - 通知イベント:
        - LogNotificationSent(string orderId, string type)
        - LogNotificationFailure(string orderId, string reason)
    - 予期しないエラー:
        - LogUnexpectedError(string orderId, string error)

手動テストでは、リファクタリング作業において、システムの機能を維持しながらコードの構造を改善するという目標が正しく達成されたことを確認します。 リファクタリング後のコードでは、各メソッドが明確で限定的な責任を持つ保守性の高い基盤が提供されるようになり、将来の機能拡張とバグ修正の実装がはるかに容易になっています。

## まとめ

この演習では、GitHub Copilot を使ってアプリケーション内の大きな関数をリファクタリングする方法を学びました。 eコマース注文処理システムを調査し、リファクタリングが必要な大きな関数を特定した後、保守性と可読性が向上するように、GitHub Copilot を使ってモノリシックなメソッドをより小さくて限定的な関数に分割しました。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認してください。 変更を行った場合は、必要に応じてそれを元に戻します。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したサンプル プロジェクト フォルダーをアーカイブまたは削除できます。
