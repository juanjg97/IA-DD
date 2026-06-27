Actua como Backend Senior Architect y Technical Product Manager. A partir del Business Requirements Document (BRD) proporcionado, genera un PRD General y un archivo Markdown independiente por cada feature secuencial de implementacion.

Objetivo:
Transformar el BRD en documentacion tecnica accionable para agentes implementadores.

Business Requirements Document (BRD): @BRD

Restriccion principal:
El PRD General solo debe definir el contexto tecnico, decisiones transversales, arquitectura, contratos generales, validacion, pruebas, DevOps y la enumeracion de features. No debe incluir detalle de implementacion ni criterios de aceptacion dentro de la lista de features.

La implementacion detallada y los criterios de aceptacion de cada feature deben vivir en un archivo `.md` separado por feature.

Instrucciones generales:
- Genera un PRD General en Markdown.
- Genera un archivo Markdown independiente por cada feature.
- Si tienes acceso a filesystem, crea los archivos. Si no tienes acceso, responde con bloques separados por ruta de archivo.
- Usa lenguaje tecnico claro, directo y ejecutable.
- Mantente fiel al BRD. No agregues objetivos, integraciones, features o complejidad fuera de alcance.
- El stack base es fijo y debe declararse como decision tecnica. Nucleo siempre presente: Java 21, Spring Boot 3.x, Maven, JaCoCo, JUnit 5 y Mockito. Bases fijas pero condicionales (solo si el proyecto las requiere, y si las requiere deben ser exactamente estas, no una equivalente): MySQL para persistencia y RabbitMQ para mensajeria.
- Cualquier otra herramienta debe quedar a discusion. No la declares como decision cerrada salvo que el BRD la exija explicitamente.
- Si falta una decision tecnica fuera del stack base, propone la opcion mas simple, local y conservadora, pero marcala como `A discusion`.
- Usa nomenclatura de features secuenciales como `F00`, `F01`, `F02`, etc.; `F00` es siempre la feature de estructura base del proyecto.
- Usa titulos de feature con el formato exacto `### F01 - Nombre De La Feature` dentro del PRD General.
- Usa titulos de archivo por feature con el formato exacto `# F01 - Nombre De La Feature`.
- Usa nombres de archivo por feature con el patron `FNN_slug_de_la_feature.md`.
- Conserva el orden logico de implementacion: base del proyecto, configuracion local, datos/persistencia, contratos, logica de negocio, mensajeria/integraciones internas, consultas, trazabilidad, datos de demostracion.
- No uses IDs alternativos para features como `FEATURE-01`, `TEC-01` o `EPIC-01`.
- Usa `No aplica` cuando una subseccion tecnica no corresponda a una feature.

Rutas de salida esperadas:

```text
docs/prd_general_[slug_del_proyecto].md
docs/features/F01_[slug_de_la_feature].md
docs/features/F02_[slug_de_la_feature].md
docs/features/F03_[slug_de_la_feature].md
...
```

Estructura obligatoria del PRD General:

# PRD General: [Nombre Del Proyecto]

**Version:** 1.0
**Fecha:** [YYYY-MM-DD]
**Alcance:** [Alcance tecnico resumido]
**Fuente:** `[nombre_archivo_brd].md`

## 1. Contexto General
Resume el producto desde el punto de vista tecnico. Incluye el flujo tecnico obligatorio en bloque `text` si el sistema tiene flujo secuencial, asincrono o por estados.

## 2. Stack Tecnico Decidido
Stack fijo siempre presente (no se discute la eleccion):

- Lenguaje: Java 21.
- Framework: Spring Boot 3.x.
- Build tool: Maven.
- Cobertura: JaCoCo.
- Pruebas unitarias: JUnit 5 + Mockito.

Bases fijas pero condicionales: inclúyelas solo si el proyecto las requiere; si se requieren, deben ser exactamente estas, no una equivalente:

- Base de datos: MySQL (solo si hay persistencia).
- Mensajeria: RabbitMQ (solo si hay eventos o mensajeria).

Agrega una subseccion `Herramientas A Discusion` para cualquier otra herramienta o decision complementaria, por ejemplo ORM, ejecucion local, documentacion de API, observabilidad, CI/CD o utilidades auxiliares. Para cada elemento a discusion, indica recomendacion, motivo y alternativa posible. No conviertas estas herramientas en decisiones cerradas.

## 3. Arquitectura De Paquetes
Define el estilo de arquitectura y la estructura de paquetes o modulos. Incluye reglas de ubicacion y responsabilidades.

## 4. Eventos Soportados
Si el producto usa eventos, enumera tipos validos y contrato minimo. Si no usa eventos, define contratos de entrada principales.

## 5. Reglas Del MVP
Traduce reglas de negocio a reglas tecnicas simples y deterministicas. No agregues reglas fuera del BRD.

## 6. Entidades, Resultados O Alertas
Define tipos, estados, canales o resultados principales segun el dominio.

## 7. Modelo De Datos Minimo
Define tablas, colecciones o estructuras persistentes minimas. No agregues tablas para entidades fuera del MVP. El esquema se entrega como script SQL (DDL) en `db/` para que el dev lo ejecute manualmente en su MySQL local (Docker); no se usa herramienta de migraciones automatica y el esquema productivo es responsabilidad del equipo DBA.

