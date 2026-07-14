# Arquitectura general

```text
USB-C
  |
  +-- Alimentación
  |
  +-- ESP32-S3-WROOM-1-N16R8
          |
          +-- I2S
          +-- I2C
          +-- Reset y diagnóstico
                  |
                  +-- CS4272
                         |
                         +-- Placa de entrada intercambiable
                         +-- Placa de salida intercambiable
```

## Mainboard digital

Basada inicialmente en ESP32-S3-WROOM-1-N16R8 y CS4272.

## Placas analógicas intercambiables

Las etapas analógicas de entrada y salida se desarrollarán como placas separadas e intercambiables.

## Firmware modular

El firmware se organizará de forma modular cuando su desarrollo sea autorizado.

## Arquitectura pendiente de detalle

La arquitectura detallada permanece pendiente. Todavía no se definen hojas jerárquicas ni nombres de proyectos KiCad.

