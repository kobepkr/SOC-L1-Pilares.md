# Reporte de Triaje - Alerta #002

## Resumen de la Alerta

| Campo | Valor |
|-------|-------|
| **Fuente** | SIEM (Splunk) |
| **Nombre de la regla** | Brute Force Detection - Windows |
| **Severidad inicial** | High |
| **Fecha/Hora** | 2026-10-08 03:14:22 UTC |
| **Analista** | Victor Labbe (SOC L1) |

---

## Las 5 W's

### Who (Quién)
- **Usuario afectado:** `administrador` (cuenta privilegiada)
- **Origen del ataque:** IP externa `185.220.101.45`

### What (Qué)
- Se detectaron **47 intentos de inicio de sesión fallidos** (Event ID 4625) en un período de **2 minutos**.
- Todos los intentos fueron contra la misma cuenta de usuario.
- El patrón es consistente con un **ataque de fuerza bruta**.

### When (Cuándo)
- **Inicio:** 2026-10-08 03:12:10 UTC
- **Fin:** 2026-10-08 03:14:22 UTC
- **Duración:** ~2 minutos

### Where (Dónde)
- **Activo objetivo:** `SRV-DC-01` (10.0.0.5) — Controlador de dominio
- **IP origen:** `185.220.101.45` (externa)
- **Puerto destino:** 3389 (RDP)

### Why (Por qué - Razonamiento)
- El volumen de intentos (47 en 2 minutos) es anormal.
- La IP origen es externa y no pertenece a la organización.
- La cuenta objetivo es `administrador`, una cuenta privilegiada.
- **Conclusión:** Verdadero Positivo — Ataque de fuerza bruta en curso.

---

## Evidencia Recolectada

| Evidencia | Resultado |
|-----------|-----------|
| **Event ID 4625** | 47 eventos en 2 minutos |
| **IP 185.220.101.45 en VirusTotal** | Marcada como maliciosa por 12 vendors |
| **IP en AbuseIPDB** | Reportada 89 veces en los últimos 30 días |
| **Usuario `administrador`** | Cuenta privilegiada activa |
| **Puerto 3389 (RDP)** | Expuesto a Internet (mal configurado) |

---

## Veredicto y Acción

| Campo | Resultado |
|-------|-----------|
| **Clasificación** | 🔴 Verdadero Positivo (Ataque real) |
| **Justificación** | Múltiples intentos fallidos desde IP maliciosa conocida contra cuenta privilegiada |
| **Acción tomada** | 1. Escalar a L2 con toda la evidencia <br> 2. Bloquear IP `185.220.101.45` en el firewall <br> 3. Notificar al equipo de infraestructura sobre RDP expuesto |
| **Prioridad de escalado** | 🔴 Alta — Ataque en curso contra activo crítico |

---

## Recomendaciones

1. **Inmediato:** Bloquear la IP origen en el firewall perimetral.
2. **Corto plazo:** Cerrar el puerto RDP (3389) a Internet; usar VPN para acceso remoto.
3. **Mediano plazo:** Implementar autenticación multifactor (MFA) para cuentas privilegiadas.
4. **Largo plazo:** Configurar bloqueo automático de cuentas tras N intentos fallidos.

---

## Notas del Analista

- El ataque no tuvo éxito (no hubo Event ID 4624 exitoso posterior).
- La cuenta `administrador` no fue comprometida.
- Se recomienda revisar si hay otros activos con RDP expuesto.

---

**Fecha del reporte:** 2026-10-08  
**Analista:** Victor Labbe  
**Estado:** Escalado a L2
