---
lab:
  title: 演習 - GitHub Copilot を使用して新しいコード機能を開発する (Python)
  description: Visual Studio Code で GitHub Copilot を使って新しいコード機能の開発をスピードアップする方法について説明します。
---

# GitHub Copilot を使用して新しいコード機能を開発する

GitHub Copilot のコード補完機能と対話型チャット機能により、開発者はより速く、より少ないエラーでコードを記述できるようになります。 記述されているコードのコンテキストに基づいて、コード スニペット、関数、さらにはクラス全体の提案が提供されます。 この演習では、GitHub Copilot を使って、Visual Studio Code での新しいコード機能の開発の速度を上げます。

この演習の所要時間は約 **30** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://go.microsoft.com/fwlink/?linkid=2320148" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のものが必要です。Git 2.48 以降、Python 3.10 以降、Visual Studio Code と Microsoft が提供する Python 拡張機能、GitHub Copilot が有効になっている GitHub アカウントへのアクセス。

この演習のラボ環境として、ローカル PC をお使いの場合:

- ローカル PC をラボ環境として構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://microsoftlearning.github.io/mslearn-github-copilot-dev/Instructions/Labs/LAB_AK_00_configure_lab_environment_py.html" target="_blank">ラボ環境のリソースを構成する</a>。

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

この演習に、ホストされたラボ環境をお使いの場合:

- Visual Studio Code で GitHub Copilot サブスクリプションを有効にする方法については、次の URL をブラウザーのサイト ナビゲーション バーに貼り付けてください: <a href="https://go.microsoft.com/fwlink/?linkid=2320158" target="_blank">Visual Studio Code で GitHub Copilot を有効にする</a>。

- コマンド ターミナルを開き、次のコマンドを実行します。

    正しいバージョンの Python を使うように Visual Studio Code が構成されていることを確認するには、Python のインストールがバージョン 3.10 以降であることを確認します。

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

あなたは地域コミュニティの IT 部署で働く開発者です。 公共図書館を支えるバックエンド システムが火災で失われました。 あなたのチームは、システムを置き換えられるようになるまで図書館スタッフの業務の管理を支援する一時的なプロジェクトを開発する必要があります。 チームは、開発プロセスをスピードアップするために GitHub Copilot を選びました。

図書館アプリケーションの初期バージョンはエンド ユーザーによってテストされ、いくつかの追加機能が要望されています。 チームは、次の機能に取り組むことに同意しました。

- 書籍の在庫: 司書が書籍の在庫の状態を確認できるようにします。 この機能では、書籍が貸し出し可能であることを示すメッセージ、またはその書籍が現在他の利用者に貸し出し中の場合は返却期限を表示する必要があります。

- 書籍の貸出: 司書が書籍を利用者に貸し出せるようにします (書籍がある場合)。 この機能では、利用者が貸し出し中の書籍を受け取るオプションを表示し、新しい貸し出し内容で Loans.json を更新して、利用者の更新された貸出の詳細を表示する必要があります。

- 書籍の予約: 司書が利用者のために書籍を予約できるようにします (書籍が既に予約されている場合を除く)。 この機能は、新しい書籍予約プロセスを実装する必要があります。 この機能では、予約プロセスのサポートに必要な新しいクラスとインターフェイスと共に、新しい Reservations.json ファイルを作成する必要がある場合があります。

各チーム メンバーは新しい機能の 1 つの作業を行った後、再びグループになります。 あなたは、書籍の在庫の状態を判断する機能に取り組みます。 同僚は、書籍を利用者に貸し出す機能に取り組みます。 最後の、利用者のために書籍を予約する機能は、他の 2 つの機能が完了した後に開発されます。

この演習には、次のタスクが含まれています。

1. Visual Studio Code で図書館アプリケーションを設定します。

1. Visual Studio Code を使って、図書館アプリケーション用の GitHub リポジトリを作成します。

1. コード リポジトリに "書籍の在庫" ブランチを作成します。

1. 新しい "書籍の在庫" 機能を開発します。

    - GitHub Copilot の提案を使用して、コードをより迅速かつ正確に実装できるようにします。
    - コードの更新をリモート リポジトリの "書籍の在庫" ブランチと同期させます。

1. "書籍貸出状況" の更新内容をリポジトリのメイン ブランチにマージします。

## Visual Studio Code で図書館アプリケーションを設定する

既存のアプリケーションをダウンロードし、コード ファイルを展開した後、Visual Studio Code でプロジェクトを開く必要があります。

次の手順のようにして、図書館アプリケーションを設定します。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. 図書館アプリケーションを含む ZIP ファイルをダウンロードするには、次の URL をブラウザーのアドレス バーに貼り付けます。[GitHub Copilot ラボ - コード機能の開発](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/AZ2007LabAppM3Python.zip)

    ZIP ファイルの名前は **AZ2007LabAppM3Python.zip** です。

1. **AZ2007LabAppM3Python.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **AZ2007LabAppM3Python.zip** を右クリックして、**[すべて展開]** を選びます。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. 展開されたファイル フォルダーを開き、**AccelerateDevGHCopilot** フォルダーを Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で **AccelerateDevGHCopilot** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**AccelerateDevGHCopilot** を選んでから、**[フォルダーの選択]** を選びます。

1. Visual Studio Code のエクスプローラー ビューで、次のプロジェクト構造を確認します。

    - AccelerateDevGHCopilot/library   ├── application_core   ├── console   ├── infrastructure   └── tests   └── readme.md

1. アプリケーションが正常に動いていることを確認します。

    たとえば、Visual Studio Code でターミナルを開き、**AccelerateDevGHCopilot/library** ディレクトリに移動して、次のコマンドを実行します。

    ```bash
    python -m unittest discover -v tests
    ```

    いくつかの警告が表示されますが、エラーは発生しないはずです。

## コード用の GitHub リポジトリを作成する

コード用の GitHub リポジトリを作成すると、他のユーザーと作業を共有したり、プロジェクトで共同作業を行うことができます。

> **注**:自分の GitHub アカウントを使用して、ライブラリ アプリケーション用のプライベート GitHub リポジトリを作成してください。

以下の手順に従って、演習のこのセクションを完了します。

