<!-- ---
lab:
    title: 'Exercise - Develop a greenfield application using GitHub Spec Kit'
    description: 'Learn how to apply the spec-driven development methodology with GitHub Spec Kit for a greenfield application project. in Visual Studio Code.'
--- -->

# GitHub Spec Kit を使用してグリーンフィールド アプリケーションを開発する

GitHub Spec Kit は、仕様と GitHub Copilot などの AI コーディング アシスタントを統合することで、仕様駆動開発 (SSD) を有効にするオープンソース ツールキットです。

この演習では、GitHub Spec Kit を使用して新しいグリーンフィールド アプリケーションを開発する方法を学習します。 まず、新しい .NET プロジェクト用に GitHub Spec Kit を初期化します。 次に、GitHub Spec Kit ワークフローを使用して、新しいアプリケーションの規約、仕様、計画、タスクのドキュメントを作成します。 最後に、GitHub Spec Kit の実装ワークフローを使用して、アプリケーションの初期 MVP バージョンを実装します。

この演習の所要時間は約 **60** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。Git 2.48 以降、.NET SDK 8.0 以降 (.NET 10 が既定値)、C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code、SQLite、Python 3.11 以降、uv パッケージ マネージャー、Specify CLI、GitHub Copilot が有効な GitHub アカウントへのアクセス。

ラボ環境を構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2345907" target="_blank">GitHub Spec Kit のラボ環境を構成する</a>。

## 演習のシナリオ

あなたはコンサルティング会社に勤務するソフトウェア開発者です。この会社は、Visual Studio Code で GitHub Spec Kit と GitHub Copilot を使用する仕様駆動開発 (SDD) 手法を採用しています。 あなたは、可能な限り早急に SDD 手法と GitHub Spec Kit の使用を開始する必要があります。 顧客の 1 つである Contoso Corporation からは、社内従業員向けの RSS フィード リーダーを開発するように依頼されています。 このグリーンフィールド プロジェクトに SDD 手法を使用することにしました。 SDD 手法と GitHub Spec Kit を使用することにより、アプリケーションの MVP バージョンを迅速に配布でき、追加の機能をシームレスにロールアウトできるようになります。

Contoso の利害関係者は、RSS フィード リーダー アプリのプロジェクト目標、機能、技術要件を文書化しました。 この情報は、規約、仕様、計画、タスクのドキュメントを生成するのに役立ちます。

この演習には、次のタスクが含まれています。

1. プロジェクト フォルダーを作成し、GitHub Spec Kit を初期化します。
1. GitHub Spec Kit の規約のコマンドとファイルを調べます。
1. 利害関係者のドキュメントを使用して規約を更新します。
1. 利害関係者のドキュメントを使用して spec.md ファイルを生成します。
1. 利害関係者のドキュメントと spec.md を使用して plan.md ファイルを生成します。
1. spec.md、plan.md、constitution.md を使用して tasks.md ファイルを生成します。
1. MVP アプリケーションに必要なタスクを実装する。

## プロジェクト フォルダーを作成し、GitHub Spec Kit を初期化する

Specify CLI は、プロジェクト フォルダー内の GitHub Spec Kit を初期化するために使用されます。 GitHub Spec Kit では、プロジェクト フォルダーを使用して、仕様駆動型開発ワークフローをサポートする構成ファイル、テンプレート、スクリプト、エージェントを格納します。

このタスクでは、新しいプロジェクト フォルダーを作成し、プロジェクト ディレクトリで GitHub Spec Kit を初期化します。

そのためには、以下の手順を実行してください。

1. ターミナル ウィンドウを開き、C: ドライブのルートに移動します。

    C: ドライブのルートに移動するには、コマンド プロンプトで次のコマンドを入力します。

    ```powershell
    cd C:\
    ```

1. RSSFeedReader プロジェクトという名前の新しいフォルダーを作成するには、次のコマンドを入力します。

    ```powershell
    mkdir TrainingProjects\RSSFeedReader
    ```

1. 新しいプロジェクト フォルダーに移動するには、次のコマンドを入力します。

    ```powershell
    cd TrainingProjects\RSSFeedReader
    ```

1. 現在のディレクトリで GitHub Spec Kit を初期化するには、次のコマンドを入力します。

    ```powershell
    specify init --here --ai copilot --script ps
    ```

    > **注:** macOS または Linux で bash または zsh を使用している場合は、`--script ps` を `--script sh`に置き換えます。

    このコマンドは、次のパラメーターを指定します。

    - `--here` - 現在のディレクトリ (既存の RSSFeedReader プロジェクト) で GitHub Spec Kit を初期化します。
    - `--ai copilot` - AI アシスタントとして GitHub Copilot を使用するようにプロジェクトを構成します。
    - `--script ps` - PowerShell スクリプトを使用することを指定します。

    `specify init` コマンドは、次のアクションを完了します。

    - `.github/agents/` ディレクトリと `.github/prompts/` ディレクトリにエージェント プロンプト ファイルを作成します。
    - `.specify/memory/` ディレクトリと `.specify/templates/` ディレクトリにテンプレート ファイルを作成します。
    - `.specify/scripts/powershell/` ディレクトリにスクリプト ファイルを作成します。
    - `.vscode/` ディレクトリに settings.json ファイルを作成します。
    - 成功メッセージ ("Project ready") を表示します。
    - 次に行う手順をいくつか提示しますが、これらは省略できます。

    ブラウンフィールド プロジェクトで `specify init` コマンドを使用すると、現在のディレクトリが空ではないことが認識され、続行する前に確認を求められます。 このコマンドは、既存のアプリケーション ファイルを保持します。

    **トラブルシューティング**:問題が発生した場合:

    - **"specify command not found"**:Specify CLI がインストールされていることを確認するには、`specify version` を実行します。
    - **アクセス許可が拒否されたエラー**:Windows では、適切なアクセス許可で PowerShell を実行していることを確認します。 macOS/Linux では、ファイルのアクセス許可を確認します。
    - **git clone エラー**:GitHub にサインイン済みで、インポートしたリポジトリへのアクセスがあることを確認します。
    - **GitHub Spec Kit コマンドが表示されない**:ワークスペースのルートに `.github/prompts/` が存在することを確認します。 Visual Studio Code を再度読み込んでみてください。

1. Visual Studio Code で RSSFeedReader プロジェクトを開くには、次のコマンドを入力します。

    ```powershell
    code .
    ```

    `code .` コマンドは、Visual Studio Code で現在のディレクトリ (RSSFeedReader) を開きます。

