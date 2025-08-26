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

## ステップ2.5: 🗺️ 既存資産マップの作成【重要】

既存の共通コンポーネントと再利用可能な資産を特定します。
この情報は設計時に必ず参照し、車輪の再発明を防ぎます。

### 共通コンポーネントの検出

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📦 既存資産の収集と分析"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

!echo "### 🔧 共通コンポーネント"
!find . -type d \( -name "shared" -o -name "common" -o -name "components" -o -name "lib" -o -name "utils" \) -not -path "*/node_modules/*" -not -path "*/.git/*" 2>/dev/null | head -10

!echo ""
!echo "### 🎯 既存サービス/モジュール"
!find . -type f \( -name "*Service*" -o -name "*Repository*" -o -name "*Controller*" -o -name "*Handler*" \) -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/dist/*" -not -path "*/build/*" 2>/dev/null | grep -E "\.(ts|js|py|go|java)$" | head -15

!echo ""
!echo "### 🔐 認証・認可関連"
!find . -type f \( -name "*auth*" -o -name "*Auth*" -o -name "*login*" -o -name "*Login*" \) -not -path "*/node_modules/*" -not -path "*/.git/*" 2>/dev/null | grep -E "\.(ts|js|py|go|java)$" | head -10

!echo ""
!echo "### 📊 データモデル/型定義"
!find . -type d \( -name "models" -o -name "types" -o -name "entities" -o -name "schemas" -o -name "dto" \) -not -path "*/node_modules/*" -not -path "*/.git/*" 2>/dev/null | head -10

!echo ""
!echo "### 🛠️ ユーティリティ関数"
!find . -path "*/utils/*" -o -path "*/helpers/*" -o -path "*/common/*" -type f -not -path "*/node_modules/*" -not -path "*/.git/*" 2>/dev/null | grep -E "\.(ts|js|py|go|java)$" | head -15

!echo ""
!echo "### 📝 既存のAPI/エンドポイント"
!find . -type f \( -name "*route*" -o -name "*Route*" -o -name "*endpoint*" -o -name "*api*" \) -not -path "*/node_modules/*" -not -path "*/.git/*" 2>/dev/null | grep -E "\.(ts|js|py|go|java)$" | head -10

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📌 既存実装の調査方法"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo ""
!echo "【重要】上記のファイルの実装詳細を確認してください："
!echo ""
!echo "1. 🎯 優先使用: Serena MCP（トークン効率的）"
!echo "   mcp__serena__get_symbols_overview('ファイルパス')"
!echo "   → クラス名、メソッド名の概要を取得"
!echo "   mcp__serena__find_symbol('クラス名', include_body: true)"
!echo "   → 必要な部分の詳細を取得"
!echo ""
!echo "2. 📖 Serenaが使えない場合: Readツール"
!echo "   Read('ファイルパス')"
!echo "   → ファイル全体を読み込み（確実だがトークン消費大）"
!echo ""
!echo "特に認証・トークン管理・HTTPクライアント関連は詳細確認必須"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

## ステップ3: 要件の読み込みと番号の抽出

requirement.mdファイルを見つけたら、以下を分析します:
- 機能要件とその複雑さ
- 技術的な制約と好み
- パフォーマンスとスケーラビリティのニーズ
- 統合要件
- 新機能が既存アーキテクチャにどう適合するか

### 要件番号の自動抽出

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📋 要件番号の抽出"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

要件ファイルから番号付き要件を抽出します：

!echo "### 機能要件 (REQ-XXX)"
!grep -E "^\[REQ-[0-9]+\]" .specs/*/requirement.md 2>/dev/null || echo "番号付き機能要件が見つかりません"

!echo ""
!echo "### 非機能要件 (NFR-XXX)"
!grep -E "^\[NFR-[0-9]+\]" .specs/*/requirement.md 2>/dev/null || echo "番号付き非機能要件が見つかりません"

!echo ""
!echo "### 制約事項 (CON-XXX)"
!grep -E "^\[CON-[0-9]+\]" .specs/*/requirement.md 2>/dev/null || echo "番号付き制約事項が見つかりません"

