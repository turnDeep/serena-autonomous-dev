{
  "name": "Serena-MCP Autonomous Dev Container with GPU",
  "version": "2.0.0",
  
  // GPU対応のCUDA 13.0ベースイメージを使用（最新安定版）
  "image": "nvidia/cuda:13.0.0-cudnn-devel-ubuntu22.04",
  
  // GPU有効化とネットワーク設定
  "runArgs": [
    "--gpus=all",
    "--network=host",
    "-v", "/var/run/docker.sock:/var/run/docker.sock",
    "--privileged=true",
    "--cap-add=SYS_ADMIN",
    "--security-opt", "seccomp=unconfined"
  ],
  
  // GPU環境変数の設定
  "remoteEnv": {
    "PATH": "${containerEnv:PATH}:/usr/local/cuda/bin",
    "LD_LIBRARY_PATH": "$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64",
    "XLA_FLAGS": "--xla_gpu_cuda_data_dir=/usr/local/cuda",
    "NVIDIA_VISIBLE_DEVICES": "all",
    "NVIDIA_DRIVER_CAPABILITIES": "compute,utility",
    "CUDA_VISIBLE_DEVICES": "all",
    
    // Serena-MCP設定
    "SERENA_AUTO_LEVEL": "5",
    "SERENA_CONTEXT": "ide-assistant",
    "SERENA_PROJECT_PATH": "/workspaces/${localWorkspaceFolderBasename}",
    
    // APIキー（ローカル環境から継承）
    "ANTHROPIC_API_KEY": "${localEnv:ANTHROPIC_API_KEY}",
    "GEMINI_API_KEY": "${localEnv:GEMINI_API_KEY}"
  },
  
  // 初期化コマンド（順次実行）
  "onCreateCommand": {
    "install-base": "apt-get update && apt-get install -y curl git build-essential python3 python3-pip nodejs npm",
    "install-uv": "curl -LsSf https://astral.sh/uv/install.sh | sh",
    "install-cli-tools": "npm install -g @anthropic-ai/claude-code @google/gemini-cli"
  },
  
  // プロジェクトセットアップ
  "postCreateCommand": {
    "verify-gpu": "nvidia-smi",
    "setup-project": "make setup || echo 'No Makefile found'",
    "init-serena": "uvx --from git+https://github.com/oraios/serena serena activate_project ${containerWorkspaceFolder}",
    "create-directories": "mkdir -p ~/.serena ~/.gemini ~/.claude/commands ~/PDF",
    "set-permissions": "chmod -R 755 ~/.serena ~/.gemini ~/.claude ~/PDF"
  },
  
  // 起動時コマンド
  "postStartCommand": {
    "start-serena": "nohup uvx --from git+https://github.com/oraios/serena serena start-mcp-server --transport sse --port 9121 --context ide-assistant --auto-level 5 > /tmp/serena.log 2>&1 &",
    "show-status": "echo '🤖 Serena-MCP Server running on http://localhost:9121/sse' && echo '🎮 GPU: ' && nvidia-smi --query-gpu=name --format=csv,noheader"
  },
  
  // VS Code設定
  "customizations": {
    "vscode": {
      "settings": {
        "terminal.integrated.defaultProfile.linux": "bash",
        "python.defaultInterpreterPath": "/usr/bin/python3",
        
        // MCP Server設定
        "mcp.servers": {
          "serena": {
            "command": "uvx",
            "args": [
              "--from",
              "git+https://github.com/oraios/serena",
              "serena",
              "start-mcp-server",
              "--transport", "stdio",
              "--context", "ide-assistant",
              "--project", "${workspaceFolder}",
              "--auto-level", "5"
            ]
          }
        },
        
        // Claude Code設定
        "claude.mcpServers": {
          "serena": {
            "enabled": true,
            "autoStart": true
          }
        }
      },
      
      "extensions": [
        // 開発基本ツール
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "ms-python.python",
        "ms-python.vscode-pylance",
        
        // AI/ML関連
        "ms-toolsai.jupyter",
        "ms-toolsai.vscode-jupyter-cell-tags",
        "nvidia.nsight-vscode-edition",
        
        // Git/GitHub
        "GitHub.copilot",
        "GitHub.copilot-chat",
        "eamodio.gitlens",
        
        // コンテナ関連
        "ms-azuretools.vscode-docker",
        "ms-vscode-remote.remote-containers"
      ]
    }
  },
  
  // ユーザー設定
  "remoteUser": "root",
  
  // 追加機能
  "features": {
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {},
    "ghcr.io/devcontainers/features/docker-in-docker:2": {
      "moby": true,
      "installDockerBuildx": true
    },
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.11"
    },
    "ghcr.io/devcontainers/features/node:1": {
      "version": "20"
    }
  },
  
  // ポートフォワーディング
  "forwardPorts": [
    9121,  // Serena-MCP SSE Server
    3000,  // 開発サーバー（Next.js等）
    8080,  // APIサーバー
    5173,  // Vite
    8888   // Jupyter
  ],
  
  // ホスト要件
  "hostRequirements": {
    "gpu": "optional",
    "cpus": 4,
    "memory": "8gb",
    "storage": "32gb"
  },
  
  // マウント設定
  "mounts": [
    // PDFライブラリをマウント
    "source=${localEnv:HOME}/PDF,target=/root/PDF,type=bind,consistency=cached",
    // Serena設定を永続化
    "source=${localEnv:HOME}/.serena,target=/root/.serena,type=bind,consistency=cached",
    // SSH設定
    "source=${localEnv:HOME}/.ssh,target=/root/.ssh,type=bind,readonly"
  ],
  
  // 待機設定
  "waitFor": "postStartCommand",
  
  // 更新時の動作
  "updateContentCommand": "echo 'Checking for updates...' && npm update -g @anthropic-ai/claude-code @google/gemini-cli",
  
  // シャットダウン時の処理
  "shutdownAction": "stopContainer"
}