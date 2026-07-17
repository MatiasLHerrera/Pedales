# Decisiones de diseño

## DEC-001 — Usar módulo ESP32-S3-WROOM-1-N16R8

**Estado: APROBADA.**

Motivos:

- menor complejidad que el SoC desnudo;
- Flash y PSRAM integradas;
- cristal y radiofrecuencia resueltos;
- más apropiado para prototipado y montaje.

## DEC-002 — Usar CS4272

**Estado: APROBADA.**

Motivo:

- ADC y DAC estéreo de audio para la plataforma inicial.

## DEC-003 — Usar I2S e I2C

**Estado: SUPERSEDED por DEC-007.**

- I2S transportará audio.
- I2C configuraría y permitiría diagnosticar el CODEC.

Esta fue la decisión inicial. Se conserva como historial y ya no describe la implementación vigente de MB-001.

## DEC-004 — Separar mainboard y circuitos analógicos

**Estado: APROBADA.**

Motivo:

- permitir probar diferentes entradas, preamplificadores y salidas.

## DEC-005 — Comenzar con PCB de dos capas

**Estado: APROBADA.**

Motivo:

- validar funcionamiento y aprender del primer prototipo.

## DEC-006 — Diseñar posteriormente una revisión de cuatro capas

**Estado: APROBADA.**

Motivo:

- mejorar integridad de señal, alimentación y desempeño de audio después de medir el prototipo.

## DEC-007 — Usar CS4272 en Stand-Alone Mode

**Estado: APROBADA.**

- I2S transportará el audio digital.
- No se implementarán I2C ni SPI en MB-001.
- El CS4272 se configurará mediante los pines `I2S_LJ`, `M1` y `M0`.
- No habrá acceso a registros, lectura de Chip ID, secuencia CPEN/PDN por firmware ni dirección I2C `0x10`.
- El modo inicial será I2S, Single Speed a 48 kHz y con de-emphasis desactivado.

Motivo:

- reducir la complejidad de control de la primera revisión y fijar la configuración inicial mediante straps visibles en el esquemático.

## DEC-008 — Separar VBUS de +5V

**Estado: APROBADA.**

- `VBUS` identifica los 5 V directos del USB-C antes de protección.
- `+5V` identifica la alimentación protegida y filtrada de la placa.
- `VA` deriva de `+5V`; `+3V3` se regula desde `+5V`; `VD` y `VL` derivan de `+3V3`.

## Decisiones pendientes

PENDIENTE. No se resuelven decisiones adicionales en esta etapa.
