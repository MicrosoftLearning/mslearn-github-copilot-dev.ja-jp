---
lab:
  title: 演習 - GitHub Spec Kit を使用してグリーンフィールド アプリケーションを開発する
  description: GitHub Spec Kit をインストールする方法と、GitHub Spec Kit ワークフローを使用してグリーンフィールド アプリケーションの仕様駆動型開発手法を実装する方法について学習します。
---

# GitHub Spec Kit を使用してグリーンフィールド アプリケーションを開発する

GitHub Spec Kit は、仕様と GitHub Copilot などの AI コーディング アシスタントを統合することで、仕様駆動開発 (SSD) を有効にするオープンソース ツールキットです。

この演習では、GitHub Spec Kit を使用して新しいグリーンフィールド アプリケーションを開発する方法を学習します。 まず、新しい .NET プロジェクト用に GitHub Spec Kit を初期化します。 次に、GitHub Spec Kit ワークフローを使用して、新しいアプリケーションの規約、仕様、計画、タスクのドキュメントを作成します。 最後に、GitHub Spec Kit の実装ワークフローを使用して、アプリケーションの初期 MVP バージョンを実装します。

この演習の所要時間は約 **60** 分です。

> **重要**:この演習を完了するには、自分の GitHub アカウントと GitHub Copilot サブスクリプションを用意する必要があります。 GitHub アカウントをお持ちでない場合は、無料の個人用アカウントに<a href="https://github.com/" target="_blank">サインアップ</a>し、GitHub Copilot Free プランを使用して演習を完了できます。 ラボ環境内から GitHub Copilot Pro、GitHub Copilot Pro+、GitHub Copilot Business、または GitHub Copilot Enterprise サブスクリプションにアクセスできる場合は、既存の GitHub Copilot サブスクリプションを使用してこの演習を完了できます。

## 開始する前に

ラボ環境には次のリソースが必要です。Git 2.48 以降、.NET SDK 8.0 以降、C# 開発キットおよび GitHub Copilot Chat の拡張機能を含む Visual Studio Code、Python 3.11 以降、uv パッケージ マネージャー、Specify CLI、GitHub Copilot が有効な GitHub アカウントへのアクセス。

ラボ環境を構成する方法については、ブラウザーで次のリンクを開いてください: <a href="https://go.microsoft.com/fwlink/?linkid=2345907" target="_blank">GitHub Spec Kit のラボ環境を構成する</a>。

## 演習のシナリオ

あなたは、コンサルティング会社で働くソフトウェア開発者です。 あなたの会社は、Visual Studio Code で GitHub Spec Kit と GitHub Copilot を使用して仕様駆動開発手法に移行しているところです。 あなたは、可能な限り早急に SDD と GitHub Spec Kit の使用を開始するように言われています。

顧客の 1 つである Contoso Corporation からは、初期 MVP バージョンの RSS フィード リーダー アプリを開発するように依頼されています。 このアプリのプロジェクト目標、初期機能、技術面の要件は、Contoso の利害関係者が文書化しました。 あなたは、この利害関係者ドキュメントを使用して規約、仕様、計画、タスクのドキュメントを生成してから、アプリケーションの初期 MVP バージョンを実装します。 Contoso は、最初のサインオフ後に追加の機能を依頼することを示唆しています。

GitHub Spec Kit を使用して SDD 手法を実装することで、MVP アプリが迅速に配信され、利害関係者の要件を満たし、必要に応じて新しい機能をシームレスにロールアウトできるようになります。

この演習には、次のタスクが含まれています。

1. プロジェクト フォルダーを作成し、GitHub Spec Kit を初期化します。
1. 利害関係者のドキュメントを使用して規約を生成します。
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

