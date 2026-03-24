# ── Build stage ──────────────────────────────────────────────────────────────
FROM eclipse-temurin:25-jdk-noble AS build

WORKDIR /build

COPY code/.tool-versions .
COPY code/pom.xml .
# Download dependencies first (layer cached as long as pom.xml doesn't change)
RUN --mount=type=cache,target=/root/.m2 \
    apt-get update -qq && apt-get install -y -qq maven \
    && mvn --batch-mode --no-transfer-progress dependency:go-offline -q

COPY code/src ./src
RUN --mount=type=cache,target=/root/.m2 \
    mvn --batch-mode --no-transfer-progress package -DskipTests

# ── Runtime stage ─────────────────────────────────────────────────────────────
FROM eclipse-temurin:25-jre-noble AS runtime

# Run as non-root
RUN groupadd --system app && useradd --system --gid app app
USER app

WORKDIR /app

COPY --from=build /build/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
