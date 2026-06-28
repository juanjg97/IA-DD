Actua como Staff Engineer experto en analizar codebases existentes (brownfield). Tu tarea es hacer un reconocimiento de **solo lectura** del proyecto y producir un "Perfil del Proyecto" que sirva de **fuente de verdad** para el resto del flujo (brief de cambio, PRD de cambio e implementacion).

Objetivo:
Documentar, con evidencia del codigo, el estado real del proyecto: stack, arquitectura, convenciones, build/run, pruebas, persistencia, configuracion e integraciones. No decides nada nuevo: documentas lo que ya existe.

Restricciones (importantes):
- Analisis de **solo lectura**: no modifiques codigo, no ejecutes comandos que cambien estado, no hagas commits.
- **No leas** archivos de secretos/credenciales (`.env`, `application-*prod*.properties`, `*.jks`, `*.pem`, `*.key`, `**/secrets/**`). Si necesitas saber que variables existen, listalas por nombre sin abrir el archivo sensible.
- No accedas a internet salvo lo permitido por las reglas.
- Respalda cada afirmacion con **evidencia** (ruta de archivo y, si aplica, linea). Si algo no se puede confirmar, marcalo como `Por confirmar`.

Instrucciones de salida:
- Genera un unico archivo Markdown nombrado `docs/Project_Profile_{NombreProyecto}.md`, donde `{NombreProyecto}` es el nombre del proyecto normalizado a ASCII (sin tildes y sin espacios).
- Usa lenguaje tecnico claro y directo.
- Usa `No aplica` cuando una seccion no corresponda y `Por confirmar` cuando no se pueda determinar.

Estructura obligatoria del documento:

# Perfil del Proyecto: [Nombre Del Proyecto]

**Version:** 1.0
**Fecha:** [YYYY-MM-DD]
**Tipo de documento:** Perfil del Proyecto (baseline brownfield)
**Fuente:** analisis de solo lectura del repositorio

## 1. Resumen
2 a 4 lineas: que es el proyecto, su proposito tecnico y su madurez aparente.

## 2. Stack Real
- Lenguaje y version (evidencia: `pom.xml`/`build.gradle`, toolchain, `.java-version`).
- Framework y version (Spring Boot u otro; evidencia del parent/BOM).
- Build tool (Maven o Gradle) y como se invoca (`mvn ...` / `./gradlew ...`).
- Dependencias clave (persistencia, mensajeria, cliente HTTP, mapeo, etc.).
- Comparacion con la familia esperada (Java / Spring Boot / Maven): indica si coincide o si difiere (p. ej. Java 17, Spring Boot 2.x, Gradle) y marca lo que el flujo debera respetar.

## 3. Estructura Y Arquitectura
- Mono-modulo o multi-modulo (lista los modulos y sus rutas).
- Estilo de arquitectura observado (package-by-layer, package-by-feature, hexagonal, etc.) con evidencia.
- Mapa de paquetes/capas principales y sus responsabilidades.

## 4. Convenciones Del Codigo
Infiere del codigo existente, con evidencia: naming, ubicacion de DTOs (in/out), validacion, manejo de errores, logging, estilo de controllers/services/repositories. Esto es lo que el agente debera **imitar** al implementar.

## 5. Build, Ejecucion Y Configuracion Local
- Comandos de build, run y test locales.
- Archivos de configuracion (`application.properties`/`.yml`, profiles) y como se inyecta la config (env, placeholders, profiles). No abras los que contengan secretos.
- Como se levanta localmente (puertos, dependencias de infra: BD, mensajeria, mocks).

## 6. Pruebas
- Frameworks (JUnit, Mockito, etc.) y ubicacion de los tests.
- Comando exacto para correr la **suite completa**.
- Estado actual: ¿corre en verde? ¿hay cobertura medida y con que herramienta?
- Esta suite es la **base de regresion** para validar que un cambio no rompe lo existente.

## 7. Persistencia Y Base De Datos
- Motor (MySQL u otro) y mecanismo de acceso a datos / ORM.
- Esquema y entidades principales.
- Herramienta de migraciones (Flyway/Liquibase) y ubicacion de las migraciones; si no hay, indicarlo.
- Recordatorio: los cambios de **estructura/migraciones** son responsabilidad del **DBA**; el agente solo deja el artefacto, no lo ejecuta.

## 8. Integraciones Externas
APIs que el proyecto consume, mensajeria, servicios externos y contratos conocidos. Indica si hay material de contratos en `docs/references/`.

## 9. Git Y Flujo De Trabajo
- ¿Existe la rama `develop`? Estado del repositorio.
- Confirma que el equipo se apega al gitflow del kit (`develop` + rama feature general + `feature/FNN` + `AI/feature/FNN`, y el agente nunca mergea a `develop`). Si el equipo usa otro flujo, advierte que deberan adaptar `commands/develop-command.md` y `rules/rules.md` a su flujo.

## 10. Zonas Sensibles Y Riesgos
Areas de alto impacto, deuda tecnica visible, acoplamientos fuertes y cualquier cosa que aumente el riesgo de regresion al tocar el proyecto.

## 11. Brechas Y Supuestos
Lista lo que quedo `Por confirmar` y los supuestos tomados, para que el dev los resuelva antes de implementar.

Validacion antes de terminar:
- Cada seccion tiene evidencia (rutas) o esta marcada como `Por confirmar`/`No aplica`.
- No se leyeron archivos de secretos ni se modifico codigo.
- El stack quedo confirmado y documentado (coincida o difiera de la familia esperada).
- Se identifico el comando para correr la **suite completa** (base de regresion).
- Se identifico la herramienta de migraciones (o su ausencia).
- Se confirmo si existe `develop` y si el equipo puede apegarse al gitflow del kit.
