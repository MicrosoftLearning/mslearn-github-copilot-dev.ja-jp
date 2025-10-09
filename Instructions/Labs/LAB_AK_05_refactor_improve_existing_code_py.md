---
lab:
  title: 演習 - GitHub Copilot を使用して既存のコードをリファクターする (Python)
  description: GitHub Copilot in Visual Studio Code を使用して既存のコード セクションをリファクターし、改善する方法について説明します。
---

# GitHub Copilot を使用して既存のコードをリファクターする

GitHub Copilot を使用すると、コードベース全体を評価し、コードのリファクターと改善に役立つ更新の提案を受けることができます。 この演習では、GitHub Copilot を使用し、Python アプリケーションの指定したセクションをリファクターして、同時にコードの品質、信頼性、パフォーマンス、セキュリティを向上させます。

この演習の所要時間は約 **30** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://go.microsoft.com/fwlink/?linkid=2320148" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

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

図書館アプリケーションの初期バージョンをレビューのために提出しました。 レビュー チームが、コードの品質、パフォーマンス、読みやすさ、保守容易性、セキュリティを向上させる機会を特定しました。

この演習には、次のタスクが含まれています。

1. Visual Studio Code で図書館アプリケーションを設定します。
1. 質問モードと編集モードでチャット ビューを使用してコードを分析し、リファクターします。
1. インライン チャットと、編集およびエージェント モードのチャット ビューを使用してコードをリファクターします。

## Visual Studio Code で図書館アプリケーションを設定する

既存のアプリケーションをダウンロードし、コード ファイルを展開して、Visual Studio Code でプロジェクトを開く必要があります。

図書館アプリケーションを設定するには、次の手順に従います。

1. ラボ環境でブラウザー ウィンドウを開きます。

1. 図書館アプリケーションを含む ZIP ファイルをダウンロードするには、次の URL をブラウザーのアドレス バーに貼り付けます。[GitHub Copilot ラボ - 既存のコードをリファクターする](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/AZ2007LabAppM5Python.zip)

    ZIP ファイルの名前は **AZ2007LabAppM5Python.zip** です。

1. **AZ2007LabAppM5Python.zip** ファイルからファイルを展開します。

    次に例を示します。

    1. ラボ環境のダウンロード フォルダーに移動します。

    1. **AZ2007LabAppM5Python.zip** を右クリックし、**[すべて展開]** を選択します。

    1. **[完了時に展開されたファイルを表示する]** を選んでから、**[展開]** を選びます。

1. 展開されたファイル フォルダーを開き、**AccelerateDevGHCopilot** フォルダーを Windows デスクトップ フォルダーなどのアクセスしやすい場所にコピーします。

1. Visual Studio Code で **AccelerateDevGHCopilot** フォルダーを開きます。

    次に例を示します。

    1. ラボ環境で Visual Studio Code を開きます。

    1. Visual Studio Code の **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。

    1. Windows デスクトップ フォルダーに移動し、**AccelerateDevGHCopilot** を選択してから、**[フォルダーの選択]** を選択します。

1. Visual Studio Code のエクスプローラー ビューで、次のプロジェクト構造を確認します。

    - AccelerateDevGHCopilot/library   ├── application_core   ├── console   ├── infrastructure   └── tests

1. 次のセクションでテストを実行して、初期コードが正常に実行されることを確認します。 前のラボに慣れている場合は、必要に応じてターミナルの **\library** フォルダーから **`python console\main.py`** を使用してアプリケーションを実行します。

### Pytest を有効にする

Unittest と比較して、Pytest には、簡潔な構文、フィクスチャやパラメーター化などの機能、より優れた障害レポートなどの利点があります。 Pytest を使用するとテストの作成と保守が簡単になります。また、Pytest によって Unittest テスト ケースが実行されます。

1. Pytest は、<a href="https://marketplace.visualstudio.com/items?itemName=ms-python.python" target="_blank">Visual Studio Microsoft Python 拡張機能</a>のインストールから有効になります。必要に応じてインストールしてください。

1. テストが検出されたら、ツール バーのフラスコ アイコン  ![テストのフラスコ アイコンを示すスクリーンショット。](./Media/m04-pytest-flask-py.png) を選択します。 アイコンが表示されない場合は、前の手順を確認してください

1. [Python テストの構成] を選択するか、以前に構成した場合は次を行います。

1. テストが構成されていないか、正しいプロジェクトをテストしている場合は、次の手順に進みます。 テスト プロジェクトを変更する必要がある場合は、次の手順に進みます。
    - `Ctrl+Shift+P` によりコマンド パレットを開きます。
    - 「**Python:Configure Tests**」と入力します。
    - [pytest] を選択します。
    - python コードのディレクトリを選択します。
    - 再生アイコンを選択してテストを実行します。

1. オプションから [Pytest] を選択します。

