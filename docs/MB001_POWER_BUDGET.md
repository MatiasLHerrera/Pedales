# MB-001 - Presupuesto preliminar de potencia y corriente

## Estado

BORRADOR TECNICO - REQUIERE REVISION HUMANA

Este documento prepara el presupuesto para la futura hoja `01_POWER`. No
selecciona componentes, no define footprints y no autoriza la implementacion
electrica en KiCad.

## 1. Fuentes y criterio

Se utilizaron las copias locales de solo lectura catalogadas en `docs/SOURCES.md`:

- `documentation/reference_designs/espressif/ESP32-S3-WROOM-1_WROOM-1U_Datasheet_v1.8.pdf`.
- `documentation/official/espressif/ESP32-S3_Series_Datasheet.pdf`.
- `documentation/official/espressif/ESP32-S3_Hardware_Design_Guidelines.pdf`.
- `documentation/reference_designs/espressif/ESP32-S3-DevKitC-1_V1.1_Schematic.pdf`.
- `documentation/official/cirrus_logic/CS4272_DS593F2_OCT2021.pdf`.
- `documentation/reference_designs/cirrus_logic/CDB4272_Evaluation_Board.pdf`.

La trazabilidad de valores y paginas se contrasta con
`docs/MB001_TECHNICAL_REQUIREMENTS.md`. Los PDFs locales no se modificaron.
La especificacion USB-C normativa no esta almacenada localmente, por lo que
sus valores electricos concretos permanecen `NOT SPECIFIED`.

Convenciones:

- No se convierte un valor tipico en maximo.
- `NOT SPECIFIED` significa que no hay un valor utilizable en las fuentes locales.
- Las reservas y eficiencias de calculo estan marcadas como `PROPOSED`.
- Las corrientes del CODEC se mantienen separadas por dominio.

## 2. Datos oficiales disponibles

| Carga o parametro | Tension | Tipico | Maximo/pico | Reserva adoptada | Fuente | Observaciones |
|---|---:|---:|---:|---:|---|---|
| ESP32-S3-WROOM-1-N16R8 | 3,3 V nominal | NOT SPECIFIED | NOT SPECIFIED | 500 mA de capacidad minima | ESP-MOD, p. 27 | Es capacidad minima de fuente del modulo, no consumo medido. |
| Rango VDD33 del modulo | 3,0-3,6 V | N/A | N/A | N/A | ESP-MOD, p. 27 | 3,3 V es la decision nominal del proyecto. |
| Wi-Fi del ESP32-S3 | 3,3 V | NOT SPECIFIED | NOT SPECIFIED | Dentro de 500 mA | ESP-MOD/ESP-SOC | No hay separacion local de TX, RX o picos. |
| Bluetooth del ESP32-S3 | 3,3 V | NOT SPECIFIED | NOT SPECIFIED | NOT SPECIFIED | ESP-SOC | No se adopta una cifra. |
| Flash y PSRAM del modulo | 3,3 V | NOT SPECIFIED | NOT SPECIFIED | Dentro de 500 mA | ESP-MOD, p. 3 | 16 MB Flash y 8 MB PSRAM; corriente separada no especificada. |
| CS4272, VA | 5 V nominal | NOT SPECIFIED | 53 mA | 0 mA adicional | CS-DS, p. 17 | Maximo trazado en PWR-006. |
| CS4272, VD + VL | 3,3 V nominal | NOT SPECIFIED | 28 mA total digital | 0 mA adicional | CS-DS, p. 17 | La fuente local no desglosa VD y VL. |
| CS4272 en reset/power-down | VA/VD/VL | NOT SPECIFIED | NOT SPECIFIED | 0 mA adoptado | CS-DS, pp. 6, 17, 44 | RST bajo reduce consumo, sin corriente cuantificada. |
| VCOM | VA | NOT SPECIFIED | 1 uA fuente/sumidero | 0 mA | CS-DS, p. 17 | No es una alimentacion. |
| AMUTEC/BMUTEC | VA | NOT SPECIFIED | 3 mA por salida | 0 mA | CS-DS, p. 17 | Corriente de carga externa; adaptacion pendiente. |
| Desacoplamiento CS4272 | VA/VD/VL | 0,1 uF + 1 uF por rama | N/A | N/A | CS-DS, p. 23 | El 0,1 uF debe estar junto al pin. |
| LED, USB, UART, reset y proteccion | VBUS/+5V/+3V3 | NOT SPECIFIED | NOT SPECIFIED | No adoptada | Proyecto | Componentes no seleccionados. |
| Placas analogicas externas | 5 V o 3,3 V | NOT SPECIFIED | NOT SPECIFIED | 100/300/500 mA | Proyecto | Reserva propuesta; tension pendiente. |