1. `specify init` コマンドによって作成されたフォルダー構造に注意してください。

    Visual Studio Code のエクスプローラー ビューを使用して、.github フォルダーと .specify フォルダーを展開します。 次の例のようなフォルダー構造が表示されます。

    ```plaintext
    RSSFEEDREADER (root)
    ├── .github/
    │   ├── agents/                 (GitHub Spec Kit executable workflows that can be triggered via commands)
    │   └── prompts/                (GitHub Spec Kit prompt files that provide detailed instructions for each of the agent workflows)
    ├── .specify/                   (GitHub Spec Kit configuration)
    │   ├── memory/                 (GitHub Spec Kit stores the project constitution defining core principles and governance rules that all features must follow)
    │   ├── scripts/powershell/     (GitHub Spec Kit uses automation utilities (scripts) for creating features, setting up plans, and managing the specification workflow)
    │   └── templates/              (GitHub Spec Kit provides standardized markdown formats for specs, plans, tasks, and checklists to ensure consistent documentation across all features)
    └── .vscode/                    (Visual Studio Code configuration)
    ```

1. GitHub Copilot のチャット ビューが開かれていることを確認します。

    GitHub Spec Kit は、Visual Studio Code のチャット インターフェイスを通じて GitHub Copilot と連携します。 プロジェクト ディレクトリで "specify init --ai copilot" を実行すると、このツールキットによって、"/speckit.*" コマンドを認識するようにワークスペースが構成されます。

1. チャット ビューで GitHub Spec Kit コマンドが使用可能かどうかを確認するには、「**/speckit**」と入力します

    使用できるコマンドを示すオートコンプリートの候補が表示されます。

    - `/speckit.analyze` - 実装計画を監査します。
    - `/speckit.checklist` - 仕様の完全性を検証します。
    - `/speckit.clarify` - 質問と回答のプロセスを通じて仕様を絞り込みます。
    - `/speckit.constitution` - プロジェクトの管理原則を定義します。
    - `/speckit.implement` - 実装を実行します。
    - `/speckit.plan` - 技術的な実装計画を生成します。
    - `/speckit.specify` - 機能仕様を作成します。
    - `/speckit.tasks` - 作業を実行可能なタスクに分割します。
    - `/speckit.taskstoissues` - tasks.md のタスクを GitHub の issue に変換します。

    > **注**:'/speckit.'  コマンドが表示されない場合は、Visual Studio Code でプロジェクトを閉じてから再度開いてみてください。

1. プロジェクトを新しい GitHub リポジトリに公開します。

    プロセスのこの時点では、プロジェクトを GitHub リポジトリに公開する必要はありませんが、GitHub Spec Kit では最終的に Git リポジトリが必要になるため、今すぐ設定しても問題はありません。 リポジトリを使用すると、問題点の追跡とプロジェクト マネジメントのために変更を追跡し、他のユーザーと共同作業を行い、GitHub の機能を使用することができます。

    次のステップを使用して、プロジェクトを新しい GitHub リポジトリに公開できます。

    1. Visual Studio Code の [ソース管理] ビューを開きます。

    1. **[ブランチの発行]** を選びます。

        リポジトリ名 (RSSFeedReader) は、ローカル フォルダー名に基づいて自動的に提案されます。

    1. **[GitHub プライベート リポジトリに公開]** を選択します。

        > **重要**:GitHub にサインインするように求められたら、サインイン プロセスに従って GitHub アカウントを認証します。

1. リポジトリが正常に作成されたことを確認します。

    次に例を示します。

    オプション 1: ブラウザーを開き、GitHub プロファイル ページに移動します。 リポジトリの一覧に新しい RSSFeedReader リポジトリが表示されます。 必要に応じてページを更新します。

    オプション 2: Visual Studio Code の通知 (右下) を開きます。 リポジトリが正常に公開されたことを確認する通知と、そのリポジトリを GitHub で表示するためのリンクが表示されます。

プロジェクト フォルダーの作成、GitHub Spec Kit の初期化、ソース管理の構成が完了したので、GitHub Spec Kit を使用して RSS フィード リーダー アプリケーションの規約、仕様、計画、タスクを作成する準備が整いました。

## GitHub Spec Kit の規約のコマンドとファイルを調べる

constitution.md ファイルでは、開発プロセス全体で従う必要があるポリシー、要件、技術標準を定義します。 GitHub Spec Kit には、constitution.md ファイルの作成と保守に役立ついくつかのリソースが含まれています。

- .specify/memory/constitution.md ファイルには、規約ドキュメントのテンプレートが含まれています。
- .github/agents/speckit.constitution.agent.md ファイルには、constitution.md ファイルの生成 (または更新) に使用される詳細な手順が記載されています。
- .github/prompts/speckit.constitution.prompt.md ファイルには、/speckit.constitution コマンドが呼び出されたときに speckit.constitution という名前のエージェントを実行するように Copilot Chat に指示する "ルーティング スタブ" が含まれています。
- /speckit.constitution コマンドは、プロジェクトの constitution.md ファイルを生成するために使用されます。

このタスクでは、規約の生成に使用されるリソース ファイルを調べて、`/speckit.constitution` コマンドを評価します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューを使用して、**.github/agents** フォルダーと **.specify/memory** フォルダーを展開します。

    これらのフォルダーには、constitution.md ファイルの作成に使用される GitHub Spec Kit リソースが格納されています。 規約ファイルを生成する前に、これらのリソース ファイルについて十分に理解しておくと役に立つ場合があります。

1. **.specify/memory** フォルダー内の **constitution.md** ファイルを開きます。

    constitution.md ファイルの初期バージョンには、規約用の既定のテンプレートが含まれます。

1. 少し時間を取って、**constitution.md** ファイルを確認します。

    規約テンプレートでは、4 つの主要なセクション (コア原則 (上部)、名前のない 2 つのセクション、ガバナンス (下部)) が定義されていることに注意してください。 プレースホルダー トークンは、特定の内容を追加する場所を示すためにドキュメント全体で使用されます。 "コア原則" セクションには、5 つの主要な原則のプレースホルダーが含まれており、テンプレートにはセクションとサブセクションの内容例も含まれています。

1. **.github/agents** フォルダー内の **speckit.constitution.agent.md** ファイルを開きます。

1. 少し時間を取って **speckit.constitution.agent.md** ファイルを確認します。

    このマークダウン ファイルで提供されている詳細な手順に注意してください。 "アウトライン" セクションでは、constitution.md テンプレートを開始点として使用するように GitHub Copilot に指示し、プレースホルダー トークンを使用する方法を説明し、規約の各セクションに入力する方法に関するガイダンスを提供します。

1. エディターで開いているすべてのファイルを閉じます。

1. チャット ビューが開いていることを確認してから、新しいチャット セッションを開始します。

    **[新しいチャット]** ボタン (チャット パネルの上部にある **+** アイコン) を選択すると、新しいセッションを開始できます。 新しいチャット セッションを開始すると、コンテキストがクリーンな状態になります。

    > **注:** このラボ演習は、GPT-5.2、Claude Sonnet 4.5、Claude Opus 4.5 のモデルを使用して正常にテストされました。 これらのモデルによって生成された結果は似ていて、比較的一貫性がありました。 しかし、GPT-4 や GPT-5 mini などの古いモデルを使用すると、一貫性がなく、予期しない結果が生成されることが多くありました。 GitHub Spec Kit コマンドを実行するときは、可能な限り複雑な推論のために最適化された新しい言語モデルを使用することをお勧めします。

