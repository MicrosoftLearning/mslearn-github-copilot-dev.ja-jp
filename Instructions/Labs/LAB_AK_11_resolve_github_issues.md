<!-- ---
lab:
    title: 'Exercise - Resolve GitHub issues using GitHub Copilot'
    description: 'Learn how to identify and address performance bottlenecks and code inefficiencies using GitHub Copilot tools.'
--- -->

# GitHub Copilot を使用して GitHub の issue を解決する

GitHub の issue は、プロジェクトのバグ、機能強化、タスクを追跡するための強力な手段です。

この演習では、GitHub Copilot を使用して、eコマース アプリケーションのセキュリティの脆弱性に関連する GitHub の issue を分析して解決します。

この演習の所要時間は約 **40** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です: Git 2.48 以降、.NET SDK 9.0 以降、C# 開発キット拡張機能をインストールした Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

この演習のラボ環境としてローカル PC を使用している場合:

- ローカル PC をラボ環境として構成する方法については、ブラウザーで次のリンクを開いてください。<a href="https://go.microsoft.com/fwlink/?linkid=2320147" target="_blank">ラボ環境のリソースを構成する</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開きます: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習に、ホストされたラボ環境をお使いの場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーのサイト ナビゲーション バーに次の URL を貼り付けてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

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

1. ページの左側にある **[すべてのワークフロー]** で、**[ContosoShopEasy トレーニングの issue の作成]** ワークフローを選択し、**[ワークフローの実行]** を選択します。

1. 表示されたワークフロー ダイアログで、「**作成**」と入力し、**[ワークフローの実行]** を選択します。

1. 画面上でワークフローの進行状況を監視します。

    しばらくすると、ページが更新され、進行状況バーが表示されます。 ワークフローは 1 分以内に正常に完了します。

1. 続行する前に、ワークフローが正常に完了していることを確認します。

    緑色の円で囲まれたチェックマークは、ワークフローが正常に実行されたことを示します (ワークフロー名の左側に表示されます)。

    ワークフロー名の左側に、赤い円で囲まれた X が表示されている場合は、ワークフローが失敗したことを意味します。 ワークフローが正常に実行されない場合は、リポジトリのインポート時にアカウントを選択し、そのアカウントに読み取りと書き込みのアクセス許可があることを確認します。 GitHub の **Copilot とチャット**機能を使用して、issue の診断に役立てることができます。

### GitHub で issue を確認する

GitHub の issue は、バグ、セキュリティの脆弱性、機能強化要求の一元的な追跡システムとして機能します。 各 issue では、問題に関するコンテキスト、重大度、アプリケーションへの潜在的な影響が提供されます。 コードに進む前にこれらの issue を理解することは、優先順位を決めるのに役立ち、包括的な修復が保証されます。

このタスクでは、GitHub の issue を確認し、対処する必要があるセキュリティの脆弱性を調べます。

そのためには、以下の手順を実行してください。

1. リポジトリの **[Issues]** タブを選択し、少し時間を取って [Issues] ページを確認します。

    10 件の開いている issue が一覧表示されます。 次のことに注意してください。

    - すべての issue はバグとしてラベル付けされています。
    - すべての issue には優先度レベルがあります。
    - どの issue も誰にも割り当てられていません。

1. 重大な issue のみを表示するには、**[ラベル]** ドロップダウンを選択し、**[重大]** ラベルを選択します。

    issue の一覧が更新されて、重大な issue のみが表示されます。

    - **🔐 ハードコーディングされた管理者資格情報を削除する**  

    - **🔐 クレジット カードのデータ保存違反を修正する**  

1. 高優先度の issue のみを表示するには、**[ラベル]** ドロップダウンを選択し、**[重大]** の選択を解除してから、**[高優先度]** ラベルを選択します。

    issue の一覧が更新されて、高優先度の issue のみが表示されます。

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

1. 次の重大および高優先度の issue を選択し、**[割り当て]** ドロップダウンを使用して自分に割り当てます。

    - **🔐 クレジット カードのデータ保存違反を修正する**  

    - **🔐 製品検索の SQL インジェクションの脆弱性を修正する**  

    一般的には、優先度の高い issue から先に取り組むのが最善の方法です。 issue を自分に割り当てると、修復プロセスを進める際に進行状況を追跡するのに役立ちます。

### リポジトリをローカルにクローンし、コードベースを確認する

ContosoShopEasy アプリケーションはエンタープライズ アプリケーションに典型的な階層構造のアーキテクチャに従っており、モデル、サービス、データ アクセス、セキュリティ コンポーネントが明確に分離されています。

少し時間を取って既存のコードベースの基本的な構造、動作、機能を理解することは、セキュリティの問題を解決する際に最初に行うべき重要なステップです。

