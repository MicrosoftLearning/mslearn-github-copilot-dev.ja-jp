---
lab:
  title: 演習 - GitHub Copilot を使用して単体テストを開発する
  description: Visual Studio Code で GitHub を使用して単体テストの開発を高速化する方法について説明します。
---

# GitHub Copilot を使用して単体テストを開発する

GitHub Copilot を支える大規模言語モデルは、さまざまなコード テスト フレームワークとシナリオでトレーニングされています。 GitHub Copilot は、テスト ケース、テスト メソッド、テスト アサーションとモック、テスト データを生成するための優れたツールです。 この演習では、GitHub Copilot を使って C# アプリケーションの単体テストをより速く開発できるようにします。

この演習の完了に要する時間は約 **25** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です: Git 2.48 以降、.NET SDK 9.0 以降、C# 開発キット拡張機能をインストールした Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

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

## 演習のシナリオ

あなたは地域コミュニティの IT 部署で働く開発者です。 公共図書館を支えるバックエンド システムが火災で失われました。 システムが置き換えられるまで、チームは図書館スタッフが業務を管理できるようにするための一時的なソリューションを開発する必要があります。 チームは、開発プロセスをスピードアップするために GitHub Copilot を選びました。

UnitTests という名前の単体テスト プロジェクトを含む図書館アプリケーションの初期バージョンがあります。 GitHub Copilot を使って、追加の単体テストの開発をより速く行えるようにする必要があります。

この演習には、次のタスクが含まれています。

1. Visual Studio Code で図書館アプリケーションを設定します。

1. UnitTests プロジェクトによって実装される単体テストへのアプローチを確認します。

1. Library.Infrastructure プロジェクトでデータ アクセス クラスのテストを開始するように、UnitTests プロジェクトを拡張します。

## Visual Studio Code で図書館アプリケーションを設定する

既存のアプリケーションをダウンロードし、コード ファイルを展開した後、Visual Studio Code でソリューションを開く必要があります。

図書館アプリケーションを設定するには、次の手順に従います。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. 図書館アプリケーションを含む ZIP ファイルをダウンロードするには、次の URL をブラウザーのアドレス バーに貼り付けます。[GitHub Copilot ラボ - 単体テストの開発](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/AZ2007LabAppM4.zip)

    ZIP ファイルの名前は **AZ2007LabAppM4.zip** です。

1. **AZ2007LabAppM4.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **AZ2007LabAppM4.zip** を右クリックして、**[すべて展開]** を選びます。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. 展開されたファイル フォルダーを開き、**AccelerateDevGHCopilot** フォルダーを Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で **AccelerateDevGHCopilot** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**AccelerateDevGHCopilot** を選択してから、**[フォルダーの選択]** を選択します。

1. Visual Studio Code のソリューション エクスプローラー ビューで、次のソリューション構造を確認します:

    - AccelerateDevGHCopilot\
        - src\
            - Library.ApplicationCore\
            - Library.Console\
            - Library.Infrastructure\
        - tests\
            - UnitTests\

1. ソリューションが正常にビルドされていることを確認します。

    たとえば、ソリューション エクスプローラー ビューで、**AccelerateDevGHCopilot** を右クリックし、次に **[ビルド]** を選択します。

    いくつかの警告が表示されますが、エラーは発生しないはずです。

## UnitTests プロジェクトによって実装される単体テストへのアプローチを確認する

演習のこのセクションでは、GitHub Copilot を使用して、UnitTests プロジェクトによって実装される単体テストのアプローチを調べます。

以下の手順に従って、演習のこのセクションを完了します。

1. ソリューション エクスプローラー ビューで **UnitTests** プロジェクトを展開します。

    既存のコードベースには、次のフォルダー構造を実装する UnitTests プロジェクトが含まれています。

    - UnitTests\
      - ApplicationCore\
        - LoanService\
          - **ExtendLoan.cs**
          - **ReturnLoan.cs**
        - PatronService\
          - **RenewMembership.cs**
      - LoanFactory.cs
      - PatronFactory.cs

    テスト プロジェクトの構造は、**ApplicationCore** プロジェクトの **Services** 部分を反映し、それをサポートしています。

    - ApplicationCore\
      - Services\
        - LoanService.cs:**ExtendLoan** メソッドと **ReturnLoan** メソッドが含まれます。
        - PatronService.cs:**RenewMembership** メソッドが含まれます。