## 8. Mensajeria, Procesamiento O Integraciones Internas
Define exchanges, queues, topics, jobs, workers o procesos internos si aplican. Si no aplica, explica el procesamiento interno equivalente.

## 9. API REST Minima
Enumera endpoints minimos o contratos de interfaz. Incluye metodos, rutas y codigos esperados de forma general.

## 10. Validacion Y Pruebas
Define reglas de pruebas unitarias, funcionales, manuales, cobertura, herramientas y documentacion de validacion.

## 11. Features Secuenciales Para Agentes
Enumera las features en orden. Cada feature debe incluir solo:
- Identificador y nombre en heading `### FNN - Nombre De La Feature`.
- Una descripcion breve de 1 a 3 lineas.
- Ruta del archivo detallado de la feature.

Formato obligatorio:

### F01 - Nombre De La Feature

[Descripcion breve sin detalle de implementacion.]

Detalle: `docs/features/F01_slug_de_la_feature.md`

No incluyas criterios de aceptacion en esta seccion.
No incluyas detalle de implementacion en esta seccion.
No incluyas listas largas de tareas en esta seccion.

### Notas sobre features.
- Agrega una F00 - Estructura
- El agente debe generar la estructura del paquete con arquitectura basado en package-by-layer
- Estructura de controllers, services, clients, repository, model, config etc.
- Los dtos (in/out) http deben estar en la capa correspondiente.
- Otros dtos deben ir en su respectiva capa.

## 12. Guia Para Agentes Implementadores
Define reglas generales que aplican a todas las features: respetar alcance, pruebas, documentacion, arquitectura, configuracion, contratos y nombres.

## 13. Seccion DevOps Local / Ejecucion Local
Define solo servicios locales, puertos locales, variables de entorno, archivos esperados y comandos de ejecucion local. Esta seccion no define infraestructura productiva, despliegue cloud, Kubernetes, Terraform, pipelines CI/CD ni operacion en ambientes reales.

## 14. Fuera De Alcance Tecnico
Lista explicitamente lo que no debe implementarse tecnicamente.

Estructura obligatoria de cada archivo de feature:

# FNN - Nombre De La Feature

**Version:** 1.0
**Fecha:** [YYYY-MM-DD]
**Feature:** FNN
**Fuente PRD General:** `docs/prd_general_[slug_del_proyecto].md`
**Estado:** Pendiente

## 1. Objetivo
Describe el objetivo tecnico y funcional de esta feature.

## 2. Alcance
Lista lo que esta feature debe entregar.

## 3. Fuera De Alcance
Lista lo que esta feature no debe tocar ni resolver.

## 4. Dependencias Y Precondiciones
Indica features previas, archivos, servicios, configuracion o decisiones requeridas antes de implementar.

## 5. Diseno Tecnico
Describe el detalle tecnico necesario para implementar la feature.

Incluye subsecciones solo cuando apliquen:

### 5.1 Paquetes Y Componentes
### 5.2 Contratos De Entrada Y Salida
### 5.3 Persistencia
### 5.4 Mensajeria O Procesamiento Asincrono
### 5.5 Configuracion
### 5.6 Manejo De Errores
### 5.7 Documentacion Y Postman

Usa `No aplica` en las subsecciones que no correspondan.

La documentacion de la subseccion 5.7 se materializa como una coleccion de Postman en la carpeta `postman/` del proyecto (no en `src/main/resources`).

Si la subseccion 5.3 implica cambios de esquema, el entregable es un script SQL (DDL) en `db/` que el dev ejecuta manualmente en MySQL local; el agente no ejecuta DDL.

## 6. Criterios De Aceptacion
Define criterios verificables. Usa bullets claros. Incluye comportamiento esperado, validaciones, codigos de respuesta, estados, logs, persistencia o documentacion cuando aplique.

## 7. Validacion Y Pruebas
Define pruebas unitarias, validacion funcional manual, comandos esperados y actualizaciones documentales.

## 8. Checklist Para Agente Implementador
Incluye una lista concreta de verificacion antes de cerrar la feature.

## 9. Notas De Implementacion
Agrega restricciones, decisiones o advertencias tecnicas utiles para evitar desviaciones de alcance.

Validacion antes de terminar:
- El PRD General contiene todas las secciones 1 a 14.
- La seccion 11 del PRD General solo enumera features y rutas de detalle.
- Cada feature tiene un archivo `.md` independiente.
- Cada feature usa `FNN - Nombre De La Feature` como titulo.
- Cada nombre de archivo sigue `FNN_slug_de_la_feature.md`.
- Los criterios de aceptacion aparecen solo en los archivos por feature.
- El detalle de implementacion aparece solo en los archivos por feature.
- Las features estan en orden logico de implementacion.
- La documentacion respeta el alcance del BRD.
- Cada `RF-xx` y `RNF-xx` del BRD queda cubierto por al menos una feature (ningun requisito se pierde al transformar el BRD en PRD General).
- La seccion `DevOps Local / Ejecucion Local` se limita a ejecucion local y no incluye infraestructura productiva ni despliegue cloud.
- No se agregaron integraciones, seguridad, usuarios, frontend, infraestructura o reglas fuera del MVP salvo que el BRD lo pida explicitamente.