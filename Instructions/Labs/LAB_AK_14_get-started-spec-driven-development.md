<!-- ---
lab:
    title: 'Exercise - Develop a greenfield application using GitHub Spec Kit'
    description: 'Learn how to apply the spec-driven development methodology with GitHub Spec Kit for a greenfield application project. in Visual Studio Code.'
--- -->

# GitHub Spec Kit を使用してグリーンフィールド アプリケーションを開発する

GitHub Spec Kit は、仕様と GitHub Copilot などの AI コーディング アシスタントを統合することで、仕様駆動開発 (SSD) を有効にするオープンソース ツールキットです。

この演習では、GitHub Spec Kit を使用して新しいグリーンフィールド アプリケーションを開発する方法を学習します。 まず、新しい .NET プロジェクト用に GitHub Spec Kit を初期化します。 次に、GitHub Spec Kit ワークフローを使用して、新しいアプリケーションの規約、仕様、計画、タスクのドキュメントを作成します。 最後に、GitHub Spec Kit の実装ワークフローを使用して、アプリケーションの初期 MVP バージョンを実装します。

この演習の所要時間は約 **75** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。Git 2.48 以降、.NET SDK 8.0 以降、C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code、SQL Server LocalDB、Python 3.11 以降、uv パッケージ マネージャー、GitHub Copilot が有効な GitHub アカウントへのアクセス。

ラボ環境を構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2345907" target="_blank">GitHub Spec Kit のラボ環境を構成する</a>。

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 この会社では、Visual Studio Code で GitHub Spec Kit と GitHub Copilot を使用して仕様駆動開発 (SDD) 手法に移行しているところです。 クライアントである Contoso Corporation からは、社内従業員向けの RSS フィード リーダーを開発するように依頼されています。 会社は、アプリケーションの MVP バージョンを迅速に配布でき、顧客のサインオフ後に追加機能をシームレスにロールアウトできるようするために、SDD 手法と GitHub Spec Kit を使用することを求めています。

Contoso の利害関係者は、プロジェクトの目標、機能、技術の要件を文書化しました。 この情報を使用して、開発プロセスをガイドする規約、仕様、計画、タスクのドキュメントを作成する必要があります。

この演習には、次のタスクが含まれています。

1. プロジェクト フォルダーを作成し、GitHub Spec Kit を初期化します。
1. プロジェクト ファイルを確認し、利害関係者のドキュメントを準備します。
1. 標準とガイドラインに基づいて、規約を生成します。
1. 利害関係者の要件を使用して spec.md ファイルを生成します。
1. 利害関係者の要件と仕様を使用して、plan.md ファイルを生成します。
1. 仕様、計画、規約を使用して tasks.md を生成します。
1. MVP アプリケーションに必要なタスクを実装する。

## プロジェクト フォルダーを作成し、GitHub Spec Kit を初期化する

グリーンフィールド プロジェクトで作業していますが、GitHub Spec Kit を初期化する前にプロジェクト フォルダーが必要です。

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

    この `specify init` コマンドは、次のコンポーネントを使用します。

    - `--here` - 現在のディレクトリ (既存の RSSFeedReader プロジェクト) で GitHub Spec Kit を初期化します。
    - `--ai copilot` - GitHub Copilot のプロジェクトを構成します。
    - `--script ps` - PowerShell スクリプトを使用します。

    ブラウンフィールド プロジェクトの場合、`specify init` コマンドは、現在のディレクトリが空ではないことを認識し、続行するかどうかの確認を求めます。

    `specify init` コマンドは、次のアクションを完了します。

    - `.github/agents/` ディレクトリと `.github/prompts/` ディレクトリにエージェント プロンプト ファイルを作成します。
    - `.specify/memory/` ディレクトリと `.specify/templates/` ディレクトリにテンプレート ファイルを作成します。
    - `.specify/scripts/powershell/` ディレクトリにスクリプト ファイルを作成します。
    - `.vscode/` ディレクトリ内の settings.json ファイルを更新または作成します。
    - 既存のアプリケーション ファイルを保持します。
    - 成功メッセージ ("Project ready") を表示します。
    - 次に行う手順をいくつか提示しますが、これらは省略できます。

1. Visual Studio Code で RSSFeedReader プロジェクトを開くには、次のコマンドを入力します。

    ```powershell
    code .
    ```

    `code .` コマンドは、Visual Studio Code で現在のディレクトリ (RSSFeedReader) を開きます。

1. Visual Studio Code にプロジェクトが完全に読み込まれるまで待ちます。

1. 少し時間を取ってプロジェクト構造を確認します。

    Visual Studio Code のエクスプローラー ビューを使用して、アプリケーション フォルダーを展開します。 次の例のようなフォルダー構造が表示されます。

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

    > **注:** このラボ演習は、GPT-5.2 モデルと Claude Sonnet 4.5 モデルを使用してテストされました。 これらの 2 つのモデルの結果はよく似ています。 ただし、GPT-4 および GPT-5 ミニ モデルを使用すると、矛盾した予期しない結果が生成されました。 GitHub Spec Kit コマンドを実行するときに複雑な推論のために最適化された新しい言語モデルを使用することをお勧めします。

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

    **トラブルシューティング**:問題が発生した場合:

    - **"specify command not found"**:タスク 1 が完了し、Specify CLI がインストールされていることを確認します。 `specify version` を実行してインストールを確認します。
    - **アクセス許可が拒否されたエラー**:Windows では、適切なアクセス許可で PowerShell を実行していることを確認します。 macOS/Linux では、ファイルのアクセス許可を確認します。
    - **git clone エラー**:GitHub にサインイン済みで、インポートしたリポジトリへのアクセスがあることを確認します。
    - **GitHub Spec Kit コマンドが表示されない**:ワークスペースのルートに `.github/prompts/` が存在することを確認します。 Visual Studio Code を再度読み込んでみてください。

