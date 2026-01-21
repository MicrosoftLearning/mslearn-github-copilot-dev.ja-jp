---
lab:
  title: 準備 - GitHub Spec Kit のラボ環境を構成する
  description: ラボの要件を確認し、GitHub Spec Kit の演習向けにリソースを構成します。
---

# GitHub Spec Kit のラボ環境を構成する

GitHub Dev Kit ラボの演習で仕様駆動型の開発を開始する前に、開発環境に必要なツールとリソースが含まれていることを確認する必要があります。

ラボ環境には次のリソースが必要です。

- Git バージョン 2.48 以降。
- .NET SDK バージョン 8.0 以降。
- GitHub Copilot が有効になっている GitHub アカウントへのアクセス。
- C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code。
- SQL Server LocalDB または SQLite。
- Python バージョン 3.11 以降。
- uv パッケージ マネージャー。

## GitHub、.NET、Visual Studio Code のリソースをインストールする

ラボ演習「GitHub 開発キットを使用した仕様駆動型の開発」では、Visual Studio Code でプライマリ AI アシスタントとして GitHub Copilot を使用します。 GitHub Copilot を使用するには、GitHub Copilot サブスクリプションを持つ GitHub アカウントにアクセスする必要があります。 GitHub では、バージョン管理操作に Git が必要です。 ラボで使用するアプリケーションは、.NET (ASP.NET Core 8.0 と Blazor) を使用してビルドされています。

次の手順を実行して、必要な GitHub、.NET、Visual Studio Code のツールとリソースを確実に使用できるようにします。

1. ラボ環境に Git バージョン 2.48 以降がインストールされていることを確認します。

    ターミナル ウィンドウで次のコマンドを実行し、インストールされている Git のバージョンを確認します。

    ```bash
    git --version
    ```

    Windows を実行していて、Git を更新する場合は、次のコマンドを使用できます。

    ```bash
    git update-git-for-windows
    ```

    必要に応じて、次の URL を使用して Git をダウンロードできます。<a href="https://git-scm.com/downloads" target="_blank">Git のダウンロード</a>。

1. Git が自分の名前と電子メール アドレスを使用するように構成されていることを確認します。

    必要に応じて、次のコマンドを使用して Git のユーザー名とメール アドレスを設定できます。

    > **注**:次のコマンドを実行する前に、コマンドを自分の情報で更新します。

    ```bash
    git config --global user.name "Julie Miller"
    ```

    ```bash
    git config --global user.email julie.miller@example.com
    ```

1. ラボ環境に .NET 8.0 SDK 以降のバージョンがインストールされていることを確認します。

    .NET SDK の最新の LTS または STS バージョンをインストールすることをお勧めしますが、.NET 8.0 を使用してこの演習を完了することもできます。

    ターミナル ウィンドウで次のコマンドを実行し、インストールされている .NET SDK のバージョンを確認します。

    ```dotnetcli
    dotnet --version
    ```

    必要に応じて、次の URL を使用して .NET SDK をダウンロードできます。<a href="https://dotnet.microsoft.com/download/dotnet" target="_blank">.NET SDK のダウンロード</a>。

1. パッケージのダウンロードおよび復元用のソースとして公式の NuGet.org リポジトリを使用するように .NET SDK が構成されていることを確認します。

    たとえば、ターミナル ウィンドウを開き、次のコマンドを実行します。

    ```bash
    dotnet nuget add source https://api.nuget.org/v3/index.json -n nuget.org
    ```

1. Visual Studio Code と C# 開発キット拡張機能がラボ環境にインストールされていることを確認します。

    必要に応じて、次の URL を使用して Visual Studio Code をダウンロードできます。<a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code をダウンロードする</a>

    C# 開発キット拡張機能は、Visual Studio Code の [拡張機能] ビューを使用してインストールできます。

