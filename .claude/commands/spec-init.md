---
description: 要件定義を生成 - 既存コードの分析とクイックモード・対話モードに対応（デフォルト：日本語）
---

# 仕様駆動要件ジェネレーター

## 使い方

```bash
/spec-init [プロジェクト説明] [オプション]
```

### パラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `プロジェクト説明` (任意) | プロジェクトの簡単な説明 | `TODOアプリ`, `株価分析ツール`, `メール通知システム` |
| `--quick` または `-q` | クイックモードを強制（対話なし） | `--quick`, `-q` |
| `--en` | 英語で生成（デフォルトは日本語） | `--en` |
| `--analyze` または `-a` | 既存コードを分析 | `--analyze`, `-a` |

### 使用例

```bash
# 対話モード（パラメータなしの場合のデフォルト）
/spec-init
/spec-init interactive  # 明示的に対話モードを指定

# クイックモード - 簡単なプロジェクト説明  
/spec-init TODOアプリ --quick
/spec-init 株価分析ツール -q
/spec-init "ECプラットフォーム" --quick

# 初期コンテキスト付き対話モード
/spec-init TODOアプリ  # プロジェクト名を指定して対話モード開始
/spec-init ECサイト

# 英語で生成
/spec-init todo app --en  # 英語で対話モード
/spec-init "stock analysis tool" --quick --en  # 英語でクイックモード
```

## モード選択と言語設定

### パラメータの確認
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔍 コマンド実行開始"
!if [ -z "$ARGUMENTS" ]; then
  echo "📋 モード: 対話モード（パラメータなし）"
  echo "💬 これから新機能について詳しくお聞きします"
else
  echo "📋 受信したパラメータ: $ARGUMENTS"
fi
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

### 言語設定の確認
!if [[ "$ARGUMENTS" == *"--en"* ]]; then echo "🌐 Language: English"; else echo "🌐 言語: 日本語（デフォルト）"; fi

## ステップ1: 既存プロジェクトの分析

既存のソースコードがあるかチェックします:

!find . -type f -name "*.py" -o -name "*.js" -o -name "*.ts" -o -name "*.java" -o -name "*.go" | head -20

ソースコードが存在する場合、以下を分析します:
- 現在のプロジェクト構造とアーキテクチャ
- 既存の機能  
- 依存関係と技術スタック
- コードパターンと規約
- 改善が必要な領域

入力内容に基づいて、最適なアプローチを決定します:

### 💬 対話モード（デフォルト）
**使用場面**: 詳細でカスタマイズされた要件が必要な場合
- 起動条件: 
  - パラメータなし: `/spec-init`
  - オプションなしのプロジェクト説明: `/spec-init TODOアプリ`
  - 明示的なキーワード: `/spec-init interactive`
- 実行内容:
  - 以下について詳細な質問でガイド:
    1. プロジェクトの概要と目標
    2. 具体的な機能要件
    3. 技術的な制約と好み
    4. ユーザー体験の考慮事項
  - プロジェクト説明が提供された場合、初期コンテキストとして使用

### 🚀 クイックモード
**使用場面**: シンプルなプロジェクトアイデアで素早く要件が欲しい場合
- 起動条件: `--quick` または `-q` フラグ
- 例: `/spec-init TODOアプリ --quick`
- 実行内容:
  - コアコンセプトを分析
  - このタイプのアプリケーションの一般的な要件を推測
  - ベストプラクティスに基づいて包括的な要件を生成
  - 標準機能を備えた完全なrequirement.mdを作成
  - 対話型の質問なし

### 🔍 分析モード
**使用場面**: 既存コードを分析したい場合
- 起動条件: `--analyze` または `-a` フラグ
- 例: `/spec-init --analyze`
- 実行内容:
  - 既存のコードベースを分析
  - 現在の機能とアーキテクチャを抽出
  - 実際の実装に基づいて要件を生成

---

## ステップ2: 仕様ディレクトリの決定

!echo "📁 仕様ディレクトリ構造の準備"

パラメータに基づいて、適切なディレクトリ構造を作成または使用します:

