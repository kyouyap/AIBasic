ARG UV_VERSION=latest

FROM ghcr.io/astral-sh/uv:$UV_VERSION AS uv


FROM mcr.microsoft.com/vscode/devcontainers/python:3.13

# コンテナ内に.venvを作らないようにする環境変数
ENV UV_PROJECT_ENVIRONMENT='/usr/local/'
ENV UV_SYSTEM_PYTHON=1

# site-packagesディレクトリの権限を設定
RUN chmod -R 777 /usr/local/

WORKDIR /opt/

# バージョンを指定してuvをコピー
COPY --from=uv /uv /bin/uv

COPY pyproject.toml uv.lock /opt/

RUN uv sync
