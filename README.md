# Recomendaciones / Buenas Practicas / Tips

## Contents of this file

 - Dockerifle
 -
## Dockerfile

Diferencia entre ADD y COPY: ADD copia Sources Remotos (ejemplo URL) y ademas descomprime automaticamente (ejemplo app.jar que es un archivo comprimido). Es recomendable usar COPY en lugar de ADD.
