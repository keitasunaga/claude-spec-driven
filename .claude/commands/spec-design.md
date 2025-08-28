---
description: 要件から技術設計書を生成 - アーキテクチャ、クラス設計、データフローを作成、MCP統合と多面的設計レビュー対応（デフォルト：日本語）
mcp-servers: [sequential, context7, magic, morphllm, serena]
personas: [architect, backend, frontend, security, devops, database-engineer, qa-engineer]
---

# 仕様駆動設計ジェネレーター - インテリジェント設計支援システム

## 使い方

```bash
/spec-design [プロジェクト名] [オプション]
```

### パラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `プロジェクト名` | 要件ファイルがあるプロジェクト | `TODOアプリ`, `株価分析ツール` |
| `--en` | 英語で生成（デフォルトは日本語） | `--en` |
| `--deep-analysis` または `-da` | MCP活用による深い設計分析 | `--deep-analysis`, `-da` |
| `--personas` または `-p` | 複数視点による設計レビュー | `--personas`, `-p` |
| `--pattern-library` または `-pl` | デザインパターンライブラリの活用 | `--pattern-library`, `-pl` |
| `--visual` または `-v` | ダイアグラム生成の強化（Mermaid/PlantUML） | `--visual`, `-v` |
| `--iterative` または `-i` | 段階的な設計洗練モード | `--iterative`, `-i` |

### 使用例

```bash
# 日本語で設計書生成（デフォルト）
/spec-design TODOアプリ
/spec-design 株価分析ツール

# 英語で設計書生成
/spec-design todo-app --en
/spec-design "stock analysis" --en

# 深い設計分析
/spec-design ECサイト --deep-analysis  # MCPによる包括的分析
/spec-design 決済システム -da --personas  # 深い分析＋多面的レビュー

# デザインパターンとビジュアル化
/spec-design チャットアプリ --pattern-library --visual  # パターン適用＋図生成
/spec-design マイクロサービス -pl -v  # 省略形

# 反復的設計改善
/spec-design AIシステム --iterative  # 段階的な設計洗練
/spec-design データ分析 --iterative --personas  # 反復改善＋レビュー
```

## 言語設定の確認

入力内容: "$ARGUMENTS"

!if [[ "$ARGUMENTS" == *"--en"* ]]; then echo "🌐 Language: English - Generating design document in English"; else echo "🌐 言語: 日本語 - 設計書を日本語で生成します"; fi

## 重要な注意事項

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "📄 design.mdファイルの作成について"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo ""
!echo "⚠️ このコマンドはdesign.mdの内容を画面に表示します。"
!echo ""
!echo "【実際のファイル作成手順】"
!echo "1. 以下のステップで表示される内容を確認"
!echo "2. 要件分析結果に基づいて適切な内容を生成"
!echo "3. Writeツールを使用して .specs/[プロジェクト名]/design.md として保存"
!echo ""
!echo "保存場所の例:"
!echo "- .specs/todo-app/design.md"
!echo "- .specs/stock-analysis/design.md"
!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

### 分析モードの確認
!if [[ "$ARGUMENTS" == *"--deep-analysis"* ]] || [[ "$ARGUMENTS" == *"-da"* ]]; then echo "🧠 モード: MCP統合による深い設計分析"; fi
!if [[ "$ARGUMENTS" == *"--personas"* ]] || [[ "$ARGUMENTS" == *"-p"* ]]; then echo "👥 モード: 複数視点による設計レビュー"; fi
!if [[ "$ARGUMENTS" == *"--pattern-library"* ]] || [[ "$ARGUMENTS" == *"-pl"* ]]; then echo "📚 モード: デザインパターンライブラリ活用"; fi
!if [[ "$ARGUMENTS" == *"--visual"* ]] || [[ "$ARGUMENTS" == *"-v"* ]]; then echo "📊 モード: ビジュアル設計強化（Mermaid/PlantUML）"; fi
!if [[ "$ARGUMENTS" == *"--iterative"* ]] || [[ "$ARGUMENTS" == *"-i"* ]]; then echo "🔄 モード: 段階的設計洗練"; fi

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

### 🎯 設計フェーズ（--iterativeモード有効時）

!if [[ "$ARGUMENTS" == *"--iterative"* ]]; then
  echo ""
  echo "📊 段階的設計洗練フェーズ:"
  echo "1. 初期設計（Draft）: 基本的なアーキテクチャと構造"
  echo "2. 詳細設計（Review）: 詳細化とパターン適用"
  echo "3. 最終設計（Final）: 最適化と品質保証"
  echo "各フェーズでフィードバックと改善を実施"
fi

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

#### MCP支援による高度な分析（--deep-analysis）

!if [[ "$ARGUMENTS" == *"--deep-analysis"* ]]; then
  echo ""
  echo "🧠 Sequential MCPによるアーキテクチャ分析:"
  echo "- システム全体の複雑性評価"
  echo "- ボトルネックの予測と対策"
  echo "- スケーラビリティ戦略の提案"
  echo ""
  echo "📚 Context7 MCPによるベストプラクティス:"
  echo "- フレームワーク固有のパターン適用"
  echo "- 業界標準のアーキテクチャパターン"
  echo "- バージョン固有の推奨事項"
fi

#### デザインパターンの適用（--pattern-library）

