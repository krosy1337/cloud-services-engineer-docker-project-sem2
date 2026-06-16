FROM golang:1.17-alpine AS build

WORKDIR /backend

COPY go.mod go.sum ./
RUN go mod download
COPY . ./

RUN go build -ldflags="-s -w" -o /backend/backend ./cmd/api

FROM alpine:3.24.1

COPY --from=build /backend/backend ./backend
RUN apk add --no-cache curl
EXPOSE 8081

CMD ["./backend"] 
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 CMD curl -fsS http://127.0.0.1:8081/health || exit 1