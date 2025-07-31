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

## ステップ3: 設計書の読み込み

design.mdファイルを見つけたら、以下を分析します:
- アーキテクチャコンポーネントとその依存関係
- モジュールとクラスの仕様
- データモデルとデータベーススキーマ
- 統合ポイントとAPI
- テスト要件
- 既存コードとの適合性

@.specs/*/design.md

## ステップ4: 実装タスクの生成

設計とプロジェクトコンテキストに基づいて、以下のタスクを作成します:

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

### 3.1 タスク構造
各タスクには以下を含みます:
- **タスクID**: 追跡用の一意識別子
- **タスク名**: 明確で実行可能な説明
- **依存関係**: 先に完了すべき他のタスク
- **推定時間**: 完了までの時間/日数
- **受け入れ基準**: 完了を確認する方法
- **実装メモ**: 技術的な詳細とヒント

### 3.2 タスクカテゴリー

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "1. **Environment Setup**"
  echo "   - Project initialization"
  echo "   - Dependency installation"
  echo "   - Configuration setup"
  echo ""
  echo "2. **Infrastructure**"
  echo "   - Database setup"
  echo "   - Cache implementation"
  echo "   - Logging configuration"
  echo ""
  echo "3. **Core Components**"
  echo "   - Data layer implementation"
  echo "   - Service layer implementation"
  echo "   - API development"
  echo ""
  echo "4. **Business Logic**"
  echo "   - Feature implementation"
  echo "   - Algorithm development"
  echo "   - Integration work"
  echo ""
  echo "5. **UI Development**"
  echo "   - Component creation"
  echo "   - Layout implementation"
  echo "   - Internationalization"
  echo ""
  echo "6. **Testing**"
  echo "   - Unit test creation"
  echo "   - Integration testing"
  echo "   - Performance testing"
  echo ""
  echo "7. **Documentation**"
  echo "   - API documentation"
  echo "   - User guides"
  echo "   - Deployment docs"
else
  echo "1. **環境構築**"
  echo "   - プロジェクト初期化"
  echo "   - 依存関係のインストール"
  echo "   - 設定のセットアップ"
  echo ""
  echo "2. **インフラストラクチャ**"
  echo "   - データベースセットアップ"
  echo "   - キャッシュ実装"
  echo "   - ログ設定"
  echo ""
  echo "3. **コアコンポーネント**"
  echo "   - データ層の実装"
  echo "   - サービス層の実装"
  echo "   - API開発"
  echo ""
  echo "4. **ビジネスロジック**"
  echo "   - 機能実装"
  echo "   - アルゴリズム開発"
  echo "   - 統合作業"
  echo ""
  echo "5. **UI開発**"
  echo "   - コンポーネント作成"
  echo "   - レイアウト実装"
  echo "   - 国際化対応"
  echo ""
  echo "6. **テスト**"
  echo "   - ユニットテスト作成"
  echo "   - 統合テスト"
  echo "   - パフォーマンステスト"
  echo ""
  echo "7. **ドキュメント**"
  echo "   - APIドキュメント"
  echo "   - ユーザーガイド"
  echo "   - デプロイメントドキュメント"
fi

### 3.3 タスク優先順位
タスクは以下の基準で順序付けされます:
1. 技術的依存関係（最初に構築すべきもの）
2. クリティカルパス項目（他の作業をブロックするもの）
3. リスクレベル（複雑な項目を早期に対処）
4. ビジネス価値（高影響の機能）

## ステップ5: タスク文書の保存

tasks.mdはdesign.mdと同じディレクトリに保存されます:
`.specs/[機能名]/tasks.md`

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "The document will include:"
  echo "- Numbered task list with clear dependencies"
  echo "- Gantt chart representation (ASCII)"
  echo "- Sprint/milestone suggestions"
  echo "- Command examples for common operations"
  echo "- All content in English"
else
  echo "ドキュメントには以下が含まれます:"
  echo "- 明確な依存関係を持つ番号付きタスクリスト"
  echo "- ガントチャート表現（ASCII）"
  echo "- スプリント/マイルストーンの提案"
  echo "- 一般的な操作のコマンド例"
  echo "- すべてのコンテンツが日本語"
fi