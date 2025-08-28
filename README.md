# Claude Code Spec-Driven Development Commands

[日本語](#日本語) | [English](#english)

---

## 日本語

### 概要

KIROのspec-driven developmentアプローチをClaude Codeで実現するカスタムコマンド集です。このリポジトリは、要件定義から設計、タスク生成まで自動化するClaude Code用のカスタムコマンドを提供します。

### インストール

```bash
# リポジトリをクローン
git clone https://github.com/yourusername/claude-spec-driven.git

# プロジェクトのルートディレクトリに.claudeディレクトリをコピー
cp -r claude-spec-driven/.claude /path/to/your/project/

# または、個人用コマンドとして全プロジェクトで使用可能にする
cp -r claude-spec-driven/.claude/commands/* ~/.claude/commands/
```

### コマンド一覧

#### `/spec-init` - 要件定義書の生成
プロジェクトの説明から詳細な要件定義書（requirement.md）を生成します。ソクラテス式探索、多面的分析、MCP統合による高度な分析に対応します。

**使用例:**
```
# 対話モード（パラメータなしのデフォルト）
/spec-init
/spec-init interactive  # 明示的に対話モードを指定

# クイックモード
/spec-init 株価分析アプリ --quick
/spec-init create a todo app with React --quick

# ソクラテス式深掘り対話
/spec-init AIアシスタント --deep
/spec-init ECサイト --deep --personas

# 高度な分析機能
/spec-init 決済システム --advanced
/spec-init マイクロサービス --analyze --advanced
```

**オプション:**
- `--quick/-q`: クイックモードを強制（対話なし）
- `--deep/-d`: ソクラテス式深掘り対話モード
- `--personas/-p`: 複数視点による多面的分析
- `--advanced`: MCP統合による高度な分析機能
- `--analyze/-a`: 既存コードを分析

#### `/spec-design` - 技術設計書の生成
要件定義書から技術設計書（design.md）を生成します。MCP統合と多面的設計レビューに対応し、既存資産の最大活用を重視します。

**使用例:**
```
# 基本的な設計書生成
/spec-design stock-analysis
/spec-design todo-app

# 深い設計分析
/spec-design ECサイト --deep-analysis
/spec-design 決済システム -da --personas

# デザインパターンとビジュアル化
/spec-design チャットアプリ --pattern-library --visual

# 反復的設計改善
/spec-design AIシステム --iterative
```

**オプション:**
- `--deep-analysis/-da`: MCP活用による深い設計分析
- `--personas/-p`: 複数視点による設計レビュー
- `--pattern-library/-pl`: デザインパターンライブラリの活用
- `--visual/-v`: ダイアグラム生成の強化（Mermaid/PlantUML）
- `--iterative/-i`: 段階的な設計洗練モード

#### `/spec-tasks` - タスクリストの生成
設計書から実装タスクリスト（tasks.md）を生成します。階層的タスク管理とインテリジェントな分析を統合し、複数の実行戦略に対応します。

**使用例:**
```
# 基本的なタスクリスト生成
/spec-tasks stock-analysis
/spec-tasks todo-app

# 深い分析と見積もりを含む生成
/spec-tasks 株価分析ツール --depth deep --estimate

# アジャイル戦略で英語生成
/spec-tasks "payment system" --en --strategy agile

# 完全な階層構造で生成
/spec-tasks ECサイト --hierarchy epic --analyze
```

**新オプション:**
- `--strategy [systematic/agile/enterprise]`: 実行戦略の選択
- `--depth [quick/standard/deep]`: 分析の深さ
- `--analyze`: プロジェクト深層分析を含める
- `--estimate`: 見積もりとリスク評価を含める
- `--hierarchy [epic/story/task/subtask]`: タスク階層の設定

#### `/dev-team` - GitHub Issue実行
GitHub Issueから開発を実行します。検証ゲート付きで確実な開発完了を保証します。

**使用例:**
```
# 基本実行（検証ゲート付き）
/dev-team #123

# チェックポイント付き実行
/dev-team #123 --checkpoint

# アトミックモード（タスクごと確認）
/dev-team #123 --atomic

# 検証スキップ（非推奨）
/dev-team #123 --no-validate
```

**主な機能:**
- 🔍 **検証ゲート**: 各フェーズでビルド・テスト・品質チェック
- 💾 **チェックポイント**: 進捗保存と復元機能
- ⚛️ **アトミックモード**: タスクごとの確認で安全に実行
- 🤖 **専門エージェント**: バックエンド、フロントエンド、テストエンジニアを自動選択
- ✅ **完了保証**: ビルド成功・テストパスを確認してからタスク完了

### ディレクトリ構造

```
your-project/
├── .claude/
│   └── commands/
│       ├── spec-init.md
│       ├── spec-design.md
│       ├── spec-tasks.md
│       └── dev-team.md
└── .specs/
    ├── stock-analysis-app/
    │   ├── requirement.md
    │   ├── design.md
    │   └── tasks.md
    └── todo-app/
        ├── requirement.md
        ├── design.md
        └── tasks.md
```

### 特徴

- 🌏 **日英併記**: すべてのドキュメントは日本語と英語で生成
- 📊 **バージョン管理**: 技術スタックのバージョンを明記
- 🔄 **既存プロジェクト対応**: 新規・既存プロジェクト両方に対応
- 📁 **整理された構造**: 機能ごとにディレクトリを分けて管理
- 🧠 **インテリジェント分析**: MCP統合による深い分析機能
- ⚡ **並列実行最適化**: タスクの依存関係を分析し並列実行を最大化
- 📈 **リスク管理**: 見積もりと信頼区間による実現可能性評価

### カスタマイズ

#### コマンドの編集
`.claude/commands/`内のMarkdownファイルを編集することで、コマンドの動作をカスタマイズできます。

#### 新しいコマンドの追加
`.claude/commands/`に新しいMarkdownファイルを追加するだけで、新しいコマンドが利用可能になります。

### Contributing

プルリクエストを歓迎します！以下の点にご協力ください：

1. 新機能はissueで議論してから実装
2. コマンドは日英併記を維持
3. READMEの更新を忘れずに

### License

MIT License

### 謝辞

このプロジェクトは[KIRO](https://kiro.dev/)のspec-driven developmentアプローチにインスパイアされています。

---

## English

### Overview

A collection of custom commands for Claude Code that implements KIRO's spec-driven development approach. This repository provides custom commands for Claude Code that automate the entire process from requirement definition to design and task generation.

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/claude-spec-driven.git

# Copy the .claude directory to your project root
cp -r claude-spec-driven/.claude /path/to/your/project/

# Or, make it available as personal commands for all projects
cp -r claude-spec-driven/.claude/commands/* ~/.claude/commands/
```

### Command List

#### `/spec-init` - Generate Requirements Document
Generates a detailed requirements document (requirement.md) from a project description. Supports Socratic exploration, multi-faceted analysis, and advanced features through MCP integration.

**Usage examples:**
```
# Interactive mode (default when no parameters)
/spec-init
/spec-init interactive  # Explicitly specify interactive mode

# Quick mode
/spec-init 株価分析アプリ --quick
/spec-init create a todo app with React --quick

# Socratic deep exploration
/spec-init "AI assistant" --deep
/spec-init "e-commerce site" --deep --personas

# Advanced analysis features
/spec-init "payment system" --advanced
/spec-init "microservices" --analyze --advanced
```

**Options:**
- `--quick/-q`: Force quick mode (no interaction)
- `--deep/-d`: Socratic deep exploration mode
- `--personas/-p`: Multi-perspective analysis
- `--advanced`: Advanced analysis through MCP integration
- `--analyze/-a`: Analyze existing code

#### `/spec-design` - Generate Technical Design Document
Generates a technical design document (design.md) from the requirements document. Supports MCP integration and multi-faceted design review, emphasizing maximum utilization of existing assets.

**Usage examples:**
```
# Basic design document generation
/spec-design stock-analysis
/spec-design todo-app

# Deep design analysis
/spec-design "e-commerce site" --deep-analysis
/spec-design "payment system" -da --personas

# Design patterns and visualization
/spec-design "chat app" --pattern-library --visual

# Iterative design improvement
/spec-design "AI system" --iterative
```

**Options:**
- `--deep-analysis/-da`: Deep design analysis through MCP utilization
- `--personas/-p`: Multi-perspective design review
- `--pattern-library/-pl`: Design pattern library utilization
- `--visual/-v`: Enhanced diagram generation (Mermaid/PlantUML)
- `--iterative/-i`: Phased design refinement mode

#### `/spec-tasks` - Generate Task List
Generates an implementation task list (tasks.md) from the design document with hierarchical task management and intelligent analysis integrated, supporting multiple execution strategies.

**Usage examples:**
```
# Basic task list generation
/spec-tasks stock-analysis
/spec-tasks todo-app

# Generation with deep analysis and estimation
/spec-tasks "stock analysis tool" --depth deep --estimate

# English generation with agile strategy  
/spec-tasks "payment system" --en --strategy agile

# Generation with complete hierarchy
/spec-tasks "e-commerce site" --hierarchy epic --analyze
```

**New options:**
- `--strategy [systematic/agile/enterprise]`: Select execution strategy
- `--depth [quick/standard/deep]`: Analysis depth
- `--analyze`: Include deep project analysis
- `--estimate`: Include estimation and risk assessment
- `--hierarchy [epic/story/task/subtask]`: Set task hierarchy

#### `/dev-team` - GitHub Issue Execution
Executes development from GitHub Issues with validation gates to ensure reliable completion.

**Usage examples:**
```
# Basic execution with validation gates
/dev-team #123

# Execution with checkpoints
/dev-team #123 --checkpoint

# Atomic mode (confirm each task)
/dev-team #123 --atomic

# Skip validation (not recommended)
/dev-team #123 --no-validate
```

**Key features:**
- 🔍 **Validation Gates**: Build, test, and quality checks at each phase
- 💾 **Checkpoints**: Progress save and restore functionality
- ⚛️ **Atomic Mode**: Safe execution with confirmation for each task
- 🤖 **Specialized Agents**: Auto-selects backend, frontend, and test engineers
- ✅ **Completion Guarantee**: Confirms build success and test pass before task completion

### Directory Structure

```
your-project/
├── .claude/
│   └── commands/
│       ├── spec-init.md
│       ├── spec-design.md
│       ├── spec-tasks.md
│       └── dev-team.md
└── .specs/
    ├── stock-analysis-app/
    │   ├── requirement.md
    │   ├── design.md
    │   └── tasks.md
    └── todo-app/
        ├── requirement.md
        ├── design.md
        └── tasks.md
```

### Features

- 🌏 **Bilingual Support**: All documents are generated in both Japanese and English
- 📊 **Version Management**: Technology stack versions are clearly specified
- 🔄 **Existing Project Support**: Works with both new and existing projects
- 📁 **Organized Structure**: Features are managed in separate directories
- 🧠 **Intelligent Analysis**: Deep analysis features through MCP integration
- ⚡ **Parallel Execution Optimization**: Analyzes task dependencies to maximize parallel execution
- 📈 **Risk Management**: Feasibility assessment with estimates and confidence intervals

### Customization

#### Editing Commands
You can customize command behavior by editing the Markdown files in `.claude/commands/`.

#### Adding New Commands
Simply add a new Markdown file to `.claude/commands/` to make a new command available.

### Contributing

Pull requests are welcome! Please help with the following:

1. Discuss new features in issues before implementation
2. Maintain bilingual support in commands
3. Don't forget to update the README

### License

MIT License

### Acknowledgments

This project is inspired by [KIRO](https://kiro.dev/)'s spec-driven development approach.