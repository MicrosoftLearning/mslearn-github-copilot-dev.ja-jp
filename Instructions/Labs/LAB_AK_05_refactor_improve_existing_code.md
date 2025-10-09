---
lab:
  title: 演習 - GitHub Copilot を使用して既存のコードをリファクタリングする
  description: Visual Studio Code で GitHub Copilot を使用し、既存のコード セクションをリファクタリングして改善する方法について説明します。
---

# GitHub Copilot を使用して既存のコードをリファクターする

GitHub Copilot を使用すると、コードベース全体を評価し、コードのリファクターと改善に役立つ更新の提案を受けることができます。 この演習では、GitHub Copilot を使用し、C# アプリケーションの指定されたセクションをリファクタリングして、同時にコードの品質、信頼性、パフォーマンス、およびセキュリティを向上させます。

この演習の所要時間は約 **30** 分です。

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

あなたは地域コミュニティの IT 部署で働く開発者です。 公共図書館を支えるバックエンド システムが火災で失われました。 システムが置き換えられるまで、チームは図書館スタッフが業務を管理できるようにするための一時的なソリューションを開発する必要があります。 開発プロセスを加速するために GitHub Copilot が選択されました。

図書館アプリケーションの初期バージョンをレビューのために提出しました。 レビュー チームが、コードの品質、パフォーマンス、読みやすさ、保守容易性、セキュリティを向上させる機会を特定しました。

次の更新作業があなたに割り当てられます。

1. リフレクションではなくディクショナリを使用するように EnumHelper クラスをリファクタリングします。

    - 静的ディクショナリを使用すると、パフォーマンスが向上します (リフレクションのオーバーヘッドがなくなります)。
    - また、リフレクションを排除すると、コードの可読性、保守性、セキュリティも向上します。

1. foreach ループではなく LINQ (統合言語クエリ) を使用するようにデータ アクセス メソッドをリファクタリングします。

    - LINQ を使用すると、コレクション、データベース、XML ドキュメントのクエリを実行する、より簡潔で読みやすい方法が提供されます。
    - LINQ を使用すると、コードの可読性、保守性、パフォーマンスを向上させることができます。

この演習には、次のタスクが含まれています。

1. Visual Studio Code で図書館アプリケーションを設定します。
1. 質問モードと編集モードでチャット ビューを使用してコードを分析し、リファクターします。
1. インライン チャットおよび編集モードとエージェント モードのチャット ビューを使用してコードをリファクタリングします。

## Visual Studio Code で図書館アプリケーションを設定する

既存のアプリケーションをダウンロードし、コード ファイルを抽出して、Visual Studio Code でそのソリューションを開く必要があります。

図書館アプリケーションを設定するには、次の手順に従います。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. 図書館アプリケーションを含む ZIP ファイルをダウンロードするには、次の URL をブラウザーのアドレス バーに貼り付けます。[GitHub Copilot ラボ - 既存コードのリファクタリング](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/AZ2007LabAppM5.zip)

    zip ファイルの名前は **AZ2007LabAppM5.zip** です。

1. **AZ2007LabAppM5.zip** ファイルからファイルを抽出します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **AZ2007LabAppM5.zip** を右クリックし、**[すべて抽出]** を選択します。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. 抽出したファイルのフォルダーを開き、**AccelerateDevGHCopilot** フォルダーを、アクセスしやすい場所 (Windows デスクトップ フォルダーなど) にコピーします。

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

    いくつかの警告が表示されますが、エラーは表示されないはずです。

## 質問モードと編集モードのチャット ビューを使用して、コードを分析およびリファクタリングする

リフレクションは、実行時にオブジェクトを検査および操作できる強力なコーディング機能です。 ただし、リフレクションには時間がかかる可能性があり、リフレクションに関連する潜在的なセキュリティ リスクを考慮する必要があります。

以下を実行する必要があります。

1. ワークスペースを分析して、割り当てられたタスクに対処する方法を調査します。
1. リフレクションではなくディクショナリを使用するように EnumHelper クラスをリファクタリングします。

### 質問モードのチャット ビューを使用して EnumHelper クラスを分析する

GitHub Copilot のチャット ビューには、次の 3 つのモードがあります。**質問**、**編集**、および**エージェント**。 各モードは、GitHub Copilot のさまざまな操作のために設計されています。

- **質問**:このモードを使用して、コードベースについて GitHub Copilot に質問します。 コードの説明、変更の提案、コードベースに関する情報の提供を GitHub Copilot に依頼できます。
- **編集**:選択したコード ファイルを編集するには、このモードを使用します。 GitHub Copilot を使用して、コードをリファクターし、コメントを追加し、コードにその他の変更を加えることができます。
- **エージェント**:このモードを使用して、GitHub Copilot をエージェントとして実行します。 GitHub Copilot を使用して、コマンドの実行、コードの実行、あるいはその他の各種タスクをワークスペース内で実行することができます。

演習のこのセクションでは、質問モードのチャット ビューを使用して、割り当てられたコーディングを分析します。

以下の手順に従って、演習のこのセクションを完了します。

1. ソリューション エクスプローラー ビューで、**Library.ApplicationCore** フォルダーを展開してから、**Enums** フォルダーを展開します。

1. EnumHelper.cs ファイルを開き、既存のコードを確認します。

    ```csharp
    using System.ComponentModel;
    using System.Reflection;
    
    namespace Library.ApplicationCore.Enums;
    
    public static class EnumHelper
    {
        public static string GetDescription(Enum value)
        {
            if (value == null)
                return string.Empty;
    
            FieldInfo fieldInfo = value.GetType().GetField(value.ToString())!;
    
            DescriptionAttribute[] attributes =
                (DescriptionAttribute[])fieldInfo.GetCustomAttributes(typeof(DescriptionAttribute), false);
    
            if (attributes != null && attributes.Length > 0)
            {
                return attributes[0].Description;
            }
            else
            {
                return value.ToString();
            }
        }
    }
    ```

