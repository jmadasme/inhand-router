# InHand Router · InHand · Red

![Estado](https://img.shields.io/badge/estado-preservado-2A9D8F?style=flat-square)
![Versiones](https://img.shields.io/badge/versiones-1-457B9D?style=flat-square)
![Sección](https://img.shields.io/badge/sección-iot-5B7C99?style=flat-square)
![Licencia](https://img.shields.io/badge/licencia-MIT-06141b?style=flat-square)

> Bitácora de intervención en un InGateway IG601PS08: cambio de LAN, intento de SSH y reversión limpia.

Este repositorio reúne **1 versión** de InHand Router en `versions/`.
Cada carpeta conserva el contenido original de su etapa, sin `.git`, dependencias
instaladas ni binarios pesados. La documentación de operación y de código está en
[`docs/`](docs/), y el sitio del proyecto en [`docs/index.html`](docs/index.html).

## Índice de versiones

| Versión | Qué aporta | Archivos |
|---|---|---:|
| [`inhand-router`](versions/inhand-router/) | Informe único de cambios y reversión, con modelo, serial, firmware y estado final. | 1 |

La versión de referencia de esta familia es **`inhand-router`**: es la que se usa
para el README base y para los documentos canónicos. Las demás se mantienen
completas para que se pueda reconstruir la evolución y comparar decisiones.

## Arranque rápido

```bash
git clone https://github.com/jmadasme/inhand-router.git
cd inhand-router/versions/inhand-router
cat CAMBIOS_Y_REVERSION.md
```

Este repositorio es documentación, no firmware. No hay binarios ni credenciales; el equipo se dejó en su estado original.

## Documentación

- [`docs/datasheet.md`](docs/datasheet.md) — instalación, operación y límites.
- [`docs/developer.md`](docs/developer.md) — arquitectura, módulos y extensión.
- [`docs/REFERENCES.md`](docs/REFERENCES.md) — estándares, librerías y fuentes.
- [`docs/index.html`](docs/index.html) — página del proyecto (GitHub Pages).

## Alcance y honestidad


## Qué es este repositorio

Una bitácora de intervención sobre un router industrial **InHand IG601PS08**, también
conocido por su línea de firmware **InGateway**. No es una librería, no es un firmware
y no es un tutorial genérico: es el registro de un cambio de red que se hizo, se probó
y se revirtió cuando la capacidad deseada no estaba disponible.

El repositorio conserva ese único documento porque la información de campo suele
perderse. Saber qué modelo era, qué firmware traía, qué dirección tenía la LAN, qué
se cambió y cómo se volvió atrás vale más que un procedimiento idealizado que nadie
probó en el equipo real.

## El equipo

| Dato | Valor documentado |
|---|---|
| Marca | InHand |
| Modelo | IG601PS08 |
| Serie | GP6011511293474 |
| Firmware | InGateway 1.2.0.r3722 |
| Red original | LAN 192.168.1.1 con DHCP |
| Red posterior | LAN 192.168.2.1 con pool DHCP ajustado |
| Estado final | Configuración original restaurada |

La serie y el firmware importan: dos equipos de la misma familia pueden traer
interfaces distintas, menús distintos y capacidades distintas. Cualquier persona que
quiera repetir este trabajo debería confirmar primero esos dos datos en su propio
equipo.

## El problema

El router venía configurado en `192.168.1.1`, una dirección que en muchas redes
domésticas y de laboratorio ya está ocupada por otro equipo. Eso produce un conflicto
silencioso: el router responde, pero no es el router que uno cree, o directamente no
responde porque otra interfaz ganó la dirección.

La intención era mover la LAN a `192.168.2.1`, ajustar el pool DHCP para que los
clientes recibieran direcciones coherentes y, ya que se estaba en el equipo, habilitar
SSH para administración remota. El cambio de red es de bajo riesgo si se hace por el
puerto correcto y se tiene acceso físico. El SSH era la parte opcional.

## Qué se hizo

1. **Inventario antes de tocar.** Se registraron modelo, serie, versión de firmware,
   dirección LAN, máscara y rango DHCP. Sin esa foto inicial no hay reversión posible.
2. **Cambio de LAN.** La interfaz pasó de `192.168.1.1` a `192.168.2.1`. El pool DHCP
   se movió al nuevo rango para no repartir direcciones del segmento anterior.
3. **Prueba de acceso.** Se confirmó que la administración respondía en la nueva
   dirección y que un cliente obtenía una dirección coherente.
4. **Intento de SSH.** El firmware no incluía el servidor `dropbear`. Habilitar SSH
   habría requerido instalar componentes no soportados por esa imagen.
5. **Reversión.** Se volvió la LAN a `192.168.1.1`, se restauró el pool y se dejó el
   equipo como estaba. El host quedó limpio.

## Por qué no se forzó el SSH

Un router industrial no es un servidor de laboratorio. Instalar un paquete que el
fabricante no incluyó puede:

- romper la garantía o el soporte;
- dejar el equipo sin acceso si el servicio no arranca;
- abrir una superficie de ataque que nadie va a actualizar;
- crear una configuración que el siguiente técnico no puede reproducir.

La decisión fue aceptar la limitación y documentarla. Es más útil un informe que dice
"esta imagen no trae dropbear" que un equipo a medio modificar y sin registro.

## Cómo leer el repositorio

El contenido está en `versions/inhand-router/CAMBIOS_Y_REVERSION.md`. Es el documento
fuente y el único artefacto de la versión. La lectura recomendada es:

1. Identificación del equipo y estado inicial.
2. Cambios aplicados, con la dirección y el pool exactos.
3. Resultado de la prueba de SSH.
4. Reversión y estado final.

Si vas a intervenir un equipo similar, copiá primero el estado inicial con el mismo
nivel de detalle. No empieces por el cambio: empezá por el inventario.

## Qué se verificó y qué no

**Verificado:** el cambio de LAN, la asignación DHCP en el nuevo rango, la ausencia de
dropbear en esa imagen y la vuelta a la configuración original.

**No verificado:** el comportamiento en otras versiones de firmware, la compatibilidad
con otros modelos InHand y cualquier procedimiento de actualización. Este repositorio
no contiene firmware ni herramientas del fabricante.

## Seguridad

No se publican credenciales, claves ni el respaldo de configuración. El documento
original las omitió deliberadamente. Si repetís la intervención, usá una contraseña
propia, cambiala después de la puesta en marcha y no la guardes en el repositorio.

La administración del router debe quedar en una red de gestión, no expuesta a Internet.
Si necesitás acceso remoto, preferí una VPN o un servicio de overlay antes que abrir
el panel de administración a la WAN.

## Preguntas frecuentes

**¿Por qué no hay código?** Porque el trabajo fue de configuración, no de desarrollo.
El valor está en el registro, no en un script.

**¿Puedo aplicar los mismos pasos a otro InHand?** Podés usarlos como checklist, pero
verificá modelo, firmware y menús. La estructura puede cambiar.

**¿El router quedó modificado?** No. La LAN y el DHCP se revirtieron y el equipo quedó
en su estado original.

**¿Se puede habilitar SSH de otra forma?** Solo con componentes que el firmware
soporte. En esta imagen no estaba disponible y no se intentó forzar.

**¿Dónde consigo el firmware?** En el canal oficial de InHand. No se redistribuye acá.

## Licencia

MIT © 2026 jmadasme. El documento se comparte para que otras personas puedan aprender
del proceso; el hardware y el firmware conservan sus propias licencias y condiciones.


---

Licencia MIT © 2026 jmadasme. Las versiones se publican como material de
referencia; los nombres de hardware y marcas pertenecen a sus respectivos dueños.
