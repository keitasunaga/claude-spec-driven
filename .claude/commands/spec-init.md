---
description: 要件定義を生成 - 既存コードの分析とクイックモード・対話モードに対応、ソクラテス式探索と多面的分析機能を統合（デフォルト：日本語）
mcp-servers: [sequential, context7, magic, playwright, morphllm, serena]
personas: [architect, analyzer, frontend, backend, security, devops, project-manager]
---

# 仕様駆動要件ジェネレーター - インタラクティブ要件発見システム

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
| `--deep` または `-d` | ソクラテス式深掘り対話モード | `--deep`, `-d` |
| `--personas` または `-p` | 複数視点による多面的分析を有効化 | `--personas`, `-p` |
| `--advanced` | MCP統合による高度な分析機能を有効化 | `--advanced` |

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

# ソクラテス式深掘り対話
/spec-init AIアシスタント --deep  # なぜこのプロジェクトが必要かから探索
/spec-init ECサイト --deep --personas  # 深掘り対話＋多面的分析

# 高度な分析機能
/spec-init 決済システム --advanced  # MCPサーバーによる包括的分析
/spec-init マイクロサービス --analyze --advanced  # 既存コード分析＋高度な分析
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

### 分析モードの確認
!if [[ "$ARGUMENTS" == *"--deep"* ]] || [[ "$ARGUMENTS" == *"-d"* ]]; then echo "🤔 モード: ソクラテス式深掘り対話"; fi
!if [[ "$ARGUMENTS" == *"--personas"* ]] || [[ "$ARGUMENTS" == *"-p"* ]]; then echo "👥 モード: 複数視点分析を有効化"; fi
!if [[ "$ARGUMENTS" == *"--advanced"* ]]; then echo "🚀 モード: MCP統合による高度な分析"; fi

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

### 🤔 ソクラテス式深掘り対話モード
**使用場面**: プロジェクトの本質的な目的から要件を探索したい場合
- 起動条件: `--deep` または `-d` フラグ
- 例: `/spec-init プロジェクト --deep`
- 実行内容:
  - 「なぜこのプロジェクトが必要か？」から開始
  - 問題の本質を理解するための探索的な質問
  - ユーザーの潜在的なニーズを引き出す
  - 隠れた要件や前提条件を明確化
  - より深い理解に基づいた包括的な要件定義

### 👥 複数視点分析モード（ペルソナ）
**使用場面**: 多面的な視点から要件を検討したい場合
- 起動条件: `--personas` または `-p` フラグ
- 例: `/spec-init システム --personas`
- 実行内容:
  - **アーキテクト（architect）**: システム設計、拡張性、保守性
  - **アナライザー（analyzer）**: データフロー、ボトルネック分析、最適化
  - **フロントエンド（frontend）**: UI/UX、レスポンシブ、アクセシビリティ
  - **バックエンド（backend）**: API設計、データ処理、パフォーマンス
  - **セキュリティ（security）**: 脆弱性、認証認可、データ保護
  - **DevOps（devops）**: デプロイ、監視、スケーラビリティ
  - **プロジェクトマネージャー（project-manager）**: スケジュール、リソース、リスク管理
  - 各視点からの要件と推奨事項を統合

### 🚀 高度な分析モード（MCP統合）
**使用場面**: AIを活用した包括的な分析が必要な場合
- 起動条件: `--advanced` フラグ
- 例: `/spec-init プロジェクト --advanced`
- 実行内容:
  - **Sequential MCP**: 複雑な要件の体系的な分析と推論
  - **Context7 MCP**: フレームワーク固有のベストプラクティス適用
  - **Magic MCP**: UI/UXパターンの推奨とコンポーネント設計
  - **Playwright MCP**: テスト要件とE2Eシナリオの検討
  - **Morphllm MCP**: コード変換要件とリファクタリング戦略
  - **Serena MCP**: 既存コードの深い意味解析とセッション管理
  - 各MCPサーバーの分析結果を統合した高度な要件定義

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

### 探索フェーズ（brainstormのフローを統合）

!if [[ "$ARGUMENTS" == *"--deep"* ]] || [[ "$ARGUMENTS" == *"--advanced"* ]]; then
  echo ""
  echo "📊 要件発見フェーズを開始します:"
  echo "1. 探索（Explore）: 本質的な問題と目的の理解"
  echo "2. 分析（Analyze）: 多面的な観点からの検討" 
  echo "3. 検証（Validate）: 実現可能性と制約の確認"
  echo "4. 仕様化（Specify）: 具体的な要件への変換"
  echo "5. 引き渡し（Handoff）: 実装可能な仕様書の作成"
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

