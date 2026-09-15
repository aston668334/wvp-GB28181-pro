FROM ubuntu:24.04 AS builder

RUN apt-get update && \
    apt-get install -y nodejs npm  && \
    rm -rf /var/lib/apt/lists/*

COPY ./web /build
WORKDIR /build

RUN npm --registry=https://registry.npmmirror.com install
RUN npm run build:prod

FROM nginx:alpine

ARG TZ=Asia/Shanghai

COPY ./docker/nginx/templates /etc/nginx/templates
COPY --from=builder /src/main/resources/static /opt/dist

CMD ["nginx","-g","daemon off;"]
