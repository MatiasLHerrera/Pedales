# Arquitectura general

```text
USB-C
  |
  +-- VBUS
  |     |
  |     +-- Protección y filtrado pendientes
  |             |
  |             +-- +5V
  |                   +-- VA
  |                   +-- Regulador pendiente --> +3V3
  |                                               +-- VD
  |                                               +-- VL
  |                                               +-- ESP32-S3
  |
  +-- ESP32-S3-WROOM-1-N16R8
          |
          +-- I2S
          +-- Reset y diagnóstico
                  |
                  +-- CS4272
                         +-- Stand-Alone Mode
                         +-- I2S_LJ, M1 y M0
                         |
                         +-- Placa de entrada intercambiable
                         +-- Placa de salida intercambiable
```

## Mainboard digital

Basada inicialmente en ESP32-S3-WROOM-1-N16R8 y CS4272.

El transporte de audio usa I2S. MB-001 no implementa I2C ni SPI para el CS4272: la primera revisión usa Stand-Alone Mode y configura el CODEC mediante `I2S_LJ`, `M1` y `M0`.

## Alimentación

`VBUS` identifica los 5 V directos del USB-C antes de protección. `+5V` identifica la alimentación protegida y filtrada de la placa. `VA` deriva de `+5V`; `+3V3` se regula desde `+5V`; `VD`, `VL` y la alimentación del ESP32-S3 derivan de `+3V3`.

## Placas analógicas intercambiables

Las etapas analógicas de entrada y salida se desarrollarán como placas separadas e intercambiables.

## Firmware modular

El firmware se organizará de forma modular cuando su desarrollo sea autorizado.

## Arquitectura pendiente de detalle

La división documental de hojas está definida en `docs/MB001_SCHEMATIC_ARCHITECTURE.md`. Su implementación en KiCad continúa sin autorización.
