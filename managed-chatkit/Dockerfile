# Build stage for frontend
FROM node:20-alpine AS frontend-builder
WORKDIR /app/frontend
COPY frontend/package*.json ./
RUN npm ci
COPY frontend/ ./
ENV VITE_CHATKIT_WORKFLOW_ID=wf_6925f45748908190b9b4fd12df4b8935049d835cf5b1e7da
RUN npm run build

# Production stage
FROM python:3.11-slim
WORKDIR /app

# Install Node.js for serving static files (or use Python to serve)
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Copy backend
COPY backend/ ./backend/
WORKDIR /app/backend

# Install Python dependencies
RUN pip install --no-cache-dir -e .

# Copy built frontend to be served by FastAPI
COPY --from=frontend-builder /app/frontend/dist /app/frontend/dist

WORKDIR /app

# Expose port
EXPOSE 8000

# Environment variables (will be overridden by Dokploy)
ENV PYTHONPATH=/app/backend
ENV ENVIRONMENT=production

# Start command - FastAPI serves both API and static files
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
