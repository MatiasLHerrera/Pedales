# MB-001 — Arquitectura del esquemático

## Estado

PROPUESTA APROBADA PARA DOCUMENTACIÓN — JERARQUÍA KICAD VACÍA IMPLEMENTADA Y
REVISADA MANUALMENTE; LOS CIRCUITOS TODAVÍA NO ESTÁN AUTORIZADOS

## Propósito

Definir la división funcional del esquemático MB-001 y documentar el estado de
la jerarquía vacía ya implementada en KiCad.

Este documento establece responsabilidades, interfaces y nombres de red. La
jerarquía vacía fue implementada y revisada manualmente como estructura. No
selecciona componentes, no asigna GPIO definitivos, no diseña circuitos y no
autoriza la implementación eléctrica de las hojas.

## Jerarquía de hojas

```text
00_TOP
├── 01_POWER
├── 02_ESP32
└── 03_CODEC
```

No se agregan hojas independientes de debug, expansión ni audio analógico en esta etapa.

## 00_TOP

`00_TOP` es la hoja raíz.

### Responsabilidades

- contener los símbolos jerárquicos de `01_POWER`, `02_ESP32` y `03_CODEC`;
- mostrar la arquitectura general;
- interconectar alimentación, reset, audio digital y señales analógicas;
- permitir comprender el sistema completo sin entrar en detalles internos.

### Contenido excluido

- circuitos eléctricos complejos;
- componentes discretos;
- reguladores;
- protección USB;
- ESP32-S3;
- CS4272;
- desacoplamientos;
- conectores físicos definitivos.

## 01_POWER

### Responsabilidades

- recibir `VBUS` desde el USB-C ubicado funcionalmente en `02_ESP32`;
- incorporar en una etapa posterior la protección y limitación de entrada, todavía pendientes de selección;
- generar la red protegida y filtrada `+5V` desde `VBUS`;
- generar `+3V3`;
- distribuir `+5V` y `+3V3`;
- alimentar el ESP32-S3 y el CS4272;
- generar o filtrar las ramas funcionales `VA`, `VD` y `VL`;
- incluir desacoplamientos generales y puntos de prueba de alimentación;
- incluir indicadores de alimentación únicamente si se aprueban más adelante.

### Dominios de alimentación

| Red | Interpretación | Tensión aprobada | Derivación |
|---|---|---:|---|
| `VBUS` | Entrada directa desde USB-C, antes de protección | 5 V nominales | USB-C |
| `+5V` | Alimentación protegida y filtrada de la placa | 5 V nominales | `VBUS`, mediante protección y filtrado pendientes |
| `+3V3` | Alimentación general de 3,3 V | 3,3 V nominales | `+5V`, mediante regulación pendiente de selección |
| `VA` | Alimentación analógica del CS4272 | 5 V nominales | `+5V` |
| `VD` | Alimentación digital interna del CS4272 | 3,3 V nominales | `+3V3` |
| `VL` | Dominio lógico del CS4272 | 3,3 V nominales | `+3V3` |
| `GND` | Referencia común | 0 V | Común al sistema |

`VA`, `VD` y `VL` deben mantenerse como redes funcionales separadas aunque puedan compartir la misma fuente mediante filtros o enlaces todavía no seleccionados. La separación funcional no autoriza dividir arbitrariamente el plano o los retornos de masa.

### Selecciones pendientes

Esta tarea no selecciona regulador, ferritas, filtros, fusible, protección contra inversión, TVS, limitador de corriente ni valores definitivos.

## 02_ESP32

### Responsabilidades

- alojar el ESP32-S3-WROOM-1-N16R8;
- alojar la interfaz USB-C;
- reservar USB D− en GPIO19 y USB D+ en GPIO20;
- incluir CC1 y CC2, con implementación todavía pendiente;
- prever la protección USB pendiente de selección;
- prever BOOT mediante GPIO0 y RESET mediante EN;
- incluir el desacoplamiento propio del módulo;
- conservar UART0 como interfaz de respaldo;
- soportar USB Serial/JTAG;
- exponer las señales I2S;
- controlar el reset del CS4272;
- prever `DEBUG_TIMING`;
- reservar GPIO de expansión, cuya definición permanece pendiente.

