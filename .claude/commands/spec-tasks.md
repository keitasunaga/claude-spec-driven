---
description: 設計書から実装タスクを生成 - 階層的タスク管理とインテリジェントな分析を統合（デフォルト：日本語）
category: planning
complexity: advanced
mcp-servers: [sequential, serena, context7]
personas: [architect, analyzer, project-manager]
---

# 仕様駆動タスクジェネレーター（強化版）

## 使い方

```bash
/spec-tasks [プロジェクト名] [オプション]
```

### パラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `プロジェクト名` | 設計書があるプロジェクト | `TODOアプリ`, `株価分析ツール` |
| `--en` | 英語で生成（デフォルトは日本語） | `--en` |
| `--strategy` | 実行戦略（systematic/agile/enterprise） | `--strategy agile` |
| `--depth` | 分析の深さ（quick/standard/deep） | `--depth deep` |
| `--analyze` | プロジェクト分析を含める | `--analyze` |
| `--estimate` | 見積もりとリスク評価を含める | `--estimate` |
| `--hierarchy` | タスク階層（epic/story/task/subtask） | `--hierarchy epic` |

### 使用例

```bash
# 基本的なタスクリスト生成
/spec-tasks TODOアプリ

# 深い分析と見積もりを含む生成
/spec-tasks 株価分析ツール --depth deep --estimate

# アジャイル戦略で英語生成
/spec-tasks "payment system" --en --strategy agile

# 完全な階層構造で生成
/spec-tasks ECサイト --hierarchy epic --analyze
```

## 言語設定の確認

入力内容: "$ARGUMENTS"

!if [[ "$ARGUMENTS" == *"--en"* ]]; then echo "🌐 Language: English - Generating task list in English"; else echo "🌐 言語: 日本語 - タスクリストを日本語で生成します"; fi

## ステップ0: 初期分析とコンテキスト理解

### 要件の明確化（Brainstorming要素）

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🤔 要件理解フェーズ"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

設計書を探す前に、プロジェクトの本質を理解します：

!echo "プロジェクト「$ARGUMENTS」について以下を確認します："
!echo "1. 設計書の存在確認"
!echo "2. 既存実装の有無"
!echo "3. プロジェクトタイプの判定"
!echo "4. 適切な戦略の選択"

### 実行戦略の決定

!if [[ "$ARGUMENTS" == *"--strategy systematic"* ]]; then 
  echo "📊 戦略: Systematic - 包括的で体系的なアプローチ"
elif [[ "$ARGUMENTS" == *"--strategy agile"* ]]; then
  echo "🚀 戦略: Agile - 反復的で柔軟なアプローチ"  
elif [[ "$ARGUMENTS" == *"--strategy enterprise"* ]]; then
  echo "🏢 戦略: Enterprise - ガバナンス重視のアプローチ"
else
  echo "🔍 戦略: 自動判定中..."
fi

## ステップ1: 設計書の特定とプロジェクト構造分析

実装タスクを作成するために設計書を見つけて分析します。

!echo "設計書を検索中: $ARGUMENTS"

適切な仕様ディレクトリを探します:

!find .specs -name "design.md" -type f | grep -i "$ARGUMENTS" || find .specs -type d -name "*$ARGUMENTS*"

### プロジェクト構造の深層分析

!if [[ "$ARGUMENTS" == *"--analyze"* ]]; then
  echo ""
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "🔍 プロジェクト深層分析"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "### 品質分析"
  echo "- コード規模とカバレッジ"
  echo "- 技術的負債の評価"
  echo "- 依存関係の健全性"
  echo ""
  echo "### セキュリティ分析"
  echo "- 既知の脆弱性チェック"
  echo "- 認証・認可の実装状況"
  echo "- データ保護の評価"
  echo ""
  echo "### パフォーマンス分析"
  echo "- 既存のボトルネック"
  echo "- スケーラビリティ評価"
  echo "- リソース使用状況"
  echo ""
  echo "### アーキテクチャ分析"
  echo "- 設計パターンの適合性"
  echo "- モジュール結合度"
  echo "- 拡張性の評価"
