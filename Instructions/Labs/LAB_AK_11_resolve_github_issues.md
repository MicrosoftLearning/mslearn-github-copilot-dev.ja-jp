<!-- ---
lab:
    title: 'Exercise - Resolve GitHub issues using GitHub Copilot'
    description: 'Learn how to identify and address performance bottlenecks and code inefficiencies using GitHub Copilot tools.'
--- -->

# GitHub Copilot を使用して GitHub の issue を解決する

GitHub の issue は、プロジェクトのバグ、機能強化、タスクを追跡するための強力な手段です。 この演習では、GitHub Copilot を使ってサンプル コードベースの issue を分析して解決する方法を学びます。

この演習では、ContosoShopEasy という名前のサンプルの eコマース アプリケーションの作業を行います。 このアプリケーションには、GitHub の issue としてログされたセキュリティの脆弱性がいくつか含まれます。 目標は、GitHub Copilot を使って、これらの issue の分析と解決を支援することです。

この演習の所要時間は約**40** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です: Git 2.48 以降、.NET SDK 9.0 以降、GitHub CLI、C# 開発キット拡張機能を備えた Visual Studio Code、GitHub Copilot が有効な GitHub アカウントへのアクセス。

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

    git config --global user.name "John Doe"

    ```

    ```bash

    git config --global user.email johndoe@example.com

    ```

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 クライアントは、GitHub プロジェクトに対してログされた issue の解決に関する支援を必要としています。 あなたの目標は、Visual Studio Code で GitHub Copilot を使って、コード プロジェクトを更新するときのガイダンスとして issue を使うことです。 すべての issue に確実に対処してそれを解決する必要があります。 あなたには次のアプリが割り当てられています。

- ContosoShopEasy:GitHub の issue としてログされた複数のセキュリティ脆弱性を含む現実的な eコマース アプリケーション。 このアプリケーションでは、機能する eコマース ワークフローを維持しながら、実際のアプリケーションで見られる一般的なセキュリティ上の問題が示されています。

この演習には、次のタスクが含まれています。

1. ContosoShopEasy リポジトリをインポートします。
1. GitHub で issue を確認します。
1. リポジトリをクローンしてコードベースを確認します。
1. GitHub Copilot の質問モードを使って issue を分析します。
1. GitHub Copilot のエージェント モードを使って issue を解決します。
1. リファクタリング後のコードをテストして検証します。
1. 変更をコミットして issue を閉じます。

### ContosoShopEasy リポジトリをインポートする

GitHub Importer を使用すると、独自の GitHub アカウントに既存のリポジトリのコピーを作成し、インポートされたコピーを完全に制御できます。 GitHub Importer は、issue、PR、ディスカッションを移行しませんが、リポジトリには、コードベースに基づいて issue の作成を自動化する GitHub Actions ワークフローが含まれています。

このタスクでは、ContosoShopEasy プロジェクトをインポートし、コードベースに存在するセキュリティの脆弱性を反映した GitHub の issue を作成します。

そのためには、以下の手順を実行してください。

1. ブラウザー ウィンドウを開いて GitHub.com に移動します。

1. GitHub アカウントにサインインします。

1. [リポジトリ] タブを開きます。

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

    > **注**:インポート プロセスが完了するまでに少し時間がかかる場合があります。

1. インポート プロセスが完了するまで待ってから、新しいリポジトリを開きます。

1. リポジトリの [アクション] タブに移動し、**[ContosoShopEasy トレーニングの issue の作成]** という名前の GitHub Actions ワークフローを実行します。

1. 「作成」と入力して、issue の作成を確認します。

    このワークフローは、コードベースで識別されたセキュリティの脆弱性ごとに、リポジトリに issue を作成します。

    重大な優先度の issue

    1. **ハードコーディングされた管理者資格情報**  
        ハードコーディングされた管理者のユーザー名とパスワードを削除します。

    1. **クレジット カードのデータ保存**  
        PCI DSS コンプライアンス違反を修正します。

    高優先度の issue

    1. **SQL インジェクションの脆弱性**  
        製品検索機能をセキュリティで保護します。

    1. **脆弱なパスワード ハッシュ**  
        MD5 を、セキュリティで保護されたハッシュに置き換えます。

    1. **ログ内の機密データ**  
        デバッグ出力からパスワードとカードを削除します。

    1. **入力検証のバイパス**  
        脅威を検出するが許可する検証を修正します。

    中優先度の issue

    1. **予測可能なセッション トークン**  
        暗号化で保護されたトークンを実装します。

    1. **弱い電子メール検証**  
        電子メール形式の検証を改善します。

    1. **不十分なパスワード要件**  
        パスワードの複雑さのルールを強化します。

    低優先度の issue

    1. **情報漏えい**  
         冗長なエラー メッセージを減らし、出力をデバッグします。

### GitHub で issue を確認する

GitHub の issue は、バグ、セキュリティの脆弱性、機能強化要求の一元的な追跡システムとして機能します。 各 issue では、問題に関するコンテキスト、重大度、アプリケーションへの潜在的な影響が提供されます。 コードに進む前にこれらの issue を理解することは、優先順位を決めるのに役立ち、包括的な修復が保証されます。

このタスクでは、ContosoShopEasy プロジェクトの未解決の issue を確認し、対処する必要があるセキュリティの脆弱性を理解します。

そのためには、以下の手順を実行してください。

1. GitHub の ResolveGitHubIssues リポジトリに移動します。

1. **[Issues]** タブを選んで、未解決のすべての issue を表示します。

1. 各 issue の説明を確認し、ワークフローによって、セキュリティに関する 10 件の特定の issue が作成され、優先度別に整理されていることに注意してください。

    **重大な優先度の issue**:これらは最も重大なセキュリティ リスクを表しており、システム全体の侵害や規制違反につながる危険性があります。

    **高優先度の issue**:これらは重大なセキュリティの脆弱性であり、不正アクセスやデータ侵害を可能にする危険性があります。

    **中優先度の issue**:これらは、悪用される危険性はあるが、すぐには影響しないセキュリティ上の弱点を表しています。

    **低優先度の issue**:これらは、情報漏えいを軽減し、全体的なセキュリティ体制を向上するセキュリティ強化です。

1. 各 issue には、脆弱性の詳細な説明、特定のコードの場所、脆弱なコードの例、セキュリティ リスク、修正の受け入れ基準が含まれていることに注意してください。

1. **[ContosoShopEasy セキュリティ トレーニング - issue の概要]** という issue を確認します。これには、すべての脆弱性および学習目的の概要が記載されています。

1. これらの issue は実際のアプリケーションで見られる一般的なセキュリティの脆弱性を表しており、OWASP セキュリティ ガイドラインと一致することに注意してください。

### リポジトリをクローンしてコードベースを確認する

セキュリティの修正を実装する前に、既存のコードベースの構造と機能を理解することが不可欠です。 ContosoShopEasy アプリケーションはエンタープライズ アプリケーションに典型的な階層構造のアーキテクチャに従っており、モデル、サービス、データ アクセス、セキュリティ コンポーネントが明確に分離されています。 コードの構造をレビューしてアプリケーションを実行すると、セキュリティ改善を実装した後のテストのためのベースラインを確立するのに役立ちます。

このタスクでは、ContosoShopEasy リポジトリをクローンし、プロジェクトの構造を調べて、アプリケーションの現在の動作を確認します。

そのためには、以下の手順を実行してください。

1. ResolveGitHubIssues リポジトリをローカルの開発環境にクローンします。

    ターミナル ウィンドウを開いて、次のコマンドを実行します。`your-username` はご自分の GitHub ユーザー名に置き換えます。

    ```bash
    git clone https://github.com/your-username/ResolveGitHubIssues.git
    ```

1. クローンしたリポジトリを Visual Studio Code で開きます。

    リポジトリ フォルダーに移動し、それを Visual Studio Code で開きます。 GitHub Copilot と GitHub Copilot Chat 拡張機能がインストールされ、有効になっていることを確認します。

1. ソリューション エクスプローラーでプロジェクトの構造を確認します。

    ContosoShopEasy アプリケーションは、次のコンポーネントを含む階層構造のアーキテクチャに従いっています。

    - **Models/**:`Product.cs`、`User.cs`、`Order.cs`、`Category.cs` のデータ モデルが含まれます
    - **Services/**:`ProductService.cs`、`UserService.cs`、`PaymentService.cs`、`OrderService.cs` にビジネス ロジックが含まれます
    - **Data/**:`ProductRepository.cs`、`UserRepository.cs`、`OrderRepository.cs` にデータ リポジトリが含まれます
    - **Security/**:`SecurityValidator.cs` にセキュリティ検証ロジックが含まれます
    - **Program.cs**依存関係インジェクションのセットアップが含まれるメイン アプリケーション エントリ ポイント
    - **README.md**: アプリケーションの目的と脆弱性が説明されているドキュメント

1. アプリケーションをビルドして実行し、現在の動作を確認します。

    ターミナルで次のコマンドを実行します。

    ```bash
    cd ContosoShopEasy
    dotnet build
    dotnet run
    ```

    アプリケーションでは、意図的な脆弱性を明らかにするセキュリティ監査を含む、eコマース機能の包括的なデモンストレーションが表示されます。

1. コンソール出力を確認して、セキュリティ関連のログを特定します。

    アプリケーションにより、パスワード、クレジット カード番号、管理者資格情報、内部システムの詳細などの機密情報がログされることに注意してください。 この出力は、対処する必要があるセキュリティの問題の明確な証拠を提供します。

1. 少し時間をかけて、GitHub の issue で定義されている各セキュリティの脆弱性に関連付けられているコードをスキャンします。

    - **SQL インジェクション** (#1):`ProductService.cs` -`SearchProducts` メソッド (35 行目付近)
    - **弱いパスワード ハッシュ** (#2):`UserService.cs` -`GetMd5Hash` メソッド (55 行目付近)
    - **ログ内の機密データ** (#3):複数のファイル -`UserService.cs`、`PaymentService.cs` 登録/ログイン/支払い方法
    - **ハードコーディングされた管理者資格情報** (#4):`SecurityValidator.cs` - 管理者資格情報の定数 (7 から 9 行目)
    - **クレジット カードのデータ保存** (#5):`Models/Order.cs` - CardNumber プロパティと CVV プロパティ
    - **入力検証のバイパス** (#6): 常に true を返す`SecurityValidator.cs` -`ValidateInput` メソッド
    - **予測可能なセッション トークン** (#7):`SecurityValidator.cs` -`GenerateSessionToken` メソッド
    - **弱い電子メール検証** (#8):`SecurityValidator.cs` -`ValidateEmail` メソッド
    - **不十分なパスワード要件** (#9):`SecurityValidator.cs` -`ValidatePasswordStrength` メソッド
    - **情報漏えい** (#10):冗長なデバッグ ログとセキュリティ監査メソッドを使用した複数のクラス

### GitHub Copilot の質問モードを使って issue を分析する

GitHub Copilot の質問モードが備えるインテリジェントなコード分析機能は、セキュリティの脆弱性を明らかにし、潜在的な影響を理解し、修復戦略を提案するのに役立ちます。 各セキュリティの問題を体系的に分析すると、修正を実装する前に、問題の包括的な理解を深めることができます。 このアプローチにより、解決策では単なる症状ではなく根本原因に対処できます。

このタスクでは、GitHub Copilot の Ask モードを使用して、最も重大な issue から優先度の高い順にセキュリティの脆弱性を体系的に分析します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat ビューを開き、質問モードが選ばれていることを確認します。

    チャット ビューをまだ開いていない場合は、Visual Studio Code ウィンドウの上部にある **[チャット]** アイコンを選びます。 チャット モードが **[Ask]** に設定されており、複雑なセキュリティ分析用の**GPT-4.1** モデルを使っていることを確認します。

1. SQL インジェクションの脆弱性の分析から始めます。

    `ProductService.cs` ファイルを開いて`SearchProducts` メソッドを見つけます。 メソッド全体を選び、ドラッグ アンド ドロップを使うか、右クリックして **[Add to Chat]** 選んで、チャット コンテキストに追加します。

1. SQL インジェクションの脆弱性を分析するよう GitHub Copilot に指示します。

    セキュリティの問題を分析するには、次のプロンプトを送信します。

    ```text
    Analyze the SearchProducts method for security vulnerabilities. What makes this code susceptible to SQL injection attacks, and what are the potential consequences if an attacker exploits this vulnerability?
    ```

1. GitHub Copilot の分析を確認して、具体的な修復ガイダンスを求めます。

    最初の分析を確認した後、具体的な修正を求めます。

    ```text
    How can I modify this method to prevent SQL injection attacks? What secure coding practices should I implement to safely handle user input in database queries?
    ```

1. 脆弱なパスワード ハッシュの脆弱性を分析します。

    `UserService.cs` ファイルを開いて`GetMd5Hash` メソッドを見つけます。 このメソッドをチャット コンテキストに追加し、次のプロンプトを送信します。

    ```text
    Why is MD5 hashing unsuitable for password storage? What are the security risks of using MD5 for passwords, and what stronger alternatives should I use instead?
    ```

1. 安全なパスワード ハッシュの実装に関する具体的なガイダンスを求めます。

    ```text
    Show me how to implement secure password hashing using bcrypt or PBKDF2. What additional security measures should I implement for password handling?
    ```

1. 機密データ ログに関する問題を分析します (Issue #3)。

    `PaymentService.cs` ファイルと`UserService.cs` ファイルを開き、機密情報をログするメソッドを見つけます。 関連するメソッドをチャット コンテキストに追加し、次の質問を行います。

    ```text
    What sensitive information is being logged in the payment processing and user registration methods? Why is logging passwords, credit card numbers, and CVV codes a security risk?
    ```

1. ハードコーディングされた資格情報の脆弱性を調べます (Issue #4)。

    `SecurityValidator.cs` ファイルを開き、7 から 9 行目付近にある管理者資格情報の定数を見つけます。 関連するコードをチャット コンテキストに追加して、次のように指示します。

    ```text
    What security risks are created by hardcoding admin credentials in source code? How should application credentials be managed securely in production environments?
    ```

1. クレジット カードのデータ保存の問題を分析します (Issue #5)。

    `Models/Order.cs` ファイルを開き、CardNumber プロパティと CVV プロパティを調べます。 このコードをチャット コンテキストに追加し、次の質問を行います。

    ```text
    Why is storing full credit card numbers and CVV codes a PCI DSS compliance violation? What are the proper ways to handle payment card data securely?
    ```

1. 入力検証のバイパスを確認します (Issue #6)。

    `SecurityValidator.cs` 内にある、脅威を検出したにもかかわらず常に true を返す`ValidateInput` メソッドに焦点を当てます。 確認:

    ```text
    What makes this input validation method ineffective? Why does it detect dangerous input but still return true, and how should proper input validation work?
    ```

1. 予測可能なセッション トークンの生成を調べます (Issue #7)。

    `SecurityValidator.cs` の`GenerateSessionToken` メソッドに焦点を当てて、次のように指示します。

    ```text
    Why are predictable session tokens based on username and timestamp a security risk? How should secure, unpredictable session tokens be generated?
    ```

1. 弱い電子メール検証を分析します (Issue #8)。

    `SecurityValidator.cs` 内にある、文字 "@" and "" のみをチェックする`ValidateEmail` メソッドを確認します。 確認:

    ```text
    What makes this email validation insufficient? What are the security risks of weak email validation, and how should proper email validation be implemented?
    ```

1. 不十分なパスワード要件を確認します (Issue #9)。

    `SecurityValidator.cs` 内にある、4 文字しか要求しない`ValidatePasswordStrength` メソッドを調べます。 確認:

    ```text
    Why are these password requirements insufficient for security? What are proper password complexity requirements, and how should password strength be validated?
    ```

1. 情報漏えいの問題を分析します (Issue #10)。

    さまざまなファイル全体で`RunSecurityAudit` メソッドとその他のデバッグ ログを選択し、次の質問をします。

    ```text
    How does the security audit method and excessive debug logging create information disclosure vulnerabilities? What information should never be exposed in logs or error messages?
    ```

1. 修復フェーズの間に参照できるよう、分析の結果を文書化します。

    各脆弱性カテゴリに関する GitHub Copilot の推奨事項を記録します。 このドキュメントは、次のタスクでセキュリティの修正を実装する際のガイドになります。

### GitHub Copilot のエージェント モードを使って issue を解決する

GitHub Copilot のエージェント モードを使うと、複数のファイルとメソッドで複雑なセキュリティ修正を自律的に実装できます。 分析と推奨事項を提供する質問モードとは異なり、エージェント モードでは、コードを直接変更してセキュリティの改善を実装できます。 このアプローチは、複数の関連する脆弱性に一貫して対処する必要がある体系的なセキュリティ修復に特に有効です。

> **注**:このラボ演習では時間を節約するために、issue のコレクションを解決してテストし、1 回のコミットで更新をプッシュします。 このように issue をバッチ処理することは、推奨されるベスト プラクティスではありません。 Microsoft と GitHub では、バッチ処理ではなく、個別のコミットで各 issue を個別に解決することをお勧めします。 issue を個別に解決すると、追跡可能性が向上し、コード レビューが容易になり、問題が発生した場合にロールバック オプションの安全性が高くなります。 次の issue に進む前に、各修正を徹底的にテストして、変更によって回帰が発生しないようにする必要があります。

このタスクでは、GitHub Copilot のエージェント モードを使って、ContosoShopEasy アプリケーションで特定されたすべての脆弱性に対する包括的なセキュリティ修正を実装します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat をエージェント モードに切り替えます。

    チャット ビューでモード セレクターを見つけて、**[Ask]** から **[Agent]** に変更します。 エージェント モードの GitHub Copilot は、ユーザーの指示に基づいてコードを直接変更できます。

1. 最初に SQL インジェクションの脆弱性に対処します。

    `ProductService.cs` ファイルを開いて`SearchProducts` メソッドを見つけます。 次のプロンプトを使ってエージェントに指示します。

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
