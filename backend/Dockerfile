# ====== BUILD STAGE ======
FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /app

# Copy Maven config first (better caching)
COPY pom.xml .
RUN mvn -q -DskipTests dependency:go-offline

# Copy source and build
COPY src ./src
RUN mvn -q -DskipTests package

# ====== RUNTIME STAGE ======
FROM eclipse-temurin:17-jre-jammy

WORKDIR /app

# Copy the built jar
COPY --from=build /app/target/*.jar app.jar

# Expose backend port (change if your app uses a different one)
EXPOSE 8080

# DB config – override in docker run / compose if needed
ENV SPRING_DATASOURCE_URL=jdbc:mariadb://db:3306/student_db \
    SPRING_DATASOURCE_USERNAME=username \
    SPRING_DATASOURCE_PASSWORD=your_password

ENTRYPOINT ["java", "-jar", "app.jar"]
