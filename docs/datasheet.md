# Datasheet · InHand Router

## 1. Qué es

Registro de una intervención sobre un router industrial **InHand IG601PS08** con
firmware **InGateway 1.2.0.r3722**. El trabajo consistió en mover la LAN de
`192.168.1.1` a `192.168.2.1`, ajustar el pool DHCP e intentar habilitar SSH. Como
la imagen no incluía `dropbear`, el acceso SSH no se habilitó y la configuración se
revirtió.

Este documento describe el equipo, el estado inicial, los cambios y la reversión.
No es un manual de usuario del fabricante ni un firmware alternativo.

## 2. Requisitos

- Router InHand IG601PS08 (serie documentada `GP6011511293474`).
- Acceso físico al puerto de administración y a la alimentación.
- Credenciales de administrador vigentes.
- Un cliente en la misma red para confirmar el cambio.
- Copia del estado inicial: dirección, máscara, pool DHCP, rutas y versión.

No se requiere software adicional. El navegador del equipo de gestión alcanza para
el panel, siempre que se esté en el mismo segmento.

## 3. Estado inicial

| Parámetro | Valor |
|---|---|
| LAN | `192.168.1.1` |
| DHCP | Activo, pool en el segmento `192.168.1.0/24` |
| SSH | No disponible en la imagen |
| Firmware | InGateway 1.2.0.r3722 |

Anotar estos valores antes de cualquier cambio es obligatorio. Sin la foto inicial
no se puede afirmar que la reversión fue completa.

## 4. Procedimiento aplicado

1. **Inventario.** Se registraron modelo, serie, firmware, LAN y pool DHCP.
2. **Cambio de LAN.** Se modificó la dirección de la interfaz a `192.168.2.1`.
3. **Ajuste DHCP.** El pool se movió al nuevo segmento para evitar direcciones
   incoherentes en los clientes.
4. **Verificación.** Se comprobó que el panel respondía en la nueva dirección y que
   un cliente obtenía una IP del rango nuevo.
5. **Intento de SSH.** Se buscó el servidor `dropbear` en la imagen; no estaba
   disponible. No se instalaron componentes externos.
6. **Reversión.** Se restauraron la LAN `192.168.1.1` y el pool original. El equipo
   quedó como estaba al inicio.

## 5. Operación

El router no queda publicando ningún servicio nuevo. La administración se hace por
el panel web en la LAN. Si se necesita acceso remoto, la recomendación es una VPN o
un overlay de red, no exponer el panel a Internet.

Para revisar el detalle de cada paso, el documento fuente es
`versions/inhand-router/CAMBIOS_Y_REVERSION.md`. Ese archivo contiene la secuencia
real y las observaciones de campo.

## 6. Troubleshooting

**No responde en `192.168.2.1`.** Confirmá que el cliente está en el mismo segmento
y que la interfaz se aplicó. Si el cambio se hizo por WiFi, la sesión puede cortarse
antes de confirmar.

**El cliente recibe una IP de `192.168.1.x`.** El pool DHCP no se movió o hay otro
servidor DHCP en la red. Revisá ambos antes de cambiar la LAN otra vez.

**No aparece la opción de SSH.** Es una limitación de la imagen: `dropbear` no está
incluido. No fuerces paquetes que el firmware no soporta.

**Se perdió el acceso.** Usá acceso físico y el procedimiento de recuperación del
fabricante. Este repositorio no incluye firmware ni herramientas de recuperación.

## 7. Seguridad

- Cambiar la contraseña por defecto antes de conectar el equipo a una red compartida.
- No reutilizar la clave de administración en otros servicios.
- No publicar el panel en la WAN.
- Guardar el respaldo de configuración fuera del repositorio.
- Las credenciales no se versionan; el documento fuente las omite a propósito.

## 8. Alcance y límites

**Verificado:** cambio de LAN, pool DHCP, ausencia de SSH en esa imagen y reversión.

**No verificado:** otros modelos InHand, otras versiones de firmware, actualizaciones
y comportamiento bajo carga. No hay código ni binarios en esta familia.
