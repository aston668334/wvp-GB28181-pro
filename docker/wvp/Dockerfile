FROM ringcentral/jdk:21.0.9 AS builder

EXPOSE 18978/tcp
EXPOSE 8116/tcp
EXPOSE 8116/udp
EXPOSE 8080/tcp

RUN java -version && javac -version

RUN apt-get update && \
    apt-get install -y maven && \
    rm -rf /var/lib/apt/lists/*

COPY . /build
WORKDIR /build
RUN ls && mvn clean package -Dmaven.test.skip=true -Dmaven.gitcommitid.skip=true
WORKDIR /build/target
RUN mv wvp-pro-*.jar wvp.jar

FROM ringcentral/jdk:21.0.9
RUN mkdir -p /opt/wvp /opt/ylcx/wvp
WORKDIR /opt/wvp
COPY --from=builder /build/target /opt/wvp
COPY ./docker/wvp/wvp /opt/wvp
COPY ./docker/wvp/wvp /opt/ylcx/wvp
ENTRYPOINT ["java", "-Djava.net.preferIPv4Stack=true", "-Xms512m", "-Xmx1024m", "-XX:+HeapDumpOnOutOfMemoryError", "-XX:HeapDumpPath=/opt/ylcx/", "-jar", "wvp.jar", "--spring.config.location=/opt/wvp/application.yml"]