1. Visual Studio Code のエクスプローラー ビューを使用して、.github フォルダーと .specify フォルダーを展開します。

    次の例のようなフォルダー構造が表示されます。

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

    '/speckit.'  コマンドが表示されない場合は、Visual Studio Code でプロジェクトを閉じてから再度開いてみてください。

    > **重要**:このラボ演習は、GPT-5.2 と Claude Sonnet 4.5 のモデルを使用して正常にテストされました。 どちらのモデルでも、動作するアプリケーションを生成できましたが、いくつかの違いが見られました。 Claude Sonnet 4.5 モデルの方が詳細な出力が生成される傾向があります。 たとえば、tasks.md ファイルのタスクとフェーズの数が多くなる傾向があります。 Claude モデルの応答は一貫性があり、パフォーマンスは信頼できるものでした。 GPT-5.2 モデルは、生成される出力の詳細さが低くなる傾向があります。 たとえば、一連のタスクはスコープが広くて数が少なく、これらをまとめたフェーズの数が少なくなっています。 GPT モデルはタスクを正常に実装することができましたが、バグを解決するために追加のイテレーションが使用された可能性があります。 GPT モデルのパフォーマンスは全般的に良好でしたが、テスト中の一貫性は低くなりました。 たとえば、/speckit コマンドの処理中に AI が応答しなくなることが何度かありました。 チャット ビューでコマンドを再起動すると、すぐに正常に戻りました。 最新ではないモデル、たとえば GPT-4 や GPT-5 mini を使用するテストでは、予期しない結果が生成されることがよくありました。 GitHub Spec Kit コマンドを実行するときは、可能な限り複雑な推論のために最適化された新しい言語モデルを使用することをお勧めします。

1. プロジェクトを新しい GitHub リポジトリに公開します。

    プロセスのこの時点では、プロジェクトを GitHub リポジトリに公開する必要はありませんが、GitHub Spec Kit では最終的に Git リポジトリが必要になるため、今すぐ設定しても問題はありません。 リポジトリを使用すると、問題点の追跡とプロジェクト マネジメントのために変更を追跡し、他のユーザーと共同作業を行い、GitHub の機能を使用することができます。

    次のステップを使用して、プロジェクトを新しい GitHub リポジトリに公開できます。

    1. Visual Studio Code の [ソース管理] ビューを開きます。

    1. **[ブランチの発行]** を選びます。

        リポジトリ名 (RSSFeedReader) は、ローカル フォルダー名に基づいて自動的に提案されます。

    1. **[GitHub プライベート リポジトリに公開]** を選択します。

        GitHub にサインインするように求められたら、サインイン プロセスに従って GitHub アカウントを認証します。

1. リポジトリが正常に作成されたことを確認します。

    次に例を示します。

    オプション 1: ブラウザーを開き、GitHub プロファイル ページに移動します。 リポジトリの一覧に新しい RSSFeedReader リポジトリが表示されます。 必要に応じてページを更新します。

    オプション 2: Visual Studio Code の通知 (右下) を開きます。 リポジトリが正常に公開されたことを確認する通知と、そのリポジトリを GitHub で表示するためのリンクが表示されます。

プロジェクト フォルダーの作成、GitHub Spec Kit の初期化、ソース管理の構成が完了したので、GitHub Spec Kit を使用して RSS フィード リーダー アプリケーションの規約、仕様、計画、タスクを作成する準備が整いました。

## 利害関係者のドキュメントを使用して規約を生成する

constitution.md ファイルでは、開発プロセス全体で従う必要があるポリシー、要件、技術標準を定義します。

GitHub Spec Kit には、constitution.md ファイルの作成と保守に役立ついくつかのリソースが含まれています。

- .specify/memory/constitution.md ファイルには、規約ドキュメントのテンプレートが含まれています。
- .github/agents/speckit.constitution.agent.md ファイルには、constitution.md ファイルの生成 (または更新) に使用される詳細な手順が記載されています。
- .github/prompts/speckit.constitution.prompt.md ファイルには、/speckit.constitution コマンドが呼び出されたときに speckit.constitution という名前のエージェントを実行するように Copilot Chat に指示する "ルーティング スタブ" が含まれています。
- /speckit.constitution コマンドは、プロジェクトの constitution.md ファイルを生成するために使用されます。

**constitution.md** ファイルの初期バージョンによって、規約に以下を含める必要があることが指定されます。

