# Builder
FROM node:20-alpine AS builder
WORKDIR /app

# Build-time public env for Vite/Astro (import.meta.env.PUBLIC_API_BASE_URL)
ARG PUBLIC_API_BASE_URL=/
ENV PUBLIC_API_BASE_URL=${PUBLIC_API_BASE_URL}

# Copy package manifests first for caching
COPY package*.json ./
# If you use pnpm or yarn, adapt commands accordingly
RUN npm ci --silent

# Copy source and build
COPY . .
RUN npm run build

# Production image: nginx serving the static build (dist/)
FROM nginx:stable-alpine AS production
# Remove default nginx content
RUN rm -rf /usr/share/nginx/html/*

# Copy built site from builder
COPY --from=builder /app/dist/ /usr/share/nginx/html/

# Optional: copy a custom nginx config if needed
# COPY ./nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]