1. 少し時間を取って、/speckit.constitution コマンドを実行するためのオプションを検討してみましょう。

    次のオプションを使用して /speckit.constitution コマンドを実行できます。

    - `/speckit.constitution --text "..."`:インライン テキストを使用して、規約に含めるべき標準、ガイドライン、原則、制約を記述します。
    - `/speckit.constitution --files ...`:規約作成のコンテキストを提供するプロジェクト ドキュメントを指定します。
    - `/speckit.constitution --text "..." --files ...`: インライン テキストとプロジェクト ドキュメントの組み合わせを指定します。
    - `/speckit.constitution`:入力せずにコマンドを実行します。 ワークフローでは、コードベース内のファイルを使用して、標準、ガイドライン、要件を識別し、規約を生成します。

    > **注**:/speckit.constitution コマンドを同じプロジェクト内で複数回実行して、constitution.md ファイルを改良または拡張することができます。 詳細な (入力) 要件を提供すると、より正確で包括的な規約を作成するのに役立ちます。

1. 入力なしで規約ワークフローを開始するには、チャット ビューで次のコマンドを入力します。

    ```plaintext
    /speckit.constitution
    ```

    > **注**:テキストやファイルが入力されていない空のプロジェクトで規約ワークフローを実行することはお勧めしませんが、ワークフローでテンプレートと指示をどのように使用するかを示すのには役立ちます。

1. GitHub Copilot の応答を監視します。

    constitution.md ファイルが更新されると、GitHub Copilot のチャット ビューに進行状況が表示されます。

    > **注**:GitHub Copilot から、ファイルへのアクセスまたは編集ができないという報告があった場合は、Visual Studio Code の **[設定]** を開き、**[機能]** を展開して **[チャット]** を選択し、**[チャット] > [エージェント]** が有効になっていることを確認します。

1. GitHub Copilot が constitution.md ファイルの更新を完了したら、そのファイルを開き、少し時間を取って、推奨される編集内容を確認します。

    ワークフローによって constitution.md ファイルが正常に更新され、5 つの主要な原則が入力され、名前のない 2 つのセクションが作成されていることに注意してください。 ところで、更新は何に基づいて行われたのでしょうか? 

    簡単な分析を次に示します。

    - 元の **constitution.md** ファイルは、更新のテンプレートとして機能します。 このドキュメントは、規約に次の内容を含める必要があることを指定します。

        - プロジェクト名
        - セクション 1:コア原則。 "コア原則" セクションには、5 つのコア原則を含める必要があります。 テンプレートには、それらの原則とその説明の例が提供されています。
        - セクション 2: 名前なし。 テンプレートには、セクション名と内容の例が提供されています。
        - セクション 3: 名前なし。 テンプレートには、セクション名と内容の例が提供されています。
        - セクション 4:ガバナンス。 テンプレートには、ガバナンス セクションの適用方法とガバナンス規則の例が提供されています。

    - **speckit.constitution.agent.md** ファイルには、`/speckit.constitution` コマンドで指定されたテキスト入力またはファイル入力に基づいて constitution.md ファイルを更新する手順を提供します。 ガイダンスが提供されない場合、エージェントはコードベースで見つかった情報を使用して規約テンプレートに入力します。 この場合、コードベースで入手できる情報は、RSS フィード リーダーを作成する必要があるということだけであるため、AI は RSS フィード リーダーに関する知識を使用して constitution.md ファイルを更新します。

    規約ワークフローでは、関連する GitHub Spec Kit ファイルを更新することもできます。 これにより、すべてのプロジェクト ドキュメントの整合性が確保されます。 グリーンフィールド プロジェクトのこの初期段階では、[テンプレート] フォルダー内のファイル (spec-template.md、plan-template.md、tasks-template.md) を更新できます。 いずれかのファイルが更新された場合、変更は、constitution.md ファイルで定義されている原則を反映しているはずです。

1. 提案されたすべての更新を受け入れるには、[チャット] ビューで **[保持]** ボタンを選択します。

    エディターで **[保持]** ボタン オプションを選択して、個々のファイルへの変更、またはファイル内の個々の変更を受け入れることもできます。

## 利害関係者のドキュメントを使用して規約を更新する

/speckit.constitution ワークフローでは、テキスト入力、ファイル入力、コードベースを使用して、constitution.md ファイルに含めるポリシー、標準、要件、ガイドラインを収集します。 詳細な入力を提供すると、より正確で包括的な規約を生成するのに役立ちます。

このタスクでは、RSSFeedReader プロジェクトの利害関係者のドキュメントをダウンロードし、GitHub Spec Kit コマンドとの関係を評価し、それらのドキュメントを使用して constitution.md ファイルを更新します。

そのためには、以下の手順を実行してください。

1. 利害関係者ドキュメントをダウンロードするには、ブラウザーで次のリンクを開きます。[RSSFeedReader - 利害関係者ドキュメント](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHSpecKitEx14StakeholderDocuments.zip)。

1. ダウンロードした ZIP ファイルが格納されているフォルダーを開きます。

1. ダウンロードした ZIP ファイルの内容を一時フォルダーに抽出します。

1. Visual Studio Code のエクスプローラー ビューで、RSSFeedReader プロジェクトのルート フォルダーを右クリックし、**[エクスプローラーで表示]** (macOS の場合は **[Finder で表示]**) を選択します。

1. エクスプローラー (または Finder) で、ZIP ファイルを抽出した一時フォルダーを開きます。

1. 一時フォルダー内のすべてのファイルを選択し、コピーして、RSSFeedReader プロジェクトのルート フォルダーに貼り付けます。

1. Visual Studio Code に戻ります。

    更新された RSSFeedReader プロジェクトは、次のようになります。

    ```plaintext
    RSSFEEDREADER (root)
    ├── .github/
    │   ├── agents/                 (GitHub Spec Kit executable workflows that can be triggered via commands)
    │   └── prompts/                (GitHub Spec Kit prompt files that provide detailed instructions for each of the agent workflows)
    ├── .specify/                   (GitHub Spec Kit configuration)
    │   ├── memory/                 (GitHub Spec Kit stores the project constitution defining core principles and governance rules that all features must follow)
    │   ├── scripts/powershell/     (GitHub Spec Kit uses automation utilities (scripts) for creating features, setting up plans, and managing the specification workflow)
    │   └── templates/              (GitHub Spec Kit provides standardized markdown formats for specs, plans, tasks, and checklists to ensure consistent documentation across all features)
    ├── .vscode/                    (Visual Studio Code configuration)    
    ├── StakeholderDocuments        (folder containing stakeholder supplied documents)
    └── README.md                   (a readme file describing the project documentation)
    ```

