# Stage 1: Build frontend
FROM node:18-alpine AS frontend-builder

WORKDIR /app/frontend

COPY frontend/package*.json ./
RUN npm ci
COPY frontend/ ./
RUN npm run build

# Stage 2: Install backend dependencies
FROM node:18-alpine AS backend-builder

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

# Copy backend source code
COPY . .

# Stage 3: Production image
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy backend code and deps
COPY --from=backend-builder /app ./

# Copy built frontend files
COPY --from=frontend-builder /app/frontend/dist ./frontend/dist

# Set env
ENV NODE_ENV=production

# Expose the port your backend runs on
EXPOSE 5001

# Run as non-root
USER appuser

# Start the server
CMD ["npm", "start"]
