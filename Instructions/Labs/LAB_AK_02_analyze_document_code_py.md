---
lab:
  title: 演習 - GitHub Copilot を使用してコードを分析し、文書化する (Python)
  description: GitHub Copilot in Visual Studio Code を使用して、新しい、またはなじみのないコードを分析し、ドキュメントを生成する方法について説明します。
---

# GitHub Copilot を使用してコードを分析し、文書化する

GitHub Copilot は、説明やドキュメントを生成することで、コードベースを理解して文書化するのに役立ちます。 この演習では、GitHub Copilot を使用してコードベースを分析し、プロジェクトのドキュメントを生成します。

この演習の所要時間は約 **20** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントにサインアップし、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です。Git 2.48 以降、Python 3.10 以降、Microsoft の Python 拡張機能をインストールした Visual Studio Code、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

この演習のラボ環境としてローカル PC を使用している場合:

- ローカル PC をラボ環境として構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://microsoftlearning.github.io/mslearn-github-copilot-dev/Instructions/Labs/LAB_AK_00_configure_lab_environment_py.html" target="_blank">ラボ環境のリソースを構成する</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習に、ホストされたラボ環境をお使いの場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、次の URL をブラウザーのサイト ナビゲーション バーに貼り付けてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

- コマンド ターミナルを開き、次のコマンドを実行します。

    Visual Studio Code が正しいバージョンの Python を使用するように構成されていることを確認するには、Python インストールがバージョン 3.10 以降であることを確認します。

    ```bash
    python --version
    ```

    Git が自分の名前とメール アドレスを使用するように構成されていることを確認するには、次のコマンドを自分の情報で更新してから、コマンドを実行します。

    ```bash

    git config --global user.name "John Doe"

    ```

    ```bash

    git config --global user.email johndoe@example.com

    ```

## 演習のシナリオ

あなたは地域コミュニティの IT 部署で働く開発者です。 公共図書館を支えるバックエンド システムが火災で失われました。 システムを置き換えられるまで、チームは図書館スタッフが業務を管理できるようにするための一時的なプロジェクトを開発する必要があります。 チームは、開発プロセスをスピードアップするために GitHub Copilot を選びました。

同僚は図書館アプリケーションの初期バージョンを開発しましたが、時間の制約があり、コードを文書化できませんでした。 コードベースを分析し、プロジェクトのドキュメントを作成する必要があります。

この演習には、次のタスクが含まれています。

- Visual Studio Code で図書館アプリケーションを設定します。
- GitHub Copilot を使用して、ライブラリ アプリケーションのコードベースを説明する。
- GitHub Copilot を使用して、図書館アプリケーションの README.md ファイルを作成します。

## Visual Studio Code で図書館アプリケーションを設定する

あなたの同僚は、図書館アプリケーションの初期バージョンを開発し、.zip ファイルとして使用できるようにしました。 ZIP ファイルをダウンロードし、コード ファイルを展開して、Visual Studio Code でプロジェクトを開く必要があります。

図書館アプリケーションを設定するには、次の手順に従います。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. 図書館アプリケーションを含む ZIP ファイルをダウンロードするには、次の URL をブラウザーのアドレス バーに貼り付けます。[GitHub Copilot ラボ - コードの分析と文書化](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/AZ2007LabAppM2Python.zip)

    AZ2007LabAppM2Python.zip という ZIP ファイルがラボ環境にダウンロードされます。

1. **AZ2007LabAppM2Python.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **AZ2007LabAppM2Python.zip** を右クリックし、**[すべて展開]** を選択します。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. 展開されたファイル フォルダーを開き、**AccelerateDevGHCopilot** フォルダーを Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で **AccelerateDevGHCopilot** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**AccelerateDevGHCopilot** を選択してから、**[フォルダーの選択]** を選択します。

1. Visual Studio Code のエクスプローラー ビューで、次のプロジェクト構造を確認します。

    - AccelerateDevGHCopilot/library   ├── application_core   ├── console   ├── infrastructure   └── tests