1. Visual Studio Code のエクスプローラー ビューで、**StakeholderDocuments** フォルダーを展開します。

    StakeholderDocuments フォルダーには、次のファイルを含める必要があります。

    - **ProjectGoals.md** - プロジェクト目標の概要、目的、スコープ、配布方法、ロールアウト計画、品質目標、標準とガイドライン。
    - **AppFeatures.md** - ユーザー向けの詳細な機能要件。
    - **TechStack.md** - テクノロジの選択肢とアーキテクチャの根拠。

1. 少し時間をとって、利害関係者のドキュメントを確認します。

    これらのドキュメントでは、プロジェクトの目標と制約、アプリの機能、技術要件について自然言語で説明されています。 このコンテキストを理解することは、効果的な仕様、計画、タスクを作成するために不可欠です。 詳細レベルは、多くの実際のプロジェクトの暫定版ドキュメントで見られる典型的なレベルです。

    プロジェクト ドキュメントとドキュメントで提供される詳細は、会社のポリシーとプロジェクトの複雑さによって大きく異なる場合があります。 GitHub Spec Kit コマンドは、使用できる詳細レベルで動作するように設計されており、その情報を使用して、仕様駆動型開発プロセスを成功させるために必要な規約、仕様、計画、タスクのドキュメントを作成します。 ただし、詳細な入力を行うと、より予測可能な結果が得られます。

1. 利害関係者のドキュメントと GitHub Spec Kit コマンドの間の関係を検討します。

    各ドキュメントは、仕様駆動型開発プロセスを導くのに役立つ情報を提供します。

    次に例を示します。

    - **ProjectGoals.md**: このドキュメントは、constitution.md、spec.md、plan.md ファイルの作成に役立つプロジェクト情報を提供します。
    - **AppFeatures.md**: このドキュメントには、spec.md ファイルの作成に役立つユーザー向けの詳細な要件が記載されています。
    - **TechStack.md**: このドキュメントには、plan.md ファイルの作成に役立つテクノロジの選択肢とアーキテクチャの根拠の概要が記載されています。

    > **注**:利害関係者のドキュメントと GitHub Spec Kit コマンドを 1 対 1 でマッピングする必要はありません。 たとえば、1 つの ProjectDescription.md ファイルで、constitution.md、spec.md、plan.md ファイルの生成に使用されるコンテキストを提供できます。

1. チャット ビューで、インライン テキストと利害関係者のドキュメントの組み合わせを使用して規約ワークフローを開始するには、次のコマンドを入力します。

    ```plaintext
    /speckit.constitution --text "Code projects emphasize security, privacy, accessibility, performance, reliability, observability, release management, documentation, dependency management, and code quality. Ensure that all principles are specific, actionable, and relevant to the project context." --files StakeholderDocuments/ProjectGoals.md StakeholderDocuments/AppFeatures.md StakeholderDocuments/TechStack.md
    ```

    > **注**:/speckit.constitution コマンドを同じプロジェクト内で複数回実行して、constitution.md ファイルを改良または拡張することができます。 詳細な入力を提供すると、より正確で包括的な規約が生成されます。

1. GitHub Copilot の応答を監視します。

    GitHub Copilot でプロジェクトの要件が分析され、constitution.md ファイルが更新されるまでに 1 ～ 2 分かかる場合があります。

1. GitHub Copilot が constitution.md ファイルの更新を完了したら、少し時間を取って、推奨される編集内容を確認します。

    規約ワークフローは (テキストとファイルの両方から) 入力の基になる原則を抽出し、その情報を使用して詳細を規約に追加していることに注目してください。

    開発者は、規約を見直して、要件が規約に正確に取り込まれていることを確認する必要があります。 ビジネス要件と技術ガバナンスを規約で表す運用環境で作業している場合、この手順は重要です。 トレーニング演習では、このレビューは、主に規約の内容を十分に理解するのに役立ちます。

    各原則は明確に記述され、実行可能なものである必要があります。 次に例を示します。

    - ❌ あいまい:"セキュリティのベスト プラクティスを適用する。"  は一般的すぎます。
    - ✅ 明確:"HTML コンテンツは、レンダリング前にサニタイズする必要があります。"  は具体的かつ実践的です。

    重要な要件が欠落しているか不確かな場合は、constitution.md ファイルを直接編集して原則を追加または変更できます。

    実際のプロジェクトでは、次の条件に照らして規約を確認することが重要です。

    - 完全性:すべての主要な領域 (セキュリティ、プライバシー、アクセシビリティ、パフォーマンス、信頼性、監視、リリース管理、文書化、依存関係管理、コード品質) について説明します。
    - 明確さ:各原則は具体的で明確である。
    - 一貫性: 原則は相互に矛盾していない。
    - 関連性:すべての原則は RSSFeedReader プロジェクトに関連しています。

1. /speckit.constitution ワークフローによって **templates** フォルダー内のファイルが更新された場合は、少し時間を取って、それらの更新も確認します。

1. 更新されたすべてのファイルへの変更を承認するには、[チャット] ビューで **[保持]** ボタンを選択します。

1. 更新されたファイルを保存して閉じます。

1. 更新されたファイルを Git リポジトリにコミットしてプッシュします。

    次に例を示します。

    1. Visual Studio Code の [ソース管理] ビューを開きます。
    1. "利害関係者の要件を使用して規約を更新します" などのコミット メッセージを入力します。
    1. 変更をステージしてコミットしまう。
    1. 変更を Git リポジトリにプッシュします。

    ブラウザーで GitHub リポジトリを確認して、コミットを検証できます。 これで、constitution.md ファイルがコミット メッセージと共に表示されます。

この規約は、ビジネス要件と技術実装の間の "契約" として機能し、仕様駆動開発のプロセス全体の一貫性を確保します。 GitHub Spec Kit を使用して仕様、計画、タスクを生成する場合、これらの原則が参照され、実装が、指定された要件と合致していることが確認されます。

## 利害関係者のドキュメントを使用して spec.md ファイルを生成する

仕様 (spec.md) は、ユーザーの観点から何を構築するかを定義します。 仕様では、機能、ユーザー ストーリー、受け入れ基準、ビジネス要件について説明しますが、実装方法は規定しません。 適切に記述された仕様は、実装計画とタスクを作成するための基礎として機能します。

このタスクでは、GitHub Copilot の `/speckit.specify` コマンドを使用して、Contoso のビジネス利害関係者によって提供された要件に基づいて、RSS フィード リーダーの詳細な仕様を生成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューを使用して、**spec-template.md** ファイルと **speckit.specify.agent.md** ファイルを調べます。

    次のことに注意してください。

    - spec-template.md ファイルでは、仕様ドキュメントの構造とセクションを定義します。 これには、機能の説明、ユーザー ストーリー、受け入れ基準、その他の関連情報の例やプレースホルダーが含まれます。
    - speckit.specify.agent.md ファイルには、/speckit.specify コマンドの詳細な手順が記載されています。 提供された要件に基づいて仕様を作成する方法を GitHub Copilot にガイドします。
    - speckit.specify.agent.md ファイルは、ワークフローの開始時にリポジトリ ブランチを生成します。 通常、ブランチを作成するにはユーザーのアクセス許可が必要であるため、ワークフローの実行時に GitHub Copilot によってアクセス許可の入力が求められます。