- プロジェクト名
- セクション 1:コア原則。 "コア原則" セクションには、5 つのコア原則を含める必要があります。 テンプレートには、それらの原則とその説明の例が提供されています。
- セクション 2: 名前なし。 テンプレートには、セクション名と内容の例が提供されています。
- セクション 3: 名前なし。 テンプレートには、セクション名と内容の例が提供されています。
- セクション 4:ガバナンス。 テンプレートには、ガバナンス セクションの適用方法とガバナンス規則の例が提供されています。

**speckit.constitution.agent.md** ファイルには、`/speckit.constitution` コマンドで指定されたテキスト入力またはファイル入力に基づいて constitution.md ファイルを更新する手順を提供します。 ガイダンスが提供されない場合、エージェントはコードベースで見つかった情報を使用して規約テンプレートに入力します。

/speckit.constitution ワークフローでは、テキスト入力、ファイル入力、コードベースを使用して、constitution.md ファイルに含めるポリシー、標準、要件、ガイドラインを収集します。 詳細な入力を提供すると、より正確で包括的な規約を生成するのに役立ちます。

このタスクでは、RSSFeedReader プロジェクトの利害関係者のドキュメントをダウンロードし、GitHub Spec Kit コマンドとの関係を評価し、その利害関係者のドキュメントを使用して constitution.md ファイルを生成します。

そのためには、以下の手順を実行してください。