1. GitHub Copilot チャット ビューを開きます。

    チャット ビューには、GitHub Copilot と対話するための管理された会話型インターフェイスが用意されています。

    Visual Studio Code ウィンドウ上部の検索テキスト ボックスのすぐ右側にある **[チャットの切り替え]** ボタンを使用して、チャット ビューのオープンとクローズを切り替えることができます。

    ![Copilot の [チャットの切り替え] ボタンを示すスクリーンショット。](./Media/m01-github-copilot-toggle-chat.png)

    キーボード ショートカット **Ctrl + Alt + I** を使用してチャット ビューを切り替えることもできます。

1. チャット ビューは既定では**質問**モードで開かれることに注意します。

    現在のチャット モードは、チャット ビューの右下隅の近くに表示されます。 チャット応答がチャット ビューに表示されるのは、**質問**モードで作業しているときです。

1. EnumHelper.cs ファイル内のコードを選択します。

1. 次のプロンプトを確認してから送信します。

    ```plaintext
    @workspace Explain how the GetDescription method uses reflection to assign the return value.
    ```

1. 少し時間を取って応答を確認します。

    **GetDescription** メソッドは、リフレクションを使用して、**value** という名前の列挙型パラメーターの説明属性を取得します。

    このメソッドは、**value** パラメーターが null かどうかを確認します。 該当する場合、メソッドは空の文字列を返します。 該当しない場合は、リフレクションを使用して列挙値のフィールド情報を取得し、**DescriptionAttribute** 型の属性を取得します。 属性が見つかった場合は、説明を返します。それ以外の場合は、列挙値の文字列表現を返します。

1. 次のプロンプトを確認してから送信します。

    ```plaintext
    @workspace Which files in this workspace are used to store the enum values passed to the GetDescription method?
    ```

    応答によって、Enums フォルダーを確認するように指示されます。 列挙値は、**LoanExtensionStatus**、**LoanReturnStatus** および **MembershipRenewalStatus** ファイルに定義されています。

1. チャット コンテキストに次のファイルを追加します。

    - EnumHelper.cs
    - LoanExtensionStatus.cs
    - LoanReturnStatus.cs
    - MembershipRenewalStatus.cs

    ドラッグ アンド ドロップ操作を使用して、Visual Studio Code のエクスプローラーからチャット ビューにファイルを追加できます。 チャット ビューの **[コンテキストの追加]** ボタンを使用して、ファイルやその他のリソースを追加することもできます。

    > **注**:チャット コンテキストにファイルを追加すると、GitHub Copilot は応答を生成するときにそれらのファイルを考慮します。 プロンプトに関連付けられているコンテキストを GitHub Copilot が理解すると、応答の関連性と正確性が向上します。

1. 次のプロンプトを確認してから送信します。

    ```plaintext

    @workspace I need to refactor the `EnumHelper` class and remove any code that uses reflection. Use static dictionaries to supply enum description attributes. Use a separate dictionary for each enum. The dictionaries should use values from the `LoanExtensionStatus.cs`, `LoanReturnStatus.cs`, and `MembershipRenewalStatus.cs` files. Explain how to update the EnumHelper class using dictionaries and show me the updated code.

    ```

    プロンプトを記述するときは、明確さとコンテキストが重要です。 チャット参加者、スラッシュ コマンド、チャット変数を使用すると、GitHub Copilot が理解できる方法でコンテキストを定義できます。

    GitHub Copilot に問題の解決方法を尋ねるプロンプトを記述するときは、解決しようとしている問題から始めます。 簡潔な文を使用して、詳細を記述し、制約を指定し、リソースを識別します。 最後に、応答に含める内容を必ず GitHub Copilot に指示します。

    このケースでは、問題/目標の説明でプロンプトを開始します。 **EnumHelper** クラスをリファクタリングし、リフレクションを使用するすべてのコードを削除する必要があることを、GitHub Copilot に伝えます。 ディクショナリを使用して列挙型の説明属性を指定しようとしていることを GitHub Copilot に伝えます。 列挙型ごとに個別のディクショナリが必要であることを指定します。また、名前を指定したファイルを列挙値のソースとして使用する必要があることを GitHub Copilot に伝えます。 最後に、**EnumHelper** クラスの更新方法を説明するよう GitHub Copilot に依頼し、更新されたコードを含むコード サンプルを要求します。

