FROM gradle:jdk17 as build

WORKDIR /app

COPY gradlew .
COPY gradle gradle
COPY build.gradle* settings.gradle* ./

# 🔧 Fix Windows CRLF → LF + permission
RUN sed -i 's/\r$//' gradlew && chmod +x gradlew
RUN ./gradlew dependencies --no-daemon || true

COPY src src

RUN ./gradlew clean build --no-daemon

FROM alpine:3.19 as back

WORKDIR /app

# create a group "appgroup" and a user "appuser"
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

RUN apk add openjdk21-jre-headless

COPY --from=build --chown=appuser:appgroup /app/build/libs/*.jar .

USER appuser

EXPOSE 8080

CMD ["java", "-jar", "/app/microcrm-0.0.1-SNAPSHOT.jar"]