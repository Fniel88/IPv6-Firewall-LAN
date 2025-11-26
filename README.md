# Infraestructura de Red IPv6 con Servicios Centrales y Firewall Perimetral — GNS3
Infraestructura de Red IPv6 con Servicios Centrales y Firewall Perimetral — GNS3 Proyecto académico — Implementación multi-sede con OSPFv3, pfSense, DHCPv6 y servidor HTTP (nginx)  Este repositorio documenta la construcción de una infraestructura de red IPv6 corporativa simulada en GNS3, diseñada para integrar en múltiples sedes.

1. Finalidad del proyecto

El objetivo principal es simular una red corporativa real que utilice exclusivamente IPv6, implementando:

- Enrutamiento dinámico entre sedes con OSPFv3
- Firewall por departamentos / VLAN con pfSense
- Distribución automática de direcciones con DHCPv6 + RA
- Un servidor HTTP (nginx) accesible sólo para las VLAN autorizadas
- Políticas de acceso entre departamentos
- Pruebas completas de conectividad y servicios

Esto permite comprender cómo se interconectan sedes, cómo se distribuyen servicios centrales y cómo se controla el tráfico en un entorno moderno IPv6.

2. Herramientas utilizadas

- GNS3:	Simuldaor graficos de redes y de codigo abierto.
- Routers Cisco (IOS) : Routers Virtuales/Fisicos reconocidos a nivel mundial por su compatibilidad y facilidad de uso. (Se adjuntan para su descarga)
- pfSense: Firewall Fisico/Virtual amigable y completo en su version Community (Codigo abierto)
- Ubuntu Server: Servidor multiproposito con OS Ubuntu que permite el desplieguie de diferentes aplicativos.
- Linux Mint: Equipos Fisicos/Virtuales de bajo consumo.
- PDF + TXT : Documentos incluidos con la documentacion del proyecto y codigo para su ejecuccion.

3. Topología general

Incluye sedes: Bogotá (CORE), Barranquilla, Cali, Medellín
Cada sede utiliza un pfSense y tres VLAN:

VLAN 101 — MINTIC
VLAN 102 — MINAGRICULTURA
VLAN 103 — MINEXTERIOR

Bloque principal IPv6 usado:

2001:db8:acad::/48

Los enlaces entre sedes usan prefijos /64 dedicados (ej: FF01, FF02, FF03, FF04).

*La imagen de la topología se incluye en la carpeta del repositorio.*

4. Puntos a tener en cuenta:

Estos son los elementos fundamentales que garantizan que este proyecto funcione igual que en una red real:

- Activar obligatoriamente IPv6 Routing en los routers Cisco
ipv6 unicast-routing

- Definir Router-ID único por sede en OSPFv3

Sin esto no forma vecinos.

- Habilitar DHCPv6 Server + Router Advertisements en pfSense

RA Mode: Managed o Assisted

Prefijos de cada VLAN definidos correctamente

- Ajustes esenciales en pfSense

Habilitar Allow IPv6

Activar IPv6 forwarding

Reglas de LAN deben permitir tráfico IPv6

Reglas de WAN deben permitir ICMPv6 para diagnóstico

- DNS Resolver con Host Override

Para el dominio:

mintic.localdomain → 2001:db8:acad:FF04::2

- Nginx accesible solo desde VLAN permitidas

Esto exige reglas específicas en pfSense, ejemplo:

Permitir HTTP desde MINTIC

Denegar tráfico desde MINAGRICULTURA o MINEXTERIOR según la política

- Tabla de rutas IPv6 verificable en todas las sedes

Comando clave:

show ipv6 route

- Conectividad extremo a extremo

Ejemplos:

ping6 2001:db8:acad:FF04::2
curl -6 http://mintic.localdomain

5. Pasos generales de implementación
A. Configurar routers Cisco (IPv6 + OSPFv3 + rutas)

- Habilitar IPv6 routing
- Asignar direcciones IPv6 a cada interfaz
- Activar OSPFv3 en cada enlace WAN
- Configurar rutas estáticas necesarias en redes LAN
- Verificar vecinos OSPFv3

B. Configuraraciones en cada pfSense

- Interfaces: WAN ↔ Router local / LAN ↔ VLANs
- DHCPv6 + RA por VLAN
- DNS Resolver
- Reglas de Firewall estructuradas
- Gateway IPv6 correcto por sede
- Registro de logs de DHCPv6, Firewall y DNS

C. Configurar Ubuntu Server (nginx)

- netplan con dirección IPv6 fija
- nginx activo
-Index.html personalizado (Se adjunta)
- Permisos ufw para HTTP

D. Clientes finales (Linux Mint)

Obtener IPv6 por DHCPv6
Resolución DNS probada
Acceso HTTP según las reglas entre VLANs

6. Archivos incluidos en este repositorio

comandos_PYF_IPv6.txt — Son los comandos de routers, pfSense y servidores

Infraestructura de Red IPv6 - Es el Informe donde se encuentran las pruebas, capturas y validaciones.

topologia.png — Es la imagen de la topología completa

index.html — archivo usado por nginx

7. Pruebas necesarias para validar el proyecto
Conectividad básica
ping6 <gateway LAN>
ping6 <router sede>
ping6 <servidor HTTP>

Resolución del DNS interno
ping6 mintic.localdomain

Acceso HTTP
curl -6 http://mintic.localdomain

Ver OSPFv3
show ipv6 ospf neighbor
show ipv6 route ospf

Ver DHCPv6 en pfSense
Logs DHCPv6
Lease activo para cada cliente en el pfsense

8. Conclusiones

El enrutamiento IPv6 se propagó correctamente entre sedes mediante OSPFv3, validado en todas las tablas de rutas.
pfSense demostró ser una solución completa para DHCPv6, RA, DNS y control por VLAN.
Las políticas entre departamentos se cumplieron según lo previsto.
El servidor nginx fue accesible solo desde las VLAN habilitadas, evidenciando el correcto funcionamiento del firewall.
Toda la infraestructura es escalable y permite añadir nuevas sedes o servicios sin alterar la arquitectura base.

Gracias por revisar ;)
