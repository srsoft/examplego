FROM golang:stretch AS builder

WORKDIR $GOPATH/src/golang-gorm

COPY go.mod go.sum ./
RUN cd $GOPATH/src/golang-gorm && go mod download

COPY ./ $GOPATH/src/golang-gorm/cmd

WORKDIR $GOPATH/src/golang-gorm/cmd
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /ceres-go .

FROM alpine:3.12.3

COPY --from=builder /ceres-go /usr/local/bin/ceres-go
COPY deploy/vm/config.toml /configs/ceres-go.toml

ENTRYPOINT ["/usr/local/bin/ceres-go"]
CMD ["-c", "/configs/ceres-go.toml"]