## 利害関係者ドキュメントをダウンロードして確認する

このタスクでは、Contoso の利害関係者から提供された RSSFeedReader ドキュメントをダウンロードし、プロジェクトに追加して、ドキュメントが GitHub Spec Kit コマンドとどのように関連しているかを評価します。

そのためには、以下の手順を実行してください。

1. 利害関係者ドキュメントをダウンロードするには、ブラウザーで次のリンクを開きます。[RSSFeedReader - 利害関係者ドキュメント](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHSpecKitEx14StakeholderDocuments.zip)。

1. ダウンロードした ZIP ファイルが格納されているフォルダーを開きます。

1. ダウンロードした ZIP ファイルの内容を一時フォルダーに抽出します。

1. Visual Studio Code のエクスプローラー ビューで、RSSFeedReader プロジェクトのルート フォルダーを右クリックし、**[エクスプローラーで表示]** (macOS の場合は **[Finder で表示]**) を選択します。

1. エクスプローラー (または Finder) で、ZIP ファイルを抽出した一時フォルダーを開きます。

1. 一時フォルダー内のすべてのファイルを選択し、コピーして、RSSFeedReader プロジェクトのルート フォルダーに貼り付けます。

    抽出されたファイル フォルダーの内容は次のとおりです。

    ```plaintext
    GHSpecKitEx14StakeholderDocuments (root)
    ├── StakeholderDocuments        (folder containing stakeholder supplied documents)
    └── README.md                   (a readme file describing the project)
    ```

1. Visual Studio Code に戻ります。

1. Visual Studio Code のエクスプローラー ビューで、**StakeholderDocuments** フォルダーを展開します。

    StakeholderDocuments フォルダーには次のファイルが格納されています。

    - **Project Goals.md** - プロジェクト目標の概要、目的、スコープ、提供アプローチ、ロールアウト計画、品質目標、標準/ガイドライン。
    - **App Features.md** - ユーザー向け機能要件の詳細。
    - **Tech Stack.md** - テクノロジの選択とアーキテクチャの根拠。
    - **MVP System Rules.md** - 仕様策定の基礎となる MVP システムの動作規則。

1. 少し時間をとり、各利害関係者ドキュメントを開いて確認してください。

    これらのドキュメントには、プロジェクトの目標、機能、技術要件、制約に関する自然言語による説明が記載されています。 このコンテキストを理解することは、効果的な仕様、計画、タスクを作成するために不可欠です。 詳細レベルはドキュメントによって異なりますが、全体的な構成は、多くの実際のプロジェクトで見られる一般的な内容です。

    ドキュメントの数やドキュメントで提供される詳細は、会社のポリシーとプロジェクトの複雑さに応じて大きく変わります。 GitHub Spec Kit コマンドは、使用できるファイルと詳細を処理し、その情報を使用して、仕様駆動型開発プロセスを成功させるために必要な規約、仕様、計画、タスクのドキュメントを作成するように設計されています。

1. 少し時間をとり、各ドキュメントが GitHub Spec Kit コマンドとどのように関連しているかを検討してください。

    各利害関係者ドキュメントには、仕様駆動型開発プロセスのさまざまな側面を導くのに役立つ情報が提供されています。

    次に例を示します。

    - **Project Goals.md**:このドキュメントには、constitution.md ファイルの参考情報となる目標の概要と標準が記載されています。
    - **App Features.md**:このドキュメントには、spec.md ファイルの作成に利用される詳細なユーザー向け要件が記載されています。
    - **Tech Stack.md**:このドキュメントには、plan.md ファイルに影響を与えるテクノロジの選択とアーキテクチャの根拠の概要が記載されています。
    - **MVP System Rules.md**:このドキュメントには、tasks.md 内の実装タスクをガイドするシステム動作規則が定義されています。

## 標準とガイドラインに基づいて、規約を生成する

GitHub Spec Kit では、constitution.md ファイルを使用して、RSSFeedReader プロジェクトに関するすべての開発判断を導く管理原則と制約を定めています。 これにより、実装全体で従う必要がある組織のポリシー、技術標準、セキュリティ要件、開発プラクティスが取り込まれます。

このタスクでは、GitHub Copilot の `/speckit.constitution` コマンドを使用して、最初はユーザーが指定したインライン テキスト入力、次に利害関係者ドキュメントを使用して、包括的な規約を生成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューを使用して、**.github/agents** フォルダーと **.specify/memory** フォルダーを展開します。

    これらのフォルダーには、constitution.md ファイルの作成に使用される GitHub Spec Kit リソースが格納されています。 規約ファイルの作成に取りかかる前に、これらのリソース ファイルに目を通しておくことをお勧めします。

1. **.github/agents** フォルダー内の **speckit.constitution.agent.md** ファイルを開きます。

1. 少し時間を取って **speckit.constitution.agent.md** ファイルを確認します。

    このマークダウン ファイルで提供されている詳細な手順に注意してください。 これらの手順は、GitHub Copilot によって constitution.md ファイルを生成するために使用されます。 エージェントは、体系的なアプローチに従い、主要な原則と制約を捉えた規約を生成します。

1. **.specify/memory** フォルダー内の **constitution.md** ファイルを開きます。

    constitution.md ファイルの初期バージョンには、規約用の既定のテンプレートが含まれます。

1. 少し時間を取って **constitution.md** テンプレートをレビューします。

    このテンプレートが、原則と制約を示すコンテンツの例で初期化されていることに注意してください。 このテンプレートには、セキュリティ、パフォーマンス、品質、技術の標準などの例が含まれています。

    規約ファイルは開いたままにしておくことができます。

