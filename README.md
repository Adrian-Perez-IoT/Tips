# Recomendaciones / Buenas Practicas / Tips

## Contents of this file

 - Dockerifle
 
## Dockerfile

Diferencia entre ADD y COPY: ADD copia Sources Remotos (ejemplo URL) y ademas descomprime automaticamente (ejemplo app.jar que es un archivo comprimido). Es recomendable usar COPY en lugar de ADD.

Lo siguiente: 

FROM debian
RUN apt-get update $$ apt-get -y install  --no-install-recommends openjdk-8-jdk && rm -rf /var/lig/apt/list/*

reemplazarlo por:

FROM openjdk:8-jre-alpine
COPY target/app.jar /app
CMD ["java", "-jar", "/app/app.jar"]

Tratar de usar imagenes alphine en lo posible y si es compatible con mi app (investigar en algunos casos no sirve para todo).

Reemplazar el anterior Dockerfile (sabor) por:

FROM maven:3.6-jdk-8-alpine
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn -e -B package
CMD ["java", "-jar", "/app/app.jar"]

El problema aqui es que estamos usando un .jar (archivo comprimido por java para correr nuestra app). Y nosotros no sabemos con que version de java esta esta construido ese .jar y debemos asegurarnos siempre que todos los pasos sean reproducible (construir el .jar en mi maquina local debe ser igual que construirla en el Servidor PROD). Por lo tanto debemos usar JAVA.

Luego reemplazarlo por esto, para asegurarnos que las dependencias esten correctas (despues de copiar el .xml y antes de copiar el codigo fuente, para  evitar que invalide el cache del Dockerfile: 

FROM maven:3.6-jdk-8-alpine
WORKDIR /app
COPY pom.xml .
RUN mvn -e -B dependency:resolver
COPY src ./src
RUN mvn -e -B package
CMD ["java", "-jar", "/app/app.jar"]

### Aplicar MultiStage Build

Con el motivo de que la imagen tiene cosas que no necesitamos (binario de maven). Creamos diferentes stage y copiar archivos de una etapa a la otra. 

FROM maven:3.6-jdk-8-alpine AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn -e -B dependency:resolver
COPY src ./src
RUN mvn -e -B package

FROM openjdk:8-jre-alpine
COPY --from=builder /app/target/app.jar
CMD ["java", "-jar", "/app/app.jar"]
