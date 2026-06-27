---
description: Prueba funcional local de la app Spring Boot.
---

# skill-functional-tests

Skill para ejecutar y validar funcionalmente aplicaciones Spring Boot sin construir un JAR. 

El agente debe preguntar antes de ejecutar comandos `curl` y `kill`, estos deben ser siempre relacionados al proyecto y prueba actual.
** Ser específico en qué acción realizará el comando a implementar **

## Cuándo usar este skill

- El usuario pide hacer una prueba funcional, de humo o de integración sobre este proyecto.
- Se quiere verificar que un endpoint responde correctamente tras un cambio.
- Se necesita levantar la app para interactuar con ella manualmente.

## Metodología

1. **Verificar requisitos** — confirmar que `java` y `mvn` están disponibles y con las versiones correctas. Si la feature usa una herramienta de infraestructura local (MySQL, RabbitMQ, etc.), confirmar con el dev que ya levantó el compose de la raíz (`docker compose up -d`) y, cuando haya persistencia, que ejecutó el script DDL de `db/` en su MySQL local antes de la prueba. Si la feature consume una API externa, confirmar con el dev que el mock/stub está disponible y que su host (valor) está en `functional-test-env.local`; el placeholder correspondiente va en `application.properties`. El agente puede revisar el estado de los contenedores (`docker ps`, `docker logs`) para diagnosticar, pero no levanta el compose ni el mock (eso es tarea del dev).
2. **Levantar en segundo plano** — leer las variables de `functional-test-env.local` y pasarlas como variables de entorno al ejecutar `mvn spring-boot:run` (la app usa `application.properties` con placeholders sin default, así que sin esas variables no arranca); redirigir la salida a un log. El comando va con las variables inline (`VAR=… mvn spring-boot:run`), así que pedirá confirmación de permiso al dev; es esperado.
3. **Esperar que inicie** — poll sobre el log buscando `Started .*Application`, esperar hasta ~60 s.
4. **Ejecutar el curl** — si la feature tocó el flujo HTTP, construir la petición desde la colección de Postman en `postman/` (endpoint, método, body), tomando host/puerto/credenciales mock de `functional-test-env.local`. Si no existe colección, validar al menos que la app arrancó (startup/health).
5. **Verificar la respuesta** — comparar contra el valor esperado.
6. **Reportar resultado** — resumir status HTTP, validación de body y cualquier error relevante.
7. **Detener el proceso** — `kill $PID` al terminar. (Considerar -Dspring-boot.run.fork=false)

## Variables de entorno
** Está prohibido revisar archivos .env **
- Las variables de entorno son para pruebas locales y algunas apuntan a mocks.
- `functional-test-env.local` es la **fuente de verdad** de la config local (host, puerto, credenciales mock). `application.properties` usa placeholders sin valores por defecto; el agente lee `functional-test-env.local` y pasa esas variables como entorno al levantar la app para la prueba. El dev las configura manualmente en su IDE (IntelliJ).
- El endpoint y la petición a probar provienen de la colección de Postman, no de este archivo.

## Detalles

- **Señal de inicio**: línea `Started .*Application` en el log de Maven.
- **Log de la app**: `/tmp/spring-boot-app.log` — revisar ahí en caso de error.
- **Sin JAR**: no se ejecuta `mvn package`; el skill usa siempre `spring-boot:run`.
