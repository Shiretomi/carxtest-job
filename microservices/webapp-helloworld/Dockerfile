FROM node:20-alpine AS builder
WORKDIR /app
COPY src/package.json src/package-lock.json ./
RUN npm ci --omit=dev

FROM node:20-alpine AS runtime
WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules
COPY src/ ./

EXPOSE 3000
ENTRYPOINT ["node", "app.js"]
