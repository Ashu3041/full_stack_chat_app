# Stage 1: Build static assets
FROM node:22-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN --mount=type=cache,target=/root/.npm \
    npm ci --no-audit --prefer-offline

COPY . .
ENV NODE_ENV=production
RUN npm run build

# Stage 2: Serve via lightweight stable Nginx
FROM nginx:stable-alpine-slim

# Clean out default static pages
RUN rm -rf /usr/share/nginx/html/*

# Copy built frontend bundle (adjust to /app/build if using CRA)
COPY --from=builder /app/dist /usr/share/nginx/html

# Copy custom Nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

STOPSIGNAL SIGQUIT

CMD ["nginx", "-g", "daemon off;"]