1. 少し時間を取って、GitHub Copilot から提供された応答を確認してください。

    応答は次のマークダウンとコード サンプルのようになります。

    ```markdown

    To refactor the EnumHelper class and remove reflection, you can create static dictionaries for each enum type. These dictionaries will map each enum value to its description. The dictionaries will be initialized with the descriptions defined in the Description attributes of the enums.

    Here's how to update the **EnumHelper** class:

    1. Create a static dictionary for each enum (LoanExtensionStatus, LoanReturnStatus, and MembershipRenewalStatus).
    2. Populate these dictionaries with the enum values as keys and their descriptions as values.
    3. Update the GetDescription method to retrieve the description from the appropriate dictionary based on the enum type.

    Here’s the updated code:

    ```

    ```csharp

    using System.Collections.Generic;
    
    namespace Library.ApplicationCore.Enums;
    
    public static class EnumHelper
    {
        private static readonly Dictionary<LoanExtensionStatus, string> LoanExtensionStatusDescriptions = new()
        {
            { LoanExtensionStatus.Success, "Book loan extension was successful." },
            { LoanExtensionStatus.LoanNotFound, "Loan not found." },
            { LoanExtensionStatus.LoanExpired, "Cannot extend book loan as it already has expired. Return the book instead." },
            { LoanExtensionStatus.MembershipExpired, "Cannot extend book loan due to expired patron's membership." },
            { LoanExtensionStatus.LoanReturned, "Cannot extend book loan as the book is already returned." },
            { LoanExtensionStatus.Error, "Cannot extend book loan due to an error." }
        };
    
        private static readonly Dictionary<LoanReturnStatus, string> LoanReturnStatusDescriptions = new()
        {
            { LoanReturnStatus.Success, "Book was successfully returned." },
            { LoanReturnStatus.LoanNotFound, "Loan not found." },
            { LoanReturnStatus.AlreadyReturned, "Cannot return book as the book is already returned." },
            { LoanReturnStatus.Error, "Cannot return book due to an error." }
        };
    
        private static readonly Dictionary<MembershipRenewalStatus, string> MembershipRenewalStatusDescriptions = new()
        {
            { MembershipRenewalStatus.Success, "Membership renewal was successful." },
            { MembershipRenewalStatus.PatronNotFound, "Patron not found." },
            { MembershipRenewalStatus.TooEarlyToRenew, "It is too early to renew the membership." },
            { MembershipRenewalStatus.LoanNotReturned, "Cannot renew membership due to an outstanding loan." },
            { MembershipRenewalStatus.Error, "Cannot renew membership due to an error." }
        };
    
        public static string GetDescription<TEnum>(TEnum value) where TEnum : Enum
        {
            return value switch
            {
                LoanExtensionStatus loanExtensionStatus => LoanExtensionStatusDescriptions[loanExtensionStatus],
                LoanReturnStatus loanReturnStatus => LoanReturnStatusDescriptions[loanReturnStatus],
                MembershipRenewalStatus membershipRenewalStatus => MembershipRenewalStatusDescriptions[membershipRenewalStatus],
                _ => value.ToString()
            };
        }
    }

    ```

    ```markdown

    Explanation:
    
    1. Static Dictionaries: Each dictionary is specific to an enum type and maps enum values to their descriptions.
    2. Generic Method: The GetDescription method is now generic and uses a switch expression to determine which dictionary to use based on the enum type.
    3. No Reflection: The use of reflection is completely removed, improving performance and type safety.
    
    This approach ensures that the descriptions are statically defined and avoids the runtime overhead of reflection.

    ```

1. チャット ビューで、応答に含まれるコード サンプルの上にマウス ポインターを置きます。

1. コード スニペットの右上隅に表示される 3 つのボタンに注目してください。

1. 各ボタンの上にマウス ポインターを置くと、アクションを説明するヒントが表示されます。

    最初の 2 つのボタンを使用すると、エディターにコードがコピーされます。 3 つ目のボタンを使用すると、クリップボードにコードがコピーされます。

> **注**:質問モードを使用して、**EnumHelper** クラスを更新することができました。 ただし、編集モードでは、コード エディター内で直接コードがリファクタリングされ、更新内容を受け入れるためのその他のオプションが提供されます。

### 編集モードのチャット ビューを使用して EnumHelper クラスをリファクタリングする

チャット ビューの編集モードは、ワークスペース内でコードを編集するために設計されています。 編集モードを選択して、コードをリファクターし、コメントを追加し、その他のコード変更を行うことができます。

1. チャット ビューで **[モードの設定]** を選択し、**[編集]** を選択します。

    編集モードで新しいセッションを開始するように求められたら、**[はい]** を選択します。

    **編集**モードでは、GitHub Copilot の応答はコード エディター内にコード更新の提案として表示されます。 一般に編集モードが使用されるのは、新機能の実装、バグの修正またはコードのリファクタリング時です。

1. チャット コンテキストに次のファイルを追加します。

    - EnumHelper.cs
    - LoanExtensionStatus.cs
    - LoanReturnStatus.cs
    - MembershipRenewalStatus.cs

1. 次のプロンプトを確認してから送信します。

    ```plaintext

    #codebase I need to refactor the `EnumHelper` class and remove any code that uses reflection. Use static dictionaries to supply enum description attributes. Use a separate dictionary for each enum. The dictionaries should use values from the `LoanExtensionStatus.cs`, `LoanReturnStatus.cs`, and `MembershipRenewalStatus.cs` files.

    ```

    このプロンプトは、列挙型の説明属性を割り当てるためにリフレクションではなくディクショナリを使用するように **EnumHelper** クラスをリファクタリングすることを GitHub Copilot に指示します。 これは、各列挙型に別のディクショナリを使用することと、列挙値のソースとして特定のファイルを使用することを指定しています。

1. 少し時間を取って、提案されたコード更新を確認します。

    提案された更新内容を見て、列挙値が **LoanExtensionStatus.cs**、**LoanReturnStatus.cs** および **MembershipRenewalStatus.cs** ファイルに基づいていることを確認します。

    列挙型の各ファイルを開き、ディクショナリ内の列挙値が正しいことを確認できます。 不整合が見つかった場合は、GitHub Copilot で列挙型の各ディクショナリを個別に更新します。 たとえば、**LoanExtensionStatus** 列挙型に対して次のプロンプトを使用できます。

    ```plaintext

    #codebase Use the description values in LoanExtensionStatus.cs to update the LoanExtensionStatus dictionary in the EnumHelper class. Provide the updated code for the LoanExtensionStatus dictionary in the EnumHelper class.

    ```

1. チャット ビューで、すべての更新内容を受け入れるには、**[保持する]** を選択します。

    コード エディター タブの下部付近にある [チャット編集] ツール バーを使用して、コードの更新内容を承諾または拒否することもできます。

