---
description: 設計書から実装タスクを生成 - 順序付けされた実行可能な開発タスクを作成（デフォルト：日本語）
---

# 仕様駆動タスクジェネレーター

## 使い方

```bash
/spec-tasks [プロジェクト名] [オプション]
```

### パラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `プロジェクト名` | 設計書があるプロジェクト | `TODOアプリ`, `株価分析ツール` |
| `--en` | 英語で生成（デフォルトは日本語） | `--en` |

### 使用例

```bash
# 日本語でタスクリスト生成（デフォルト）
/spec-tasks TODOアプリ
/spec-tasks 株価分析ツール

# 英語でタスクリスト生成
/spec-tasks todo-app --en
/spec-tasks "stock analysis" --en
```

## 言語設定の確認

入力内容: "$ARGUMENTS"

!if [[ "$ARGUMENTS" == *"--en"* ]]; then echo "🌐 Language: English - Generating task list in English"; else echo "🌐 言語: 日本語 - タスクリストを日本語で生成します"; fi

## ステップ1: 設計書の特定

実装タスクを作成するために設計書を見つけて分析します。

!echo "設計書を検索中: $ARGUMENTS"

適切な仕様ディレクトリを探します:

!find .specs -name "design.md" -type f | grep -i "$ARGUMENTS" || find .specs -type d -name "*$ARGUMENTS*"

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

### 既存実装の確認

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

## ステップ4: 実装タスクの直接生成とファイル作成

設計書の内容に基づいて、実装タスクを生成し、即座にtasks.mdファイルに保存します:

@.specs/*/design.md

### タスク生成の原則

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📌 タスク生成の原則"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "1. すべてのタスクは要件IDまたは設計書と紐付ける"
!echo "2. 設計書にない機能のタスクは生成しない"
!echo "3. 既存実装と重複するタスクは除外する"
!echo "4. プロジェクトタイプに応じた標準タスクを含める"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

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

### 3.1 タスク構造（改善版）
各タスクには以下を含みます:
- **タスクID**: 追跡用の一意識別子 (T001, T002...)
- **要件ID**: 対応する要件番号 (REQ-001, NFR-001...)
- **タスク名**: 明確で実行可能な説明
- **設計書参照**: design.md の該当箇所 (L:123-145)
- **依存関係**: 先に完了すべき他のタスク
- **推定時間**: 完了までの時間/日数
- **受け入れ基準**: 完了を確認する方法
- **実装ファイル**: 作成/修正するファイルパス

### 3.2 タスクカテゴリー（動的生成）

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
fi

!if grep -q "API\|REST\|GraphQL" .specs/*/design.md 2>/dev/null; then
  echo "- API仕様書の作成"
  echo "- エラーハンドリングの統一"
fi

!if grep -q "データベース\|Database\|DB" .specs/*/design.md 2>/dev/null; then
  echo "- マイグレーションスクリプト"
  echo "- データバックアップ戦略（本番環境向け）"
fi

#### 除外タスク（要件にない場合）

!echo ""
!echo "### ⚠️ 以下は要件に明記されていない限り生成しない："
!echo "❌ 詳細な監視・メトリクス収集"
!echo "❌ 管理画面・ダッシュボード"
!echo "❌ 高度なキャッシング戦略"
!echo "❌ 負荷分散・スケーリング設定"
!echo "❌ 詳細なパフォーマンステスト（NFRに記載がない限り）"

### 4.1 設計書の分析とタスク抽出

設計書から以下の項目を特定してタスク化します：

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔍 設計書からのタスク抽出"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "1. 各要件ID（REQ-XXX）に対応する実装タスク"
!echo "2. アーキテクチャコンポーネントの実装"
!echo "3. データモデル・API実装"
!echo "4. テスト実装（単体・統合）"
!echo "5. ドキュメント・設定ファイル作成"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

### 4.2 タスクの構造化と優先順位付け

各タスクは以下の情報を含みます：
- **タスクID**: T001, T002... 形式
- **要件ID**: 対応するREQ-XXX, NFR-XXX
- **依存関係**: 先行タスクとブロックするタスク
- **推定時間**: 現実的な工数見積もり
- **実装ファイル**: 作成・修正対象ファイル
- **受け入れ基準**: 完了判定条件

### 4.3 tasks.mdファイルの生成

分析結果に基づいて、以下の構造でtasks.mdを作成します：

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "# [Project Name] Implementation Tasks"
  echo ""
  echo "## 1. Overview"
  echo "This document breaks down implementation tasks based on requirement.md and design.md"
  echo ""
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
  echo ""
  echo "## 3. Task Details"
  echo ""
  echo "### T001: Environment Setup"
  echo "- Requirement ID: - (Infrastructure)"
  echo "- Design Reference: design.md L:45-60"
  echo "- Dependencies: None"
  echo "- Estimated Time: 1h"
  echo "- Files: package.json, .env"
  echo "- Acceptance Criteria:"
  echo "  - [ ] Dependencies installed"
  echo "  - [ ] Development environment starts"
  echo ""
  echo "### T002: [REQ-001] Authentication Feature"
  echo "- Requirement ID: REQ-001"
  echo "- Design Reference: design.md L:120-180"
  echo "- Dependencies: T001"
  echo "- Estimated Time: 4h"
  echo "- Files: src/auth/*, src/api/auth.ts"
  echo "- Acceptance Criteria:"
  echo "  - [ ] Login functionality works"
  echo "  - [ ] JWT tokens are issued"
  echo "  - [ ] Unit tests pass"
else
  echo "# [機能名] 実装タスク分解書"
  echo ""
  echo "## 1. 概要"
  echo "このドキュメントは、要件定義書（requirement.md）と技術設計書（design.md）に基づいて、"
  echo "実装タスクを詳細に分解したものです。"
  echo ""
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
  echo ""
  echo "## 3. タスク詳細"
  echo ""
  echo "### T001: 環境構築"
  echo "- 要件ID: -（基盤タスク）"
  echo "- 設計書参照: design.md L:45-60"
  echo "- 依存関係: なし"
  echo "- 推定時間: 1時間"
  echo "- 対象ファイル: package.json, .env"
  echo "- 完了条件:"
  echo "  - [ ] 必要な依存関係がインストール済み"
  echo "  - [ ] 開発環境が正常に起動する"
  echo ""
  echo "### T002: [REQ-001] 認証機能"
  echo "- 要件ID: REQ-001"
  echo "- 設計書参照: design.md L:120-180"
  echo "- 依存関係: T001"
  echo "- 推定時間: 4時間"
  echo "- 対象ファイル: src/auth/*, src/api/auth.ts"
  echo "- 完了条件:"
  echo "  - [ ] ログイン機能が動作する"
  echo "  - [ ] JWTトークンが正しく発行される"
  echo "  - [ ] 単体テストがすべてパスする"
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
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "Task breakdown document will be generated in English with:"
  echo "- Requirement traceability (REQ-XXX, NFR-XXX)"
  echo "- Clear task dependencies"
  echo "- Realistic time estimates"
  echo "- Specific acceptance criteria"
else
  echo "タスク分解書は日本語で生成され、以下を含みます："
  echo "- 要件トレーサビリティ（REQ-XXX, NFR-XXX）"
  echo "- 明確なタスク依存関係"
  echo "- 現実的な時間見積もり"
  echo "- 具体的な完了条件"
fi