FROM node:alpine AS builder
WORKDIR /app
RUN yarn global add turbo yarn
COPY . .
RUN turbo prune --scope=web --docker

FROM node:alpine as installer
RUN apk update
WORKDIR /app

COPY --from=builder /app/out/json/ .
COPY --from=builder /app/out/yarn.lock ./yarn.lock
RUN apk update && apk add python3 make g++ && npm install -g npm
RUN yarn install

COPY --from=builder /app/out/full/ .
COPY turbo.json turbo.json

FROM node:alpine AS runner
WORKDIR /app

COPY --from=installer /app/apps/web/next.config.js .
COPY --from=installer /app/apps/web/package.json .

COPY --from=installer --chown=nextjs:nodejs /app/apps/web/.next/static ./apps/web/.next/static

CMD ["turbo", "dev"]
