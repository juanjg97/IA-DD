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
4. Crea, a partir de develop, la rama feature/{NumeroFeature}-{NombreFeature}
5. Crea, a partir de develop, la rama AI/feature/{NumeroFeature}-{NombreFeature}

Regla de nombrado de ramas:
- Conserva el número de feature como aparece en el PRD, por ejemplo `F06-Motor-De-Reglas`
- Usa el mismo sufijo para ambas ramas: `feature/F06-Motor-De-Reglas` y `AI/feature/F06-Motor-De-Reglas`
- Normaliza el nombre de la rama a ASCII: sin tildes y con los espacios convertidos a guiones, por ejemplo `F03 - Gestión De Reglas` → `feature/F03-Gestion-De-Reglas`

Estrategia de ramas:
- El dev ya está posicionado manualmente en `develop`; el agente no crea ni cambia esa rama.
- Trabaja y haz commits únicamente sobre `AI/feature/{NumeroFeature}-{NombreFeature}`, nunca sobre `feature/{NumeroFeature}-{NombreFeature}` ni `develop`.
- Al terminar se mergea `AI/feature/{NumeroFeature}-{NombreFeature}` a `feature/{NumeroFeature}-{NombreFeature}` (ver "Al terminar").

Al implementar:
- Respeta estrictamente las indicaciones del archivo de PRD General indicado.
- Básate en la estructura de paquetes ya implementada.
- Nunca ejecutes DDL contra la base de datos. Si la feature requiere cambios de esquema, entrega un script SQL listo en `db/` e indica al dev que lo ejecute en su MySQL local (Docker); el esquema productivo es responsabilidad del equipo DBA.

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
- Reporta resultados y espera validación del dev.
- Al recibir aprobación pasa a la fase "Al hacer pruebas funcionales".

Al hacer pruebas funcionales:
Adopta el comportamiento del siguiente agente: agents/functional-reviewer.md
- Ejecuta las tareas de ese rol
- Al finalizar espera aprobación del dev para pasar a la fase "Al terminar"

Al terminar:
- Si ejecutaste la aplicación asegurate de apagarla.
- Si se usaron variables de entorno en las pruebas, asegurate que no queden guardadas en memoria.
- Los contenedores de docker pueden seguir ejecutandose.
- Solicita la revisión del dev
- Si aprueba haz merge de AI/feature/{NumeroFeature}-{NombreFeature} a feature/{NumeroFeature}-{NombreFeature}
- Nunca hagas push