1. チャット ビューが開いていることを確認してから、新しいチャット セッションを開始します。

    **[新しいチャット]** ボタン (チャット パネルの上部にある **+** アイコン) を選択すると、新しいセッションを開始できます。 新しいチャット セッションを開始すると、コンテキストがクリーンな状態になります。

1. 少し時間をとって、/speckit.constitution コマンドを実行する際のオプションを検討してみましょう。

    次のオプションを使用して /speckit.constitution コマンドを実行できます。

    - `/speckit.constitution --text "..."`:インライン テキストを使用して、規約に含めるべき標準、ガイドライン、原則、制約を記述します。
    - `/speckit.constitution --files ...`:規約作成のコンテキストを提供するプロジェクト ドキュメントを指定します。
    - `/speckit.constitution --text "..." --files ...`:インライン テキスト入力とプロジェクト ドキュメントを組み合わせます。
    - `/speckit.constitution`:入力せずにコマンドを実行します。 ブラウンフィールド プロジェクトの場合、ワークフローはコードベースを使用して標準、ガイドライン、要件を特定し、規約を生成します。

    > **注**:/speckit.constitution コマンドを同じプロジェクト内で複数回実行して、constitution.md ファイルを改良または拡張することができます。 詳細な情報を入力すると、より正確で包括的な規約が作成されます。

1. [チャット] ビューで、インライン テキストを使用して規約ワークフローを開始するには、次のコマンドを入力します。

    ```plaintext
    /speckit.constitution --text "Code projects emphasize security, accessibility, performance, and code quality. Ensure that all principles are specific, actionable, and relevant to the project context."
    ```

    この場合、インライン テキストは、規約を作成するための大まかなガイダンスとして機能します。 このアプローチは、詳細なプロジェクト ドキュメントなしでコア原則を迅速に確立したい場合に役立ちます。

1. [チャット] ビューで GitHub Copilot の応答を監視します。

1. 更新された Constitution.md ファイルをエディターで確認します。

    GitHub Copilot によって Constitution.md ファイルが更新され、指定したインライン テキストに基づいた原則が組み込まれていることに注目してください。 原則は明確に記述され、実行可能なものである必要があります。

    実際のプロジェクトでは、次の条件に照らして規約を確認することが重要です。

    - 完全性:すべての主要な領域 (セキュリティ、パフォーマンス、品質、技術基準) がカバーされている。
    - 明確さ:各原則は具体的で明確である。
    - 一貫性: 原則は相互に矛盾していない。
    - 関連性:すべての原則は RSSFeedReader プロジェクトに関連しています。

1. /speckit.constitution ワークフローによって **templates** フォルダー内のファイルが更新された場合は、それらのファイルも確認します。

    規約ワークフローでは、他の GitHub Spec Kit ファイル (spec.md、plan.md、tasks.md) のテンプレートも更新される可能性があります。 更新は、constitution.md ファイルに定義されている原則を反映しているはずです。 これにより、すべてのプロジェクト ドキュメントの整合性が確保されます。

1. 更新されたすべてのファイルへの変更を承認するには、[チャット] ビューで **[保持]** ボタンを選択します。

    エディターで **[保持]** ボタン オプションを選択して、個々のファイルへの変更、またはファイル内の個々の変更を受け入れることもできます。

1. [チャット] ビューで、利害関係者ドキュメントを使用して 2 つ目の規約ワークフローを開始するには、次のコマンドを入力します。

    ```plaintext
    /speckit.constitution --files StakeholderDocuments/ProjectGoals.md StakeholderDocuments/AppFeatures.md StakeholderDocuments/TechStack.md StakeholderDocuments/MVPSystemRules.md
    ```

1. GitHub Copilot の応答を監視します。

    constitution.md ファイルが更新されると、GitHub Copilot のチャット ビューに進行状況が表示されます。

    GitHub Copilot でプロジェクトの要件が分析され、規約のドキュメントが作成されるまでに 1 から 2 分かかる場合があります。 ワークフローが他の GitHub Spec Kit ファイル (spec.md、plan.md、tasks.md) のテンプレートを更新する場合は、変更を確認せずに更新を受け入れることができます。 これらのファイルは、後のタスクで生成します。

    > **注**:GitHub Copilot から、ファイルへのアクセスまたは編集ができないという報告があった場合は、Visual Studio Code の **[設定]** を開き、**[機能]** を展開して **[チャット]** を選択し、**[チャット] > [エージェント]** が有効になっていることを確認します。

1. 更新された constitution.md ファイルをエディターで確認します。

    ベスト プラクティス:エージェントによって作成された提案を常に確認してください。

    GitHub Copilot によって規約が更新されたら、ドキュメントをレビューして、要件が正確に反映されていることを確認します。 ビジネス要件と技術ガバナンスを規約で表す運用環境で作業している場合、この手順は重要です。 トレーニング演習では、このレビューは、主に規約の内容を十分に理解するのに役立ちます。

    GitHub Copilot により、ContosoDashboard プロジェクトの基本原則が認識され、それが規約に組み込まれていることに注目してください。 この規約により、仕様駆動型の開発アプローチが適用され、トレーニング アプリと運用コードの違いが認識されます。

    各原則は明確に記述され、実行可能なものである必要があります。 次に例を示します。

    - ❌ あいまい:"適切なセキュリティ プラクティスを使用する" というのは一般的すぎます。
    - ✅ 明確:"すべての API エンドポイントで認証トークンを検証し、ロールベースのアクセス許可を適用する必要があります" というのは、具体的かつ実用的です。

    重要な要件が欠落しているか不確かな場合は、constitution.md ファイルを直接編集して原則を追加または変更できます。

1. 規約ドキュメントが完成したことを確認してから、変更を受け入れます。

    実際のプロジェクトでは、次の条件に照らして規約を確認してから保存することが重要です。

    - 完全性:すべての主要な領域 (セキュリティ、パフォーマンス、品質、技術基準) がカバーされている。
    - 明確さ:各原則は具体的で明確である。
    - 一貫性: 原則は相互に矛盾していない。
    - 関連性:すべての原則が ContosoDashboard プロジェクトに関連している。