1. 少し時間を取って、更新された **GetDescription** メソッドを確認してください。

    GitHub Copilot は、リフレクションの代わりにパターン マッチングと静的ディクショナリを使用するように **GetDescription** メソッドを更新したはずです。 更新されたメソッドは次の例のようになります。

    ```csharp

    public static string GetDescription(Enum value)
    {
        if (value == null)
            return string.Empty;

        // Use type checks to select the correct dictionary
        if (value is LoanExtensionStatus les && LoanExtensionStatusDescriptions.TryGetValue(les, out var lesDesc))
            return lesDesc;
        if (value is LoanReturnStatus lrs && LoanReturnStatusDescriptions.TryGetValue(lrs, out var lrsDesc))
            return lrsDesc;
        if (value is MembershipRenewalStatus mrs && MembershipRenewalStatusDescriptions.TryGetValue(mrs, out var mrsDesc))
            return mrsDesc;

        return value.ToString();
    }

    ```

    または

    ```csharp

    public static string GetDescription<TEnum>(TEnum value) where TEnum : Enum
    {
        return value switch
        {
            MembershipRenewalStatus status => MembershipRenewalDescriptions.TryGetValue(status, out var description) ? description : status.ToString(),
            LoanReturnStatus status => LoanReturnDescriptions.TryGetValue(status, out var description) ? description : status.ToString(),
            LoanExtensionStatus status => LoanExtensionDescriptions.TryGetValue(status, out var description) ? description : status.ToString(),
            _ => value.ToString()
        };
    }

    ```

    このコードは、パターン マッチングを使用して列挙型の型を判断し、適切なディクショナリから説明を取得します。 **switch** ステートメントは列挙**値**の型をチェックし、ディクショナリから対応する説明を返します。 列挙値がディクショナリに見つからない場合、このメソッドは、列挙値に対する ToString() の呼び出しにフォールバックし、列挙型メンバーの名前を文字列として返します。

    GitHub Copilot で GetDescription メソッドをリファクタリングしてラムダ式を排除した場合は、基になるロジックに従う方が簡単です。

    ```csharp

    public static string GetDescription<TEnum>(TEnum value) where TEnum : Enum
    {
        switch (value)
        {
            case MembershipRenewalStatus status:
                string membershipDescription;
                if (MembershipRenewalDescriptions.TryGetValue(status, out membershipDescription))
                {
                    return membershipDescription;
                }
                return status.ToString();

            case LoanReturnStatus status:
                string loanReturnDescription;
                if (LoanReturnDescriptions.TryGetValue(status, out loanReturnDescription))
                {
                    return loanReturnDescription;
                }
                return status.ToString();

            case LoanExtensionStatus status:
                string loanExtensionDescription;
                if (LoanExtensionDescriptions.TryGetValue(status, out loanExtensionDescription))
                {
                    return loanExtensionDescription;
                }
                return status.ToString();

            default:
                return value.ToString();
        }
    }

    ```

1. ソリューションをビルドし、エラーが生成されていないことを確認します。

    この演習の開始時に表示されたのと同じ警告が表示されますが、エラー メッセージは表示されないはずです。

## インライン チャットおよび編集モードとエージェント モードのチャット ビューを使用してコードをリファクタリングする

LINQ は、コレクション、データベース、XML ドキュメントに対し統一した方法でクエリを実行できるようにする C# の強力な機能です。 LINQ は、従来の foreach ループと比較して、データのクエリを実行するためのより簡潔で読みやすい方法を提供します。

演習のこのセクションには次のタスクが含まれています。

- インライン チャットを使用して JsonData クラスをリファクタリングします。
- 編集モードのチャット ビューを使用して JsonLoanRepository クラスをリファクタリングします。
- エージェント モードのチャット ビューを使用して JsonPatronRepository クラスをリファクタリングします。

### インライン チャットを使用して JsonData クラスをリファクタリングする

JsonData クラスには、次のデータ アクセス メソッドが含まれています。GetPopulatedPatron、GetPopulatedLoan、GetPopulatedBookItem、GetPopulatedBook。 これらのメソッドは、foreach ループを使用してコレクションを反復処理し、オブジェクトを設定します。 これらのメソッドをリファクターして LINQ を使用すると、コードの可読性と保守性が向上します。

以下の手順に従って、演習のこのセクションを完了します。

1. ソリューション エクスプローラー ビューで、**Library.Infrastructure** プロジェクトを展開し、**Data** フォルダーを展開します。

1. JsonData.cs ファイルを開きます。

1. 下にスクロールして、**GetPopulatedPatron** メソッドを見つけます。

    **GetPopulatedPatron** メソッドは、図書館の完全に事前設定された **Patron** オブジェクトを作成するように設計されています。 これは、**Patron** の基本プロパティをコピーし、その **Loans** コレクションを詳細な **Loan** オブジェクトを使用して事前設定します。

1. **GetPopulatedPatron** メソッドを選択します。

    ```csharp

    public Patron GetPopulatedPatron(Patron p)
    {
        Patron populated = new Patron
        {
            Id = p.Id,
            Name = p.Name,
            ImageName = p.ImageName,
            MembershipStart = p.MembershipStart,
            MembershipEnd = p.MembershipEnd,
            Loans = new List<Loan>()
        };

        foreach (Loan loan in Loans!)
        {
            if (loan.PatronId == p.Id)
            {
                populated.Loans.Add(GetPopulatedLoan(loan));
            }
        }

        return populated;
    }

    ```

1. インライン チャットを開き、LINQ を使用してメソッドをリファクタリングするプロンプトを入力します。

    ```plaintext
    #selection refactor selection to `return new Patron` using LINQ
    ```