1. アプリケーションが正常に動いていることを確認します。

    たとえば、Visual Studio Code でターミナルを開き、**AccelerateDevGHCopilot/library** ディレクトリに移動して、次のコマンドを実行します。

    ```bash
    python -m unittest discover tests
    ```

    警告はいくつか表示されますが、エラーは発生しないはずです。

## GitHub Copilot を使用してライブラリ アプリケーションのコードベースを説明する

GitHub Copilot を使用してプロジェクト、ファイル、コード行レベルで説明を生成すると、なじみのないコードベースを理解するのに役立ちます。

### チャット ビューのプロンプトを使用してコードを分析する

GitHub Copilot のチャット ビューには、自然言語プロンプトを使用して GitHub Copilot と対話できるチャットベースのインターフェイスが用意されています。 既存のコードベースを初めて評価するときに、ワークスペースまたはプロジェクト レベル、またはコード ブロックまたはコード行レベルで説明を生成するプロンプトを作成できます。 プロンプトのコンテキストを指定する際に利用できるように、GitHub Copilot にはチャット参加者、チャット変数、スラッシュ コマンドが用意されています。

- チャット参加者は、プロンプトのスコープを特定のドメインに限定するために使用されます。
- チャット変数は、プロンプトに特定のコンテキストを含めるために使用されます。
- スラッシュ コマンドは、一般的なシナリオで複雑なプロンプトを記述する手間を省くために使用されます。

以下の手順に従って、演習のこのセクションを完了します。

1. Visual Studio Code で AccelerateDevGHCopilot/library プロジェクトが開いていることを確認します。

1. GitHub Copilot のチャット ビューを開きます。

    チャット ビューを開くには、Visual Studio Code ウィンドウの上部にある **[チャットの切り替え]** ボタンを選択します。

    ![GitHub Copilot の状態メニューを示すスクリーンショット。](./Media/m02-github-copilot-toggle-chat.png)

    **Ctrl+Alt+I** キーボード ショートカットを使用してチャット ビューを開くこともできます。

1. チャット ビューで、GitHub Copilot の **@workspace** チャット参加者を使用してプロジェクトの説明を生成するプロンプトを入力します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```plaintext
    @workspace describe this project
    ```

    **@workspace** などのチャット参加者を使用して、GitHub Copilot によって生成される応答を改善します。 チャット参加者は、特定分野の専門家のように機能し、それぞれの専門領域で支援を提供します。 GitHub Copilot でプロジェクトの構造、コードのさまざまな部分の操作、またはプロジェクトの設計パターンを考慮する場合は、**@workspace** を使用します。

    使用できるすべてのチャット参加者の一覧を表示するには、チャット プロンプト ボックスに「**@**」と入力します。

1. 少し時間を取って、GitHub Copilot の応答と実際のプロジェクト ファイルを比較してみましょう。

    プロジェクト内の各プロジェクトについて説明する応答が表示されます。

    - **application_core**
    - **infrastructure**
    - **console**
    - **tests**

1. エクスプローラー ビューを使用してプロジェクト フォルダーを展開します。

1. **console_app.py** ファイルを見つけて開きます。

    **console_app.py** ファイルは、**application _core\console** フォルダーにあります。

1. 少し時間を取って、コード ファイルを確認しましょう。

1. **console_app.py** クラスの説明を生成するプロンプトをチャット ビューに入力します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```plaintext
    @workspace #usages How is the ConsoleApp class used?
    ```

    **#usages** などのチャット変数を使用して、プロンプトに特定のコンテキストを含めます。 チャット変数の一覧を表示するには、チャット プロンプト ボックスに「**#**」と入力します。

    > **注**:GitHub Copilot は、プロンプトのコンテキストを構築し、応答を生成するときに、チャット履歴と Visual Studio Code で開いているコード ファイルを考慮します。

