FROM alpine:latest as jsbuilder
RUN  apk update && apk add git make
# Later add libfuntools, cfitsio, and node
RUN  mkdir -p /src && \
      cd /src && \
      git clone https://github.com/ericmandel/js9 && \
      cd js9 && \
      ./configure --with-webdir=/www/js9 && \
      make && \
      make install
FROM golang:alpine as gobuilder
ENV  CGO_ENABLED=0
ENV  GOOS=linux
RUN  mkdir -p /src/tinyserver
COPY main.go /src/tinyserver/main.go
RUN  cd /src/tinyserver && \
      go build -ldflags '-w'
FROM scratch
COPY --from=gobuilder /src/tinyserver/tinyserver /tinyserver
COPY --from=jsbuilder /www/js9 /www/js9/js9
COPY --from=jsbuilder /www/js9/index.html /www/js9/index.html
COPY casa.fits.gz /www/js9/js9/fits/casa.fits.gz
COPY passwd /etc/passwd
COPY group /etc/group
USER 65534
LABEL      description="JS9 Web Server" \
             name="lsstsqre/js9server" \
             version="0.0.1"
CMD ["/tinyserver"]