### Alimentación

- entrada `+3V3`;
- `GND`.

### Señales hacia 03_CODEC

| Señal | Dirección prevista |
|---|---|
| `MCLK` | `02_ESP32` → `03_CODEC` |
| `BCLK` | `02_ESP32` → `03_CODEC` |
| `LRCK` | `02_ESP32` → `03_CODEC` |
| `SDIN` | `02_ESP32` → `03_CODEC` |
| `SDOUT` | `03_CODEC` → `02_ESP32` |
| `CODEC_RST` | `02_ESP32` → `03_CODEC` |

No se asignan GPIO definitivos a I2S, `CODEC_RST`, mute, `DEBUG_TIMING` ni expansión en esta tarea. No se reservan GPIO para SDA o SCL porque MB-001 no implementa I2C ni SPI para el CODEC.

## 03_CODEC

### Responsabilidades

- alojar el CS4272;
- recibir `VA`, `VD` y `VL`;
- tratar AGND y DGND de acuerdo con una estrategia de retorno común todavía pendiente de implementación detallada;
- incluir los desacoplamientos específicos del CODEC;
- implementar los nodos `VCOM` y `FILT+` según el datasheet vigente;
- recibir el reset del CODEC;
- operar inicialmente en Stand-Alone Mode;
- usar AD0/CS como `I2S_LJ`, SDA/CDIN como `M1` y SCL/CCLK como `M0`;
- mantener `I2S_LJ` alto mediante una resistencia pull-up de 10 kΩ a VL;
- mantener `M1` bajo y `M0` alto;
- seleccionar I2S, Single Speed para 4 kHz a 50 kHz y de-emphasis desactivado;
- dejar visibles en el futuro esquemático los straps de `I2S_LJ`, `M1` y `M0`;
- no implementar I2C, SPI, dirección `0x10`, lectura de Chip ID ni secuencia CPEN/PDN por firmware;
- implementar I2S;
- recibir MCLK externo desde el ESP32-S3 como propuesta inicial;
- conectar XTI a GND y dejar XTO sin conexión;
- exponer entradas y salidas diferenciales;
- exponer `AMUTEC` y `BMUTEC`;
- proporcionar interfaces, todavía no definidas, hacia futuras placas analógicas;
- prever puntos de medición del CODEC.

### Señales analógicas a exponer

- `AINA+`
- `AINA-`
- `AINB+`
- `AINB-`
- `AOUTA+`
- `AOUTA-`
- `AOUTB+`
- `AOUTB-`
- `AMUTEC`
- `BMUTEC`

### Alimentación

- `VA`;
- `VD`;
- `VL`;
- `GND`.

### Reset

`CODEC_RST` debe ser una entrada proveniente de `02_ESP32`. Debe permanecer bajo hasta que las alimentaciones, MCLK, BCLK, LRCK, `I2S_LJ`, `M1` y `M0` estén estables; después puede liberarse para iniciar el CODEC. No se realizarán escrituras de registros tras liberar reset. SDIN debe mantenerse en un estado definido durante el arranque para evitar audio no deseado. La secuencia exacta se validará mediante firmware y mediciones de bring-up.

### Contenido excluido

Esta hoja no incluirá preamplificadores, jacks, amplificadores de salida ni adaptación analógica.

## Contrato entre hojas

