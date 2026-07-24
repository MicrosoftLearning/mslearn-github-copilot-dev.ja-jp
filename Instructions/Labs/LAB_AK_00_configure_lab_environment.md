---
lab:
  title: 準備 - GitHub Copilot 演習用にラボ環境を構成する
  description: GitHub Copilot 演習を開始する前に、ラボの要件を確認し、リソースを構成します。
  duration: 15 minutes
  level: 200
  primarytopics:
    - GitHub
    - Visual Studio Code
---

# GitHub Copilot 演習用にラボ環境を構成する

ラボ環境は、Visual Studio Code と GitHub Copilot を使用する C# 開発用に構成する必要があります。 GitHub Copilot が有効になっている GitHub アカウントへのアクセスが必要です。

ラボ環境が正しく構成されていることを確認するには、次の手順を実行します。

1. Git バージョン 2.48 以降がラボ環境にインストールされていることを確認します。

    ターミナル ウィンドウで次のコマンドを実行し、インストールされている Git のバージョンを確認します。

    ```bash
    git --version
    ```

    Windows を実行していて、Git を更新する場合は、次のコマンドを使用できます。

    ```bash
    git update-git-for-windows
    ```

    必要に応じて、次の URL を使用して Git をダウンロードできます。<a href="https://git-scm.com/downloads" target="_blank">Git のダウンロード</a>。

1. ラボ環境に .NET SDK の最新の LTS または STS バージョンがインストールされていることを確認します。

    ターミナル ウィンドウで次のコマンドを実行し、インストールされている .NET SDK のバージョンを確認します。

    ```dotnetcli
    dotnet --version
    ```

    必要に応じて、次の URL を使用して .NET SDK をダウンロードできます。<a href="https://dotnet.microsoft.com/download/dotnet" target="_blank">.NET SDK のダウンロード</a>。

1. Visual Studio Code と C# 開発キット拡張機能がラボ環境にインストールされていることを確認します。

    必要に応じて、次の URL を使用して Visual Studio Code をダウンロードできます。<a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code をダウンロードする</a>

    C# 開発キット拡張機能は、Visual Studio Code の [拡張機能] ビューを使用してインストールできます。

1. GitHub アカウントと GitHub Copilot サブスクリプションへのアクセス権があることを確認します。

    次の URL を使用して GitHub アカウントにログインできます。<a href="https://github.com/login" target="_blank">GitHub ログイン</a>。

    GitHub アカウントをお持ちでない場合は、GitHub ログイン ページで個別のアカウントを作成できます。 ログイン ページで、**[アカウントの作成]** を選択します。

    GitHub アカウントの設定/プロファイル ページを開き、GitHub Copilot サブスクリプションへのアクセス権が得られたことを確認します。 トレーニングに使用できる GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business または GitHub Copilot Enterprise のアクティブなサブスクリプションをお持ちの場合は、GitHub Copilot 演習を完了するために既存の GitHub Copilot サブスクリプションを使用できます。

    個別の GitHub アカウントはあるが、GitHub Copilot サブスクリプションがない場合は、トレーニング演習中に GitHub の設定ページまたは Visual Studio Code から GitHub Copilot Free プランを設定できます。

    > **重要**: GitHub Copilot Free は、学習および評価を目的とした限定バージョンの GitHub Copilot です。 これには、1 か月あたり最大 2,000 件のコード補完と、Copilot Chat やその他の AI 搭載機能への制限付きアクセスが含まれます。 トレーニング演習以外で GitHub Copilot Free を使用すると、コースを完了する前に使用制限に達する可能性があります。 GitHub Copilot の課金および使用量の制限は 2026 年 6 月 1 日に変更され、現在は Premium 要求ではなく使用量に基づきます。 使用制限や含まれる機能はプランによって異なる場合があり、時間の経過と共に変化する可能性もあります。
