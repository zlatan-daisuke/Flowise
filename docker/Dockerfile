# ==========
# Stage 1: build
# ==========
FROM node:20-alpine AS build
WORKDIR /app

# 依存（Flowiseの一部機能でビルドに必要になりやすい）
RUN apk add --no-cache git python3 make g++ build-base

# pnpm を有効化（Node 20 なら corepack が使えます）
RUN corepack enable

# まず lockfile だけコピーして依存キャッシュを効かせる
COPY pnpm-lock.yaml package.json pnpm-workspace.yaml ./
COPY packages/ui/package.json packages/ui/package.json
COPY packages/server/package.json packages/server/package.json

# monorepo install
RUN pnpm install --frozen-lockfile

# ソース全体をコピー
COPY . .

# UI + server をビルド（あなたがローカルでやった pnpm build と同じ）
RUN pnpm build


# ==========
# Stage 2: runtime
# ==========
FROM node:20-alpine AS runtime
WORKDIR /app

# ランタイム依存（あなたの元Dockerfileの流儀に寄せて残す）
RUN apk add --no-cache chromium git python3 py3-pip make g++ build-base cairo-dev pango-dev curl
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser
ENV PUPPETEER_SKIP_DOWNLOAD=true

RUN corepack enable

# 実行に必要なものだけコピー
COPY --from=build /app/package.json /app/pnpm-lock.yaml /app/pnpm-workspace.yaml ./
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/packages ./packages

# server を起動（3000）
EXPOSE 3000
WORKDIR /app/packages/server
CMD ["pnpm", "start"]
