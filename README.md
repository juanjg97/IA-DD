# Kit de Desarrollo Asistido por IA — Java / Spring Boot

Plantillas de **referencia** para llevar un proyecto Java / Spring Boot desde la idea hasta la implementación, **feature por feature**, apoyándote en un agente de IA.

No es una aplicación: son archivos que el dev **adapta por proyecto**. El núcleo (prompts, roles y flujo) es agnóstico de la herramienta de IA; el cableado específico de Claude Code (`rules/rules.md`) va aparte.

---

## El flujo en 3 etapas

```text
        Contexto del proyecto (lo pegas tú)
                    │
                    ▼
        prompts/Prompt_Create_BRD.md   ──►  BRD            (nivel negocio, sin stack)
                    │
                    ▼
        prompts/Prompt_Create_PRD.md   ──►  PRD General + 1 archivo por feature   (nivel técnico)
                    │
                    ▼
        commands/develop-command.md   (se ejecuta por CADA feature)
            dev-writer → dev-reviewer ⇄ dev-writer → unit-test-writer → functional-reviewer
                    │
                    ▼
        merge  AI/feature → feature → rama feature general   (luego el dev integra a develop; nunca push)
```

---

## Estructura de archivos

| Ruta | Qué es |
|------|--------|
| `prompts/Prompt_Create_BRD.md` | Prompt: pegas el contexto → genera el **BRD** (negocio) |
| `prompts/Prompt_Create_PRD.md` | Prompt: toma el BRD → genera el **PRD General** + un `.md` por feature (técnico) |
| `commands/develop-command.md` | Flujo de implementación de una feature de principio a fin |
| `agents/dev-writer.md` | Rol: implementa la feature |
| `agents/dev-reviewer.md` | Rol: revisa el código (estricto, busca problemas) |
| `agents/unit-test-writer.md` | Rol: tests unitarios (JUnit 5 + Mockito, cobertura JaCoCo) |
| `agents/functional-reviewer.md` | Rol: pruebas funcionales (usa el skill) |
| `skills/skill-functional-tests.md` | Metodología de prueba funcional local |
| `rules/rules.md` | Permisos del agente (formato `settings.json` de Claude Code) |

---

## Punto de partida en un proyecto nuevo (mínimo para el agente)

Lo mínimo que debe existir para que el agente tenga el panorama completo. El dev **copia de este kit** las carpetas de plantillas y **crea** `docs/`. La estructura Spring Boot (`src/`, `pom.xml`) es la feature `F00`, y el resto de artefactos (`db/`, `postman/`, `docker-compose.yml`) se generan feature por feature durante el flujo. (`functional-test-env.local` lo crea el dev a mano en el paso 0, no el agente.)

```text
mi-proyecto/
├── prompts/          # copiado del kit — plantillas BRD/PRD
├── agents/           # copiado del kit — roles del flujo (dev-writer, dev-reviewer, …)
├── commands/         # copiado del kit — comando de desarrollo por feature
├── skills/           # copiado del kit — metodologías (p. ej. pruebas funcionales)
├── rules/            # copiado del kit — permisos del agente (se cablea a la herramienta de IA)
└── docs/             # lo crea el dev — contexto que el agente lee y escribe
    └── references/   # (opcional) insumos técnicos de entrada del PRD: contratos, OpenAPI, esquemas
```

**Notas:**
- Las plantillas (`prompts/`, `agents/`, `commands/`, `skills/`) son **agnósticas de la herramienta de IA**; `rules/` es lo único atado a ella (en Claude Code → `.claude/settings.json`).
- `docs/` arranca casi vacío: el **BRD**, el **PRD General** y los `docs/features/FNN_*.md` se van generando con los prompts. `docs/references/` es opcional y lo llena el dev **antes** de generar el PRD.

---

## Cómo usarlo (paso a paso)

### 0. Preparar el proyecto destino
- Destino: un proyecto **Java 21 + Spring Boot 3.x** con **Maven**. La estructura base la crea o verifica la feature `F00`; no hace falta scaffoldearla a mano.
- **Git es tu tarea:** `git init` (si aún no hay repo), crea `develop` y, desde `develop`, crea la **rama feature general** del requerimiento (`feature/<requerimiento>`) y posiciónate en ella. El agente no crea ni integra esas ramas: si no estás en la rama feature general, se detiene y te lo pide.
- Copia `rules/rules.md` a `.claude/settings.json` del proyecto para que los permisos apliquen.
- Crea `functional-test-env.local` con host/puerto/credenciales **mock** (no datos reales; no es un `.env`). Es la **fuente de verdad** de la config local; `application.properties` la referencia con placeholders sin default. Ejemplo de formato (valores dummy):

```text
SERVER_PORT=8080
DB_HOST=localhost
DB_PORT=3306
DB_NAME=demo
DB_USER=demo
DB_PASSWORD=demo
```

### 1. Generar el BRD
- Abre `prompts/Prompt_Create_BRD.md` y pega el contexto donde dice `[PEGAR AQUI EL CONTEXTO DEL PROYECTO]`.
- Dáselo a un agente → te devuelve el **BRD** (solo negocio, sin decisiones técnicas).

