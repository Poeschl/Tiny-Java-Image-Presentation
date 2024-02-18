# Use jdk 18 to analyze dependencies bacause jdeps from JDK 17 has a bug
FROM  docker.io/eclipse-temurin:18-jdk-alpine as detect-deps

COPY Hands-On-Kittens-1.0-SNAPSHOT.jar /app/Hands-On-Kittens.jar

RUN mkdir /app/unpacked && \
    cd /app/unpacked && \
    unzip ../Hands-On-Kittens.jar && \
    cd .. && \
    $JAVA_HOME/bin/jdeps \
    --ignore-missing-deps \
    --print-module-deps \
    -q \
    --recursive \
    --multi-release 17 \
    --class-path="./unpacked/BOOT-INF/lib/*" \
    --module-path="./unpacked/BOOT-INF/lib/*" \
    ./Hands-On-Kittens.jar > /deps.info && \
    cat /deps.info

FROM  docker.io/eclipse-temurin:17-jdk-alpine as slim-jre

RUN apk add --no-cache binutils
COPY --from=detect-deps /deps.info /deps.info
RUN $JAVA_HOME/bin/jlink \
    --verbose \
    --add-modules $(cat /deps.info) \
    --strip-debug \
    --no-man-pages \
    --no-header-files \
    --compress=2 \
    --output /slim-jre

FROM docker.io/alpine:3.19 as application
ENV JAVA_HOME=/jre
ENV PATH="${JAVA_HOME}/bin:${PATH}"

COPY --from=slim-jre /slim-jre $JAVA_HOME
WORKDIR /app

ENTRYPOINT ["java", "-jar", "/app/Hands-On-Kittens.jar"]
EXPOSE 8080

COPY Hands-On-Kittens-1.0-SNAPSHOT.jar /app/Hands-On-Kittens.jar
