---
lab:
  title: 準備 - GitHub Copilot 演習用にラボ環境を構成する (Python)
  description: GitHub Copilot 演習を開始する前に、ラボの要件を確認し、リソースを構成します。
---

# GitHub Copilot 演習用にラボ環境を構成する

ラボ環境は、Visual Studio Code と GitHub Copilot を使用する Python 開発用に構成する必要があります。 GitHub Copilot が有効になっている GitHub アカウントへのアクセスが必要です。

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

1. ラボ環境に最新バージョンの Python がインストールされていることを確認します。

    ターミナル ウィンドウで次のコマンドを実行し、インストールされている Python のバージョンを確認します。

    ```bash
    python3 --version
    ```

    必要に応じて、次の URL を使用して Visual Studio Code で Python を構成する手順に従います。<a href="https://code.visualstudio.com/docs/python/python-tutorial" target="_blank">VS Code での Python の概要</a>。

1. Visual Studio Code と Python 拡張機能がラボ環境にインストールされていることを確認します。

    必要に応じて、次の URL を使用して Visual Studio Code をダウンロードできます。<a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code をダウンロードする</a>

    Python 拡張機能は、Visual Studio Code の [拡張機能] ビューを使用してインストールできます。

1. GitHub アカウントと GitHub Copilot サブスクリプションへのアクセス権があることを確認します。

    次の URL を使用して GitHub アカウントにログインできます。<a href="https://github.com/login" target="_blank">GitHub ログイン</a>。

    GitHub アカウントをお持ちでない場合は、GitHub ログイン ページで個別のアカウントを作成できます。 ログイン ページで、**[アカウントの作成]** を選択します。

    GitHub アカウントの設定/プロファイル ページを開き、GitHub Copilot サブスクリプションへのアクセス権が得られたことを確認します。 トレーニングに使用できる GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business または GitHub Copilot Enterprise のアクティブなサブスクリプションをお持ちの場合は、GitHub Copilot 演習を完了するために既存の GitHub Copilot サブスクリプションを使用できます。

    個別の GitHub アカウントを持っているが、GitHub Copilot サブスクリプションがない場合は、トレーニング演習中に Visual Studio Code で GitHub Copilot Free プランを設定できます。

    > **重要**:GitHub Copilot Free プラン は、GitHub Copilot の制限付きバージョンであり、1 か月あたり最大 2,000 個のコード補完と 50 個のチャットまたは Premium リクエストが可能です。 トレーニング演習以外で GitHub Copilot Free プランを使用すると、トレーニングを完了する前にプランのリソース制限を超える可能性があります。 GitHub Copilot Free プランは、GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business または GitHub Copilot Enterprise サブスクリプションでは使用できません。