1. 利害関係者ドキュメントをダウンロードするには、ブラウザーで次のリンクを開きます。[RSSFeedReader - 利害関係者ドキュメント](https://github.com/MicrosoftLearning/mslearn-github-copilot-dev/raw/refs/heads/main/DownloadableCodeProjects/Downloads/GHSpecKitEx13StakeholderDocuments.zip)。

1. ダウンロードした ZIP ファイルが格納されているフォルダーを開きます。

1. ダウンロードした ZIP ファイルの内容を一時フォルダーに抽出し、ファイルをコピーして、RSSFeedReader プロジェクトのルート フォルダーに貼り付けます。

    更新された RSSFeedReader プロジェクトは、次の例のようになります。

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

    これらのドキュメントでは、プロジェクトの目標と制約、アプリの機能、技術要件について自然言語で説明されています。 このコンテキストを理解することは、効果的な仕様、計画、タスクを作成するために不可欠です。 詳細レベルは、多くの実際のプロジェクトの暫定版ドキュメントで見られる典型的なレベルです。

    プロジェクト ドキュメントとドキュメントで提供される詳細は、会社のポリシーとプロジェクトの複雑さによって大きく異なる場合があります。 GitHub Spec Kit コマンドは、使用できる詳細レベルで動作するように設計されており、その情報を使用して、仕様駆動型開発プロセスを成功させるために必要な規約、仕様、計画、タスクのドキュメントを作成します。 ただし、詳細な入力を行うと、より予測可能な結果が得られます。

1. チャット ビューで、インライン テキストと利害関係者のドキュメントの組み合わせを使用して規約ワークフローを開始するには、次のコマンドを入力します。

    ```plaintext
    /speckit.constitution --text "Code projects emphasize security, maintainability, and code quality. Ensure that all principles are specific, actionable, and relevant to the project context." --files StakeholderDocuments/ProjectGoals.md StakeholderDocuments/AppFeatures.md StakeholderDocuments/TechStack.md
    ```

1. GitHub Copilot の応答を監視します。

    GitHub Copilot でプロジェクトの要件が分析され、constitution.md ファイルが更新されるまでに数分かかる場合があります。

1. GitHub Copilot で規約の生成が完了したら、少し時間を取って、編集候補を確認します。

    規約ワークフローは (テキストとファイルの両方から) 入力の基になる原則を抽出し、その情報を使用して詳細を規約に追加していることに注目してください。

    あなたは、要件が正確に取り込まれるように、規約をレビューする必要があります。 ビジネス要件と技術ガバナンスを規約で表す運用環境で作業している場合、この手順は重要です。 トレーニング演習では、このレビューは、主に規約の内容を十分に理解するのに役立ちます。

    各原則は明確に記述され、実行可能なものである必要があります。 次に例を示します。

    - ❌ あいまい:"セキュリティのベスト プラクティスを適用する。"  は一般的すぎます。
    - ✅ 明確:"すべての API エンドポイントで、処理前に入力を検証する必要があります (URL 形式の検証、長さの制限、null チェック)。" は具体的かつ実践的です。

    重要な要件が欠落している、または不明確な場合は、constitution.md ファイルを直接編集して原則を追加または変更できます。

    運用環境のシナリオでは、次の条件に照らして規約をレビューすることが重要です。

    - 完全性:すべての主要な領域が含まれています。
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

    これらのファイルについては、次の点に注意してください。

    - spec-template.md ファイルでは、仕様ドキュメントの構造とセクションを定義します。 これには、機能の説明、ユーザー ストーリー、受け入れ基準、その他の関連情報の例やプレースホルダーが含まれます。
    - speckit.specify.agent.md ファイルには、/speckit.specify コマンドの詳細な手順が記載されています。 提供された要件に基づいて仕様を作成する方法を GitHub Copilot にガイドします。
    - speckit.specify.agent.md ファイルは、ワークフローの開始時にリポジトリ ブランチを生成します。 通常、ブランチを作成するにはユーザーのアクセス許可が必要であるため、ワークフローの実行時に GitHub Copilot によってアクセス許可の入力が求められます。

1. Visual Studio Code のエクスプローラー ビューを使用して、利害関係者のドキュメント **ProjectGoals.md** と **AppFeatures.md** を調べます。

    AppFeatures.md ファイルは、ユーザー向けの機能要件の主要なリソースであり、包括的な仕様を作成するために必要なコンテキストを提供します。 ProjectGoals.md ファイルは、仕様を通知するのにも役立つ MVP とロールアウト計画に関する情報を提供します。

1. 利害関係者のドキュメントに基づいて、RSS フィード リーダー アプリの概要説明を作成します。

    概要説明は、簡潔 (1 つか 2 つの文) であり、RSS フィード リーダー アプリのコア機能が取り込まれている必要があります。 次に例を示します。

    "MVP RSS リーダー: 運用可能なアプリケーションの複雑さを伴わずに最も基本的な機能 (サブスクリプションの追加) をデモンストレーションするシンプルな RSS/Atom フィード リーダー。"

1. エディターで開いているすべてのファイルを閉じます。

1. チャット ビューが開いていることを確認します。

    GitHub Copilot が、現在のチャット セッションで以前の対話のコンテキストを保持していることに注意してください。 現在のセッションで constitution.md ファイルを生成した場合、GitHub Copilot のチャット ビューの下部付近に **[ビルド仕様]** ボタンが表示され、これを使用して仕様の生成を開始できます。 この場合は、要件ドキュメントを明示的に指定する必要があるため、[ビルド仕様] ボタンは使用しません。

1. チャット ビューで、利害関係者のドキュメントの情報を使用して spec.md ファイルを生成する仕様ワークフローを開始するには、次のコマンドを入力します。

    ```plaintext
    /speckit.specify --text "MVP RSS reader: a simple RSS/Atom feed reader that demonstrates the most basic capability (add subscriptions) without the complexity of a production-ready application." --files StakeholderDocuments/ProjectGoals.md StakeholderDocuments/AppFeatures.md
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

    - ✅ 推奨:**Given** アプリケーションが実行されている、**When** ユーザーが有効な RSS フィードの URL を入力して送信する、**Then** フィードがサブスクリプション リストに追加される。

    - ✅ 推奨:**Given** ユーザーが (適切な URI ではなく) 無効な URL を入力する、**When** それを送信しようとする、**Then** URL の形式が正しくないことを示すエラー メッセージが表示される。

    - ❌ 非推奨:"アップロードが適切に機能する必要がある" や "システムは高速である必要がある" などのあいまいな条件。

1. **spec.md** ファイルの "要件" セクションに、利害関係者の要件ドキュメントの主要な要件が含まれていることを確認します。

    たとえば、次の例のような要件が表示されるはずです。

    - システムでは、ユーザーがフィードの URL を貼り付けてフィード サブスクリプションを追加できるようにする必要があります。

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

1. チャット ビューで、plan.md ファイルを生成するワークフローを開始するには、次のコマンドを入力します。

    ```dotnetcli
    /speckit.plan --files StakeholderDocuments/ProjectGoals.md StakeholderDocuments/TechStack.md
    ```

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    GitHub Copilot は、constitution.md、spec.md、さらに利害関係者のファイルを分析して計画を生成します。 必要に応じて、アクセス許可と支援を提供します。

    GitHub Copilot で技術計画とそれに関連するマークダウン ファイルが生成されるまでに 6 から 8 分かかる場合があります。

1. 計画ワークフローが完了したら、次のファイルが **specs** フォルダーのルートに追加されたことを確認します。

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

    運用シナリオの場合、計画によって、技術的なコンテキストの包括的な説明と、新しいアプリ/機能の明確に定義された実装戦略が提供されることを確認する必要があります。 調査、クイック スタート、データ モデルの各ファイルは、追加のコンテキストと詳細を提供して計画を補完する必要があります。 この演習では、各ファイルに関連付けられているコンテンツを十分に理解することに重点を置きます。

1. ファイルを確認したら、すべての編集候補を受け入れます。

    計画で重要な詳細が省略されている場合、または同意しない仮定を行う場合は、次のことができます。

    - plan.md ファイルを直接編集するか、
    - GitHub Copilot Chat でフォローアップの質問をします。

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

    GitHub Copilot は、spec.md と plan.md のファイルを分析し、tasks.md ファイルにタスクを生成します。

    GitHub Copilot で tasks.md ファイルが生成されるまでに 4 から 6 分かかる場合があります。 必要に応じて、アクセス許可と支援を提供します。

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

1. 推奨されるファイルの更新を受け入れて、**tasks.md** ファイルを保存します。

1. 変更をコミットし、更新を同期します。

これで、tasks.md ファイルにより、実装の明確なロードマップが提供されます。

## MVP アプリケーションに必要なタスクを実装する

仕様、技術計画、タスクの明確なドキュメントが用意されたので、RSS フィード リーダー アプリを実装する準備が整いました。

tasks.md ファイルは、作業を管理可能なチャンクに分割する段階的な実装戦略を提供します。 実装については、プロジェクトの優先順位と制約に応じて、さまざまなアプローチ方法があります。 たとえば、次のいずれかの戦略を検討できます。

- アプリの機能を段階的に (一度に 1 フェーズずつ) 実装します。
- 1 回のパスでアプリ全体を実装します。
- 最初に MVP アプリ機能を実装し、その後に追加の機能を構築する。

GitHub Spec Kit の実装ワークフローでは、tasks.md ファイルを使用して実装プロセスをガイドする方法を示します。

このタスクでは、実装戦略をレビューし、`speckit/implement` を使用して、アプリケーションの MVP バージョン (サブスクリプションを追加および表示する機能) を実装します。

そのためには、以下の手順を実行してください。

1. **tasks.md** ファイルを開き、**実装戦略**セクションを見つけて、推奨される MVP 戦略を確認します。

    MVP (または MVP 優先) 戦略は、作業中のアプリを可能な限り迅速に提供することを目的としています。 最初のユーザー ストーリー (US1) を構築する前に、まず "セットアップ" (初期化) フェーズと "基盤" (ブロック) フェーズを完成することに重点を置く必要があります。

    たとえば、MVP 実装戦略 (サブスクリプションを追加および表示する機能) は、次の例のようになります。

    **フェーズ**: セットアップ → 基盤 → US1 のみ  
    **タスク**:T001 - T050 (50 タスク)  
    **成果物**: ユーザーは既知の適切なフィード URL を追加する、最新の情報に更新する、アイテムを見る、再起動して永続性を確認することができます。

    MVP 優先戦略は、必ずしも最初のユーザー ストーリーに限定されるわけではありません。 機能の複雑さに応じて、いくつかのユーザー ストーリーが含まれる場合があります。 これらのストーリーを基本フェーズの後に順番に一覧表示し、MVP 実装戦略の一部として明確にマークする必要があります。

1. チャット ビューで、MVP 優先戦略の実装ワークフローを開始するコマンドを入力します。

    機能の MVP バージョンを実装するために必要なタスクの範囲を指定するコマンドを作成します。 tasks.md ファイルの [実装戦略] セクションで指定したタスク範囲を使用するか、各フェーズに一覧表示されているタスクに基づいて計算してください。

    > **重要**:入力するコマンドは、tasks.md ファイルで定義されている特定のタスク範囲を参照する必要があります。

    たとえば (前の手順の MVP 実装例を参照)、次のコマンドを入力します。

    ```dotnetcli
    /speckit.implement Implement the MVP First strategy (Tasks: T001 - T050)
    ```

    このコマンドは、RSS フィード リーダー アプリの MVP 優先戦略に必要なタスクの実装を開始するように GitHub Copilot に指示します。

    この演習では、1 回の /speckit.implement コマンドを使用して、MVP 優先戦略のすべてのタスクを実装します。 運用環境では、セットアップと基本のフェーズのタスクを最初に実装し、その後に各ユーザー ストーリーのフェーズのタスクを一度に 1 つずつ実装するなど、段階的なアプローチに従う場合があります。

1. GitHub Copilot の応答を監視し、Chat ビューで支援します。

    エージェントは、tasks.md ファイルで定義されている順序に従って、機能をタスクごとに段階的に構築します。

    > **注**:GitHub Copilot は、実装フェーズ中にサポートの要求を頻繁に表示します。 実装を完了するために必要な時間は、支援とアクセス許可の要求にどの程度迅速に対応するかによって影響を受ける可能性があります。

1. MVP アプリケーションに必要なすべてのタスクが完了するまで、実装ワークフローの監視を続行します。

    バックエンドとフロントエンドのアプリケーションが完了すると、GitHub Copilot のチャット ビューに通知があるはずです。

1. プロジェクト ファイルに加えられたすべての変更を受け入れます。

    このラボ演習では、レビューを行わずに、GitHub Copilot によって行われたすべての変更を受け入れても問題ありません。 ただし、運用環境では、すべてのコード変更を慎重にレビューして、品質基準を満たし、プロジェクトの要件に合致していることを確認することが重要です。

1. 更新されたすべてのファイルを保存します。

1. バックエンド アプリケーションを起動してから、フロントエンド アプリケーションを起動します。

    分割ターミナルを使用して、両方のアプリケーションを左右に並べて実行できます。 両方のアプリケーションがエラーなしで起動することを確認します。 両方のアプリケーションを起動するために必要なコマンドが不明な場合は、GitHub Copilot に問い合わせることができます。

    いずれかのアプリケーションが起動に失敗した場合は、この問題をチャット ビューで GitHub Copilot に報告してください。 問題を詳しく説明し、問題の診断に役立つエラー メッセージやログなども、あれば添えてください。 GitHub Copilot はこの情報を使用して、問題のデバッグと解決を開始します。

1. フロントエンド アプリケーションがブラウザーで正常に開くことを確認します。

    GitHub Copilot は、`http://localhost:5213` のようなフロントエンドの URL をチャット ビューで指定する必要があります。

    フロントエンド アプリケーションが開いたときに UI にエラーが報告される場合は、問題の詳細な説明を GitHub Copilot に報告します。

    次に例を示します。

    1. バックエンド アプリケーションを起動します。 バックエンド アプリケーションは `http://localhost:5151` でローカルに実行されています。
    1. フロントエンド アプリケーションを起動します。 フロントエンド アプリケーションは `http://localhost:5213` でローカルに実行されています。
    1. ブラウザーを開き、フロントエンド アプリケーションに指定された URL に移動します。
    1. ページが読み込まれると、"未処理のエラーが発生しました。 再度読み込みます" というエラーが報告されます。
    1. [再読み込み] オプションを選択しても、エラーは解決しません。
    1. ブラウザーの開発者ツール コンソールを開いて、さらに調査します。
    1. 開発者ツール コンソールに、次のエラー メッセージが表示されます。

        ```plaintext
        "Microsoft.AspNetCore.Components.WebAssembly.Rendering.WebAssemblyRenderer[100]
        Unhandled exception rendering component: The following routes are ambiguous:
        '' in 'RSSFeedReader.UI.Pages.Home'
        '' in 'RSSFeedReader.UI.Pages.Subscriptions'
        
        System.InvalidOperationException: The following routes are ambiguous:
        '' in 'RSSFeedReader.UI.Pages.Home'
        '' in 'RSSFeedReader.UI.Pages.Subscriptions'
        
        at Microsoft.AspNetCore.Components.RouteTableFactory.DetectAmbiguousRoutes(:5213/TreeRouteBuilder builder)
        at Microsoft.AspNetCore.Components.RouteTableFactory.Create(:5213/Dictionary2 templatesByHandler, IServiceProvider serviceProvider)    at Microsoft.AspNetCore.Components.RouteTableFactory.Create(:5213/List1 componentTypes, IServiceProvider serviceProvider)
        at Microsoft.AspNetCore.Components.RouteTableFactory.Create(:5213/RouteKey routeKey, IServiceProvider serviceProvider)
        at Microsoft.AspNetCore.Components.Routing.Router.RefreshRouteTable((index))
        at Microsoft.AspNetCore.Components.Routing.Router.Refresh(:5213/Boolean isNavigationIntercepted)
        at Microsoft.AspNetCore.Components.Routing.Router.RunOnNavigateAsync(:5213/String path, Boolean isNavigationIntercepted)
        at Microsoft.AspNetCore.Components.Routing.Router.<>c__DisplayClass82_0.<RunOnNavigateAsync>b__1(:5213/RenderTreeBuilder builder)
        at Microsoft.AspNetCore.Components.Rendering.ComponentState.RenderIntoBatch(:5213/RenderBatchBuilder batchBuilder, RenderFragment renderFragment, Exception& renderFragmentException)"
        ```

    1. チャット ビューで GitHub Copilot に問題を報告します。

        次に例を示します。

        ```plaintext
        I was able to start the backend and frontend apps successfully. I opened the frontend app in the browser at http://localhost:5213. When the page opens, I see an error message: "An unhandled error has occurred. Reload". Selecting reload doesn't resolve the issue. When I check the browser's developer tools console, I see the following error message: 

        "Microsoft.AspNetCore.Components.WebAssembly.Rendering.WebAssemblyRenderer[100]
        Unhandled exception rendering component: The following routes are ambiguous:
        '' in 'RSSFeedReader.UI.Pages.Home'
        '' in 'RSSFeedReader.UI.Pages.Subscriptions'
        
        System.InvalidOperationException: The following routes are ambiguous:
        '' in 'RSSFeedReader.UI.Pages.Home'
        '' in 'RSSFeedReader.UI.Pages.Subscriptions'
        
        at Microsoft.AspNetCore.Components.RouteTableFactory.DetectAmbiguousRoutes(:5213/TreeRouteBuilder builder)
        at Microsoft.AspNetCore.Components.RouteTableFactory.Create(:5213/Dictionary2 templatesByHandler, IServiceProvider serviceProvider)    at Microsoft.AspNetCore.Components.RouteTableFactory.Create(:5213/List1 componentTypes, IServiceProvider serviceProvider)
        at Microsoft.AspNetCore.Components.RouteTableFactory.Create(:5213/RouteKey routeKey, IServiceProvider serviceProvider)
        at Microsoft.AspNetCore.Components.Routing.Router.RefreshRouteTable((index))
        at Microsoft.AspNetCore.Components.Routing.Router.Refresh(:5213/Boolean isNavigationIntercepted)
        at Microsoft.AspNetCore.Components.Routing.Router.RunOnNavigateAsync(:5213/String path, Boolean isNavigationIntercepted)
        at Microsoft.AspNetCore.Components.Routing.Router.<>c__DisplayClass82_0.<RunOnNavigateAsync>b__1(:5213/RenderTreeBuilder builder)
        at Microsoft.AspNetCore.Components.Rendering.ComponentState.RenderIntoBatch(:5213/RenderBatchBuilder batchBuilder, RenderFragment renderFragment, Exception& renderFragmentException)".
        ```

    1. GitHub Copilot が、指定した情報を分析し、問題のデバッグを開始します。

        問題を報告すると、GitHub Copilot により、提供した情報を使用してデバッグが開始されます。 動作内容などの詳細な説明は、GitHub Copilot が問題をより深く理解するのに役立ちます。 一部の問題については、GitHub Copilot で解決するために、具体的なエラー メッセージなどの追加の詳細が必要になる場合があります。 問題の診断 (や解決) に役立てるために GitHub Copilot から要求された追加情報を提供します。

        問題が解決されるまで、支援を提供し続けます。 問題が解決したら、GitHub Copilot から、手動テストを再開するように求められます。

1. フロントエンド アプリケーションが期待どおりに動作していることを確認するには、数分かかります。

    次のフィードの URL を使用して、アプリケーションをテストします。

    - https://devblogs.microsoft.com/dotnet/feed/
    - https://devblogs.microsoft.com/visualstudio/feed/

    受け入れシナリオは、spec.md ファイルの **[ユーザー シナリオとテスト]** セクションにあります。

    たとえば、MVP アプリケーションの受け入れシナリオは、次の例のようになります。

    1. **Given** サブスクリプションが追加されていない、**When** ユーザーがページを読み込む、**Then** 空の状態が表示される (例: "サブスクリプションがまだありません" というメッセージ)
    2. **Given** サブスクリプション管理インターフェイスが読み込まれる、**When** ユーザーが入力フィールドに有効なフィードの URL を入力し、[サブスクリプションの追加] をクリックする、**Then** システムが URL を受け入れ、サブスクリプションが追加されたことを確認する
    3. **Given** ユーザーがフィードの URL を入力している、**When** ユーザーがフォームを送信する、**Then** 入力フィールドがクリアされ、別の URL の準備が整う
    4. **Given** ユーザーが空の文字列または空白のみの入力を入力する、**When** サブスクリプションの追加が試行される、**Then** システムが送信を禁止する (基本的なクライアント側の検証)
    5. **Given** ユーザーがサブスクリプションを 1 つ追加している、**When** ページが表示される、**Then** サブスクリプションの URL がリストに表示される
    6. **Given** ユーザーが複数のサブスクリプションを追加した、**When** ページが表示される、**Then** すべてのサブスクリプションの URL が追加された順序でリストに表示される (最新が最後)

    また、MVP 実装の手動テストを実行するために必要な手順について、GitHub Copilot に質問することができます。 たとえば、チャット ビューで次のプロンプトを入力します。

    ```plaintext
    Can you provide the steps required to manually test the MVP implementation?
    ```

    Visual Studio Code を使用してアプリケーションを実行し、RSS Feed Reader 機能を手動でテストして、期待どおりに動作することを確認します。

1. MVP アプリケーションのすべての受け入れシナリオが成功するまで、フロントエンドのテスト (および GitHub Copilot への問題の報告) を続行します。

主な観察内容: 

- 実装プロセスは反復的であり、複数のテストとデバッグが必要になる場合があります。
- 効果的なトラブルシューティングを行うには、GitHub Copilot との明確な通信が不可欠です。
- 徹底的なテストを行うことで、MVP アプリケーションが指定された要件と合致し、意図したとおりに機能することを確認できます。

## クリーンアップ

演習が完了したので、少し時間を取って、GitHub アカウントまたは GitHub Copilot サブスクリプションに保持したくない変更が加えられていないか確認します。 たとえば、ContosoDashboard リポジトリを削除するとします。 ローカル PC をラボ環境として使用している場合は、演習中にインストールされた可能性のあるツールはすべて保持してください。 この演習用に作成したリポジトリのローカル クローンは、アーカイブまたは削除できます。
