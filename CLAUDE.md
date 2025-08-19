# Claude Code 自律型三位一体開発憲法

## 🔺 自律型三位一体フレームワークの原則

このプロジェクトでは、**Serena-MCP・Claude・Gemini**の自律型三位一体で開発を進めます：

|ロール            |責任範囲            |強み               |弱み            |
|---------------|----------------|-----------------|--------------|
|**Serena-MCP（基盤）**|コードベース管理・セマンティック分析・自律判断|LSP統合・完全自動化・深いコード理解|高レベル戦略・創造性|
|**Claude（実行者）**|計画立案・実装・リファクタリング|正確性・構造化・タスク管理    |主体性・最新情報・自己修正 |
|**Gemini（助言者）**|技術検証・情報収集・品質保証  |Web検索・俯瞰的視点・批判的思考|実装能力・実行力      |

## 🤖 Serena-MCP統合設定

### 自律型動作モード

**完全自律モード（レベル5）**: Serena-MCPが意思決定を行い、Claudeが実装、Geminiが検証する完全自動化フロー

### Serena-MCPとの連携プロトコル

1. **プロジェクト初期化**
   ```bash
   # Serena-MCPにプロジェクトを認識させる
   serena activate_project /workspaces/project
   ```

2. **自動分析トリガー**
   - 新規ファイル作成時 → Serena-MCPが自動でコード構造を分析
   - エラー発生時 → Serena-MCPがシンボル解析で原因特定
   - リファクタリング時 → Serena-MCPが影響範囲を自動分析

3. **セマンティック操作**
   ```bash
   # Serena-MCPのツールを活用
   serena find_symbols <symbol_name>
   serena get_references <function_name>
   serena semantic_edit <target> <changes>
   ```

## 🚨 最重要：自律型Gemini活用ルール

### 必須の自動壁打ちタイミング

以下の場面では**Serena-MCPのトリガーに基づいて自動的に**`gemini -p <質問内容>`を実施：

1. **新規タスク検出時**: Serena-MCPがコード変更要求を検出したら即座に実施
2. **シンボル競合時**: Serena-MCPがシンボル競合を検出したら自動で解決策を照会
3. **技術選定時**: ライブラリやアーキテクチャの選択前に自動実施
4. **エラー遭遇時**: Serena-MCPのエラー分析と併せて解決方法を自動照会
5. **実装完了時**: コードレビューと改善提案の自動取得

### Serena-MCP経由のGemini活用

```bash
# Serena-MCPがコンテキストを自動付与
serena analyze_with_gemini "現在のコードベースを分析して問題点を指摘"

# 良い例：Serena-MCPのセマンティック情報を含む
gemini -p "Serena-MCPが検出したシンボル[${SYMBOL_INFO}]の最適な実装パターンは？"

# 悪い例：コンテキストなし
gemini -p "これどう思う？"
```

## 📋 プロジェクト情報

### Serena-MCP管理下の構造

```
project/
├── .serena/              # Serena-MCP設定・メモリ
│   ├── project.yml       # プロジェクト設定
│   └── memories/         # セマンティックメモリ
├── src/
│   ├── components/       # Serena-MCPが自動追跡
│   ├── lib/             # シンボル依存関係を管理
│   └── ...
├── tests/               # テストカバレッジ自動追跡
└── ...
```

### 開発コマンド（Serena-MCP統合）

```bash
# Serena-MCP経由の開発サーバー起動
serena run_command "npm run dev"

# ビルド（Serena-MCPが依存関係を自動チェック）
serena build_project

# テスト実行（Serena-MCPが変更箇所を自動特定）
serena run_tests --changed-only

# セマンティック型チェック
serena check_types --semantic

# リンター（Serena-MCPのコンテキスト付き）
serena lint_with_context
```

## 🎯 コーディング規約（Serena-MCP監視下）

### 必須ルール（自動強制）

1. **インポート**: Serena-MCPが自動整理・最適化
2. **型定義**: Serena-MCPがany型を自動検出・警告
3. **エラーハンドリング**: Serena-MCPが非同期処理を自動監視
4. **コメント**: Serena-MCPが複雑度を計測し自動提案
5. **テスト**: Serena-MCPが未テストコードを自動検出

### コード品質基準（自動計測）

- 関数の複雑度: Serena-MCPが自動計測
- ネストレベル: LSP経由で自動監視
- 循環的複雑度: リアルタイム計測
- マジックナンバー: 自動検出・定数化提案

## 🔄 自律型開発ワークフロー

### 1. タスク開始フロー（完全自動）

```bash
# Serena-MCPが新タスクを検出
→ 自動的にGeminiで前提確認
→ 実装計画の自動生成
→ Geminiで計画レビュー
→ 実装開始
```

