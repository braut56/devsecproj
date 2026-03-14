# ==========================================================
# Multi-stage Dockerfile for Node.js backend
# ==========================================================

# ---- Build stage ----
FROM node:20.8.1-alpine AS build
WORKDIR /app

# Copy package files
COPY package.json package-lock.json* ./

# Install production dependencies only
RUN npm ci --only=production && npm cache clean --force

# ---- Production stage ----
FROM node:20.8.1-alpine AS production

# Security: run as non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Install dumb-init with pinned version
RUN apk --no-cache add dumb-init=1.2.5-r2

WORKDIR /app

# Copy production dependencies and app code
COPY --from=build /app/node_modules ./node_modules
COPY src/ ./src/

# Security: own files by non-root user
RUN chown -R appuser:appgroup /app

# Use non-root user
USER appuser

# Expose app port
EXPOSE 5000

# Use dumb-init for proper signal handling (PID 1)
ENTRYPOINT ["dumb-init", "--"]

# Start the app
CMD ["node", "src/index.js"]
