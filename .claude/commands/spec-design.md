---
description: 要件から技術設計書を生成 - アーキテクチャ、クラス設計、データフローを作成（デフォルト：日本語）
---

# 仕様駆動設計ジェネレーター

## 使い方

```bash
/spec-design [プロジェクト名] [オプション]
```

### パラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `プロジェクト名` | 要件ファイルがあるプロジェクト | `TODOアプリ`, `株価分析ツール` |
| `--en` | 英語で生成（デフォルトは日本語） | `--en` |

### 使用例

```bash
# 日本語で設計書生成（デフォルト）
/spec-design TODOアプリ
/spec-design 株価分析ツール

# 英語で設計書生成
/spec-design todo-app --en
/spec-design "stock analysis" --en
```

## 言語設定の確認

入力内容: "$ARGUMENTS"

!if [[ "$ARGUMENTS" == *"--en"* ]]; then echo "🌐 Language: English - Generating design document in English"; else echo "🌐 言語: 日本語 - 設計書を日本語で生成します"; fi

## ステップ1: 要件の特定

技術設計を作成するために要件を見つけて分析します。

!echo "要件を検索中: $ARGUMENTS"

適切な仕様ディレクトリを探します:

!find .specs -name "requirement.md" -type f | grep -i "$ARGUMENTS" || find .specs -type d -name "*$ARGUMENTS*"

## ステップ2: 既存プロジェクト構造の分析

既存プロジェクトか新規プロジェクトかを確認します:

!find . -name "package.json" -o -name "requirements.txt" -o -name "pom.xml" -o -name "go.mod" -o -name "Cargo.toml" | head -5

既存プロジェクトの場合、以下を分析します:
- 現在のディレクトリ構造とファイル編成
- 既存の技術スタックと依存関係
- コードパターンとアーキテクチャの決定事項
- 利用可能な統合ポイント

!ls -la src/ 2>/dev/null || ls -la app/ 2>/dev/null || ls -la lib/ 2>/dev/null || echo "標準的なソースディレクトリが見つかりません"

## ステップ3: 要件の読み込み

requirement.mdファイルを見つけたら、以下を分析します:
- 機能要件とその複雑さ
- 技術的な制約と好み
- パフォーマンスとスケーラビリティのニーズ
- 統合要件
- 新機能が既存アーキテクチャにどう適合するか

@.specs/*/requirement.md

## ステップ4: 技術設計の生成

要件と既存プロジェクトの分析に基づいて、以下の設計を作成します:

**新規プロジェクトの場合:**
- ゼロから完全なアーキテクチャを定義
- 最適な技術スタックを選択
- 標準的なプロジェクト構造を作成
- すべてのインフラコンポーネントを設定

**既存プロジェクト（機能追加）の場合:**
- 現在のアーキテクチャパターンと統合
- 既存のサービスとユーティリティを再利用
- 確立されたコーディング規約に従う
- 既存コードへの変更を最小限に
- 必要な新しい依存関係のみ追加

プロジェクトのコンテキストに基づいて設計書を適応させます:

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "### 3.1 Architecture Overview"
  echo "- High-level system architecture"
  echo "- Component interaction diagram"
  echo "- Data flow visualization"
else
  echo "### 3.1 アーキテクチャ概要"
  echo "- 高レベルシステムアーキテクチャ"
  echo "- コンポーネント相互作用図"
  echo "- データフロー可視化"
fi

### 3.2 技術スタック
具体的なバージョンを含む詳細な技術選択:

- **言語**: 
  - メイン: [言語 v.X.X]
  - サブ: [該当する場合]
  
- **フレームワーク**:
  - Web: [フレームワーク v.X.X]
  - API: [フレームワーク v.X.X]
  - テスト: [フレームワーク v.X.X]
  
- **ライブラリ**:
  - バージョン付きコア依存関係
  - 各ライブラリの目的と根拠
  - セキュリティ/パフォーマンスの考慮事項
  
- **開発ツール**:
  - ビルド: [ツール v.X.X]
  - テスト: [ツール v.X.X]  
  - リント: [ツール v.X.X]
  - パッケージマネージャー: [ツール v.X.X]
  
- **インフラ**:
  - データベース: [DB v.X.X]
  - キャッシュ: [Cache v.X.X]
  - コンテナ: [Docker v.X.X]

### 3.3 モジュール・クラス設計
各主要コンポーネントについて:
- 目的と責任
- パブリックインターフェース（メソッド、API）
- 依存関係と相互作用
- エラーハンドリング戦略

### 3.4 データ設計
- データモデルとスキーマ
- データベース設計（該当する場合）
- コンポーネント間のデータフロー
- ストレージとキャッシング戦略

### 3.5 技術的決定事項
- フレームワークとライブラリの選択根拠
- 使用するデザインパターン
- パフォーマンス最適化戦略
- セキュリティの考慮事項

### 3.6 実装ガイドライン
- コーディング標準と規約
- テスト戦略
- デプロイメントの考慮事項
- モニタリングとロギングのアプローチ

## ステップ5: 設計書の保存

design.mdはrequirement.mdと同じディレクトリに保存されます:
`.specs/[機能名]/design.md`

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "The document will be generated entirely in English with:"
  echo "- Clear technical specifications"
  echo "- Diagrams in ASCII or Mermaid format"
  echo "- Clear mapping to requirements"
  echo "- Revision history"
else
  echo "ドキュメントは日本語で生成され、以下を含みます:"
  echo "- 明確な技術仕様"
  echo "- ASCIIまたはMermaid形式の図"
  echo "- 要件への明確なマッピング"
  echo "- 改訂履歴"
fi