1. チャット ビューを開きます。

1. 次のファイルをチャット コンテキストに追加します。

    (Library.ApplicationCore プロジェクトの下)

    - LoanService.cs
    - PatronService.cs

    (UnitTests プロジェクトの下)

    - ExtendLoan.cs
    - ReturnLoan.cs
    - RenewMembership.cs
    - LoanFactory.cs
    - PatronFactory.cs

1. 既存の単体テスト プロジェクトについて知るには、次のプロンプトを入力します。

    ```plaintext
    @workspace Explain the approach to unit testing that's been implemented in this workspace.
    ```

    このプロンプトは、ワークスペースのコンテキストでの単体テストのアプローチについて説明するように GitHub Copilot に求めます。 関連するファイルをチャット コンテキストに追加すると、応答が向上します。

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

    次の例のような説明が表示されるはずです。

    ```markdown

    The unit testing approach in this workspace follows a structured and comprehensive methodology to ensure the correctness of the application's core business logic. Here's an overview of the approach:
    
    1. **Use of Test Factories**
       - Factories like PatronFactory and LoanFactory are used to create test data. These factories provide reusable methods to generate consistent and meaningful test objects, such as patrons with specific membership statuses or loans with different states (e.g., returned, current, expired).
       - This approach reduces boilerplate code in test cases and ensures that test data is consistent across different tests.
    
    2. **Mocking Dependencies**
       - **NSubstitute** is used to mock dependencies like repositories (IPatronRepository, ILoanRepository). This allows tests to isolate the behavior of the services being tested without relying on actual database or external dependencies.
       - Mocked methods are configured to return specific data or simulate certain behaviors, enabling precise control over test scenarios.
    
    3. **Service-Oriented Testing**
       - Unit tests are focused on the core services (PatronService and LoanService), which encapsulate the business logic of the application.
       - Each service method is tested for various scenarios, including success cases, edge cases, and error conditions.
    
    4. **Comprehensive Test Coverage**
       - Tests cover a wide range of scenarios for each service method:
         - **PatronService.RenewMembership:**
           - Success cases (e.g., renewing with or without loans, expired membership).
           - Failure cases (e.g., patron not found, too early to renew, overdue loans).
         - **LoanService.ReturnLoan:**
           - Success cases (e.g., returning a loan with valid conditions).
           - Failure cases (e.g., loan not found, already returned).
         - **LoanService.ExtendLoan:**
           - Success cases (e.g., extending a valid loan).
           - Failure cases (e.g., loan not found, membership expired, loan already returned or expired).
       - This ensures that all possible paths in the business logic are tested.
    
    5. **Assertions for Expected Behavior**
       - Each test uses assertions to validate the expected outcomes:
         - **Return values**: Ensuring the correct status is returned (e.g., MembershipRenewalStatus.Success, LoanReturnStatus.LoanNotFound).
         - **State changes**: Verifying that object properties (e.g., MembershipEnd, ReturnDate, DueDate) are updated correctly.
         - **Error handling**: Ensuring exceptions or errors are handled gracefully.
    
    6. **Descriptive Test Names**
       - Test methods use descriptive names and Fact attributes with DisplayName to clearly indicate the scenario being tested. This improves readability and makes it easier to understand the purpose of each test.
    
    7. **Focus on Business Logic**
       - Tests are designed to validate the business rules implemented in the services, such as:
         - Not allowing membership renewal if it's too early or if there are overdue loans.
         - Preventing loan extensions for expired memberships or already returned loans.
       - This ensures that the core functionality of the application behaves as expected under various conditions.
    
    8. **Error Simulation**
       - Tests simulate error scenarios, such as repository methods returning null or throwing exceptions, to verify that the services handle these cases appropriately.
    
    9. **Constants for Configurable Logic**
       - Constants like LoanService.ExtendByDays are used in the logic and verified in tests to ensure consistency and maintainability.
    
    **Summary**
    This workspace implements a robust unit testing strategy by leveraging test factories, mocking, and comprehensive test coverage. The focus is on isolating and validating the business logic of core services, ensuring that all possible scenarios are accounted for. This approach enhances the reliability and maintainability of the application.

    ```

