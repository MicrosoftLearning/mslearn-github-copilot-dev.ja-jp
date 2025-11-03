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

GitHub リポジトリのインポートを使うと、既存のリポジトリのコピーを独自の GitHub アカウントに作成できます。 このプロセスでは、インポートされるコピーをユーザーが完全に制御できる一方で、元のリポジトリの履歴が保持されます。 このタスクでは、ContosoShopEasy プロジェクトをインポートし、コードベースに存在するセキュリティの脆弱性を反映した GitHub の issue を作成します。

そのためには、以下の手順を実行してください。

1. ブラウザー ウィンドウを開いて GitHub.com に移動します。

1. GitHub アカウントにサインインします。

1. **ContosoShopEasy** という名前の新しいリポジトリを作成します。

    新しいリポジトリを作成するには、GitHub の右上隅にある**+** アイコンを選んで、**[New repository]** を選びます。

1. ContosoShopEasy プロジェクト ファイルをローカル ラボ環境から新しいリポジトリにコピーします。

    GitHub の Web インターフェイスから直接ファイルをアップロードするのでも、空のリポジトリをクローンしてローカル コンピューターから ContosoShopEasy ファイルをプッシュするのでもかまいません。

1. 次の GitHub issue を作成して、セキュリティの脆弱性を追跡します。

    - **SQL インジェクションの脆弱性**:製品検索機能は認可されていない入力を受け入れ、インジェクション攻撃に対して脆弱です。 `ProductService.cs` の`SearchProducts` メソッドは、適切なパラメーター化を行わず、ユーザーによる入力をシミュレートされた SQL クエリに直接組み込みます。

    - **脆弱なパスワード ハッシュ**:アプリケーションでパスワードの格納に使われている MD5 ハッシュは、暗号としては脆弱なものです。 `UserService.cs` の`GetMd5Hash` メソッドには、この脆弱なハッシュ アプローチが実装されています。

    - **機密データの露出**:完全なクレジット カード番号、CVV コード、パスワードがプレーンテキストで記録されます。 `PaymentService.cs` の`ProcessPayment` メソッドと、`UserService.cs` の登録とログインのメソッドは、機密情報を公開します。

    - **ハードコーディングされた管理者の資格情報**:管理者のユーザー名とパスワードが`SecurityValidator.cs` クラスにハードコーディングされており、ソース コード アクセス権を持つすべてのユーザーがアクセスできます。

    - **入力検証の問題**:アプリケーションは、適切なサニタイズを行わずに潜在的に危険な文字を受け入れます。 `SecurityValidator.cs` の`ValidateInput` メソッドは警告をログしますが、それでも悪意のある入力を受け入れます。

    - **情報漏えい**:デバッグ ログでは、機密性の高いシステム情報と構成の詳細が公開されます。 アプリケーション全体の複数のクラスで、デバッグのために機密データがログされます。

    - **予測可能なセッション トークン**:セッション トークンは、ユーザー名とタイムスタンプに基づく予測可能なパターンに従っています。 `SecurityValidator.cs` の`GenerateSessionToken` メソッドは、簡単に推測できるトークンを作成します。

    - **ファイルのアップロードに関するセキュリティの問題**:アプリケーションは、危険なファイルの種類を適切な検証なしで受け入れます。 `SecurityValidator.cs` の`ValidateFileUpload` メソッドが提供する保護は、悪意のあるアップロードに対して十分ではありません。

### GitHub で issue を確認する

GitHub の issue は、バグ、セキュリティの脆弱性、機能強化要求の一元的な追跡システムとして機能します。 各 issue では、問題に関するコンテキスト、重大度、アプリケーションへの潜在的な影響が提供されます。 コードに進む前にこれらの issue を理解することは、優先順位を決めるのに役立ち、包括的な修復が保証されます。

このタスクでは、ContosoShopEasy プロジェクトの未解決の issue を確認し、対処する必要があるセキュリティの脆弱性を理解します。

そのためには、以下の手順を実行してください。

1. GitHub で ContosoShopEasy リポジトリに移動します。

1. **[Issues]** タブを選んで、未解決のすべての issue を表示します。