1. GitHub アカウントと GitHub Copilot サブスクリプションにアクセスできることを確認します。

    次の URL を使用して GitHub アカウントにログインできます。<a href="https://github.com/login" target="_blank">GitHub ログイン</a>。

    GitHub アカウントをお持ちでない場合は、GitHub ログイン ページで個別のアカウントを作成できます。 ログイン ページで、**[アカウントの作成]** を選択します。

    GitHub アカウントの設定/プロファイル ページを開き、GitHub Copilot サブスクリプションへのアクセス権が得られたことを確認します。 トレーニングに使用できる GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business または GitHub Copilot Enterprise のアクティブなサブスクリプションをお持ちの場合は、GitHub Copilot 演習を完了するために既存の GitHub Copilot サブスクリプションを使用できます。

    個別の GitHub アカウントを持っているが、GitHub Copilot サブスクリプションがない場合は、トレーニング演習中に Visual Studio Code で GitHub Copilot Free プランを設定できます。

    > **重要**:GitHub Copilot Free プラン は、GitHub Copilot の制限付きバージョンであり、1 か月あたり最大 2,000 個のコード補完と 50 個のチャットまたは Premium リクエストが可能です。 トレーニング演習以外で GitHub Copilot Free プランを使用すると、トレーニングを完了する前にプランのリソース制限を超える可能性があります。 GitHub Copilot Free プランは、GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business または GitHub Copilot Enterprise サブスクリプションでは使用できません。

1. Visual Studio Code 環境で GitHub Copilot Chat にアクセスできることを確認します。

    GitHub Copilot Chat 拡張機能は、Visual Studio Code の [拡張機能] ビューを使用してインストールできます。

## アプリケーションの依存関係をインストールする

ラボで作業しているアプリケーションは、SQL Server LocalDB データベースまたは SQLite データベースのいずれかを使用してアプリケーション データを格納します。 SQL Server LocalDB は、開発とテストに最適な軽量バージョンの SQL Server です。 SQLite は、簡単に設定して使用できる自己完結型のサーバーレス データベース エンジンです。

次の手順を実行して、SQL Server LocalDB をラボ環境に確実にインストールします。

1. SQL Server LocalDB がラボ環境にインストールされているかどうかを確認します。

    ターミナル ウィンドウで次のコマンドを実行し、LocalDB がインストールされていることを確認します。

    ```powershell
    sqllocaldb info
    ```

    予想される出力:LocalDB インスタンスの一覧。存在しない場合は空のリスト。 次に例を示します。

    ```output
    MSSQLLocalDB
    ```

    コマンドが失敗した場合、または LocalDB がインストールされていない場合は、次の手順に従って SQL Server 2019 LocalDB をインストールします。 それ以外の場合は、「GitHub Spec Kit のツールとリソースをインストールする」セクションに進んでください。

1. SQL Server 2019 Express Edition のインストーラー ファイルをダウンロードするには、ブラウザーで次のリンクを開いてください。<a href="https://go.microsoft.com/fwlink/?LinkID=866658" target="_blank">SQL Server 2019 Express Edition のダウンロード</a>

1. ダウンロードが完了したら、SQL Server 2019 インストーラー ファイル (**SQL2019-SSEI-Expr.exe** など) を開きます。

1. SQL Server 2019 インストール ウィザードで、**[メディアのダウンロード]** を選択します。

1. **[SQL Server インストーラーのダウンロードを指定する]** で、**[LocalDB]** パッケージを選択し、**[ダウンロード]** ボタンを選択します。

1. **ダウンロードに成功しました**というメッセージが表示されたら、**[フォルダーを開く]** ボタンを選択します。

1. SQL Server LocalDB インストーラー ファイル (**SqlLocalDB.msi** など) を実行し、プロンプトに従ってインストールを完了します。

1. インストールを確認するには、PowerShell またはコマンド プロンプトを開き、次のコマンドを実行します。

    ```powershell
    sqllocaldb info
    ```

    LocalDB インスタンスの一覧 (まだ存在しない場合は空のリスト) が表示されるはずです。 次に例を示します。

    ```output
    MSSQLLocalDB
    ```

    MSSQLLocalDB の既定のインスタンスを作成する必要がある場合は、次のコマンドを実行します。

    ```powershell
    sqllocaldb create MSSQLLocalDB
    sqllocaldb start MSSQLLocalDB
    ```

