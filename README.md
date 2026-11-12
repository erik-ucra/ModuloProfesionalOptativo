# Gestor de Incidencias

## Respuestas a las preguntas de la Entrega Final

**1. ¿Por qué el frontend no necesita conectarse a PostgreSQL?**
Porque el backend actúa como intermediario exponiendo una API. El frontend (interfaz web) se comunica con el backend para solicitar o enviar datos, y es este último el encargado de ejecutar la lógica de negocio y conectarse de forma segura a la base de datos. Esta separación mejora tanto la seguridad como la mantenibilidad de la aplicación.

**2. ¿Por qué el backend pertenece a dos redes?**
Porque necesita actuar como puente entre el frontend y los datos. Pertenece a la red `frontend` para poder recibir y responder a las peticiones HTTP que llegan desde Nginx, y también a la red `datos` para poder consultar, insertar y modificar la información en la base de datos PostgreSQL.

**3. ¿Qué servicio necesita publicar un puerto en el anfitrión?**
El servicio `frontend` (que contiene Nginx). Es el único que necesita publicar un puerto (por ejemplo, el 8080) hacia el anfitrión para que los usuarios puedan acceder a la interfaz web de la aplicación a través del navegador web mediante `localhost:8080`.

**4. ¿Por qué publicar PostgreSQL hacia fuera no es la solución adecuada para un fallo de comunicación interno?**
Porque al publicar los puertos de una base de datos hacia el exterior (al host anfitrión o internet) se expone información sensible y se crea una grave brecha de seguridad, dejando la base de datos vulnerable a ataques externos. Los problemas internos de comunicación deben resolverse configurando correctamente las redes de Docker, asegurando que el tráfico se mantenga en el entorno aislado de contenedores.

**5. ¿Qué ventaja ofrece Docker Compose respecto a ejecutar los tres contenedores manualmente?**
Docker Compose permite gestionar toda la aplicación de manera centralizada y declarativa. En lugar de escribir de memoria comandos largos (`docker run`, crear redes, crear volúmenes, linkear variables, etc.), todo se describe en un único archivo YAML (`compose.yaml`). Esto facilita levantar la infraestructura (`docker compose up`), detenerla (`docker compose down`), compartir la configuración y asegurar que el entorno sea idéntico cada vez que se despliegue.

**6. ¿Por qué hemos dividido la arquitectura en dos redes?**
Para aplicar el principio de mínimo privilegio y aislamiento de seguridad. Al separar en la red `frontend` (para la comunicación frontend-backend) y la red `datos` (para la comunicación backend-PostgreSQL), nos aseguramos de que el servicio del frontend nunca tenga visibilidad ni acceso directo por red a la base de datos. Si la interfaz web es comprometida por un atacante, la base de datos se mantiene más segura en su propia subred aislada.

**7. ¿Por qué el backend utiliza `db` como dirección de PostgreSQL?**
Porque Docker incorpora un sistema interno de resolución DNS (Domain Name System) para contenedores que pertenecen a la misma red de usuario. De esta forma, el backend puede localizar dinámicamente al contenedor de PostgreSQL utilizando el nombre lógico del servicio (`db`), sin necesidad de conocer qué IP estática tiene asignada en ese momento.

**8. ¿Qué diferencia existe entre detener un proyecto y eliminar sus volúmenes?**
Detener un proyecto (por ejemplo, con `docker compose stop` o `docker compose down`) finaliza la ejecución de los contenedores y puede eliminar las redes, pero preserva de forma segura los volúmenes, manteniendo todos los datos intactos. En cambio, si decides eliminar los volúmenes (usando `docker compose down -v`), destruirás por completo el almacenamiento persistente y perderás todos los registros e información almacenada.

**9. ¿Qué consecuencias tendría para la empresa perder el volumen de PostgreSQL?**
Resultaría en la pérdida total y permanente de todas las incidencias registradas en la aplicación. La empresa perdería su historial operativo y datos en producción, lo que podría ocasionar un paro en la capacidad de soporte, problemas en la resolución de fallos reales, y un impacto económico y reputacional severo.
