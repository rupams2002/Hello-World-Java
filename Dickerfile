# Stage 1: build the jar with Maven
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
# Cache dependencies separately from source for faster rebuilds
RUN mvn -B dependency:go-offline
COPY src ./src
RUN mvn -B clean package -DskipTests

# Stage 2: run with a minimal JRE
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
# Adjust the jar name/path if your pom.xml artifact name differs
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
# OpenShift runs containers as an arbitrary non-root UID — this image
# doesn't need root, so no USER change needed, but ensure /app is readable
RUN chmod -R g+rwX /app
ENTRYPOINT ["java", "-jar", "app.jar"]