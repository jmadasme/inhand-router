# Guía de desarrollador · InHand Router

## 1. Qué hay en este repositorio

Una sola versión, `versions/inhand-router/`, con un único documento de campo:
`CAMBIOS_Y_REVERSION.md`. No hay código ejecutable, firmware ni scripts. La familia
existe para preservar el registro de una intervención real y evitar que se repita a
ciegas.

```
inhand-router/
├── README.md
├── LICENSE
├── docs/
│   ├── datasheet.md
│   ├── developer.md
│   ├── REFERENCES.md
│   └── index.html
└── versions/
    └── inhand-router/
        └── CAMBIOS_Y_REVERSION.md
```

## 2. Por qué no hay código

El cambio se hizo desde el panel de administración del router. Automatizarlo habría
requerido SSH, que no estaba disponible en la imagen. Forzar la instalación de un
servidor SSH no soportado habría convertido una intervención reversible en un equipo
difícil de mantener.

La decisión de diseño fue: **si no se puede hacer de forma soportada, se documenta la
limitación y se revierte**. Esa es la lección técnica del repositorio.

## 3. Estructura del documento fuente

`CAMBIOS_Y_REVERSION.md` se lee en cuatro bloques:

1. **Identificación:** marca, modelo, serie y firmware.
2. **Estado inicial:** LAN, DHCP y acceso.
3. **Cambios:** dirección nueva, pool y prueba de SSH.
4. **Reversión:** qué se restauró y cómo se confirmó.

Si agregás una versión nueva, mantené esos cuatro bloques. Son el contrato mínimo
para que otra persona pueda auditar el cambio.

## 4. Cómo extender la familia

- **Otra intervención, mismo modelo:** creá `versions/<nombre>/` con su propio
  `CAMBIOS_Y_REVERSION.md` y agregala a `families.py`.
- **Otro modelo InHand:** no reutilices el mismo nombre de versión. Documentá modelo,
  firmware y diferencias de menú.
- **Scripts de automatización:** solo si el firmware soporta el acceso necesario.
  En ese caso, incluí verificación previa y un camino de reversión.

No agregues credenciales, respaldos de configuración ni binarios de firmware. El
repositorio es documentación; el hardware se administra aparte.

## 5. Contrato de una versión

Cada carpeta de `versions/` debe poder respondernos:

- ¿Qué equipo exacto se tocó?
- ¿Cuál era el estado antes?
- ¿Qué se cambió y por qué?
- ¿Qué se verificó?
- ¿Cómo se vuelve atrás?
- ¿Qué no se pudo hacer?

Si falta una de esas respuestas, la versión está incompleta aunque el cambio haya
funcionado.

## 6. Revisión y pruebas

No hay tests automáticos porque no hay código. La revisión es documental:

- Coherencia entre estado inicial y reversión.
- Direcciones y máscaras sin ambigüedad.
- Ausencia de credenciales.
- Limitaciones declaradas, no escondidas.

Una persona que nunca vio el equipo debería poder entender qué pasó en diez minutos.
Ese es el criterio de calidad.

## 7. Roadmap posible

- Agregar una versión con acceso SSH solo si aparece una imagen que lo soporte.
- Documentar la recuperación por consola si el fabricante la publica.
- Separar el informe en una plantilla reutilizable para otros routers.

Nada de esto está implementado. El repositorio conserva lo que se hizo, no lo que
podría hacerse.

## 8. Licencia y uso

MIT © 2026 jmadasme. El documento se comparte como referencia. Las marcas, el firmware
y las herramientas de InHand conservan sus propias licencias.
