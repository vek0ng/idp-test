FROM golang:1.23.3-alpine AS build
RUN mkdir /src
WORKDIR /src
ADD ./go.mod .
ADD ./go.sum .
ADD ./*.go ./
RUN GOOS=linux GOARCH=amd64 go build -o sample-go-app
RUN chmod +x sample-go-app

FROM scratch
ARG VERSION
ENV VERSION=$VERSION
ENV DB_PORT=5432 DB_USERNAME=postgres DB_NAME=sample-go-app
COPY --from=build /src/sample-go-app /usr/local/bin/sample-go-app
EXPOSE 8080
CMD ["sample-go-app"]