```
.specs/
├── stock-analysis-app/
│   ├── requirement.md
│   ├── design.md
│   └── tasks.md
├── email-unsubscribe-feature/
│   ├── requirement.md
│   ├── design.md
│   └── tasks.md
└── user-authentication-system/
    ├── requirement.md
    ├── design.md
    └── tasks.md
```

### ディレクトリ名の決定プロセス

!if [ -z "$ARGUMENTS" ]; then
  echo "💭 対話モードのため、後でディレクトリ名を決定します"
else
  echo "📝 パラメータから仕様ディレクトリ名を生成します"
fi

仕様ファイルは以下に整理されます: `.specs/[feature-or-app-name]/`

## ステップ3: 要件の生成・更新

パラメータと上記の分析に基づいて、以下を実行します:

### 言語に応じた対話と生成

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "🌐 I will interact with you in English and generate all documents in English."
  echo "📝 Requirements will be numbered (REQ-001, NFR-001, etc.) for traceability"
else
  echo "🌐 日本語で対話し、すべてのドキュメントを日本語で生成します。"
  echo "📝 要件には番号を付与します（REQ-001、NFR-001など）- 設計時の追跡性向上のため"
fi

**ディレクトリ作成:**
1. 入力を解析してディレクトリ名を生成（英語のケバブケース形式）
   - "株価分析アプリ" → `.specs/stock-analysis-app/`
   - "TODOアプリ" → `.specs/todo-app/`
   - "ユーザー認証システム" → `.specs/user-authentication-system/`
   - "システム管理ユーザー管理" → `.specs/system-admin-user-management/`

2. ディレクトリが存在しない場合は作成:
   ```bash
   mkdir -p .specs/[feature-name]/
   ```

3. そのディレクトリにrequirement.mdを生成:
   ```
   .specs/[feature-name]/requirement.md
   ```

**このアプローチの利点:**
- 各機能/アプリに独自の仕様セット
- 既存の仕様を上書きしない
- すべての機能の明確な履歴
- いつ何が実装されたかを簡単に追跡
- 統合のために他の仕様を参照可能

生成されたrequirement.mdは適切なディレクトリに保存されます！

## 要件番号付けフォーマット

requirement.mdは以下の番号体系で生成されます：

### 番号体系
- **[REQ-XXX]**: 機能要件 (Functional Requirements)
- **[NFR-XXX]**: 非機能要件 (Non-Functional Requirements)  
- **[CON-XXX]**: 制約事項 (Constraints)
- **[ASM-XXX]**: 前提条件 (Assumptions)

### 生成例
```markdown
# 要件定義書

## 1. 機能要件

[REQ-001] ユーザー認証機能
- メールアドレスとパスワードでログインできる
- パスワードリセット機能を提供する

[REQ-002] データ管理機能
- データの作成・更新・削除ができる
- データの検索ができる

## 2. 非機能要件

[NFR-001] パフォーマンス要件
- API応答時間は2秒以内

[NFR-002] セキュリティ要件
- HTTPS通信を使用する
```

この番号は後で/spec-designコマンドで設計書を作成する際に、要件と設計の紐付けに使用されます。

### 対話例（日本語モード）

```
私: どのようなアプリケーションを作成しますか？
あなた: TODOアプリを作りたいです
私: 素晴らしい！TODOアプリの要件について詳しく聞かせてください。
    1. 主な機能は何ですか？
    2. ユーザー認証は必要ですか？
    3. データの保存方法は？
```

### 対話例（英語モード: --en）

```
Me: What kind of application would you like to create?
You: I want to build a todo app
Me: Great! Let me ask you more about your todo app requirements.
    1. What are the main features?
    2. Do you need user authentication?
    3. How should data be stored?
```

## フォルダ名規則

仕様ディレクトリ名は常に**英語のケバブケース形式**で作成されます:
- 日本語入力 → 英語に変換
- スペースはハイフンに変換
- 小文字のみ使用

例:
- "株価分析アプリ" → `stock-analysis-app`
- "メール配信停止機能" → `email-unsubscribe-feature`
- "ユーザー認証システム" → `user-authentication-system`
- "System Admin User Management" → `system-admin-user-management`