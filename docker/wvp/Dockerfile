# ringcentral/jdk:21.0.9 requires the x86-64-v2 instruction set.  Keep the
# Java 21 toolchain on the more broadly compatible Jammy base so this stack
# also runs on older x86_64 hosts.
FROM eclipse-temurin:21-jdk-jammy AS builder

EXPOSE 18978/tcp
EXPOSE 8116/tcp
EXPOSE 8116/udp
EXPOSE 8080/tcp

RUN java -version && javac -version

RUN apt-get update && \
    apt-get install -y maven && \
    rm -rf /var/lib/apt/lists/*

COPY ./docker/wvp/settings.xml /root/.m2/settings.xml
COPY . /build
WORKDIR /build
RUN if ! mvn clean package -Dmaven.test.skip=true -Dmaven.gitcommitid.skip=true > /tmp/maven-build.log 2>&1; then \
      cat /tmp/maven-build.log; \
      exit 1; \
    fi
WORKDIR /build/target
RUN mv wvp-pro-*.jar wvp.jar

FROM eclipse-temurin:21-jdk-jammy
RUN mkdir -p /opt/wvp /opt/ylcx/wvp
WORKDIR /opt/wvp
COPY --from=builder /build/target /opt/wvp
COPY ./docker/wvp/wvp /opt/wvp
COPY ./docker/wvp/wvp /opt/ylcx/wvp
ENTRYPOINT ["java", "-Djava.net.preferIPv4Stack=true", "-Xms512m", "-Xmx1024m", "-XX:+HeapDumpOnOutOfMemoryError", "-XX:HeapDumpPath=/opt/ylcx/", "-jar", "wvp.jar", "--spring.config.location=/opt/wvp/application.yml"]