1. Visual Studio Code のエクスプローラー ビューを使用して、利害関係者のドキュメント **ProjectGoals.md** と **AppFeatures.md** を調べます。

    AppFeatures.md ファイルは、ユーザー向けの機能要件の主要なリソースであり、包括的な仕様を作成するために必要なコンテキストを提供します。 ProjectGoals.md ファイルは、仕様を通知するのにも役立つ MVP とロールアウト計画に関する情報を提供します。

1. 利害関係者のドキュメントに基づいて、RSS フィード リーダー アプリの概要説明を作成します。

    概要説明は、簡潔 (数個の文) であり、RSS フィード リーダー アプリのコア機能が取り込まれている必要があります。 次に例を示します。

    ```plaintext
    "MVP RSS reader: a local-first RSS/Atom reader where users can subscribe and manage feeds, add them via URL or website discovery, refresh to see updates, and read items with a clear newest-first, read/unread flow. Data and state persist on the device, with an item view, open-original links, and the ability to mark items (or all) as read. Reliability and safety are core: the system handles common feed issues gracefully, avoids duplicates, shows clear errors, and renders content securely with HTTPS and HTML sanitization."
    ```

1. エディターで開いているすべてのファイルを閉じます。

1. チャット ビューが開いていることを確認します。

    GitHub Copilot が、現在のチャット セッションで以前の対話のコンテキストを保持していることに注意してください。 現在のセッションで constitution.md ファイルを生成した場合、GitHub Copilot のチャット ビューの下部付近に **[ビルド仕様]** ボタンが表示され、これを使用して仕様の生成を開始できます。 この場合は、要件ドキュメントを明示的に指定する必要があるため、[ビルド仕様] ボタンは使用しません。

1. チャット ビューで、利害関係者のドキュメントの情報を使用して spec.md ファイルを生成する仕様ワークフローを開始するには、次のコマンドを入力します。

    ```plaintext
    /speckit.specify --text "MVP RSS reader: a local-first RSS/Atom reader where users can subscribe and manage feeds, add them via URL or website discovery, refresh to see updates, and read items with a clear newest-first, read/unread flow. Data and state persist on the device, with an item view, open-original links, and the ability to mark items (or all) as read. Reliability and safety are core: the system handles common feed issues gracefully, avoids duplicates, shows clear errors, and renders content securely with HTTPS and HTML sanitization." --files StakeholderDocuments/ProjectGoals.md StakeholderDocuments/AppFeatures.md
    ```

    `--text` オプションを指定しない場合は、続行する前にアプリの機能の説明を入力するように求められる場合があります。

1. GitHub Copilot の応答を監視し、必要に応じて支援します。

    > **重要**:GitHub Copilot は、spec.md ファイルを生成するときに支援を必要とします。 たとえば、GitHub Copilot は、リポジトリの新しいブランチを作成するためのアクセス許可を要求します。 要求されたら、チャット ビューで応答してアクセス許可を付与します。

    仕様の検証に使用される spec.md ファイルと要件チェックリストが作成されるまでに 4 ～ 6 分かかる場合があります。 ワークフロー プロセスが正常に完了したことを報告せずに 6 分以上非アクティブになっている場合は、GitHub Copilot の **retry** コマンドを使用してワークフローを再起動できます。

1. 指定ワークフローが完了したら、Visual Studio Code のエクスプローラー ビューを使用して、**specs** フォルダーと **checklists** フォルダーを展開します。

1. エクスプローラー ビューで **spec.md** を選択し、数分かけて spec.md ファイルを確認します。

    spec.md ファイルは、**.specify/templates/spec-template.md** ファイル内にあるテンプレートに基づきます。 更新された spec.md ファイルには、利害関係者の要件に基づいた 'RSS フィード リーダー アプリについて指定した詳細な仕様が含まれている必要があります。

    仕様は明確かつ包括的で、適切に構造化されている必要があります。 また、技術計画とタスクを作成するための強固な基盤も提供する必要があります。

    spec.md ファイルに、仕様テンプレートで定義されている必須セクションが含まれていることを確認します。 次に例を示します。

    - **ユーザー シナリオとテスト**:機能の能力とそのテスト方法に関するユーザー中心の説明。
    - **必要事項**:カテゴリ別にまとめられた、満たす必要がある詳細な要件。
    - **成功基準**:測定可能な結果、前提条件、対象外事項。

1. **spec.md** ファイル内の "ユーザー ストーリー" (および "受け入れシナリオ") が具体的で、かつテスト可能であることを確認します。

    受け入れシナリオは、**Given-When-Then** 形式に従う必要があります。 このシナリオでは、成功または失敗の明確な条件を提供する必要があります。 次に例を示します。

    - ✅ 推奨:(**Given**) ユーザーがサブスクリプションを持っていない場合、(**When**) 有効なフィード URL を追加すると、(**Then**) そのフィードはサブスクリプション リストに表示される。

    - ✅ 推奨:(**Given**) ユーザーがフィードにサブスクライブしている場合、(**When**) ユーザーが "今すぐ更新" をトリガーすると、(**Then**) システムはフィードをフェッチし、項目を最新のものから順に一覧表示する。

    - ❌ 非推奨:"アップロードが適切に機能する必要がある" や "システムは高速である必要がある" などのあいまいな条件。

1. **spec.md** ファイルの "要件" セクションに、利害関係者の要件ドキュメントの主要な要件が含まれていることを確認します。

    たとえば、次のような要件が確認されます。

    - システムでは、単一のユーザーがフィード URL を貼り付けてフィード サブスクリプションを追加できるようにする必要がある。
    - システムでは、ユーザーがフィード サブスクリプションを削除できるようにする必要がある。
    - システムは、フィードごとに手動の "今すぐ更新" アクションを提供する必要がある。

1. エクスプローラー ビューで **requirements.md** を選択し、少し時間を取って requirements.md ファイルを確認します。

    **requirements.md** ファイルで問題が報告されていないことを確認します。 すべてのチェックリスト項目を正常に通過したことがわかります。

    > **注**:`/speckit.clarify` コマンドを使用して、仕様のあいまいさ、ギャップ、指定されていない領域を特定できます。 運用環境では、最初の仕様を生成した後、技術計画フェーズに移行する前に、すべての要件が明確かつ完全であることを確認するために明確化プロセスを実行することをお勧めします。 このラボ演習では、明確化ステップをスキップします。

1. 推奨されるファイルの更新を受け入れ、**spec.md** と **requirements.md** ファイルを保存します。

1. 仕様ファイルをコミットし、新しいブランチを Git リポジトリに公開します。

    次に例を示します。

    Visual Studio Code のソース管理ビューを開き、変更をステージングし、「RSS Feed Reader アプリの仕様を追加する」のようなコミット メッセージを入力して、新しいブランチを Git リポジトリに公開します。

