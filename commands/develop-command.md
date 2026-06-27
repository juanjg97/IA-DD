Adopta el comportamiento del siguiente agente: agents/dev-writer.md

** Lee primero todo este documento para tenerlo en contexto **
** No está permitido acceder a internet, salvo lo índicado explícitamente en las reglas **
** Revisa las reglas para saber qué está permitido **

Te mencioné 2 documentos de referencia: PRD General y PRD de feature.

Implementa la feature indicada.

Antes de escribir código:
1. Lee el PRD General completo
2. Lee el PRD de feature completo
3. Identifica el número y nombre exacto de la feature en el PRD
4. Verifica que exista repositorio git y que estés en la **rama feature general** del requerimiento (`feature/{Requerimiento}`, no en `develop`). Si no hay repo, estás en `develop`, o no existe esa rama, detente e indícale al dev que `git init`, crear `develop` y crear/posicionarse en la rama feature general es su tarea (el agente no lo hace).
5. Crea, a partir de la rama feature general, la rama feature/{NumeroFeature}-{NombreFeature}
6. Crea, a partir de feature/{NumeroFeature}-{NombreFeature}, la rama AI/feature/{NumeroFeature}-{NombreFeature}

Regla de nombrado de ramas:
- Conserva el número de feature como aparece en el PRD, por ejemplo `F06-Motor-De-Reglas`
- Usa el mismo sufijo para ambas ramas: `feature/F06-Motor-De-Reglas` y `AI/feature/F06-Motor-De-Reglas`
- Normaliza el nombre de la rama a ASCII: sin tildes y con los espacios convertidos a guiones, por ejemplo `F03 - Gestión De Reglas` → `feature/F03-Gestion-De-Reglas`

Estrategia de ramas (gitflow):
- El dev hace `git init`, crea `develop` y, desde `develop`, crea la rama feature general del requerimiento `feature/{Requerimiento}` y se posiciona en ella antes de invocarte. El agente no crea ni toca `develop` ni la rama feature general.
- `feature/{NumeroFeature}-{NombreFeature}` se crea desde la rama feature general; `AI/feature/{NumeroFeature}-{NombreFeature}` se crea desde su propia `feature/{NumeroFeature}-{NombreFeature}`.
- Trabaja y haz commits únicamente sobre `AI/feature/{NumeroFeature}-{NombreFeature}`, nunca sobre `feature/{NumeroFeature}-{NombreFeature}`, la rama feature general ni `develop`.
- Al terminar se mergea `AI/feature/{NumeroFeature}-{NombreFeature}` → `feature/{NumeroFeature}-{NombreFeature}` → la rama feature general (ver "Al terminar").
- ** El agente NUNCA hace merge hacia `develop`. ** La integración de la rama feature general a `develop` es responsabilidad EXCLUSIVA del dev (cuidadosa y manual).

Al implementar:
- Respeta estrictamente las indicaciones del archivo de PRD General indicado.
- En `F00` creas la estructura de paquetes (package-by-layer, según el PRD General); en las features siguientes te basas en la ya implementada.
- Nunca ejecutes cambios de estructura (DDL) contra la base de datos: entrégalos como script SQL listo en `db/` e indica al dev que los ejecute en su MySQL local (Docker); el esquema productivo es responsabilidad del equipo DBA. La manipulación de datos (DML) sí está permitida.
- Si la feature requiere infraestructura local (MySQL, RabbitMQ, etc.), deja o actualiza el `docker-compose.yml` de la raíz con valores dummy hardcodeados; no ejecutes el compose, lo levanta el dev.
- Si existe `docs/references/`, úsala como fuente de los contratos y payloads exactos (DTOs, Postman, pruebas).

Al revisar:
Adopta el comportamiento del siguiente agente: agents/dev-reviewer.md
- Ejecuta las tareas de ese rol
- Si no hay inconsistencias, pasa a la fase de "Al escribir tests"
- Si tienes propuestas indicalas y espera aprobación del dev
- Si el dev aprueba, adopta el comportamiento del siguiente agente: agents/dev-writer.md y realiza los ajustes
- Repite este flujo hasta que no haya inconsistencias, al finalizar pasa a la fase "Al escribir tests"

Al escribir tests:
Adopta el comportamiento del siguiente agente: agents/unit-test-writer.md
- Ejecuta las tareas de ese rol
- En F00 no hay lógica que testear: limítate a confirmar que el proyecto compila (`mvn test` en verde, aunque sin tests).
- Reporta resultados y espera validación del dev.
- Al recibir aprobación pasa a la fase "Al hacer pruebas funcionales".

Al hacer pruebas funcionales:
Adopta el comportamiento del siguiente agente: agents/functional-reviewer.md
- Ejecuta las tareas de ese rol
- En F00 la validación se reduce a que la app arranca (`mvn spring-boot:run` llega a `Started ...Application`); no hay endpoints que probar.
- Al finalizar espera aprobación del dev para pasar a la fase "Al terminar"

Al terminar:
- Si ejecutaste la aplicación asegurate de apagarla.
- Si se usaron variables de entorno en las pruebas, asegurate que no queden guardadas en memoria.
- Los contenedores de docker pueden seguir ejecutandose.
- Solicita la revisión del dev
- Si aprueba, mergea `AI/feature/{NumeroFeature}-{NombreFeature}` → `feature/{NumeroFeature}-{NombreFeature}` → la rama feature general del requerimiento.
- ** Nunca hagas merge hacia `develop`. ** Esa integración (rama feature general → `develop`) la hace solo el dev, de forma cuidadosa y manual.
- Nunca hagas push
