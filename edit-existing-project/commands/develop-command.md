Adopta el comportamiento del siguiente agente: agents/dev-writer.md

** Lee primero todo este documento para tenerlo en contexto **
** No está permitido acceder a internet, salvo lo indicado explícitamente en las reglas **
** Revisa las reglas para saber qué está permitido **

Te mencioné 3 documentos de referencia: el Perfil del Proyecto, el PRD de Cambio y el PRD de feature.

Implementa la feature indicada del cambio, conformándote al proyecto existente.

Antes de escribir código:
1. Lee el Perfil del Proyecto (`docs/Project_Profile_{NombreProyecto}.md`): stack, arquitectura, convenciones, build, pruebas y persistencia reales que debes respetar.
2. Lee el PRD de Cambio completo.
3. Lee el PRD de la feature completo.
4. Identifica el número y nombre exacto de la feature en el PRD.
5. Análisis de impacto: confirma qué componentes/clases/módulos toca la feature y qué depende de ellos (callers, consumidores de tablas, otros módulos). No rompas su comportamiento actual.
6. Verifica que exista repositorio git y que estés en la **rama feature general** del requerimiento (`feature/{Requerimiento}`, no en `develop`). Si no hay repo, estás en `develop`, o no existe esa rama, detente e indícale al dev que `git init`, crear `develop` y crear/posicionarse en la rama feature general es su tarea (el agente no lo hace).
7. Crea, a partir de la rama feature general, la rama feature/{NumeroFeature}-{NombreFeature}
8. Crea, a partir de feature/{NumeroFeature}-{NombreFeature}, la rama AI/feature/{NumeroFeature}-{NombreFeature}

Regla de nombrado de ramas:
- Conserva el número de feature como aparece en el PRD, por ejemplo `F06-Motor-De-Reglas`
- Usa el mismo sufijo para ambas ramas: `feature/F06-Motor-De-Reglas` y `AI/feature/F06-Motor-De-Reglas`
- Normaliza el nombre de la rama a ASCII: sin tildes y con los espacios convertidos a guiones, por ejemplo `F03 - Gestión De Reglas` → `feature/F03-Gestion-De-Reglas`

Estrategia de ramas (gitflow):
- El dev hace `git init` (si aplica), crea `develop` y, desde `develop`, crea la rama feature general del requerimiento `feature/{Requerimiento}` y se posiciona en ella antes de invocarte. El agente no crea ni toca `develop` ni la rama feature general.
- `feature/{NumeroFeature}-{NombreFeature}` se crea desde la rama feature general; `AI/feature/{NumeroFeature}-{NombreFeature}` se crea desde su propia `feature/{NumeroFeature}-{NombreFeature}`.
- Trabaja y haz commits únicamente sobre `AI/feature/{NumeroFeature}-{NombreFeature}`, nunca sobre `feature/{NumeroFeature}-{NombreFeature}`, la rama feature general ni `develop`.
- Al terminar se mergea `AI/feature/{NumeroFeature}-{NombreFeature}` → `feature/{NumeroFeature}-{NombreFeature}` → la rama feature general (ver "Al terminar").
- ** El agente NUNCA hace merge hacia `develop`. ** La integración de la rama feature general a `develop` es responsabilidad EXCLUSIVA del dev (cuidadosa y manual). Tras `develop`, el cambio pasa por las pruebas y validaciones de otros equipos.

Al implementar:
- Conforma a lo existente: respeta la arquitectura, convenciones y librerías reales del proyecto (Perfil + código cercano). No impongas patrones ni dependencias nuevas; las desviaciones deben estar marcadas en el PRD de Cambio.
- En componentes existentes cambia lo mínimo necesario y no rompas su contrato. Distingue lo nuevo de lo modificado.
- **No ejecutes cambios contra la base de datos.** Los de estructura/esquema (DDL) entrégalos como **migración** (Flyway/Liquibase, según el Perfil) o script SQL en la ubicación del proyecto; lo ejecuta el **DBA**.

Al revisar:
Adopta el comportamiento del siguiente agente: agents/dev-reviewer.md
- Ejecuta las tareas de ese rol
- Si no hay inconsistencias, pasa a la fase de "Al escribir tests"
- Si tienes propuestas indicalas y espera aprobación del dev
- Si el dev aprueba, adopta el comportamiento del siguiente agente: agents/dev-writer.md y realiza los ajustes
- Repite este flujo hasta que no haya inconsistencias, al finalizar pasa a la fase "Al escribir tests"

Al escribir tests:
Adopta el comportamiento del siguiente agente: agents/unit-test-writer.md
- Ejecuta las tareas de ese rol: tests del cambio (cobertura > 80% sobre lo cambiado) **y regresión de la suite completa existente (debe quedar en verde)**.
- Reporta resultados (incluido el resultado de la suite completa) y espera validación del dev.
- Al recibir aprobación pasa a la fase "Al hacer pruebas funcionales".

Al preparar las pruebas funcionales (las ejecuta el dev, no el agente):
Adopta el comportamiento del siguiente agente: agents/functional-reviewer.md
- Ejecuta las tareas de ese rol: prepara el hand-off (colecciones/payloads + resultado esperado + precondiciones).
- El agente NO levanta la app ni ejecuta funcionales; el dev las corre.
- Al finalizar, entrega el hand-off y espera la validación del dev para pasar a la fase "Al terminar".

Al terminar:
- Solicita la revisión del dev.
- Si aprueba, mergea `AI/feature/{NumeroFeature}-{NombreFeature}` → `feature/{NumeroFeature}-{NombreFeature}` → la rama feature general del requerimiento.
- ** Nunca hagas merge hacia `develop`. ** Esa integración (rama feature general → `develop`) la hace solo el dev, de forma cuidadosa y manual.
- Nunca hagas push
