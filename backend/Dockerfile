FROM golang:1.17-alpine AS build

WORKDIR /backend

COPY go.mod go.sum ./
RUN go mod download
COPY . ./

RUN go build -ldflags="-s -w" -o /backend/backend ./cmd/api

FROM alpine:3.24.1

COPY --from=build /backend/backend ./backend

EXPOSE 8081

CMD ["./backend"] 