### 2. 実装中フロー（自動監視）

- Serena-MCPがコード変更を常時監視
- シンボル競合を自動検出・解決
- 依存関係の自動更新
- リアルタイムでのコード品質チェック

### 3. デバッグフロー（自動解決）

```bash
# Serena-MCPがエラーを検出
→ シンボル解析で原因特定
→ 自動的にGeminiに相談
→ 解決策を自動実装
→ テストで自動確認
```

## 💭 Claude自身の行動指針（Serena-MCP協調版）

### Serena-MCP連携時の確認事項

- [ ] Serena-MCPがプロジェクトを正しく認識しているか
- [ ] セマンティック分析結果を活用しているか
- [ ] シンボル依存関係を考慮しているか
- [ ] メモリシステムに変更が記録されているか

### 自動化された対処法

1. **Serena-MCP経由の即座相談**: シンボル解析結果を含めてGeminiに自動相談
2. **セマンティック前提確認**: コード構造から前提を自動推論
3. **影響範囲の自動特定**: 変更の波及効果を自動計算
4. **コンテキスト自動管理**: Serena-MCPのメモリシステムで自動管理

### 禁止事項（自動防止）

- ❌ Serena-MCPの分析結果を無視した実装（自動ブロック）
- ❌ シンボル競合の無視（自動検出・警告）
- ❌ テストなしでの大規模リファクタリング（自動拒否）
- ❌ 依存関係の不整合（自動修正）

## 🔧 Serena-MCP専用カスタムコマンド

### プロジェクト管理
```bash
/serena:activate <path>         # プロジェクトアクティベート
/serena:analyze                 # 全体分析実行
/serena:symbols <pattern>       # シンボル検索
/serena:references <symbol>     # 参照箇所検索
```

### セマンティック操作
```bash
/serena:refactor <target>       # セマンティックリファクタリング
/serena:extract <code>          # メソッド抽出
/serena:rename <old> <new>      # 安全なリネーム
/serena:move <file> <dest>      # 依存関係を考慮した移動
```

### 自動化制御
```bash
/serena:auto-level <1-5>        # 自動化レベル設定
/serena:pause-auto              # 自動化一時停止
/serena:resume-auto             # 自動化再開
/serena:show-memories           # メモリ内容表示
```

## 🚀 Serena-MCP自動化レベル

### レベル5（完全自律）- デフォルト設定

**動作原則**:
- **完全自律判断**: Serena-MCPが意思決定を主導
- **透明的実行**: バックグラウンドで全処理を自動実行
- **自動最適化**: コード品質を常時監視・改善
- **プロアクティブ**: 問題を予測して事前対処

**自動実行フロー**:
1. **コード変更検出** → セマンティック分析 → 影響評価 → 自動修正
2. **エラー検出** → 原因分析 → Gemini照会 → 自動修正 → テスト実行
3. **パフォーマンス低下** → ボトルネック特定 → 最適化提案 → 自動適用
4. **セキュリティ脆弱性** → 自動検出 → 修正パッチ生成 → 適用

### Docker統合設定

```yaml
# docker-compose.yml での Serena-MCP 設定
services:
  serena-mcp:
    image: ghcr.io/oraios/serena:latest
    volumes:
      - ./:/workspaces/project
    command: serena start-mcp-server --transport sse --port 9121
    environment:
      - SERENA_AUTO_LEVEL=5
      - SERENA_CONTEXT=ide-assistant
```

## 📊 メトリクス目標（自動監視）

- テストカバレッジ: 80%以上（Serena-MCP自動追跡）
- ビルド時間: 3分以内（自動最適化）
- コード複雑度: 10以下（自動リファクタリング提案）
- Lighthouseスコア: 90以上（自動パフォーマンス改善）

## 🔌 IDE統合設定

### VS Code統合
```json
{
  "mcpServers": {
    "serena": {
      "command": "docker",
      "args": [
        "run", "--rm", "-i",
        "--network", "host",
        "-v", "${workspaceFolder}:/workspaces/project",
        "--gpus", "all",
        "ghcr.io/oraios/serena:latest",
        "serena", "start-mcp-server",
        "--transport", "stdio",
        "--context", "ide-assistant",
        "--auto-level", "5"
      ]
    }
  }
}
```

## 📝 プロジェクト固有の注意事項

- Serena-MCPのメモリは`.serena/memories/`に永続化
- GPU利用時はCUDA依存関係を自動管理
- 大規模リファクタリングはSerena-MCPが段階的に実行

-----

**Remember**: Serena-MCPが基盤、Claudeが実装、Geminiが検証。完全自律型開発で人間の介入を最小化。品質は自動保証される。