### 2. Generar el PRD
- Toma ese BRD y pásalo con `prompts/Prompt_Create_PRD.md` → obtienes el **PRD General** + un archivo por feature (`F00`, `F01`, …) en `docs/`.
- (Opcional) Si tienes material técnico de referencia (request/response de APIs consumidas, OpenAPI/Swagger, esquemas JSON, ejemplos de payload), déjalo en `docs/references/` **antes** de generar el PRD: el agente lo usará como fuente de contratos. Eso no va en el BRD.
- `F00` siempre es la estructura base del proyecto. Solo se valida que **compile y arranque**; las pruebas (unitarias y funcionales) empiezan en `F01`.

### 3. Implementar cada feature
- Invoca `commands/develop-command.md` dándole el **PRD General** + el **PRD de la feature**.
- El agente recorre el flujo: implementa → revisa ⇄ ajusta → tests unitarios → pruebas funcionales → merge `AI/feature` → `feature` → rama feature general (la integración a `develop` la hace el dev).
- Tú apruebas en cada fase.

---

## Convenciones clave

- **Stack fijo (siempre):** Java 21, Spring Boot 3.x, Maven, JaCoCo, JUnit 5 + Mockito.
- **Stack condicional (si se usa, debe ser este):** MySQL (persistencia), RabbitMQ (mensajería).
- **Ramas (gitflow):** el dev crea `develop` y, desde ahí, la **rama feature general** del requerimiento (`feature/<requerimiento>`). El agente crea `feature/FNN-Nombre` desde la rama feature general y `AI/feature/FNN-Nombre` desde su propia `feature/FNN-Nombre`, trabaja y commitea **solo** en `AI/feature/...`, y al terminar mergea `AI/feature/FNN` → `feature/FNN` → rama feature general. **El agente nunca hace merge hacia `develop`:** la integración a `develop` la hace solo el dev (cuidadosa y manual). **Nunca se hace push.**
- **`postman/` (salida):** si la feature toca el flujo HTTP, el agente genera o actualiza en `postman/` la colección de los endpoints que la app **expone**; es un entregable y se usa en las pruebas funcionales.
- **`docs/references/` (entrada):** material técnico que no es negocio (request/response de APIs que la app **consume**, OpenAPI/Swagger, esquemas JSON, payloads) que el **dev** deja —antes de generar el PRD— para que el agente lo **lea** como fuente de contratos. No va en el BRD, no expande el alcance y es opcional. Es lo opuesto a `postman/`: insumo de solo-lectura, no artefacto producido.
- **Base de datos:** el agente **no ejecuta cambios de estructura (DDL)**; los deja como script SQL en `db/` y el **dev** los corre en su MySQL local (Docker). La **manipulación de datos (DML: insert/update/delete) sí puede hacerla el agente**. El esquema productivo lo maneja el equipo **DBA**.
- **Infra local (Docker):** si la feature requiere herramientas que corren en contenedor (MySQL, RabbitMQ, etc.), el agente deja un `docker-compose.yml` en la raíz con valores **dummy hardcodeados** (es todo local) para que el **dev** levante con `docker compose up -d`. Es **condicional**: solo se declaran los servicios que el proyecto realmente usa (p. ej. no se agrega Mongo si no hay NoSQL). Adaptar a otros ambientes es responsabilidad del dev.
- **APIs externas (consumidas):** el **dev** prepara el mock/stub y pone su host en `functional-test-env.local`; el **agente** declara el placeholder correspondiente en `application.properties`. El agente no levanta el mock, solo pide revisar las dependencias externas (APIs, Docker) antes de las pruebas funcionales (sí puede inspeccionar contenedores con `docker ps`/`docker logs`).
- **Configuración local:** `application.properties` con **placeholders sin valores por defecto** (p. ej. `${DB_HOST}`, no `${DB_HOST:localhost}`); la **fuente de verdad** de esos valores es `functional-test-env.local`. Los valores mock deben **coincidir** entre `functional-test-env.local`, `docker-compose.yml` y el DDL de `db/`.
- **Pruebas funcionales:** levantan la app con `mvn spring-boot:run` (sin construir JAR, usa `application.properties`); el agente lee las variables de `functional-test-env.local` y las pasa como **variables de entorno** al ejecutar. El dev las configura manualmente en IntelliJ.
- **Cobertura:** > 80% medida con JaCoCo, solo sobre código testeable; no aplica a DTOs/entidades/configuración ni cuando aún no hay lógica que probar.

---

## Notas

- Son **referencias adaptables por proyecto**, no una app lista para correr.
- El agente **no accede a internet** salvo los dominios de documentación permitidos en `rules/rules.md`.
- Está **prohibido leer archivos `.env`/secretos**; las pruebas usan solo valores mock locales.
- **Sobre la agnosticidad:** el núcleo es agnóstico de la herramienta de IA para mantener la metodología portable y fácil de refinar, pero por sí solo no ejecuta nada — siempre corre sobre una herramienta concreta (aquí Claude Code) y necesita su cableado (`rules/rules.md` → `.claude/settings.json`). Para que le sirva a otra persona del equipo, hay que aportar el cableado de su herramienta.