1. 少し時間を取って、おすすめの更新を確認します。

    おすすめの更新は、次のコードのようになります。

    ```csharp
    public Patron GetPopulatedPatron(Patron p)
    {
        return new Patron
        {
            Id = p.Id,
            Name = p.Name,
            ImageName = p.ImageName,
            MembershipStart = p.MembershipStart,
            MembershipEnd = p.MembershipEnd,
            Loans = Loans!
                .Where(loan => loan.PatronId == p.Id)
                .Select(GetPopulatedLoan)
                .ToList()
        };
    }
    ```

    LINQ クエリを使用して foreach ループを置き換える点に注意してください。

    LINQ コードは、オブジェクト初期化子を使用して、オブジェクト プロパティを新しい **Patron** オブジェクトに割り当てます。 こうすることで、**Patron** オブジェクトの個別の**事前設定**インスタンスの必要性がなくなります。 全体的に、更新されたコードは短くなり、読みやすくなっています。

    このコードは、利用者 **p** を使用して、いくつかの基本プロパティを新しい **Patron** オブジェクトに割り当てます。 次に、Patron パラメーター **p** に関連付けられた貸し出しを使用して **Loans** コレクションを事前設定し、**GetPopulatedLoan** メソッドを使用して各貸し出しを変換します。

    **Loans** コレクションを事前設定する LINQ コード行は次のように分解できます。

    - **Loans!**:**Loans!** 式は **Loans** コレクションにアクセスします。 **!** 演算子は null 免除演算子であり、**Loans** が null ではないと開発者が確信していることを示します。 **GetPopulatedPatron** メソッドを呼び出す前に、**Loans** を適切に初期化する必要があります。

    - **.Where(loan => loan.PatronId == p.Id)**:このコードは、入力された利用者 **p** に属するもののみを含むように、貸し出しをフィルター処理します。

    - **.Select(GetPopulatedLoan)**:このコードは、**GetPopulatedLoan** メソッドを使用して、フィルター処理された各貸し出しを変換します。

    - **.ToList()**:結果を **List\<Loan\>** に変換します。

1. おすすめの更新を受け入れるには、**[同意する]** を選択します。

    これと同じ方法を使用して、他の 3 つのメソッドをリファクタリングします。

1. 同じ方法で、**GetPopulatedLoan**、**GetPopulatedBookItem**、および **GetPopulatedBook** メソッドをリファクターします。

    たとえば、次のプロンプトを使用して 3 つのメソッドをリファクターします。

    **GetPopulatedLoan** メソッドの場合:

    ```plaintext

    #selection refactor selection to `return new Loan` using LINQ. Use `GetPopulatedBookItem` for the `BookItem` property. Use `Single` for BookItem and Patron properties.

    ```

    **GetPopulatedBookItem** メソッドの場合:

    ```plaintext

    #selection refactor selection to `return new BookItem` using LINQ. Use `GetPopulatedBook` and `Single` for the `BookItem` property.

    ```

    **GetPopulatedBook** メソッドの場合:

    ```plaintext

    #selection refactor selection to `return new Book` using LINQ. Use `Where` and `Select` for `Author` property. Use `First` author.

    ```

1. おすすめの更新を受け入れたら、少し時間を取って、コードの変更を確認します。

    更新されたコードは、次のコードのようになります。

    ```csharp
    public Loan GetPopulatedLoan(Loan l)
    {
        return new Loan
        {
            Id = l.Id,
            BookItemId = l.BookItemId,
            PatronId = l.PatronId,
            LoanDate = l.LoanDate,
            DueDate = l.DueDate,
            ReturnDate = l.ReturnDate,
            BookItem = GetPopulatedBookItem(BookItems!.Single(bi => bi.Id == l.BookItemId)),
            Patron = Patrons!.Single(p => p.Id == l.PatronId)
        };
    }

    public BookItem GetPopulatedBookItem(BookItem bi)
    {
        return new BookItem
        {
            Id = bi.Id,
            BookId = bi.BookId,
            AcquisitionDate = bi.AcquisitionDate,
            Condition = bi.Condition,
            Book = GetPopulatedBook(Books!.Single(b => b.Id == bi.BookId))
        };
    }

    public Book GetPopulatedBook(Book b)
    {
        return new Book
        {
            Id = b.Id,
            Title = b.Title,
            AuthorId = b.AuthorId,
            Genre = b.Genre,
            ISBN = b.ISBN,
            ImageName = b.ImageName,
            Author = Authors!.Where(a => a.Id == b.AuthorId).Select(a => new Author {
                Id = a.Id,
                Name = a.Name
            }).First()
        };
    }
    ```

1. LINQ クエリの説明を表示するには、**Explain** スマート アクションを使用します。

    **[説明]** スマート アクションを開くには、エディターでコードを選択し、選択したコードを右クリックして、[Copilot] を選択してから **[説明]** を選択します。 **[説明]** スマート アクションによって、選択したコードの詳しい説明が提供されます。 このケースでは、LINQ クエリがコードで使用されています。

    たとえば、**GetPopulatedBook** メソッドで **[説明]** スマート アクションを使用すると、**Book** オブジェクトの **Author** プロパティを事前設定するのに使用される LINQ クエリの説明を表示できます。

    ```csharp
    Author = Authors!.Where(a => a.Id == b.AuthorId).Select(a => new Author {
        Id = a.Id,
        Name = a.Name
    }).First()
    ```

    **[説明]** スマート アクションは、**Book** オブジェクトの **Author** プロパティを事前設定するのに使用される LINQ クエリの詳細な説明を提供します。

    たとえば、説明は次のようになります。

    ```plaintext

    The active selection is a C# code snippet that assigns a value to the Author property. This value is derived from a collection of Author objects named Authors. The code uses LINQ to filter and transform the data within this collection.
    
    First, the Authors! expression uses the null-forgiving operator (!) to indicate that Authors is not null, even if the compiler might think otherwise. This is a way to suppress nullable warnings. The Where method is then called on the Authors collection to filter the elements. The lambda expression a => a.Id == b.AuthorId is used to find all Author objects where the Id matches the AuthorId property of another object b.
    
    After filtering, the Select method is used to project each filtered Author object into a new Author object. This is done by creating a new instance of the Author class and copying the Id and Name properties from the original Author object.
    
    Finally, the First method is called to retrieve the first element from the resulting sequence. This means that the Author property will be assigned the first Author object that matches the filter criteria and has been projected into a new Author instance.
    
    This approach ensures that the Author property is set to a new Author object with the same Id and Name as the first matching Author in the Authors collection.

    ```

