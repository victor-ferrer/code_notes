Basic Dockerfile that takes some Go code (`/go/src/myapp/` in the example) and buils a Docker container.

```
FROM golang:alpine AS build
RUN mkdir /app
WORKDIR /app

# Copies module files first
COPY ./go/src/myapp/go.mod . 
COPY ./go/src/myapp/go.sum .

# Get dependancies - will also be cached if we won't change mod/sum
RUN go mod download

COPY ./go/src/myapp/. .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -installsuffix cgo -o /go/bin/hello

FROM scratch
COPY --from=build /go/bin/hello /go/bin/hello
ENTRYPOINT ["/go/bin/hello"]
```

Run this to build the image:

```
docker build -t [[image_name]] .
````