fi

## ステップ2: プロジェクトコンテキストの分析

新規プロジェクトか機能追加かを理解します:

!test -f package.json && echo "Node.jsプロジェクトを検出" || echo "Node.jsプロジェクトではありません"
!test -f requirements.txt && echo "Pythonプロジェクトを検出" || echo "Pythonプロジェクトではありません"
!test -d .git && echo "Gitリポジトリを検出" || echo "Gitリポジトリではありません"

既存プロジェクトの場合、以下を確認:
- 現在の開発状況
- 既存のテストインフラ
- ビルドとデプロイメントのセットアップ
- 開発ワークフロー

!test -f Makefile && cat Makefile | grep -E "^(test|build|install):" | head -5
!test -f package.json && cat package.json | jq '.scripts' 2>/dev/null | head -10

## ステップ2.5: プロジェクトタイプの判定と既存資産の確認

### プロジェクトタイプの自動判定

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📊 プロジェクトタイプの判定"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

設計書からプロジェクトタイプを判定:

!if grep -q "React\|Vue\|Angular\|Next\|Frontend\|UI" .specs/*/design.md 2>/dev/null; then echo "📱 Webアプリケーションを検出 → 国際化対応タスクを推奨"; fi
!if grep -q "REST\|GraphQL\|API\|エンドポイント" .specs/*/design.md 2>/dev/null; then echo "🔌 APIサーバーを検出 → API仕様書タスクを推奨"; fi
!if grep -q "CLI\|コマンドライン\|Terminal" .specs/*/design.md 2>/dev/null; then echo "⚡ CLIツールを検出 → ヘルプドキュメントタスクを推奨"; fi
!if grep -q "iOS\|Android\|React Native\|Flutter" .specs/*/design.md 2>/dev/null; then echo "📱 モバイルアプリを検出 → 国際化対応タスクを推奨"; fi

### 既存実装の確認（Serena MCP統合）

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔍 既存資産の確認"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

!echo "### 実装済み機能の確認"
!find . -name "*test*" -o -name "*spec*" -type f -not -path "*/node_modules/*" -not -path "*/.git/*" 2>/dev/null | head -10 || echo "テストファイルなし"

!echo ""
!echo "### 既存のビルド/開発スクリプト"
!if [ -f package.json ]; then cat package.json | jq '.scripts | keys[]' 2>/dev/null | head -10 || echo "スクリプトなし"; fi

!echo ""
!echo "### 既存のコンポーネント/モジュール"
!find . -type f \( -name "*Service*" -o -name "*Controller*" -o -name "*Component*" \) -not -path "*/node_modules/*" -not -path "*/.git/*" 2>/dev/null | head -10 || echo "既存コンポーネントなし"

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📌 既存実装の詳細調査（タスク最適化のため）"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo ""
!echo "重要：タスク生成前に既存実装を詳細確認してください"
!echo ""
!echo "1. 🎯 優先使用: Serena MCP（トークン効率的）"
!echo "   mcp__serena__get_symbols_overview('ファイルパス')"
!echo "   → 既存のクラス・メソッドを確認"
!echo "   mcp__serena__find_symbol('クラス名', include_body: true)"
!echo "   → 実装詳細を確認"
!echo ""
!echo "2. 📖 Serenaが使えない場合: Readツール"
!echo "   Read('ファイルパス')"
!echo "   → ファイル全体を確認"
!echo ""
!echo "【重要】既存実装を確認することで："
!echo "✅ 新規実装 vs 拡張タスクを適切に判断"
!echo "✅ 既存機能と重複するタスクを除外"
!echo "✅ 実装工数の正確な見積もり"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

## ステップ3: 設計書と要件IDの読み込み

### 要件番号の抽出

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📋 要件IDの抽出"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

要件ファイルから番号付き要件を抽出:

!echo "### 機能要件 (REQ-XXX)"
!grep -E "^\[REQ-[0-9]+\]" .specs/*/requirement.md 2>/dev/null | head -20 || echo "番号付き要件が見つかりません"

!echo ""
!echo "### 非機能要件 (NFR-XXX)"
!grep -E "^\[NFR-[0-9]+\]" .specs/*/requirement.md 2>/dev/null | head -10 || echo "番号付き非機能要件が見つかりません"

