<!-- ---
lab:
    title: 'Exercise - Resolve GitHub issues using GitHub Copilot'
    description: 'Learn how to identify and address performance bottlenecks and code inefficiencies using GitHub Copilot tools.'
--- -->

# GitHub Copilot を使用して GitHub の issue を解決する

GitHub の issue は、プロジェクトのバグ、機能強化、タスクを追跡するための強力な手段です。

この演習では、GitHub Copilot を使用して、eコマース アプリケーションのセキュリティの脆弱性に関連する GitHub の issue を分析して解決します。

この演習の所要時間は約**40** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です: Git 2.48 以降、.NET SDK 9.0 以降、C# 開発キット拡張機能をインストールした Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

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

- 自分の名前と電子メール アドレスを使用するように Git を確実に構成するには、次の手順を実行します。

    次のコマンドを自分の情報で更新し、コマンドを実行します。

    ```bash

    git config --global user.name "Julie Miller"

    ```

    ```bash

    git config --global user.email julie.miller@example.com

    ```

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 顧客は、顧客は GitHub リポジトリの issue を解決するのに支援を必要としています。 あなたは、すべての issue が解決され、閉じられるようにする必要があります。 開発環境として Visual Studio Code を使用し、開発タスクを支援するために GitHub Copilot を使用します。 あなたには次のアプリが割り当てられています。

- ContosoShopEasy:ContosoShopEasy は eコマース アプリケーションであり、複数のセキュリティの脆弱性が含まれています。 これらの脆弱性は、実際のアプリケーションで見られる一般的なセキュリティの問題を表します。

この演習には、次のタスクが含まれています。

1. ContosoShopEasy リポジトリをインポートします。
1. GitHub で issue を確認します。
1. リポジトリをローカルにクローンし、コードベースを確認します。
1. GitHub Copilot の質問モードを使って issue を分析します。
1. GitHub Copilot のエージェント モードを使って issue を解決します。
1. リファクタリング後のコードをテストして検証します。
1. 変更をコミットして issue を閉じます。

> **注**:このトレーニング演習では時間を節約するために、issue のグループを解決してテストし、1 回のコミットで更新をプッシュします。 issue をバッチ処理することは、推奨されるベスト プラクティスではありません。 Microsoft と GitHub では、個別のコミットで各 issue を個別に解決することをお勧めします。 issue を個別に解決すると、追跡可能性が向上し、コード レビューが容易になり、問題が発生した場合にロールバック オプションの安全性が高くなります。

### ContosoShopEasy リポジトリをインポートする

GitHub Importer を使用すると、独自の GitHub アカウントに既存のリポジトリのコピーを作成し、インポートされたコピーを完全に制御できます。 GitHub Importer は、issue、PR、ディスカッションを移行しませんが、GitHub Actions ワークフローをインポートします。 インポートするリポジトリには、コードベースに関連する issue を作成する GitHub Actions ワークフローが含まれています。

このタスクでは、ContosoShopEasy リポジトリをインポートし、ワークフローを実行して、コードベースに含まれているセキュリティの脆弱性に関する GitHub の issue を作成します。

そのためには、以下の手順を実行してください。

1. ブラウザー ウィンドウを開いて GitHub.com に移動します。

1. GitHub アカウントにサインインし、[リポジトリ] タブを開きます。

    右上隅にあるプロファイル アイコンをクリックし、**[リポジトリ]** を選択して、[リポジトリ] タブを開きます。

1. [リポジトリ] タブで、**[新規]** ボタンを選択します。

1. **[新しいリポジトリの作成]** セクションで、**[リポジトリのインポート]** を選択します。

    **[プロジェクトを GitHub にインポートする]** ページが表示されます。

1. [プロジェクトを GitHub にインポートする] ページの **[ソース リポジトリの詳細]** で、ソース リポジトリの次の URL を入力します。

    ```plaintext
    https://github.com/MicrosoftLearning/resolve-github-issues-lab-project
    ```

1. **[新しいリポジトリの詳細]** セクションの **[所有者]** ドロップダウンで、GitHub ユーザー名を選択します。

1. **[リポジトリ名]** フィールドに「**ResolveGitHubIssues**」と入力し、**[インポートの開始]** を選択します。

    GitHub により、ContosoShopEasy プロジェクト ファイルを含む新しいリポジトリがアカウントに作成されます。

1. インポート プロセスが完了するまで待ってから、新しいリポジトリを開きます。

    > **注**:リポジトリのインポートには 1 から 2 分かかる場合があります。

1. リポジトリの [アクション] タブを開きます。

1. 左側の **[すべてのワークフロー]** で、**[ContosoShopEasy トレーニングの issue の作成]** ワークフローを選択し、**[ワークフローの実行]** を選択します。

1. 表示されたワークフロー ダイアログで、「**作成**」と入力し、**[ワークフローの実行]** を選択します。

1. 画面上でワークフローの進行状況を監視します。

    しばらくすると、ページが更新され、進行状況バーが表示されます。 ワークフローは 1 分以内に正常に完了します。