1. SQLite をダウンロードするには、次の URL の手順に従います: <a href="https://www.sqlite.org/download.html" target="_blank">SQLite のダウンロード</a>。

## GitHub Spec Kit のツールとリソースをインストールする

GitHub Spec Kit のコマンド ライン インターフェイス (CLI) ツールは Python ベースであり、Python 3.11 以降が必要です。 uv パッケージ マネージャーは、GitHub Spec Kit CLI ツールのインストールと管理に使用されます。

次の手順を実行して、ラボ環境に GitHub Spec Kit のツールとリソースをインストールして構成します。

1. ラボ環境に Python 3.11 以降がインストールされていることを確認します。

    GitHub Spec Kit の CLI ツールは Python ベースであり、Python 3.11 以降が必要です。

    インストールされている Python のバージョンを確認するには、次のコマンドを実行します。

    ```powershell
    python --version
    ```

    必要な出力:**Python 3.11.0** 以降。

    Python をインストールする必要がある場合は、次の URL からインストーラーをダウンロードできます: <a href="https://www.python.org/downloads/" target="_blank">python.org</a>。

    企業環境では、組織のソフトウェア配布システムを使用することもできます。

1. ラボ環境に uv パッケージがインストールされていることを確認します。

    ```powershell
    uv --version
    ```

    次のサンプルのように出力が表示されます。

    ```output
    uv 0.9.17 (2b5d65e61 2025-12-09)
    ```

    Windows PowerShell を使用して uv をインストールするには、次のコマンドを実行します。

    ```powershell
    powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
    ```

    GitHub Spec Kit は、CLI のインストールと管理に uv を使用します。

    詳細なインストール手順については、次の URL を参照してください: <a href="https://docs.astral.sh/uv/" target="_blank">docs.astral.sh/uv</a>。

1. uv が環境の PATH 内にあることを確認するには、ターミナル ウィンドウを再起動してから、次のコマンドを実行します。

    ```powershell
    cd C:\
    uv --version
    ```

    次のサンプルのように出力が表示されます。

    ```output
    uv 0.9.17 (2b5d65e61 2025-12-09)
    ```

1. ターミナル ウィンドウを開きます。

    コマンド プロンプト、PowerShell、またはターミナル ウィンドウを使用できます。

1. GitHub Spec Kit の Specify CLI ツールをインストールするには、次の PowerShell コマンドを実行します。

    ```powershell
    uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
    ```

    このコマンドで、GitHub リポジトリから最新バージョンが直接インストールされ、*specify* コマンドがシステム全体で使用できるようになります。

    specify コマンドライン ツールは、仕様駆動型開発用のプロジェクトを初期化するために使用されます。

1. *specify* コマンドが環境の PATH 内にあることを確認するには、ターミナル ウィンドウを再起動してから、次のコマンドを実行します。

    ```powershell
    specify version
    ```

    しばらくすると、次の例のような出力が表示されます。

    ```output
         CLI Version    0.0.22
    Template Version    0.0.90
            Released    2025-12-04
              Python    3.14.0
            Platform    Windows
        Architecture    AMD64
          OS Version    10.0.26200
    ```

    インストールに関する問題のトラブルシューティング:

    - コマンドが見つからない:インストール後に *specify* コマンドが認識されない場合は、*uv* tools ディレクトリが PATH にない可能性があります。 インストールを確認するには、*uv tool list* コマンドを実行します。 ターミナルを再起動するか、ツール ディレクトリを PATH に手動で追加することが必要になる場合があります。

    - SSL インターセプトを使用する企業環境では、証明書の構成が必要になる場合があります。 サポートが必要な場合は社内の IT 部門にお問い合わせください。

これで、GitHub Spec Kit の開発環境が構成され、準備が整いました。
