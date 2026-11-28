# Registro de triage de hallazgos · CenfoPay

> Una fila por hallazgo revisado. La **huella** es la columna `huella` de
> `reportes/hallazgos-normalizados.csv` (la genera `tools/security_gate.py`).
> Decisión: **VP** verdadero positivo · **FP** falso positivo · **RA** riesgo aceptado (con excepción y vencimiento).

| # | Huella | Herramienta | Regla / CVE | Ubicación | Severidad | Decisión | Justificación (contexto, explotabilidad, evidencia) | Acción | Responsable | Fecha |
|---|--------|-------------|-------------|-----------|-----------|----------|------------------------------------------------------|--------|-------------|-------|
| 1 | pendiente | Bandit | B602 | app/cenfopay/__init__.py:86 (resolver) | HIGH | VP | El usuario controla `host` y llega a un comando con `shell=True`. Se usa de verdad y no había otra protección. | Corregido: lista blanca y `socket`. Prueba: test_resolver_no_ejecuta_comandos | karlamm08 | 2026-11-27 |
| 2 | pendiente | Bandit | B608 | app/cenfopay/__init__.py:57 (login) | MEDIUM | VP | El usuario controla `usuario` y va pegado dentro del SQL. Bandit tenía confianza baja, pero Semgrep también lo marcó y se puede explotar. | Corregido: consulta con `?`. Prueba: test_login_resiste_inyeccion_sql | karlamm08 | 2026-11-27 |
| 3 | pendiente | Bandit | B506 | app/cenfopay/__init__.py:95 (importar) | MEDIUM | VP | El cuerpo de la petición lo controla el usuario y `yaml.Loader` puede crear objetos de Python. | Corregido: `yaml.safe_load`. Prueba: test_importar_rechaza_objetos_python | karlamm08 | 2026-11-27 |
| 4 | pendiente | Bandit | B324 | app/cenfopay/__init__.py:54 (login) | HIGH | VP | MD5 se usaba para guardar contraseñas y es fácil de descifrar. | Corregido: scrypt en auth.py | karlamm08 | 2026-11-27 |
| 5 | pendiente | Bandit | B105 | app/cenfopay/config.py:10 | LOW | VP | La `SECRET_KEY` está escrita en el código, así que cualquiera con acceso al repositorio la ve. | Pendiente: se corrige en la semana 4 (falla test_secret_key_se_inyecta_desde_el_entorno) | karlamm08 | 2026-11-27 |
| 6 | pendiente | Semgrep | cenfopay-path-traversal | app/cenfopay/__init__.py:105 (reportes) | ERROR | VP | El nombre del reporte lo escribe el usuario. Con `../../` podía leer archivos de otras carpetas. | Corregido: lista de nombres permitidos y `safe_join`. Prueba: test_reportes_no_permite_path_traversal | karlamm08 | 2026-11-27 |
| 7 | pendiente | Semgrep | cenfopay-secreto-en-logs | app/cenfopay/__init__.py:52 (login) | ERROR | VP | La contraseña se escribía en los registros, y los logs se copian a respaldos y consolas. | Corregido: ahora el log solo guarda el usuario | karlamm08 | 2026-11-27 |

## Criterios de decisión usados
- **FP**: la regla dispara pero el flujo de datos no es explotable en este contexto (explica por qué).
- **RA**: es real, pero se acepta temporalmente: existe un control compensatorio y una fecha de vencimiento.
- Todo FP o RA se refleja en el código (`# nosemgrep: <regla>` / `# nosec <id>`) **o** en
  `security/politica-umbrales.yml > excepciones`, nunca solo en este documento.

## Paso 1 · Comparación de reglas generales vs. propias

| Revisión | Reglas | Hallazgos |
|---|---|---|
| Reglas generales (p/python) | 151 | 10 |
| Reglas propias de CenfoPay | 6 | 9 |

### Solo las reglas generales lo encontraron
- MD5 como hash de contraseñas (`__init__.py`, línea 54).
- Inyección de comandos con `subprocess` y `shell=True` (líneas 85-86).
- YAML inseguro con `yaml.load` (línea 95).
- Servidor expuesto con `host="0.0.0.0"` (`run.py`, línea 9).

### Solo las reglas propias lo encontraron
- Inyección SQL (líneas 60 y 74): las generales no conocen `db.consultar`.
- Recorrido de rutas (línea 105).
- Contraseña escrita en los logs (línea 52).
- Secretos escritos en el código (`config.py`).
- Depurador activo con `debug=True` (`run.py`, línea 9).

### Ambas lo encontraron
- Inyección de plantillas / XSS en `render_template_string` (línea 44).

### Conclusión
Las dos revisiones se complementan: las generales cubren errores comunes de Python y las propias cubren lo específico de la aplicación. Se necesitan ambas.