1. 続行する前に、ワークフローが正常に完了していることを確認します。

    緑色の円で囲まれたチェックマークは、ワークフローが正常に実行されたことを示します (ワークフロー名の左側に表示されます)。

    ワークフロー名の左側に、赤い円で囲まれた X が表示されている場合は、ワークフローが失敗したことを意味します。 ワークフローが正常に実行されない場合は、リポジトリのインポート時にアカウントを選択し、そのアカウントに読み取りと書き込みのアクセス許可があることを確認します。 GitHub の**Copilot とチャット**機能を使用して、issue の診断に役立てることができます。

### GitHub で issue を確認する

GitHub の issue は、バグ、セキュリティの脆弱性、機能強化要求の一元的な追跡システムとして機能します。 各 issue では、問題に関するコンテキスト、重大度、アプリケーションへの潜在的な影響が提供されます。 コードに進む前にこれらの issue を理解することは、優先順位を決めるのに役立ち、包括的な修復が保証されます。

このタスクでは、GitHub の issue を確認し、対処する必要があるセキュリティの脆弱性を調べます。

そのためには、以下の手順を実行してください。

1. リポジトリの **[Issues]** タブを選択し、少し時間を取って [Issues] ページを確認します。

    10 件の issue が一覧表示されます。 issue はバグとして定義されており、優先度レベルが割り当てられていることに注意してください。

1. 重大な issue のみを表示するには、**[ラベル]** ドロップダウンを選択し、**[重大]** ラベルを選択します。

    issue の一覧がフィルター処理されて重大な issue のみが表示されます。

    - **🔐 ハードコーディングされた管理者資格情報を削除する**  

    - **🔐 クレジット カードのデータ保存違反を修正する**  

1. 高優先度の issue のみを表示するには、**[ラベル]** ドロップダウンを選択し、**[重大]** の選択を解除してから、**[高優先度]** ラベルを選択します。

    issue の一覧がフィルター処理されて高優先度の issue のみが表示されます。

    - **🔐 入力検証のセキュリティ バイパスを修正する**  

    - **🔐 デバッグ ログから機密データを削除する**  

    - **🔐 MD5 パスワード ハッシュを安全な代替ハッシュに置き換える**  

    - **🔐 製品検索の SQL インジェクションの脆弱性を修正する**  

1. **[製品検索の SQL インジェクションの脆弱性を修正する]** issue を選択します。

1. 少し時間を取って issue の詳細を確認します。

    issue の詳細には、問題と予想される修正が説明されています。

    > **注**:issue の文書化に使用されるプロセス (手動プロセスと AI で自動化されたプロセスなど) は、issue の記述の全体的な品質と正確性に影響を与える可能性があります。 このトレーニングに含まれる issue は、エージェントでコードベースを確認した後、GitHub Copilot のエージェント モードを使用して記述されたものです。 GitHub Copilot を使用すると、脆弱性、コードの場所、脆弱なコードの例、セキュリティ リスク、修正の受け入れ基準について、非常に詳細な説明を生成できます。

1. issue には誰も割り当てられていないことに注意してください。

1. [issue] タブに戻り、フィルターをクリアします。

1. すべての issue を選択し、**[割り当て]** ドロップダウンを使用して自分に割り当てます。

    issue を自分に割り当てると、修復プロセスを進める際に進行状況を追跡するのに役立ちます。

### リポジトリをローカルにクローンし、コードベースを確認する

ContosoShopEasy アプリケーションはエンタープライズ アプリケーションに典型的な階層構造のアーキテクチャに従っており、モデル、サービス、データ アクセス、セキュリティ コンポーネントが明確に分離されています。

セキュリティの問題を解決する前に、少し時間を取って、既存のコードベースの基本的な構造、動作、機能を理解することが不可欠です。

このタスクでは、リポジトリのローカル クローンを作成し、Visual Studio Code でプロジェクト構造を調べ、アプリケーションのコンソール出力を確認して、セキュリティの脆弱性を見つけます。

そのためには、以下の手順を実行してください。

1. リポジトリのルート ページ ([コード] タブ) に戻ります。

1. ResolveGitHubIssues リポジトリをローカル開発環境にクローンします。

    たとえば、Git CLI を使用してリポジトリをクローンするには、次の手順を使用できます。

    1. **[コード]** ボタンを選択し、HTTPS URL をコピーして、リポジトリの URL をコピーします。

    1. ターミナル ウィンドウを開き、リポジトリをクローンするディレクトリに移動し、リポジトリの URL を使用する "git clone" コマンドを実行します。

        たとえば、Windows PowerShell を開き、C:\TrainingProjects に移動し、次のコマンドを実行します (**your-username** を自分の GitHub ユーザー名に置き換えます)。

        ```bash
        git clone https://github.com/your-username/ResolveGitHubIssues.git
        ```

1. クローンしたリポジトリを Visual Studio Code で開きます。

    最新バージョンの Visual Studio Code を使用していること、および GitHub Copilot と GitHub Copilot Chat の各拡張機能がインストールされ、有効になっていることを確認します。

