# create first stage to build the application
FROM eclipse-temurin:17-jdk-alpine as builder
WORKDIR /workspace/app

COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
COPY src src

RUN ./mvnw install -DskipTests
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)

# create second stage to run the application from the building stage
# a full jdk image is not needed, a small jre image is ok
FROM eclipse-temurin:17-jre-alpine 
# use unpriviledged user
RUN addgroup -S demo && adduser -S demo -G demo
USER demo
# creates a specific space to persist some data in your container
VOLUME /tmp
#expose port
EXPOSE 8080
ARG DEPENDENCY=/workspace/app/target/dependency
# unpack the jar file
COPY --from=builder ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=builder ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=builder ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","demoApplication.Application"]
