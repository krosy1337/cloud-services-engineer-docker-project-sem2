FROM golang:1.17-alpine

WORKDIR /backend

COPY go.mod go.sum ./
RUN go mod download
COPY . ./

RUN go build -o ./backend ./cmd/api

EXPOSE 8081

CMD ["./backend"] 