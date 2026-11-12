# Stage 1: Dependency resolution
FROM node:22-alpine AS deps

WORKDIR /app

COPY package*.json ./
# Cache npm downloads across builds and avoid caching npm artifacts inside the layer
RUN --mount=type=cache,target=/root/.npm \
    npm ci --omit=dev --no-audit --prefer-offline

# Stage 2: Production runtime
FROM node:22-alpine AS runner

WORKDIR /app

ENV NODE_ENV=production

# Use the pre-existing unprivileged "node" user (uid:gid 1000:1000)
USER node

# Copy dependencies and application source with appropriate ownership
COPY --from=deps --chown=node:node /app/node_modules ./node_modules
COPY --chown=node:node package*.json ./
COPY --chown=node:node src ./src

EXPOSE 5001

CMD ["node", "src/index.js"]
