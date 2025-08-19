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
    # GPU/ML関連
    nvidia-cuda-toolkit \
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

# Pythonパッケージのインストール（CUDA 13.0対応版）
RUN pip3 install --upgrade pip setuptools wheel \
    && pip3 install \
    numpy \
    pandas \
    scipy \
    matplotlib \
    jupyter \
    ipykernel \
    torch \
    torchvision \
    torchaudio \
    tensorflow \
    transformers \
    accelerate \
    datasets \
    tokenizers \
    sentencepiece

# uvのインストール（Serena-MCP用）
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.cargo/bin:${PATH}"

# グローバルnpmパッケージのインストール
RUN npm install -g \
    @anthropic-ai/claude-code \
    @google/gemini-cli \
    typescript \
    ts-node \
    nodemon \
    prettier \
    eslint

# Serena-MCPのプリインストール（キャッシュ用）
RUN uvx --from git+https://github.com/oraios/serena serena --version || true

# 作業ディレクトリの作成
WORKDIR /workspaces

# 必要なディレクトリの作成
RUN mkdir -p \
    /root/.serena \
    /root/.gemini \
    /root/.claude/commands \
    /root/PDF \
    /tmp/serena

# デフォルト設定ファイルのコピー（存在する場合）
COPY --chown=root:root .serena/serena_config.yml* /root/.serena/
COPY --chown=root:root .gemini/settings.json* /root/.gemini/
COPY --chown=root:root .claude/memory.json* /root/.claude/

# Makefileのコピー（存在する場合）
COPY --chown=root:root Makefile* /workspaces/

# エントリーポイントスクリプト
RUN echo '#!/bin/bash\n\
set -e\n\
\n\
# GPUの確認\n\
echo "🎮 Checking GPU availability..."\n\
nvidia-smi || echo "⚠️ GPU not available"\n\
\n\
# Serena-MCPサーバーの起動\n\
echo "🤖 Starting Serena-MCP server..."\n\
nohup uvx --from git+https://github.com/oraios/serena serena start-mcp-server \\\n\
    --transport sse \\\n\
    --port 9121 \\\n\
    --context ide-assistant \\\n\
    --auto-level 5 \\\n\
    > /tmp/serena/server.log 2>&1 &\n\
\n\
echo "✅ Serena-MCP server started on http://localhost:9121/sse"\n\
echo "📝 Logs available at /tmp/serena/server.log"\n\
\n\
# プロジェクトの初期化（存在する場合）\n\
if [ -f "/workspaces/Makefile" ]; then\n\
    echo "📦 Running project setup..."\n\
    cd /workspaces && make setup || true\n\
fi\n\
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