| Señal | Origen | Destino | Dirección | Dominio | Tipo | Estado | Observaciones |
|---|---|---|---|---|---|---|---|
| `VBUS` | `02_ESP32` / USB-C | `01_POWER` | Hacia alimentación | 5 V sin proteger | Alimentación | APPROVED | Entrada directa anterior a protección y filtrado. |
| `+5V` | `01_POWER` | `01_POWER`, `03_CODEC` | Distribución local | 5 V protegido | Alimentación | APPROVED | Se origina después de protección y filtrado pendientes; alimenta la derivación de VA y el futuro regulador. |
| `+3V3` | `01_POWER` | `02_ESP32` | Hacia ESP32 | 3,3 V | Alimentación | APPROVED | El regulador y el presupuesto de corriente están pendientes. |
| `VA` | `01_POWER` | `03_CODEC` | Hacia CODEC | 5 V analógico | Alimentación | APPROVED | Red funcional separada derivada de `+5V`; filtrado pendiente. |
| `VD` | `01_POWER` | `03_CODEC` | Hacia CODEC | 3,3 V digital | Alimentación | APPROVED | Red funcional separada derivada de `+3V3`; filtrado pendiente. |
| `VL` | `01_POWER` | `03_CODEC` | Hacia CODEC | 3,3 V lógico | Alimentación | APPROVED | Compatible con la lógica del ESP32-S3; filtrado pendiente. |
| `GND` | `01_POWER` | `02_ESP32`, `03_CODEC` | Común | Referencia | Alimentación | APPROVED | Referencia común; la estrategia física de retornos se definirá posteriormente. |
| `MCLK` | `02_ESP32` | `03_CODEC` | Salida → entrada | 3,3 V lógico | Reloj de audio | PENDING_GPIO_ASSIGNMENT | Fuente inicial: ESP32-S3; generación y GPIO requieren validación. |
| `BCLK` | `02_ESP32` | `03_CODEC` | Salida → entrada | 3,3 V lógico | Reloj I2S | PENDING_GPIO_ASSIGNMENT | Corresponde al pin SCLK del CS4272. |
| `LRCK` | `02_ESP32` | `03_CODEC` | Salida → entrada | 3,3 V lógico | Reloj I2S | PENDING_GPIO_ASSIGNMENT | Frecuencia inicial prevista: 48 kHz. |
| `SDIN` | `02_ESP32` | `03_CODEC` | Salida → entrada | 3,3 V lógico | Datos I2S | PENDING_GPIO_ASSIGNMENT | Nombre desde la perspectiva del CS4272; alimenta el DAC. |
| `SDOUT` | `03_CODEC` | `02_ESP32` | Salida → entrada | 3,3 V lógico | Datos I2S | PENDING_GPIO_ASSIGNMENT | Nombre desde la perspectiva del CS4272; proviene del ADC. |
| `CODEC_RST` | `02_ESP32` | `03_CODEC` | Salida → entrada | 3,3 V lógico | Control | PENDING_GPIO_ASSIGNMENT | Secuencia concreta pendiente de implementación y validación. |
| `AINA+` | `00_TOP` / futura interfaz | `03_CODEC` | Hacia CODEC | Analógico diferencial | Entrada ADC | PENDING_INTERFACE_DEFINITION | No se define conector ni etapa analógica. |
| `AINA-` | `00_TOP` / futura interfaz | `03_CODEC` | Hacia CODEC | Analógico diferencial | Entrada ADC | PENDING_INTERFACE_DEFINITION | Par diferencial con `AINA+`. |
| `AINB+` | `00_TOP` / futura interfaz | `03_CODEC` | Hacia CODEC | Analógico diferencial | Entrada ADC | PENDING_INTERFACE_DEFINITION | No se define conector ni etapa analógica. |
| `AINB-` | `00_TOP` / futura interfaz | `03_CODEC` | Hacia CODEC | Analógico diferencial | Entrada ADC | PENDING_INTERFACE_DEFINITION | Par diferencial con `AINB+`. |
| `AOUTA+` | `03_CODEC` | `00_TOP` / futura interfaz | Desde CODEC | Analógico diferencial | Salida DAC | PENDING_INTERFACE_DEFINITION | No se define conector ni etapa de salida. |
| `AOUTA-` | `03_CODEC` | `00_TOP` / futura interfaz | Desde CODEC | Analógico diferencial | Salida DAC | PENDING_INTERFACE_DEFINITION | Par diferencial con `AOUTA+`. |
| `AOUTB+` | `03_CODEC` | `00_TOP` / futura interfaz | Desde CODEC | Analógico diferencial | Salida DAC | PENDING_INTERFACE_DEFINITION | No se define conector ni etapa de salida. |
| `AOUTB-` | `03_CODEC` | `00_TOP` / futura interfaz | Desde CODEC | Analógico diferencial | Salida DAC | PENDING_INTERFACE_DEFINITION | Par diferencial con `AOUTB+`. |
| `AMUTEC` | `03_CODEC` | `00_TOP` / futura interfaz | Desde CODEC | Dominio VA | Control de mute | PENDING_INTERFACE_DEFINITION | Adaptación eléctrica e interfaz pendientes. |
| `BMUTEC` | `03_CODEC` | `00_TOP` / futura interfaz | Desde CODEC | Dominio VA | Control de mute | PENDING_INTERFACE_DEFINITION | Adaptación eléctrica e interfaz pendientes. |

