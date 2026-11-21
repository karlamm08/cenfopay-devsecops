# Inventario y clasificación de vulnerabilidades del contenedor · CenfoPay (semana 2)

## 1. Inventario (SBOM)
- Archivo: `reportes/sbom-cyclonedx.json`   · Componentes totales: 512 piezas____
- Imagen base: ________________  · Digest: `sha256:________________`
- Sistema operativo y versión: __________  · Paquetes del SO: ____  · Librerías Python: ____

## 2. Clasificación (antes / después del endurecimiento)
| Origen | Severidad | Antes | Después | Con corrección | Sin corrección | En CISA KEV |
|--------|-----------|------:|--------:|---------------:|---------------:|------------:|
| SO (trivy-so) | CRITICAL | 235| | | | |
| SO (trivy-so) | HIGH |2599| | | | |
| Dependencias (trivy-dependencias) | CRITICAL |1| | | | |
| Dependencias (trivy-dependencias) | HIGH |16| | | | |
| Configuración (trivy-config / hadolint) | ALTA | | | | | |

## 3. Top 5 para priorizar
| CVE | Paquete | CVSS | EPSS | ¿KEV? | ¿Se usa la función vulnerable? | Prioridad |
|-----|---------|------|------|-------|--------------------------------|-----------|
| CVE-2020-14343 | PyYAML 5.3.1 | 9.8 High| 6.24%| No |No| Alta|
| CVE-2024-1135 | Gunicorn 20.0.4 | 7.5 high | 2.996% |No|No |Alta |
| CVE-2023-30861 | Flask 2.0.3 | 7.5 High | 1.25% | No|No | Alta |
| CVE-2024-34069 | Werkzeug 2.0.3 | 7.5 High | 3.397% | No|No |Alta|
| CVE-2026-24049 | wheel 0.45.1 | 7.1 High | 0.35% | No|No |Alta|

## 4. Selección de la imagen base
| Criterio | Opción A | Opción B | Opción C |
|----------|----------|----------|----------|
| Imagen | `python:3.9` (actual) | `python:3.9-slim-bookworm` | `python:3.9-alpine` |
| Mantenida / con soporte | No. Python 3.9 está fuera de soporte | No. Python 3.9 está fuera de soporte; Debian Bookworm tiene mantenimiento | No. Python 3.9 está fuera de soporte |
| Tamaño (MB) | Aproximadamente 386 MB | Aproximadamente 45 MB | Aproximadamente 19 MB |
| Vulnerabilidades C/H | Por verificar con escaneo | Por verificar con escaneo | Por verificar con escaneo |
| Tiene shell / gestor de paquetes | Sí: shell y `apt` | Sí: shell y `apt` | Sí: shell y `apk` |
| Origen verificado (oficial, firmado) | Imagen oficial de Docker; verificar digest y firma | Imagen oficial de Docker; verificar digest y firma | Imagen oficial de Docker; verificar digest y firma |
| Compatibilidad con la app | Alta, es la imagen actual | Alta, utiliza Debian y conserva Python 3.9 | Por validar; Alpine utiliza musl y algunas dependencias pueden requerir ajustes |
| **Decisión y justificación** | No seleccionada. Es una imagen completa y contiene más componentes de los necesarios. | **Seleccionada.** Reduce el tamaño y los componentes innecesarios, conserva compatibilidad con las librerías actuales y utiliza Debian Bookworm. | No seleccionada. Aunque es más pequeña, puede presentar problemas de compatibilidad con dependencias existentes. |

## 5. Verificación post-endurecimiento
Pega aquí la salida de `bash scripts/verificar-endurecimiento.sh cenfopay:local cenfopay-app-1`.
encontradas: apt-get __sin_shell__