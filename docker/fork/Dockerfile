FROM eclipse-temurin:25-jdk AS build
RUN apt-get update && apt-get install -y --no-install-recommends git ca-certificates && rm -rf /var/lib/apt/lists/*
WORKDIR /src
ARG FORK_REVISION
RUN git init . && git fetch https://github.com/Matinho/manga-manhwa-reader.git "$FORK_REVISION" && git checkout --detach FETCH_HEAD && test "$(git rev-parse HEAD)" = "$FORK_REVISION"
RUN --mount=type=cache,target=/root/.gradle cp .github/runner-files/ci-gradle.properties /root/.gradle/gradle.properties && bash gradlew :server:shadowJar && cp server/build/*.jar /tmp/server.jar

FROM ghcr.io/suwayomi/suwayomi-server@sha256:7e25953e143c890417d1e69426281c6e8e470cca208b1cbdca684e8236c2bb13
ARG FORK_REVISION
COPY --from=build --chown=1000:1000 /tmp/server.jar /home/suwayomi/startup/tachidesk_latest.jar
LABEL org.opencontainers.image.source="https://github.com/Matinho/manga-manhwa-reader" org.opencontainers.image.revision="$FORK_REVISION"
