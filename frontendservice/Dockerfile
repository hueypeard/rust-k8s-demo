FROM rust:1.69.0-slim-buster AS chef 
# We only pay the installation cost once, 
# it will be cached from the second build onwards
RUN cargo install cargo-chef
# https://github.com/hyperium/tonic/issues/965
RUN apt update && apt install -y --no-install-recommends protobuf-compiler

# required for tonic-build
RUN rustup component add rustfmt
WORKDIR /app

FROM chef AS planner
COPY . .
RUN cargo chef prepare  --recipe-path recipe.json

FROM chef AS builder
COPY --from=planner /app/recipe.json recipe.json
# Build dependencies - this is the caching Docker layer!
RUN cargo chef cook --recipe-path recipe.json
# Build application
COPY . .
RUN cargo build

# We do not need the Rust toolchain to run the binary!
FROM debian:buster-slim AS runtime
WORKDIR /app
EXPOSE 8080
ENV RUST_LOG frontend=info
ENV FORTUNE_SERVICE_HOSTNAME fortuneservice

COPY --from=builder /app/target/debug/frontend-server /usr/local/bin/frontend-server
ENTRYPOINT ["/usr/local/bin/frontend-server"]
