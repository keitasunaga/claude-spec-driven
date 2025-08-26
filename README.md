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
プロジェクトの説明から詳細な要件定義書（requirement.md）を生成します。

**使用例:**
```
/spec-init 株価分析アプリを作りたい
/spec-init create a todo app with React
/spec-init interactive  # 対話モード
```

#### `/spec-design` - 技術設計書の生成
要件定義書から技術設計書（design.md）を生成します。

**使用例:**
```
/spec-design stock-analysis
/spec-design todo-app
```

#### `/spec-tasks` - タスクリストの生成
設計書から実装タスクリスト（tasks.md）を生成します。

**使用例:**
```
/spec-tasks stock-analysis
/spec-tasks todo-app
```

### ディレクトリ構造

```
your-project/
├── .claude/
│   └── commands/
│       ├── spec-init.md
│       ├── spec-design.md
│       └── spec-tasks.md
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
Generates a detailed requirements document (requirement.md) from a project description.

**Usage examples:**
```
/spec-init 株価分析アプリを作りたい
/spec-init create a todo app with React
/spec-init interactive  # Interactive mode
```

#### `/spec-design` - Generate Technical Design Document
Generates a technical design document (design.md) from the requirements document.

**Usage examples:**
```
/spec-design stock-analysis
/spec-design todo-app
```

#### `/spec-tasks` - Generate Task List
Generates an implementation task list (tasks.md) from the design document.

**Usage examples:**
```
/spec-tasks stock-analysis
/spec-tasks todo-app
```

### Directory Structure

```
your-project/
├── .claude/
│   └── commands/
│       ├── spec-init.md
│       ├── spec-design.md
│       └── spec-tasks.md
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