この仕様では、"どのように" を指定するのではなく "何を" を定義します。 プログラミング言語、フレームワーク、データベース スキーマ、またはコード編成は指定しません。これらの実装の詳細は、規約の技術的制約に基づいて計画およびタスク フェーズで決定されます。 この仕様は、ユーザーのニーズとビジネス要件に重点を置いており、技術面以外の関係者とのレビューを容易にします。

## 利害関係者のドキュメントと spec.md を使用して plan.md ファイルを生成する

技術計画は、"what" (仕様) と "how" (実装) の間のギャップを埋めるものです。 アーキテクチャ、テクノロジの選択、データ モデル、API の設計、実装のアプローチを定義し、規約で定義されている制約に従います。

このタスクでは、GitHub Copilot の `/speckit.plan` コマンドを使用して、包括的な技術実装計画を生成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューを使用して、**plan-template.md** ファイルと **speckit.plan.agent.md** ファイルを開きます。

1. 少し時間を取って、**plan-template.md** ファイルと **speckit.plan.agent.md** ファイルを確認します。

    次のことに注意してください。

    - plan-template.md ファイルは、技術計画ドキュメントの構造とセクションを定義します。
    - speckit.plan.agent.md ファイルには、/speckit.plan コマンドの詳細な手順が記載されています。 仕様と規約に基づいて技術計画を作成する方法を GitHub Copilot にガイドします。

1. エディターで開いているすべてのファイルを閉じます。

1. Chat ビューで、技術計画プロセスを開始するために、次のコマンドを入力します。

    ```dotnetcli
    /speckit.plan --files StakeholderDocuments/ProjectGoals.md StakeholderDocuments/TechStack.md
    ```

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    GitHub Copilot は、constitution.md、spec.md、さらに利害関係者のファイルを分析して計画を生成します。 必要に応じて、アクセス許可と支援を提供します。

    GitHub Copilot で技術計画とそれに関連するマークダウン ファイルが生成されるまでに 6 から 8 分かかる場合があります。

1. 計画ワークフローが完了したら、次のファイルが **specs** フォルダーの下に一覧表示されていることを確認します。

    - **plan.md**
    - **research.md**
    - **quickstart.md**
    - **data-model.md**

    **contracts** フォルダーの下に 1 つ以上のファイルが表示される場合もあります。

1. 少し時間を取って、**research.md**、**plan.md**、**quickstart.md**、**data-model.md** ファイルをレビューします。

    - research.md ファイルには、RSS フィード リーダー アプリの調査結果とテクノロジの決定が取り込まれています。
    - plan.md ファイルには、RSS フィード リーダー アプリの技術的な実装計画の概要が示されています。
    - quickstart.md ファイルは、セットアップ手順と、実装を開始する方法の概要を提供します。
    - data-model.md ファイルでは、RSS フィード リーダー アプリに必要なデータ エンティティ、プロパティ、関係を定義します。

    運用シナリオの場合、計画によって、技術的なコンテキストの包括的な説明と、新しい機能の明確に定義された実装戦略が提供されることを確認する必要があります。 調査、クイック スタート、データ モデルの各ファイルは、追加のコンテキストと詳細を提供して計画を補完する必要があります。 この演習では、各ファイルに関連付けられているコンテンツを十分に理解することに重点を置きます。

    > **重要**:このラボは、.NET 8 または .NET 9 がインストールされている環境に対応しています。 ただし、利害関係者のドキュメントでは、.NET 10 がターゲット フレームワークとして指定されています。 お使いの環境に .NET 8 または .NET 9 (ただし .NET 10 ではない) が含まれている場合は、それに応じて plan.md と quickstart.md のファイルを更新する必要があります。

1. ファイルを確認したら、更新内容を確定します。

    計画で重要な詳細が省略されている場合、または同意しない仮定を行う場合は、次のことができます。

    - plan.md ファイルを直接編集するか、
    - GitHub Copilot Chat でフォローアップの質問をします。 次に例を示します。

    ```plaintext
    The plan should include a section on how to handle feed parsing errors gracefully. Please update the plan.md file to include this information.
    ```

1. ファイルを保存し、変更をコミットして同期します。

技術計画が、実装のブループリントとして機能するようになりました。 これは、組織の制約を尊重しながら、ビジネス要件を具体的な技術的決定に変換します。

## spec.md、plan.md、constitution.md を使用して tasks.md ファイルを生成する

tasks.md ファイルでは、技術計画を具体的で実用的な実装手順に分割します。 各タスクは、妥当な期間内 (AI の支援なしで実装する場合は通常数時間から 1 日) で完了するのに十分な小ささにする必要があり、明確な受け入れ基準を含んでいる必要があります。

このタスクでは、GitHub Spec Kit の `/speckit.tasks` コマンドを使用して、包括的なタスク リストと段階的な実装計画を生成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューを使用して、**tasks-template.md** ファイルと **speckit.tasks.agent.md** ファイルを開きます。

1. 少し時間を取って、**tasks-template.md** ファイルと **speckit.tasks.agent.md** ファイルを確認します。

    tasks-template.md ファイルはタスクを論理フェーズに整理し、speckit.tasks.agent.md ファイルでは、/speckit.tasks ワークフローで従う必要がある次の手順について説明されていることに注意してください。

    - 読み取る入力ファイル (spec.md、plan.md など)
    - 生成するファイル (tasks.md)
    - タスクをシーケンスする方法 (フェーズごと、ユーザー ストーリーごとなど)
    - 各タスクを定義する方法 (具体的、実践的、テスト可能)
    - 適用するチャックおよびゲート (カバレッジ、順序、スコープ)

1. エディターで開いているすべてのファイルを閉じます。

1. チャット ビューで、tasks.md ファイルの生成を開始するには、次のコマンドを入力します。

    ```dotnetcli
    /speckit.tasks
    ```

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    GitHub Copilot で、spec.md と plan.md ファイルが分析され、tasks.md ファイルにタスクが生成されます。

    GitHub Copilot で tasks.md ファイルが生成されるまでに 3 から 4 分かかる場合があります。 必要に応じて、アクセス許可と支援を提供します。

1. タスク ワークフローが完了したら、少し時間を取って、**tasks.md** ファイルをレビューします。

    タスクがフェーズとユーザー ストーリー別に論理的に順序付けされていることを確認します。 次に例を示します。

    - "セットアップ" と "基盤" のタスクが最初になります。
    - バックエンド API タスクは、基礎上に構築します。
    - フロントエンド タスクは、バックエンド エンドポイントを参照します。
    - テスト タスクは、実装後に実行します。
    - デプロイ タスクは、最後に実行します。

    運用環境のシナリオでは、すべての要件 (spec.md) とすべての主要な設計コミットメント (plan.md) が、少なくとも 1 つ (通常は複数) の具体的なタスクにマップされていることを確認する時間が必要です。 次に例を示します。

    - plan.md ファイルの設計コミットメントには、対応する実装タスクが必要です。
    - ユーザー ストーリーの受け入れ基準には、対応する実装タスクと検証タスクが必要です。
    - 機能要件には、対応する実装タスクが必要です。
    - セキュリティ要件には、対応する実装タスクが必要です。
    - パフォーマンス要件には、テスト タスクが必要です。