#### 標準対話モード
```
私: どのようなアプリケーションを作成しますか？
あなた: TODOアプリを作りたいです
私: 素晴らしい！TODOアプリの要件について詳しく聞かせてください。
    1. 主な機能は何ですか？
    2. ユーザー認証は必要ですか？
    3. データの保存方法は？
```

#### ソクラテス式深掘り対話（--deep）
```
私: どのようなアプリケーションを作成しようと考えていますか？
あなた: TODOアプリを作りたいです
私: なるほど、TODOアプリですね。まず根本的な質問から始めましょう。
    
    🤔 なぜTODOアプリが必要だと感じましたか？
    - 現在どのような課題を抱えていますか？
    - 既存のTODOアプリでは解決できない問題は何ですか？
    
あなた: チームでのタスク管理が煩雑で...
私: チームでのタスク管理の課題ですね。もう少し深く探ってみましょう。
    
    🎯 チームの課題の本質：
    - チームの規模と役割は？
    - 現在のワークフローのボトルネックは？
    - 理想的なタスク管理とは？
```

#### 複数視点分析（--personas）
```
私: TODOアプリの要件を多面的に分析します。

👤 アーキテクト視点:
- マルチテナント対応は必要ですか？
- 将来的な機能拡張の可能性は？
- 他システムとの連携要件は？

📊 アナライザー視点:
- 想定されるデータ量とトラフィックは？
- パフォーマンスボトルネックの懸念は？
- 分析・レポート機能の必要性は？

🎨 フロントエンド視点:
- 対象デバイスとブラウザは？
- オフライン対応は必要ですか？
- リアルタイム更新の要件は？

🔧 バックエンド視点:
- API設計の方針（REST/GraphQL）は？
- データベース選定の基準は？
- キャッシュ戦略は？

🔐 セキュリティ視点:
- データの機密性レベルは？
- アクセス制御の粒度は？
- 監査ログの要件は？

⚙️ DevOps視点:
- デプロイ環境とCI/CDの要件は？
- 監視・アラートの必要性は？
- スケーラビリティ要件は？

📋 プロジェクトマネージャー視点:
- 開発期間とマイルストーンは？
- チーム構成とスキルセットは？
- 予算とリソース制約は？
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

### ⚠️ YAGNI原則チェック

!echo ""
!echo "🚫 以下は明確な要件のディスカッションや指示がない限り要件に含めない："
!echo "  ❌ 複雑な権限管理（管理者・一般ユーザーで十分な場合）"
!echo "  ❌ 高度な分析・レポート機能"
!echo "  ❌ APIバージョニング（外部連携要件がない限り）"
!echo "  ❌ マルチテナント対応"
!echo "  ❌ リアルタイム通知・更新"
!echo "  ❌ ソーシャルログイン（基本認証で十分な場合）"
!echo "  ❌ 詳細な監査ログ（コンプライアンス要件がない限り）"
!echo "  ❌ データ移行・移行計画"
!echo "  ❌ バッチ処理・定期実行機能"
!echo "  ❌ 非同期処理（明示的な性能要件がない限り）"

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

## 高度な分析機能とMCP統合

### Sequential MCP統合（--advanced）
複雑な要件の体系的な分析：
- 多段階の推論による要件の深掘り
- 仮説生成と検証サイクル
- 要件間の依存関係の明確化

### Context7 MCP統合（--advanced）
フレームワーク固有のベストプラクティス：
- 使用予定フレームワークの公式パターン適用
- バージョン固有の実装推奨事項
- コミュニティのベストプラクティス統合

### Serena MCP統合（--analyze --advanced）
既存コードの深い意味解析：
- シンボル解析による機能マッピング
- アーキテクチャパターンの抽出
- リファクタリング機会の特定
- クロスセッション対応による継続的な改善

### 統合的な要件生成フロー
1. **初期探索**: ソクラテス式対話で本質を理解
2. **多面的分析**: 複数ペルソナによる観点整理
3. **AI支援分析**: MCPサーバーによる深い洞察
4. **統合と検証**: すべての分析結果を統合
5. **継続的改善**: セッション間での学習と洗練