1. 少し時間を取って、GitHub Copilot の応答の正確性を確認しましょう。

    **ConsoleApp** クラスが定義されている場所と、コードベースでどのように使用されているかを説明する応答が表示されます。 応答では、ファイル **console_app.py** と **main.py** が行番号と共に参照されます。

1. **application _core\console** フォルダーのルートから **main.py** ファイルを開き、コードを確認します。

1. **main.py** ファイルの説明を生成するプロンプトをチャット ビューに入力します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```plaintext
    @workspace /explain Explain the main.py file
    ```

    一般的なシナリオで複雑なプロンプトを入力しなくても済むようにするには、**/explain** などのスラッシュ コマンドを使用します。 使用できるすべてのスラッシュ コマンドの一覧を表示するには、チャット プロンプト ボックスに「**/**」と入力します。 使用できるスラッシュ コマンドは、環境やチャットのコンテキストによって異なります。

1. 少し時間を取って、GitHub Copilot によって生成された詳細な応答を確認しましょう。

    アプリケーション内でファイルがどのように使用されるかを説明する概要と詳細を含む応答が表示されます。

1. **main.py** ファイルを閉じます。

### コンテキストを追加してチャットの応答を改善する

GitHub Copilot は、コンテキストを使用して、より関連性の高い応答を生成します。

コード エディターでファイルを開くことはコンテキストを確立する 1 つの方法ですが、ドラッグ アンド ドロップ操作や、チャット ビューの **[コンテキストのアタッチ]** ボタンを使用して、チャット コンテキストにファイルを追加することもできます。

以下の手順に従って、演習のこのセクションを完了します。

1. **Infrastructure** フォルダーを展開します。

1. ドラッグ アンド ドロップ操作を使用して、エクスプローラー ビューからチャット コンテキストに次のファイルを追加します: **json_data.py**、**json_loan_repository.py**、**json_patron_repository.py**。

    GitHub Copilot はチャット コンテキストを使用して、プロンプトに関連するコード ファイルを理解します。 ドラッグ アンド ドロップ操作を使用してチャット コンテキストにファイルを追加するか、チャット ビューの **[コンテキストのアタッチ]** ボタンを使用することができます。

    個々のファイルを手動で追加するのではなく、Copilot にコードベースから適切なファイルを自動的に見つけてもらうことができます。 これは、どのファイルが質問に関連しているかわからない場合に役立ちます。

    Copilot が適切なファイルを自動的に見つけられるようにするには、プロンプトに #codebase を追加するか、コンテキストの種類の一覧から Codebase を選択します。

1. データ アクセス クラスの説明を生成するプロンプトをチャット ビューに入力します。

    たとえば、チャット ビューに次のプロンプトを入力します。

    ```plaintext
    @workspace /explain Explain how the data access classes work
    ```

1. 少し時間を取って、応答を読みましょう。

    各データ アクセス クラス (**json_data.py**、**json_loan_repository.py**、**json_patron_repository.py**) と、それらがどのように連携してアプリケーションでデータ アクセスを管理するかを説明する応答が表示されます。 応答では、**load_data**、**save_loans**、**save_patrons** などの主要なメソッドが記載されているはずです。

1. 少し時間を取って、図書館レコードのシミュレートに使用される JSON データ ファイルを確認しましょう。

    JSON データ ファイルは **infrastructure/json** フォルダーにあります。

    データ ファイルは、ID プロパティを使用してエンティティをリンクします。 たとえば、**loan** オブジェクトには、同じ ID を持つ **patron** オブジェクトにリンクする **patron_id** プロパティがあります。 JSON ファイルには、著者、書籍、書籍アイテム、パトロン、ローンのデータが含まれています。

    > **注**:このトレーニングの目的上、著者名、書籍タイトル、パトロン名は匿名化されてることに注意してください。

### アプリケーションの構築と実行

アプリケーションを実行すると、ユーザー インターフェイス、アプリケーションの主な機能、アプリ コンポーネントの相互作用を理解するのに役立ちます。

以下の手順に従って、演習のこのセクションを完了します。

1. **エクスプローラー** ビューが開かれていることを確認します。

1. Visual Studio Code で Python を使用してアプリケーションを実行するには、エディターで **console/main.py** を開き、**CTRL+Shift+D** キーを押して [実行とデバッグ] パネルを開き、**[Python:現在のファイル]** または別のデバッグ構成を選択し、**F5** キーを押してデバッグを開始します。

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

## README ファイルのプロジェクト ドキュメントを作成する

Readme ファイルは、プロジェクトの共同作成者と関係者にコード リポジトリに関する重要な情報を提供するものです。 ユーザーがプロジェクトの目的、使用方法、貢献方法を理解するのに役立ちます。 README ファイルを適切に構成すると、プロジェクトの使いやすさと保守性が大幅に向上します。

次のセクションを含む README ファイルが必要です。

- **プロジェクトのタイトル**:プロジェクトの簡潔で明確なタイトル。
- **[説明]**: プロジェクトの内容と実行内容の詳細な説明。
- **プロジェクト構造**:主要なフォルダーやファイルなど、プロジェクト構造の内訳。
- **主要なクラスとインターフェイス**:プロジェクト内の主要なクラスとインターフェイスの一覧。
- **使用状況**:プロジェクトを使用する手順 (多くの場合、コード例を含む)。
- **ライセンス**:プロジェクトの基となるライセンス。

演習のこのセクションでは、GitHub Copilot を使用してプロジェクト ドキュメントを作成し、それを **README.md** ファイルに追加します。

以下の手順に従って、演習のこのセクションを完了します。

1. **AccelerateDevGHCopilot** プロジェクトのルート フォルダーに、**README.md** という新しいファイルを追加します。

1. チャット ビューを開きます。

1. README ファイルのプロジェクト ドキュメントを生成するには、次のプロンプトを入力します。

    ```plaintext
    @workspace Generate the contents of a README.md file for a code repository. 
    Use "Library App" as the project title. The README file should include the 
    following sections: Description, Project Structure, Key Classes and Interfaces, 
    Usage, License. Format all sections as raw markdown. Use a bullet list with 
    indents to represent the project structure. Do not include ".gitignore", ".
    pyc", or the ".github", "__pycache__" folders.
    ```

    > **注**:複数のプロンプトを使用すると、README ファイルのセクションごとに 1 つずつ、より詳細な結果が生成されます。 この演習では、プロセスを簡略化するために 1 つのプロンプトを使用します。

1. 応答を確認して、各セクションがマークダウンとして書式設定されていることを確認します。

    正しく書式設定されていない場合は、セクションを個別に更新して、より詳細な情報を提供できます。 GitHub Copilot の応答を README ファイルにコピーし、マークダウン ファイルで直接修正することもできます。

1. 提案されたドキュメントをコピーし、README.md ファイルに貼り付けます。

    応答全体をコピーするには、回答の一番下までスクロールし、"親指を立てた" アイコンの右側の空白部分を右クリックして、**[コピー]** を選択します。

1. 必要に応じて README.md ファイルを書式設定します。

    GitHub Copilot の設定を更新して Markdown 形式を有効にし、GitHub Copilot を使用して README.md ファイルのセクションを更新できます。

    完了すると、指定した各セクションが適切な詳細レベルで含まれた README.md ファイルが作成されるはずです。

## まとめ

この演習では、GitHub Copilot を使用してコードベースを分析し、文書化する方法を学びました。 GitHub Copilot を使用して、プロジェクト構造、主要クラス、データ アクセス クラスの説明を生成しました。 また、GitHub Copilot を使用して、プロジェクトの README.md ファイルも作成しました。

## クリーンアップ

演習が完了したので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに保持したくない変更が加えられていないか確認します。 変更を加えた場合は、ここで元に戻します。
