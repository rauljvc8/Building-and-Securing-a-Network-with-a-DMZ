Construyendo y Asegurando una Red con una Zona Desmilitarizada (DMZ)

Laboratorio de Seguridad en Redes · Cisco Packet Tracer · 4Geeks Academy
Estudiante: Raúl Velásquez · 

Descripción

Implementación de una arquitectura de red segmentada con una Zona Desmilitarizada (DMZ) en Cisco Packet Tracer, aplicando principios de ciberseguridad defensiva: aislamiento de servicios críticos, exposición controlada mediante NAT, y control de tráfico entre zonas con Listas de Control de Acceso (ACLs).

Objetivos


Aislar servicios críticos en una DMZ separada de la red interna (LAN)
Configurar NAT estático para exponer el servidor web de la DMZ hacia Internet de forma controlada
Implementar ACLs que simulen un firewall, permitiendo solo el tráfico legítimo
Validar la configuración mediante pruebas de conectividad, acceso HTTP y verificación de bloqueos


Topología de red

Router central (Router_FW, Cisco ISR 2911) con tres interfaces, cada una conectada a un switch (Cisco 2960) que representa una zona:

ZonaInterfaz RouterDispositivo finalLAN internaGigabitEthernet0/0PC_InternalDMZGigabitEthernet0/1Web_DMZ (Server-PT)Externa (Internet simulado)GigabitEthernet0/2PC_External

Plan de direccionamiento IP

DispositivoIPMáscaraGatewayZonaRouter_FW (GE0/0)192.168.1.1/24—LANRouter_FW (GE0/1)192.168.2.1/24—DMZRouter_FW (GE0/2)192.168.3.1/24—ExternaPC_Internal192.168.1.10/24192.168.1.1LANWeb_DMZ192.168.2.10/24192.168.2.1DMZPC_External192.168.3.10/24192.168.3.1Externa

Configuración implementada

NAT estático — expone el servidor de la DMZ (192.168.2.10) hacia la red externa a través de la IP pública 192.168.3.1, sin revelar la IP privada real:

interface GigabitEthernet0/1
 ip nat inside
interface GigabitEthernet0/2
 ip nat outside
ip nat inside source static 192.168.2.10 192.168.3.1

ACLs extendidas — tres listas de control de acceso que replican el comportamiento de un firewall entre zonas:

ACLAplicada enRegla principalACL_EXTERNAL_INGE0/2 (entrada externa)Permite TCP hacia 192.168.3.1:80; deniega todo lo demásBLOQUEAR_DMZ_A_LANGE0/1 (entrada DMZ)Permite tráfico establecido DMZ→LAN; bloquea nuevas conexiones DMZ→LANACL_LAN_OUTGE0/0 (salida LAN)Permite tráfico saliente desde la LAN interna

Pruebas de validación

PruebaResultadoConectividad PC_Internal → Gateway LAN✅ Exitoso (0% pérdida)Conectividad Web_DMZ → Gateway DMZ✅ Exitoso (0% pérdida)Conectividad PC_External → Gateway Externa✅ Exitoso (0% pérdida)Acceso HTTP externo vía NAT (http://192.168.3.1)✅ ExitosoAcceso HTTP interno directo (http://192.168.2.10)✅ ExitosoPing ICMP externo → 192.168.3.1🚫 Bloqueado (ACL_EXTERNAL_IN solo permite TCP/80)Ping DMZ → LAN (Web_DMZ → 192.168.1.10)🚫 Bloqueado (BLOQUEAR_DMZ_A_LAN)

La prueba más crítica del laboratorio es el bloqueo DMZ→LAN: confirma que, aunque la DMZ esté comprometida, no puede usarse como puente hacia la red interna.

Conclusiones


Segmentación efectiva: separar la red en tres interfaces (LAN, DMZ, Externa) reduce la superficie de ataque al aislar los servicios públicos de la red interna.
NAT como exposición controlada: publica el servicio web sin revelar la IP privada real del servidor.
ACLs como firewall de capa 3/4: permiten políticas granulares de tráfico entre zonas.
Arquitectura validada: todo el tráfico no autorizado (ICMP externo, DMZ→LAN) fue bloqueado, mientras el servicio legítimo (HTTP vía NAT) funcionó correctamente.


Contenido del repositorio

ArchivoDescripciónInforme_DMZ_Raul_Velasquez.pdfInforme técnico completo (15 páginas) con configuración paso a paso y evidenciasDMZ_PROJECT-hecho.pkaArchivo de topología completo en Cisco Packet Tracerassets/Capturas de pantalla y recursos del laboratorio


Proyecto académico — Programa de Ciberseguridad, 4Geeks Academy
