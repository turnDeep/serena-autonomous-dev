# GPU対応の自律型開発環境
# CUDA 13.0 + cuDNN + Ubuntu 22.04 LTS (最新安定版)
FROM nvidia/cuda:13.0.0-cudnn-devel-ubuntu22.04

# 環境変数設定
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
ENV PYTHONUNBUFFERED=1
ENV NODE_VERSION=20
ENV CUDA_HOME=/usr/local/cuda
ENV PATH=${CUDA_HOME}/bin:${PATH}
ENV LD_LIBRARY_PATH=${CUDA_HOME}/lib64:${LD_LIBRARY_PATH}

# CUDAの設定
ENV NVIDIA_VISIBLE_DEVICES=all
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility
ENV XLA_FLAGS=--xla_gpu_cuda_data_dir=/usr/local/cuda

# Serena-MCP設定
ENV SERENA_AUTO_LEVEL=5
ENV SERENA_CONTEXT=ide-assistant

# 基本パッケージのインストール
RUN apt-get update && apt-get install -y \
    # 基本ツール
    curl \
    wget \
    git \
    vim \
    nano \
    htop \
    tmux \
    # ビルドツール
    build-essential \
    cmake \
    pkg-config \
    # Python関連
    python3 \
    python3-pip \
    python3-dev \
    python3-venv \
    # システムライブラリ
    libssl-dev \
    libffi-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    libncurses5-dev \
    libncursesw5-dev \
    libgdbm-dev \
    libnss3-dev \
    libxml2-dev \
    libxmlsec1-dev \
    # GPU/ML関連（nvidia-cuda-toolkitは削除 - ベースイメージに含まれている）
    libnccl2 \
    libnccl-dev \
    # その他
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release \
    && rm -rf /var/lib/apt/lists/*

# Node.jsのインストール（NodeSource経由）
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@latest

# npmのグローバルディレクトリの権限を修正
RUN mkdir -p /usr/lib/node_modules \
    && chmod -R 755 /usr/lib/node_modules

# Pythonパッケージのインストール（基本パッケージのみ）
RUN pip3 install --upgrade pip setuptools wheel \
    && pip3 install \
    numpy \
    pandas \
    scipy \
    matplotlib \
    jupyter \
    ipykernel

# uvのインストール（Serena-MCP用）
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:${PATH}"

# 作業ディレクトリの作成
WORKDIR /workspaces

# 必要なディレクトリの作成
RUN mkdir -p \
    /root/.serena \
    /root/.gemini \
    /root/.claude/commands \
    /root/PDF \
    /tmp/serena

# グローバルnpmパッケージのインストール（1つずつ確実にインストール）
RUN echo "Installing Claude Code..." && \
    npm install -g @anthropic-ai/claude-code && \
    echo "Claude Code installed successfully" && \
    which claude || echo "Warning: claude command not found in PATH"

RUN echo "Installing Gemini CLI..." && \
    npm install -g @google/gemini-cli && \
    echo "Gemini CLI installed successfully" && \
    which gemini || echo "Warning: gemini command not found in PATH"

# 追加の開発ツール
RUN npm install -g \
    typescript \
    ts-node \
    nodemon \
    prettier \
    eslint

# パスの確認とシンボリックリンクの作成（必要に応じて）
RUN echo "Checking installed commands..." && \
    ls -la /usr/lib/node_modules/@anthropic-ai/claude-code/bin/ 2>/dev/null || true && \
    ls -la /usr/lib/node_modules/@google/gemini-cli/bin/ 2>/dev/null || true && \
    echo "PATH: $PATH"

# npmのグローバルbinディレクトリをPATHに追加
ENV PATH="/usr/bin:${PATH}"

# Serena-MCPのキャッシュ用プレロード（エラーを無視）
RUN echo "Pre-loading Serena-MCP..." && \
    (timeout 10 uvx --from git+https://github.com/oraios/serena serena --version || true) && \
    echo "Serena-MCP pre-load complete"

# デフォルト設定ファイルのコピー（条件付きコピーのため、RUNコマンドを使用）
# 存在するファイルのみコピー
RUN if [ -f .serena/serena_config.yml ]; then \
        cp .serena/serena_config.yml /root/.serena/; \
    fi && \
    if [ -f .serena/serena_config.yaml ]; then \
        cp .serena/serena_config.yaml /root/.serena/; \
    fi

RUN if [ -f .gemini/settings.json ]; then \
        cp .gemini/settings.json /root/.gemini/; \
    fi

RUN if [ -f .claude/memory.json ]; then \
        cp .claude/memory.json /root/.claude/; \
    fi

# コマンドファイルのコピー（ディレクトリが存在する場合のみ）
# 注：ビルドを成功させるには、プロジェクトルートに .claude/commands/ ディレクトリが存在する必要があります（空でも構いません）。
COPY ../.claude/commands/ /tmp/claude-commands/
RUN if [ -d /tmp/claude-commands ]; then \
        cp -r /tmp/claude-commands/* /root/.claude/commands/ 2>/dev/null || true; \
        rm -rf /tmp/claude-commands; \
    fi

# Makefileのコピー（存在する場合）
# 注：ビルドを成功させるには、プロジェクトルートに Makefile が存在する必要があります。
COPY ../Makefile /workspaces/

# エントリーポイントスクリプト
RUN echo '#!/bin/bash\n\
set -e\n\
\n\
echo "=== Development Environment Starting ==="\n\
\n\
# GPUの確認\n\
echo "🎮 Checking GPU availability..."\n\
nvidia-smi --query-gpu=name,memory.total --format=csv,noheader || echo "⚠️ GPU not available"\n\
\n\
# コマンドの確認\n\
echo ""\n\
echo "📦 Checking installed tools..."\n\
echo -n "  Claude Code: "\n\
if command -v claude &> /dev/null; then\n\
    claude --version 2>/dev/null || echo "installed (version check failed)"\n\
else\n\
    echo "❌ NOT FOUND - Run: npm install -g @anthropic-ai/claude-code"\n\
fi\n\
\n\
echo -n "  Gemini CLI: "\n\
if command -v gemini &> /dev/null; then\n\
    gemini --version 2>/dev/null || echo "installed (version check failed)"\n\
else\n\
    echo "❌ NOT FOUND - Run: npm install -g @google/gemini-cli"\n\
fi\n\
\n\
echo -n "  Serena-MCP: "\n\
if command -v uvx &> /dev/null; then\n\
    echo "✅ Available via uvx"\n\
else\n\
    echo "❌ uvx not found"\n\
fi\n\
\n\
# Serena-MCPサーバーの起動（バックグラウンド）\n\
echo ""\n\
echo "🤖 Starting Serena-MCP server..."\n\
mkdir -p /tmp/serena\n\
nohup uvx --from git+https://github.com/oraios/serena serena start-mcp-server \\\n\
    --transport sse \\\n\
    --port 9121 \\\n\
    --context ide-assistant \\\n\
    --auto-level 5 \\\n\
    > /tmp/serena/server.log 2>&1 &\n\
\n\
echo "✅ Serena-MCP server starting on http://localhost:9121/sse"\n\
echo "📝 Logs available at /tmp/serena/server.log"\n\
\n\
# プロジェクトの初期化（存在する場合）\n\
if [ -f "/workspaces/Makefile" ]; then\n\
    echo ""\n\
    echo "📦 Running project setup..."\n\
    cd /workspaces && make setup || true\n\
fi\n\
\n\
# 不足しているツールのインストール案内\n\
echo ""\n\
echo "=== Setup Instructions ==="\n\
if ! command -v claude &> /dev/null; then\n\
    echo ""\n\
    echo "⚠️ Claude Code is not installed. To install:"\n\
    echo "   npm install -g @anthropic-ai/claude-code"\n\
fi\n\
\n\
if ! command -v gemini &> /dev/null; then\n\
    echo ""\n\
    echo "⚠️ Gemini CLI is not installed. To install:"\n\
    echo "   npm install -g @google/gemini-cli"\n\
fi\n\
\n\
echo ""\n\
echo "📚 After installing, authenticate with:"\n\
echo "   claude  # For Claude Code"\n\
echo "   gemini  # For Gemini CLI"\n\
echo ""\n\
echo "=== Environment Ready ==="\n\
echo ""\n\
\n\
# シェルの起動\n\
exec "$@"\n\
' > /entrypoint.sh && chmod +x /entrypoint.sh

# ヘルスチェック
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:9121/health || exit 1

# ポート公開
EXPOSE 9121 3000 8080 5173 8888

# エントリーポイント
ENTRYPOINT ["/entrypoint.sh"]
CMD ["/bin/bash"]