### 設計書の分析

design.mdファイルを見つけたら、以下を分析します:
- アーキテクチャコンポーネントとその依存関係
- モジュールとクラスの仕様
- データモデルとデータベーススキーマ
- 統合ポイントとAPI
- テスト要件
- 既存コードとの適合性
- **要件IDとの紐付け**

@.specs/*/design.md

### 要件トレーサビリティの確認

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔗 要件トレーサビリティの確認"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!grep -E "\[REQ-[0-9]+\]|\[NFR-[0-9]+\]" .specs/*/design.md 2>/dev/null | head -10 || echo "設計書に要件IDの記載がありません"

## ステップ3.5: 見積もりとリスク評価（Estimate要素）

!if [[ "$ARGUMENTS" == *"--estimate"* ]]; then
  echo ""
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "📊 見積もりとリスク評価"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "### 見積もりタイプ"
  echo "- 時間見積もり：開発時間と工数"
  echo "- 複雑性見積もり：技術的難易度"
  echo "- リソース見積もり：必要人員とスキル"
  echo ""
  echo "### 信頼区間"
  echo "- 楽観的見積もり（Best Case）"
  echo "- 現実的見積もり（Likely Case）"  
  echo "- 悲観的見積もり（Worst Case）"
  echo ""
  echo "### リスク要因"
  echo "- 技術的リスク：未知の技術、複雑な統合"
  echo "- 依存関係リスク：外部システム、サードパーティ"
  echo "- リソースリスク：スキル不足、時間制約"
  echo ""
  echo "各タスクの見積もりには上記を考慮します。"
fi

## ステップ4: 実装タスクの階層的生成

設計書の内容に基づいて、階層的な実装タスクを生成します:

@.specs/*/design.md

### タスク生成の原則

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📌 タスク生成の原則"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "1. すべてのタスクは要件IDまたは設計書と紐付ける"
!echo "2. 設計書にない機能のタスクは生成しない"
!echo "3. 既存実装と重複するタスクは除外する"
!echo "4. プロジェクトタイプに応じた標準タスクを含める"
!if [[ "$ARGUMENTS" == *"--hierarchy"* ]]; then
  echo "5. 階層構造：Epic → Story → Task → Subtask"
  echo "6. 依存関係の明確化と並列実行の最適化"
fi
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

### タスク階層（Task要素）

!if [[ "$ARGUMENTS" == *"--hierarchy epic"* ]]; then
  echo ""
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "📚 階層的タスク構造"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "### Epic（大規模機能）"
  echo "プロジェクト全体を大きな機能単位に分割"
  echo ""
  echo "### Story（ユーザーストーリー）"
  echo "各Epicをユーザー価値の単位に分割"
  echo ""
  echo "### Task（開発タスク）"
  echo "各Storyを実装可能な技術タスクに分割"
  echo ""
  echo "### Subtask（詳細タスク）"
  echo "各Taskを具体的な作業単位に分割"
fi

**新規プロジェクトの場合:**
- 完全な環境セットアップから開始
- ゼロからプロジェクト構造を作成
- すべての依存関係をインストール
- 開発ツールのセットアップ

**既存プロジェクト（機能追加）の場合:**
- 冗長なセットアップタスクをスキップ
- 機能固有の実装に集中
- 既存のテストスイートと統合
- 確立されたビルドプロセスを使用
- 不足している依存関係のみ追加

タスク生成はコンテキストを考慮します:

### 4.1 タスク構造（強化版）
各タスクには以下を含みます:
- **タスクID**: 追跡用の一意識別子 (T001, T002...)
- **要件ID**: 対応する要件番号 (REQ-001, NFR-001...)
- **タスク名**: 明確で実行可能な説明
- **設計書参照**: design.md の該当箇所 (L:123-145)
- **依存関係**: 先に完了すべき他のタスク
- **推定時間**: 完了までの時間/日数
- **受け入れ基準**: 完了を確認する方法
- **実装ファイル**: 作成/修正するファイルパス
!if [[ "$ARGUMENTS" == *"--estimate"* ]]; then
  echo "- **信頼区間**: 楽観/現実/悲観的見積もり"
  echo "- **リスクレベル**: 低/中/高"
  echo "- **前提条件**: 見積もりの前提"
fi

### 4.2 タスクカテゴリー（動的生成）

設計書とプロジェクトタイプに基づいて動的にカテゴリーを生成:

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📂 タスクカテゴリーの生成"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

#### 必須タスク（設計書ベース）

!echo "### 必須タスク（設計書から生成）"
!echo "1. **要件実装タスク**"
!echo "   - 各REQ-XXXに対応する実装"
!echo "   - 設計書に記載されたコンポーネント"
!echo ""
!echo "2. **基本テスト**"
!echo "   - 各機能のユニットテスト"
!echo "   - 基本的な動作確認"

#### 推奨タスク（プロジェクトタイプ別）

!echo ""
!echo "### 推奨タスク（プロジェクトタイプに応じて）"

!if grep -q "React\|Vue\|Angular\|Frontend" .specs/*/design.md 2>/dev/null; then
  echo "- 国際化対応の基盤実装（Webアプリのため）"
  echo "- アクセシビリティ対応"
  echo "- コンポーネントライブラリ統合（Magic MCP活用）"