1. 各 issue の説明を確認し、次のセキュリティ脆弱性カテゴリに注意します。

    **SQL インジェクション脆弱性**: ユーザーによる入力をデータベース クエリに直接組み込む製品検索機能により、悪意のある SQL インジェクション攻撃の機会が生まれます。

    **脆弱な暗号化プラクティス**: パスワード ストレージに対する MD5 ハッシュの使用。これは暗号的に脆弱と見なされ、パスワードのセキュリティに適しません。

    **機密データの露出**:クレジット カードの完全な番号、CVV コード、パスワード、およびプレーンテキストで保存またはログしてはいけない他の機密情報のログ。

    **ハードコーディングされた資格情報**: 管理者のユーザー名とパスワードがソース コードに直接埋め込まれており、リポジトリのアクセス権を持つユーザーであれば誰でもアクセスできます。

    **入力検証エラー**: ユーザーによる入力の検証とサニタイズが不十分であり、悪意のある可能性のあるコンテンツをアプリケーションで処理できます。

    **情報漏えい**:機密性の高いシステム構成、内部プロセス、ユーザー データを公開するデバッグ ログ。

    **脆弱なセッション管理**: 攻撃者がユーザー セッションをハイジャックできる可能性がある、予測可能なセッション トークンの生成。

    **ファイルアップロードの脆弱性**: 悪意のあるコードの実行が許可される可能性がある、アップロードされたファイルの不十分な検証。

1. これらの issue は実際のアプリケーションで見られる一般的なセキュリティの脆弱性を表しており、OWASP セキュリティ ガイドラインと一致することに注意してください。

### リポジトリをクローンしてコードベースを確認する

セキュリティの修正を実装する前に、既存のコードベースの構造と機能を理解することが不可欠です。 ContosoShopEasy アプリケーションはエンタープライズ アプリケーションに典型的な階層構造のアーキテクチャに従っており、モデル、サービス、データ アクセス、セキュリティ コンポーネントが明確に分離されています。 コードの構造をレビューしてアプリケーションを実行すると、セキュリティ改善を実装した後のテストのためのベースラインを確立するのに役立ちます。

このタスクでは、ContosoShopEasy リポジトリをクローンし、プロジェクトの構造を調べて、アプリケーションの現在の動作を確認します。

そのためには、以下の手順を実行してください。