1. 各タスクが具体的でアクション可能であることを確認します。

    - ✅ 推奨:"backend/src/RssFeedReader.Infrastructure/Http/FeedHttpClient.cs に安全な HTTP フェッチ機能 (http/https のみ、タイムアウト、サイズ制限、コンテンツの種類チェック) を実装する"
    - ❌ あいまい:"HTTP クライアントでセキュリティと信頼性の要件を適用する"

    タスクに適切なスコープがあることを確認します。

    - 開発者は、数時間から 1 日で個々のタスクを完了できる必要があります。
    - タスクが大きすぎると思われる場合は、実装時に分割することが必要になる場合があります。

    必要に応じて、タスクの依存関係またはメモを追加します。 次に例を示します。

    ```markdown
    - [ ] T026 Implement refresh pipeline orchestration (fetch -> parse -> sanitize -> persist -> status update) in backend/src/RssFeedReader.Domain/Services/FeedRefreshService.cs
      - Depends on: T021, T022, T024, T025
      - Note: This task implicitly depends on several earlier foundational tasks (HTTP fetcher, parser, sanitizer, identity hashing)
    ```

1. 推奨されるファイルの更新を受け入れて、**tasks.md** ファイルを保存します。

1. 変更をコミットし、更新を同期します。

これで、tasks.md ファイルにより、実装の明確なロードマップが提供されます。

## MVP アプリケーションに必要なタスクを実装する

仕様、技術計画、タスクの明確なドキュメントが用意されたので、RSS フィード リーダー アプリを実装する準備が整いました。

tasks.md ファイルは、作業を管理可能なチャンクに分割する段階的な実装戦略を提供します。 実装については、プロジェクトの優先順位と制約に応じて、さまざまなアプローチ方法があります。 たとえば、次のいずれかの戦略を検討できます。

- 機能をフェーズごとに段階的に実装する。
- 機能セット全体を一度に実装する。
- 最初に MVP アプリ機能を実装し、その後に追加の機能を構築する。

GitHub Spec Kit の実装ワークフローでは、tasks.md ファイルを使用して実装プロセスをガイドする方法を示します。

このタスクでは、実装戦略をレビューし、`speckit/implement` を使用してアプリケーションの MVP バージョンを実装します。

そのためには、以下の手順を実行してください。

1. **tasks.md** ファイルを開き、**実装戦略**セクションを見つけて、推奨される MVP 戦略を確認します。

    MVP (または MVP 優先) 戦略は、作業機能を可能な限り迅速に提供することを目的としています。 最初のユーザー ストーリー (US1) を構築する前に、まず "セットアップ" (初期化) フェーズと "基盤" (ブロック) フェーズを完成することに重点を置く必要があります。

    たとえば、MVP 実装戦略は次の例のようになります。

    ```plaintext
    **Phases**: Setup → Foundation → US1 only  
    **Tasks**: T001 - T037 (37 tasks)  
    **Deliverable**: Users can add a known-good feed URL; refresh; see items; restart and confirm persistence.
    ```

1. チャット ビューで、MVP 戦略の実装ワークフローを開始するコマンドを入力します。

    機能の MVP バージョンを実装するために必要なタスクの範囲を指定するコマンドを作成します。 tasks.md ファイルの [実装戦略] セクションで指定したタスク範囲を使用するか、各フェーズに一覧表示されているタスクに基づいて計算してください。

    > **重要**:入力するコマンドは、tasks.md ファイルで定義されている特定のタスク範囲を参照する必要があります。

    たとえば (前の手順の MVP 実装例を参照)、次のコマンドを入力します。

    ```dotnetcli
    /speckit.implement Implement the MVP strategy (Tasks: T001 - T037)
    ```

    このコマンドは、RSS Feed Reader アプリの MVP バージョンに必要なタスクの実装を開始するように GitHub Copilot に指示します。

    この演習では、1 回の /speckit.implement コマンドで、MVP 戦略のすべてのタスクを実装する必要があります。 これは、tasks.md ファイル内の MVP に関連付けられるタスクとフェーズの数を予測できないためです。 RSS リーダーのシナリオは比較的単純とはいえ重要であり、実装が完了するまでに 20 分以上かかる場合があります (手動コーディングよりも高速ですが、AI が作業をデバッグするまでしばらく待つ必要があります)。 運用環境では、最初にセットアップと基本のフェーズのタスクを実装し、その後ユーザー ストーリーのフェーズのタスクを一度に 1 つずつ実装するなど、段階的なアプローチに従う場合があります。

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    エージェントは、tasks.md ファイルで定義されている順序に従って、機能をタスクごとに段階的に構築します。

    > **注**:GitHub Copilot は、実装中に自身の作業を入念に検証しており、これは大変好ましい点です。 GitHub Copilot は、実装プロセス中もあなたを巻き込みながら進めます。 支援の要求は頻繁に発生します。 実装を完了するために必要な時間は、支援の要求にどの程度迅速に対応するかによって影響を受ける可能性があります。

1. タスクを検証するために手動テストが必要な場合は、チャット ビューで説明されている手順を実行し、結果を GitHub Copilot に報告します。

    手動テスト中に問題が発生する可能性があります。 次に例を示します。

    1. GitHub Copilot は、フィード URL が正しく動作していることを確認するために手動テストが必要であることを示します。
    1. バックエンド アプリケーションは既に `http://localhost:5000` でローカルに実行されています。
    1. Visual Studio Code のターミナルを使用して、フロントエンド アプリケーションを起動します。 フロントエンド アプリケーションが正常に起動し、`http://localhost:5239`で実行されています。
    1. ブラウザーを開き、フロントエンド アプリケーション (たとえば、`http://localhost:5239`) に移動します。
    1. ページが読み込まれると、"未処理のエラーが発生しました。 再度読み込みます" というエラーが報告されます。
    1. ブラウザーの開発者ツールを開いて、さらに調査します。
    1. 開発者ツール コンソールに、次のエラー メッセージが表示されます。

        ```plaintext
        "Unhandled exception rendering component: Object of type 'Microsoft.AspNetCore.Components.Routing.Router' does not have a property matching the name 'NotFoundPage'.
        System.InvalidOperationException: Object of type 'Microsoft.AspNetCore.Components.Routing.Router' does not have a property matching the name 'NotFoundPage'.
        at Microsoft.AspNetCore.Components.Reflection.ComponentProperties.ThrowForUnknownIncomingParameterName(:5239/Type targetTyp…tring parameterName)
        at Microsoft.AspNetCore.Components.Reflection.ComponentProperties.SetProperties(:5239/ParameterView&…ters, Object target)
        at Microsoft.AspNetCore.Components.ParameterView.SetParameterProperties(:5239/Object target)
        at Microsoft.AspNetCore.Components.Routing.Router.SetParametersAsync(:5239/ParameterView parameters)"
        ```

    この時点で、GitHub Copilot に問題を報告する必要があります。

    1. Visual Studio Code のチャット ビューに戻ります。
    1. チャット ビューで問題を報告します。 次に例を示します。

        ```plaintext
        I opened the application in the browser at http://localhost:5239. When the page opens, I see an error message: "An unhandled error has occurred. Reload". When I check the browser's developer tools console, I see the following error message: "Unhandled exception rendering component: Object of type 'Microsoft.AspNetCore.Components.Routing.Router' does not have a property matching the name 'NotFoundPage'". Can you help me troubleshoot this issue?
        ```

    問題を報告すると、GitHub Copilot により、提供した情報を使用してデバッグが開始されます。 動作内容などの詳細な説明は、GitHub Copilot が問題をより深く理解するのに役立ちます。 一部の問題については、GitHub Copilot で解決するために、具体的なエラー メッセージなどの追加の詳細が必要になる場合があります。 問題の診断 (や解決) に役立てるために GitHub Copilot から要求された追加情報を提供します。

    問題が解決されるまで、支援を提供し続けます。 問題が解決したら、GitHub Copilot から、手動テストを再開するように求められます。

