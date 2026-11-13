# Cambios aplicados al router InHand IG601PS08 y cómo revertirlos

Fecha: 2026-10-14
Equipo: InHand IG601PS08 (serie InGateway 600). Serial: GP6011511293474.
Firmware: InGateway 1.2.0.r3722 (inner 2817.3652.3722.3370.release, build 2015-08-10 UTC).
Bootloader: 1.1.3.r2777.

> Nota: las credenciales de administración del equipo no se registran en este
> archivo (son las de fábrica; consultar al propietario o el manual InHand).

## Acceso actual

- Web: http://192.168.2.1:8080 (usuario `adm`)
- Consola: telnet 192.168.2.1:23 (usuario `adm`; comando `enable` para modo privilegiado)
- SSH: NO disponible (ver cambio 3)

## Cambios aplicados (persistentes en el equipo)

### 1. IP de LAN (lan0): 192.168.1.1 → 192.168.2.1 / 255.255.255.0

- Motivo: alinear con la etiqueta/default de fábrica de la familia (192.168.2.1)
  y permitir acceso directo desde el PC (que tiene 192.168.2.3/24 en el cable).
- Método: página web `/setup-lan0.jsp` → POST a `apply.cgi`
  (`_service=lan0-restart,dhcpd-restart`, campo `lan0_ip=192.168.2.1`).
  Respuesta "InGateway -> Saved!"; verificado releyendo la página y por ping/ARP/web.
- Cómo revertir: misma página LAN, poner `lan0_ip=192.168.1.1`, guardar.
  (El PC debe estar en 192.168.1.0/24 o usar ruta temporal para llegar.)
  Alternativa: reset a valores de fábrica — la familia vuelve a LAN 192.168.2.1.

### 2. Pool DHCP: 192.168.1.2–192.168.1.254 → 192.168.2.2–192.168.2.254

- Motivo: coherencia con la nueva LAN.
- Método: página `/service-dhcpd.jsp` → POST a `apply.cgi`
  (`_service=dhcpd-restart`, `dhcpd_start=192.168.2.2`, `dhcpd_end=192.168.2.254`).
- Cómo revertir: misma página, volver a `192.168.1.2`–`192.168.1.254` (solo tiene
  sentido si se revierte también el cambio 1).

### 3. sshd_enable: se probó 1 y se revirtió a 0 (SSH no funciona en este firmware)

- Se habilitó `sshd_enable=1` (POST a `apply.cgi`, `_service=admin-restart`).
  El equipo respondió "Saved!" y el valor quedó en nvram, pero el puerto 22
  nunca abrió. Evidencia en el log del equipo (consola `show log`):
  `syswatcher: start service [SSH Server]` →
  `failed to execute dropbear! ret: -1, err: 2, No such file or directory` →
  `service [SSH Server] failed for too many times! is paused for 120s!`
  ⇒ el firmware NO incluye el binario `dropbear`; SSH es imposible.
- Se revirtió a `sshd_enable=0` (verificado) para no dejar un servicio fallando.
- No hay nada que revertir aquí: estado final = estado original (0).

## Estado del PC (host)

- NO se dejaron cambios permanentes. La interfaz del cable `enp0s31f6` quedó
  con su configuración preexistente `192.168.2.3/24` (ajena a esta operación).
- Se usaron ajustes temporales de sesión (IP /32 + ruta puntual) que fueron
  eliminados al terminar; verificado: sin restos, internet OK.

## Datos del equipo relevantes (solo lectura)

- MAC lan0: 00:18:05:06:E6:63. Modelo interno: `601PS08` (UI: IG601PS08).
- Módem celular: Siemens PHS8 (3G). SIM Entel (MCC/MNC 730-01), registrada.
- WAN1: dialup, APN `bam.entelpcs.cl`, trigger por datos (`trig_data=1`).
  El equipo reintenta el dial por sí solo (comportamiento propio, no activado
  por nosotros). Sin sesión PPP activa al momento del reporte.
- Reloj del equipo desajustado (~2031); sin ruta default actualmente.
