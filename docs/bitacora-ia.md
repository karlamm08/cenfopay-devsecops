# Bitácora de uso de inteligencia artificial · CenfoPay

> Requisito del proyecto (criterio 5 de la rúbrica): documenta **cada** aporte de una
> herramienta de IA (ChatGPT, Claude, Copilot, Gemini, DiálogoX, etc.) y **cómo lo verificaste**.
> Nunca pegues en una IA secretos, tokens ni datos reales: en este curso solo existen datos de prueba.

| # | Fecha | Herramienta de IA | Semana / tarea | Qué le pediste (resumen del prompt) | Qué aportó | Cómo lo verificaste (prueba, escaneo, documentación oficial) | ¿Lo usaste? ¿Qué cambiaste? |
|---|-------|-------------------|----------------|-------------------------------------|------------|---------------------------------------------------------------|-----------------------------|
| 1 |       |                   |                |                                     |            |                                                               |                             |
| # | Fecha | Herramienta de IA | Semana / tarea | Qué le pediste (resumen del prompt) | Qué aportó | Cómo lo verificaste (prueba, escaneo, documentación oficial) | ¿Lo usaste? ¿Qué cambiaste? |
|---|---|---|---|---|---|---|---|
| 1 | 14/11/2026 | ChatGPT | Semana 1 – Análisis de seguridad con OWASP ZAP | Interpretar las alertas del archivo JSON generado por OWASP ZAP. | Explicó en palabras sencillas los dos problemas encontrados: ausencia de protección en el formulario de inicio de sesión y falta de una protección adicional en las páginas de la aplicación. | Se revisó el JSON generado por OWASP ZAP y se comparó la explicación con las alertas reportadas. | Sí. Se utilizó la explicación para comprender los hallazgos y documentarlos en la bitácora. No se modificó el código. |
| 2 | 14/11/2026 | ChatGPT | Semana 1 – Análisis de seguridad con Semgrep | Interpretar los resultados del archivo JSON generado por Semgrep. | Identificó y explicó en lenguaje sencillo los problemas encontrados, como consultas SQL vulnerables, ejecución insegura de comandos, exposición de información sensible y configuraciones inseguras. | Se revisó el JSON generado por Semgrep y se contrastó la explicación con los hallazgos reportados. | Sí. Se utilizó la explicación para comprender los riesgos y documentar los resultados. No se modificó el código. |
| 3 | 21/11/2026 | Claude | Semana 2 – Compuerta de seguridad en rojo | La compuerta bloqueó el PR por END-02 (`GPG_KEY`). Pregunté cómo arreglarlo. | Explicó que `GPG_KEY` viene de la imagen base de Python y sugirió vaciarla en el Dockerfile. Eso arregló END-02, pero Trivy marcó DS-0031 en el Dockerfile. Entonces propuso registrar una excepción de falso positivo con motivo, responsable y vencimiento. | Corrí el verificador (END-02 pasó a ✅). Revisé el formato de la excepción en `politica-umbrales.yml` y la compuerta del PR pasó a verde. | Sí. Agregué la excepción en `politica-umbrales.yml` y en `umbrales-y-riesgo-residual.md`, puse mi nombre y la fecha de vencimiento (2026-12-15). |
| 4| 21/11/2026 | Claude | Semana 2 – Completar `docs/inventario-imagen.md` | Que me ayudara a construir las tablas del documento. | Armó las tablas y las explicó en palabras sencillas. | Comparé las tablas con mis reportes, el Dockerfile y el verificador. | Sí. Pegué las tablas y corregí los datos con mis números reales. |

## Reflexión final (5-10 líneas)
- ¿En qué te ayudó la IA y en qué se equivocó?
- ¿Qué aprendiste a verificar que antes darías por bueno?