1. **constitution.md** ファイルを保存して閉じます。

1. 更新されたファイルを Git リポジトリにコミットしてプッシュします。

    たとえば、constitution.md ファイルが更新された唯一のファイルである場合は、ターミナルで次のコマンドを使用できます。

    ```powershell
    git add constitution.md
    git commit -m "Add project constitution with development principles and constraints"
    git push
    ```

    ブラウザーで GitHub リポジトリを確認して、コミットを検証できます。 これで、constitution.md ファイルがコミット メッセージと共に表示されます。

この規約は、ビジネス要件と技術実装の間の "契約" として機能し、仕様駆動開発のプロセス全体の一貫性を確保します。 GitHub Spec Kit を使用して仕様、計画、タスクを生成する場合、これらの原則が参照され、実装が、指定された要件と合致していることが確認されます。

## 利害関係者の要件と規約を使用して機能仕様を作成する

仕様 (spec.md) は、ユーザーの観点から何を構築するかを定義します。 機能、ユーザー ストーリー、受け入れ基準、ビジネス要件について説明しますが、実装方法は規定しません。 適切に記述された仕様は、実装計画とタスクを作成するための基礎として機能します。

このタスクでは、GitHub Copilot の `/speckit.specify` コマンドを使用して、Contoso のビジネス利害関係者によって提供された要件に基づいて、"ドキュメントのアップロードと管理機能" の詳細な仕様を生成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューで、**.github/agents** フォルダー内にある **speckit.specify.agent.md** ファイルを開きます。

1. 少し時間を取って **speckit.specify.agent.md** ファイルを確認します。

    GitHub Copilot に提供されている詳細な手順に注目してください。 エージェントにより、体系的なアプローチに従って、要件を明確に定義した仕様ファイルが生成されます。

1. Visual Studio Code のエクスプローラー ビューで **StakeholderDocs** フォルダーを展開し、**document-upload-and-management-feature.md** ファイルを開きます。

1. 少し時間を取って、**document-upload-and-management-feature.md** ファイルを読みます。

    document-upload-and-management-feature.md ファイルには、ContosoDashboard アプリケーションに追加する機能に関する詳細な利害関係者の要件が含まれています。 明確で詳細な要件は、効果的な仕様を作成するために不可欠です。

    このドキュメントでは、Contoso Corporation の従業員が、ContosoDashboard アプリケーション内で作業関連のドキュメントをアップロード、整理、共有できる機能を必要としていることが説明されています。 機能は、一元化された安全なロールベースのドキュメント管理ソリューションを提供して、ドキュメントが複数の場所に分散して保存されていることに伴う現在の課題に対処する必要があります。 このドキュメントでは、機能が、将来の Azure クラウド移行のためにクリーンな抽象化を維持しながら、トレーニング目的で、オフラインで動作する必要があることが示されています。 仕様では、6 つのコア要件領域 (アップロード機能、整理と参照、アクセス管理、既存の機能との統合、パフォーマンス要件、レポート) と詳細な技術規約を定義して、実装が、運用デプロイ用のインターフェイス ベースの抽象化を使用してオフライン優先アーキテクチャ パターンに確実に従うようにしています。 機能がユーザーのニーズとビジネス目標を満たすことを保証するために、パフォーマンス目標と成功メトリックを提供します。

    機能の包括的な説明は、あらかじめ準備しておくのが最善です。 ただし、StakeholderDocs フォルダーに含まれているような詳細な要件ドキュメントがない場合は、主要な機能、制約、成功基準を明確に示した簡潔な説明を使用してみることもできます。 たとえば、ドキュメントのアップロードと管理機能については、次のような (簡略化された) 説明を使用できます。

    ```plaintext
    Feature: Document Upload and Management for ContosoDashboard
    
    Enable employees to upload work-related documents (PDF, Office, images, text), organize by category/project, share with team members, and search efficiently. Must integrate with existing dashboard features while maintaining security.
    
    Target Users: All 5,000 Contoso employees with role-based access (Employee, Team Lead, Project Manager, Administrator).
    
    Core Capabilities:
    1. Upload: Multiple files, max 25 MB each, supported types (PDF, Office docs, images, text), metadata (title, category, description, project, tags), progress indicator, virus scanning.
    2. Organization: My Documents view, Project Documents view, search by title/description/tags/uploader/project (results under 2 seconds).
    3. Management: Download, in-browser preview (PDF/images), edit metadata, replace files, delete documents, sharing with notifications.
    4. Integration: Attach to tasks, dashboard Recent Documents widget, notifications for sharing/new project docs.
    5. Performance: Upload in 30s (25 MB files), list load in 2s (500 docs), search in 2s, preview in 3s.
    6. Audit: Log all uploads/downloads/deletions/sharing, admin reports.
    
    Security: Azure Blob Storage encryption at rest, TLS 1.3 in transit, RBAC enforcement, virus scanning.
    
    Success Criteria: 70% adoption in 3 months, find docs under 30s, 90% properly categorized, zero security incidents.
    
    Constraints: Azure Blob Storage, ASP.NET Core integration, 8-10 week timeline, Entra ID authentication.
    
    Out of Scope: Version history, storage quotas, soft delete/trash, collaborative editing, external integrations, mobile apps.
    ```

1. チャット ビューが開いていることを確認します。

    GitHub Copilot が、現在のチャット セッションで以前の対話のコンテキストを保持していることに注意してください。 現在のセッションで constitution.md ファイルを生成した場合、GitHub Copilot のチャット ビューの下部付近に **[ビルド仕様]** ボタンが表示され、これを使用して仕様の生成を開始できます。 この場合は、要件ドキュメントを明示的に指定する必要があるため、[ビルド仕様] ボタンは使用しません。

