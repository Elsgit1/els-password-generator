# syntax=docker/dockerfile:1

# ---- Build stage: compile the Vite/React app to static assets ----
FROM node:22-alpine AS build
WORKDIR /app

# pnpm is the package manager for this repo (pnpm-lock.yaml)
RUN npm install -g pnpm

# Install dependencies first for better layer caching
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile

# Build the production bundle into /app/dist
COPY . .
RUN pnpm build

# ---- Runtime stage: serve static assets with a rootless nginx ----
FROM nginxinc/nginx-unprivileged:alpine

# Only the compiled static output is shipped in the final image
COPY --from=build /app/dist /usr/share/nginx/html

# Rootless nginx listens on 8080 (no privileged port, runs as uid 101)
EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
