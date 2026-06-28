---
description: Hand-off de pruebas funcionales al dev (el agente no las ejecuta).
---

# skill-functional-tests

Skill para **preparar y entregar al dev** las pruebas funcionales de un cambio. En un proyecto existente, levantar la app con sus acoples (BD, colas, servicios, config real) es fragil, asi que **el agente no ejecuta pruebas funcionales**: las deja listas y documentadas para que el dev las corra.

## Cuando usar este skill

- Tras implementar un cambio que afecta el flujo HTTP o el comportamiento observable.
- Para entregarle al dev que probar y que esperar.

## Que hace el agente (sin ejecutar)

1. Identifica el flujo funcional afectado por el cambio (endpoints nuevos/modificados, comportamiento observable).
2. Reune las **colecciones de Postman / payloads** del cambio (ya creadas/actualizadas al implementar) donde el proyecto las tenga (o en `postman/` si no hay convencion); si falta algun request del cambio, completalo.
3. Para cada coleccion/request/payload nuevo o modificado, documenta el **resultado esperado**: status HTTP, body o validacion clave, y efecto esperado en datos (p. ej. "se crea/actualiza la fila X").
4. Si la prueba depende de infra o de un mock (BD, cola, API externa), indicalo como **precondicion** para el dev (que debe estar levantado/configurado); el agente no lo levanta.

## Que NO hace el agente

- No levanta la app, no ejecuta `curl` ni `kill`, no corre la app para probar endpoints.
- No ejecuta el `docker-compose` ni los mocks (eso es del dev).
- (Los tests unitarios y la regresion de la suite si los corre el agente; eso es nivel build, ver `agents/unit-test-writer.md`.)

## Entregable (hand-off al dev)

Un reporte claro con:
- Colecciones/payloads nuevos o actualizados (rutas).
- Por cada uno: que probar, el request, y el **resultado esperado**.
- Precondiciones (infra/mocks/config) que el dev debe tener listas.
- Para endpoints de escritura: como verificar la persistencia (p. ej. la consulta esperada), para que la haga el dev.
