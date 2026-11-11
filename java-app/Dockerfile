# syntax=docker/dockerfile:1
FROM maven:3-eclipse-temurin-21 AS build
WORKDIR /build
COPY pom.xml .
RUN mvn -B -ntp dependency:go-offline
COPY src ./src
RUN mvn -B -ntp package -DskipTests

FROM eclipse-temurin:21-jre AS final
WORKDIR /app
RUN groupadd --system app && useradd --system --gid app app
COPY --from=build /build/target/*.jar app.jar
EXPOSE 8080
USER app
ENTRYPOINT ["java", "-jar", "app.jar"]