## 3. Dominios y calculo

- `VBUS`: entrada USB-C de 5 V antes de proteccion.
- `+5V`: red protegida y filtrada; alimenta VA y la conversion a 3,3 V.
- `VA`: rama de 5 V del CS4272, con hasta 53 mA trazados.
- `+3V3`: salida del regulador; alimenta ESP32-S3, VD y VL.
- `VD` y `VL`: ramas funcionales de 3,3 V; su maximo conjunto trazado es 28 mA.

Para un LDO, la corriente de entrada de 5 V se aproxima a la corriente de
salida de 3,3 V. Para un buck se usa:

`Iin_3V3 = (3,3 V * Iout_3V3) / (5 V * eficiencia)`

La eficiencia del buck se fija en 90 % solo como supuesto `PROPOSED`.

## 4. Escenarios calculables

La cifra de 500 mA es capacidad minima de la fuente del modulo, no consumo
maximo ni pico. Las cifras son por tanto de dimensionamiento conservador.

| Escenario | Supuesto ESP32 | +3V3 calculable | VA desde +5V | +5V total con LDO | Nota |
|---|---|---:|---:|---:|---|
| 1. Nucleo minimo | 500 mA reservados; radio deshabilitada | 528 mA | 53 mA | 581 mA | Consumo real de CPU/USB: NOT SPECIFIED. |
| 2. Validacion completa | 500 mA reservados; Wi-Fi habilitado | 528 mA | 53 mA | 581 mA | Incremento especifico Wi-Fi: NOT SPECIFIED. |
| 3a. Expansion 100 mA | Escenario 2 + 100 mA en 3,3 V | 628 mA | 53 mA | 681 mA | Reserva `PROPOSED`. |
| 3b. Expansion 300 mA | Escenario 2 + 300 mA en 3,3 V | 828 mA | 53 mA | 881 mA | Reserva `PROPOSED`. |
| 3c. Expansion 500 mA | Escenario 2 + 500 mA en 3,3 V | 1028 mA | 53 mA | 1081 mA | Reserva `PROPOSED`. |

| Escenario | +5V con LDO | +5V con buck al 90 % | Potencia LDO | Potencia buck | Margen conocido |
|---|---:|---:|---:|---:|---|
| Nucleo minimo | 581 mA | 440 mA | 2,905 W | 2,201 W | Sin margen adicional cuantificado |
| Validacion completa | 581 mA | 440 mA | 2,905 W | 2,201 W | Wi-Fi separado: NOT SPECIFIED |
| Expansion 100 mA | 681 mA | 514 mA | 3,405 W | 2,568 W | 100 mA propuestos |
| Expansion 300 mA | 881 mA | 660 mA | 4,405 W | 3,301 W | 300 mA propuestos |
| Expansion 500 mA | 1081 mA | 808 mA | 5,405 W | 4,040 W | 500 mA propuestos |

El buck incluye 53 mA de VA directamente desde 5 V y convierte la carga de
3,3 V con 90 %. No incluye proteccion, pistas, conectores ni otras cargas.

## 5. LDO frente a buck

### LDO

Con 5 V de entrada y 3,3 V de salida, la eficiencia teorica es 66 %. La
disipacion `P = (5 - 3,3) * Iout` es:

| Escenario | Disipacion | Clasificacion |
|---|---:|---|
| Nucleo minimo | 0,898 W | MARGINALLY_VIABLE |
| Validacion completa | 0,898 W | MARGINALLY_VIABLE |
| Expansion 100 mA | 1,068 W | NOT_RECOMMENDED |
| Expansion 300 mA | 1,408 W | NOT_RECOMMENDED |
| Expansion 500 mA | 1,748 W | NOT_RECOMMENDED |

