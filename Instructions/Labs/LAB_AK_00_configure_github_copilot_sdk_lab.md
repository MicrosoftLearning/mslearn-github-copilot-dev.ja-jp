---
lab:
  title: 準備 - GitHub Copilot SDK ラボ環境を構成する
  description: ラボの要件を確認し、GitHub Copilot SDK の演習向けにリソースを構成します。
---

# GitHub Copilot SDK ラボ環境を構成する

GitHub Copilot SDK ラボの演習を開始する前に、開発環境に必要なツールとリソースが含まれていることを確認する必要があります。

ラボ環境には次のリソースが必要です。

- Git バージョン 2.48 以降。
- .NET SDK バージョン 8.0 以降。
- GitHub Copilot が有効になっている GitHub アカウントへのアクセス。
- C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code。
- GitHub Copilot CLI がインストールされ、お使いの GitHub アカウントで認証済みです。

## Git、.NET、Visual Studio Code、GitHub のリソースをインストールする

GitHub Copilot SDK ラボの演習では、Visual Studio Code の GitHub Copilot をプライマリ AI コーディング アシスタントとして使用します。 GitHub Copilot を使用するには、GitHub Copilot サブスクリプションを持つ GitHub アカウントにアクセスする必要があります。 GitHub では、バージョン管理操作に Git が必要です。

ラボ アプリケーションは、C# (ASP.NET Core 8.0 と Blazor) を使用してビルドされています。 ラボ アプリケーションのデータ アクセス レイヤーでは、Entity Framework Core と SQLite が使用されます。 ラボ アプリケーションは、ラボの演習中にラボ環境にクローンする GitHub リポジトリで使用できます。

次の手順を実行して、必要な Git、.NET、Visual Studio Code、GitHub のツールとリソースを確実に使用できるようにします。

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

    このラボのスターター アプリケーションは、.NET 8.0 を使用して開発されました。 ただし、.NET 8 がインストールされていない場合は、最新の LTS または STS バージョンの .NET SDK を使用するようにプロジェクトを更新できます。

    ターミナル ウィンドウで次のコマンドを実行し、インストールされている .NET SDK のバージョンを確認します。

    ```dotnetcli
    dotnet --list-sdks
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

GitHub Copilot SDK は、AI コードの生成とチャットの対話に GitHub Copilot CLI の基盤のエンジンを使用します。 GitHub Copilot SDK 演習用にラボ環境が正しく構成されていることを確認するには、GitHub Copilot CLI をインストールして認証する必要があります。

GitHub Copilot CLI の詳細については、次の公式ドキュメントを参照してください:<a href="https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli" target="_blank">GitHub Copilot CLI について</a>。

次の手順を実行して、ラボ環境に GitHub Copilot CLI がインストールされ、構成されていることを確認します。

1. GitHub Copilot CLI の公式インストール手順を開くには、次のリンクを使用します:

    <a href="https://docs.github.com/en/copilot/how-tos/copilot-cli/install-copilot-cli" target="_blank">GitHub Copilot CLI のインストール</a>

    Windows、macOS、Linux 用の GitHub Copilot CLI が提供されています。 指示に従って、お使いのオペレーティング システム用の GitHub Copilot CLI をインストールします。

1. GitHub Copilot CLI をインストールした後、次のコマンドを実行して、お使いの GitHub アカウントで CLI を認証します。

    ```bash
    copilot login
    ```

    このコマンドを実行すると、ブラウザー ウィンドウが開きます。ここで GitHub アカウントにログインし、GitHub Copilot CLI がお使いのアカウントにアクセスすることを承認できます。

    > **注**:`copilot login` コマンドを実行すると、認証コードがターミナル ウィンドウに表示されます。 ブラウザー ウィンドウが自動的に開かない場合は、認証コードをコピーしてブラウザーに貼り付けることで、認証プロセスを完了できます。

    認証後、ラボ演習での GitHub Copilot SDK の使用を開始できます。 GitHub Copilot CLI は、Visual Studio Code で AI コード補完とチャット応答を生成するために、GitHub Copilot SDK によって使用されます。