1. チャット ビューで、利害関係者ドキュメントから仕様を生成する指定ワークフローを開始するには、次のコマンドを入力します。

    ```plaintext
    /speckit.specify --file StakeholderDocs/document-upload-and-management-feature.md
    ```

    `--file` オプションを使用して要件ドキュメントを指定しない場合は、構築する機能を説明するように求められます。

1. GitHub Copilot の応答を監視し、必要に応じて支援します。

    > **重要**:GitHub Copilot は、spec.md ファイルを生成するときに支援を必要とします。 たとえば、GitHub Copilot は、新機能用のリポジトリ ブランチを作成するためのアクセス許可を要求します。 要求されたら、チャット ビューで応答してアクセス許可を付与します。

    spec.md ファイルの作成と検証には 4 から 6 分かかる場合があります。

1. 指定ワークフローが完了したら、Visual Studio Code のエクスプローラー ビューを使用して、**specs** フォルダーと **checklists** フォルダーを展開します。

1. エクスプローラー ビューで **spec.md** を選択し、数分かけて spec.md ファイルを確認します。

    spec.md ファイルには、利害関係者の要件に基づいたドキュメントのアップロードと管理機能の詳細な仕様が含まれている必要があります。

    仕様は明確かつ包括的で、適切に構造化されている必要があります。 また、技術計画とタスクを作成するための強固な基盤を提供する必要があります。

    spec.md ファイルは、**.specify/templates/spec-template.md** ファイル内にあるテンプレートに基づきます。 更新された spec.md ファイルには、利害関係者の要件に基づいた 'ドキュメントのアップロードと管理機能' について指定した詳細な仕様が含まれている必要があります。

    spec.md ファイルに、仕様テンプレートで定義されている必須セクションが含まれていることを確認します。 次に例を示します。

    - **ユーザー シナリオとテスト**:機能の能力とそのテスト方法に関するユーザー中心の説明。
    - **必要事項**:カテゴリ別にまとめられた、満たす必要がある詳細な要件。
    - **成功基準**:測定可能な結果、前提条件、対象外事項。

1. **spec.md** ファイルをレビューし、(利害関係者の要件ドキュメントの) 主要な要件が Requirements セクションに取り込まれていることを確認します。

    たとえば、次に関連する要件が表示されます。

    - ファイル サイズの制限 (ファイルあたり 25 MB)
    - サポートされているファイルの種類 (PDF、Office ドキュメント、画像、テキスト ファイル)
    - パフォーマンス目標 (2 秒でページ読み込み、30 秒でアップロード)

1. **spec.md** ファイルをレビューし、(ユーザー シナリオに関連付けられた) 受け入れシナリオが具体的でテスト可能であることを確認します。

    受け入れシナリオは、**Given-When-Then** 形式に従う必要があります。 このシナリオでは、成功または失敗の明確な条件を提供する必要があります。 次に例を示します。

    - ✅ 推奨:従業員としてログインしていることを前提として (**Given**)、ドキュメント ページに移動し、有効なメタデータ (タイトルとカテゴリ) を含む 25 MB 未満の PDF ファイルを選択すると (**When**)、その結果として (**Then**)、ドキュメントが正常にアップロードされ、すべてのメタデータが正しく表示された状態で [マイ ドキュメント] リストに表示されます

    - ✅ 推奨:**前提として**従業員が 30 MB のファイルをアップロードしようとした場合、**もし**検証が行われると、**その結果** 25 MB の制限を示すエラーメッセージが表示される

    - ❌ 非推奨:"アップロードが適切に機能する必要がある" や "システムが高速である必要がある" などのあいまいな条件

1. エクスプローラー ビューで **requirements.md** を選択し、少し時間を取って requirements.md ファイルを確認します。

    **requirements.md** ファイルで問題が報告されていないことを確認します。 すべてのチェックリスト項目を正常に通過したことがわかります。

1. 推奨されるファイルの更新を受け入れ、**spec.md** と **requirements.md** ファイルを保存します。

1. 仕様ファイルをコミットし、新しいブランチを Git リポジトリに公開します。

    次に例を示します。

    Visual Studio Code のソース管理ビューを開き、変更をステージングし、「ドキュメントのアップロードと管理機能の仕様を追加する」のようなコミット メッセージを入力して、新しいブランチを Git リポジトリに公開します。

この仕様では、"どのように" を指定するのではなく "何を" を定義します。 プログラミング言語、フレームワーク、データベース スキーマ、またはコード編成は指定しません。これらの実装の詳細は、規約の技術的制約に基づいて計画およびタスク フェーズで決定されます。 この仕様は、ユーザーのニーズとビジネス要件に重点を置いており、技術面以外の関係者とのレビューを容易にします。

## 明確になった要件で仕様を更新する

`/speckit.clarify` コマンドは、仕様のあいまいさ、ギャップ、指定されていない領域を特定するのに役立ちます。 GitHub Copilot で仕様が分析され、対象を絞った質問がされ、技術的な計画フェーズに進む前にすべての要件が明確かつ完全であることが確認されます。

このタスクでは、明確化プロセスを使用して、ドキュメントのアップロードと管理の仕様を調整します。

そのためには、以下の手順を実行してください。

1. GitHub Copilot Chat ビューが開かれていることを確認します。

1. Chat ビューで、明確化プロセスを開始するために、次のコマンドを入力します。

    ```plaintext
    /speckit.clarify
    ```

