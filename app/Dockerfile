# -------------------
# Development Builder Stage
# -------------------
FROM node:18-alpine AS dev-builder

WORKDIR /app

COPY package-*.json ./

# Install all dependencies for development
RUN npm ci && npm cache clean --force

COPY . .


# -------------------
# Development Stage
# -------------------
FROM node:18-alpine AS development

RUN apk add --no-cache dumb-init

WORKDIR /app

RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

COPY --from=dev-builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs . .

USER nodejs

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (res) => { process.exit(res.statusCode === 200 ? 0 : 1) })"

ENTRYPOINT ["dumb-init", "--"]

CMD ["node", "index.js"]


# -------------------
# Production Builder Stage
# -------------------
FROM node:18-alpine AS prod-builder

WORKDIR /app

COPY package-*.json ./

RUN npm ci  && npm cache clean --force

COPY . .


# -------------------
# Production Stage
# -------------------
FROM node:18-alpine AS production

RUN apk add --no-cache dumb-init

WORKDIR /app

RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

COPY --from=prod-builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs . .

USER nodejs

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (res) => { process.exit(res.statusCode === 200 ? 0 : 1) })"

ENTRYPOINT ["dumb-init", "--"]

CMD ["node", "index.js"]