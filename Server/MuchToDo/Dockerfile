# ==========================
# Stage 1 - Build
# ==========================
FROM golang:1.25-alpine AS builder

WORKDIR /app

# Install certificates
RUN apk add --no-cache ca-certificates

# Copy dependency files first for better caching
COPY go.mod go.sum ./

RUN go mod download

# Copy application source
COPY . .

# Build binary
RUN CGO_ENABLED=0 GOOS=linux go build \
    -a \
    -installsuffix cgo \
    -o muchtodo \
    ./cmd/api

# ==========================
# Stage 2 - Runtime
# ==========================
FROM alpine:3.22

RUN apk add --no-cache \
    ca-certificates \
    wget

# Create non-root user
RUN addgroup -S appgroup && \
    adduser -S appuser -G appgroup

WORKDIR /app

# Copy binary
COPY --from=builder /app/muchtodo .

# Copy swagger docs
COPY --from=builder /app/docs ./docs

# Set ownership
RUN chown -R appuser:appgroup /app

USER appuser

EXPOSE 8080

HEALTHCHECK --interval=30s \
            --timeout=5s \
            --start-period=20s \
            --retries=3 \
    CMD wget --quiet --tries=1 --spider http://localhost:8080/health || exit 1

ENTRYPOINT ["./muchtodo"]