1. GitHub Copilot の応答を監視し、必要に応じて支援します。

    GitHub Copilot で、spec.md ファイルが分析され、明確にするための質問が必要かどうかが評価されます。

    たとえば、次の例のような質問を受ける場合があります。

    - "ユーザーが、ドキュメントをプロジェクトにアップロードした後にそのプロジェクトから削除された場合、ドキュメントはどうなりますか?"
    - "プロジェクトが削除された場合、そのプロジェクトに関連付けられているすべてのドキュメントはどうなりますか?"
    - "共有ドキュメントが所有者によって削除された場合、その共有ドキュメントにアクセスしていた受信者はどうなりますか?"
    - "ユーザーがファイル名に特殊文字を含むドキュメント (例: Q4 Report (2025) - Finance & Ops.pdf) をアップロードした場合、システムはそれをどのように処理する必要がありますか?"
    - "ドキュメントのアップロード中にディスク ストレージがいっぱいになった場合、システムはどのように応答する必要がありますか?"

    質問は一度に 1 つずつ表示されます。

1. 明確化が必要な場合は、答える前に、各質問を十分に検討します。

    運用環境では、回答には、ビジネス ニーズ、ユーザー エクスペリエンスに関する考慮事項、技術的な制約に関する慎重な分析が反映されている必要があります。 ただし、このトレーニングでは、質問ごとに推奨オプションを選択できます。

    各回答を入力すると、GitHub Copilot により spec.md ファイルが明確化され更新されます。

    > **注**:GitHub Copilot で追加の質問が提示される場合は、それ以上明確にする必要がないことが示されるまで回答を続けてください。 通常、この明確化プロセスでは、GitHub Copilot で仕様が調整される過程で質問のやり取りが 1 から 2 回行われます。

    明確化のプロセスが完了したら、更新された **spec.md** ファイルを確認し、変更内容を確定します。

    - 回答が仕様に正確に反映されていることを確認します
    - 以前はあいまいだった領域が明確な要件になっていることを確認します
    - 明確化に基づいて、新しく追加された受け入れ基準を探します

    必要に応じて、手動で編集します。 たとえば、GitHub Copilot が意図していた答えと異なる解釈をした場合は、仕様を直接編集して修正します。

1. 明確化プロセスによって変更が生じた場合は、更新された **spec.md** ファイルを保存し、変更をコミットして同期します。

仕様が明確で包括的なガイダンスを提供していることを確認することが重要です。 あいまいさに事前に対処すれば、間違ったソリューションを構築したり、開発プロセスの後半で大きな変更を加えたりするリスクを軽減できます。

## 仕様と規約を使用して技術計画を生成する

技術計画は、"what" (仕様) と "how" (実装) の間のギャップを埋めるものです。 アーキテクチャ、テクノロジの選択、データ モデル、API の設計、実装のアプローチを定義し、規約で定義されている制約に従います。

このタスクでは、GitHub Copilot の `/speckit.plan` コマンドを使用して、包括的な技術実装計画を生成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューで、**.github/agents** フォルダー内にある **speckit.plan.agent.md** ファイルを開きます。

1. 少し時間を取って **speckit.plan.agent.md** ファイルをレビューします。

    GitHub Copilot に提供されている詳細な手順に注目してください。 エージェントは体系的なアプローチに従って、技術的な実装戦略の概要を示す計画ファイルを生成します。

    関心がある場合は、**.specify/templates/plan-template.md** ファイルをレビューして、plan.md ファイルに使用される構造を確認することもできます。

1. GitHub Copilot Chat ビューが開かれていることを確認します。

1. Chat ビューで、技術計画プロセスを開始するために、次のコマンドを入力します。

    ```dotnetcli
    /speckit.plan
    ```

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    GitHub Copilot で、constitution.md ファイルと spec.md ファイルが分析されて計画が生成されます。 必要に応じて、アクセス許可と支援を提供します。

    GitHub Copilot で技術計画とそれに関連するマークダウン ファイルが生成されるまでに 6 から 8 分かかる場合があります。

1. 計画ワークフローが完了したら、次のファイルが **specs** フォルダーの下に一覧表示されていることを確認します。

    - **plan.md**
    - **research.md**
    - **quickstart.md**
    - **data-model.md**

    **contracts** フォルダーの下に 1 つ以上のファイルが表示される場合もあります。

1. 少し時間を取って、**research.md**、**plan.md**、**quickstart.md**、**data-model.md** ファイルをレビューします。

    - research.md ファイルには、ドキュメントのアップロードと管理機能に関する調査結果とテクノロジの決定が取り込まれます。
    - plan.md ファイルは、ドキュメントのアップロードと管理機能の技術的な実装計画の概要を示します。
    - quickstart.md ファイルは、セットアップ手順と、実装を開始する方法の概要を提供します。
    - data-model.md ファイルでは、ドキュメントのアップロードと管理機能に必要なデータ エンティティ、プロパティ、リレーションシップを定義します。

    > **注**:運用シナリオの場合、計画によって、技術的なコンテキストの包括的な説明と、新しい機能の明確に定義された実装戦略が提供されることを確認する必要があります。 調査、クイック スタート、データ モデルの各ファイルは、追加のコンテキストと詳細を提供して計画を補完する必要があります。 この演習では、各ファイルに関連付けられているコンテンツを十分に理解することに重点を置きます。

1. ファイルを確認したら、更新内容を確定します。

    計画で重要な詳細が省略されている場合、または同意しない仮定を行う場合は、次のことができます。

    - plan.md ファイルを直接編集するか、
    - GitHub Copilot Chat でフォローアップの質問をします。 次に例を示します。

    ```plaintext
    The plan should include a background job for processing virus scans. Add details about using Azure Functions with Queue Storage triggers to handle async file scanning after upload.
    ```

1. ファイルを保存し、変更をコミットして同期します。

技術計画が、実装のブループリントとして機能するようになりました。 これは、組織の制約を尊重しながら、ビジネス要件を具体的な技術的決定に変換します。

## 仕様、計画、規約を使用してタスク ファイルを生成する

