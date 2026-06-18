# Metodología y Explicación del Análisis

## Parte 1: Desglose del registro DNS e ICMP

* **Uso de Protocolos:**
    * **Hallazgo:** El protocolo UDP se utilizó para contactar al servidor DNS. El protocolo ICMP se utilizó para devolver el mensaje de error.
    * **Explicación:** El escenario de captura de `tcpdump` registra paquetes UDP desde el equipo de origen hacia la IP y el puerto del servidor DNS (`203.0.113.2.domain`). A su vez, registra las respuestas de error ICMP devueltas al equipo.

* **Análisis de Puertos y Banderas:**
    * **Hallazgo:** El error específico es `udp port 53 unreachable`. Las banderas registradas incluyen un signo `+` y el símbolo `A?`.
    * **Explicación:** El puerto 53 está reservado para el servicio DNS. El signo `+` después del ID de consulta (`35084`) indica banderas en el mensaje UDP, mientras que `A?` refleja una solicitud DNS para mapear un nombre de dominio a una dirección IP (Registro A).

* **Interpretación del Fallo:**
    * **Hallazgo:** El servidor DNS no está respondiendo.
    * **Explicación:** La inaccesibilidad del puerto 53 confirma una caída o bloqueo del servicio. Esto impide la resolución de nombres, lo que detiene cualquier intento posterior de conexión web.

## Parte 2: Análisis de Datos y Causa Raíz

* **Cronología y Síntomas:**
    * **Hallazgo:** Incidente reportado a la 1:24 p.m. con errores de "puerto de destino inalcanzable" reportados por los clientes.
    * **Explicación:** La hora exacta se extrae de las marcas de tiempo del registro (`13:24:32.192571`). Los síntomas iniciales provienen del reporte directo de los usuarios al intentar cargar la página web.

* **Estado de la Investigación:**
    * **Hallazgo:** Se comprobó mediante *packet sniffing* con `tcpdump` que el puerto 53 es inalcanzable en la capa de red.
    * **Explicación:** La investigación reprodujo el error del usuario y aisló el fallo técnico, demostrando que el problema no reside en el servidor web (puerto 80/443), sino en la infraestructura de resolución de nombres previa a la conexión HTTPS.

* **Siguientes Pasos y Causa Raíz Probable:**
    * **Hallazgo:** Se requiere verificar si el servidor DNS está caído por un ataque DoS o si el puerto 53 fue bloqueado por una configuración de firewall.
    * **Explicación:** Un ataque de Denegación de Servicio (DoS) busca inundar un dispositivo de red para colapsarlo. Si el servidor está operativo, la otra causa técnica probable es un cambio manual o automático en las reglas perimetrales del firewall que impide el paso del tráfico.