El LDO simplifica el ruido de conmutacion, pero la viabilidad termica depende
del encapsulado, cobre, temperatura y consumo real. No se selecciona un LDO.

### Buck

La potencia perdida de la conversion 3,3 V con 90 % es:

| Escenario | Perdida | Clasificacion |
|---|---:|---|
| Nucleo minimo | 0,194 W | VIABLE |
| Validacion completa | 0,194 W | VIABLE, sujeto a ruido |
| Expansion 100 mA | 0,230 W | VIABLE, sujeto a filtrado |
| Expansion 300 mA | 0,303 W | MARGINALLY_VIABLE por USB |
| Expansion 500 mA | 0,377 W | MARGINALLY_VIABLE por USB |

El buck reduce el riesgo termico, pero introduce ripple y ruido. La rama VA
debe conservar bajo ruido y puede requerir filtrado posterior. La recomendacion
preliminar es estudiar buck para la conversion principal y filtrado posterior
de VA, VD y VL.

## 6. USB-C y VBUS

El proyecto trabaja a 5 V y no requiere por principio una tension superior ni
USB-PD. La capacidad anunciada por la fuente, CC1/CC2 y la politica exacta de
un sink/UFP requieren revisar la especificacion USB-C, no almacenada localmente.

| Capacidad anunciada | Comparacion | Resultado |
|---:|---|---|
| 500 mA | Menor que 581 mA del nucleo LDO, sin cargas adicionales | No aconsejada |
| 900 mA | Cubre nucleo y expansion de 100 mA en calculo LDO, con poco margen | Limitada |
| 1,5 A | Cubre el calculo LDO de expansion de 500 mA con margen reducido | Capacidad de diseno recomendada, pendiente de USB-C |

Un host no entrega automaticamente 1,5 A. La placa debe respetar la corriente
permitida por CC y no asumir mas capacidad que la anunciada. El comportamiento
ante VBUS insuficiente permanece `PENDING`.

## 7. Recomendaciones preliminares

Estas recomendaciones son `PROPOSED`, no decisiones aprobadas:

- Regulador 3,3 V: minimo calculable 528 mA; estudiar al menos 750 mA para
  margen inicial, sujeto a RF, temperatura, transitorios y expansion.
- Entrada protegida +5V: minimo calculable 581 mA sin expansion; el calculo
  LDO con reserva de 500 mA llega a 1081 mA antes de cargas no definidas.
  Estudiar una capacidad de diseno de 1,5 A, sin afirmar que USB-C la concedera.
- Reserva externa: mantener opciones separadas de 100, 300 y 500 mA hasta
  definir tension, conectores y cargas.
- Alimentacion exclusiva por USB-C: posible para el nucleo solo tras validar
  capacidad real, CC1/CC2, proteccion, caida y ruido. No esta garantizada para
  expansion de 500 mA.

## 8. Riesgos e incertidumbres

- Consumos tipicos/maximos separados de ESP32-S3, Wi-Fi, Bluetooth, Flash y
  PSRAM: `NOT SPECIFIED` en la trazabilidad local utilizable.
- 0,5 A es capacidad minima documentada del modulo, no consumo maximo/pico.
- No hay presupuesto para LEDs, USB, proteccion, UART, reset, conectores,
  regulador ni perdidas de distribucion.
- No existe reparto oficial local entre VD y VL.
- Consumo de placas externas: `NOT SPECIFIED`.
- Especificacion USB-C normativa: no disponible localmente.
- Eficiencia buck del 90 %: supuesto de calculo.
- Temperatura, cobre y encapsulado del regulador: no definidos.

## 9. Decisiones que requieren aprobacion

1. Capacidad y margen del regulador de 3,3 V.
2. Capacidad de la entrada protegida de +5V.
3. Reserva y tension de placas externas.
4. Uso real de Wi-Fi/Bluetooth durante audio.
5. Politica ante VBUS insuficiente.
6. Arquitectura buck/LDO y filtrado posterior.
7. Requisitos USB-C de CC1/CC2 y capacidad permitida.
8. Presupuesto de LEDs, USB, proteccion, reset, UART y conectores.

Este documento no autoriza seleccionar componentes ni comenzar el esquematico
de `01_POWER`.
