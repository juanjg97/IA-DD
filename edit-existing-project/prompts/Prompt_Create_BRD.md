Actua como Product Manager / Business Analyst senior. Redacta un **Brief de Cambio** para una feature o cambio sobre un proyecto **ya existente**.

Objetivo:
Capturar, a nivel negocio/funcional, **que** cambio se quiere y **por que**, acotado y listo para servir de fuente al PRD de Cambio. No incluyes detalle tecnico.

Contexto del proyecto existente:
El proyecto ya esta documentado en `docs/Project_Profile_{NombreProyecto}.md` (Perfil del Proyecto). No lo redocumentes aqui; solo describe el cambio.

Contexto del cambio (feature):
[PEGAR AQUI LA DESCRIPCION DE LA FEATURE / CAMBIO O EL TICKET]

Instrucciones de salida:
- Genera un unico archivo Markdown nombrado `docs/Change_Brief_{NombreCambio}.md`, donde `{NombreCambio}` es el nombre del cambio normalizado a ASCII (sin tildes y sin espacios).
- Tono formal, directo y funcional. **Sin** arquitectura, stack, base de datos, clases, endpoints ni detalle de implementacion (eso va en el PRD de Cambio).
- Manten el alcance al **cambio**, no al producto entero.
- Si falta informacion, toma supuestos conservadores y marcalos como `Supuesto`.
- Usa nomenclatura `RF-01`, `RF-02`, etc. para los requerimientos funcionales del cambio (proporcional a su tamaño).

Estructura obligatoria del documento:

# Brief de Cambio: [Nombre Del Cambio]

**Version:** 1.0
**Fecha:** [YYYY-MM-DD]
**Tipo de documento:** Brief de Cambio (brownfield)
**Proyecto:** [Nombre del proyecto existente]
**Perfil:** `docs/Project_Profile_{NombreProyecto}.md`

## 1. Resumen Del Cambio
1 a 3 lineas: que se quiere agregar o modificar y para quien.

## 2. Problema / Motivacion
Por que se necesita el cambio (negocio o usuario); que duele hoy.

## 3. Objetivo Del Cambio
Que se quiere lograr, en terminos verificables desde negocio.

## 4. Alcance

### 4.1 Incluido
### 4.2 Fuera De Alcance / No Objetivos

## 5. Comportamiento Esperado
Que debe pasar a nivel funcional (flujo, entradas y salidas observables). Sin detalle tecnico.

## 6. Requerimientos Funcionales Del Cambio

### RF-01 [Nombre Del Requerimiento]
[Descripcion]

## 7. Reglas De Negocio Y Requerimientos No Funcionales
Reglas, validaciones o condiciones de negocio que aplican al cambio (incluidas las existentes que se deben respetar) y cualquier requisito no funcional relevante (rendimiento, seguridad, etc.).

## 8. Usuarios / Actores Afectados
Quien usa o se ve afectado por el cambio.

## 9. Impacto Esperado En Lo Existente
A nivel negocio/funcional: que funcionalidad o flujo actual podria verse afectado. (El analisis tecnico de impacto va en el PRD de Cambio.)

## 10. Dependencias Y Supuestos
Dependencias funcionales, datos necesarios y supuestos tomados (marcados como `Supuesto`).

## 11. Criterios De Aceptacion (Negocio)
Lista verificable que permita decir que el cambio quedo bien, desde negocio.

Validacion antes de terminar:
- El brief describe solo el cambio (no el producto entero) y no incluye detalle tecnico.
- El alcance (incluido/excluido) esta claro y acotado.
- Los RF tienen nomenclatura consistente.
- Los supuestos quedaron explicitos.