fi

!if grep -q "API\|REST\|GraphQL" .specs/*/design.md 2>/dev/null; then
  echo "- API仕様書の作成"
  echo "- エラーハンドリングの統一"
  echo "- 認証・認可の実装（Security persona活用）"
fi

!if grep -q "データベース\|Database\|DB" .specs/*/design.md 2>/dev/null; then
  echo "- マイグレーションスクリプト"
  echo "- データバックアップ戦略（本番環境向け）"
  echo "- パフォーマンスインデックス設計"
fi

#### 除外タスク（要件にない場合）

!echo ""
!echo "### ⚠️ 以下は要件に明記されていない限り生成しない："
!echo "❌ 詳細な監視・メトリクス収集"
!echo "❌ 管理画面・ダッシュボード"
!echo "❌ 高度なキャッシング戦略"
!echo "❌ 負荷分散・スケーリング設定"
!echo "❌ 詳細なパフォーマンステスト（NFRに記載がない限り）"

### 4.3 タスク実行戦略（Strategy要素）

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🚀 タスク実行戦略"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

!if [[ "$ARGUMENTS" == *"--strategy systematic"* ]]; then
  echo "### Systematic戦略（包括的アプローチ）"
  echo "- すべての要件を網羅的に実装"
  echo "- 品質ゲートを各フェーズに設置"
  echo "- ドキュメント完備を重視"
  echo "- 並列実行の機会を最大化"
elif [[ "$ARGUMENTS" == *"--strategy agile"* ]]; then
  echo "### Agile戦略（反復的アプローチ）"
  echo "- MVPファーストで価値を早期提供"
  echo "- スプリント単位でタスク分割"
  echo "- 継続的なフィードバックループ"
  echo "- 柔軟な優先順位変更"
elif [[ "$ARGUMENTS" == *"--strategy enterprise"* ]]; then
  echo "### Enterprise戦略（ガバナンス重視）"
  echo "- コンプライアンス要件を優先"
  echo "- 承認プロセスの明確化"
  echo "- 監査証跡の確保"
  echo "- リスク管理の徹底"
fi

### 4.4 並列実行の最適化（Orchestration要素）

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "⚡ 並列実行の最適化"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "### 並列実行可能なタスク群"
!echo "- 独立したコンポーネント開発"
!echo "- フロントエンドとバックエンドの同時開発"
!echo "- テストとドキュメントの並行作成"
!echo ""
!echo "### 依存関係によるシーケンシャル実行"
!echo "- データモデル → API → UI の順序"
!echo "- 認証基盤 → 機能実装の順序"
!echo "- インフラ → アプリケーションの順序"

### 4.5 tasks.mdファイルの生成

