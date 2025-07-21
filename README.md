# Claude Code Spec-Driven Development Commands

KIROのspec-driven developmentアプローチをClaude Codeで実現するカスタムコマンド集です。

## 概要

このリポジトリは、要件定義から設計、タスク生成まで自動化するClaude Code用のカスタムコマンドを提供します。

## インストール

```bash
# リポジトリをクローン
git clone https://github.com/yourusername/claude-spec-driven.git

# プロジェクトのルートディレクトリに.claudeディレクトリをコピー
cp -r claude-spec-driven/.claude /path/to/your/project/

# または、個人用コマンドとして全プロジェクトで使用可能にする
cp -r claude-spec-driven/.claude/commands/* ~/.claude/commands/
```

## コマンド一覧

### `/spec-init` - 要件定義書の生成
プロジェクトの説明から詳細な要件定義書（requirement.md）を生成します。

**使用例:**
```
/spec-init 株価分析アプリを作りたい
/spec-init create a todo app with React
/spec-init interactive  # 対話モード
```

### `/spec-design` - 技術設計書の生成
要件定義書から技術設計書（design.md）を生成します。

**使用例:**
```
/spec-design stock-analysis
/spec-design todo-app
```

### `/spec-tasks` - タスクリストの生成
設計書から実装タスクリスト（tasks.md）を生成します。

**使用例:**
```
/spec-tasks stock-analysis
/spec-tasks todo-app
```

## ディレクトリ構造

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

## 特徴

- 🌏 **日英併記**: すべてのドキュメントは日本語と英語で生成
- 📊 **バージョン管理**: 技術スタックのバージョンを明記
- 🔄 **既存プロジェクト対応**: 新規・既存プロジェクト両方に対応
- 📁 **整理された構造**: 機能ごとにディレクトリを分けて管理

## カスタマイズ

### コマンドの編集
`.claude/commands/`内のMarkdownファイルを編集することで、コマンドの動作をカスタマイズできます。

### 新しいコマンドの追加
`.claude/commands/`に新しいMarkdownファイルを追加するだけで、新しいコマンドが利用可能になります。

## Contributing

プルリクエストを歓迎します！以下の点にご協力ください：

1. 新機能はissueで議論してから実装
2. コマンドは日英併記を維持
3. READMEの更新を忘れずに

## License

MIT License

## 謝辞

このプロジェクトは[KIRO](https://kiro.dev/)のspec-driven developmentアプローチにインスパイアされています。