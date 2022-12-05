# Recomendaciones / Buenas Practicas / Tips

## Contents of this file

 - Dockerifle
 -
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
