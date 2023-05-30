FROM java:8
VOLUME /tmp
ADD ./target/capsule-demo-app.jar app.jar
EXPOSE 8080
ENTRYPOINT [ "sh", "-c", "java -jar /app.jar"]