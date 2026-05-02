# Política de seguridad

## Versiones soportadas

Mientras el proyecto esté en desarrollo activo (pre-1.0 estable), solo la última versión recibe parches de seguridad.

| Versión | Soporte |
|---------|---------|
| 2.x     | ✅      |
| 1.x (TFG original) | ❌ archivado |

## Reportar una vulnerabilidad

**No abras issues públicas para vulnerabilidades de seguridad.**

Envía un email a **nando.sr@gmail.com** con:

1. Descripción de la vulnerabilidad
2. Componente afectado (firmware / backend / web / mobile / infra)
3. Pasos para reproducir
4. Impacto estimado (confidencialidad / integridad / disponibilidad)
5. PoC si la tienes
6. Sugerencia de fix si aplica

Recibirás respuesta en ≤ 72 h. Si la vulnerabilidad se confirma:

- Acuerdo de timeline para parche y disclosure (típicamente 30-90 días)
- Crédito público en el `CHANGELOG.md` si lo deseas
- Reconocimiento en el `SECURITY.md` (sección "Hall of Fame")

## Áreas particularmente sensibles

- **Firmware**: bypass de Secure Boot, fugas de clave privada del device, escalada vía OTA
- **MQTT**: bypass de ACL, lectura de topics de otra org
- **API**: bypass de RBAC, escalada de role, escape de tenant
- **Auth**: token forgery, bypass de refresh rotation
- **OTA**: aceptación de firmware sin firma válida
- **PCB**: side channels en Flash Encryption, glitching del bootloader

## Compromisos

- No persiguimos legalmente a investigadores de buena fe que sigan este proceso.
- No exigimos NDA para reportar.
- Publicamos un advisory en GitHub Security Advisories tras el parche.

## Hall of Fame

_Vacío por ahora. Sé el primero._