このタスクでは、リポジトリのローカル クローンを作成し、Visual Studio Code でプロジェクト構造を調べ、アプリケーションのコンソール出力を確認して、コードベース内のセキュリティの脆弱性を特定します。

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

    - **Data/**: **OrderRepository.cs**、**ProductRepository.cs**、**UserRepository.cs** のデータ リポジトリが含まれています。

    - **Models/**: **Category.cs**、**Order.cs**、**Product.cs**、**User.cs** のデータ モデルが含まれています。

    - **Security/**: **SecurityValidator.cs** のセキュリティ検証ロジックが含まれています

    - **Services/**: **OrderService.cs**、**PaymentService.cs**、**ProductService.cs**、**UserService.cs** のビジネス ロジックが含まれています。

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

    ContosoShopEasy アプリケーションでは、意図的に過剰に作成されたログ記録を教育ツールとして使用します。 ログはコードベース内のセキュリティの問題を明らかにするだけでなく、一部には、実際に問題を引き起こすものがあります。 セキュリティの問題を引き起こすログを含めることにより、一部の運用システムで実際に見られるオーバーログの問題を例示します。 ContosoShopEasy アプリケーションで作成されるログは、開発者が次の 2 種類の問題を区別するのに役立ちます。

    - ログによって引き起こされた問題:ContosoShopEasy アプリケーション内の脆弱性の約 40% は、過剰なログが原因で発生します。 たとえば、パスワードの露出、クレジット カード番号の開示、セッション トークンの露出、構成情報の開示などがあります。

    - ログとは関係なく存在する問題:ContosoShopEasy アプリケーション内の脆弱性の約 60% は、ログとは関係なく存在します。 たとえば、SQL インジェクション、脆弱なパスワード ハッシュ、ハードコーディングされた資格情報、予測可能なトークン、入力検証のバイパス、クレジット カードの保存、弱い電子メール検証などがあります。 これらの脆弱性はログによって生じるものではありませんが、ログはトレーニング環境内の問題を明らかにするのに役立ちます。

1. コードベース内のセキュリティの脆弱性の確認を開始するには、**Models** フォルダーを展開し、**Order.cs** ファイルを開きます。

1. 下方向にスクロールして、**PaymentInfo** クラスを見つけます。

    CardNumber プロパティと CVV プロパティに関するコメントに注目してください。 このコードは、自分に割り当てた "**クレジット カードのデータ保存違反を修正する** " issue に関連しています。

1. **Security** フォルダーを展開し、**SecurityValidator.cs** ファイルを開きます。

    ContosoShopEasy アプリケーションでは、コード コメント、ロジック、ログを使用してセキュリティの問題を明らかにしていることに注意してください。 実装は作為的なものですが、このアプローチは、実際のアプリケーションで一般的な脆弱性を浮き彫りにするのに役立ちます。

    > **注**:SecurityValidator.cs クラスは、ContosoShopEasy アプリケーションのセキュリティ関連のロジックを一元化するように設計されており、セキュリティの issue の特定、管理、解決を容易にします。 実際のアプリケーションでは、SecurityValidator のようなクラスを使用して、セキュリティのベスト プラクティスと入力検証を強制することができます。 ただし、ContosoShopEasy の特定の実装は、意図的に安全ではなく、脆弱性が露呈するように作成されています。

1. 少し時間を取って、次のセキュリティに関する issue を見つけます。

    - ファイルの先頭付近で、管理者資格情報定数 (7 から 9 行目) に関連するコメントに注目してください。 このコードは、"ハードコーディングされた管理者資格情報を削除する" issue に関連しています。

    - ValidateInput メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"入力検証セキュリティ バイパスを修正する" issue に関連しています。

    - ValidateEmail メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"電子メール検証のセキュリティを強化する" issue に関連しています。

    - ValidatePasswordStrength メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"パスワードのセキュリティ要件を強化する" issue に関連しています。

    - ValidateCreditCard メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、自分に割り当てた "**クレジット カードのデータ保存違反を修正する** " issue に関連しています。

    - GenerateSessionToken メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"予測可能なセッション トークンの生成を修正する" issue に関連しています。

    - RunSecurityAudit メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"エラー メッセージでの情報漏えいを削減する (コンソール出力)" issue に関連しています。

    SecurityValidator.cs ファイル内のいくつかのメソッドは、"デバッグ ログから機密データを削除する" issue にも関連しています。

    SecurityValidator クラスによって明らかになる問題は、一般的に、実際のアプリケーションのクラス (特にレガシまたは保守が不十分なコードベース) に分散して見られるものです。

1. **Services** フォルダーを展開し、**UserService.cs** ファイルを開きます。

1. 少し時間を取って、次のセキュリティに関する issue を見つけます。

    - RegisterUser、LoginUser、ValidateUserInput の各メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"デバッグ ログから機密データを削除する" issue に関連しています。

    - GetMd5Hash メソッドを見つけて、セキュリティの脆弱性を説明するコメントを確認します。 このコードは、"MD5 パスワード ハッシュを安全な代替ハッシュに置き換える" issue に関連しています。

1. **PaymentService.cs** ファイルを開きます。

1. 少し時間を取って、支払いと検証の方法のコメントを確認します。

    このコードのセキュリティの脆弱性は、自分に割り当てた "**クレジット カードのデータ保存違反**" issue に関連しています。

    PaymentService クラスは、他の issue にも関連しています。 たとえば、"デバッグ ログから機密データを削除する" と "エラー メッセージの情報漏えいを削減する (コンソール出力)" の issue です。

    PaymentService クラスでは、OrderRepository を使用して支払関連の注文データを保持していることに注意してください。 OrderRepository クラスで機密データが適切に処理されない場合、OrderRepository クラスでデータの露出の脆弱性が生じる危険性があります。

1. **ProductService.cs** ファイルを開きます。

1. 少し時間を取って、SearchProducts メソッドを確認します。

    このコードのセキュリティの脆弱性は、自分に割り当てた "**製品検索の SQL インジェクションの脆弱性を修正する**" issue に関連しています。

    ProductService の SearchProducts メソッドによって ProductRepository の SearchProducts メソッドが呼び出されることに注意してください。 リポジトリ メソッドを分析して、これにもセキュリティの強化が必要であるかどうかを判断することが必要な場合があります。

1. 自分に割り当てた問題に関連するコード ファイルの一覧を作成します。

    自分に割り当てた問題は次のとおりです。

    - **🔐 クレジット カードのデータ保存違反を修正する**
    - **🔐 製品検索の SQL インジェクションの脆弱性を修正する**

    "クレジット カードのデータ保存違反を修正する" issue に関連するコード ファイルは次のとおりです。

    - Models/Orders.cs/PaymentInfo クラス
    - Security/SecurityValidator.cs/ValidateCreditCard メソッド
    - Data/OrderRepository.cs

    "製品検索の SQL インジェクションの脆弱性を修正する" issue に関連するコード ファイルは次のとおりです。

    - Services/ProductService.cs/SearchProducts メソッド
    - Data/ProductRepository.cs/SearchProducts メソッド

### GitHub Copilot の質問モードを使って issue を分析する

GitHub の issue には、多くの場合、修正プログラムを実装する前に慎重に分析する必要がある複雑な問題が含まれています。 根本原因、潜在的な影響、最適な修復戦略を理解することは、効果的な解決に不可欠です。

Visual Studio Code 用の次の GitHub 拡張機能は、GitHub の issue を分析するのに役立ちます。

- **GitHub Copilot Chat**:GitHub Copilot の質問モードが備えるインテリジェントなコード分析機能は、セキュリティの脆弱性を明らかにし、潜在的な影響を理解し、修復戦略を提案するのに役立ちます。

- **GitHub pull request**。GitHub pull request 拡張機能は、GitHub の issue を Visual Studio Code に直接統合し、開発環境を離れることなく issue を管理および操作できるようにします。

セキュリティの問題を体系的に分析すると、修正を実装する前に、問題の包括的な理解を深めることができます。 このアプローチにより、解決策では単なる症状ではなく根本原因に対処できます。

このタスクでは、GitHub Copilot の Ask モードを使用して、自分に割り当てた GitHub の issue を分析します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat と GitHub Pull Requests 拡張機能が Visual Studio Code にインストールされていることを確認します。

    Visual Studio Code で [拡張機能] ビューを開き、インストールされている拡張機能を確認します。 いずれかの拡張機能が見つからない場合は、続行する前にインストールします。

    たとえば、次の手順を使用して GitHub Pull Requests 拡張機能をインストールできます。

    1. Visual Studio Code で、[拡張機能] ビューを開きます。

    1. [拡張機能] ビューで、「**GitHub Pull Requests**」を検索します。

    1. 検索結果から **[GitHub Pull Requests]** を選択し、拡張機能をインストールします。

        インストールが完了したら、変更を有効にするために Visual Studio Code の再読み込みが必要になる場合があります。 **[GitHub]** アイコンを Visual Studio Code のアクティビティ バーに追加する必要があります。

1. [GitHub pull request] ビューを開くには、アクティビティ バーから **[GitHub]** アイコンを選択します。

    ダイアログが表示されたら、お使いの GitHub アカウントにサインインして、Visual Studio Code を GitHub リポジトリに接続します。

1. GitHub ビューには、**[Pull Requests]** と **[Issues]** の 2 つのセクションが含まれていることに注意してください。

    **[issue]** セクションでは、Visual Studio Code 内で直接 GitHub リポジトリから issue を表示および管理できます。 **[Pull Requests]** セクションでは、pull request を管理できます。

1. **[Pull Requests]** セクションを折りたたみます。

1. 少し時間を取って、**[Issues]** セクションを確認します。

    自分に割り当てた問題が [My Issues] セクションに一覧表示されていることに注意してください (マイルストーンは定義されていません)。 **[Recent Issues]** セクションを展開すると、リポジトリに追加されたすべての issue が表示されます。

1. [My Issues] セクションで、**[製品検索の SQL インジェクションの脆弱性を修正する]** を選択します。

    GitHub Pull Requests 拡張機能によって、新しいエディター タブに issue の詳細が表示されます。このタブでは、issue の説明、コメント、関連情報を確認できます。issue の詳細を使用すると、チャット ビューで GitHub Copilot に送信するプロンプトを作成するのに役立ちます。

1. GitHub Copilot のチャット ビューを開き、**[Ask]** モードが選択されていることを確認します。

    チャット ビューをまだ開いていない場合は、Visual Studio Code ウィンドウの上部にある **[チャット]** アイコンを選びます。 チャット モードが **[Ask]** に設定されており、**GPT-4.1** モデルを使用していることを確認します。

    > **注**:GPT-4.1 モデルは優れたコード分析機能を提供し、GitHub Copilot Free プランに含まれています。 別のモデルを選択すると、得られる結果が異なる場合があります。

1. クリーンなチャット セッションから開始していることを確認します。

    チャット セッションは、GitHub Copilot との対話を整理するのに役立ちます。 各セッションは独自のコンテキストを維持するため、特定のタスクや issue に集中できます。 セッション内の会話履歴により継続性が維持されるため、GitHub Copilot は以前の対話に基づいてより正確で関連性の高い応答を提供することができます。 このチャット会話では、自分に割り当てた ContosoShopEasy アプリケーション内のセキュリティの脆弱性を分析して解決することに重点を置きます。 GitHub Copilot の Ask モードを使用して GitHub の issue の分析を完了したら、同じ会話を使用して、GitHub Copilot の Agent モードを使用してコード変更を実装できます。 GitHub Copilot は、Ask モードからの詳細な分析を使用して、Agent モードでコード生成を通知します。これにより、修正は、特定された脆弱性および推奨される修復戦略と確実に一致します。

    必要に応じて、**[新しいチャット]** ボタン ([チャット] パネルの上部にある **+** アイコン) を選択すると、新しいチャット セッションを開始できます。

#### SQL インジェクションの脆弱性を分析する

SQL インジェクションの脆弱性は ProductService.cs ファイルに存在し、ProductRepository.cs ファイルにも存在する可能性があります。 脆弱性の完全な範囲を把握するために、両方のファイルを分析します。

SQL インジェクションの脆弱性を分析するには、次の手順に従います。

1. **ProductService.cs** ファイルを開き、**SearchProducts** メソッドを見つけます。

1. コード エディターで、**SearchProducts** メソッド全体を選択します。

    エディターでコードを選択すると、チャット コンテキストがフォーカスされます。 GitHub Copilot では、選択したコードを使用して、関連する分析と推奨事項が提供されます。

1. GitHub Copilot に、SQL インジェクションの脆弱性のコードを分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Analyze the SearchProducts method for SQL injection vulnerabilities. Consider the following issue description: "The product search functionality is vulnerable to SQL injection attacks. User input is directly concatenated into SQL queries without proper parameterization or sanitization." Explain the impact of directly concatenating user input into SQL queries without proper parameterization or sanitization. What are the potential consequences if an attacker exploits this vulnerability?
    ```

1. GitHub Copilot の分析を確認します。

    GitHub Copilot によって、このメソッドは、適切なサニタイズを行わないままユーザー入力を使用して SQL クエリを構築することが明らかになりました。 シミュレートされた SQL クエリは、ユーザー入力がどのようにしてクエリ文字列に直接連結されるかを示しており、これにより、攻撃者がデータベースに対するクエリを操作できる危険性があります。

1. 特定の修復ガイダンスを要求します。

    たとえば、最初の分析を確認した後、次のプロンプトを送信できます。

    ```text
    How can I modify this method to prevent SQL injection attacks? What secure coding practices should I implement to safely handle user input in database queries? Where should user input be validated and sanitized? What techniques can I use to construct SQL queries safely?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    SQL インジェクション リスクの管理に役立つパラメーター化クエリまたは ORM メソッドを使用するための推奨事項が表示されます。 入力の検証とサニタイズの手法に関する提案も表示される場合があります。 多くの場合、GitHub Copilot により、提案を実装する方法を示すコード スニペットが提供されます。

1. **Data** フォルダー内の **ProductRepository.cs** ファイルを開き、**SearchProducts** メソッドを見つけます。

    コード レビュー中に、ProductRepository 内の SearchProducts メソッドが ProductService の SearchProducts メソッドによって呼び出されることに気付きました。 リポジトリ メソッドを分析すると、これにもセキュリティの強化が必要であるかどうかを判断できます。

1. コード エディターで、**SearchProducts** メソッド全体を選択し、GitHub Copilot に、SQL インジェクションの脆弱性についてコードを分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Analyze the SearchProducts method in ProductRepository. Does this method properly handle the search term to prevent SQL injection, or are there vulnerabilities here as well? How does this method relate to the vulnerability in ProductService?
    ```

1. GitHub Copilot によるリポジトリ メソッドの分析を確認します。

    GitHub Copilot によって、リポジトリメソッドでは安全な文字列操作 (ToLower と Contains) が使用されているが、主な脆弱性は、シミュレートされた SQL クエリがユーザー入力を使用して構築される ProductService レイヤーにあることがわかりました。 リポジトリの実装自体は比較的安全ですが、サービス レイヤーでは、SQL クエリの不適切な構築によって脆弱性が露呈します。

1. ProductRepository.cs ファイルを閉じます。

1. GitHub Copilot に、SQL インジェクションの脆弱性に関して入力の検証とサニタイズの手法を含む包括的な修復戦略を提案するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    #codebase I need to resolve SQL injection vulnerabilities associated with the SearchProducts method in the ProductService.cs file. Notice that user input is directly concatenated into SQL queries without proper parameterization or sanitization. The updated codebase should use parameterized queries or prepared statements, implement proper input validation and sanitization, remove debug logging of SQL queries, and add input length restrictions. My acceptance criteria includes: User input is properly parameterized; No raw SQL construction with user input; Input validation prevents malicious characters; Debug logging removed or sanitized. Review the codebase and identify the code files that must be updated to address the SQL injection vulnerability. Based on your code review and the current Chat conversation, suggest a phased approach to required file updates.
    ```

1. 修復フェーズの間に参照できるよう、分析の結果を文書化します。

    両方の脆弱性カテゴリに関する GitHub Copilot の推奨事項をメモします。 このドキュメントは、次のタスクでセキュリティの修正を実装する際のガイドになります。

#### クレジット カードのデータ保存違反を分析する

クレジット カードのデータ保存の脆弱性は複数のファイル (Order.cs モデル、PaymentService.cs サービス、SecurityValidator.cs 検証ツール、OrderRepository.cs データ レイヤー) に存在します。 これらのファイルを分析して、脆弱性の完全な範囲を把握します。

クレジット カードのデータ保存違反を分析するには、次の手順に従います。

1. **Models** フォルダーで、**Order.cs** ファイルを開き、**PaymentInfo** クラスを見つけます。

1. コード エディターで、**PaymentInfo** クラス内の **CardNumber** プロパティと **CVV** プロパティを選択します。

    これらのプロパティがセキュリティの脆弱性であることを示すコメントに注意してください。 完全なカード番号と CVV コードを保存すると、PCI DSS コンプライアンス要件に違反します。

1. GitHub Copilot に、クレジット カードのデータ保存違反を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    Why is storing full credit card numbers and CVV codes in the PaymentInfo class a PCI DSS compliance violation? What are the proper ways to handle payment card data securely?
    ```

1. GitHub Copilot の分析を確認します。

    GitHub Copilot によって、PCI DSS 要件では、承認後に CVV コードなどの機密の認証データを保存することは禁止されていることが説明されます。 また、完全なカード番号はトークン化またはマスキングする必要があることも説明されます。

1. 特定の修復ガイダンスを要求します。

    たとえば、次のプロンプトを送信できます。

    ```text
    How should I modify the PaymentInfo class to comply with PCI DSS requirements? What properties should I add or change to store payment information securely?
    ```

1. 少し時間を取って、GitHub Copilot の修復提案を確認します。

    CVV プロパティを完全に削除し、CardNumber をマスキングしたバージョンまたはトークンに置き換え、最後の 4 桁のみを保存し、表示目的でカードの種類のプロパティを追加するための推奨事項が表示されます。

1. **PaymentService.cs** ファイルを開き、**ProcessPayment** メソッドを見つけます。

1. コード エディターで、**ProcessPayment** メソッド全体を選択します。

    このメソッドは PaymentInfo オブジェクトを作成し、完全なカード番号と CVV を保存することに注意してください。 このメソッドは、機密の支払い情報もログします。

1. GitHub Copilot に、クレジット カードのデータ保存の問題について ProcessPayment メソッドを分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    What security vulnerabilities exist in the ProcessPayment method related to credit card data storage and logging? How does this method contribute to the PCI DSS violations?
    ```

1. GitHub Copilot の分析を確認します。

    GitHub Copilot によって、完全なカード番号と CVV コードのログ、PaymentInfo オブジェクトへのこれらの値の保存、処理フロー全体での機密データの露出という複数の問題が特定されます。

1. ProcessPayment メソッドに関する特定の修復ガイダンスを要求します。

    たとえば、次のプロンプトを送信できます。

    ```text
    How should I modify the ProcessPayment method to handle credit card data securely? What changes are needed to prevent storing and logging sensitive card information?
    ```

1. **SecurityValidator.cs** ファイルを開き、**ValidateCreditCard** メソッドを見つけます。

1. コード エディターで、**ValidateCreditCard** メソッド全体を選択します。

    このメソッドは完全なクレジット カード番号をログします。これは、セキュリティの脆弱性です。

1. GitHub Copilot に、ValidateCreditCard メソッドを分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    What security issues exist in the ValidateCreditCard method? How should credit card validation be performed without logging sensitive data?
    ```

1. GitHub Copilot による分析と修復の提案を確認します。

    GitHub Copilot によって、セキュリティの問題の一覧と、安全なコーディング プラクティスに関するいくつかの推奨事項が生成されます。 これらの推奨事項としては、ログ ステートメントでのクレジット カード番号の削除またはマスキング、アルゴリズムによるカード番号の検証、カード番号の長さと形式の検証の改善などがあります。

1. **Data** フォルダー内の **OrderRepository.cs** ファイルを開きます。

1. ファイルを調べて、PaymentInfo オブジェクトが処理されるかどうかを確認します。

    OrderRepository クラスには、PaymentInfo などの Order オブジェクトが保存されることに注意してください。 PaymentInfo クラスに完全なカード番号と CVV コードが保存されると、この機密データがリポジトリで保持されます。

1. GitHub Copilot に、クレジット カードのデータ保存に対する OrderRepository の影響を分析するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    How does the OrderRepository contribute to credit card data storage violations? What happens when Order objects containing PaymentInfo with full card numbers and CVV codes are stored?
    ```

1. GitHub Copilot の分析を確認します。

    GitHub Copilot によって、Order オブジェクトおよび PaymentInfo オブジェクト内のあらゆるデータがリポジトリで保持されることが説明されます。 PaymentInfo モデルが安全なデータ (トークン、最後の 4 桁) のみを保存するように固定されている場合、リポジトリには安全なデータが自動的に保存されます。

1. OrderRepository.cs ファイルを閉じます。

1. GitHub Copilot に、"クレジット カード データ ストレージ違反を修正する" issue について、入力の検証とサニタイズの手法を含む包括的な修復戦略を提案するように依頼します。

    たとえば、次のプロンプトを送信できます。

    ```text
    #codebase I need to resolve credit card data storage violations associated with the PaymentInfo model in the OrderRepository.cs file. Notice that the model currently stores full card numbers and CVV codes. The updated codebase should never store CVV codes (remove CVV storage completely), tokenize card numbers and store tokens instead of actual card numbers, mask the display of credit card numbers to show only last 4 digits, and implement proper encryption if card data must be stored temporarily. My acceptance criteria includes: CVV storage completely removed; Full card numbers replaced with tokens; Only the last 4 digits of a credit card are stored for display; Card type detection implemented. Review the codebase and identify the code files that must be updated to address the credit card data storage violations. Based on your code review and the current Chat conversation, suggest a phased approach to required file updates.
    ```

1. 修復フェーズの間に参照できるよう、分析の結果を文書化します。

    両方の脆弱性カテゴリに関する GitHub Copilot の推奨事項をメモします。 このドキュメントは、次のタスクでセキュリティの修正を実装する際のガイドになります。

### GitHub Copilot のエージェント モードを使って issue を解決する

GitHub Copilot のエージェント モードを使うと、複数のファイルとメソッドで複雑なセキュリティ修正を自律的に実装できます。 分析と推奨事項を提供する質問モードとは異なり、エージェント モードでは、コードを直接変更してセキュリティの改善を実装できます。 このアプローチは、複数の関連する脆弱性に一貫して対処する必要がある体系的なセキュリティ修復に特に有効です。

このタスクでは、GitHub Copilot のエージェント モードを使用して、自分に割り当てた GitHub の issue を修復します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat をエージェント モードに切り替えます。

    エージェント モードの GitHub Copilot は、ユーザーの指示に基づいてコードを直接変更できます。 エージェント モードは、コードベース内の関連ファイルを確認して、適切なコンテキストを確立するために機能します。 エージェントが複雑なタスクを実行するために必要な情報を確実に得られるように、コンテキストにファイルとフォルダーを手動で追加できます。

1. 少し時間を取って、修復戦略を検討します。

    GitHub Copilot の Ask モードを使用して、完了した分析に基づく修復戦略を作成します。 自分に割り当てた issue に対処する順序、issue を解決するためのアプローチ、コードの脆弱性が正常に修復されたことを確認する方法を検討します。

    自分に割り当てた 2 つの GitHub の issue は次のとおりです。

    1. 🔐 製品検索の SQL インジェクションの脆弱性を修正する (高優先度)
    1. 🔐 クレジット カードのデータ保存違反を修正する (重大な優先度)

    重大度はクレジット カードの保存の問題の方が高くなりますが、修正は SQL インジェクションの問題の方が簡単であるため、先に対処できます。 これにより、より複雑なクレジット カードの保存違反に取り組む前に、修正がより簡単なワークフローを検証できます。

    これらの問題は、コードベース内の特定のファイルとメソッドに関連しています。

    - **SQL インジェクションの問題**:ProductService.cs (SearchProducts メソッド)
    - **クレジット カードの保存の問題**:Models/Order.cs (PaymentInfo クラス)、PaymentService.cs (ProcessPayment メソッド)、SecurityValidator.cs (ValidateCreditCard メソッド)、OrderRepository.cs (データの永続化)

    > **注**:GitHub Pull Requests 拡張機能では、issue の別個のブランチでの個別処理がサポートされています。 issue を個別に解決すると、追跡可能性が向上し、コード レビューが容易になり、問題が発生した場合にロールバック オプションの安全性が高くなります。 運用環境では、別個のコミットと pull request で各 issue に個別に対処する必要があります。

#### SQL インジェクションの脆弱性を解決する

SQL インジェクションの脆弱性を解決するには、次の手順に従います。

1. コード エディターで開いているすべてのファイルを閉じます。

    ファイルを閉じると、エージェントは、コンテキストに追加するファイルに集中しやすくなります。 気付かずにエディターでファイルを開いたままにしておくと、目前のタスクに集中できなくなることがあります。

1. **ProductService.cs** ファイルをチャット コンテキストに追加します。

    SQL インジェクションの問題は、主に ProductService.cs ファイルの SearchProducts メソッドに存在します。

1. GitHub Copilot に、SQL インジェクションの脆弱性に対処するように依頼します。

    GitHub Copilot の Ask モードを使用した分析により、このメソッドは、適切なサニタイズを行わないままユーザー入力を使用して SQL クエリを構築することが明らかになりました。 分析を使用して、エージェントが脆弱性の修復に使用できる明確なタスク命令を作成します。

    たとえば、エージェントに次のタスクを割り当てることができます。

    ```text
    #codebase I need you to fix the SQL injection vulnerability in the SearchProducts method. Review the current Chat conversation related to SQL injection vulnerabilities to identify my expected code fixes and acceptance criteria. Remove the simulated SQL query logging that demonstrates the vulnerability, and implement proper input sanitization to safely handle search terms. Ensure that the method still functions correctly for legitimate searches while preventing malicious input. Update the DisplayKnownVulnerabilities method in SecurityValidator.cs to reflect that SQL injection protection is enabled.
    ```

1. エージェントの進行状況を監視します。

    エージェントはコードを変更して脆弱なログを削除し、より安全な入力処理を実装します。

1. 少し時間をかけて、提案された変更を確認し、[チャット] ビューで **[Keep]** を選択します。

    コード エディターで GitHub Copilot の編集候補を常に確認します。 セキュリティ上の問題に対処しても機能が確実に維持されるようにします。

    変更には、次のものを含める必要があります。
    - シミュレートされた SQL クエリ ログの削除
    - 検索用語を露出するデバッグ ログの削除またはサニタイズ
    - 入力検証またはサニタイズ ロジックの追加

    運用環境では、チームは次の issue に進む前に、次のチェックリストを完了する必要があります。

    - コードに含まれていた脆弱性はなくなりました。
    - アプリケーションは引き続き正しく機能します。
    - セキュリティのベスト プラクティスが実装され、新しいセキュリティの問題は発生しません。
    - 自動テスト (使用可能な場合) は正常に完了します。
    - コードの更新は明確に文書化されています。
    - 変更は、説明メッセージでコミットされ、issue をマージして閉じる前にピア レビューされます。

#### クレジット カードのデータ保存違反を解決する

クレジット カードのデータ保存違反は複数のファイルにまたがるため、変更の調整が必要です。 データ モデルの変更、支払いデータを処理するサービスの更新、ログからの機密データの削除を行う必要があります。

クレジット カードのデータ保存違反を解決するには、次の手順に従います。

1. エディターで開いているファイルをすべて閉じ、**Order.cs** ファイル (Models フォルダー内) をチャット コンテキストに追加します。

    このファイル内の PaymentInfo クラスには、完全なカード番号と CVV コードが保存されます。これは、PCI DSS コンプライアンス要件に違反します。

1. GitHub Copilot に、PaymentInfo クラスを修正するように依頼します。

    たとえば、エージェントに次のタスクを割り当てることができます。

    ```text
    Fix PCI DSS compliance violations in the PaymentInfo class in Order.cs. Remove the CVV property entirely as CVV codes should never be stored. Replace the CardNumber property with a CardLastFourDigits property that stores only the last 4 digits. Add a CardType property to identify the card brand (Visa, Mastercard, etc.). Update the constructor and any initializations accordingly.
    ```

1. エージェントの進行状況を監視し、提案された変更を検討します。

    エージェントによって、機密データの保存を削除するように PaymentInfo クラスが変更されます。 変更を確認し、issue に正しく対処している場合は、**[Keep]** を選択します。

1. Order.cs ファイルを閉じ、チャット コンテキストに **PaymentService.cs** ファイルを追加します。

    このファイル内の ProcessPayment メソッドは、機密の支払いデータをログし、完全なカード番号と CVV コードを含む PaymentInfo オブジェクトを作成します。

1. GitHub Copilot に、ProcessPayment メソッドを修正するように依頼します。

    たとえば、エージェントに次のタスクを割り当てることができます。

    ```text
    Fix the credit card data handling in the ProcessPayment method in PaymentService.cs. Remove all logging of full card numbers, CVV codes, and other sensitive payment data. Update the PaymentInfo object creation to store only the last 4 digits of the card number and the card type, without storing CVV. Implement card number masking in any remaining log statements (show only last 4 digits). Ensure the payment processing logic still works correctly.
    ```

1. エージェントの進行状況を監視します。

    変更には、次のものを含める必要があります。
    - ログ ステートメント内の機密データの削除またはマスキング
    - 最後の 4 桁のみを使用するように PaymentInfo オブジェクトの作成を更新する
    - CVV ストレージの削除
    - 必要に応じて、カードの種類の検出ロジックを追加する

1. 少し時間を取って、コード エディターで提案されている変更を確認し、チャット ビューで **[Keep]** を選択します。

    コード エディターで GitHub Copilot の編集候補を常に確認します。 セキュリティ上の問題に対処しても機能が確実に維持されるようにします。

1. PaymentService.cs ファイルを閉じ、チャット コンテキストに **SecurityValidator.cs** ファイルを追加します。

    ValidateCreditCard メソッドは、完全なクレジット カード番号をログします。

1. GitHub Copilot に、ValidateCreditCard メソッドを修正するように依頼します。

    たとえば、エージェントに次のタスクを割り当てることができます。

    ```text
    Fix the credit card validation logging in the ValidateCreditCard method in SecurityValidator.cs. Remove or mask the full credit card number in log statements, showing only the last 4 digits if logging is necessary. Ensure the validation logic continues to work correctly. Update the DisplayKnownVulnerabilities method to reflect that credit card data storage is now secure.
    ```

1. エージェントの進行状況を監視します。

    エージェントによって、ログが、検証機能を維持しながら機密データをマスキングするように更新されます。

1. 少し時間を取って、コード エディターで提案されている変更を確認し、チャット ビューで **[Keep]** を選択します。

    コード エディターで GitHub Copilot の編集候補を常に確認します。 セキュリティ上の問題に対処しても機能が確実に維持されるようにします。

1. OrderRepository への影響を検討します。

    OrderRepository.cs ファイルには、PaymentInfo を含む Order オブジェクトが保存されます。 安全なデータ (最後の 4 桁、カードの種類) のみを保存するように PaymentInfo クラスを更新したので、リポジトリには、完全なカード番号と CVV コードではなく、安全なデータが自動的に保持されます。 リポジトリを直接変更する必要はありませんが、テスト中にこれを検証する必要があります。

1. アプリケーションをビルドして、すべての変更が正常にコンパイルされることを確認します。

    ターミナルで次のコマンドを実行します。

    ```bash
    dotnet build
    ```

    コンパイル エラーがある場合は、GitHub Copilot を使って、セキュリティ修正の過程で発生した問題を特定して解決します。 一般的な問題としては次のものが考えられます。
    - 削除されたプロパティへの参照 (CVV、完全な CardNumber)
    - コンストラクター パラメーターの不一致
    - 代入での型の不一致

### リファクタリング後のコードをテストして検証する

セキュリティ修復後の包括的なテストでは、セキュリティの改善が有効であることを確認する一方で、脆弱性の修正によって機能の回帰が発生しないことを確認します。 この検証プロセスでは、アプリケーションのセキュリティ面とビジネス機能の両方をテストする必要があります。 適切なテストで、アプリケーションが意図した動作を維持しながら、より安全になっていることを検証します。

このタスクでは、更新された ContosoShopEasy アプリケーションを体系的にテストして、セキュリティに関する 2 つの issue が解決されていることと、コア機能はそのままであることを確認します。

そのためには、以下の手順を実行してください。

1. 完成したアプリケーションを実行して、全体的な動作を確認します。

    アプリケーションを実行し、コンソールの出力を確認します。

    ```bash
    dotnet run
    ```

    出力を元のアプリケーション実行の記録と比較します。 ログの機密情報が大幅に減っているはずです。

1. SQL インジェクションの修正をテストします。

    SearchProducts メソッドが、クエリ文字列に直接連結されたユーザー入力を使用してシミュレートされた SQL クエリをログしなくなったことを確認します。 アプリケーションは次のことを行う必要があります。

    - 引き続き製品検索を正しく実行する
    - 脆弱な SQL クエリのログを表示しない
    - SQL インジェクションの脆弱性を露出することなく、検索語句を安全に処理する
    - 生の検索語句を過度にログしない

1. クレジット カードのデータ保存の修正をテストします。

    PaymentInfo クラスとこれに関連するコードが、完全なクレジット カード番号と CVV コードを保存またはログしなくなったことを確認します。 アプリケーションは次のことを行う必要があります。

    - 完全なクレジット カード番号をログしない (マスキング (例: ****1234) の確認)
    - CVV コードをまったくログしない
    - PaymentInfo オブジェクトに CVV コードを保存しない
    - カード番号の最後の 4 桁のみを保存する
    - 引き続き支払いを正しく処理する (シミュレート)

1. 全体的なセキュリティの強化を確認します。

    コンソール出力を最初の観測値と比較します。 主な改善点は次のとおりです。

    - **SQL インジェクション**:シミュレートされた SQL クエリはユーザー入力の連結を示さない
    - **クレジット カード データ**:ログまたは保存されたデータに完全なカード番号または CVV コードがない
    - アプリケーションのコア機能 (製品検索、支払い処理) は引き続き正しく機能する

1. 残っている問題や改善する領域を文書化します。

    さらに注意が必要なセキュリティ上の問題や、対処が必要な機能上の問題を記録します。

### 変更をコミットして issue を閉じる

適切なバージョン管理プラクティスにより、セキュリティの改善が適切に文書化されて追跡されます。 コミット メッセージでは、実装されたセキュリティ修正を明確に記述して、行われた変更とその理由をチーム メンバーが簡単に理解できるようにする必要があります。 わかりやすいコミット メッセージを使って GitHub の issue を閉じると、セキュリティ修復作業の明確な監査証跡が作成されます。

このタスクでは、セキュリティの改善をリポジトリにコミットし、対応する GitHub の issue を閉じます。

そのためには、以下の手順を実行してください。

1. Visual Studio Code の [ソース管理] ビューを開き、更新された各ファイルに加えられた変更を確認します。

    修復プロセス中に発生した可能性がある予期しない変更を探します。 すべての変更が修復戦略と一致し、新しい脆弱性が導入されていないことを確認します。

1. GitHub Copilot に、包括的なコミット メッセージを作成するように依頼します。

    たとえば、チャット ビューで次のプロンプトを使用できます。

    ```text
    I need to create a commit message that summarizes the security fixes I implemented for two GitHub issues: "Fix SQL Injection Vulnerability in Product Search" and "Fix Credit Card Data Storage Violations." The commit message should clearly describe the changes made to address each issue, including specific code modifications and the overall impact on application security. Draft a detailed commit message that captures all relevant information.
    ```

1. 少し時間を取って、提案されたコミット メッセージを確認します。

    行われたセキュリティの強化が正確に反映され、将来参照するのに十分な詳細が提供されていることを確認します。

    たとえば、コミット メッセージは次のサンプルのようになります。

    ```text
    Fix SQL injection and credit card data storage vulnerabilities

    Security improvements implemented:
    - Fix SQL injection in ProductService SearchProducts method
      - Remove vulnerable SQL query logging with user input
      - Implement proper input handling and sanitization
    
    - Fix PCI DSS violations for credit card data storage
      - Remove CVV property from PaymentInfo class
      - Replace CardNumber with CardLastFourDigits
      - Add CardType property for card brand identification
      - Update PaymentService to not log or store sensitive card data
      - Mask credit card numbers in SecurityValidator logs
    
    Fixes #[SQL_INJECTION_ISSUE_NUMBER] #[CREDIT_CARD_ISSUE_NUMBER]
    ```

1. `[SQL_INJECTION_ISSUE_NUMBER]` と `[CREDIT_CARD_ISSUE_NUMBER]` を GitHub リポジトリの実際の issue 番号に置き換えます。

    これらの番号は、Visual Studio Code の GitHub Pull Requests ビューで、または GitHub で issue を表示することで確認できます。

    > **注**:運用環境では、通常、各 issue は個別のテストとコード レビューを使用して個別のコミットで対処されます。 ここでは、トレーニング演習のワークフローを効率化するために、両方の修正を 1 つのコミットに組み合わせて使用します。

1. 変更をステージングしてコミットし、GitHub リポジトリに変更をプッシュ (または同期) します。

1. GitHub を開き、GitHub の issue が自動的に閉じられていることを確認します。

    GitHub 上のリポジトリに移動し、コミット メッセージで参照した 2 つの issue が終了としてマークされていることを確認します。 コミット メッセージに "Fixes #[issue_number]" 構文が含まれている場合、issue は GitHub によって自動的に閉じられます。

1. コミット履歴を確認して、セキュリティの修正が適切に文書化されていることを確認します。

    コミット メッセージでセキュリティの強化について明確に説明されており、将来参照するための適切な監査証跡が提供されることを確認します。

## クリーンアップ

演習が完了したので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに保持したくない変更が加えられていないか確認します。 たとえば、ResolveGitHubIssues リポジトリを削除することが必要になる可能性があります。 ラボ環境としてローカル PC を使用している場合は、この演習用に作成したリポジトリのローカル クローンをアーカイブするか、削除することができます。