1. ソリューションを構築してエラーがないことを確認します。

### 編集モードのチャット ビューを使用して JsonLoanRepository クラスをリファクタリングする

JsonLoanRepository クラスには、**GetLoan** および **UpdateLoan** データ アクセス メソッドが含まれています。 これら 2 つのメソッドをリファクターし、foreach ループを LINQ に置き換えて、コードの可読性と保守性を向上させます。

以下の手順に従って、演習のこのセクションを完了します。

1. **JsonLoanRepository.cs** ファイルを開きます。

1. **GetLoan** メソッドを選択します。

    **GetLoan** メソッドは、ID によって貸し出しを取得するように設計されています。

    ```csharp
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
    ```

1. チャット ビューが**編集**モードで開いていることを確認します。

    チャット ビューが開いていない場合は、**[チャットの切り替え]** を選択して、モードを **[編集]** に設定します。

1. LINQ を使用してメソッドをリファクタリングするプロンプトを入力します。

    たとえば、次のプロンプトを入力します。

    ```plaintext
    refactor the foreach using LINQ. Use Where, Select, and GetPopulatedLoan return the first matching loan.
    ```

1. 少し時間を取って、おすすめの更新を確認します。

    おすすめの更新は、次のコードのようになります。

    ```csharp
    public async Task<Loan?> GetLoan(int id)
    {
        await _jsonData.EnsureDataLoaded();

        return _jsonData.Loans!
            .Where(l => l.Id == id)
            .Select(l => _jsonData.GetPopulatedLoan(l))
            .FirstOrDefault();

    }
    ```

    更新されたコードでは、LINQ を使用してローンのコレクションをフィルター処理し、指定された ID を持つローンのみを含めます。 **貸し出し**は Null 許容 (**Loan? loan**) として宣言する必要があることに注意してください。 次に、**GetPopulatedLoan** メソッドを使用して貸し出しを変換し、最初の結果を返します。 一致する貸し出しが見つからない場合、**FirstOrDefault** は **null** 値を返します。 このメソッドは、指定された **id** の貸し出しが存在しない場合は null 値となる可能性がある、この貸し出しオブジェクトを返します。 このアプローチにより、返されたローンには必要なすべての関連データが完全に設定され、ローンのレコードの包括的なビューが提供されます。

    GitHub Copilot によって、機能的に同等である次のコードが提案されることもあります。

    ```csharp
    public async Task<Loan?> GetLoan(int id)
    {
        await _jsonData.EnsureDataLoaded();

        Loan? loan = _jsonData.Loans!
            .Where(l => l.Id == id)
            .Select(l => _jsonData.GetPopulatedLoan(l))
            .FirstOrDefault();

        return loan;
    }
    ```

1. 更新された GetLoan メソッドを受け入れるには、**[保持する]** を選択します。

1. **UpdateLoan** メソッドを選択します。

    ```csharp
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
    ```

