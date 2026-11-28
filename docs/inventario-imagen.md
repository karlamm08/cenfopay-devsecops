# Inventario y clasificación de vulnerabilidades del contenedor · CenfoPay (semana 2)

## 1. Inventario (SBOM)
- Archivo: `reportes/sbom-cyclonedx.json`   · Componentes totales: _512___
- Imagen base: ________________  · Digest: `sha256:________________`
- Sistema operativo y versión: __________  · Paquetes del SO: ____  · Librerías Python: ____

## 2. Clasificación (antes / después del endurecimiento)
| Origen | Severidad | Antes | Después | Con corrección | Sin corrección | En CISA KEV |
|--------|-----------|------:|--------:|---------------:|---------------:|------------:|
| SO (trivy-so) | CRITICAL | 235 | 2 | 0 | 2 |  |
| SO (trivy-so) | HIGH | 2599 | 99 | 0 | 99 | |
| Dependencias (trivy-dependencias) | CRITICAL | 1 | 1 | 1 | 0 |  |
| Dependencias (trivy-dependencias) | HIGH | 16 | 18 | 18 | 0 |  |
| Configuración (trivy-config / hadolint) | ALTA | ? | 0 | 0 | 0 | 0 |

## 3. Top 5 para priorizar
| CVE | Pieza | CVSS | EPSS | KEV | ¿Arreglo? | ¿Se usa? | Prioridad |
|-----|-------|-----:|-----:|-----|-----------|----------|-----------|
| CVE-2020-14343 | PyYAML 5.3.1 | 9.8 | 6.24 % | No | Sí (5.4) | Sí (`yaml.load` con datos de la petición, `__init__.py:95`) | Alta |
| CVE-2024-1135 | Gunicorn 20.0.4 | 7.5 | 2.996 % | No | Sí (22.0.0) | Sí | Alta |
| CVE-2023-30861 | Flask 2.0.3 | 7.5 | 1.25 % | No | Sí (2.3.2 / 2.2.5) | Sí | Alta |
| CVE-2024-34069 | Werkzeug 2.0.3 | 7.5 | 3.397 % | No | Sí (3.0.3) | Sí | Alta |
| CVE-2026-24049 | wheel 0.45.1 | 7.1 | 0.35 % | No | Sí (0.46.2) | No (herramienta de construcción, no está en requirements.txt) | Quitar |

## 4. Selección de la imagen base
| Criterio | Opción A | Opción B | Opción C |
|----------|----------|----------|----------|
| Imagen | `python:3.9` (actual) | `python:3.9-slim-bookworm` | `python:3.9-alpine` |
| Mantenida / con soporte | Python 3.9 sin soporte desde oct. 2025; base Debian completa | Python 3.9 sin soporte desde oct. 2025; base Debian 12 con parches de seguridad | Python 3.9 sin soporte desde oct. 2025; base Alpine |
| Tamaño (MB) | ~1470 | ~185 | ~80 |
| Vulnerabilidades C/H | 235 C / 2599 H (reporte «antes») | 9 C / 138 H | 0 C / 3 H |
| Tiene shell / gestor de paquetes | Sí (bash, apt y muchas herramientas extra) | Sí (shell y apt-get, sin las herramientas extra) | Sí (sh/busybox y apk) |
| Origen verificado (oficial, firmado) | Imagen oficial de Docker Hub | Imagen oficial de Docker Hub, fijada por digest sha256 | Imagen oficial de Docker Hub |
| Compatibilidad con la app | Sí (es la actual) | Sí: mismo ecosistema Debian/glibc, la app arrancó y respondió en el Paso 5 | Riesgo: usa musl en lugar de glibc, y `psycopg2-binary 2.9.3` y `PyYAML 5.3.1` pueden requerir compilarse. No se probó. |
| **Decisión y justificación** | Descartada: ~1,5 GB y cientos de paquetes que la app no usa, lo que da miles de fallas. | **Elegida:** reduce el tamaño ~8 veces y las fallas críticas de 235 a 9, mantiene el ecosistema Debian con el que la app ya funciona (probado en el Paso 5) y se fija por digest. | Descartada por ahora: es la más pequeña y con menos fallas, pero cambiar a musl arriesga las librerías heredadas. Se puede reevaluar en la semana 3, al actualizar Python y las dependencias. |

## 5. Verificación post-endurecimiento
Se ejecutó `bash scripts/verificar-endurecimiento.sh cenfopay:local cenfopay-app-1` sobre el contenedor real. Salida:
```
[i] Imagen: cenfopay:local
✅ END-01 La imagen define un usuario no root (USER)       USER=10001:10001
✅ END-02 Sin secretos en variables ENV de la imagen       ninguna variable sensible
✅ END-03 HEALTHCHECK definido                             presente
❌ END-04 Tamaño de la imagen <= 250 MB                    312 MB (superficie de ataque mayor)
❌ END-05 Sin herramientas innecesarias en runtime         encontradas: apt-get __sin_shell__
[i] Contenedor en ejecución: cenfopay-app-1
✅ END-06 Sistema de archivos raíz de solo lectura         read_only: true
✅ END-07 Capacidades Linux eliminadas (cap_drop ALL)      [ALL]
✅ END-08 no-new-privileges activo                         activo
✅ END-09 Contenedor no privilegiado                       privileged=false
✅ END-10 Puertos publicados solo en 127.0.0.1             127.0.0.1:8000
✅ END-11 El proceso corre con UID distinto de 0           uid=10001
✅ END-12 Límite de memoria definido                       256 MB
[✓] Resultados en reportes/endurecimiento.json
```
**Resultado:** 10 de 12 protecciones cumplidas.
**Corrección hecha:** en la primera verificación END-02 falló por `GPG_KEY`, una variable heredada de la imagen oficial de Python (clave pública para verificar la descarga de Python, no un secreto). Como la app no la usa, se vació en el Dockerfile con `ENV GPG_KEY=""` y la compuerta de seguridad dejó de bloquear.
**Las 2 que no pasan (aceptadas):**
- **END-04:** la imagen pesa 312 MB (límite 250 MB) por las librerías y los parches, pero es mucho menor que los ~1,5 GB originales. Riesgo bajo.
- **END-05:** `apt-get` viene incluido en la base slim. Riesgo bajo, porque el contenedor corre sin root y con el sistema de archivos de solo lectura.

**Resultado:** 9 de 12 protecciones cumplidas.
**Las 3 que no pasan (aceptadas):**
- **END-02:** `GPG_KEY` es la clave pública de la imagen oficial de Python, no un secreto. Falso positivo.
- **END-04:** la imagen pesa 312 MB (límite 250 MB) por las librerías y los parches, pero es mucho menor que los ~1 GB originales. Riesgo bajo.
- **END-05:** `apt-get` viene incluido en la base slim. Riesgo bajo, porque el contenedor corre sin root y con el sistema de archivos de solo lectura.