1. 現在のアプローチの利点を分析するには、次のプロンプトを入力します。

    ```plaintext
    @workspace What are the benefits of this approach to unit testing?
    ```

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

    対話を続けて、単体テスト アプローチの特定の側面についてさらに詳しく確認することができます。 たとえば、テスト ファクトリを使用する利点、依存関係のモック、またはテストの全体的な構造について質問できます。

1. Library.Infrastructure プロジェクトをテストするために、既存のアプローチをどのように拡張するか少し考えてみます。

    Library.Infrastructure プロジェクトには、ファイル システムを操作して、データを読み込んで保存するデータ アクセス クラスが含まれています。

1. 次のファイルをチャット コンテキストに追加します。

    (Library.ApplicationCore プロジェクトの下)

    - LoanService.cs
    - PatronService.cs

    (UnitTests プロジェクトの下)

    - ExtendLoan.cs
    - ReturnLoan.cs
    - RenewMembership.cs
    - LoanFactory.cs
    - PatronFactory.cs

1. GitHub Copilot に UnitTests プロジェクトの拡張の支援を依頼するには、次のプロンプトを入力します。

    ```plaintext
    @workspace How can I extend the `UnitTests` project to begin testing methods in the `Library.Infrastructure` project? Provide a process overview.
    ```

    このプロンプトは、UnitTests プロジェクトを拡張して、Library.Infrastructure プロジェクトでメソッドのテストを開始するためのプロセスの概要を提供するよう GitHub Copilot に求めます。 応答のコンテキストを提供するために、プロンプトにはいくつかのファイルが含まれています。

1. 少し時間を取って、GitHub Copilot からの応答を確認してください。

    次の例のような説明が表示されるはずです。

    ```markdown

    1. Add Project Reference:

      - Open the UnitTests.csproj file located at tests/UnitTests/UnitTests.csproj.
      - Add a reference to the Library.Infrastructure project.

    2. Identify methods to test:

      - Identify the methods in the **Library.Infrastructure** project that need to be tested. For example, if you want to test the **LoanService** class, identify the methods in the **LoanService** class that need to be tested.

    3. Create Test Classes:

      - Create new test classes in the **tests/UnitTests** directory to test the methods in the **Library.Infrastructure** project. For example, if you want to test **LoanService**, create a file named **LoanServiceTests.cs**.

    4. Write Unit Tests:

      - In the newly created test classes, write unit tests for the methods in the **Library.Infrastructure** project. Use the xUnit framework for writing tests and NSubstitute for mocking dependencies.

    5. Run Tests:

      - Use Visual Studio Code's integrated terminal or the Test Explorer to run the tests.
      - Ensure that the tests are discovered and executed correctly.

    ```

    > **ヒント**:GitHub Copilot の**質問**モードを使って、テストへのアプローチを調べます。 応答を使って、単体テストを計画、開発、または拡張します。

## UnitTests プロジェクトを拡張してデータ アクセス クラスのテストを開始する

**Library.Infrastructure** プロジェクトには、ファイル システムと対話してデータの読み込みと保存を行うデータ アクセス クラスが含まれています。 プロジェクトには、次のクラスが含まれています。

- JsonData: JSON データを読み込んで保存するクラス。
- JsonLoanRepository: ILoanRepository インターフェイスを実装し、JsonData クラスを使用して貸し出しデータを読み込んで保存するクラス。
- JsonPatronRepository: IPatronRepository インターフェイスを実装し、JsonData クラスを使用して利用者データを読み込んで保存するクラス。

### エージェント モードを使用して新しいテスト クラスを作成する

特定のタスクが念頭にあり、Copilot がコードを自律的に編集できるようにしたい場合は、チャット ビューのエージェント モードを使用できます。 たとえば、エージェント モードを使って、ファイルの作成や編集を行ったり、ツールを呼び出してタスクを実行したりできます。 エージェント モードの GitHub Copilot は、必要な作業を自律的に計画し、関連するファイルとコンテキストを決定できます。 その後、コードベースを編集し、ツールを呼び出してユーザーが行った要求を遂行します。

> **注**:エージェント モードは Visual Studio Code でのみ使用できます。 GitHub Copilot を別の環境でお使いの場合は、チャット モードを使って同様のタスクを実行できます。

演習のこのセクションでは、GitHub Copilot のエージェント モードを使って、JsonLoanRepository クラスの GetLoan メソッド用の新しいテスト クラスを作成します。

以下の手順に従って、演習のこのセクションを完了します。

