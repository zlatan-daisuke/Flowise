# ==========
# Stage 1: build
# ==========
FROM node:20-alpine AS build
WORKDIR /app

RUN apk add --no-cache git python3 make g++ build-base
RUN corepack enable

# ★ 先に全部コピーする
COPY . .

# 依存インストール（monorepo全体）
RUN pnpm install --frozen-lockfile

# ビルド
RUN pnpm build


# ==========
# Stage 2: runtime
# ==========
FROM node:20-alpine AS runtime
WORKDIR /app

RUN apk add --no-cache chromium git python3 py3-pip make g++ build-base cairo-dev pango-dev curl
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser
ENV PUPPETEER_SKIP_DOWNLOAD=true

RUN corepack enable

COPY --from=build /app /app

EXPOSE 3000
WORKDIR /app/packages/server
CMD ["pnpm", "start"]