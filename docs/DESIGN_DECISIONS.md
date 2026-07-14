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

**Estado: APROBADA.**

- I2S transportará audio.
- I2C configurará y permitirá diagnosticar el CODEC.

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

## Decisiones pendientes

PENDIENTE. No se resuelven decisiones adicionales en esta etapa.