!if [[ "$ARGUMENTS" == *"--pattern-library"* ]]; then
  echo ""
  echo "📚 適用可能なデザインパターン:"
  echo "### 生成パターン"
  echo "- Factory Method: オブジェクト生成の抽象化"
  echo "- Singleton: 単一インスタンスの保証"
  echo "- Builder: 複雑なオブジェクトの段階的構築"
  echo ""
  echo "### 構造パターン"
  echo "- Adapter: インターフェースの互換性確保"
  echo "- Facade: 複雑なサブシステムの簡略化"
  echo "- Decorator: 動的な機能拡張"
  echo ""
  echo "### 振る舞いパターン"
  echo "- Observer: イベント駆動アーキテクチャ"
  echo "- Strategy: アルゴリズムの切り替え"
  echo "- Command: 操作のカプセル化"
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

### 設計フェーズ2: モジュール・クラス設計

!echo ""
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "🔔 リマインダー: API/モジュール設計"
!echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"
!echo "✅ 既存APIのパターンを踏襲しているか？"
!echo "✅ 類似のエンドポイントが既に存在しないか？"
!echo "✅ 共通コンポーネントを再利用しているか？"
!echo "✅ 各設計に要件IDを明記しているか？"

#### 複数視点による設計レビュー（--personas）

!if [[ "$ARGUMENTS" == *"--personas"* ]]; then
  echo ""
  echo "👥 ペルソナ別設計レビューポイント:"
  echo ""
  echo "👤 アーキテクト（architect）:"
  echo "- システム全体の一貫性"
  echo "- 将来の拡張性の確保"
  echo "- 技術的負債の最小化"
  echo ""
  echo "🔧 バックエンド（backend）:"
  echo "- API設計の一貫性"
  echo "- データ処理の効率性"
  echo "- エラーハンドリングの堅牢性"
  echo ""
  echo "🎨 フロントエンド（frontend）:"
  echo "- コンポーネントの再利用性"
  echo "- 状態管理の適切性"
  echo "- パフォーマンス最適化"
  echo ""
  echo "🔐 セキュリティ（security）:"
  echo "- 認証・認可の適切性"
  echo "- データ暗号化の実装"
  echo "- 脆弱性対策の網羅性"
  echo ""
  echo "⚙️ DevOps（devops）:"
  echo "- デプロイメントの容易性"
  echo "- 監視・ロギングの実装"
  echo "- スケーラビリティの確保"
  echo ""
  echo "💾 データベースエンジニア（database-engineer）:"
  echo "- スキーマ設計の最適化"
  echo "- インデックス戦略"
  echo "- トランザクション管理"
  echo ""
  echo "🧪 QAエンジニア（qa-engineer）:"
  echo "- テスタビリティの確保"
  echo "- テストカバレッジ戦略"
  echo "- 自動化可能性の評価"
fi

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

#### ビジュアル設計の強化（--visual）

!if [[ "$ARGUMENTS" == *"--visual"* ]]; then
  echo ""
  echo "📊 生成されるダイアグラム:"
  echo ""
  echo "### Mermaidダイアグラム例:"
  echo "- システムアーキテクチャ図"
  echo "- クラス図"
  echo "- シーケンス図"
  echo "- ER図"
  echo "- フローチャート"
  echo ""
  echo "### C4モデル:"
  echo "1. Context: システム全体の外部コンテキスト"
  echo "2. Container: 主要コンテナとその関係"
  echo "3. Component: コンテナ内のコンポーネント"
  echo "4. Code: クラスレベルの詳細設計"
fi

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

!if [[ "$ARGUMENTS" == *"--deep-analysis"* ]]; then
  echo ""
  echo "🧠 高度な品質チェック:"
  echo "6. SOLID原則への準拠度"
  echo "7. パフォーマンスボトルネックの予測"
  echo "8. セキュリティリスクの評価"
  echo "9. スケーラビリティの検証"
  echo "10. 技術的負債の最小化"
fi

!if [[ "$ARGUMENTS" == *"--personas"* ]]; then
  echo ""
  echo "👥 ペルソナ別承認チェック:"
  echo "- [ ] アーキテクト承認"
  echo "- [ ] バックエンド承認"
  echo "- [ ] フロントエンド承認"
  echo "- [ ] セキュリティ承認"
  echo "- [ ] DevOps承認"
  echo "- [ ] データベースエンジニア承認"
  echo "- [ ] QAエンジニア承認"
fi

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

## 高度な設計支援機能

### MCP統合（--deep-analysis）
**Sequential MCP**: 
- 複雑なアーキテクチャ決定の支援
- 多段階推論による設計の最適化
- トレードオフ分析と意思決定支援

**Context7 MCP**:
- フレームワーク固有のベストプラクティス
- 公式ドキュメントに基づく設計パターン
- バージョン固有の実装推奨事項

**Serena MCP**:
- 既存コードベースとの整合性チェック
- シンボル解析による影響範囲の特定
- リファクタリング機会の発見

**Magic MCP**:
- UI/UXコンポーネントの設計支援
- モダンなフロントエンドパターン
- レスポンシブデザインの考慮

### ペルソナレビュー（--personas）
7つの専門視点から設計を評価：
1. **アーキテクト**: システム全体の整合性
2. **バックエンド**: API設計とデータ処理
3. **フロントエンド**: UI/UXとコンポーネント設計
4. **セキュリティ**: 脆弱性とセキュアコーディング
5. **DevOps**: デプロイメントとスケーラビリティ
6. **データベースエンジニア**: スキーマとパフォーマンス
7. **QAエンジニア**: テスタビリティと品質保証

### 統合的な設計フロー
```
1. 要件分析 → 初期設計生成
2. MCP分析 → 設計の深化と最適化
3. ペルソナレビュー → 多面的な評価
4. パターン適用 → ベストプラクティスの統合
5. ビジュアル化 → 理解しやすい図の生成
6. 反復改善 → 継続的な洗練
```