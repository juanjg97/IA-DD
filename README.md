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
        merge  AI/feature/...  ──►  feature/...      (el dev revisa; nunca se hace push)
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

## Cómo usarlo (paso a paso)

### 0. Preparar el proyecto destino
- Un proyecto **Java 21 + Spring Boot 3.x** con **Maven**.
- Copia `rules/rules.md` a `.claude/settings.json` del proyecto para que los permisos apliquen.
- Crea `functional-test-env.local` con host/puerto/credenciales **mock** (no datos reales; no es un `.env`).

### 1. Generar el BRD
- Abre `prompts/Prompt_Create_BRD.md` y pega el contexto donde dice `[PEGAR AQUI EL CONTEXTO DEL PROYECTO]`.
- Dáselo a un agente → te devuelve el **BRD** (solo negocio, sin decisiones técnicas).

### 2. Generar el PRD
- Toma ese BRD y pásalo con `prompts/Prompt_Create_PRD.md` → obtienes el **PRD General** + un archivo por feature (`F00`, `F01`, …) en `docs/`.
- (Opcional) Si tienes material técnico de referencia (request/response de APIs consumidas, OpenAPI/Swagger, esquemas JSON, ejemplos de payload), déjalo en `docs/references/` **antes** de generar el PRD: el agente lo usará como fuente de contratos. Eso no va en el BRD.
- `F00` siempre es la estructura base del proyecto.

### 3. Implementar cada feature
- Invoca `commands/develop-command.md` dándole el **PRD General** + el **PRD de la feature**.
- El agente recorre el flujo: implementa → revisa ⇄ ajusta → tests unitarios → pruebas funcionales → merge `AI/feature` → `feature`.
- Tú apruebas en cada fase.

---

## Convenciones clave

- **Stack fijo (siempre):** Java 21, Spring Boot 3.x, Maven, JaCoCo, JUnit 5 + Mockito.
- **Stack condicional (si se usa, debe ser este):** MySQL (persistencia), RabbitMQ (mensajería).
- **Ramas:** el dev está en `develop`; se crean `feature/FNN-Nombre` y `AI/feature/FNN-Nombre` desde `develop`. Se trabaja y commitea **solo** en `AI/feature/...`; al terminar se mergea a `feature/...`. **Nunca se hace push.**
- **Postman:** si la feature toca el flujo HTTP, la colección va en la carpeta `postman/`.
- **Referencias técnicas (entrada del PRD):** material técnico que no es negocio (request/response de APIs consumidas, OpenAPI/Swagger, esquemas JSON, ejemplos de payload) va en `docs/references/`. El agente del PRD lo lee como fuente de contratos; **no** se mete en el BRD y **no** expande el alcance que define el BRD. Es opcional.
- **Base de datos:** el agente **no ejecuta DDL**; deja el script SQL en `db/` y el **dev** lo corre en su MySQL local (Docker). El esquema productivo lo maneja el equipo **DBA**.
- **Infra local (Docker):** si la feature requiere herramientas que corren en contenedor (MySQL, RabbitMQ, etc.), el agente deja un `docker-compose.yml` en la raíz con valores **dummy hardcodeados** (es todo local) para que el **dev** levante con `docker compose up -d`. Es **condicional**: solo se declaran los servicios que el proyecto realmente usa (p. ej. no se agrega Mongo si no hay NoSQL). Adaptar a otros ambientes es responsabilidad del dev.
- **Pruebas funcionales:** levantan la app con `mvn spring-boot:run` (sin construir JAR); la config sale de `functional-test-env.local`.

---

## Notas

- Son **referencias adaptables por proyecto**, no una app lista para correr.
- El agente **no accede a internet** salvo los dominios de documentación permitidos en `rules/rules.md`.
- Está **prohibido leer archivos `.env`/secretos**; las pruebas usan solo valores mock locales.