1. チャット ビューで、**[モードの設定]** ボタンを選んでから、**[エージェント]** を選びます。

    > **重要**:エージェント モードでチャット ビューを使用すると、GitHub Copilot は 1 つのタスクを完了するために複数の Premium リクエストを行う場合があります。 Premium リクエストは、ユーザーが開始するプロンプトや、Copilot がユーザーに代わって実行するフォローアップ アクションで使用できます。 使われる Premium リクエストの合計数は、タスクの複雑さ、関連するステップの数、選ばれたモデルによって異なります。

1. JsonLoanRepository.GetLoan メソッド用のテスト クラスを作成する自動化タスクを開始するには、次のプロンプトを入力します。

    ```plaintext

    Add `Infrastructure\JsonLoanRepository` folders to the UnitTests project. Create a class file named `GetLoan.cs` in the `JsonLoanRepository` folder and create a stub class named `GetLoan`. Add a reference to the Library.Infrastructure project inside UnitTests.csproj.

    ```

    このプロンプトは、UnitTests プロジェクトで新しいフォルダー構造とクラス ファイルを作成するよう GitHub Copilot に依頼します。

    - UnitTests\
      - Infrastructure\
        - JsonLoanRepository\
          - GetLoan.cs

    このプロンプトも、GitHub Copilot に、UnitTests.csproj ファイル内の Library.Infrastructure プロジェクトへの参照の追加を求めます。

1. 少し時間を取って、GitHub Copilot からの応答を確認してください。

    チャット ビューとコード エディターで次の更新に注意してください。

    - エージェントは、要求されたタスクを完了するとステータス メッセージを表示します。 最初のタスクは、UnitTests プロジェクトにフォルダー構造を作成することです。 エージェントは、フォルダー構造を作成する前に、いったん停止してユーザーに確認を求める場合があります。

        ![エージェント モードでのチャット ビューを示すスクリーンショット。](./Media/m04-github-copilot-agent-mode-terminal-command-mkdir.png)

    - UnitTests.csproj ファイルがコード エディターで開かれ、次の更新のような編集が行われます。

        ![コード エディターでの UnitTests.csproj ファイルの更新を示すスクリーンショット。](./Media/m04-github-copilot-agent-mode-code-editor-update.png)

1. エージェントがタスクを一時停止し、ターミナルでディレクトリ作成コマンドを実行するアクセス許可を求めた場合は、**[続行]** を選びます。

    **[続行]** を選ぶと、GitHub Copilot は次のアクションを完了します。

    - ターミナルで mkdir コマンドを実行して、UnitTests プロジェクトに **Infrastructure\JsonLoanRepository** フォルダーを作成します。
    - **JsonLoanRepository** フォルダーに **GetLoan.cs** という名前の新しいファイルを作成します。

1. 少し時間を取って、更新の内容を確認してください。

    次のような更新がエディターに表示されるはずです。

    - **UnitTests** プロジェクトに、**Library.Infrastructure.csproj** への参照が含まれるようになります。
    - **GetLoan.cs** ファイルは、**Infrastructure\JsonLoanRepository** フォルダーに作成されます。

1. チャット ビューですべての変更を受け入れるには、**[保持する]** を選びます。

1. ソリューション エクスプローラー ビューで、**Infrastructure\JsonLoanRepository** フォルダー構造を展開します。

    次のフォルダー構造が表示されます:

    - UnitTests\
      - Infrastructure\
        - JsonLoanRepository\
          - GetLoan.cs

### 編集モードを使用して GetLoan メソッド用の単体テストを作成する

演習のこのセクションでは、GitHub Copilot の編集モードを使って、**JsonLoanRepository** クラスの **GetLoan** メソッド用の単体テストを作成します。

以下の手順に従って、演習のこのセクションを完了します。

1. チャット ビューで、**[モードの設定]** ボタンを選んでから、**[編集]** を選びます。

    編集モードを使って、選んだファイルを更新します。 応答は、コード エディターにコードの提案として表示されます。