分析結果に基づいて、以下の構造でtasks.mdを作成します：

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "# [Project Name] Implementation Tasks"
  echo ""
  echo "## 1. Overview"
  echo "This document breaks down implementation tasks based on requirement.md and design.md"
  echo "Generated with enhanced task management framework."
  echo ""
  if [[ "$ARGUMENTS" == *"--hierarchy epic"* ]]; then
    echo "## 2. Task Hierarchy"
    echo ""
    echo "### Epic 1: Core Infrastructure"
    echo "#### Story 1.1: Development Environment"
    echo "- Task 1.1.1: Setup project structure"
    echo "- Task 1.1.2: Configure development tools"
    echo ""
    echo "#### Story 1.2: Database Foundation"
    echo "- Task 1.2.1: Design schema"
    echo "- Task 1.2.2: Setup migrations"
  else
    echo "## 2. Task List"
    echo ""
    echo "### Phase 1: Foundation"
    echo "- Setup and infrastructure tasks"
    echo ""
    echo "### Phase 2: Core Implementation"
    echo "- Main feature implementation based on requirements"
    echo ""
    echo "### Phase 3: Testing & Documentation"
    echo "- Testing implementation and documentation updates"
  fi
  echo ""
  echo "## 3. Task Details"
  echo ""
  echo "### T001: Environment Setup"
  echo "- Requirement ID: - (Infrastructure)"
  echo "- Design Reference: design.md L:45-60"
  echo "- Dependencies: None"
  echo "- Estimated Time: 1h"
  if [[ "$ARGUMENTS" == *"--estimate"* ]]; then
    echo "- Confidence Interval: 0.5h (best) / 1h (likely) / 2h (worst)"
    echo "- Risk Level: Low"
    echo "- Assumptions: Developer familiar with toolchain"
  fi
  echo "- Files: package.json, .env"
  echo "- Acceptance Criteria:"
  echo "  - [ ] Dependencies installed"
  echo "  - [ ] Development environment starts"
  echo "- Parallel Execution: Can run with T002, T003"
  echo ""
  echo "### T002: [REQ-001] Authentication Feature"
  echo "- Requirement ID: REQ-001"
  echo "- Design Reference: design.md L:120-180"
  echo "- Dependencies: T001"
  echo "- Estimated Time: 4h"
  if [[ "$ARGUMENTS" == *"--estimate"* ]]; then
    echo "- Confidence Interval: 3h (best) / 4h (likely) / 6h (worst)"
    echo "- Risk Level: Medium"
    echo "- Assumptions: Using standard JWT library"
  fi
  echo "- Files: src/auth/*, src/api/auth.ts"
  echo "- Acceptance Criteria:"
  echo "  - [ ] Login functionality works"
  echo "  - [ ] JWT tokens are issued"
  echo "  - [ ] Unit tests pass"
  echo "- MCP Support: Context7 for auth patterns"