tasks.md ファイルでは、技術計画を具体的で実用的な実装手順に分割します。 各タスクは、妥当な期間内 (AI の支援なしで実装する場合は通常数時間から 1 日) で完了するのに十分な小ささにする必要があり、明確な受け入れ基準を含んでいる必要があります。

このタスクでは、GitHub Spec Kit の `/speckit.tasks` コマンドを使用して、包括的なタスク リストと段階的な実装計画を生成します。

そのためには、以下の手順を実行してください。

1. Visual Studio Code のエクスプローラー ビューで、**.github/agents** フォルダー内にある **speckit.tasks.agent.md** ファイルを開きます。

1. 少し時間を取って **speckit.tasks.agent.md** ファイルをレビューします。

    GitHub Copilot に提供されている詳細な手順に注目してください。 エージェントは、体系的なアプローチに従って、実装計画を管理可能なタスクに分割する tasks.md ファイルを生成します。

1. GitHub Copilot Chat ビューが開かれていることを確認します。

1. チャット ビューで、tasks.md ファイルの生成を開始するには、次のコマンドを入力します。

    ```dotnetcli
    /speckit.tasks
    ```

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    GitHub Copilot で、spec.md と plan.md ファイルが分析され、tasks.md ファイルにタスクが生成されます。

    GitHub Copilot で tasks.md ファイルが生成されるまでに 3 から 4 分かかる場合があります。 必要に応じて、アクセス許可と支援を提供します。

1. タスク ワークフローが完了したら、少し時間を取って、**tasks.md** ファイルをレビューします。

    tasks.md ファイルは、フェーズとユーザー ストーリー別に整理されたタスクの一覧を提供する必要があります。

    タスクが、仕様と計画の要件を満たしていることを確認します。 次に例を示します。

    - 各機能要件は、1 つ以上のタスクにマップする必要があります。
    - セキュリティ要件には、対応する実装タスクが必要です。
    - パフォーマンス要件には、テスト タスクが必要です。
    - 統合ポイントには、専用のタスクが必要です。

    タスクが論理的に順序付けされていることを確認します。 次に例を示します。

    - 基礎タスク (データベース、モデル) を最初に実行します。
    - バックエンド API タスクは、基礎上に構築します。
    - フロントエンド タスクは、バックエンド エンドポイントを参照します。
    - テスト タスクは、実装後に実行します。
    - デプロイ タスクは、最後に実行します。

1. 各タスクが具体的でアクション可能であることを確認します。

    - ✅ 推奨:"次のプロパティを使用してドキュメント エンティティを作成する:DocumentId、Title、Description、FileName、FileSize、BlobStorageUrl"
    - ❌ あいまい:"データベース関連を設定する"

    タスクに適切なスコープがあることを確認します。

    - 開発者は、数時間から 1 日で個々のタスクを完了できる必要があります。
    - タスクが大きすぎると思われる場合は、実装時に分割することが必要になる場合があります。

    必要に応じて、タスクの依存関係またはメモを追加します。 次に例を示します。

    ```markdown
    - [ ] Task 12: Implement DocumentController POST /api/documents endpoint
      - Depends on: Task 11 (DocumentService)
      - Note: Include comprehensive error handling for file size limits and unsupported types
    ```

1. 推奨されるファイルの更新を受け入れて、**tasks.md** ファイルを保存します。

1. 変更をコミットし、更新を同期します。

これで、tasks.md ファイルにより、実装の明確なロードマップが提供されます。

## MVP アプリケーションに必要なタスクを実装する

明確な仕様、技術計画、タスク ドキュメントが用意されたので、ドキュメントのアップロードと管理機能を実装する準備が整いました。 実装ワークフローは、仕様駆動型の開発によって実装がどのようにガイドされるか、および確立したコンテキストに基づいたコード生成が GitHub Copilot によってどのように支援されるかを示します。

このタスクでは、実装戦略をレビューし、`speckit/implement` を使用してアプリケーションの MVP バージョンを実装します。

そのためには、以下の手順を実行してください。

1. **tasks.md** ファイルを開き、**Implementation Strategy** セクションを見つけて、推奨される "MVP 優先" 戦略を確認します。

    MVP 優先戦略は、作業機能を可能な限り迅速に提供することを目的としています。 最初のユーザー ストーリー (US1) を構築する前に、まず重要なブロッキング フェーズを完成して機能基盤を確立することに重点を置く必要があります。

    たとえば、MVP 実装戦略は次の例のようになります。

    ```plaintext
    **Phases**: Setup → Foundation → US1 only  
    **Tasks**: T001 - T045 (45 tasks)  
    **Estimated Time**: 6-8 hours for developer familiar with ASP.NET Core/Blazor  
    **Deliverable**: Users can upload and view their documents
    ```

1. チャット ビューで、MVP 優先戦略を使用して実装ワークフローを開始するコマンドを入力します。

    機能の MVP バージョンを実装するために必要なタスクの範囲を指定するコマンドを作成します。 tasks.md ファイルの Implementation Strategy セクションで指定されたタスク範囲を使用します。

    > **重要**: 入力するコマンドは、tasks.md ファイルで定義されている特定のタスク範囲を参照する必要があります。

    たとえば (前の手順の MVP 実装例を参照)、次のコマンドを入力します。

    ```dotnetcli
    /speckit.implement Implement the MVP first strategy (Tasks: T001 - T045)
    ```

    このコマンドは、ドキュメントのアップロードと管理機能の MVP バージョンに必要なタスクの実装を開始するように GitHub Copilot に指示します。

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    エージェントは、tasks.md ファイルで定義されている順序に従って、機能をタスクごとに段階的にビルドします。

    > **注**:GitHub Copilot は、実装中に自身の作業を入念に検証しており、これは大変好ましい点です。 GitHub Copilot は、実装プロセス中もあなたを巻き込みながら進めます。 支援の要求は頻繁に発生します。 実装を完了するために必要な時間は、支援の要求にどの程度迅速に対応するかによって影響を受ける可能性があります。

