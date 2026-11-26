FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY LostFoundServer.java .
COPY . .
RUN javac LostFoundServer.java
CMD ["java", "LostFoundServer"]