1. **JsonLoanRepository.cs** ファイルを開きます。

    **JsonLoanRepository.cs** は **src/Library.Infrastructure/Data/** フォルダーにあります。

1. 少し時間を取って **JsonLoanRepository.cs** ファイルを確認してください。

    ```csharp
    using Library.ApplicationCore;
    using Library.ApplicationCore.Entities;
    
    namespace Library.Infrastructure.Data;
    
    public class JsonLoanRepository : ILoanRepository
    {
        private readonly JsonData _jsonData;
    
        public JsonLoanRepository(JsonData jsonData)
        {
            _jsonData = jsonData;
        }
    
        public async Task<Loan?> GetLoan(int id)
        {
            await _jsonData.EnsureDataLoaded();
    
            foreach (Loan loan in _jsonData.Loans!)
            {
                if (loan.Id == id)
                {
                    Loan populated = _jsonData.GetPopulatedLoan(loan);
                    return populated;
                }
            }
            return null;
        }
    
        public async Task UpdateLoan(Loan loan)
        {
            Loan? existingLoan = null;
            foreach (Loan l in _jsonData.Loans!)
            {
                if (l.Id == loan.Id)
                {
                    existingLoan = l;
                    break;
                }
            }
    
            if (existingLoan != null)
            {
                existingLoan.BookItemId = loan.BookItemId;
                existingLoan.PatronId = loan.PatronId;
                existingLoan.LoanDate = loan.LoanDate;
                existingLoan.DueDate = loan.DueDate;
                existingLoan.ReturnDate = loan.ReturnDate;
    
                await _jsonData.SaveLoans(_jsonData.Loans!);
    
                await _jsonData.LoadData();
            }
        }
    }
    ```

1. **JsonLoanRepository** クラスに関する次の詳細に注意してください。

    - **JsonLoanRepository** クラスには、次の 2 つのメソッドが含まれています: **GetLoan** と **UpdateLoan**。
    - **JsonLoanRepository** クラスは、**JsonData** オブジェクトを使って貸し出しデータの読み込みと保存を行います。

1. 少し時間を取って、**GetLoan** テスト クラスのフィールドとコンストラクターの要件を確認してください。

    **JsonLoanRepository.GetLoan** メソッドは、呼び出されるときに貸し出し ID パラメーターを受け取ります。 このメソッドは、**_jsonData.EnsureDataLoaded** を使って最新の JSON データを取得し、**_jsonData.Loans** を使って一致する貸し出しを検索します。 このメソッドは、一致する貸し出し ID が見つかると、設定された貸し出しオブジェクト (**populated**) を返します。 一致する貸し出し ID が見つからない場合は、メソッドは **null** を返します。

    GetLoan 単体テストの場合:

    - モック貸し出しリポジトリ オブジェクト (**_mockLoanRepository**) を使って、一致する ID が見つかったケースをテストできます。 検索する ID を使用してモックを読み込みます。 **ReturnLoanTest** クラスでは、**ILoanRepository** インターフェイスのモックを作成し、モック貸し出しリポジトリ オブジェクトをインスタンス化する方法が示されています。

    - 非モック貸し出しリポジトリ オブジェクト (**_jsonLoanRepository**) を使うと、一致する ID が見つからないケースをテストできます。 ファイルに含まれていないことがわかっている貸し出し ID を指定するだけです (100 を超えるものが機能するはずです)。

    - 非モック **JsonLoanRepository** オブジェクトを作成するには、**JsonData** オブジェクトが必要です。 **UnitTests** プロジェクトは **ConsoleApp** プロジェクトによって作成された **JsonData** オブジェクトにアクセスできないため、**IConfiguration** インターフェイスを使ってそれを作成する必要があります。

1. GetLoan.cs テスト ファイルを開いて GetLoan クラスを選びます。

1. 次のファイルをチャット コンテキストに追加します。

    (Library.ApplicationCore プロジェクトの下)

    - LoanService.cs

    (Library.Infrastructure プロジェクトの下)

    - JsonData.cs
    - JsonLoanRepository.cs

    (UnitTests プロジェクトの下)

    - ReturnLoan.cs
    - LoanFactory.cs

1. 次のプロンプトを入力します。

    ```plaintext

    #codebase Create fields and a class constructor for the `GetLoan.cs` file. The class will be used to create unit tests for the GetLoan method in the `JsonLoanRepository.cs` file. Create the following private readonly fields: `_mockLoanRepository`, `_jsonLoanRepository`, `_configuration`, and `_jsonData`. Instantiate the fields in the `GetLoanTest` constructor. Use `ConfigurationBuilder` to create a `_configuration` object that can be used to instantiate the JsonData object.

    ```

    このプロンプトは、GitHub Copilot にフィールドとクラス コンストラクターを提案するように求めます。

1. 少し時間を取って、GitHub Copilot からの応答を確認してください。

    次のコード スニペットのようなコードの提案が表示される可能性があります。

    ```csharp
    using NSubstitute;
    using Library.ApplicationCore;
    using Library.ApplicationCore.Entities;
    using Library.ApplicationCore.Interfaces;
    using Library.Infrastructure.Data;
    using Microsoft.Extensions.Configuration;
    
    namespace UnitTests.Infrastructure.JsonLoanRepository;
    
    public class GetLoanTest
    {
        private readonly ILoanRepository _mockLoanRepository;
        private readonly JsonLoanRepository _jsonLoanRepository;
        private readonly IConfiguration _configuration;
        private readonly JsonData _jsonData;
    
        public GetLoanTest()
        {
            _mockLoanRepository = Substitute.For<ILoanRepository>();
            _configuration = new ConfigurationBuilder()
                .AddJsonFile("appsettings.json")
                .Build();
            _jsonData = new JsonData(_configuration);
            _jsonLoanRepository = new JsonLoanRepository(_jsonData);
        }
    
        // Add test methods here
    }
    ```

1. チャット ビューで、すべての更新内容を受け入れるには、**[保持する]** を選択します。

1. 更新を受け入れた後、次の問題を調べます。

    > **注**:次のステップのサンプル コードでは、次の問題を修正する更新が示されています。
  
    - **UnitTests.Infrastructure.JsonLoanRepository** 名前空間と、コードで指定された **JsonLoanRepository** 型の間に矛盾がある場合は、GetLoans.cs で名前空間を更新して矛盾を解消する必要があります。 **ReturnLoan.cs** と **RenewMembership.cs** ファイルで使われているパターンに従います。

    - **ILoanRepository** がコードで認識されない場合は、**Library.ApplicationCore** の **using** ディレクティブをファイルの先頭に追加することが必要な場合があります。

    - **_configuration** オブジェクトが正しくインスタンス化されない場合は、**ConfigurationBuilder** を含むコード行の更新が必要な場合があります。 コードを簡略化して **_configuration = new ConfigurationBuilder().Build();** を使用できます。

    - GitHub Copilot によって **using Library.ApplicationCore.Interfaces** が提案された場合は、それをファイルの先頭から削除できます。

1. 前のステップで明らかになった問題に対処するには、**GetLoan.cs** ファイルを更新します。

    次のコード スニペットを参照として使用できます。

    ```csharp
    using NSubstitute;
    using Library.ApplicationCore;
    using Library.ApplicationCore.Entities;
    using Library.Infrastructure.Data;
    using Microsoft.Extensions.Configuration;
    
    namespace UnitTests.Infrastructure.JsonLoanRepositoryTests;
    
    public class GetLoanTest
    {
        private readonly ILoanRepository _mockLoanRepository;
        private readonly JsonLoanRepository _jsonLoanRepository;
        private readonly IConfiguration _configuration;
        private readonly JsonData _jsonData;
    
        public GetLoanTest()
        {
            _mockLoanRepository = Substitute.For<ILoanRepository>();
            _configuration = new ConfigurationBuilder().Build();
            _jsonData = new JsonData(_configuration);
            _jsonLoanRepository = new JsonLoanRepository(_jsonData);
        }
    
    }
    ```

1. 次のファイルをチャット コンテキストに追加します。

    (Library.ApplicationCore プロジェクトの下)

    - LoanService.cs
    - Loans.json。

    (Library.Infrastructure プロジェクトの下)

    - JsonData.cs
    - JsonLoanRepository.cs

    (UnitTests プロジェクトの下)

    - ReturnLoan.cs
    - LoanFactory.cs

1. **GetLoan.cs** ファイルの内容を選んでから、チャット ビューに次のプロンプトを入力します。

    ```plaintext
    @workspace Update the selection to include a unit test for the `JsonLoanRepository.GetLoan` method. The unit test should test the case where a loan ID is found in the data. Use `_mockLoanRepository` to arrange the expected return loan. Use `_jsonLoanRepository` to return an actual loan. Asserts should verify that the return loan ID matches the expected loan ID. Use a loan ID that exists in the `Loans.json` file.
    ```

    このプロンプトは、GitHub Copilot に対して、**JsonLoanRepository.GetLoan** メソッドの単体テストを提案するように求めます。 単体テストでは、データに貸し出し ID が見つかったケースをテストする必要があります。 このテストでは、**_mockLoanRepository** を使って、想定される返却貸出、実際の貸出を返却するための **_jsonLoanRepository**、返却貸出 ID と想定される貸出 ID の一致を確認するためのアサートを用意する必要があります。 貸出 ID は、**Loans.json** ファイルに存在するはずです。

1. 少し時間を取って、GitHub Copilot によって提案された更新を確認してください。

    次のコード スニペットのようなコード候補が表示されます。

    ```csharp
    using NSubstitute;
    using Library.ApplicationCore;
    using Library.ApplicationCore.Entities;
    using Library.Infrastructure.Data;
    using Microsoft.Extensions.Configuration;
    using Xunit;
    
    namespace UnitTests.Infrastructure.JsonLoanRepositoryTests;
    
    public class GetLoanTest
    {
        private readonly ILoanRepository _mockLoanRepository;
        private readonly JsonLoanRepository _jsonLoanRepository;
        private readonly IConfiguration _configuration;
        private readonly JsonData _jsonData;
    
        public GetLoanTest()
        {
            _mockLoanRepository = Substitute.For<ILoanRepository>();
            _configuration = new ConfigurationBuilder().Build();
            _jsonData = new JsonData(_configuration);
            _jsonLoanRepository = new JsonLoanRepository(_jsonData);
        }
    
        [Fact(DisplayName = "JsonLoanRepository.GetLoan: Returns loan when loan ID is found")]
        public async Task GetLoan_ReturnsLoanWhenLoanIdIsFound()
        {
            // Arrange
            var loanId = 1; // Use a loan ID that exists in the Loans.json file
            var expectedLoan = new Loan
            {
                Id = loanId,
                BookItemId = 17,
                PatronId = 22,
                LoanDate = DateTime.Parse("2023-12-08T00:40:43.1808862"),
                DueDate = DateTime.Parse("2023-12-22T00:40:43.1808862"),
                ReturnDate = null
            };

            _mockLoanRepository.GetLoan(loanId).Returns(expectedLoan);

            // Act
            var actualLoan = await _jsonLoanRepository.GetLoan(loanId);
    
            // Assert
            Assert.NotNull(actualLoan);
            Assert.Equal(expectedLoan.Id, actualLoan?.Id);
        }
    }
    ```

1. チャット ビューで、すべての更新内容を受け入れるには、**[保持する]** を選択します。

    コードで **Loan** クラスが認識されない場合は、GetLoan.cs ファイルの先頭に **using Library.ApplicationCore.Entities** ステートメントがあることを確認します。 **Loan** クラスは **Library.ApplicationCore.Entities** 名前空間にあります。

1. **AccelerateDevGitHubCopilot** ソリューションをビルドして、エラーがないことを確認します。

1. GitHub Copilot のオートコンプリート機能を使用して、貸し出し ID が見つからない場合のテストを作成します。

    **GetLoan_ReturnsLoanWhenLoanIdIsFound** メソッドの後に空白行を作成します。

    オートコンプリートの提案を受け入れて、新しいテスト メソッドを作成します。

    > **注**:コード入力候補は一度に 1 行ずつ表示される場合があります。 完成した単体テスト コードを取得するには、**Tab** または **Enter** キーを数回押すことが必要な場合があります。

1. 少し時間を取って、新しい単体テストを確認してください。

    次のコード スニペットのような単体テストが提案されているはずです。

    ```csharp

        [Fact(DisplayName = "JsonLoanRepository.GetLoan: Returns null when ID is not found")]
        public async Task GetLoan_ReturnsNullWhenIdIsNotFound()
        {
            // Arrange
            var loanId = 999; // Loan ID that does not exist in Loans.json

            _mockLoanRepository.GetLoan(loanId).Returns((Loan?)null);

            // Act
            var actualLoan = await _jsonLoanRepository.GetLoan(loanId);

            // Assert
            Assert.Null(actualLoan);
        }

    ```

    GitHub Copilot のオートコンプリート機能は、必要ない場合でも想定される貸出をモックする場合があるため、次のコード スニペットが表示されることがあります。

    ```csharp
    [Fact(DisplayName = "JsonLoanRepository.GetLoan: Returns null when loan ID is not found")]
    public async Task GetLoan_ReturnsNullWhenLoanIdIsNotFound()
    {
        // Arrange
        var loanId = 999; // Use a loan ID that does not exist in the Loans.json file
        var expectedLoan = new Loan { Id = loanId, BookItemId = 101, PatronId = 202, LoanDate = DateTime.Now, DueDate = DateTime.Now.AddDays(14) };
        _mockLoanRepository.GetLoan(loanId).Returns(expectedLoan);

        // Act
        var actualLoan = await _jsonLoanRepository.GetLoan(loanId);

        // Assert
        Assert.Null(actualLoan);
    }

    ```

    想定される貸出をモックするコードは削除できますが、**Loans.json** ファイルに存在しない貸出 ID が必要です。

    "Returns null when loan ID is not found" 単体テストで、データ セットに含まれない **loanId** 値が割り当てられることを確認します。

1. 単体テストでは JSON データ ファイルへのアクセスが必要であることに注意してください。

    **JsonLoanRepository.GetLoan** メソッドは、**JsonData** オブジェクトを使って貸出データの読み込みと保存を行います。

    JSON データ ファイルは、**Library.Console\Json** フォルダーにあります。 **UnitTests.csproj** ファイルを更新して、これらのファイルをテスト プロジェクトに含める必要があります。

1. 次の XML スニペットを **UnitTests.csproj** ファイルに追加します。

    ```xml
    <ItemGroup>
        <None Include="..\..\src\Library.Console\Json\**\*">
            <Link>Json\%(RecursiveDir)%(FileName)%(Extension)</Link>
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    これにより、テストの実行時に JSON データ ファイルが出力ディレクトリにコピーされます。

## 単体テストを実行する

**JsonLoanRepository** クラスの単体テストを実行するには、いくつかの方法があります。 Visual Studio Code のテスト エクスプローラー、統合ターミナル、または **dotnet test** コマンドを使用できます。

以下の手順に従って、演習のこのセクションを完了します。

1. GetLoans.cs ファイルがエディターで開かれていることを確認します。

1. ソリューションをビルドし、エラーがないことを確認します。

    **AccelerateDevGitHubCopilot** を右クリックし、**[ビルド]** を選択します。

1. テスト メソッドの左側にある "緑色の再生ボタン" に注目してください。

1. Visual Studio Code のテスト エクスプローラー ビューを開きます。

    テスト エクスプローラー ビューを開くには、左側のアクティビティ バーにあるビーカー型のアイコンを選択します。 テスト エクスプローラーには、ユーザー インターフェイスで [テスト] というラベルが付けられています。

    テスト エクスプローラーは、ワークスペース内のすべてのテスト ケースを表示するツリー ビューです。 テスト ケースを実行/デバッグし、テスト エクスプローラーを使用してテスト結果を表示できます。

1. **UnitTests** とその下のノードを展開して、**GetLoanTest** を見つけます。

1. 次を実行します: **JsonLoanRepository.GetLoan:Returns loan when loan ID is found** テスト ケース。

1. テスト結果がテスト エクスプローラー ビューとコード エディターに表示されることに注意してください。

    テストに合格したことを示す緑色のチェックマークが表示されます。

1. コード エディターを使って、次を実行します: **JsonLoanRepository.GetLoan:Returns null when loan ID is not found** テスト ケース。

    コード エディターからテストを実行するには、テスト メソッドの左側にある緑色の再生ボタンを選びます。

    テスト結果がテスト エクスプローラー ビューとコード エディターに表示されることに注意してください。

    **JsonLoanRepository.GetLoan:Returns null when loan ID is not found** テストに合格していることを確認します。 両方のテストの左側に緑色のチェックマークが表示されているはずです。

## まとめ

この演習では、C# アプリケーションで GitHub Copilot を使って単体テストをより速く開発する方法を学びました。 GitHub Copilot のチャット ビューを質問モード、エージェント モード、編集モードで使いました。 質問モードを使って既存の単体テストのアプローチを調べ、エージェント モードを使ってプロジェクト フォルダーと新しいテスト クラスを作成し、編集モードを使って単体テストを作成しました。 また、GitHub Copilot のコード入力候補機能を使って単体テストを作成しました。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認してください。 変更を加えた場合は、ここで元に戻します。
