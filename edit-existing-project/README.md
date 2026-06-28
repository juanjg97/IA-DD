# Kit de Desarrollo Asistido por IA — Proyectos Existentes (Brownfield)

Plantillas de **referencia** para evolucionar un proyecto Java / Spring Boot **ya existente**, cambio por cambio, apoyándote en un agente de IA.

No es una aplicación: son archivos que el dev **adapta por proyecto**. A diferencia de la edición *greenfield* (arrancar de cero), aquí el agente **se conforma al proyecto existente**: detecta su stack, arquitectura y convenciones, y respeta lo que ya hay.

> Para **arrancar un proyecto nuevo**, usa la carpeta `build-new-project/`.

---

## El flujo

```text
── UNA VEZ por proyecto ──
  prompts/Prompt_Analyze_Existing.md  ──►  docs/Project_Profile_{NombreProyecto}.md
                                            (baseline técnico: stack real, arquitectura,
                                             convenciones, build, tests, BD, git)

── POR CADA cambio / feature ──
  prompts/Prompt_Create_BRD.md   ◄── pegas la descripción del cambio/ticket
        ──►  docs/Change_Brief_{NombreCambio}.md            (negocio/funcional: qué y por qué)
                    │
  prompts/Prompt_Create_PRD.md   ◄── Change Brief + Project Profile (+ docs/references)
        ──►  docs/PRD_Change_{NombreCambio}.md + docs/features/FNN_*.md
                    │                  (técnico: conforme a lo existente, impacto, regresión)
  commands/develop-command.md   (por CADA feature del cambio)
        dev-writer → dev-reviewer ⇄ dev-writer → unit-test-writer → functional-reviewer (hand-off)
                    │
        merge  AI/feature → feature → rama feature general   (el dev integra a develop; nunca push)
```

---

## Estructura de archivos

| Ruta | Qué es |
|------|--------|
| `prompts/Prompt_Analyze_Existing.md` | Prompt: analiza el proyecto → **Perfil del Proyecto** (baseline técnico) |
| `prompts/Prompt_Create_BRD.md` | Prompt: pegas el cambio → **Brief de Cambio** (negocio/funcional) |
| `prompts/Prompt_Create_PRD.md` | Prompt: Brief + Perfil → **PRD de Cambio** + un `.md` por feature (técnico) |
| `commands/develop-command.md` | Flujo de implementación de una feature del cambio |
| `agents/dev-writer.md` | Rol: implementa, conformándose a lo existente |
| `agents/dev-reviewer.md` | Rol: revisa (calidad + conformidad + impacto/regresión) |
| `agents/unit-test-writer.md` | Rol: tests del cambio + regresión de la suite completa |
| `agents/functional-reviewer.md` | Rol: prepara el **hand-off** de pruebas funcionales para el dev |
| `skills/skill-functional-tests.md` | Metodología del hand-off funcional (el agente no ejecuta) |
| `rules/rules.md` | Permisos del agente (formato `settings.json` de Claude Code) |

---

## Cómo usarlo (paso a paso)

### 0. Preparar
- Un proyecto **Java + Spring Boot + Maven o Gradle** ya existente (el agente confirma y respeta su versión real; puede ser Java 17 / Spring Boot 2.x).
- Copia las carpetas del kit (`prompts/`, `agents/`, `commands/`, `skills/`, `rules/`) al proyecto y crea `docs/`.
- Copia `rules/rules.md` a `.claude/settings.json`. Ajusta los globs a tu layout (mono o multi-módulo) si hace falta.
- **Git (tarea tuya):** el proyecto debe tener `develop`; por cada requerimiento creas la **rama feature general** (`feature/<requerimiento>`) y te posicionas en ella. Si tu equipo usa otro git flow, adapta `commands/develop-command.md` y `rules/rules.md`.

### 1. Generar el Perfil del Proyecto (una vez)
- Corre `prompts/Prompt_Analyze_Existing.md` → genera `docs/Project_Profile_{NombreProyecto}.md`. Es la **fuente de verdad** del estado real; se reutiliza para todos los cambios.

### 2. Brief de Cambio (por cambio)
- Pega la descripción del cambio/ticket en `prompts/Prompt_Create_BRD.md` → `docs/Change_Brief_{NombreCambio}.md` (negocio/funcional).
- (Opcional) Material técnico de contratos (request/response, OpenAPI, esquemas) → `docs/references/`.

### 3. PRD de Cambio (por cambio)
- Pasa el Brief + el Perfil con `prompts/Prompt_Create_PRD.md` → `docs/PRD_Change_{NombreCambio}.md` + un archivo por feature. **Sin `F00`:** el proyecto ya existe; las features empiezan en `F01`.

### 4. Implementar cada feature
- Invoca `commands/develop-command.md` con el **PRD de Cambio** + el **PRD de la feature**.
- El agente: implementa (conforme a lo existente) → revisa ⇄ ajusta → tests + regresión → prepara el hand-off funcional → merge `AI/feature` → `feature` → rama feature general.
- Tú apruebas en cada fase, **tú corres las pruebas funcionales** y **tú integras a `develop`**.

---

## Convenciones clave

- **Conformar a lo existente:** el agente detecta y respeta el stack, la arquitectura (package-by-layer, package-by-feature, hexagonal o la que sea), las convenciones y librerías del proyecto. No impone patrones; las desviaciones se marcan como `Desviacion` en el PRD de Cambio.
- **Stack:** se confirma y documenta en el Perfil (familia Java / Spring Boot / Maven o Gradle; con su versión real).
- **Ramas (gitflow):** `develop` + **rama feature general** (`feature/<requerimiento>`) + `feature/FNN` + `AI/feature/FNN`. El agente trabaja en `AI/feature/...` y mergea hasta la rama feature general; **nunca a `develop`** — eso lo hace el dev (y luego validan otros equipos). **Nunca se hace push.**
- **Base de datos:** el agente **no ejecuta cambios contra la base de datos**; entrega los de estructura como **migración** (Flyway/Liquibase) y la corre el **DBA**.
- **Pruebas unitarias + regresión:** el agente escribe tests del **código cambiado** (cobertura > 80% sobre lo cambiado) y **ejecuta la suite completa existente** (debe quedar en verde, sin regresión).
- **Pruebas funcionales (hand-off):** el agente **no las ejecuta** (levantar la app con sus acoples es frágil); arma colecciones/payloads + **resultado esperado** + precondiciones, y **el dev las corre**.
- **Configuración:** el agente usa el **mecanismo de config existente** del proyecto (profiles/properties/yaml/variables); no impone uno nuevo. No lee secretos.
- **`docs/references/` (entrada):** contratos de APIs consumidas / esquemas que el dev deja para el PRD; el agente los lee.
- **`postman/` (salida):** colecciones de los endpoints, generadas/actualizadas por el agente **al implementar**; el hand-off las reúne y les documenta el resultado esperado.

---

## Notas

- Son **referencias adaptables por proyecto**, no una app lista para correr.
- El agente **no accede a internet** salvo los dominios permitidos en `rules/rules.md`.
- Está **prohibido leer `.env`/secretos**; el `deny` está endurecido para config de producción, keystores y credenciales.
- **Agnosticidad:** el núcleo es agnóstico de la herramienta de IA; el cableado específico (Claude Code → `.claude/settings.json`) va en `rules/`. Por sí solo no ejecuta nada: siempre corre sobre una herramienta concreta.