1. LINQ を使用してメソッドをリファクタリングするプロンプトを入力します。

    たとえば、次のプロンプトを入力します。

    ```plaintext

    refactor selection using LINQ. find existing loan in `_jsonData.Loans!. replace existing loan.

    ```

1. 少し時間を取って、おすすめの更新を確認します。

    更新の候補は、次のいずれかの例のようになるはずです。

    ```csharp

    public async Task UpdateLoan(Loan loan)
    {
        var loans = _jsonData.Loans!;
        var index = loans.FindIndex(l => l.Id == loan.Id);

        if (index >= 0)
        {
            loans[index] = loan;
            await _jsonData.SaveLoans(loans);
            await _jsonData.LoadData();
        }
    }

    ```

    または

    ```csharp

    public async Task UpdateLoan(Loan loan)
    {
        Loan? existingLoan = _jsonData.Loans!.FirstOrDefault(l => l.Id == loan.Id);

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

    ```

    更新されたコードでは、LINQ を使用してローンのコレクション内の既存のローンを検索します。 次に、既存のローンを新しいローン データで更新します。 次に、このメソッドは更新されたローンのコレクションを保存し、データを再読み込みします。 このアプローチにより、ローン データが正しく更新され、変更がデータ ストアに保持されることが保証されます。

    メソッドが実行される前にデータが確実に読み込まれるようにするコードを追加することもできます。

    ```csharp

    public async Task UpdateLoan(Loan loan)
    {
        await _jsonData.EnsureDataLoaded();

        Loan? existingLoan = _jsonData.Loans!.FirstOrDefault(l => l.Id == loan.Id);

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

    ```

1. 更新された UpdateLoan メソッドを受け入れるには、**[保持する]** を選択します。

1. ソリューションをビルドし、エラーが生成されていないことを確認します。

    警告が表示されます。 今のところは無視しても構いません。

### エージェント モードのチャット ビューを使用して JsonPatronRepository クラスをリファクタリングする

**JsonPatronRepository** クラスには、次の 3 つのメソッドが含まれています。

- SearchPatrons:SearchPatrons メソッドは、利用者を名前で検索するために使用します。 このメソッドは、並べ替えられた利用者の一覧を返します。
- GetPatron:GetPatron メソッドは、ID で利用者を取得するために使用されます。 このメソッドは、事前設定された patron オブジェクトを返します。
- UpdatePatron:UpdatePatron メソッドは、利用者の情報を更新するために使用されます。 このメソッドにより、既存の利用者は新しいデータで更新され、更新された patrons コレクションが保存されます。

3 つのメソッドはそれぞれ、foreach ループを使用して利用者について反復処理し、検索入力または ID に基づいて一致するものを見つけます。

エージェント モードのチャット ビューを使用してメソッドをリファクタリングし、foreach ループを LINQ クエリに置き換えます。これは、**JsonData** と **JsonLoanRepository** クラス に対して行ったのと同じ方法です。

以下の手順に従って、演習のこのセクションを完了します。

1. **JsonPatronRepository.cs** ファイルを開きます。

    **JsonPatronRepository** クラスは、図書館の利用者を管理するために設計されています。

1. 少し時間を取って、**JsonPatronRepository** クラスに含まれる 3 つのメソッドを確認します。

    **SearchPatrons** メソッドは、利用者を名前で検索するように設計されています。

    ```csharp

    public async Task<List<Patron>> SearchPatrons(string searchInput)
    {
        await _jsonData.EnsureDataLoaded();

        List<Patron> searchResults = new List<Patron>();
        foreach (Patron patron in _jsonData.Patrons)
        {
            if (patron.Name.Contains(searchInput))
            {
                searchResults.Add(patron);
            }
        }
        searchResults.Sort((p1, p2) => String.Compare(p1.Name, p2.Name));

        searchResults = _jsonData.GetPopulatedPatrons(searchResults);

        return searchResults;
    }

    ```

    **SearchPatrons** メソッドは foreach ループを使用し、利用者に対して反復処理を行い、**searchInput** 文字列に基づいて一致するものを見つけます。 次に、このメソッドは結果を名前で並べ替え、事前設定された利用者の一覧を返します。

    **GetPatron** メソッドは、指定した **id** に一致する利用者を返すように設計されています。

    ```csharp

    public async Task<Patron?> GetPatron(int id)
    {
        await _jsonData.EnsureDataLoaded();

        foreach (Patron patron in _jsonData.Patrons!)
        {
            if (patron.Id == id)
            {
                Patron populated = _jsonData.GetPopulatedPatron(patron);
                return populated;
            }
        }
        return null;
    }

    ```

    **GetPatron** メソッドは foreach ループを使用し、利用者について反復処理を行い、**id** パラメーターに基づいて一致するものを見つけます。 その後、このメソッドは、事前設定された patron オブジェクトを返します。

    **UpdatePatron** メソッドは、指定した **id** の利用者を更新するように設計されています。

    ```csharp

    public async Task UpdatePatron(Patron patron)
    {
        await _jsonData.EnsureDataLoaded();
        var patrons = _jsonData.Patrons!;
        Patron existingPatron = null;
        foreach (var p in patrons)
        {
            if (p.Id == patron.Id)
            {
                existingPatron = p;
                break;
            }
        }
        if (existingPatron != null)
        {
            existingPatron.Name = patron.Name;
            existingPatron.ImageName = patron.ImageName;
            existingPatron.MembershipStart = patron.MembershipStart;
            existingPatron.MembershipEnd = patron.MembershipEnd;
            existingPatron.Loans = patron.Loans;
            await _jsonData.SavePatrons(patrons);
            await _jsonData.LoadData();
        }
    }

    ```

    **UpdatePatron** メソッドは foreach ループを使用し、利用者について反復処理を行い、**id** パラメーターに基づいて一致するものを見つけます。 次に、このメソッドは、既存の利用者を新しいデータで更新し、更新された patron コレクションを保存します。

1. チャット ビューのモードを **[エージェント]** に変更します。

    エージェント モードは、GitHub Copilot をエージェントとして実行するように設計されています。 自然言語を使用して、高度なタスクを指定できます。 エージェントにより、割り当てたタスクの評価、必要な作業の計画作成、変更のコードベースへの適用が行われます。

    エージェント モードは、ユーザーが指定したタスクを実行するために、コード編集とツール呼び出しを組み合わせて使用します。 ユーザーのリクエストを処理する際に、編集とツールの結果を監視し、問題が発生した場合には解決します。 エージェント側で問題を解決できない場合は、介入を求められます。 たとえば、エージェントが同じ問題を解決するために複数の反復処理を使用する場合は、プロセスを一時停止して、ユーザーに、要求を明確にするための追加コンテキストの提供やプロセスのキャンセルを求めます。

    > **重要**:エージェント モードでチャット ビューを使用すると、GitHub Copilot は 1 つのタスクを完了するために複数の Premium リクエストを行う場合があります。 Premium リクエストは、ユーザーが開始するプロンプトや、Copilot がユーザーに代わって実行するフォローアップ アクションで使用できます。 使用される Premium リクエストの合計数は、タスクの複雑さ、必要なステップ数、選択されたモデルによって決まります。

1. 少し時間を取って、エージェントに割り当てる必要があるタスクについて検討します。

    タスクは、**JsonPatronRepository** クラスをリファクタリングすることです。 目標は、foreach ループを、元の foreach コードと同じ結果を生成する LINQ クエリで置換することです。

    **JsonData** および **JsonLoanRepository** クラスでの経験を活かすことで、エージェントのためにタスクを記述できます。 LINQ クエリは、**Where**、**Select** および **FirstOrDefault** を使用して、一致する利用者を見つける必要があります。 LINQ クエリは、元の foreach コードの並べ替えを維持するために **OrderBy** も使用する必要があります。

1. エージェントにタスクを割り当てるには、次のプロンプトを入力します。

    ```plaintext

    Review the LINQ code used in the JsonData and JsonLoanRepository classes. Notice how Where, Select, and FirstOrDefault are used. Refactor the methods in the JsonPatronRepository class, replacing foreach loops with LINQ queries that produce the same result as the original foreach code. Use OrderBy to preserve sorting in original foreach code. Use ! to suppress nullability warnings when accessing collections.

    ```

    このプロンプトは、**JsonPatronRepository** クラスをリファクタリングするようにエージェントに指示します。 これは、foreach ループを、元の foreach コードと同じ結果を生成する LINQ クエリに置き換える必要があることを指定しています。 また、**OrderBy** を使用して元の foreach コードの並べ替えを維持する必要があることと、**!** を使用してコレクションにアクセスする際の Null 値の許容に関する警告を抑制する必要があることも指定しています。

1. エージェントがコードをリファクタリングする進行状況を監視します。

    エージェントにより、複数の反復処理でタスクが完了することに注目してください。 各コード編集パスの後にはレビュー パスが続き、問題がチェックされます。 エージェントによって問題が検出されると、問題を解決するようにコードがリファクターされます。 エージェント側で問題を解決できない場合は、介入を求められます。

1. エージェントが完了したら、少し時間を取って、更新の候補を確認します。

    おすすめの更新は、次のコードのようになります。

    ```csharp

    public async Task<List<Patron>> SearchPatrons(string searchInput)
    {
        await _jsonData.EnsureDataLoaded();

        var searchResults = _jsonData.Patrons!
            .Where(patron => patron.Name.Contains(searchInput))
            .OrderBy(patron => patron.Name)
            .ToList();

        return _jsonData.GetPopulatedPatrons(searchResults);
    }

    public async Task<Patron?> GetPatron(int id)
    {
        await _jsonData.EnsureDataLoaded();

        return _jsonData.Patrons!
            .Where(patron => patron.Id == id)
            .Select(patron => _jsonData.GetPopulatedPatron(patron))
            .FirstOrDefault();
    }

    public async Task UpdatePatron(Patron patron)
    {
        await _jsonData.EnsureDataLoaded();

        var existingPatron = _jsonData.Patrons!.FirstOrDefault(p => p.Id == patron.Id);
        if (existingPatron != null)
        {
            existingPatron.Name = patron.Name;
            existingPatron.ImageName = patron.ImageName;
            existingPatron.MembershipStart = patron.MembershipStart;
            existingPatron.MembershipEnd = patron.MembershipEnd;
            existingPatron.Loans = patron.Loans;

            if (_jsonData.Patrons != null)
            {
                await _jsonData.SavePatrons(_jsonData.Patrons);
                await _jsonData.LoadData();
            }
        }
    }

    ```

1. すべての更新を受け入れるには、**[保持する]** を選択します。

### アプリケーションの構築と実行

コードをリファクタリングしたところで、アプリケーションをビルドして実行し、すべてが正常に動作することを確認します。 また、アプリケーションをテストして、リファクタリングされたコードが予期したとおりに機能していることを確認します。

1. ソリューションをクリーンアップするには、**AccelerateAppDevGitHubCopilot** を右クリックし、**[Clean]** を選択します。

    このアクションにより、以前のビルドからビルド成果物が削除されます。 ソリューションを消去すると、JSON データ ファイルが (出力ディレクトリ内で) 元の値に実質的にリセットされます。

1. ソリューションが正常にビルドされていることを確認します。

    たとえば、ソリューション エクスプローラー ビューで **AccelerateDevGHCopilot** を右クリックし、**[ビルド]** を選択します。

    警告はいくつか表示されますが、エラーは発生しないはずです。

1. アプリケーションを実行するには、**Library.Console** を右クリックし、**[デバッグ]** を選択してから **[新しいインスタンスを開始]** を選択します。

    次の手順では、簡単なユース ケースについて説明します。

1. パトロン名の入力を求められたら、「**One**」と入力して Enter キーを押します。

    検索クエリに一致する利用者の一覧が表示されます。

    > **注**:アプリケーションは、大文字と小文字を区別する検索プロセスを使用します。

1. "Input Options" プロンプトで「**2**」と入力し、Enter キーを押します。

    「**2**」と入力すると、リストにある 2 番目の利用者が選択されます。

    パトロンの名前とメンバーシップの状態が表示され、その後に書籍ローンの詳細が表示されます。

1. "Input Options" プロンプトで「**1**」と入力し、Enter キーを押します。

    「**1**」と入力すると、リストにある最初の書籍が選択されます。

    期限や返却状況など、書籍の詳細が一覧表示されます。

1. "Input Options" プロンプトで「**r**」と入力し、Enter キーを押します。

    「**r**」と入力すると、書籍が返されます。

1. "Book was successfully returned." (ブックが正常に返されました。) というメッセージを確認します。 確認します。

    "Book was successfully returned." (ブックが正常に返されました。) というメッセージ の後に書籍の詳細が続く必要があります。 返されたブックは **Returned: True** でマークされます。

1. 新しい検索を開始するには、「**s**」と入力して Enter キーを押します。

1. パトロン名の入力を求められたら、「**One**」と入力して Enter キーを押します。

1. "Input Options" プロンプトで「**2**」と入力し、Enter キーを押します。

1. 最初の書籍貸出が **Returned: True** とマークされていることを確認します。

1. "Input Options" プロンプトで「**q**」と入力し、Enter キーを押します。

1. デバッグ セッションを停止します。

## まとめ

この演習では、GitHub Copilot を使用してコードをリファクタリングする方法を学習しました。 編集モードのチャット ビューを使用して、**EnumHelper** クラスをリファクタリングし、リフレクションを静的ディクショナリに置き換えました。 また、インライン チャットと編集モードを使用して、**JsonData** および **JsonLoanRepository** クラスをリファクタリングし、foreach ループを LINQ クエリで置き換えました。 最後に、エージェント モードを使用して **JsonPatronRepository** クラスをリファクタリングし、foreach ループを LINQ クエリで置き換えました。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認します。 変更を加えた場合は、ここで元に戻します。