1. ブラウザー ウィンドウを開き、GitHub アカウントに移動します。

    GitHub ログイン ページは [https://github.com/login](https://github.com/login) です。

1. GitHub アカウントにサインインします。

1. GitHub アカウント メニューを開き、**[ご使用のリポジトリ]** を選択します。

1. [Visual Studio Code] ウィンドウに切り替えます。

1. Visual Studio Code で、ソース管理ビューを開きます。

1. **[GitHub に公開]** を選択します。

1. リポジトリの名前は **AccelerateDevGHCopilot** です。

    > **注**:Visual Studio Code で GitHub にサインインしていない場合は、サインインするように求められます。 サインインしたら、要求されたアクセス許可で Visual Studio Code を認可します。

1. **[GitHub プライベート リポジトリに公開]** を選択します。

1. 公開プロセス中に、Visual Studio Code にステータス メッセージが表示されている点に注目します。

    公開プロセスが完了すると、指定した GitHub リポジトリにコードが正常に公開されたことを示すメッセージが表示されます。

1. GitHub アカウントのブラウザー ウィンドウに切り替えます。

1. GitHub アカウントで新しい AccelerateDevGHCopilot リポジトリを開きます。

    AccelerateDevGHCopilot リポジトリが表示されない場合は、ページを最新の情報に更新します。 それでもリポジトリが表示されない場合は、次の手順を試してください。

    1. Visual Studio Code に切り替えます。
    1. 通知を開きます (通知は、新しいリポジトリが公開されたときに生成されています)。
    1. **[GitHub 上で開く]** を選択してリポジトリを開きます。

## リポジトリに新しいブランチを作成する

新しい "書籍の在庫" 機能の開発を開始する前に、リポジトリに新しいブランチを作成する必要があります。 これにより、リポジトリのメイン ブランチに影響を与えずに、新しい機能に取り組むことができます。 コードの準備ができたら、新しい機能をメイン ブランチにマージできます。

以下の手順に従って、演習のこのセクションを完了します。

1. Visual Studio Code で AccelerateDevGHCopilot プロジェクトが開いていることを確認します。

1. [ソース管理] ビューを選択し、ローカル リポジトリがリモート リポジトリ (プルまたは同期) と同期していることを確認します。

1. ウィンドウの左下隅にある  **[main]**  を選択します。

1. 新しいブランチを作成するには、「**書籍の在庫**」と入力して **[+ 新しいブランチの作成]** を選びます。

1. 新しいブランチをリモート リポジトリにプッシュするには、**[ブランチの発行]** を選択します。

## GitHub Copilot を使用して新しい "書籍貸出状況" 機能を開発する

演習のこのセクションでは、GitHub Copilot を使って図書館アプリケーションの新しい機能を開発します。 要望された機能により、図書館員は書籍が貸し出し可能かどうかを確認できるようになります。これは、現在の図書館アプリケーションではサポートされていない一般的なシナリオです。

書籍貸出状況機能を実装するには、次の更新を完了する必要があります。

- **library/console/common_actions.py** の **CommonActions** 列挙型に新しい **SEARCH_BOOKS** アクションを追加します。

- **library/console/console_app.py** で **WriteInputOptions** メソッドを更新します。

  - 新しい **CommonActions.SEARCH_BOOKS** オプションのサポートを追加します。
  - 書籍が貸出可能かどうかを確認するオプションを表示します。

- **library/console/console_app.py** で **ReadInputOptions** メソッドを更新します。

  - 新しい **CommonActions.SEARCH_BOOKS** オプションのサポートを追加します。

- **library/console/console_app.py** で **PatronDetails** メソッドを更新します。

  - **ReadInputOptions** を呼び出す前に、**CommonActions.SEARCH_BOOKS** を **options** に追加します。
  - **SEARCH_BOOKS** アクションを処理するための **else if** を追加します。
  - この **else if** ブロックでは、**SEARCH_BOOKS** という名前の新しいメソッドを呼び出す必要があります。

- **library/console/console_app.py** で新しい **SEARCH_BOOKS** メソッドを作成します。

  - **SEARCH_BOOKS** メソッドでは、ユーザーが指定した書籍のタイトルを読み取る必要があります。
  - 書籍が貸し出し可能かどうかを調べて、次のいずれかを示すメッセージを表示します。

    - "**book.title** は貸し出し可能です"、または
    - "**book.title** は別の利用者に貸し出し中です。 返却期限は **loan.DueDate** です。"

GitHub Copilot Chat は、新機能を完成させるために必要なコードの更新の実装を支援できます。

- インライン チャット セッションを使うと、要件に基づいて、より小規模で影響の小さいコードの更新を実装できます。
- より対話的で反復的なアプローチが必要な大規模なコードの更新には、チャット ビューを使用できます。

### Copilot チャットを使用して "書籍貸出状況" の更新を実装する

**Copilot インライン チャット** セッションを使うと、コード エディター内で GitHub Copilot と直接対話できます。 インライン チャットを使用すると、質問したり、コードの提案を依頼したり、GitHub Copilot によって生成されたコードの説明を受け取ったりできます。

以下の手順に従って、演習のこのセクションを完了します。

1. エクスプローラー ビューを開きます。

1. **library/console** プロジェクトを展開します。

1. **console/common_actions.py** ファイルを開き、**CommonActions** クラスを**選択**します。

    **CommonActions** に新しい **SEARCH_BOOKS** アクションを追加する必要があります。

1. インライン チャットを開きます。選択範囲をポイントし、右クリックしてメニューを開き、**[Copilot]** を選んで、**[エディター インライン チャット]** を選びます。

1. 次のプロンプトを入力します。

    ```plaintext
    Update selection to include a new `SEARCH_BOOKS` action.
    ```

    GitHub Copilot により、新しい **SEARCH_BOOK** アクションを **CommonActions** クラスに追加するコードの更新が提案されるはずです。

1. 推奨された更新を確認し、**[承諾]** を選択します。

    更新されたコードは、次のコード スニペットのようになります。

    ```python

    class CommonActions(Flag):
        REPEAT = 0
        SELECT = auto()
        QUIT = auto()
        SEARCH_PATRONS = auto()
        SEARCH_BOOKS = auto() # added
        RENEW_PATRON_MEMBERSHIP = auto()
        RETURN_LOANED_BOOK = auto()
        EXTEND_LOANED_BOOK = auto()
    ```

    `CommonActions` に `SEARCH_BOOKS = auto()` が追加されていることに注意してください。

1. **library/console/console_app.py** ファイルを開きます。

1. `ConsoleApp` クラスで `write_input_options` メソッドを見つけて選びます。 選択範囲をポイントし、右クリックしてメニューを開き、**[Copilot]** を選んで、**[エディター インライン チャット]** を選びます。

    新しい `CommonActions.SEARCH_BOOKS` オプションのサポートを追加する必要があります。 `SEARCH_BOOKS` オプションがある場合、書籍が貸出可能かどうかを調べるオプションを表示します。

1. インライン チャットを開き、次のプロンプトを入力します。

    ```plaintext
    Update selection to include an option for the `CommonActions.SEARCH_BOOKS` action. Use the letter "b" and the message "to check for book availability".
    ```

    GitHub Copilot により、`SEARCH_BOOKS` アクションの新しい `if` ブロックを追加するコードの更新が提案されます。

1. 推奨された更新を確認し、**[承諾]** を選択します。

    提案される更新は、次のコード スニペットのような内容になります。

    ```python
        def write_input_options(self, options):
        print("Input Options:")
        if options & CommonActions.RETURN_LOANED_BOOK:
            print(' - "r" to mark as returned')
        if options & CommonActions.EXTEND_LOANED_BOOK:
            print(' - "e" to extend the book loan')
        if options & CommonActions.RENEW_PATRON_MEMBERSHIP:
            print(' - "m" to extend patron\'s membership')
        if options & CommonActions.SEARCH_PATRONS:
            print(' - "s" for new search')
        if options & CommonActions.SEARCH_BOOKS:
            print(' - "b" to check for book availability')
        if options & CommonActions.QUIT:
            print(' - "q" to quit')
        if options & CommonActions.SELECT:
            print(' - type a number to select a list item.')
    ```

1. 下にスクロールし、**library/console/console_app.py** ファイルで `_handle_patron_details_selection` メソッド (または入力処理セクション) を見つけて選びます。

    ここにも、新しい `CommonActions.SEARCH_BOOKS` オプションのサポートを追加する必要があります。 ユーザーが `SEARCH_BOOKS` アクションを選んだ場合 (たとえば、ユーザーが「b」と入力した場合) の処理を行うケースを含みます。

1. インライン チャットを開き、次のプロンプトを入力します。

    ```plaintext
    Update selection to include an option for the `CommonActions.SEARCH_BOOKS` action.
    ```

    GitHub Copilot により、`SEARCH_BOOKS` アクションを選んだユーザーを処理する新しい `elif` ブロックを追加する更新が提案されます。

1. 推奨された更新を確認し、**[承諾]** を選択します。

    提案される更新は、次のコード スニペットのような内容になります。

    ```python
    def _handle_patron_details_selection(self, selection, patron, valid_loans):
    # ...existing code...

        elif selection == 'b':
            # Placeholder for book search functionality
            print("Book search functionality is not implemented yet.")
            return ConsoleState.PATRON_DETAILS
        elif selection.isdigit():
            idx = int(selection)
            if 1 <= idx <= len(valid_loans):
                self.selected_loan_details = valid_loans[idx - 1][1]
                return ConsoleState.LOAN_DETAILS
            print("Invalid selection. Please enter a number shown in the list above.")
            return ConsoleState.PATRON_DETAILS
        else:
            print("Invalid input. Please enter a number, 'm', 's', 'b', or 'q'.")
            return ConsoleState.PATRON_DETAILS
    ```

#### Copilot インライン チャットを使用してコードの選択を GitHub Copilot Chat と共有する

1. GitHub Copilot Chat が**質問モード**で開かれていることを確認します。

1. 2 つのことを実行する必要があります。

    - `_get_patron_details_input` を呼び出す前に、`options` に `CommonActions.SEARCH_BOOKS` を追加する必要があります。
    - また、`SEARCH_BOOKS` アクションを示す `"b"` の選択を処理する `if` または `elif` ブロックを追加する必要があります。 このブロックでは、`search_books` という名前の新しいメソッドを呼び出す必要があります。

    同じプロンプトで両方の要件に対応できます。

1. **library/console/console_app.py** ファイルで `patron_details` メソッドを見つけて**選択**します。

1. `patron_details` メソッドを選んだまま、選択範囲をマウスでポイントします。 **右クリック**してメニューを開き、**[Copilot]** を選んで、**[選択内容をチャットに追加]** を選びます。

1. 次のプロンプトを入力します。

    ```plaintext
    @workspace Update selection to add `CommonActions.SEARCH_BOOKS` to `options` before calling `_get_patron_details_input`. Add an `if` or `elif` block to handle the `"b"` selection for the `SEARCH_BOOKS` action. The block should call a new method named `search_books`.
    ```

    GitHub Copilot Chat により、`_get_patron_details_input` の呼び出しの前に `options` に `CommonActions.SEARCH_BOOKS` を追加するコードの更新が提案されます。 提供されたコードで、[エディターで適用する] アイコンを選びます。

    ![GitHub Copilot 質問モードの [エディターで適用する] アイコンを示すスクリーンショット。](./Media/m03-github-copilot-chat-view-response-ask-mode.png)

1. 推奨された更新を確認し、**[承諾]** を選択します。
    ```python
    def patron_details(self) -> ConsoleState:
        patron = self.selected_patron_details
        print(f"\nName: {patron.name}")
        print(f"Membership Expiration: {patron.membership_end}")
        loans = self._loan_repository.get_loans_by_patron_id(patron.id)
        print("\nBook Loans History:")

        valid_loans = self._print_loans(loans)

        if valid_loans:
            options = (
                CommonActions.RENEW_PATRON_MEMBERSHIP
                | CommonActions.SEARCH_PATRONS
                | CommonActions.QUIT
                | CommonActions.SELECT
                | CommonActions.SEARCH_BOOKS  # Added SEARCH_BOOKS to options
            )
            selection = self._get_patron_details_input(options)
            return self._handle_patron_details_selection(selection, patron, valid_loans)
        else:
            print("No valid loans for this patron.")
            options = (
                CommonActions.SEARCH_PATRONS
                | CommonActions.QUIT
                | CommonActions.SEARCH_BOOKS  # Added SEARCH_BOOKS to options
            )
            selection = self._get_patron_details_input(options)
            return self._handle_no_loans_selection(selection)

    def _handle_patron_details_selection(self, selection, patron, valid_loans):
        if selection == 'q':
            return ConsoleState.QUIT
        elif selection == 's':
            return ConsoleState.PATRON_SEARCH
        elif selection == 'm':
            status = self._patron_service.renew_membership(patron.id)
            print(status)
            self.selected_patron_details = self._patron_repository.get_patron(patron.id)
            return ConsoleState.PATRON_DETAILS
        elif selection == 'b':
            return self.search_books()  # Call the new search_books method
        elif selection.isdigit():
            idx = int(selection)
            if 1 <= idx <= len(valid_loans):
                self.selected_loan_details = valid_loans[idx - 1][1]
                return ConsoleState.LOAN_DETAILS
            print("Invalid selection. Please enter a number shown in the list above.")
            return ConsoleState.PATRON_DETAILS
        else:
            print("Invalid input. Please enter a number, 'm', 's', 'b', or 'q'.")
            return ConsoleState.PATRON_DETAILS

    def _handle_no_loans_selection(self, selection):
        if selection == 'q':
            return ConsoleState.QUIT
        elif selection == 's':
            return ConsoleState.PATRON_SEARCH
        elif selection == 'b':
            return self.search_books()  # Handle SEARCH_BOOKS when no loans
        else:
            print("Invalid input.")
            return ConsoleState.PATRON_DETAILS

    def search_books(self) -> ConsoleState:
        print("Book search functionality is not implemented yet.")
        return ConsoleState.PATRON_DETAILS

    ```
<!-- TODO: Delete or restore? Previous version that resulted in the Agent bug fix
     ```python

    def patron_details(self) -> ConsoleState:
    patron = self.selected_patron_details
    print(f"\nName: {patron.name}")
    print(f"Membership Expiration: {patron.membership_end}")
    loans = self._loan_repository.get_loans_by_patron_id(patron.id)
    print("\nBook Loans History:")

    valid_loans = self._print_loans(loans)

    if valid_loans:
        options = (
            CommonActions.RENEW_PATRON_MEMBERSHIP
            | CommonActions.SEARCH_PATRONS
            | CommonActions.QUIT
            | CommonActions.SELECT
            | CommonActions.SEARCH_BOOKS
        )
        selection = self._get_patron_details_input(options)
        if selection == 'b':
            return self.search_books()
        return self._handle_patron_details_selection(selection, patron, valid_loans)
    else:
        print("No valid loans for this patron.")
        options = (
            CommonActions.SEARCH_PATRONS
            | CommonActions.QUIT
            | CommonActions.SEARCH_BOOKS
        )
        selection = self._get_patron_details_input(options)
        if selection == 'b':
            return self.search_books()
        return self._handle_no_loans_selection(selection)

    def search_books(self) -> ConsoleState:
        print("Book search option selected.")
        return ConsoleState.PATRON_DETAILS
    ``` 

-->

    > **NOTE**: The code suggested by Inline chat may include stub code for the **search_books()** method as in the previous code sample. You can accept that code stub, but you'll implement the **search_books** method in the next section.

### Copilot Chat の質問モードを使用して SEARCH_BOOKS メソッドを実装する

"書籍貸出状況" の更新の実装には、もう 1 つステップが残っています。**search_books** メソッドの作成です。 **search_books** メソッドでは、ユーザーが指定した書籍タイトルを読み取り、書籍が貸し出し可能かどうかを調べて、書籍の貸し出し状態を示すメッセージを表示します。 チャット ビューを使って要件を評価し、**search_books** メソッドを実装します。

GitHub Copilot のチャット ビューには、インライン チャットの使用時には利用できない、会話型で対話形式の環境があります。 チャット ビューを使うと、質問したり、コードの提案を要求したり、GitHub Copilot によって生成されたコードの説明を取得したりできます。 チャット ビューでは、次の 3 つのモードがサポートされています。

- 質問モード:質問モードは、コードベースをより深く理解し、アイデアをブレインストーミングし、コーディング タスクを支援するために使用されます。 質問モードで生成されたコード提案は、コードベースに直接実装することも、クリップボードにコピーすることもできます。
- 編集モード:編集モードは、リファクタリングや新しい機能の追加など、コードを変更するために使用されます。 編集モードでは、プロジェクト内の複数のファイルを編集できます。
- エージェント モード:エージェント モードは、上位レベルのタスクを定義し、そのタスクを実行するためのエージェント コード編集セッションを開始するために使用されます。 エージェント モードでは、Copilot は必要な作業を自律的に計画し、関連するファイルとコンテキストを決定します。 エージェントはコードの変更、テストの実行、さらにはアプリケーションのデプロイまで行うことができます。
<!-- TODO: line below do we use all 3 listed modes? -->
**インライン チャット**、**質問**、**編集**モードを使って、**search_books** メソッドを実装します。

以下の手順に従って、演習のこのセクションを完了します。

1. 少し時間を取って、**search_books** メソッドのプロセスの要件を確認してください。

    このメソッドではどのようなプロセスを完了する必要がありますか? このメソッドの戻り値の型は何ですか? パラメーターは必要ですか?

    **search_books** メソッドでは、次のプロセスを実装する必要があります。

    1. ユーザーに書籍のタイトルの入力を求めるメッセージを表示する。
    1. ユーザーが指定した書籍のタイトルを読み取る。
    1. 書籍が貸し出し可能かどうかを確認します。
    1. 次のいずれかのオプションを示すメッセージを表示する。

        - "**{book.title}** は貸し出し可能です"
        - "**{book.title}** は別の利用者に貸し出されています。 返却期限は **{loan.due_date}** です。"

    メッセージ オプションをビルドするには、コードで次の JSON ファイルにアクセスする必要があります。

    - 一致する **Title** と **Id** を見つけるには、**Books.json** が必要です。
    - 書籍の各物理コピーの **BookId** を検索するには **BookItems.json** が必要です (**BookId** が **Books.json** の **Id** と一致するもの)。
    - 一致する **BookItemId** の **ReturnDate** と **DueDate** を検索するには **Loans.json** が必要です (**BookItemId** が **BookItems.json** の **Id** と一致するもの)。

> **注**:  
> **Loans.json** の **BookItemId** は、**BookItems.json** の **Id** を参照します。  
> **BookItems.json** の **BookId** は、**Books.json** の **Id** を参照します。

1. 次の **search_books** メソッドが **console_app.py** ファイルに作成されていることを確認します。

    ```python

    def search_books(self) -> ConsoleState:
        print("Book search functionality is not implemented yet.")
        return ConsoleState.PATRON_DETAILS

    ```

    > **注**:GitHub Copilot によって作成されたコード コメントは必ず削除してください。 不要で不正確なコメントは、GitHub Copilot の提案に悪影響を及ぼすおそれがあります。

1. VSCode で **console_app.py** を開き、**search_books** メソッドを選びます。

1. チャット ビューを開き、次のプロンプトを入力します。

    ```plaintext
    @workspace Update selection to obtain a book title. Prompt the user to "Enter a book title to search for". Read the user input and ensure the book title isn't null.
    ```

1. 提案された更新内容を確認します。

    提案される更新は、次のコード スニペットのような内容になります。

    ```python

    def search_books(self) -> ConsoleState:
        while True:
            book_title = input("Enter a book title to search for: ").strip()
            if not book_title:
                print("No input provided. Please enter a book title.")
            else:
                # Placeholder for future book search logic
                print(f"Searching for book titled: {book_title}")
                break
        return ConsoleState.PATRON_DETAILS

    ```

1. 提案されたコードをマウス ポインターでポイントし、**[library\console\console_app.py に適用]** を選びます。

    提案されたコードがコード エディターに表示されて、**[保持する]** または **[元に戻す]** オプションが表示されます。

1. コード エディターで、提案されたコードを受け入れるには、**[保持する]** を選択します。

1. **search_books** メソッドを選びます。

1. ドラッグ アンド ドロップ操作を使用して、次のファイルをチャット コンテキストに追加します。

    - library/console/**console_app.py** (現在選択されているファイル)
    - library/console/**main.py**
    - library/infrastructure/Json/**Books.json**
    - library/infrastructure/Json/**Loans.json**
    - library/infrastructure/Json/**BookItems.json**
    - library/infrastructure/**json_data.py**
    - library/infrastructure/**json_loan_repository.py**

1. 次のプロンプトを入力します。

    ```plaintext
    @workspace provide a simple text overview plan of how to update the search_books method and ConsoleApps class to find a matching book title in the Books.json file. Use the user supplied book title to find a matching book. If a book is found, use Loans.json to check if the book is available for loan. If the book has been returned, display a message stating "book.title is available for loan". If the book is on loan, display a message stating "book.title is on loan to another patron. The return due date is loan.DueDate". Don't provide code yet. List the general change recommendations for each high level item as a numbered list of paragraphs.
    ```

1. 少し時間をかけて、提案された更新を確認しましょう。

    応答には次の情報が含まれているはずです。

    ```plaintext

    1. Update the ConsoleApp class to access book data:  
    Add a reference to the book repository or the loaded books data from JsonData within the ConsoleApp class. This will allow the search_books method to access the list of all books and perform searches based on user input.
    
    2. Modify the search_books method to search for a book:  
    Update the search_books method to use the user-supplied book title to search for a matching book in the loaded books data (from Books.json). The search should be case-insensitive and match the title exactly or partially, depending on requirements.
    
    3. Check book availability using loan data:  
    Once a matching book is found, use the book's ID to find all related book items (copies) and then check the Loans.json data to determine if any copies are currently on loan (i.e., have a loan record with a null ReturnDate). If all copies are returned, the book is available for loan.
    
    4. Display book availability status to the user:  
    If at least one copy of the book is available (not currently on loan), display a message such as "book.title is available for loan." If all copies are on loan, display a message such as "book.title is on loan to another patron. The return due date is loan.DueDate," using the due date from the active loan.
    
    5. Handle no match or multiple matches:  
    If no book matches the search, display an appropriate message to the user. Optionally, if multiple books match, prompt the user to refine their search or select from a list.
    
    6. Keep user in the book search loop:  
    After displaying the result, allow the user to search for another book or return to the previous menu, maintaining a smooth user experience.

    ```

    > **注**: GitHub Copilot の応答でコードの更新を受け取っている場合があります。 
    > コードの更新は、次のステップで対処します。

    チャット ビューの**質問**モードを使ってコードの更新を分析し、**編集**モードを使ってコードの更新を実装することもできます。

1. サンプル プロンプトを確認します (後でプロンプトを実行します)。

    ```plaintext

    Update the search_books method and ConsoleApp class so that when a user enters a book title, the app searches 
    Books.json for a matching title (case-insensitive, partial match allowed). If a match is found, check all 
    related BookItem records and their loan status in Loans.json. If any copy is not currently on loan (no active 
    loan or has a ReturnDate), display "book.title is available for loan". If all copies are on loan, display 
    "book.title is on loan to another patron. The return due date is loan.DueDate" (show the soonest due date). 
    Integrate this logic into the user flow and ensure clear user messaging.
    ```

    Copilot のフィードバックからのプロンプトの生成を Copilot に要求し、特定の要件を実現するようにプロンプトを調整できます。 確認のため、Copilot によって生成されたプロンプトの例を次に示します。

    ```plaintext

    Update the ConsoleApp class and its search_books method to implement the following:
    
    1. Add access to the loaded books data (from JsonData or a book repository) in ConsoleApp.
    2. In search_books, use the user-supplied book title to search for a matching book in Books.json (case-insensitive, partial or exact match).
    3. If a book is found, use its ID to find all related book items (copies) and check Loans.json to see if any copies are currently on loan (ReturnDate is null).
    4. If at least one copy is available, display: "book.title is available for loan." If all are on loan, display: "book.title is on loan to another patron. The return due date is loan.DueDate."
    5. If no book matches, inform the user. If multiple books match, prompt for refinement or selection.
    6. After displaying the result, allow the user to search again or return to the previous menu.
        
    Please provide the complete implementation for this book search and availability feature.
    ```

### Copilot Chat の編集モードを使用して search_books メソッドを実装する

1. チャット ビューを編集モードに切り替えるには、**[モードの設定]** を選択し、**[編集]** を選択します。

    新しいセッションを開始するように求められたら、**[はい]** を選択します。

1. **Chat の編集モード**を使い、ドラッグ アンド ドロップ操作を使って次のファイルをチャット コンテキストに追加します。

    - library/console/**console_app.py**
    - library/console/**main.py**
    - library/infrastructure/**json_data.py**
    - library/infrastructure/Json/**Books.json**
    - library/infrastructure/Json/**Loans.json**
    - library/infrastructure/Json/**BookItems.json**

1. **console_app.py** から **search_books** メソッドを選びます。

1. 次のプロンプトを入力します。

    ```plaintext

    @workspace Update the ConsoleApp class so it can access the loaded books data from JsonData or a book repository, and update main.py to instantiate ConsoleApp with the loaded JsonData instance by passing json_data=json_data. In the search_books method, prompt the user for a book title and search for a matching book in Books.json using a case-insensitive, partial or exact match. If a book is found, use its ID to find all related book items (copies) and check Loans.json to determine if any copies are currently on loan (ReturnDate is null). If at least one copy is available, display a message stating the book is available for loan; if all are on loan, display a message with the book title and the due date of the loan. If no book matches, inform the user, and if multiple books match, prompt for refinement or selection. After displaying the result, allow the user to search again or return to the previous menu.
    ```

1. 少し時間を取って、console_app.py ファイルで提案された更新内容を確認してください。

    提案されたコード更新内容を移動するには、**[前へ]** と **[次へ]** を使用するか、ファイルを手動でスクロールすることができます。

    **console_app.py**

    **json_data** 依存関係を **console_app** コンストラクターに追加するコードの更新は、ConsoleApp クラスの先頭近くにあります。

    ```python
    class ConsoleApp:
        def **init**(
            self,
            loan_service: ILoanService,
            patron_service: IPatronService,
            patron_repository: IPatronRepository,
            loan_repository: ILoanRepository,
            json_data=None,  # <-- Add json_data for direct access to books/items
            book_repository=None  # <-- Optionally allow a book repo
        ):
            self._current_state: ConsoleState = ConsoleState.PATRON_SEARCH
            self.matching_patrons = []
            self.selected_patron_details = None
            self.selected_loan_details = None
            self._patron_repository = patron_repository
            self._loan_repository = loan_repository
            self._loan_service = loan_service
            self._patron_service = patron_service
            self._json_data = json_data   # <-- store json_data
            self._book_repository = book_repository
    ```

    このコードは、`search_books` メソッドを完成させて、**json_data** を使ってタイトルで書籍を検索するかどうかを調べ、BookItem を取得し、アクティブな貸し出しを確認し、要求に応じて貸し出し可能または貸し出し状態を表示する機能をスタブ コードに実装します。

    ```python
        def search_books(self) -> ConsoleState:
        while True:
            book_title = input("Enter a book title to search for: ").strip()
            if not book_title:
                print("No book title provided. Please try again.")
                continue

            # Case-insensitive, partial or exact match
            books = self._json_data.books
            matches = [b for b in books if book_title.lower() in b.title.lower()]

            if not matches:
                print("No matching books found.")
                again = input("Search again? (y/n): ").strip().lower()
                if again == 'y':
                    continue
                else:
                    return ConsoleState.PATRON_DETAILS

            if len(matches) == 1:
                book = matches[0]
            else:
                print("\nMultiple books found:")
                for idx, b in enumerate(matches, 1):
                    print(f"{idx}) {b.title}")
                selection = input("Select a book by number or 'r' to refine search: ").strip().lower()
                if selection == 'r':
                    continue
                if not selection.isdigit() or not (1 <= int(selection) <= len(matches)):
                    print("Invalid selection.")
                    continue
                book = matches[int(selection) - 1]

            # Find all book items (copies) for this book
            book_items = [bi for bi in self._json_data.book_items if bi.book_id == book.id]
            if not book_items:
                print("No copies of this book are in the library.")
                again = input("Search again? (y/n): ").strip().lower()
                if again == 'y':
                    continue
                else:
                    return ConsoleState.PATRON_DETAILS

            # Find all loans for these book items
            loans = self._json_data.loans
            on_loan = []
            available = []
            for item in book_items:
                # Find latest loan for this item (if any)
                item_loans = [l for l in loans if l.book_item_id == item.id]
                if item_loans:
                    # Get the most recent loan (by LoanDate)
                    latest_loan = max(item_loans, key=lambda l: l.loan_date or l.due_date or l.return_date or 0)
                    if latest_loan.return_date is None:
                        on_loan.append(latest_loan)
                    else:
                        available.append(item)
                else:
                    available.append(item)

            if available:
                print(f"Book '{book.title}' is available for loan.")
            else:
                # All copies are on loan, show due dates
                due_dates = [l.due_date for l in on_loan if l.due_date]
                if due_dates:
                    next_due = min(due_dates)
                    print(f"All copies of '{book.title}' are currently on loan. Next due date: {next_due}")
                else:
                    print(f"All copies of '{book.title}' are currently on loan.")

            again = input("Search for another book? (y/n): ").strip().lower()
            if again == 'y':
                continue
            else:
                return ConsoleState.PATRON_DETAILS 
    ```

    **main.py**

    アプリが JSON データ ファイルにアクセスできるように、`json_data` を渡すよう `ConsoleApp` のインスタンス化を変更しました。

    ```python
    # ...existing code...
            app = ConsoleApp(
            loan_service=loan_service,
            patron_service=patron_service,
            patron_repository=patron_repo,
            loan_repository=loan_repo,
            json_data=json_data  # <-- Pass json_data so ConsoleApp can access books/items/loans
        )
    # ...existing code...
    
    ```

1. チャット ビューですべての編集内容を保持するには、**[保持する]** を選択します。

    更新を受け入れる前に、必ず GitHub Copilot の提案内容を確認します。

    提案された更新内容について不明な点がある場合は、変更を受け入れてから GitHub Copilot に説明を求めることができます。 更新を行わないことに決めた場合は、編集内容を元に戻すことができます。

    > **注**:特定のロケールまたは書式を使って戻される日付を書式設定するよう GitHub Copilot が提案した場合は、必要に応じて Python の datetime.strftime() メソッドを使って日付の書式を設定します。

1. console_app.py と main.py ファイルの両方で更新を受け入れたことを確認します。

1. Visual Studio Code のエクスプローラー ビューを開きます。

1. テストを実行するか、アプリケーションを開始し、コードの更新によってエラーが発生していないことを確認します。

    警告メッセージは表示されるかもしれませんが、エラーは発生しないはずです。

    テストを実行するには、Visual Studio Code でターミナルを開き、AccelerateDevGHCopilot/library ディレクトリに移動して以下を実行します。

    ```bash
    python -m unittest discover tests
    ```

## "書籍貸出状況" の更新をリポジトリのメイン ブランチにマージする

コードをリポジトリのメイン ブランチにマージする前にテストすることが重要です。 テストにより、コードが意図したとおりに動作し、新しい問題が発生していないことを確認します。 この演習では、手動テストを使って、"書籍貸出状況" 機能が意図したとおりに機能することを確認します。

演習のこのセクションでは、次のタスクを完了します。

1. "書籍貸出状況" 機能をテストします。
1. 変更をリモート リポジトリと同期します。
1. 変更をリポジトリのメイン ブランチにマージする pull request を作成します。

### "書籍貸出状況" 機能をテストする

手動テストを使って、新しい機能が意図したとおりに機能することを確認できます。 検証できるデータ ソースを使用することが重要です。 この場合は、**Books.json** と **Loans.json** ファイルを使って、新しい機能が書籍の貸出状態を正しく報告することを確認します。

以下の手順に従って、演習のこのセクションを完了します。

1. アプリケーションを実行するには、Visual Studio Code でターミナルを開き、`AccelerateDevGHCopilot/library` ディレクトリに移動して、以下を実行します。

```bash
python console/main.py
```

1. 利用者名の入力を求められたら、「**One**」と入力して Enter キーを押します。

    検索クエリに一致する利用者の一覧が表示されます。

1. [Input Options] プロンプトで、「**2**」と入力して ([Patron One] を選択)、Enter キーを押します。

    - 「**2**」と入力すると、リストにある 2 番目の利用者が選択されます。
    - 利用者の名前とメンバーシップの状態が表示され、その後に書籍貸出の詳細が表示されます。

1. [Book One] を選択するには ([Returned: False] と示されます)、「**1**」を入力します。

   - 次のような状態であることに注意してください: **Returned: False**
   - または、書籍が次のような状態である利用者を見つけます: **"Returned: False"**。 書籍の名前を書き留めます。

1. 書籍を返却するには、「**r**」と入力します

    - 状態が次のように更新されることに注意してください: **Returned: True**

1. "Patron One" の書籍をもう一度確認します。

    - 利用者を検索するには、「**s**」と入力します
    - **Patron One** に対して**利用者検索**の手順を繰り返します。
    - [Book One] を選択するには ([Returned: True] と示されます)、次のように入力します: **1**

    書籍のタイトルは表示されますが、貸出状況の情報はないことに注意してください。

1. 書籍の貸出状況を調べて、貸し出し可能な書籍を借ります。

    - 書籍の貸出状況を調べるには、「**b**」と入力します
    - 貸し出し可能な書籍を検索するには、次のように入力します: **One**

    書籍のタイトルが表示され、貸出状況が "Book One は貸し出し可能です" と示されることに注意してください。 また、"Book One" を借りるオプションがないことにも注意してください。

1. 次のように尋ねられます: "別の書籍を検索しますか?  (y/n)"。「**n**」と入力します

1. 終了するには「q」と入力してアプリケーションを停止します。

**アプリケーションに問題があります**。 書籍の貸出状況は報告されていますが、書籍を借りるオプションがありません。

### エージェント モードで書籍借り出しのバグを修正する

Copilot は AI ツールであり、人と同様に間違う可能性があるため、結果を確認する必要があります。 このバグを修正するには、エージェント モードを使います。

1. Github Copilot Chat を開き、**[エージェント モード]** を選びます。
1. ターミナルからチャットに手動テストを追加するには:

    - チャット ウィンドウで、Copilot Chat の **[コンテキストの追加]** を選びます (エージェント モードであることを確認します)
    - 「ツール」と入力してから「ツール」と入力し、マウスまたは Enter キーで選択します。
    - 「terminalSelection」と入力し、"**terminalLastCommand** ターミナルの最後の実行コマンド" を選びます

1. VSCode のエディターに一覧表示されているファイルを開いて、チャットの内容にファイルを追加します。 次に、Copilot チャット ウィンドウで **[コンテキストの追加]** を選んで、**[エディターを開く]** を選びます。

    - **console_app.py** (アプリケーションのメイン ロジックとユーザー操作を含みます)
    - **loan_service.py** (貸し出しのロジック)
    - **json_data.py** (書籍の読み込み/保存、書籍項目、利用者、著者、貸し出しを処理します)
    - **json_loan_repository.py** (貸し出し記録の取得、追加、更新を処理します)
    - **Books.json** (サンプル ブック データ)
    - **BookItems.json** (サンプル書籍項目/データのコピー)
    - **Patrons.json** (サンプル利用者データ)
    - **Loans.json** (サンプル貸し出し/借り出しデータ)
    - **Authors.json** (サンプル著者データ)

1. レビューし、バグに対処するプロンプトを入力します (Copilot Chat に **[エージェント]** と表示されていることを確認します)。

    ```plaintext
    @workspace My goal is to make it possible for a patron to check out an available book directly from the console app menu. Right now, when I search for a book and it is available, the app only tells me "...is available for loan" but doesn’t let me check it out. Please help me update the code so that when a book is available, the app prompts me to check it out, and if I choose yes, it creates a new loan for the current patron and book. Make sure the checkout works smoothly in the menu flow. For the loan_service add checkout functionality and ensure the console app calls this method. Only use the existing enum and menu option names as defined in the codebase. Only use enum values and menu option names that are already defined in the codebase. Do not invent, rename, or add new enum values or menu options.
    ```

    エージェントが複数のファイルの特定の行を読み取ることがわかります。 更新を行ってからそれを確認し、さらに更新を行います。

1. **console-app.py** の `search_books` メソッドの末尾でエージェント モードの更新を確認します。これは次のコードのようになっているはずです。

    ```python
    def search_books(self) -> ConsoleState:
    # ...existing code...

           if available:
                print(f"Book '{book.title}' is available for loan.")
                # Prompt to check out
                checkout = input("Would you like to check out this book? (y/n): ").strip().lower()
                if checkout == 'y':
                    if not self.selected_patron_details:
                        print("No patron selected. Please select a patron first.")
                        return ConsoleState.PATRON_SEARCH
                    # Use the first available copy
                    book_item = available[0]
                    # Create a new loan using the loan service
                    status = self._loan_service.create_loan(
                        patron_id=self.selected_patron_details.id,
                        book_item_id=book_item.id
                    )
                    print(status)
                    # Reload loans to reflect the new loan
                    if hasattr(self._json_data, 'load_data'):
                        self._json_data.load_data()
                    print(f"Book '{book.title}' checked out to {self.selected_patron_details.name}.")
                    return ConsoleState.PATRON_DETAILS
        # ...existing code...
    ```

1. **loan_service.create_loan** に対する更新を確認します。

```python
    def create_loan(self, patron_id: int, book_item_id: int):
        """Create a new loan for the given patron and book item, and add it to the repository."""
        from ..entities.loan import Loan
        from datetime import datetime, timedelta
        # Check if the book item is already on loan
        all_loans = getattr(self._loan_repository, 'get_all_loans', lambda: [])()
        for loan in all_loans:
            if loan.book_item_id == book_item_id and loan.return_date is None:
                return "This copy is already on loan."
        # Generate a new loan ID
        max_id = max((l.id for l in all_loans if hasattr(l, 'id')), default=0)
        new_id = max_id + 1
        now = datetime.now()
        due = now + timedelta(days=14)
        new_loan = Loan(
            id=new_id,
            book_item_id=book_item_id,
            patron_id=patron_id,
            loan_date=now,
            due_date=due,
            return_date=None
        )
        self._loan_repository.add_loan(new_loan)
        return f"Loan created. Due date: {due.date()}"

```

1. ConsoleApp.search_booksと LoanService の更新に注意してください。

    search_books と loan_service メソッドが更新され、書籍が貸し出し可能なときは、アプリでユーザーにその借り出しを促すようになります。ユーザーが "はい" を選ぶと、現在の利用者と選ばれた書籍項目に対して新しい貸し出しが作成され、メニュー フローが利用者の詳細に戻ります。 借り出しプロセスがメニュー フローにスムーズに統合されるようになりました。

1. 変更を受け入れます。

1. 前のテスト手順をもう一度テストし、**書籍の借り出しを完了して**修正が正しいことを確認します。 問題が解決しない場合は、必要に応じてエージェントまたは質問モードでのトラブルシューティングを続けます。

### 変更をリモート リポジトリと同期する

1. [ソース管理] ビューを選択します。

1. 更新したファイルが **[変更]** の一覧に表示されていることを確認します。

    **[変更]** の下に common_actions.py と console_app.py ファイルが表示されます。 main.py ファイルも一覧に表示される場合があります。

1. GitHub Copilot を使用して、**[コミット]** のメッセージを生成します。

    ![[Copilot を使用してコミット メッセージを生成する] ボタンを示すスクリーンショット。](./Media/m03-github-copilot-commit-message-python.png)

1. 変更をステージングしてコミットするには、**[コミット]** を選択してから、**[はい]** を選択します。

1. 変更をリモート リポジトリに同期するには、**[変更の同期]** を選びます。

### 変更をメイン ブランチにマージする pull request を作成する

図書館員が書籍の貸出状況を判断できるようにする機能を実装しました。 ここで、変更をリポジトリのメイン ブランチにマージする必要があります。 変更をメイン ブランチにマージする pull request を作成できます。

以下の手順に従って、演習のこのセクションを完了します。

1. Web ブラウザーで GitHub リポジトリを開きます。

    Visual Studio Code から GitHub リポジトリを開くには:

    1. Visual Studio Code ウィンドウの左下隅で、**book-availability** を選択します。
    1. コンテキスト メニューで、**book-availability** ブランチの右側にある **[GitHub で開く]** アイコンを選択します。

1. GitHub リポジトリ ページで、**[Compare & pull request]** タブを選びます。

1. **[Base]** で **main** が指定され、**[compare]** で **book-availability** が指定され、**[Able to merge]** がオンになっていることを確認します。

1. **[Add a description]** の下で [Copilot Actions] ボタン (GitHub Copilot アイコン) を選んで、要約を生成するオプションを選びます。

    > **注**:現在、GitHub Copilot Free プランでは pull request の要約機能はサポートされていません。

    GitHub Copilot Free プランをお使いの場合は、自分で要約を記述することも、下の要約を使って pull request を完成させることもできます。

    ```plaintext

    This pull request introduces a new feature to the library console application: the ability to search for books and check their availability. It also includes updates to dependency injection and the CommonActions enumeration to support this functionality. Below are the most important changes grouped by theme.

    New Feature: Book Search

    Added a new SEARCH_BOOKS action to the CommonActions enumeration (library\console\common_actions.py).

    Updated PatronDetails method to handle the SEARCH_BOOKS action, including a new SEARCH_BOOKS method that allows users to search for a book by title and check its availability (library\console\console_app.py).

    Modified ReadInputOptions and WriteInputOptions methods to include the new SEARCH_BOOKS option (library/console/console_app.py).

    Dependency Injection Updates

    Added JsonData as a dependency in the ConsoleApp constructor and ensured it is registered in the DI container before ConsoleApp (library/console/console_app.py, library/console/main.py).

    ```

1. 要約が生成されたら、**[Preview]** を選びます。

1. 少し時間を取って、要約を確認してください。

    GitHub Copilot によって生成される pull request の要約は、次の例のようになるはずです。

    ![GitHub Copilot Enterprise アカウントを使用して生成された pull request の概要を示すスクリーンショット。](./Media/m03-github-copilot-pull-request-summary.png)

1. **[Pull request の作成]** を選びます。

1. すべてのチェックが合格し、ベース ブランチとの競合がない場合は、**[Merge pull request]** を選んでから、**[Confirm merge]** を選びます。

    変更をマージした後、**book-availability** ブランチを削除できることに注目してください。 ブランチを削除するには、**[Delete branch]** を選びます。

1. [Visual Studio Code] ウィンドウに切り替えます。

1. リポジトリの **main** ブランチに切り替えます。

1. ソース管理ビューを開き、リモート リポジトリから変更を**プル**します。

1. 書籍貸出状況機能が**メイン** ブランチで使用できることを確認します。

## まとめ

この演習では、GitHub Copilot を使って Python アプリケーションの新しいコード機能を開発する方法を学びました。 GitHub Copilot のインライン チャットとチャット ビューを使って新しいブランチで機能を開発し、コードをテストして、変更をリポジトリのメイン ブランチにマージしました。 また、GitHub Copilot を使って、コミット メッセージと pull request の要約も生成しました。

## クリーンアップ

演習が済んだので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに残しておきたくない変更を行っていないことを確認してください。 変更を行った場合は、ここでそれを元に戻します。