## Reglas de nomenclatura

- usar nombres cortos y coincidentes con los datasheets cuando sea posible;
- usar `+5V` y `+3V3` para alimentaciones generales;
- usar `VBUS` exclusivamente para los 5 V directos del USB-C antes de protección;
- usar `+5V` exclusivamente para la alimentación protegida y filtrada de la placa;
- usar `VA`, `VD` y `VL` para las ramas funcionales del CS4272;
- usar `GND` como referencia común;
- usar `BCLK` como nombre del reloj de bits del proyecto;
- recordar que `BCLK` corresponde al pin SCLK del CS4272;
- usar `CODEC_RST` para evitar confundirlo con EN/RESET del ESP32-S3;
- usar `SDIN` y `SDOUT` desde la perspectiva del CS4272;
- no usar nombres excesivamente largos salvo que exista riesgo real de ambigüedad.

## Decisiones pendientes antes de diseñar cada hoja

### 01_POWER

- regulador de 3,3 V;
- corriente total;
- corriente para placas externas;
- protección USB;
- fusible o limitación;
- filtrado de `VA`, `VD` y `VL`;
- indicadores de alimentación.

### 02_ESP32

- GPIO definitivos;
- protección USB;
- resistencias serie;
- botones y LEDs;
- acceso UART;
- `DEBUG_TIMING`;
- conectores de expansión.

### 03_CODEC

- filtros de alimentación;
- implementación física de los straps `I2S_LJ`, `M1` y `M0`;
- conectores analógicos;
- tratamiento de `AMUTEC` y `BMUTEC`;
- puntos de prueba;
- uso de canales no utilizados;
- arquitectura futura de placas analógicas.

## Decisiones ya aprobadas o fijadas para esta arquitectura documental

- alimentaciones generales `+5V` y `+3V3`;
- `VBUS` como entrada directa desde USB-C y `+5V` como red posterior a protección;
- `VA` a 5 V;
- `VD` a 3,3 V;
- `VL` a 3,3 V;
- ESP32-S3 a 3,3 V;
- CS4272 en Stand-Alone Mode sin I2C ni SPI;
- `I2S_LJ` alto mediante 10 kΩ a VL, `M1` bajo y `M0` alto;
- formato I2S, Single Speed a 48 kHz y de-emphasis desactivado;
- ESP32-S3 como maestro inicial de audio;
- CS4272 como esclavo inicial de audio;
- MCLK externo desde el ESP32-S3 como propuesta inicial;
- XTI conectado a GND;
- XTO sin conexión.

Estas decisiones no autorizan todavía la implementación en KiCad ni resuelven las selecciones pendientes enumeradas en este documento.
