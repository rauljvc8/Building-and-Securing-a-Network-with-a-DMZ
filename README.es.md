# 🛡️ Construyendo y Asegurando una Red con una Zona Desmilitarizada (DMZ)

**Laboratorio de Seguridad en Redes · Cisco Packet Tracer · 4Geeks Academy**
Estudiante: Raúl Velásquez · Mayo 2025

## 📋 Descripción

Implementación de una arquitectura de red segmentada con una Zona Desmilitarizada (DMZ) en Cisco Packet Tracer, aplicando principios de ciberseguridad defensiva: aislamiento de servicios críticos, exposición controlada mediante NAT, y control de tráfico entre zonas con Listas de Control de Acceso (ACLs).

---

## 🎯 Objetivos

- ✅ Aislar servicios críticos en una DMZ separada de la red interna (LAN)
- ✅ Configurar NAT estático para exponer el servidor web de la DMZ hacia Internet de forma controlada
- ✅ Implementar ACLs que simulen un firewall, permitiendo solo el tráfico legítimo
- ✅ Validar la configuración mediante pruebas de conectividad, acceso HTTP y verificación de bloqueos

---

## 🗺️ Topología de Red

Router central (`Router_FW`, Cisco ISR 2911) con tres interfaces, cada una conectada a un switch (Cisco 2960) que representa una zona:

| Zona | Interfaz Router | Dispositivo final |
|---|---|---|
| LAN interna | GigabitEthernet0/0 | PC_Internal |
| DMZ | GigabitEthernet0/1 | Web_DMZ (Server-PT) |
| Externa (Internet simulado) | GigabitEthernet0/2 | PC_External |

---

## 🔢 Plan de Direccionamiento IP

| Dispositivo | IP | Máscara | Gateway | Zona |
|---|---|---|---|---|
| Router_FW (GE0/0) | 192.168.1.1 | /24 | — | LAN |
| Router_FW (GE0/1) | 192.168.2.1 | /24 | — | DMZ |
| Router_FW (GE0/2) | 192.168.3.1 | /24 | — | Externa |
| PC_Internal | 192.168.1.10 | /24 | 192.168.1.1 | LAN |
| Web_DMZ | 192.168.2.10 | /24 | 192.168.2.1 | DMZ |
| PC_External | 192.168.3.10 | /24 | 192.168.3.1 | Externa |

---

## ⚙️ Configuración Implementada

**NAT estático** — expone el servidor de la DMZ (192.168.2.10) hacia la red externa a través de la IP pública 192.168.3.1, sin revelar la IP privada real:

```
interface GigabitEthernet0/1
 ip nat inside
interface GigabitEthernet0/2
 ip nat outside
ip nat inside source static 192.168.2.10 192.168.3.1
```

**ACLs extendidas** — tres listas de control de acceso que replican el comportamiento de un firewall entre zonas:

| ACL | Aplicada en | Regla principal |
|---|---|---|
| `ACL_EXTERNAL_IN` | GE0/2 (entrada externa) | Permite TCP hacia 192.168.3.1:80; deniega todo lo demás |
| `BLOQUEAR_DMZ_A_LAN` | GE0/1 (entrada DMZ) | Permite tráfico establecido DMZ→LAN; bloquea nuevas conexiones DMZ→LAN |
| `ACL_LAN_OUT` | GE0/0 (salida LAN) | Permite tráfico saliente desde la LAN interna |

---

## 🧪 Pruebas de Validación

| Prueba | Resultado |
|---|---|
| Conectividad PC_Internal → Gateway LAN | ✅ Exitoso (0% pérdida) |
| Conectividad Web_DMZ → Gateway DMZ | ✅ Exitoso (0% pérdida) |
| Conectividad PC_External → Gateway Externa | ✅ Exitoso (0% pérdida) |
| Acceso HTTP externo vía NAT (`http://192.168.3.1`) | ✅ Exitoso |
| Acceso HTTP interno directo (`http://192.168.2.10`) | ✅ Exitoso |
| Ping ICMP externo → 192.168.3.1 | 🚫 Bloqueado (ACL_EXTERNAL_IN solo permite TCP/80) |
| Ping DMZ → LAN (Web_DMZ → 192.168.1.10) | 🚫 Bloqueado (BLOQUEAR_DMZ_A_LAN) |

La prueba más crítica del laboratorio es el bloqueo DMZ→LAN: confirma que, aunque la DMZ esté comprometida, no puede usarse como puente hacia la red interna.

---

## ✅ Conclusiones

- **Segmentación efectiva**: separar la red en tres interfaces (LAN, DMZ, Externa) reduce la superficie de ataque al aislar los servicios públicos de la red interna.
- **NAT como exposición controlada**: publica el servicio web sin revelar la IP privada real del servidor.
- **ACLs como firewall de capa 3/4**: permiten políticas granulares de tráfico entre zonas.
- **Arquitectura validada**: todo el tráfico no autorizado (ICMP externo, DMZ→LAN) fue bloqueado, mientras el servicio legítimo (HTTP vía NAT) funcionó correctamente.

---

## 📁 Contenido del Repositorio

| Archivo | Descripción |
|---|---|
| `Informe_DMZ_Raul_Velasquez.pdf` | 📄 Informe técnico completo (15 páginas) con configuración paso a paso y evidencias |
| `DMZ_PROJECT-hecho.pka` | 🖧 Archivo de topología completo en Cisco Packet Tracer |
| `assets/` | 📸 Capturas de pantalla y recursos del laboratorio |

---
Proyecto académico — Programa de Ciberseguridad, 4Geeks Academy