1. MVP アプリケーションに必要なすべてのタスクが完了するまで、実装ワークフローを続行します。

    MVP の実装が完了すると、GitHub Copilot はチャット ビューで通知します。

1. プロジェクト ファイルに加えられたすべての変更を受け入れます。

    このラボ演習では、レビューを行わずに、GitHub Copilot によって行われたすべての変更を受け入れても問題ありません。 ただし、運用環境では、すべてのコード変更を慎重にレビューして、品質基準を満たし、プロジェクトの要件に合致していることを確認することが重要です。

1. 更新されたすべてのファイルを保存します。

1. GitHub Copilot に対して、1 回のクリックで両方のアプリを起動するために使用できる VS Code の "実行とデバッグ" 起動構成を作成するように依頼します。

    たとえば、チャット ビューで次のプロンプトを入力します。

    ```plaintext
    Create a VS Code "Run and Debug" launch configuration that I can use to start both apps with one click. Provide instructions for running the apps using the launch configuration.
    ```

1. 起動構成を使用して、バックエンドアプリケーションとフロントエンド アプリケーションを実行します。

    エラーが発生した場合は、支援を受けるためにエラー メッセージの詳細な説明と関連するログを GitHub Copilot に提供してください。

1. ブラウザーを開き、フロントエンド アプリケーションの [サブスクリプション] ページに移動します。

    フロントエンド URL は `http://localhost:5239/subscriptions` のようになるはずです。

1. 数分かけて、MVP アプリケーションの受け入れシナリオを検証します。

    アプリケーションのテストに仕様できるフィードをいくつか次に示します。

    - https://devblogs.microsoft.com/dotnet/feed/ (.NET ブログ)
    - https://devblogs.microsoft.com/visualstudio/feed/ (Visual Studio ブログ)
    - https://devblogs.microsoft.com/powershell/feed/ (PowerShell Team ブログ)
    - https://devblogs.microsoft.com/azure-sdk/feed/ (Azure SDK ブログ)
    - https://devblogs.microsoft.com/commandline/feed/ (Windows コマンド ラインおよび Windows Terminal)
    - https://devblogs.microsoft.com/oldnewthing/feed/ (The Old New Thing)
    - https://learn.microsoft.com/en-us/shows/feed.xml (Microsoft Learn およびショー)

    受け入れシナリオは、spec.md ファイルで確認できます。 「**ユーザー シナリオとテスト**」セクションに記載された受け入れシナリオ。 MVP アプリケーションは通常、spec.md ファイルの最初のユーザー ストーリー (US1) に関連付けられています。

    たとえば、MVP アプリケーションの受け入れシナリオは、次の例のようになります。

    1. (**Given**) ユーザーがサブスクリプションを持っていない場合、(**When**) ユーザーが有効なフィード URL を追加して "今すぐ更新" をトリガーすると、(**Then**) アプリはフィードをフェッチし、項目をローカルに格納し、項目を最新のものから順に表示する。
    2. (**Given**) サブスクリプションが存在する場合、(**When**) ユーザーがアプリを閉じてもう一度開くと、(**Then**) サブスクリプションと以前にフェッチされた項目は、更新を必要とせずに引き続き使用できる。
    3. (**Given**) サブスクリプションがごく最近更新された場合、(**When**) ユーザーがすぐに "今すぐ更新" をもう一度トリガーすると、(**Then**) アプリは要求を調整し、更新が実行されなかった理由を説明する。
    4. (**Given**) フィードのフェッチに失敗した場合 (タイムアウト、リダイレクト、禁止、または形式の誤りの応答)、(**When**) ユーザーが "今すぐ更新" をトリガーすると、(**Then**) アプリは、フィードごとの明確なエラーを表示し、引き続き使用できる。

    また、MVP 実装の手動テストを実行するために必要な手順について、GitHub Copilot に質問することができます。 たとえば、チャット ビューで次のプロンプトを入力します。

    ```plaintext
    Can you provide the steps required to manually test the MVP implementation?
    ```

    Visual Studio Code を使用してアプリケーションを実行し、RSS Feed Reader 機能を手動でテストして、期待どおりに動作することを確認します。

1. 手動テストの結果を GitHub Copilot に報告します。

1. MVP アプリケーションの受け入れシナリオがすべて成功するまで、手動テストと結果の報告を続けます。

1. MVP アプリケーションのテストが正常に終了したら、実装ファイルをコミットして同期します。

> **注**:時間に余裕があれば、MVP の範囲を超えた追加タスクの実装を続行できます。 次の一連のタスクを続行するように GitHub Copilot に指示するか、次に実装する特定のタスクを手動で選択することができます。

主な観察内容:

- GitHub Copilot はワークスペース内の *spec.md*、*plan.md*、*tasks.md* ファイルを参照するため、仕様に合致するコードが生成されます。
- 仕様要件に基づく詳細なコメントは、GitHub Copilot で正確な実装を生成するためのガイドとなります。
- 仕様駆動型の開発アプローチを使用すると、要件 (ファイル サイズの制限、サポートされる種類など) が明示的に文書化されるため、要件を忘れることがなくなります。
- 明確な受け入れ基準を設定すると、実装が要件を満たしていることを簡単に確認できます。

完全な実装では、tasks.md ファイル内の残りのすべてのタスクを続行し、段階的なアプローチを使用して完全な機能を体系的に構築します。 仕様駆動開発のアプローチにより、要件に集中し、スコープの不備や機能の欠落を防ぐことができます。

## クリーンアップ

演習が完了したので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに保持したくない変更が加えられていないか確認します。 たとえば、ContosoDashboard リポジトリを削除するとします。 ローカル PC をラボ環境として使用している場合は、演習中にインストールされた可能性のあるツールはすべて保持してください。 この演習用に作成したリポジトリのローカル クローンは、アーカイブまたは削除できます。