1. エクスプローラー ビューでプロジェクト構造を確認します。

    ContosoShopEasy アプリケーションは、次のコンポーネントを含む階層構造のアーキテクチャに従いっています。

    - **Models/**:**Category.cs**、**Order.cs**、**Product.cs**、**User.cs** のデータ モデルが含まれています。

    - **Services/**:**OrderService.cs**、**PaymentService.cs**、**ProductService.cs**、**UserService.cs** のビジネス ロジックが含まれています。

    - **Data/**:**OrderRepository.cs**、**ProductRepository.cs**、**UserRepository.cs** のデータ リポジトリが含まれています。

    - **Security/**:**SecurityValidator.cs** のセキュリティ検証ロジックが含まれています

    - **Program.cs**依存関係インジェクションのセットアップが含まれるメイン アプリケーション エントリ ポイント

    - **README.md**: アプリケーションの目的と脆弱性が説明されているドキュメント

1. アプリケーションの現在の動作を確認するには、アプリケーションをビルドして実行します。

    たとえば、Visual Studio Code の統合ターミナル ウィンドウを開き、次のコマンドを実行できます。

    ```bash
    cd ContosoShopEasy
    dotnet build
    dotnet run
    ```

    アプリケーションは、eコマース ワークフロー シミュレーションを実行して、詳細なコンソール ログを通じてセキュリティの脆弱性を顕在化します。

1. 時間を取ってコンソール出力を確認します。

    アプリケーションにより、パスワード、クレジット カード番号、管理者資格情報、内部システムの詳細などの機密情報がログされることに注意してください。 この出力は、対処する必要があるセキュリティの問題の明確な証拠を提供します。

    > **注**:このアプリのコードのコメント、ロジック、ログは、セキュリティの脆弱性を顕在化するのに役立つように設計されています。 実装は不自然ですが、コメントと出力ログでは、実際のアプリケーションで一般的なセキュリティ上の問題が浮き彫りになります。

1. コードベースのセキュリティの脆弱性を特定するレビュー プロセスを開始するには、**Models** フォルダーを展開し、**Order.cs** ファイルを開きます。

1. 下方向にスクロールして、**PaymentInfo** クラスを見つけます。

    CardNumber プロパティと CVV プロパティに関するコメントに注目してください。 このコードは、"クレジット カードのデータ保存違反を修正する" issue に関連しています。

1. **Security** フォルダーを展開し、**SecurityValidator.cs** ファイルを開きます。

    > **注**:SecurityValidator.cs クラスは、ContosoShopEasy アプリケーションのセキュリティ関連のロジックを一元化するように設計されており、セキュリティの issue の特定、管理、解決を容易にします。 実際のアプリケーションでは、SecurityValidator のようなクラスを使用して、セキュリティのベスト プラクティスと入力検証を強制することができます。 ただし、ContosoShopEasy の特定の実装は、意図的に安全ではなく、脆弱性が露呈するように作成されています。

1. 少し時間を取って、次のセキュリティに関する issue を見つけます。

    - ファイルの先頭付近で、管理者資格情報定数 (7 から 9 行目) に関連するコメントに注目してください。 このコードは、"ハードコーディングされた管理者資格情報を削除する" issue に関連しています。

    - ValidateInput メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"入力検証セキュリティ バイパスを修正する" issue に関連しています。

    - ValidateEmail メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"電子メール検証のセキュリティを強化する" issue に関連しています。

    - ValidatePasswordStrength メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"パスワードのセキュリティ要件を強化する" issue に関連しています。

    - ValidateCreditCard メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"クレジット カードのデータ保存違反を修正する" issue に関連しています。

    - GenerateSessionToken メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"予測可能なセッション トークンの生成を修正する" issue に関連しています。

    - RunSecurityAudit メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"エラー メッセージでの情報漏えいを削減する (コンソール出力)" issue に関連しています。

    SecurityValidator.cs ファイル内のいくつかのメソッドは、"デバッグ ログから機密データを削除する" issue にも関連しています。

    SecurityValidator クラスによって明らかになる問題は、一般的に、実際のアプリケーションのクラス (特にレガシまたは保守が不十分なコードベース) に分散して見られるものです。

1. **Services** フォルダーを展開し、**UserService.cs** ファイルを開きます。

1. 少し時間を取って、次のセキュリティに関する issue を見つけます。

    - RegisterUser、LoginUser、ValidateUserInput の各メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"デバッグ ログから機密データを削除する" issue に関連しています。

    - GetMd5Hash メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"MD5 パスワード ハッシュを安全な代替ハッシュに置き換える" issue に関連しています。

1. **PaymentService.cs** ファイルを開きます。

1. 少し時間を取って、セキュリティの脆弱性を説明するコメントを確認します。

    このコードのセキュリティの脆弱性は、"デバッグ ログから機密データを削除する" issue に関連しています。

1. **ProductService.cs** ファイルを開きます。

1. 少し時間を取って、SearchProducts メソッドを確認します。

    このコードのセキュリティの脆弱性は、"製品検索の SQL インジェクションの脆弱性を修正する" issue に関連しています。

### GitHub Copilot の質問モードを使って issue を分析する

GitHub の issue には、多くの場合、修正プログラムを実装する前に慎重に分析する必要がある複雑な問題が含まれています。 根本原因、潜在的な影響、最適な修復戦略を理解することは、効果的な解決に不可欠です。

次の Visual Studio Code 拡張機能を使用して、issue の分析と解決に役立てることができます。

- **GitHub Copilot Chat**:GitHub Copilot の質問モードが備えるインテリジェントなコード分析機能は、セキュリティの脆弱性を明らかにし、潜在的な影響を理解し、修復戦略を提案するのに役立ちます。

- **GitHub pull request**。GitHub pull request 拡張機能は、GitHub の issue を Visual Studio Code に直接統合し、開発環境を離れることなく issue を管理および操作できるようにします。

各セキュリティの問題を体系的に分析すると、修正を実装する前に、問題の包括的な理解を深めることができます。 このアプローチにより、解決策では単なる症状ではなく根本原因に対処できます。

このタスクでは、GitHub Copilot の Ask モードを使用して、セキュリティの脆弱性を体系的に分析します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code で、[拡張機能] ビューを開きます。

    [拡張機能] ビューを開くには、Visual Studio Code ウィンドウで、左側のアクティビティ バーにある **[拡張機能]** アイコンを選択します。

1. [拡張機能] ビューで、"GitHub pull request" を検索し、拡張機能をインストールします。

    この拡張機能を使用すると、Visual Studio Code で GitHub の pull request と issue を確認および管理できます。

    インストールが完了したら、変更を有効にするために Visual Studio Code の再読み込みが必要になる場合があります。 **[GitHub]** アイコンを Visual Studio Code のアクティビティ バーに追加する必要があります。

1. [GitHub pull request] ビューを開くには、アクティビティ バーから **[GitHub]** アイコンを選択します。

    ダイアログが表示されたら、お使いの GitHub アカウントにサインインして、Visual Studio Code を GitHub リポジトリに接続します。

1. [GitHub] ビューの **[pull request]** と **[issue]** セクションに注目します。

    **[issue]** セクションでは、Visual Studio Code 内で直接 GitHub リポジトリから issue を表示および管理できます。

1. **[issue]** セクションに記載されている issue を確認します。

    先ほど GitHub Web インターフェイスで確認したのと同じ issue が表示されます。

1. GitHub Copilot のチャット ビューを開き、**[Ask]** モードが選択されていることを確認します。

    チャット ビューをまだ開いていない場合は、Visual Studio Code ウィンドウの上部にある **[チャット]** アイコンを選びます。 チャット モードが **[Ask]** に設定されており、**GPT-4.1** モデルを使用していることを確認します。

    > **注**:GPT-4.1 モデルは優れたコード分析機能を提供し、GitHub Copilot Free プランに含まれています。 別のモデルを選択すると、得られる結果が異なる場合があります。

1. クリーンなチャット セッションから開始していることを確認します。

    チャット セッションは、GitHub Copilot との対話を整理するのに役立ちます。 各セッションは独自のコンテキストを維持するため、特定のタスクや issue に集中できます。 セッション内の会話履歴により継続性が維持されるため、GitHub Copilot は以前の対話に基づいてより正確で関連性の高い応答を提供することができます。 このチャット会話では、ContosoShopEasy アプリケーションのセキュリティの脆弱性の分析と解決に重点を置きます。 GitHub Copilot の Ask モードを使用して GitHub の issue の分析を完了したら、同じ会話を使用して、GitHub Copilot の Agent モードを使用してコード変更を実装できます。 GitHub Copilot は、Ask モードからの詳細な分析を使用して、Agent モードでコード生成を通知します。これにより、修正は、特定された脆弱性および推奨される修復戦略と確実に一致します。

    必要に応じて、**[新しいチャット]** ボタン ([チャット] パネルの上部にある**+** アイコン) を選択すると、新しいチャット セッションを開始できます。

1. **ProductService.cs** ファイルを開き、**SearchProducts** メソッドを見つけます。

1. コード エディターで、**SearchProducts** メソッド全体を選択します。

    エディターでコードを選択すると、チャット コンテキストがフォーカスされます。 GitHub Copilot では、選択したコードを使用して、関連する分析と推奨事項が提供されます。

    **SearchProducts** メソッドは、"製品検索での SQL インジェクションの脆弱性の修正" の問題に関連付けられています。

1. GitHub Copilot に、SQL インジェクションの脆弱性のコードを分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Analyze the SearchProducts method for security vulnerabilities. What makes this code susceptible to SQL injection attacks, and what are the potential consequences if an attacker exploits this vulnerability?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    How can I modify this method to prevent SQL injection attacks? What secure coding practices should I implement to safely handle user input in database queries?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    SQL インジェクション リスクの管理に役立つパラメーター化クエリまたは ORM メソッドを使用するための推奨事項が表示されます。 入力の検証とサニタイズの手法に関する提案も表示される場合があります。 多くの場合、GitHub Copilot により、提案を実装する方法を示すコード スニペットが提供されます。

1. **UserService.cs** ファイルを開きます。

1. GitHub Copilot に、UserService.cs ファイルを確認し、セキュリティの脆弱性を特定し、関連する GitHub の issue を一覧表示するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Review the UserService.cs file and identify the security vulnerabilities that are present in the code. Create a list the corresponding GitHub issues. Indicate the methods associated with each issue.
    ```

1. 少し時間を取って、GitHub Copilot の応答を確認してください。

1. コード エディターで、**GetMd5Hash** メソッドを見つけます。

1. コード エディターで、**GetMd5Hash** メソッド全体を選択します。

    **GetMd5Hash** メソッドは、"MD5 パスワード ハッシュを安全な代替策に置き換える" issue に関連付けられています。

1. GitHub Copilot に、脆弱なパスワード ハッシュの脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Why is MD5 hashing unsuitable for password storage? What are the security risks of using MD5 for passwords, and what stronger alternatives should I use instead?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    Show me how to implement secure password hashing using bcrypt or PBKDF2. What additional security measures should I implement for password handling?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    GitHub Copilot では、"PBKDF2" と "bcrypt" の比較が提供されます。 また、これらのアルゴリズムを使用して安全なパスワード ハッシュを実装する方法を示すコード スニペットと、パスワード処理のための追加のセキュリティ対策の一覧も提供されます。

1. **UserService.cs** ファイルで、**RegisterUser** メソッドと**LoginUser** メソッドを見つけます。

1. コード エディターで、両方のメソッドを選択します。

    **RegisterUser** および**LoginUser** メソッドは、"デバッグ ログから機密データを削除する" issue に関連付けられています。

1. GitHub Copilot に、機密データのログの脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    What sensitive information is being logged in the user registration and login methods? Why is logging passwords and user data a security risk?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    How can I modify these methods to prevent sensitive data logging? What secure logging practices should I implement to protect user information?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

1. **PaymentService.cs** ファイルを開き、**ProcessPayment** メソッドを見つけます。

1. コード エディターで、**ProcessPayment** メソッド全体を選択します。

    **ProcessPayment** メソッドは、"デバッグ ログから機密データを削除する" issue に関連付けられています。

1. GitHub Copilot に、機密の支払いデータのログを分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    What sensitive payment information is being logged in this method? Why is logging credit card numbers and CVV codes a security risk?
    ```

1. **SecurityValidator.cs** ファイルを開き、ファイルの先頭付近にある管理者資格情報定数を見つけます。

1. コード エディターで、ハードコーディングされた管理者資格情報定数を選択します。

1. GitHub Copilot に、ハードコーディングされた資格情報の脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    What security risks are created by hardcoding admin credentials in source code? How should application credentials be managed securely in production environments?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    What are best practices for managing application credentials securely? How can I implement secure credential management in this application?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

1. **SecurityValidator.cs** ファイルで、**ValidateInput** メソッドを見つけます。

1. コード エディターで、**ValidateInput** メソッド全体を選択します。

1. GitHub Copilot に、入力検証バイパスの脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    What makes this input validation method ineffective? Why does it detect dangerous input but still return true, and how should proper input validation work?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    How can I modify this method to implement effective input validation? What secure coding practices should I follow to prevent input validation bypass vulnerabilities?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

1. **SecurityValidator.cs** ファイルで、**GenerateSessionToken** メソッドを見つけます。

1. コード エディターで、**GenerateSessionToken** メソッド全体を選択します。

1. GitHub Copilot に、予測可能なセッション トークンの生成の脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Why are predictable session tokens based on username and timestamp a security risk? How should secure, unpredictable session tokens be generated?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    How can I modify this method to generate secure, unpredictable session tokens? What cryptographic techniques should I use to enhance session token security?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

1. **SecurityValidator.cs** ファイルで、**ValidateEmail** メソッドを見つけます。

1. コード エディターで、**ValidateEmail** メソッド全体を選択します。

1. GitHub Copilot に、弱い電子メール検証の脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    What makes this email validation insufficient? What are the security risks of weak email validation, and how should proper email validation be implemented?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    How can I modify this method to implement robust email validation? What techniques should I use to ensure email addresses are properly validated?
    ```

1. **SecurityValidator.cs** ファイルで、**ValidatePasswordStrength** メソッドを見つけます。

1. コード エディターで、**ValidatePasswordStrength** メソッド全体を選択します。

1. GitHub Copilot に、不十分なパスワード要件の脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Why are these password requirements insufficient for security? What are proper password complexity requirements, and how should password strength be validated?
    ```

1. GitHub Copilot の分析を確認し、特定の修復ガイダンスを依頼します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    How can I modify this method to enforce strong password requirements? What best practices should I follow for password strength validation?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

1. **Models** フォルダーで、**Order.cs** ファイルを開き、**PaymentInfo** クラスを見つけます。

1. コード エディターで、**PaymentInfo** クラス内の**CardNumber** プロパティと**CVV** プロパティを選択します。

1. GitHub Copilot に、クレジット カードのデータ保存違反を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Why is storing full credit card numbers and CVV codes a PCI DSS compliance violation? What are the proper ways to handle payment card data securely?
    ```

1. **SecurityValidator.cs** ファイルに戻り、**RunSecurityAudit** メソッドを見つけます。

1. コード エディターで、**RunSecurityAudit** メソッド全体を選択します。

1. GitHub Copilot に、情報漏えいの脆弱性を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    How does the security audit method create information disclosure vulnerabilities? What information should never be exposed in logs or error messages?
    ```

1. 修復フェーズの間に参照できるよう、分析の結果を文書化します。

    各脆弱性カテゴリに関する GitHub Copilot の推奨事項を記録します。 このドキュメントは、次のタスクでセキュリティの修正を実装する際のガイドになります。

### GitHub Copilot のエージェント モードを使って issue を解決する

GitHub Copilot のエージェント モードを使うと、複数のファイルとメソッドで複雑なセキュリティ修正を自律的に実装できます。 分析と推奨事項を提供する質問モードとは異なり、エージェント モードでは、コードを直接変更してセキュリティの改善を実装できます。 このアプローチは、複数の関連する脆弱性に一貫して対処する必要がある体系的なセキュリティ修復に特に有効です。

このタスクでは、GitHub Copilot のエージェント モードを使って、ContosoShopEasy アプリケーションで特定されたすべての脆弱性に対する包括的なセキュリティ修正を実装します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat をエージェント モードに切り替えます。

    エージェント モードの GitHub Copilot は、ユーザーの指示に基づいてコードを直接変更できます。 エージェント モードは、コードベース内の関連ファイルを確認して、適切なコンテキストを確立するために機能します。 エージェントが複雑なタスクを実行するために必要な情報を確実に得られるように、コンテキストにファイルとフォルダーを手動で追加できます。

1. 少し時間を取って、修復戦略を検討します。

    GitHub Copilot の Ask モードを使用して完了した分析に基づくプランを作成します。 issue に対処する順序、修正間の依存関係、各脆弱性が正常に修復されたことを確認する方法を検討します。

    GitHub の issue は、重大な issue から優先度の低い issue の順に次のとおりです。

    1. 🔐 製品検索の SQL インジェクションの脆弱性を修正する
    1. 🔐 MD5 パスワード ハッシュを安全な代替ハッシュに置き換える
    1. 🔐 デバッグ ログから機密データを削除する
    1. 🔐 ハードコーディングされた管理者資格情報を削除する
    1. 🔐 クレジット カードのデータ保存違反を修正する
    1. 🔐 入力検証のセキュリティ バイパスを修正する
    1. 🔐 予測可能なセッション トークンの生成を修正する
    1. 🔐 電子メール検証のセキュリティを強化する
    1. 🔐 パスワードのセキュリティ要件を強化する
    1. 🔐 エラー メッセージの情報漏えいを削減する

    これらの issue は、コードベース内の特定のファイルとメソッドに関連しています。 ファイルの関連付け別に整理すると、issue は次のようになります。

    - **ProductService.cs**:issue #1
    - **UserService.cs**:issues #2 および #3
    - **PaymentService.cs**:issue #3
    - **SecurityValidator.cs**:issue #4、#6、#7、#8、#9、#10
    - **Models/Order.cs**:issue #5

    修復戦略では、各問題に体系的に対処し、修正が正しくかつ一貫して実装されるようにする必要があります。

    このトレーニング演習では時間を節約するために、すべての issue を解決してから、すべてのコード更新をまとめてコミットします。 この場合、ファイルベースの修復戦略が合理的です。 ただし、issue を大きなバッチで処理するのは、推奨されるベスト プラクティスではありません。

    運用環境では、多くの場合、別個コミットで各 issue に個別に対処することをお勧めします。 このアプローチにより、追跡可能性が向上し、コード レビューが容易になり、問題が発生した場合にロールバック オプションの安全性が高くなります。

1. コード エディターで開いているすべてのファイルを閉じます。

    ファイルを閉じると、エージェントは、コンテキストに追加するファイルに集中しやすくなります。 気付かずにエディターでファイルを開いたままにしておくと、目前のタスクに集中できなくなることがあります。

1. **ProductService.cs** ファイルをチャット コンテキストに追加します。

    SQL インジェクションの issue は、ProductService.cs ファイル内の SearchProducts メソッドに関連します。

1. GitHub Copilot に、SQL インジェクションの脆弱性に対処するように依頼します。

    GitHub Copilot の Ask モードを使用した分析により、このメソッドは、適切なサニタイズを行わないままユーザー入力を使用して SQL クエリを構築することが明らかになりました。 分析を使用して、エージェントが脆弱性の修復に使用できる明確なタスク命令を作成します。

    たとえば、エージェントに次のタスクを割り当てることができます。

    ```text
    Fix the SQL injection vulnerability in the SearchProducts method. Remove the simulated SQL query logging that demonstrates the vulnerability, and implement proper input sanitization to safely handle search terms. Ensure the method still functions correctly for legitimate searches while preventing malicious input.
    ```

1. エージェントの進行状況を監視します。

    エージェントはコードを変更して脆弱なログを削除し、より安全な入力処理を実装します。

1. 少し時間をかけて、提案された変更を確認し、[チャット] ビューで **[Keep]** を選択します。

    コード エディターで GitHub Copilot の編集候補を常に確認します。 セキュリティ上の問題に対処しても機能が確実に維持されるようにします。

    運用環境では、チームは次の issue に進む前に、次のチェックリストを完了する必要があります。

    - コードに含まれていた脆弱性はなくなりました。
    - アプリケーションは引き続き正しく機能します。
    - セキュリティのベスト プラクティスが実装され、新しいセキュリティの問題は発生しません。
    - 自動テスト (使用可能な場合) は正常に完了します。
    - コードの更新は明確に文書化されています。
    - 変更は、説明メッセージでコミットされ、issue をマージして閉じる前にピア レビューされます。

1. 安全なパスワード ハッシュを実装します。

    `UserService.cs` ファイルに注目して、次のプロンプトを使います。

    ```text
    Replace the MD5 password hashing with bcrypt or PBKDF2. Update the GetMd5Hash method to use a cryptographically secure hashing algorithm with proper salt generation. Ensure compatibility with existing user authentication while improving security.
    ```

1. パスワード ハッシュの変更をレビューしてテストします。

    エージェントは、より強力なパスワード ハッシュを実装します。 アプリケーションを実行して変更をテストし、ユーザーの登録とログインがまだ正しく機能することを確認します。

1. 機密データのログに対処します (Issue #3)。

    `PaymentService.cs` ファイルと`UserService.cs` ファイルに注目し、エージェントに次のように指示します。

    ```text
    Fix sensitive data logging throughout the application. Remove logging of passwords, full credit card numbers, CVV codes, and other sensitive information. Implement secure logging that masks sensitive data while maintaining useful operational information.
    ```

1. ハードコーディングされた管理者資格情報を削除します (Issue #4)。

    `SecurityValidator.cs` ファイルに注目して、次のプロンプトを使います。

    ```text
    Remove hardcoded admin credentials from the SecurityValidator class. Replace the hardcoded ADMIN_USERNAME and ADMIN_PASSWORD constants with a secure configuration approach using environment variables while maintaining the functionality for educational demonstration purposes.
    ```

1. クレジット カードのデータ保存違反を修正します (Issue #5)。

    `Models/Order.cs` ファイルに注目し、エージェントに次のように指示します。

    ```text
    Fix PCI DSS compliance violations in the Order model. Remove or modify the CardNumber and CVV properties to avoid storing full credit card numbers and CVV codes. Implement secure payment data handling that stores only last 4 digits for display purposes.
    ```

1. 入力検証のバイパスを修正します (Issue #6)。

    入力検証の脆弱性を修正するようにエージェントに指示します。

    ```text
    Fix the ValidateInput method in SecurityValidator that currently always returns true despite detecting threats. Implement proper input validation that actually rejects dangerous content when SQL injection, XSS, or other malicious patterns are detected.
    ```

1. セキュリティで保護されたセッション トークンの生成を実装します (Issue #7)。

    セッション トークンの脆弱性に焦点を当てます。

    ```text
    Replace the predictable session token generation in GenerateSessionToken method with a cryptographically secure random token generator. Remove the username and timestamp-based pattern and implement unpredictable tokens with sufficient entropy.
    ```

1. 電子メール検証を強化します (Issue #8)。

    弱い電子メール検証に対処します。

    ```text
    Fix the ValidateEmail method that only checks for '@' and '.' characters. Implement proper email format validation using regex or built-in validation methods. Remove email logging and add appropriate length restrictions.
    ```

1. パスワード要件を改善します (Issue #9)。

    パスワード強度の検証に焦点を当てます。

    ```text
    Strengthen the ValidatePasswordStrength method that currently only requires 4 characters. Implement proper password complexity requirements including minimum 8 characters, uppercase, lowercase, numbers, and special characters. Remove password logging.
    ```

1. 情報漏えいを削減します (Issue #10)。

    デバッグ ログとセキュリティ監査の問題に対処します。

    ```text
    Fix information disclosure vulnerabilities by removing or restricting the RunSecurityAudit method and reducing verbose error messages throughout the application. Remove sensitive system information from logs while maintaining useful debugging capabilities.
    ```

1. アプリケーションをテストします。

    エージェントが各脆弱性カテゴリの修正を実装した後、アプリケーションを実行して、機能が保たれていることを確認します。

    ```bash
    dotnet build
    dotnet run
    ```

1. セキュリティの改善によってコア機能が損なわれていないことを検証します。

    セキュリティの修正を実装した後も、製品検索、ユーザー登録、支払い処理、その他のコア機能が引き続き正しく動作することを確認します。

### リファクタリング後のコードをテストして検証する

セキュリティ修復後の包括的なテストでは、セキュリティの改善が有効であることを確認する一方で、脆弱性の修正によって機能の回帰が発生しないことを確認します。 この検証プロセスでは、アプリケーションのセキュリティ面とビジネス機能の両方をテストする必要があります。 適切なテストで、アプリケーションが意図した動作を維持しながら、より安全になっていることを検証します。

このタスクでは、更新後の ContosoShopEasy アプリケーションを体系的にテストして、セキュリティの問題が解決され、コア機能はそのままであることを確認します。

そのためには、以下の手順を実行してください。

1. アプリケーションをビルドし、コンパイル エラーを解決します。

    次のコマンドを実行して、コードが正常にコンパイルされることを確認します。

    ```bash
    dotnet build
    ```

    コンパイル エラーがある場合は、GitHub Copilot を使って、セキュリティ修正の過程で発生した問題を特定して解決します。

1. 完成したアプリケーションを実行して、全体的な動作を確認します。

    アプリケーションを実行し、コンソールの出力を確認します。

    ```bash
    dotnet run
    ```

    出力を元のアプリケーション実行の記録と比較します。 ログの機密情報が大幅に減っているはずです。

1. SQL インジェクションの修正をテストします (Issue #1)。

    `SearchProducts` メソッドが脆弱な SQL クエリをログしなくなったこと、および正当な検索語句に対して検索機能が正しく機能することを確認します。

1. パスワード セキュリティの強化を確認します (Issue #2)。

    ユーザー登録とログイン プロセスでプレーンテキストのパスワードがログされなくなったことと、より強力なパスワード ハッシュが実装されていることを確認します。 アプリケーションによってユーザーの認証が引き続き正しく行われる必要があります。

1. 機密データ ログの修正を確認します (Issue #3)。

    トランザクションを正常に処理する機能を維持しながら、支払い処理とユーザー操作でパスワード、完全なクレジット カード番号、または CVV コードがログされないようにします。

1. ハードコーディングされた資格情報の削除を検証します (Issue #4)。

    ハードコーディングされた管理者資格情報がログやセキュリティ監査に表示されなくなり、管理機能にはセキュリティで保護された構成を使用して引き続きアクセスできることを確認します。

1. クレジット カード保存のコンプライアンスをテストします (Issue #5)。

    注文モデルで完全なクレジット カード番号または CVV コードが格納されなくなり、マスクされた支払い情報のみが表示目的で保持されることを確認します。

1. 入力検証の修正を確認します (Issue #6)。

    改善された ValidateInput メソッドが、警告をログして true を返す代わりに、危険な入力を適切に拒否するようになったことを確認します。

1. セッション トークンのセキュリティを確認します (Issue #7)。

    アプリケーションの実行中にセッション トークンが生成された場合は、前のユーザー名タイムスタンプ パターンに従うのではなく、セッション トークンがランダムで予測不可能に見えるかどうかを確認します。

1. 電子メール検証の改善をテストします (Issue #8)。

    電子メールの検証で、"@" and "" 文字を含む文字列を受け入れる代わりに、無効な電子メール形式が適切に拒否されることを確認します。

1. パスワード要件の機能強化を検証します (Issue #9)。

    パスワード検証で、4 文字の文字列を受け入れるのではなく、適切な複雑さの要件が適用されるようになったことをテストします。

1. 情報漏えいの修正を確認します (Issue #10)。

    セキュリティ監査方法が削除または制限されていること、および冗長なエラー メッセージで機密のシステム情報が公開されなくなったことを確認します。

1. 全体的なセキュリティ体制を元のバージョンと比較します。

    アプリケーションを実行し、コンソール出力と最初の観測値を比較します。 アプリケーションは、すべてのコア機能を維持しながら、大幅に強化されたセキュリティを示すはずです。

1. 残っている問題や改善する領域を文書化します。

    さらに注意が必要なセキュリティ上の問題や、対処が必要な機能上の問題を記録します。

### 変更をコミットして issue を閉じる

適切なバージョン管理プラクティスにより、セキュリティの改善が適切に文書化されて追跡されます。 コミット メッセージでは、実装されたセキュリティ修正を明確に記述して、行われた変更とその理由をチーム メンバーが簡単に理解できるようにする必要があります。 わかりやすいコミット メッセージを使って GitHub の issue を閉じると、セキュリティ修復作業の明確な監査証跡が作成されます。

このタスクでは、セキュリティの改善をリポジトリにコミットし、対応する GitHub の issue を閉じます。

そのためには、以下の手順を実行してください。

1. コードベースに行われたすべての変更を確認します。

    Git を使って、変更されたファイルを確認します。

    ```bash
    git status
    git diff
    ```

1. セキュリティ関連のすべての変更をコミットのためにステージングします。

    変更したファイルをステージング領域に追加します。

    ```bash
    git add .
    ```

1. すべての GitHub の issue を参照する包括的なメッセージを使用して、すべてのセキュリティ修正をコミットします。

    このトレーニング演習で特定されたすべてのセキュリティの脆弱性に対処する 1 つのコミットを作成します。

    ```bash
    git commit -m "Fix all ContosoShopEasy security vulnerabilities

    Security improvements implemented:
    - Fix SQL injection in ProductService SearchProducts method
    - Replace MD5 with secure password hashing (bcrypt/PBKDF2)
    - Remove sensitive data from debug logging (passwords, card numbers, CVV)
    - Remove hardcoded admin credentials, use environment variables
    - Fix PCI DSS violations in Order model (remove full card storage)
    - Fix input validation bypass to properly reject dangerous input
    - Implement secure session token generation with crypto randomness
    - Strengthen email validation with proper format checking
    - Improve password requirements (8+ chars, complexity rules)
    - Reduce information disclosure from security audit and debug logs

    Fixes #1 #2 #3 #4 #5 #6 #7 #8 #9 #10"
    ```

    > **注**:運用環境では、通常、各 issue は個別のテストとコード レビューを使用して個別のコミットで対処されます。 この単一コミットアプローチは、トレーニング演習で時間を節約するためにのみ使用されます。

1. 変更を GitHub リポジトリにプッシュします。

    ```bash
    git push origin main
    ```

1. GitHub の issue が自動的に閉じられることを確認します。

    GitHub 上のリポジトリに移動し、issue がそれを参照するコミット メッセージによってクローズとしてマークされていることを確認します。

1. コミット履歴をレビューし、すべてのセキュリティ修正が適切に文書化されていることを確認します。

    コミット メッセージでセキュリティの改善が明確に説明されていて、将来の参照のための適切な監査証跡が提供されていることを確認します。

## クリーンアップ

演習が完了したので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに保持したくない変更が加えられていないか確認します。 たとえば、ResolveGitHubIssues リポジトリを削除することが必要になる可能性があります。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したリポジトリのローカル クローンをアーカイブするか、削除することができます。