else
  echo "# [機能名] 実装タスク分解書"
  echo ""
  echo "## 1. 概要"
  echo "このドキュメントは、要件定義書（requirement.md）と技術設計書（design.md）に基づいて、"
  echo "実装タスクを詳細に分解したものです。"
  echo "強化されたタスク管理フレームワークで生成されています。"
  echo ""
  if [[ "$ARGUMENTS" == *"--hierarchy epic"* ]]; then
    echo "## 2. タスク階層"
    echo ""
    echo "### Epic 1: コアインフラストラクチャ"
    echo "#### Story 1.1: 開発環境"
    echo "- Task 1.1.1: プロジェクト構造のセットアップ"
    echo "- Task 1.1.2: 開発ツールの設定"
    echo ""
    echo "#### Story 1.2: データベース基盤"
    echo "- Task 1.2.1: スキーマ設計"
    echo "- Task 1.2.2: マイグレーション設定"
  else
    echo "## 2. タスク一覧"
    echo ""
    echo "### Phase 1: 基盤構築"
    echo "- 環境構築とインフラ設定タスク"
    echo ""
    echo "### Phase 2: コア実装"
    echo "- 要件に基づくメイン機能の実装"
    echo ""
    echo "### Phase 3: テスト・ドキュメント"
    echo "- テスト実装とドキュメント更新"
  fi
  echo ""
  echo "## 3. タスク詳細"
  echo ""
  echo "### T001: 環境構築"
  echo "- 要件ID: -（基盤タスク）"
  echo "- 設計書参照: design.md L:45-60"
  echo "- 依存関係: なし"
  echo "- 推定時間: 1時間"
  if [[ "$ARGUMENTS" == *"--estimate"* ]]; then
    echo "- 信頼区間: 0.5h (楽観) / 1h (現実) / 2h (悲観)"
    echo "- リスクレベル: 低"
    echo "- 前提条件: 開発者がツールチェーンに精通"
  fi
  echo "- 対象ファイル: package.json, .env"
  echo "- 完了条件:"
  echo "  - [ ] 必要な依存関係がインストール済み"
  echo "  - [ ] 開発環境が正常に起動する"
  echo "- 並列実行: T002, T003と同時実行可能"
  echo ""
  echo "### T002: [REQ-001] 認証機能"
  echo "- 要件ID: REQ-001"
  echo "- 設計書参照: design.md L:120-180"
  echo "- 依存関係: T001"
  echo "- 推定時間: 4時間"
  if [[ "$ARGUMENTS" == *"--estimate"* ]]; then
    echo "- 信頼区間: 3h (楽観) / 4h (現実) / 6h (悲観)"
    echo "- リスクレベル: 中"
    echo "- 前提条件: 標準的なJWTライブラリを使用"
  fi
  echo "- 対象ファイル: src/auth/*, src/api/auth.ts"
  echo "- 完了条件:"
  echo "  - [ ] ログイン機能が動作する"
  echo "  - [ ] JWTトークンが正しく発行される"
  echo "  - [ ] 単体テストがすべてパスする"
  echo "- MCPサポート: Context7で認証パターン参照"
fi

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📝 タスク分解書の品質チェック"  
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "1. すべてのタスクが要件IDと紐付いているか？"
!echo "2. 設計書にない機能のタスクは含まれていないか？"
!echo "3. 依存関係が明確に定義されているか？"
!echo "4. 推定時間が現実的か？"
!echo "5. 完了条件が具体的で測定可能か？"
!if [[ "$ARGUMENTS" == *"--hierarchy"* ]]; then
  echo "6. 階層構造が適切に定義されているか？"
fi
!if [[ "$ARGUMENTS" == *"--estimate"* ]]; then
  echo "7. リスク評価と信頼区間が含まれているか？"
fi
!echo "8. 並列実行の機会が最大化されているか？"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

## ステップ5: 実行とモニタリング（強化版）

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🚀 タスク実行サポート"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo ""
!echo "### タスク実行コマンド"
!echo "/sc:task execute tasks.md --strategy $STRATEGY"
!echo ""
!echo "### 進捗モニタリング"
!echo "- TodoWriteツールでタスク進捗を追跡"
!echo "- セッション間でのメモリ永続化"
!echo "- 定期的なチェックポイント作成"
!echo ""
!echo "### 品質ゲート"
!echo "- 各フェーズ完了時の品質チェック"
!echo "- テストカバレッジの確認"
!echo "- セキュリティレビューの実施"

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "Task breakdown document will be generated in English with:"
  echo "- Requirement traceability (REQ-XXX, NFR-XXX)"
  echo "- Clear task dependencies and parallel execution opportunities"
  echo "- Realistic time estimates with confidence intervals"
  echo "- Specific acceptance criteria"
  echo "- Integration with MCP servers for enhanced capabilities"
  echo "- Hierarchical task organization when requested"
else
  echo "タスク分解書は日本語で生成され、以下を含みます："
  echo "- 要件トレーサビリティ（REQ-XXX, NFR-XXX）"
  echo "- 明確なタスク依存関係と並列実行の機会"
  echo "- 信頼区間を含む現実的な時間見積もり"
  echo "- 具体的な完了条件"
  echo "- MCPサーバー統合による拡張機能"
  echo "- 要求に応じた階層的タスク構造"
fi