@.specs/*/requirement.md

## ステップ4: 技術設計の生成【要件トレーサビリティ必須】

要件と既存プロジェクトの分析に基づいて、以下の設計を作成します:

### ⚠️ 設計の大原則

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📌 要件トレーサビリティの確保"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "1. すべての設計項目は要件番号と紐付ける"
!echo "2. 要件にない機能は設計に含めない"
!echo "3. 既存資産を最大限活用する"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

### 📋 要件トレーサビリティマトリックス

設計書の冒頭に以下の形式でトレーサビリティマトリックスを作成します：

```markdown
## 要件トレーサビリティマトリックス

| 要件ID | 要件内容（要約） | 設計項目 | 既存資産の活用 | 新規作成理由 |
|--------|----------------|---------|--------------|-------------|
| REQ-001 | ユーザー認証 | AuthService | ✅ 既存利用 | - |
| REQ-002 | データ管理 | DataAPI | ❌ 新規作成 | 特殊な要件のため |
| REQ-003 | 検索機能 | SearchModule | ✅ 既存を拡張 | - |
```

### ⚠️ YAGNI原則チェック

!echo ""
!echo "🚫 以下は要件に明記されていない限り設計に含めない："
!echo "  ❌ モニタリング・監視機能"
!echo "  ❌ ログ収集・分析システム"
!echo "  ❌ キャッシュ層（パフォーマンス要件がない限り）"
!echo "  ❌ 非同期処理（明示的に必要でない限り）"
!echo "  ❌ 管理画面・ダッシュボード"
!echo "  ❌ 通知・アラート機能"
!echo "  ❌ バックアップ・リストア機能"

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

### 設計フェーズ1: アーキテクチャ設計

!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔔 リマインダー: アーキテクチャ設計"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "✅ 既存のアーキテクチャパターンと整合性を保つ"
!echo "✅ 既存サービスの構成を確認済みか？"
!echo "✅ 要件IDと紐付けているか？"

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

### 設計フェーズ2: モジュール・クラス設計

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔔 リマインダー: API/モジュール設計"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "✅ 既存APIのパターンを踏襲しているか？"
!echo "✅ 類似のエンドポイントが既に存在しないか？"
!echo "✅ 共通コンポーネントを再利用しているか？"
!echo "✅ 各設計に要件IDを明記しているか？"

### 3.3 モジュール・クラス設計
各主要コンポーネントについて:
- 目的と責任
- パブリックインターフェース（メソッド、API）
- 依存関係と相互作用
- エラーハンドリング戦略

**要件引用フォーマット:**
```
#### [要件ID] 機能名
> 📌 要件: "[requirement.mdからの引用]"
設計: [具体的な設計内容]
```

### 設計フェーズ3: データ設計

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔔 リマインダー: データモデル設計"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "✅ 既存モデルとの関連性を確認したか？"
!echo "✅ 既存のモデル定義を拡張できないか？"
!echo "✅ 要件にないデータ項目を追加していないか？"

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

## ステップ5: 設計書の保存と品質チェック

design.mdはrequirement.mdと同じディレクトリに保存されます:
`.specs/[機能名]/design.md`

### 📊 設計書の品質チェック

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "✅ 設計書の最終チェックリスト"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "1. すべての要件IDが設計書に含まれているか？"
!echo "2. 要件にない機能を追加していないか？"
!echo "3. 既存資産の活用を検討したか？"
!echo "4. 要件トレーサビリティマトリックスは完成しているか？"
!echo "5. YAGNI原則に違反していないか？"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

!if [[ "$ARGUMENTS" == *"--en"* ]]; then
  echo "The document will be generated entirely in English with:"
  echo "- Clear technical specifications"
  echo "- Diagrams in ASCII or Mermaid format"
  echo "- Clear mapping to requirements (with requirement IDs)"
  echo "- Requirement traceability matrix"
  echo "- Existing asset utilization plan"
  echo "- Revision history"
else
  echo "ドキュメントは日本語で生成され、以下を含みます:"
  echo "- 明確な技術仕様"
  echo "- ASCIIまたはMermaid形式の図"
  echo "- 要件への明確なマッピング（要件ID付き）"
  echo "- 要件トレーサビリティマトリックス"
  echo "- 既存資産活用計画"
  echo "- 改訂履歴"
fi