1. ContosoShopEasy リポジトリをローカル開発環境にクローンします。

    ターミナル ウィンドウを開いて、次のコマンドを実行します。`your-username` はご自分の GitHub ユーザー名に置き換えます。

    ```bash
    git clone https://github.com/your-username/ContosoShopEasy.git
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

1. 各セキュリティ脆弱性に関連付けられている特定のファイルとメソッドを確認します。

    - **SQL インジェクション**:`ProductService.cs` -`SearchProducts` メソッド
    - **脆弱なパスワード ハッシュ**:`UserService.cs` -`GetMd5Hash` メソッド
    - **機密データの露出**:`PaymentService.cs` -`ProcessPayment` メソッドと`UserService.cs` - 登録/ログイン メソッド
    - **ハードコーディングされた資格情報**:`SecurityValidator.cs` - 管理者資格情報の定数
    - **入力検証**:`SecurityValidator.cs` -`ValidateInput` メソッド
    - **情報漏えい**:デバッグ ログを含む複数のクラス
    - **予測可能なトークン**:`SecurityValidator.cs` -`GenerateSessionToken` メソッド
    - **ファイル アップロードの問題**:`SecurityValidator.cs` -`ValidateFileUpload` メソッド

### GitHub Copilot の質問モードを使って issue を分析する

GitHub Copilot の質問モードが備えるインテリジェントなコード分析機能は、セキュリティの脆弱性を明らかにし、潜在的な影響を理解し、修復戦略を提案するのに役立ちます。 各セキュリティの問題を体系的に分析すると、修正を実装する前に、問題の包括的な理解を深めることができます。 このアプローチにより、解決策では単なる症状ではなく根本原因に対処できます。

このタスクでは、GitHub Copilot の質問モードを使って、ContosoShopEasy アプリケーションの各セキュリティ脆弱性を体系的に分析します。

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

1. 機密データの露出に関する問題を分析します。

    `PaymentService.cs` ファイルを開いて`ProcessPayment` メソッドを見つけます。 それをチャット コンテキストに追加して、次のように指示します。

    ```text
    What sensitive information is being logged in this payment processing method? Why is logging full credit card numbers and CVV codes a security risk, and how should payment data be handled securely?
    ```

1. ハードコーディングされた資格情報の脆弱性を調べます。

    `SecurityValidator.cs` ファイルを開いて、管理者資格情報の定数を見つけます。 関連するコードをチャット コンテキストに追加して、次のように指示します。

    ```text
    What security risks are created by hardcoding admin credentials in source code? How should application credentials be managed securely in production environments?
    ```

1. 入力検証の脆弱さを分析します。

    `SecurityValidator.cs` の`ValidateInput` メソッドに焦点を当てて、次のように指示します。

    ```text
    What makes this input validation method ineffective against malicious input? How can I implement proper input sanitization to prevent XSS and other injection attacks?
    ```

1. 情報漏えいの問題を確認します。

    さまざまなファイルでデバッグ ログを含む複数のメソッドを選んで、次のように指示します。

    ```text
    How does excessive debug logging create security vulnerabilities? What information should never be logged, and how can I implement secure logging practices?
    ```

1. 予測可能なトークンの生成を調べます。

    `GenerateSessionToken` メソッドに焦点を当てて、次のように指示します。

    ```text
    Why are predictable session tokens a security risk? How should secure, unpredictable session tokens be generated to prevent session hijacking attacks?
    ```

1. ファイル アップロードの検証の問題を分析します。

    `ValidateFileUpload` メソッドを確認し、次のように指示します。

    ```text
    What makes this file upload validation insufficient for security? How can I implement comprehensive file upload security to prevent malicious file execution?
    ```

1. 修復フェーズの間に参照できるよう、分析の結果を文書化します。

    各脆弱性カテゴリに関する GitHub Copilot の推奨事項を記録します。 このドキュメントは、次のタスクでセキュリティの修正を実装する際のガイドになります。

### GitHub Copilot のエージェント モードを使って issue を解決する

GitHub Copilot のエージェント モードを使うと、複数のファイルとメソッドで複雑なセキュリティ修正を自律的に実装できます。 分析と推奨事項を提供する質問モードとは異なり、エージェント モードでは、コードを直接変更してセキュリティの改善を実装できます。 このアプローチは、複数の関連する脆弱性に一貫して対処する必要がある体系的なセキュリティ修復に特に有効です。

このタスクでは、GitHub Copilot のエージェント モードを使って、ContosoShopEasy アプリケーションで特定されたすべての脆弱性に対する包括的なセキュリティ修正を実装します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat をエージェント モードに切り替えます。

    チャット ビューでモード セレクターを見つけて、**[Ask]** から **[Agent]** に変更します。 エージェント モードの GitHub Copilot は、ユーザーの指示に基づいてコードを直接変更できます。

1. 最初に SQL インジェクションの脆弱性に対処します。

    `ProductService.cs` ファイルを開いて`SearchProducts` メソッドを見つけます。 次のプロンプトを使ってエージェントに指示します。

    ```text
    Fix the SQL injection vulnerability in the SearchProducts method. Remove the simulated SQL query logging that demonstrates the vulnerability, and implement proper input sanitization to safely handle search terms. Ensure the method still functions correctly for legitimate searches while preventing malicious input.
    ```

1. エージェントの進行状況を監視し、提案された変更を検討します。

    エージェントはコードを変更して脆弱なログを削除し、より安全な入力処理を実装します。 変更を確認し、それがセキュリティの問題に対処しながら機能を維持していることを確認します。

1. 安全なパスワード ハッシュを実装します。

    `UserService.cs` ファイルに注目して、次のプロンプトを使います。

    ```text
    Replace the MD5 password hashing with bcrypt or PBKDF2. Update the GetMd5Hash method to use a cryptographically secure hashing algorithm with proper salt generation. Ensure compatibility with existing user authentication while improving security.
    ```

1. パスワード ハッシュの変更をレビューしてテストします。

    エージェントは、より強力なパスワード ハッシュを実装します。 アプリケーションを実行して変更をテストし、ユーザーの登録とログインがまだ正しく機能することを確認します。

1. 支払い処理での機密データの露出に対処します。

    `PaymentService.cs` ファイルを開き、エージェントに次のように指示します。

    ```text
    Fix sensitive data logging in the ProcessPayment method. Remove logging of full credit card numbers, CVV codes, and other sensitive payment information. Implement secure logging that masks sensitive data while maintaining useful operational information.
    ```

1. ハードコーディングされた管理者の資格情報を削除します。

    `SecurityValidator.cs` ファイルに注目して、次のプロンプトを使います。

    ```text
    Remove hardcoded admin credentials and replace them with a secure configuration approach. Implement environment variable or configuration file-based credential management while maintaining the functionality for educational demonstration purposes.
    ```

1. 適切な入力検証を実装します。

    入力検証の脆弱性を修正するようエージェントに指示します。

    ```text
    Strengthen the ValidateInput method to properly sanitize user input and prevent XSS attacks. Implement comprehensive input validation that rejects malicious content while allowing legitimate user input. Ensure the method returns false for truly dangerous input.
    ```

1. ログを通じた情報漏えいを減らします。

    複数のファイルでのデバッグ ログの問題に対処します。

    ```text
    Review all console logging throughout the application and remove or mask sensitive information. Implement secure logging practices that provide useful debugging information without exposing passwords, credit card data, or system internals.
    ```

1. 安全なセッション トークンの生成を実装します。

    セッション トークンの脆弱性に焦点を当てます。

    ```text
    Replace the predictable session token generation with a cryptographically secure random token generator. Ensure tokens are unpredictable and sufficiently long to prevent brute force attacks.
    ```

1. ファイル アップロードの検証を強化します。

    ファイル アップロードのセキュリティの問題に対処します。

    ```text
    Implement comprehensive file upload validation that checks file types, sizes, and content. Reject dangerous file types and implement proper security controls to prevent malicious file uploads.
    ```

1. 大きな変更のたびにアプリケーションをテストします。

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

1. 製品検索機能を調べて、SQL インジェクションの修正をテストします。

    `SearchProducts` メソッドが脆弱な SQL クエリをログしなくなったこと、および正当な検索語句に対して検索機能が正しく機能することを確認します。

1. パスワードのセキュリティの改善を検証します。

    ユーザー登録とログイン プロセスでプレーンテキストのパスワードがログされなくなったことと、より強力なパスワード ハッシュが実装されていることを確認します。 アプリケーションによってユーザーの認証が引き続き正しく行われる必要があります。

1. 支払い処理のセキュリティ向上を確認します。

    支払い処理で、支払いを正常に処理する機能を維持しながら、クレジット カードの完全な番号または CVV コードがログされなくなったことを確認します。

1. 管理者資格情報のセキュリティを検証します。

    ハードコーディングされた管理者資格情報がログやセキュリティ監査に表示されなくなり、一方で管理機能には安全な手段で引き続きアクセスできることを検証します。

1. 入力検証の改善をテストします。

    改善された入力検証で、通常のユーザー操作を損なうことなく、正当な入力と悪意の可能性がある入力の両方が適切に処理されることを確認します。

1. 情報漏えいの修正を確認します。

    コンソール出力をレビューし、機密性の高いシステム情報、構成の詳細、ユーザー データがデバッグ ログを通して公開されなくなったことを確認します。

1. セッション トークンのセキュリティを確認します。

    アプリケーションの実行中にセッション トークンが生成される場合は、以前のパターンに基づくアプローチに従うのではなく、ランダムで予測不可能に見えることを確認します。

1. ファイル アップロードのセキュリティの改善を検証します。

    ファイル アップロードの検証の改善をテストして、危険なファイルの種類が正しく拒否される一方で、正当なファイルが受け入れられることを確認します。

1. セキュリティ監査の出力を元のバージョンと比べます。

    セキュリティ監査機能を実行し、結果を最初の観察と比べます。 監査では、教育的価値を維持しながら、セキュリティ態勢が改善されたことが示される必要があります。

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

1. GitHub の issue を参照するわかりやすいメッセージを使って変更をコミットします。

    セキュリティの修正を明確に説明し、issue 番号を参照するコミット メッセージを使います。

    ```bash
    git commit -m "Fix SQL injection vulnerability in ProductService SearchProducts method

    - Remove vulnerable SQL query logging
    - Implement proper input sanitization
    - Maintain search functionality while preventing injection attacks

    Fixes #1"
    ```

1. 主要なセキュリティ修正カテゴリごとに追加のコミットを行います。

    パスワード ハッシュの改善をコミットします。

    ```bash
    git commit -m "Replace MD5 with secure password hashing

    - Implement bcrypt/PBKDF2 for password storage
    - Add proper salt generation
    - Maintain authentication compatibility

    Fixes #2"
    ```

1. 残りのセキュリティ修正についてのコミットを続けます。

    ```bash
    git commit -m "Remove sensitive data from payment processing logs

    - Mask credit card numbers in logging
    - Remove CVV code exposure
    - Maintain operational logging capabilities

    Fixes #3"

    git commit -m "Remove hardcoded admin credentials

    - Implement secure credential management
    - Use environment variables for sensitive config
    - Maintain admin functionality for demo purposes

    Fixes #4"

    git commit -m "Strengthen input validation and prevent XSS

    - Implement comprehensive input sanitization
    - Reject malicious content properly
    - Maintain user experience for legitimate input

    Fixes #5"

    git commit -m "Reduce information disclosure in debug logging

    - Remove sensitive data from console output
    - Implement secure logging practices
    - Maintain useful debugging information

    Fixes #6"

    git commit -m "Implement secure session token generation

    - Replace predictable tokens with cryptographically secure random generation
    - Increase token entropy to prevent brute force attacks

    Fixes #7"

    git commit -m "Enhance file upload security validation

    - Implement comprehensive file type checking
    - Add file size and content validation
    - Reject dangerous file types effectively

    Fixes #8"
    ```

1. 変更を GitHub リポジトリにプッシュします。

    ```bash
    git push origin main
    ```

1. GitHub の issue が自動的に閉じられることを確認します。

    GitHub 上のリポジトリに移動し、issue がそれを参照するコミット メッセージによってクローズとしてマークされていることを確認します。

1. コミット履歴をレビューし、すべてのセキュリティ修正が適切に文書化されていることを確認します。

    コミット メッセージでセキュリティの改善が明確に説明されていて、将来の参照のための適切な監査証跡が提供されていることを確認します。

