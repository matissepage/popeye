FROM maven:3.5-jdk-8-alpine AS builder

WORKDIR /app

COPY . .

RUN mvn dependency:resolve && mvn package

FROM openjdk:8-jre-alpine

COPY --from=builder /app/target/worker-jar-with-dependencies.jar .

EXPOSE 80

CMD ["java", "-jar", "worker-jar-with-dependencies.jar"]