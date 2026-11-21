# Justificación de umbrales de bloqueo y riesgos residuales · CenfoPay

> Este documento acompaña a `security/politica-umbrales.yml`. Cada número de la
> política debe tener aquí su razón. Se evalúa en el criterio 4 de la rúbrica del proyecto.

## 1. Contexto de riesgo de la aplicación
- Tipo de datos que maneja (ficticios en el curso, pero tratados como si fueran reales):La aplicación CenfoPay simula una plataforma de pagos que maneja información ficticia de usuarios, credenciales de acceso y datos relacionados con operaciones de pago. Aunque los datos son de prueba, se deben proteger como si fueran reales.
- Exposición (Internet / red interna / solo laboratorio):Actualmente, la aplicación se utiliza en un entorno de laboratorio académico. No está destinada a estar expuesta públicamente en Internet.
- Marco de referencia aplicable (p. ej. PCI-DSS 6.2-6.3, Ley 8968, normativa SUGEF de TI):se considera la Ley 8968 de Costa Rica como referencia para la protección de datos personales.

## 2. Umbrales por herramienta
| Herramienta | Severidad | Umbral | Modo | Por qué este número (impacto, explotabilidad, ruido, costo de bloquear) |
|-------------|-----------|-------:|------|--------------------------------------------------------------------------|
| semgrep     | ERROR     | 5      |bloquear|  Se permiten los 5 errores que ya existen para evitar bloquear el proyecto por problemas anteriores. No se deben agregar errores nuevos.                                                                        |
| zap         | High      | 0      |bloquear|  No se permiten alertas de nivel alto porque pueden representar riesgos importantes para la seguridad de la aplicación.                                                                        |
| zap         | Medium    | 3      |bloquear|  

Se permiten las 3 alertas actuales mientras se trabaja en corregirlas. No se deben agregar alertas nuevas de este nivel.                                                                        |
| hadolint    | error     |        |      |                                                                          |
| trivy-so    | CRITICAL / HIGH |  |      |                                                                          |
| trivy-dependencias | CRITICAL / HIGH | |  |                                                                          |
| bandit      | HIGH      |        |      |                                                                          |
| gitleaks    | SECRETO   | 0      |      |                                                                          |
| endurecimiento | ALTA   |        |      |                                                                          |
| registros   | ALTA      | 0      |      |                                                                          |

## 3. Disparadores
¡Sí! Esa es la tabla de la sección 3. Disparadores de tu documento. Debes completar las dos columnas vacías: Qué corre y Por qué.

Puedes colocar lo siguiente:

| Evento                | Qué corre                         | Por qué                                                                   |
| --------------------- | --------------------------------- | ------------------------------------------------------------------------- |
| `pull_request a main` | Pipeline de seguridad y pruebas.  | Revisar los cambios antes de integrarlos a main.                          |
| `push a main`         | Pipeline de seguridad y pruebas.  | Verificar que los cambios subidos a main no introduzcan vulnerabilidades. |
| `schedule semanal`    | Análisis de seguridad programado. | Detectar posibles vulnerabilidades de forma periódica.                    |
| `workflow_dispatch`   | Pipeline ejecutado manualmente.   | Permitir realizar análisis cuando sea necesario.                          |


## 4. Riesgos residuales (lo que queda abierto después de corregir)
| # | Riesgo | Origen (hallazgo/huella) | Por qué no se corrige ahora | Control compensatorio | Dueño | Revisión |
|---|--------|--------------------------|-----------------------------|-----------------------|-------|----------|
| 1 | | | | | | |

## 5. Excepciones aprobadas
| Herramienta | ID | Justificación | Responsable | Vence |
|-------------|----|---------------|-------------|-------|
| | | | | |