1. テスト コードを含むフォルダー (`library\`) を選択します。

1. 再生アイコンを選択してテストを実行します。
    ![pytest の結果を示すスクリーンショット。](./Media/m04-pytest-configure-results-py.png)

1. 必要に応じて、ターミナルで `library` パスから ptytest コマンドを実行します。

    ```plaintext
    pytest -v
    ```

## 質問および編集モードのチャット ビューを使用してコードを分析し、リファクターする

リフレクションは、実行時にオブジェクトを検査および操作できる強力なコーディング機能です。 ただし、リフレクションには時間がかかる可能性があり、リフレクションに関連する潜在的なセキュリティ リスクを考慮する必要があります。

以下を実行する必要があります。

1. ワークスペースを分析して、割り当てられたタスクに対処する方法を調査します。
1. Python Enum の使用を enum_helper クラスにリファクターして、リフレクションと Python 組み込み Enum ではなく静的ディクショナリを使用します。

### 質問モードのチャット ビューを使用して列挙型の使用状況を分析する

GitHub Copilot のチャット ビューには 3 つのモードがあります。**[質問]**、**[編集]**、**[エージェント]** です。 各モードは、GitHub Copilot のさまざまな操作のために設計されています。

- **質問**:このモードを使用して、コードベースについて GitHub Copilot に質問します。 コードの説明、変更の提案、コードベースに関する情報の提供を GitHub Copilot に依頼できます。
- **編集**:選択したコード ファイルを編集するには、このモードを使用します。 GitHub Copilot を使用して、コードをリファクターし、コメントを追加し、コードにその他の変更を加えることができます。
- **エージェント**:このモードを使用して、GitHub Copilot をエージェントとして実行します。 GitHub Copilot を使用して、コマンドの実行、コードの実行、またはその他の各種タスクをワークスペース内で実行することができます。

演習のこのセクションでは、**質問**モードのチャット ビューを使用して、コーディングの割り当てを分析します。

以下の手順に従って、演習のこのセクションを完了します。

1. エクスプローラー ビューで、**library** フォルダーを展開します。

1. GitHub Copilot Chat ビューを開きます。

    チャット ビューには、GitHub Copilot と対話するための管理された会話型インターフェイスが用意されています。

    Visual Studio Code ウィンドウ上部の検索テキストボックスのすぐ右側にある **[チャットの切り替え]** ボタンを使用して、チャット ビューのオープンとクローズを切り替えることができます。

    ![Copilot の [チャットの切り替え] ボタンを示すスクリーンショット。](./Media/m01-github-copilot-toggle-chat.png)

    キーボード ショートカット **Ctrl + Alt + I** を使用してチャット ビューを切り替えることもできます。

1. チャット ビューは既定では**質問**モードで開かれることに注意してください。

    現在のチャット モードは、チャット ビューの右下隅の近くに表示されます。 チャット応答がチャット ビューに表示されるのは、**質問**モードで作業しているときです。

1. 次のプロンプトを確認してから送信します。

    ```plaintext
    @workspace Provide an explanation of how the current Python code can be improved with code refactoring to:
    - avoid reflection
    - avoid repeated `if/elif`
    - make the code more explicit and efficient.
    Then provide suggestion for refactoring this Python project to implement the improvement suggestions.
    ```

1. 少し時間を取って、次のようなコード提案を削除した応答を確認しましょう。

    >**注**: まったく異なる応答になる場合もありますが、似たテーマが表示されるはずです。

    -----

    >### コードの改善の説明
    >
    > 現在のコードを改善するには、次のリファクタリング戦略を検討してください。
    >
    > 1. **リフレクションを避ける**:  
    >    リフレクションは遅くなり、エラーが発生しやすくなります。 代わりに、明示的なメソッド呼び出し、インターフェイス、またはディクショナリを使用して、アクションを関数にマップします。 こうすることで、コードの保守性とパフォーマンスが向上します。
    >
    > 2. **反復的な `if/elif` チェーンを避ける**:  
    >    長い `if/elif` チェーンは可読性と保守性が低下します。 これらをディクショナリ (Python) または switch 式 (C# 8 以降) に置き換えるか、ポリモーフィズム (オブジェクト指向設計) を使用して動作を委任します。
    >
    > 3. **コードをより明示的かつ効率的にする**:  
    >    明示的なコードは理解しやすく、デバッグも容易です。 明確な変数名を使用し、マジック文字列や数字を避け、意図が明確になるようにロジックを構築します。 多くの場合、不要なチェックや検索を減らすことで効率が向上します。
    >
    >#### リファクターの提案
    >
    > 次のようなコードがあるとします (Python の例):
    >
    > ```python
    > # ...existing code...
    > def handle_action(action, data):
    >     if action == "create":
    >         return create_item(data)
    >     elif action == "update":
    >         return update_item(data)
    >     elif action == "delete":
    >         return delete_item(data)
    >     else:
    >         raise ValueError("Unknown action")
    > # ...existing code...
    > ```
    >
    > **リファクター済みバージョン (リフレクションと if/elif を避け、より明示的にする)**
    >
    > ```python
    > # ...existing code...
    > def handle_action(action, data):
    >     action_map = {
    >         "create": create_item,
    >         "update": update_item,
    >         "delete": delete_item,
    >     }
    >     try:
    >         return action_map[action](data)
    >     except KeyError:
    >         raise ValueError(f"Unknown action: {action}")
    > # ...existing code...
    > ```
    >
    > **メリット:**
    >
    > - リフレクションは使用されていません。
    > - 反復的な `if/elif` はありません。
    > - マッピングは明示的であり、拡張が容易です。
    > - 関数を直接参照しているため、より効率的です。
    >
    >### まとめ
    >
    > - リフレクションや長い `if/elif` チェーンではなく、ディクショナリまたはスイッチ式を使用します。
    > - 保守性と効率性を高めるためにマッピングを明示的にします。
    > - 不明なケースには明確なエラー処理を使用します。
    >
    > このような変更により、コードベースの保守が容易になり、効率が向上し、エラーが発生しにくくなります。

1. 次のプロンプトを確認してから送信します。

    ```plaintext
    @workspace which Python files in this workspace use reflection or long `if/elif` chains?
    ```

    応答には **console/console_app.py**  (`getattr`) が示され、**console/console_app.py** でコマンドと入力の処理に長い `if/elif` チェーンが使用されていることも示されます。

1. **console/console_app.py** ファイルをチャット コンテキストに追加します。

    ドラッグ アンド ドロップ操作を使用して、Visual Studio Code のエクスプローラー ビューからチャット ビューにファイルを追加できます。 チャット ビューの **[コンテキストの追加]** ボタンを使用して、ファイルやその他のリソースを追加することもできます。

    > **注**:チャット コンテキストにファイルを追加すると、GitHub Copilot は応答を生成するときにそれらのファイルを考慮します。 プロンプトに関連付けられているコンテキストを GitHub Copilot が理解すると、応答の関連性と正確性が向上します。

1. 次のプロンプトを確認してから送信します。

    ```plaintext

    @workspace I need to refactor the `library\console\console_app.py` Python file to: Use dictionaries or switch expressions instead of reflection or long `if/elif` chains; Make mappings explicit for maintainability and efficiency; Use clear error handling for unknown cases; Provide refactored code to apply.

    ```

    プロンプトを記述するときは、明確さとコンテキストが重要です。 チャット参加者、スラッシュ コマンド、チャット変数を使用すると、GitHub Copilot が理解できる方法でコンテキストを定義できます。

    GitHub Copilot に問題の解決方法を尋ねるプロンプトの場合は、解決しようとしている問題から始めます。 簡潔な文を使用して、詳細を記述し、制約を指定し、リソースを識別します。 最後に、応答に含める内容を必ず GitHub Copilot に指示します。

    このケースでは、プロンプトの最初に問題/目標の説明を入力します。 `library\console\console_app.py` ファイルを次のようにリファクターする必要があることを GitHub Copilot に伝えます。

    - リフレクションや長い `if/elif` チェーンではなく、ディクショナリまたはスイッチ式を使用します。
    - 保守性と効率性を高めるためにマッピングを明示的にします。
    - 不明なケースには明確なエラー処理を使用します。

    わかりやすくするために、プロンプトの最後に、「適用するリファクター済みコードを提供してください」という指示を入力します。

1. 少し時間を取って、GitHub Copilot から提供された応答を確認しましょう。 この手順では**編集を適用しません**。

    > ### コードの改善の説明
    >
    > 現在のコードを改善するには、次のリファクタリング戦略を検討してください。
    >
    > 1. **リフレクションを避ける**:  
    >    リフレクションは遅くなり、エラーが発生しやすくなります。 代わりに、明示的なメソッド呼び出し、インターフェイス、またはディクショナリを使用して、アクションを関数にマップします。 こうすることで、コードの保守性とパフォーマンスが向上します。
    >
    > 2. **反復的な `if/elseif` チェーンを避ける**:  
    >    長い `if/elseif` チェーンは可読性と保守性が低下します。 これらをディクショナリ (Python) または switch 式 (C# 8 以降) に置き換えるか、ポリモーフィズム (オブジェクト指向設計) を使用して動作を委任します。
    >
    > 3. **コードをより明示的かつ効率的にする**:  
    >    明示的なコードは理解しやすく、デバッグも容易です。 明確な変数名を使用し、マジック文字列や数字を避け、意図が明確になるようにロジックを構築します。 多くの場合、不要なチェックや検索を減らすことで効率が向上します。
    >
    >
    > ### リファクターの提案
    >
    > 次のようなコードがあるとします (Python の例):
    >
    > ```python
    > # ...existing code...
    >    def _handle_patron_details_selection(self, selection, patron, valid_loans):
    >        if selection == 'q':
    >            return ConsoleState.QUIT
    >        elif selection == 's':
    >            return ConsoleState.PATRON_SEARCH
    >        elif selection == 'm':
    >            status = self._patron_service.renew_membership(patron.id)
    >            print(status)
    >            self.selected_patron_details = self._patron_repository.get_patron(patron.id)
    >            return ConsoleState.PATRON_DETAILS
    > # ...existing code...
    > ```
    >
    > #### リファクター済みバージョン (リフレクションと if/elif を避け、より明示的にする)
    >
    > ```python
    > # ...existing code...
    >    def _handle_patron_details_selection(self, selection, patron, valid_loans):
    >        def renew_membership():
    >           status = self._patron_service.renew_membership(patron.id)
    >           print(status)
    >           self.selected_patron_details = self._patron_repository.get_patron(patron.id)
    >           return ConsoleState.PATRON_DETAILS
    > # ...existing code...
    > ```
    >
    > **メリット:**
    >
    > - リフレクションは使用されていません。
    > - 反復的な `if/elif` はありません。
    > - マッピングは明示的であり、拡張が容易です。
    > - 関数を直接参照しているため、より効率的です。
    >
    >
    > ### まとめ
    >
    > - リフレクションや長い `if/elseif` チェーンではなく、ディクショナリまたはスイッチ式を使用します。
    > - 保守性と効率性を高めるためにマッピングを明示的にします。
    > - 不明なケースには明確なエラー処理を使用します。
    >
    > このような変更により、コードベースの保守が容易になり、効率が向上し、エラーが発生しにくくなります。

1. チャット ビューで、応答に含まれるコード サンプルの上にマウス ポインターを置きます。

1. コード スニペットの右上隅に表示される 3 つのボタンに注目してください。

1. 各ボタンの上にマウス ポインターを置くと、アクションを説明するヒントが表示されます。

    最初の 2 つのボタンを使用すると、エディターにコードがコピーされます。 3 つ目のボタンを使用すると、クリップボードにコードがコピーされます。 この手順では**編集を適用しません**。

> **注**:質問モードを使用してコードを更新できます。 ただし、編集モードでは、コード エディター内で直接コードがリファクターされ、更新内容を受け入れるためのその他のオプションが提供されます。

### 編集モードのチャット ビューを使用して、クラス ConsoleApp (console/console_app.py) ファイルをリファクターする

チャット ビューの編集モードは、ワークスペース内でコードを編集するために設計されています。 編集モードを選択して、コードをリファクターし、コメントを追加し、その他のコード変更を行うことができます。

1. チャット ビューで **[モードの設定]** を選択し、**[編集]** を選択します。

    編集モードで新しいセッションを開始するように求められたら、**[はい]** を選択します。

    **編集**モードでは、GitHub Copilot の応答はコード エディター内にコード更新の提案として表示されます。 一般に編集モードが使用されるのは、新機能の実装、バグの修正またはコードのリファクタリング時です。

1. console/console_app.py ファイルをチャット コンテキストに追加します。

1. 次のプロンプトを確認してから送信します。

    ```plaintext

    @codebase I need to refactor the ConsoleApp class. Use static dictionaries to supply enum description attributes. Use dictionaries or switch expressions instead of reflection or long `if/elif` chains. Make mappings explicit for maintainability and efficiency. Use clear error handling for unknown cases.

    ```

    編集モード エージェントの応答では、`ConsoleApp` クラスの更新が提案され、次のような応答が返されます。

    ```plaintext
    The ConsoleApp class has been refactored to use static dictionaries for enum descriptions, explicit dictionaries for state and input handling, and clear error handling for unknown cases, improving maintainability and efficiency.
    ```

1. 少し時間を取って、**console_app.py** で提案されたコードの更新を確認しましょう。 結果は以下を満たしているはずです。

    - リフレクションと長い `if/elif` チェーンを置き換えるために、列挙型の説明 (`CONSOLE_STATE_DESCRIPTIONS` と `COMMON_ACTIONS_DESCRIPTIONS`) の静的ディクショナリを導入しました。
    - 入力オプションを表示するために静的ディクショナリを使用するように `write_input_options` をリファクターしました。
    - 保守性と効率性を高めるため、`run` のメイン状態ループをディクショナリベースのハンドラー検索に置き換えました。
    - 不明な状態と次の状態に対する明示的なエラー処理を追加しました。
    - 入力ハンドラー内のすべてのアクション マッピングは、明瞭性と保守性のために明示的なディクショナリになりました。

1. チャット ビューで、すべての更新を受け入れるには、**[保持する]** を選択します。

    コード エディター タブの下部付近にある [チャット編集] ツール バーを使用して、コードの更新内容を承諾または拒否することもできます。

1. 少し時間を取って、更新された **run** メソッドを確認しましょう。

    GitHub Copilot によって **run** メソッドは更新され、長い if/elif チェーンは、ConsoleState を対応するハンドラー関数にマップする state_handlers ディクショナリへと置き換えられているはずです。 更新されたメソッドは、次のいずれかのようになります。

    ```python

    def run(self) -> None:
        state_handlers = {
            ConsoleState.PATRON_SEARCH: self.patron_search,
            ConsoleState.PATRON_SEARCH_RESULTS: self.patron_search_results,
            ConsoleState.PATRON_DETAILS: self.patron_details,
            ConsoleState.LOAN_DETAILS: self.loan_details,
            ConsoleState.QUIT: lambda: ConsoleState.QUIT
        }
        while True:
            handler = state_handlers.get(self._current_state)
            if handler is None:
                print(f"Unknown state: {self._current_state}")
                break
            next_state = handler()
            if next_state == ConsoleState.QUIT:
                print("Exiting application.")
                break
            if next_state not in state_handlers:
                print(f"Unknown next state: {next_state}")
                break
            self._current_state = next_state
    ```

    このコードでは、各 `ConsoleState` を対応するハンドラー関数にマップする `state_handlers` ディクショナリを使用しています。 ディクショナリからハンドラーを取得し、不明な状態に対してはエラーを発生させ、ハンドラーの戻り値に基づいて現在の状態を更新するようになりました。

1. Pytest を実行し、手動でテストして、エラーが発生しないことを確認してください。

    この演習の開始時に表示されたのと同じ警告が表示されますが、エラー メッセージは表示されないはずです。

## インライン チャットと、編集およびエージェント モードのチャット ビューを使用してコードをリファクターします

Python の**リスト内包表記**、**ジェネレーター式**、**組み込み関数** (`any()`、`all()`、`sum()`、`map()`、`filter()`、`sorted()` など) を採用することで、コードベースはより簡潔になり、表現力と効率性が向上して、手動による反復処理やデータ処理に伴う定型コードや潜在的なエラーを減らすことができます。

演習のこのセクションには次のタスクが含まれています。

- **インライン チャット: ジェネレーター式のリファクタリング**
- **編集モード チャット: リストのリスト内包表記と Python の組み込みメソッドのリファクタリング**
- **エージェント モード: 組み込み関数のリファクタリング**

### インライン チャットを使用したジェネレーター式のリファクタリング

1. エクスプローラー ビューで **infrastructure/json_patron_repository.py** プロジェクトを展開し、**json_loan_repository.py** ファイルを開いて、**`JsonLoanRepository` クラス**を確認します。

1. **`JsonLoanRepository` クラス**を選択するには、クラス全体を選択します。

    ```python

    class JsonPatronRepository(IPatronRepository):
        def __init__(self, json_data: JsonData):
            self._json_data = json_data
    
        def get_patron(self, patron_id: int) -> Optional[Patron]:
            for patron in self._json_data.patrons:
                if patron.id == patron_id:
                    return patron
            return None
    
        def search_patrons(self, search_input: str) -> List[Patron]:
            results = []
            for p in self._json_data.patrons:
                if search_input.lower() in p.name.lower():
                    results.append(p)
            n = len(results)
            for i in range(n):
                for j in range(0, n - i - 1):
                    if results[j].name > results[j + 1].name:
                        results[j], results[j + 1] = results[j + 1], results[j]
            return results
    
        def update_patron(self, patron: Patron) -> None:
            for idx in range(len(self._json_data.patrons)):
                if self._json_data.patrons[idx].id == patron.id:
                    self._json_data.patrons[idx] = patron
                    self._json_data.save_patrons(self._json_data.patrons)
                    return
    
        def add_patron(self, patron: Patron) -> None:
            self._json_data.patrons.append(patron)
            self._json_data.save_patrons(self._json_data.patrons)
            self._json_data.load_data()
    
        def get_all_patrons(self) -> List[Patron]:
            return self._json_data.patrons
    
        def find_patrons_by_name(self, name: str) -> List[Patron]:
            result = []
            for patron in self._json_data.patrons:
                if patron.name.lower() == name.lower():
                    result.append(patron)
            return result
    
        def get_all_books(self):
            return self._json_data.books
    
        def get_all_book_items(self):
            return self._json_data.book_items

    ```

1. インライン Copilot Chat を開き、メソッドをリファクターするプロンプトを入力します。

    ```plaintext
    #selection Refactor any manual aggregation or search over loans in this method to use generator expressions with built-in functions like any(), all(), sum(), or max() for improved readability and performance.
    ```

1. 少し時間を取って、おすすめの更新を確認します。

    提案された更新は次のコードのようになります。

    ```python
    # --code continues-- 
    def get_patron(self, patron_id: int) -> Optional[Patron]:
        return next((patron for patron in self._json_data.patrons if patron.id == patron_id), None)

    def search_patrons(self, search_input: str) -> List[Patron]:
        results = [p for p in self._json_data.patrons if search_input.lower() in p.name.lower()]
        results.sort(key=lambda p: p.name)
        return results

    def update_patron(self, patron: Patron) -> None:
        for idx, existing_patron in enumerate(self._json_data.patrons):
            if existing_patron.id == patron.id:
                self._json_data.patrons[idx] = patron
                self._json_data.save_patrons(self._json_data.patrons)
                return

    # --code continues--    
    def find_patrons_by_name(self, name: str) -> List[Patron]:
        return [patron for patron in self._json_data.patrons if patron.name.lower() == name.lower()]
    # --code continues-- 
    ```

1. おすすめの更新を受け入れるには、**[同意する]** を選択します。

#### **インライン チャットの [説明] スマート アクション**を使用してメソッドを確認する

1. `search_patrons` メソッドの説明を表示するには、**[説明]** スマート アクションを使用します。

1. `search_patrons` メソッドから (コメントの後にある) 次のコード行を選択します。

    ```python
    # def search_patrons(self, search_input: str) -> List[Patron]:
        results = [p for p in self._json_data.patrons if search_input.lower() in p.name.lower()]
        results.sort(key=lambda p: p.name)
    ```

    **[説明]** スマート アクションを開くには、エディターで `search_patrons` メソッド コードを選択し、選択したコードを右クリックして、[Copilot] を選択してから **[説明]** を選択します。 **[説明]** スマート アクションによって、選択したコードの詳しい説明が提供されます。

    たとえば、説明は次のようになります。

    ```plaintext

    The `search_patrons` method is responsible for finding patrons whose names contain a given search string, regardless of case. It takes a single argument, `search_input`, which is the string to search for. The method iterates over all patrons stored in `self._json_data.patrons` and uses a list comprehension to filter those whose `name` attribute contains the `search_input` substring. Both the patron's name and the search input are converted to lowercase to ensure the search is case-insensitive.
    
    After collecting the matching patrons, the method sorts the results alphabetically by the patron's name using the `sort()` method with a key function that extracts the `name` attribute from each patron. Finally, the sorted list of matching patrons is returned. This ensures that users receive a case-insensitive, alphabetically ordered list of patrons matching their search criteria.
    
    A potential consideration is that the search will match any part of the name, not just the beginning, which may lead to more results than expected. Also, the sorting is done in-place on a new list,

    ```

1. プロジェクトをテストして実行し、エラーが発生しないことを確認します。

#### InlineChat を使用したジェネレーター式のリファクタリングを続行する

1. 次に、**loan_service.py** のリファクタリング プロセスを続けます。

1. エクスプローラー ビューで **application_core\services\loan_service.py** プロジェクトを展開し、**loan_service.py** ファイルを開いて、**`LoanService` クラス**を確認します。

1. **`LoanService` クラス**を選択するには、クラス全体を選択し、`checkout_book` メソッドを確認します。

    ```python

    class LoanService(ILoanService):
        EXTEND_BY_DAYS = 14
    
        def __init__(self, loan_repository: ILoanRepository):
            self._loan_repository = loan_repository
    
        def return_loan(self, loan_id: int) -> LoanReturnStatus:
            loan = self._loan_repository.get_loan(loan_id)
            if loan is None:
                return LoanReturnStatus.LOAN_NOT_FOUND
            if loan.return_date is not None:
                return LoanReturnStatus.ALREADY_RETURNED
            loan.return_date = datetime.now()
            try:
                self._loan_repository.update_loan(loan)
                return LoanReturnStatus.SUCCESS
            except Exception:
                return LoanReturnStatus.ERROR
    
        def extend_loan(self, loan_id: int) -> LoanExtensionStatus:
            loan = self._loan_repository.get_loan(loan_id)
            if loan is None:
                return LoanExtensionStatus.LOAN_NOT_FOUND
            if loan.patron and loan.patron.membership_end < datetime.now():
                return LoanExtensionStatus.MEMBERSHIP_EXPIRED
            if loan.return_date is not None:
                return LoanExtensionStatus.LOAN_RETURNED
            if loan.due_date < datetime.now():
                return LoanExtensionStatus.LOAN_EXPIRED
            try:
                loan.due_date = loan.due_date + timedelta(days=self.EXTEND_BY_DAYS)
                self._loan_repository.update_loan(loan)
                return LoanExtensionStatus.SUCCESS
            except Exception:
                return LoanExtensionStatus.ERROR
    
        def checkout_book(self, patron, book_item, loan_id=None) -> None:
            from ..entities.loan import Loan
            from datetime import datetime, timedelta
            # Generate a new loan ID if not provided
            if loan_id is None:
                all_loans = getattr(self._loan_repository, 'get_all_loans', lambda: [])()
                max_id = 0
                for l in all_loans:
                    if l.id > max_id:
                        max_id = l.id
                loan_id = max_id + 1 if all_loans else 1
            now = datetime.now()
            due = now + timedelta(days=14)
            new_loan = Loan(
                id=loan_id,
                book_item_id=book_item.id,
                patron_id=patron.id,
                patron=patron,
                loan_date=now,
                due_date=due,
                return_date=None,
                book_item=book_item
            )
            self._loan_repository.add_loan(new_loan)
            return new_loan

    ```

1. インライン チャットを開き、メソッドをリファクターするプロンプトを入力します。

    ```plaintext
    #selection Refactor any manual aggregation or search over loans in this method to use generator expressions with built-in functions like any(), all(), sum(), or max() for improved readability and performance.
    ```

1. 少し時間を取って、`checkout_book` メソッドに追加されたコードを使用して提案された更新を確認しましょう。

    ```python

            loan_id = max((l.id for l in all_loans), default=0) + 1 if all_loans else 1
    ```

    完成した `checkout_book` メソッドは次のコードのようになります。

    ```python
    # --code continues-- 

    def checkout_book(self, patron, book_item, loan_id=None) -> None:
            from ..entities.loan import Loan
            from datetime import datetime, timedelta
            if loan_id is None:
                all_loans = getattr(self._loan_repository, 'get_all_loans', lambda: [])()
                loan_id = max((l.id for l in all_loans), default=0) + 1 if all_loans else 1
            now = datetime.now()
            due = now + timedelta(days=14)
            new_loan = Loan(
                id=loan_id,
                book_item_id=book_item.id,
                patron_id=patron.id,
                patron=patron,
                loan_date=now,
                due_date=due,
                return_date=None,
                book_item=book_item
            )
            self._loan_repository.add_loan(new_loan)
            return new_loan
    ```

    ジェネレーター式を使用するようにリファクターすると、手動による反復処理が、`max()`、`any()`、`sum()` などの*組み込み関数とシームレスに連携する*簡潔でメモリ効率の高い構造に置き換えられます。 これにより、定型コードと潜在的なエラーが減るだけでなく、ジェネレーター式によってメモリ内に中間リストが作成されないため、特に大規模なデータセットの処理時にパフォーマンスが向上します。 これらの変更により、全体としてコードベースはより Python らしくなり、読みやすく、保守しやすくなります。

### 編集モードのチャット ビューを使用して JsonPatronRepository クラスをリファクターする

**JsonPatronRepository** クラスには、次の 3 つのメソッドが含まれています。

- SearchPatrons:SearchPatrons メソッドは、利用者を名前で検索するために使用します。 このメソッドは、利用者の並べ替えられた一覧を返します。
- GetPatron:GetPatron メソッドは、ID で利用者を取得するために使用されます。 このメソッドは、値が設定された patron オブジェクトを返します。
- UpdatePatron:UpdatePatron メソッドは、利用者の情報を更新するために使用されます。 このメソッドにより、既存の利用者は新しいデータで更新され、更新された patrons コレクションが保存されます。

3 つのメソッドはそれぞれ、foreach ループを使用して利用者について反復処理し、検索入力または ID に基づいて一致するものを見つけます。

以下の手順に従って、演習のこのセクションを完了します。

1. **json_loan_repository.py** ファイルを開きます。

    **JsonPatronRepository** クラスは、図書館の利用者を管理するために設計されています。

1. 少し時間を取って、**JsonPatronRepository** クラスに含まれるメソッドをいくつか確認しましょう。

    **SearchPatrons** メソッドは、利用者を名前で検索するように設計されています。

    ```python

    #  ----code continues----   
    class JsonLoanRepository(ILoanRepository):
        def **init**(self, json_data: JsonData):
            self._json_data = json_data
    
        #  ----code continues----   
        def get_loans_by_patron_id(self, patron_id: int):
            result = []
            for loan in self._json_data.loans:
                if loan.patron_id == patron_id:
                    result.append(loan)
            return result
    
        #  ----code continues----  
        def get_overdue_loans(self, current_date):
            overdue = []
            for loan in self._json_data.loans:
                if loan.return_date is None and loan.due_date < current_date:
                    overdue.append(loan)
            return overdue
    
        def sort_loans_by_due_date(self):
            # Manual bubble sort for demonstration
            n = len(self._json_data.loans)
            for i in range(n):
                for j in range(0, n - i - 1):
                    if self._json_data.loans[j].due_date > self._json_data.loans[j + 1].due_date:
                        self._json_data.loans[j], self._json_data.loans[j + 1] = self._json_data.loans[j + 1], self._json_data.loans[j]
            return self._json_data.loans

        #  ----code continues----  
    ```

1. チャット ビューで **[モードの設定]** を選択し、**[編集]** を選択します。

    編集モードで新しいセッションを開始するように求められたら、**[はい]** を選択します。

    **編集**モードでは、GitHub Copilot の応答はコード エディター内にコード更新の提案として表示されます。 一般に編集モードが使用されるのは、新機能の実装、バグの修正またはコードのリファクタリング時です。

1. プロンプトを入力します

    ```plaintext
    @codebase Refactor methods in the JsonLoanRepository class with for-loops to use list comprehensions 
    or Python built-in methods to improve code clarity, conciseness, and efficiency.
    ```

1. 次の更新されたコードでは、メソッド `get_loans_by_patron_id` と `get_overdue_loans` でリスト内包表記が使用され、`sort_loans_by_due_date` メソッドで Python の組み込み `sorted` 関数が使用されるようになったことに注目してください。

    ```python
    #  ----code continues----

    def get_loan(self, loan_id: int) -> Optional[Loan]:
        return next((loan for loan in self._json_data.loans if loan.id == loan_id), None)

    def update_loan(self, loan: Loan) -> None:
        idx = next((i for i, l in enumerate(self._json_data.loans) if l.id == loan.id), None)
        if idx is not None:
            self._json_data.loans[idx] = loan
            self._json_data.save_loans(self._json_data.loans)
            return

    #  ----code continues----
    def get_loans_by_patron_id(self, patron_id: int):
        return [loan for loan in self._json_data.loans if loan.patron_id == patron_id]
    
    #  ----code continues----
    def get_overdue_loans(self, current_date):
        return [loan for loan in self._json_data.loans if loan.return_date is None and loan.due_date < current_date]

    def sort_loans_by_due_date(self):
        # Use built-in sorted for clarity and efficiency
        self._json_data.loans = sorted(self._json_data.loans, key=lambda l: l.due_date)
        return self._json_data.loans
    ```

    リスト内包表記と組み込み関数により、フィルター処理と並べ替えのための手動の for ループが置き換えられ、コードはより短く明確で効率的になりました。

### エージェント モードのチャット ビューを使用して JsonLoanRepository および JsonPatronRepository クラスをリファクターする

1. チャット ビューで、モードを **[エージェント]** に変更します

    エージェント モードは、GitHub Copilot をエージェントとして実行するように設計されています。 自然言語を使用して、高度なタスクを指定できます。 エージェントにより、割り当てたタスクの評価、必要な作業の計画作成、変更のコードベースへの適用が行われます。

    エージェント モードは、ユーザーが指定したタスクを実行するために、コード編集とツール呼び出しを組み合わせて使用します。 ユーザーのリクエストを処理する際に、編集とツールの結果を監視し、問題が発生した場合には解決します。 エージェント側で問題を解決できない場合は、介入を求められます。 たとえば、エージェントが同じ問題を解決するために複数の反復処理を使用する場合は、プロセスを一時停止して、ユーザーに、要求を明確にするための追加コンテキストの提供やプロセスのキャンセルを求めます。

    > **重要**:エージェント モードでチャット ビューを使用すると、GitHub Copilot は 1 つのタスクを完了するために複数の Premium リクエストを行う場合があります。 Premium リクエストは、ユーザーが開始するプロンプトや、Copilot がユーザーに代わって実行するフォローアップ アクションで使用できます。 使用される Premium リクエストの合計数は、タスクの複雑さ、必要なステップ数、選択されたモデルによって決まります。

1. 少し時間を取って、エージェントに割り当てる必要があるタスクについて考えてみましょう。

    タスクは、**JsonLoanRepository** & **JsonPatronRepository** クラスをリファクターすることです。 目標は、手動のループを特定し、元の foreach コードと同じ結果を生成する**組み込みの Python 関数**を使用してリファクターすることです。

    前のリファクタリングの経験を生かして、エージェントのタスクを作成することができます。

1. エージェントにタスクを割り当てるには、**エージェント モード**を使用して次のプロンプトを入力します。

    ```plaintext

    #codebase Review the manual loop code used in the methods of the JsonLoanRepository and JsonPatronRepository classes to make them more pythonic. Refactor with Built-in Python Functions that produce the same result as the original foreach code.

    ```

    このプロンプトで、**JsonPatronRepository** クラスをリファクターするようにエージェントに指示します。 foreach ループを組み込みの Python 関数に置き換えるように指定します。

1. エージェントによるコードのリファクターの進行状況を監視します。

    エージェントにより、複数の反復処理でタスクが完了することに注目してください。 各コード編集パスの後にはレビュー パスが続き、問題がチェックされます。 エージェントによって問題が検出されると、問題を解決するようにコードがリファクターされます。 エージェント側で問題を解決できない場合は、介入を求められます。

1. エージェントが完了したら、少し時間を取って、提案された更新を確認します。

    JsonLoanRepository には次のような変更が加えられます: - `update_patron` では、効率的なインデックス検索のために `enumerate` と共に `next` が使用されるようになりました。
        - `search_patrons` では、簡潔で読みやすいコードにするためにジェネレーター式と `sorted` が使用されています。
        - 手動のバブル ソートと冗長なコードがすべて削除されました。

    > **注:** JsonPatronRepository では、フィルター処理と検索に組み込み関数と内包表記が既に使用されているため、変更は不要でした。

    提案された更新は次のコードのようになります。

    ```python
    #  ----code continues----
    def search_patrons(self, search_input: str) -> List[Patron]:
        return sorted(
            (p for p in self._json_data.patrons if search_input.lower() in p.name.lower()),
            key=lambda patron: patron.name
        )

    def update_patron(self, patron: Patron) -> None:
        idx = next((i for i, existing_patron in enumerate(self._json_data.patrons) if existing_patron.id == patron.id), None)
        if idx is not None:
            self._json_data.patrons[idx] = patron
            self._json_data.save_patrons(self._json_data.patrons)

        #  ----code continues----

    ```

1. すべての更新を受け入れるには、**[保持する]** を選択します。

これらの更新により、元の動作を維持しながら、コードがより簡潔で読みやすく、Python らしくなります。

### アプリケーションをテストして実行する

コードのリファクターを完了したので、アプリケーションをテストして実行し、すべてが正しく機能することを確認します。 また、アプリケーションをテストして、リファクター後のコードが期待どおりに機能していることを確認します。

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

## まとめ

この演習では、GitHub Copilot を利用して既存のコードをリファクターし、改善することに重点を置きました。 手動のループと反復パターンを、リスト内包表記、ジェネレーター式、組み込み関数などの Python 構造に置き換えることで、体系的にコードベースを強化しました。 GitHub Copilot のさまざまなモード (質問、インライン、編集、エージェント) を使用してコードを分析し、リファクタリングの提案を生成し、Visual Studio Code で改善を直接適用しました。 これらのアプローチにより、コードの品質、読みやすさ、保守性、パフォーマンスが向上しました。 これらのスキルを身に付ければ、Python プロジェクトのリファクターと最新化を自信を持って実行できるようになります。また、新しい機能の開発を継続しながらコードベースをより堅牢かつ効率的にすることができます。

## クリーンアップ

演習が完了したので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに保持したくない変更が加えられていないか確認します。 不要な変更を加えた場合は、ここで元に戻してください。