1. タスクを検証するために手動テストが必要な場合は、チャット ビューで説明されている手順を実行し、結果を GitHub Copilot に報告します。

    手動テスト中に問題が発生する可能性があります。 次に例を示します。

    1. GitHub Copilot は、ファイルのアップロードが正しく動作していることを確認するために手動テストが必要であることを示します。
    1. アプリケーションは既にローカルで実行されています。 チャット ビューには、ブラウザーで開く URL (たとえば、`http://localhost:5000`) が表示されます。
    1. ブラウザーでアプリケーションを開き、Ni Ang としてログインし、[マイ ドキュメント] ページに移動します。
    1. アプリは応答していないように見え、ユーザー インターフェイスに "ドキュメントの読み込み中..." というメッセージが表示されます。
    1. [ドキュメントのアップロード] ボタンを選択しても、何も起こりません。
    1. ログアウトを試みますが、アプリケーションは応答しないままです。 どのボタンも機能しません。

    この時点で、GitHub Copilot に問題を報告する必要があります。

    1. Visual Studio Code のチャット ビューに戻ります。
    1. チャット ビューで問題を報告します。 次に例を示します。

        ```plaintext
        I opened the application in the browser at http://localhost:5000. I was able to login as Ni Kang and navigate to the My Documents page. However, I encountered an issue where the application appears unresponsive with a "Loading documents..." message displayed in the UI. When I select the Upload Document button, nothing happens. I also tried logging out, but the application remains unresponsive and none of the buttons work. Can you help me troubleshoot this issue?
        ```

    問題を報告すると、GitHub Copilot により、提供した情報を使用してデバッグが開始されます。 動作内容などの詳細な説明は、GitHub Copilot が問題をより深く理解するのに役立ちます。 一部の問題については、GitHub Copilot で解決するために、具体的なエラー メッセージなどの追加の詳細が必要になる場合があります。 問題の診断 (や解決) に役立てるために GitHub Copilot から要求された追加情報を提供します。

    問題が解決されるまで、支援を提供し続けます。 問題が解決したら、GitHub Copilot から、手動テストを再開するように求められます。

1. MVP アプリケーションに必要なすべてのタスクが完了するまで、実装ワークフローを続行します。

    MVP の実装が完了すると、GitHub Copilot はチャット ビューで通知します。

1. プロジェクト ファイルに加えられたすべての変更をレビューして受け入れます。

    このラボ演習では、詳細なレビューを行わずに、GitHub Copilot によって行われたすべての変更を受け入れても問題ありません。 ただし、運用環境では、すべてのコード変更を慎重にレビューして、品質基準を満たし、プロジェクトの要件に合致していることを確認することが重要です。

1. 数分かけて、MVP アプリケーションの受け入れシナリオを検証します。

    受け入れシナリオは、spec.md ファイルで確認できます。 「**ユーザー シナリオとテスト**」セクションに記載された受け入れシナリオ。 MVP アプリケーションは通常、spec.md ファイルの最初のユーザー ストーリー (US1) に関連付けられています。

    また、MVP 実装の手動テストを実行するために必要な手順について、GitHub Copilot に質問することができます。 たとえば、チャット ビューで次のプロンプトを入力します。

    ```plaintext
    Can you provide the steps required to manually test the MVP implementation?
    ```

    Visual Studio Code を使用してアプリケーションを実行し、ドキュメントのアップロードと管理機能を手動でテストして、期待どおりに動作することを確認します。

    たとえば、ドキュメントのアップロード機能を手動でテストするには、次の手順に従います。

    1. http://localhost:5000 に移動します
    1. Ni Kang (Employee) としてログインします。
    1. ナビゲーション メニューで **[ドキュメント]** を選択します。
    1. 指定されたインターフェイスを使用して、ファイル選択ダイアログを開きます。
    1. 25 MB 未満の PDF ファイルを見つけて選択し、[タイトル] フィールド ("テスト ドキュメント") と [カテゴリ] フィールド ("個人用ファイル") に入力します。
    1. [アップロード] オプションを選択して、アップロード プロセスを開始します。
    1. アップロードの進行状況インジケーターが表示されることを確認します。
    1. アップロードしたドキュメントの一覧にドキュメントが表示されることを確認します。

1. 手動テストの結果を GitHub Copilot に報告します。

    次に例を示します。

    - テストが成功したら、次のテストに進むか、次の例のようなレポートを提供することができます。

        "http://localhost:5000 のブラウザーでアプリケーションを開きました。 Ni Kang としてログインし、[マイ ドキュメント] ページに移動することができました。 タイトルが 'テスト ドキュメント' で、カテゴリが '個人用ファイル' の 25 MB 未満の PDF ファイルをアップロードできます。 アップロードの進行状況インジケーターが表示され、アップロードしたドキュメントの一覧にドキュメントが表示されます。 タスク T041 は正常にテストを通過しました。"

    - タスクが失敗した場合、支援を求めるために GitHub Copilot に問題を報告する必要があります。

        例:"http://localhost:5000 のブラウザーでアプリケーションを開きました。 Ni Kang としてログインし、[マイ ドキュメント] ページに移動することができました。 ドキュメントを選択し、[タイトル] と [カテゴリ] のフィールドに入力することはできますが、ドキュメントをアップロードしようとするとエラーが発生します。 [ドキュメントのアップロード] ページに進行状況インジケーターが表示されますが、[マイ ドキュメント] ページでは、ドキュメントをアップロードしたことが認識されません。 問題の解決に協力してもらえますか? 

    GitHub Copilot は、問題の診断と修正、ユーザー インターフェイスの改善の実装、または次の手順の提案に役立ちます。

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
