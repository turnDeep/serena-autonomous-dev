# 🤖 Serena-MCP自律型三位一体開発環境

**Serena-MCP × Claude Code × Gemini CLI = 完全自律型開発**

人間の介入を最小化し、AIエージェントが自律的に協調して開発を進める次世代フレームワークです。

## 🚀 特徴

- **完全自律型**: Serena-MCPが意思決定、Claudeが実装、Geminiが検証を自動実行
- **GPU対応**: CUDA/cuDNNを活用した高速計算とML開発をサポート
- **セマンティック理解**: LSPベースの深いコード理解と自動リファクタリング
- **継続的最適化**: パフォーマンスとコード品質を常時監視・改善

## 📋 必要要件

### ハードウェア
- **GPU**: NVIDIA GPU（Compute Capability 3.0以上）推奨
- **メモリ**: 16GB以上推奨
- **ストレージ**: 50GB以上の空き容量

### ソフトウェア
- **OS**: Linux（Ubuntu 20.04/22.04/24.04推奨）、Windows（WSL2）、macOS
- **Docker**: 20.10以上
- **NVIDIA Driver**: 580以上（GPU使用時、CUDA 13.0対応）
- **NVIDIA Container Toolkit**（GPU使用時）

## 🔧 セットアップ

### 1. リポジトリのクローン

```bash
git clone https://github.com/your-org/serena-autonomous-dev.git
cd serena-autonomous-dev
```

### 2. GPU環境の準備（GPU使用時のみ）

#### Linux
```bash
# NVIDIA Container Toolkitのインストール
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker

# GPU動作確認（CUDA 13.0）
docker run --rm --gpus all nvidia/cuda:13.0.0-base-ubuntu22.04 nvidia-smi
```

#### Windows（WSL2）
Docker DesktopがNVIDIA Container Toolkitを自動的に含んでいるため、追加設定は不要です。

### 3. 環境変数の設定

```bash
# .envファイルを作成
cat > .env << EOF
# API Keys
ANTHROPIC_API_KEY=your_anthropic_key_here
GEMINI_API_KEY=your_gemini_key_here

# Jupyter Token（オプション）
JUPYTER_TOKEN=your_secure_token_here

# Serena設定
SERENA_AUTO_LEVEL=5
EOF
```

### 4. 開発環境の起動

#### 方法A: VS Code Dev Container（推奨）

1. VS Codeでプロジェクトを開く
2. 拡張機能「Dev Containers」をインストール
3. コマンドパレット（Ctrl+Shift+P）→「Dev Containers: Reopen in Container」
4. 初回ビルドを待つ（5-10分程度）

#### 方法B: Docker Compose

```bash
# 環境の起動
docker-compose up -d

# コンテナに接続
docker-compose exec dev-environment bash

# ログの確認
docker-compose logs -f serena-mcp
```

#### 方法C: 直接Dockerコマンド

```bash
# イメージのビルド
docker build -f .devcontainer/Dockerfile -t serena-dev:latest .

# コンテナの起動
docker run -it --rm \
  --gpus all \
  --network host \
  -v $(pwd):/workspaces \
  -v ~/.ssh:/root/.ssh:ro \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -e GEMINI_API_KEY=$GEMINI_API_KEY \
  serena-dev:latest
```

### 5. Serena-MCPの初期化

コンテナ内で以下を実行：

```bash
# プロジェクトのアクティベート
serena activate_project /workspaces

# 動作確認
serena list_tools
serena find_symbols "function"
```

## 🎮 使い方

### 基本的な開発フロー

1. **要件を伝える**
   ```bash
   claude
   > ユーザー認証機能を実装して
   ```

2. **自動実行される処理**
   - Serena-MCPがコードベースを分析
   - Claudeが実装計画を策定
   - Geminiが技術調査を実施
   - 実装とテストが自動実行

3. **結果の確認**
   ```bash
   # Serenaの分析結果
   cat .serena/memories/latest_analysis.json
   
   # 実装されたコード
   git diff
   
   # テスト結果
   npm test
   ```

### Serena-MCPコマンド

```bash
# プロジェクト管理
serena activate_project <path>     # プロジェクト有効化
serena list_projects               # プロジェクト一覧
serena switch_modes <mode>         # モード切替

# コード分析
serena find_symbols <pattern>      # シンボル検索
serena get_references <symbol>     # 参照検索
serena analyze_dependencies        # 依存関係分析

# セマンティック編集
serena semantic_edit <target>      # セマンティック編集
serena refactor <pattern>          # リファクタリング
serena extract_method <code>       # メソッド抽出
```

### 自動化レベルの調整

```bash
# レベル1: 手動確認必須
export SERENA_AUTO_LEVEL=1

# レベル3: 重要な変更のみ確認
export SERENA_AUTO_LEVEL=3

# レベル5: 完全自動（デフォルト）
export SERENA_AUTO_LEVEL=5
```

## 📊 モニタリング

### Serena-MCPダッシュボード

ブラウザで `http://localhost:24282/dashboard` にアクセス

### ログの確認

```bash
# Serena-MCPサーバーログ
tail -f /tmp/serena/server.log

# Claude実行ログ
claude logs

# Gemini実行ログ
gemini logs
```

### GPUモニタリング

```bash
# GPU使用状況
nvidia-smi -l 1

# 詳細なGPU情報
nvidia-smi -q
```

## 🔧 トラブルシューティング

### GPUが認識されない

```bash
# コンテナ内でGPU確認
nvidia-smi

# Docker設定確認
docker run --rm --gpus all nvidia/cuda:12.0-base nvidia-smi
```

### Serena-MCPが起動しない

```bash
# 手動起動
uvx --from git+https://github.com/oraios/serena serena start-mcp-server \
  --transport sse \
  --port 9121 \
  --context ide-assistant

# ログ確認
cat /tmp/serena/server.log
```

### メモリ不足

```bash
# Docker設定変更
docker update --memory="16g" --memory-swap="32g" serena-dev
```

## 📚 ドキュメント

- [CLAUDE.md](CLAUDE.md) - Claude Code設定
- [GEMINI.md](GEMINI.md) - Gemini CLI設定
- [Serena公式ドキュメント](https://github.com/oraios/serena)

## 🤝 貢献

プルリクエストや課題報告を歓迎します！

## 📜 ライセンス

MIT License

---

**自律型開発の新時代へようこそ！** 🚀