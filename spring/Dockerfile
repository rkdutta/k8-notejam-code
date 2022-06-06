FROM openjdk:8-jdk-alpine
RUN apk add --update-cache \
    curl \
    maven \
    git

ARG JAR_FILE=target/*.jar
ARG APP_USER=spring
ARG WORK_DIR="/app"
ARG APP_LOCATION="/app.jar"

WORKDIR ${WORK_DIR}
COPY ${JAR_FILE} ${WORK_DIR}/app.jar

RUN chown -R root:root ${WORK_DIR} && \
chmod o+w ${WORK_DIR} && \
chmod a-w,a+x *.* && \
chmod g-rwx,o-rwx /proc && \
addgroup -S ${APP_USER} && adduser -S ${APP_USER} -G ${APP_USER} && \
rm -rf /bin/*

EXPOSE 8080
USER ${APP_USER}:${APP_USER}
ENTRYPOINT ["java", "-jar", "/app/app.jar"]

#scanning of image
# sudo docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image app:latest
