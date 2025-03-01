# Local development setup

#### Setup local postgres database

```
$ docker run -d -e POSTGRES_PASSWORD=1234 -p 5432:5432 --mount type=bind,source=$(pwd)/databaseservice/data/data.sql,target=/docker-entrypoint-initdb.d/01-data.sql postgres:12
```

#### Using docker

```shell
$ docker build -t frontend frontendservice
$ docker build -t quotation quotationservice

# for linux: use localhost instead of docker.for.mac.localhost
$ docker run -it -p 8080:8080 -e QUOTATION_SERVICE_HOSTNAME=docker.for.mac.localhost frontend
$ docker run -it -p 9001:9001 -e POSTGRES_SERVICE=docker.for.mac.localhost -e POSTGRES_PASSWORD=1234 quotation

# and goto http://localhost:8080
```

#### Local (no docker)

If you would like to run everything locally, you need the
[rust toolchain](https://rustup.rs/), of course.

```shell
$ cargo build
$ RUST_LOG=frontend_server=debug,tower_http=trace QUOTATION_SERVICE_HOSTNAME=localhost cargo run --bin frontend-server
$ RUST_LOG=quotation_server=debug,tower_http=trace POSTGRES_SERVICE=localhost POSTGRES_PASSWORD=1234 cargo run --bin quotation-server

# and goto http://localhost:8080
```
