Actua como Product Manager senior y redacta un Business Requirements Document (BRD) para el proyecto descrito en el contexto.

Objetivo:
Generar un documento Markdown de Business Requirements Document (BRD), claro, completo, acotado y listo para servir como fuente de un PRD General posterior.

Contexto del proyecto:
[PEGAR AQUI EL CONTEXTO DEL PROYECTO]

Instrucciones de salida:
- Genera un unico archivo Markdown.
- Usa el titulo: "# Business Requirements Document (BRD): [Nombre Del Proyecto]".
- Usa tono formal, directo y funcional.
- No incluyas arquitectura tecnica, decisiones de stack, diseno de base de datos, colas, clases, endpoints especificos ni detalle de implementacion.
- Mantente en nivel negocio/producto.
- Define claramente que entra y que no entra en el MVP.
- Usa datos ficticios o simulados si el proyecto lo requiere.
- Evita expandir el alcance hacia una plataforma mas grande que el objetivo descrito.
- Si falta informacion, toma decisiones conservadoras y explicitalas como supuestos.
- Usa nomenclatura consistente para requerimientos funcionales como `RF-01`, `RF-02`, etc.
- Usa nomenclatura consistente para requerimientos no funcionales como `RNF-01`, `RNF-02`, etc.
- Si el dominio usa eventos, alertas, estados, canales, reglas o entidades equivalentes, enumera sus tipos en bloques `text`.
- Si alguna seccion no aplica exactamente al dominio, adapta el contenido manteniendo la intencion y la numeracion de la plantilla.

Estructura obligatoria del documento:

# Business Requirements Document (BRD): [Nombre Del Proyecto]

**Version:** 1.0
**Fecha:** [YYYY-MM-DD]
**Tipo de documento:** Business Requirements Document (BRD)
**Nivel:** Equipo de negocio
**Proyecto:** [Descripcion corta del proyecto]

## 1. Resumen Ejecutivo
Explica en 2 a 4 parrafos que se va a construir, para que sirve, que problema resuelve y que no pretende resolver.

## 2. Problema A Resolver
Describe el problema de negocio o aprendizaje. Incluye una frase central del problema en formato de cita.

## 3. Objetivo Del Producto
Define el objetivo principal del producto. Si aplica, incluye un flujo general en bloque `text`.

## 4. Objetivos De Negocio
Lista objetivos concretos, medibles o verificables desde negocio.

## 5. No Objetivos
Lista explicitamente lo que el proyecto no busca resolver.

## 6. Usuarios Objetivo
Define los usuarios, actores o perfiles principales. Usa subsecciones `### 6.1`, `### 6.2`, etc.

## 7. Alcance Del MVP

### 7.1 Incluido
Lista lo incluido en el MVP.

### 7.2 Excluido
Lista lo excluido del MVP.

## 8. Tipos De Eventos Del MVP
Si el producto es event-driven, enumera los eventos. Si no, usa esta seccion para los disparadores, entradas o acciones principales del MVP. Incluye descripcion por tipo usando subsecciones.

## 9. Tipos De Alertas Del MVP
Si el producto genera alertas, enumera sus tipos. Si no, usa esta seccion para los resultados, salidas, notificaciones, decisiones o entidades principales generadas por el sistema. Incluye ejemplos cuando aplique.

## 10. Canales Simulados
Si aplica, enumera canales de entrega, comunicacion o visualizacion. Si no aplica, define los medios por los que el usuario consume el resultado del sistema.

## 11. Reglas De Negocio Iniciales
Define reglas simples, explicables y acotadas. Incluye umbrales o condiciones iniciales si aplica.

## 12. Estados De Una Alerta
Si el dominio usa alertas, define estados. Si no, define los estados principales de la entidad o flujo central del producto.

## 13. Casos De Uso Principales
Redacta los casos de uso principales en formato "Como [usuario], quiero [accion], para [beneficio]".

## 14. Requerimientos Funcionales Generales
Enumera requerimientos como:

### RF-01 [Nombre Del Requerimiento]
[Descripcion]

## 15. Requerimientos No Funcionales Generales
Enumera requerimientos como:

### RNF-01 [Nombre Del Requerimiento]
[Descripcion]

## 16. Restricciones Firmes Del Proyecto
Define restricciones de alcance, negocio, seguridad, integraciones, datos, cumplimiento o complejidad.

## 17. Metricas Simuladas Del Producto
Define metricas de aprendizaje, operacion, negocio o validacion local. No inventes metricas regulatorias o financieras reales si no aplican.

## 18. Criterios De Exito Del MVP
Lista criterios verificables que permitan decidir si el MVP cumple.

## 19. Flujo De Demostracion Esperado
Describe el flujo minimo demostrable de inicio a fin en bloque `text`.

## 20. Datos Ficticios Esperados
Lista los tipos de datos ficticios, semilla o simulados que el proyecto podra usar. Aclara que no deben usarse datos reales si aplica.

## 21. Dependencias Conceptuales Para PRD General
Lista las decisiones que debera resolver el PRD General posterior, sin resolverlas aqui.

## 22. Futuras Features Permitidas Solo Si No Rompen El Alcance
Lista posibles mejoras futuras que mantengan el alcance controlado.

## 23. Futuras Features Explicitamente No Permitidas
Lista features que no deben agregarse al roadmap del proyecto.

## 24. Riesgos De Alcance
Enumera riesgos y mitigaciones. Usa subsecciones como:

### Riesgo 1: [Nombre]
Mitigacion:
[Descripcion]

## 25. Principio Rector
Cierra con un principio rector claro para tomar decisiones futuras.

Validacion antes de terminar:
- Revisa que todas las secciones esten presentes.
- Revisa que el MVP sea pequeno, claro y completo.
- Revisa que el documento no incluya implementacion tecnica.
- Revisa que los RF y RNF tengan nomenclatura consistente.
- Revisa que futuras features y no objetivos no contradigan el alcance del MVP.