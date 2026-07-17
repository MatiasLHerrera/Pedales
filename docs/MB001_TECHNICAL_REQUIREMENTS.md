# MB-001 — Requisitos técnicos verificables

## Estado

BORRADOR TÉCNICO — REQUIERE REVISIÓN HUMANA

## Propósito

Transformar la documentación oficial del ESP32-S3-WROOM-1-N16R8 y del CS4272 en requisitos trazables para el diseño posterior de la primera mainboard.

Este documento no autoriza el inicio de KiCad, no define componentes todavía no seleccionados, no convierte propuestas en decisiones aprobadas y no reemplaza los datasheets oficiales. Ante cualquier diferencia, prevalece la fuente oficial vigente.

## Convenciones y fuentes

Las páginas citadas son las numeradas por el propio documento. Se usa `N/A` para documentos Markdown y páginas web sin numeración. Las clasificaciones y estados se limitan a los valores definidos para esta tarea.

**Interpretación de clasificación y estado:** `VERIFIED` significa que el contenido de la fila es coherente con las fuentes citadas. Cuando la clasificación es `PROPOSED`, ese estado no implica aprobación del proyecto; la propuesta continúa pendiente de decisión humana salvo que exista otra fila o decisión explícita que la apruebe. `SUPERSEDED` identifica una decisión histórica reemplazada que ya no forma parte del diseño activo.

| Código | Fuente |
|---|---|
| PRJ-REQ | `docs/REQUIREMENTS.md`, Requisitos aprobados inicialmente / Requisitos pendientes |
| PRJ-DEC | `docs/DESIGN_DECISIONS.md`, DEC-001 a DEC-008 |
| PRJ-ARCH | `docs/ARCHITECTURE.md`, Arquitectura general |
| PRJ-RULE | `AGENTS.md`, reglas permanentes de PCB |
| ESP-MOD | ESP32-S3-WROOM-1 & ESP32-S3-WROOM-1U Datasheet v1.8, LOC-010 |
| ESP-SOC | ESP32-S3 Series Datasheet v2.2, LOC-004 |
| ESP-HDG | ESP32-S3 Hardware Design Guidelines, Release master, LOC-003 |
| ESP-ERR | ESP32-S3 Series SoC Errata v1.3, LOC-002 |
| ESP-IDF-I2S | ESP-IDF Programming Guide v6.0.2, Inter-IC Sound (I2S), WEB-005 |
| ESP-IDF-USJ | ESP-IDF Programming Guide v6.0.2, USB Serial/JTAG Controller Console, WEB-007 |
| ESP-IDF-USB | ESP-IDF Programming Guide v6.0.2, USB Device Stack, WEB-008 |
| CS-DS | CS4272 Datasheet DS593F2, revisión F2, LOC-001 |
| CDB-DS | CDB4272 Evaluation Board DS593DB2, LOC-008 |
| I2S-SPEC | NXP I2S Bus Specification UM11732 Rev. 3.0, LOC-007 |
| USB-C-SPEC | USB Type-C Cable and Connector Specification Release 2.5, WEB-014 |

## 1. ESP32-S3-WROOM-1-N16R8

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| ESP-001 | Identificación | La mainboard debe usar exactamente el módulo ESP32-S3-WROOM-1-N16R8. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; ESP-MOD | DEC-001; Module Overview, Table 1-1 | N/A; 3 | No sustituir por otra variante sin una decisión explícita documentada. |
| ESP-002 | Memoria | La variante debe aportar 16 MB de Flash Quad SPI y 8 MB de PSRAM Octal SPI. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Module Overview, Table 1-1 | 3 | Corresponde específicamente a N16R8. |
| ESP-003A | Alimentación | VDD33 debe permanecer entre 3,0 V y 3,6 V. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Recommended Operating Conditions | 27 | No confundir el límite superior de 3,6 V con una tensión nominal. |
| ESP-003B | Alimentación | MB-001 alimentará el módulo con 3,3 V nominales. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados | N/A | La tolerancia y caída de la fuente deberán mantener el módulo dentro de ESP-003A. |
| ESP-004 | Alimentación | La fuente externa del módulo debe poder entregar como mínimo 0,5 A. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Recommended Operating Conditions, Table 6-2 | 27 | Es el mínimo indicado para la alimentación del módulo, no el presupuesto total de la placa. |
| ESP-005 | Alimentación | Se debe reservar margen por encima de 0,5 A y desacoplamiento local para transitorios de RF. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG | Power Supply | 5 | El margen exacto y el regulador siguen pendientes. |
| ESP-006 | Reset | EN no debe quedar flotante; nivel bajo deshabilita/resetea el módulo y nivel alto lo habilita. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Pin Definitions; Peripheral Pin Configurations | 11; 25 | EN corresponde a CHIP_PU del SoC. |
| ESP-007 | Reset | EN solo debe liberarse después de que la alimentación de 3,3 V sea estable y debe permanecer bajo al menos 50 µs para producir reset. | MANDATORY_MANUFACTURER | VERIFIED | ESP-SOC | Power-up and Reset Timing | 30 | La implementación concreta se decidirá en el esquemático. |
| ESP-008 | Reset | Se debe prever en EN el RC recomendado de 10 kΩ y 1 µF, sujeto a verificación con la rampa real de la fuente. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG; ESP-MOD | Chip Power-up and Reset Timing; Peripheral Schematics | 8; 41 | Los valores son recomendados, no una selección definitiva de componentes. |
| ESP-009 | Strapping | GPIO0, GPIO3, GPIO45 y GPIO46 deben tratarse como strapping pins durante reset. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Boot Configurations | 13–16 | Sus niveles de arranque no pueden quedar dominados accidentalmente por periféricos. |
| ESP-010 | Strapping | GPIO0 tiene pull-up débil por defecto y, junto con GPIO46, selecciona el modo de arranque; debe preverse control BOOT y no debe añadirse capacitancia elevada. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD; ESP-HDG | Boot Mode Control; Strapping Pins | 13–14; 15 | Para descarga manual se fuerza GPIO0 bajo durante reset. |
| ESP-011 | Strapping/JTAG | GPIO3 está flotante por defecto y controla la fuente de JTAG junto con eFuses; cualquier carga externa debe conservar un nivel definido durante el muestreo. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | JTAG Signal Source Control | 15–16 | El uso definitivo del pin queda pendiente del pinout. |
| ESP-012 | Strapping | En N16R8, VDD_SPI de la PSRAM integrada queda fijado por eFuse y GPIO45 no cambia esa tensión; aun así GPIO45 sigue siendo strap y debe protegerse durante reset. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Module Schematics; VDD_SPI Voltage Control | 3; 39 | No asignar GPIO45 sin revisar sus demás efectos de strapping. |
| ESP-013 | Strapping | GPIO46 tiene pull-down débil por defecto y participa con GPIO0 en el modo de arranque; su circuito externo no debe impedir el boot normal o de descarga. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Boot Mode Control | 13–14 | La función posterior al arranque dependerá del pinout aprobado. |
| ESP-014 | USB | GPIO19 debe reservarse como USB D− y GPIO20 como USB D+ para el USB nativo. | APPROVED_PROJECT_DECISION | VERIFIED | ESP-MOD; PRJ-REQ | Pin Definitions; Requisitos aprobados | 11; N/A | Ambos pines presentan transitorios durante el arranque; no reutilizarlos silenciosamente. |
| ESP-015 | PSRAM | GPIO35, GPIO36 y GPIO37 no deben asignarse a señales de la mainboard porque la PSRAM Octal de N16R8 los utiliza internamente. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Pin Definitions, note b | 11 | Restricción específica de módulos con PSRAM Octal. |
| ESP-016 | UART | UART0 debe quedar accesible para bring-up y recuperación; U0TXD y U0RXD corresponden a GPIO43 y GPIO44 del módulo. | PROPOSED | VERIFIED | ESP-MOD | Pin Definitions | 11–12 | Conservar puntos de diagnóstico; la forma de conexión externa está pendiente. |
| ESP-017 | JTAG | Debe preservarse una vía de depuración mediante USB Serial/JTAG o JTAG de pads; la elección definitiva no debe contradecir GPIO3 ni eFuses. | PROPOSED | REQUIRES_REVIEW | ESP-SOC; ESP-IDF-USJ | Peripheral Pin Configurations; JTAG debugging | 25–26; N/A | Los pads JTAG son GPIO39 a GPIO42; la estrategia queda pendiente. |
| ESP-018 | USB | USB Serial/JTAG debe poder utilizarse para consola bidireccional, programación y depuración JTAG. | APPROVED_PROJECT_DECISION | VERIFIED | ESP-IDF-USJ | Introduction; Hardware Requirements | N/A | Es una función fija distinta del controlador USB OTG. |
| ESP-019 | USB | USB OTG y USB Serial/JTAG comparten la PHY interna; no se debe asumir operación simultánea sin PHY externa o cambio de estrategia. | MANDATORY_MANUFACTURER | VERIFIED | ESP-IDF-USB | External PHY Configuration | N/A | Requiere una decisión de uso antes del esquemático. |
| ESP-020 | I2S | Las señales I2S pueden rutearse mediante la matriz de GPIO, pero sus GPIO definitivos deben permanecer pendientes. | PENDING_VALIDATION | PENDING | ESP-SOC; PRJ-REQ | GPIO Matrix; Requisitos pendientes | 20; N/A | MB-001 no reserva GPIO para SDA/SCL y no implementa I2C ni SPI para el CODEC. |
| ESP-021 | Alimentación | Se deben colocar los desacoplamientos de 3,3 V cerca de los puntos de alimentación del módulo y mantener trayectos de alimentación y retorno de baja impedancia. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG | Power Supply; PCB Layout Design | 5; 20–23 | Los valores y placement final se revisarán en el esquemático/PCB. |
| ESP-022 | EPAD | El pad inferior del módulo es GND; soldarlo a masa no es obligatorio, pero mejora comportamiento térmico y debe evaluarse para montaje manual. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-MOD | Pin Definitions; Peripheral Schematics | 12; 41 | Si se suelda, la apertura de pasta y vías deben evitar exceso de estaño. |
| ESP-023 | Dimensiones | El volumen reservado para el módulo debe respetar 25,5 ± 0,2 mm × 18 ± 0,2 mm × 3,1 ± 0,15 mm, además del área de antena. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Physical Dimensions, Figure 10-1 | 42 | Dimensiones del WROOM-1 con antena PCB. |
| ESP-024 | Footprint | El footprint debe reproducir el land pattern oficial del ESP32-S3-WROOM-1, incluida la numeración, pads y área de antena. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | PCB Land Pattern, Figure 11-1 | 45 | La biblioteca de Espressif para KiCad 10 aún debe validarse; no se creó footprint. |
| ESP-025 | Antena | El módulo debe situarse preferentemente en el borde de la PCB, minimizando la interferencia de la placa base con la antena. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG; ESP-MOD | PCB Layout for Modules; Module Placement | 28–31; 46 | La orientación definitiva depende de dimensiones aún pendientes. |
| ESP-026 | Antena | El keepout de antena debe implementarse como restricción real en todas las capas cuando el PCB sea autorizado. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-RULE | Reglas para futuros PCB | N/A | La regla de todas las capas proviene del proyecto; los dibujos del fabricante definen el área geométrica. |
| ESP-030 | Antena | La ausencia de cobre, pistas y componentes bajo el área de antena debe validarse contra Figure 26 y Figure 11-1 antes de congelar el layout. | PENDING_VALIDATION | REQUIRES_REVIEW | ESP-HDG; ESP-MOD | Keepout Zone for Module Antenna; PCB Land Pattern | 31; 45 | Las fuentes locales dibujan el área, pero el texto extraído no dice literalmente “sin cobre en todas las capas”. |
| ESP-027 | Montaje | Deben respetarse MSL 3, ventana de 168 h tras apertura en condiciones indicadas y un único ciclo de reflow si se usa reflow. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Recommended PCB Land Pattern and Reflow Profile | 47 | El proyecto prevé montaje manual; el proceso concreto requiere revisión. |
| ESP-028 | Errata/Adquisición | La revisión/PW del módulo debe registrarse durante compra y bring-up; lotes anteriores a Date Code 2219 o PW-2022-06 pueden no disponer de descarga USB-OTG. | PENDING_VALIDATION | REQUIRES_REVIEW | ESP-ERR | USBOTG-4289 | 9 | USB Serial/JTAG es el workaround oficial. Esta verificación no bloquea el esquemático porque la ruta principal del proyecto será USB Serial/JTAG, no descarga USB-OTG. |
| ESP-029 | Errata/Firmware | La versión de ESP-IDF seleccionada debe incluir los workarounds oficiales aplicables a cache, RTC y configuración analógica de sleep. | PENDING_VALIDATION | PENDING | ESP-ERR | CACHE-126, RTC-126, ANALOG-160 | 5–7 | La versión exacta de ESP-IDF no se selecciona aquí. |

## 2. CS4272

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| COD-001 | Identificación | La mainboard debe usar el CODEC estéreo CS4272. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; CS-DS | DEC-002; General Description | N/A; 2 | ADC y DAC estéreo de hasta 24 bits y 192 kHz. |
| COD-002 | Encapsulado | El diseño debe usar el encapsulado 28-pin TSSOP de cuerpo 4,4 mm definido por el fabricante. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Ordering Information; Package Dimensions | 2; 46 | Compatible con soldadura manual bajo proceso controlado; footprint aún no autorizado. |
| COD-003A | Alimentación analógica | VA debe permanecer entre 4,75 V y 5,25 V. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Specified Operating Conditions | 9 | No alimentar VA con 3,3 V. |
| COD-003B | Alimentación analógica | MB-001 alimentará VA con una rama nominal de 5 V. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados | N/A | El filtrado y las caídas de tensión deben conservar el rango de COD-003A. |
| COD-004A | Alimentación digital | VD debe permanecer entre 3,1 V y 5,25 V. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Specified Operating Conditions | 9 | VD alimenta la sección digital interna y se documenta separado de VL. |
| COD-004B | Alimentación digital | MB-001 alimentará VD con 3,3 V nominales. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados | N/A | Puede compartir regulación con otros dominios de 3,3 V, pero debe conservar desacoplamiento propio. |
| COD-005A | Alimentación lógica | VL debe permanecer entre 2,37 V y 5,25 V. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Specified Operating Conditions | 9 | VL fija el dominio de las E/S digitales; no confundir con VD. |
| COD-005B | Alimentación lógica | MB-001 alimentará VL con 3,3 V nominales para compatibilidad directa con la lógica del ESP32-S3. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados | N/A | Mantener desacoplamiento propio aunque comparta la fuente de 3,3 V. |
| COD-006 | Masa | AGND y DGND deben conectarse a una referencia de masa de baja impedancia conforme al esquema y layout; no deben quedar flotantes. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions; Grounding and Power Supply Decoupling | 6; 34 | La estrategia física de unión se decidirá con el layout. |
| COD-007 | Desacoplamiento | VA, VD y VL deben usar cada uno 0,1 µF + 1 µF según Figure 8, con 0,1 µF lo más próximo posible al pin. | RECOMMENDED_MANUFACTURER | VERIFIED | CS-DS | Typical Connection Diagram; Grounding and Power Supply Decoupling | 23; 34 | Con VD=3,3 V separado no corresponde montar el resistor opcional de 5,1 Ω desde VA. |
| COD-008 | VCOM | VCOM debe desacoplarse como indica Figure 8 y tratarse como referencia de alta impedancia: nominal 0,48 × VA, máximo 1 µA de fuente/sumidero e impedancia típica 25 kΩ. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DC Electrical Characteristics; Typical Connection Diagram | 17; 23 | No usar VCOM como fuente de alimentación. |
| COD-009 | FILT+ | FILT+ debe desacoplarse a AGND con 47 µF + 0,1 µF según Figure 8; su tensión nominal es VA. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DC Electrical Characteristics; Typical Connection Diagram | 17; 23 | Mantener el nodo corto, sin carga y alejado de clocks. |
| COD-010 | Reset | RST bajo pone el dispositivo en bajo consumo; debe mantenerse bajo hasta que alimentación, MCLK, BCLK, LRCK y los pines de Stand-Alone Mode estén estables. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions; Recommended Power-Up Sequence | 6; 27 | Tras liberar RST no se realizarán escrituras de registros; SDIN debe permanecer definido durante el arranque. |
| COD-011 | Inicialización | En la primera revisión el CS4272 debe iniciar en Stand-Alone Mode mediante los niveles de `I2S_LJ`, `M1` y `M0`; no se habilitará el control port. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; CS-DS | DEC-007; Stand-Alone Mode | N/A; 24–26 | La secuencia exacta de reset y clocks se validará en bring-up. |
| COD-012 | Control port | MB-001 no debe implementar I2C ni SPI y no accederá a registros internos del CS4272. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; CS-DS | DEC-007; Stand-Alone Mode; Control Port Interface | N/A; 24–26; 35 | I2S continúa siendo la interfaz de audio; no sustituye conceptualmente al puerto de control. |
| COD-013 | I2C | El datasheet documenta I2C como capacidad del control port, pero MB-001 no la selecciona. | MANDATORY_MANUFACTURER | NOT_APPLICABLE | CS-DS | I2C Mode | 36 | No habrá dirección `0x10`, SDA/SCL, pull-ups, lectura de Chip ID ni GPIO reservados para este bus. |
| COD-014 | SPI | El datasheet documenta SPI como alternativa del control port, pero MB-001 no la selecciona. | MANDATORY_MANUFACTURER | NOT_APPLICABLE | CS-DS | Control Port Interface; SPI Mode | 35 | No cablear ni habilitar SPI en la primera revisión. |
| COD-015 | Audio digital | El formato inicial debe ser I2S de hasta 24 bits; left-justified y right-justified permanecen alternativas documentadas, no seleccionadas. | APPROVED_PROJECT_DECISION | VERIFIED | CS-DS; PRJ-REQ | Serial Audio Formats; Requisitos aprobados | 20; N/A | Los formatos right-justified solo están disponibles en control port mode. |
| COD-016 | Clocks | LRCK identifica canal, SCLK es el bit clock de audio y MCLK alimenta los moduladores. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions | 6 | En este documento BCLK equivale al pin SCLK del CS4272. El pin reutilizado SCL/CCLK se denomina `M0` en Stand-Alone Mode. |
| COD-017 | Master/slave | En modo maestro, el CS4272 genera LRCK y SCLK; en modo esclavo ambos son entradas síncronas a MCLK. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Master/Slave Mode Selection | 27 | El proyecto eligió inicialmente CS4272 esclavo. |
| COD-018 | Clock ratio | En modo esclavo se recomienda SCLK = 64 × Fs; con MCLK externo, las razones MCLK/LRCK deben corresponder a Table 9 y a los bits Ratio. | RECOMMENDED_MANUFACTURER | VERIFIED | CS-DS | Master/Slave Mode; Clock Ratio Selection | 27–29 | Para 48 kHz y 12,288 MHz, MCLK/LRCK = 256. |
| COD-019 | MCLK externo | Cuando MCLK sea externo, el pin MCLK debe ser conducido, XTI debe conectarse a masa, XTO debe quedar sin conectar y no debe montarse cristal. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Crystal Applications (XTI/XTO), Control Port Mode | 27–28 | Requisito expreso: no dejar XTI y XTO ambos sin conectar. |
| COD-020 | Clock interno | Si en una revisión se usa cristal, debe conectarse únicamente entre XTI/XTO con sus cargas y MCLK pasa a ser salida bufferizada. | MANDATORY_MANUFACTURER | NOT_APPLICABLE | CS-DS | Crystal Applications (XTI/XTO) | 27 | No es la propuesta inicial; conservar como alternativa documentada. |
| COD-021 | ADC | AINA± y AINB± son dos entradas analógicas diferenciales; no deben convertirse silenciosamente a single-ended en la mainboard. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions; Input Connections | 6; 32 | Las placas analógicas externas harán la adaptación futura. |
| COD-022 | ADC | La entrada diferencial full-scale es 1,07 × VA mínima, 1,13 × VA típica y 1,19 × VA máxima; la impedancia diferencial típica es 37 kΩ. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | ADC Analog Characteristics | 14 | Con VA=5 V el valor típico equivale a 5,65 Vpp diferencial; no es nivel de guitarra. |
| COD-023 | DAC | AOUTA± y AOUTB± son dos salidas analógicas diferenciales. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions; Output Connections | 6; 33 | Requieren acondicionamiento externo antes de cargas finales. |
| COD-024 | DAC | La salida full-scale diferencial es 0,91 × VA mínima, 0,96 × VA típica y 1,01 × VA máxima, con resistencia de salida típica de 100 Ω. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DAC Analog Characteristics | 10 | Especificación medida bajo carga indicada por el fabricante. |
| COD-025 | DAC | La carga AC no debe ser menor de 3 kΩ y la capacitancia de carga directa no debe superar 100 pF. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DAC Analog Characteristics | 10 | Excluye conexión directa de auriculares típicos. |
| COD-026 | Mute | AMUTEC y BMUTEC son salidas activas en bajo para controlar mute externo; se activan en inicialización, reset, mute, relación de clocks incorrecta y power-down. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions; Mute Control | 6; 34 | La arquitectura externa de mute sigue pendiente. |
| COD-027 | Mute | El dominio de AMUTEC/BMUTEC es analógico: nivel alto típico VA, bajo típico 0 V y corriente máxima 3 mA; no conectarlas directamente a lógica de 3,3 V sin adaptación validada. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DC Electrical Characteristics | 17 | Requiere revisar control, inversión y level shifting. |
| COD-028 | Power-down | Para power-down por hardware, RST debe estar bajo y clocks/datos estáticos. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DC Electrical Characteristics note 20; Power Down | 17; 44 | La función PDN del control port no forma parte del diseño activo; la secuencia de apagado debe evitar pops mediante mute externo si se implementa. |
| COD-029 | Diagnóstico | MB-001 no leerá el registro Chip ID en la primera revisión. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; CS-DS | DEC-007; Chip ID, Register 08h | N/A; 44 | El registro permanece como capacidad documentada del control port no seleccionado. |
| COD-030 | Layout | Clocks y señales digitales deben mantenerse alejados de VCOM, FILT+, entradas y salidas analógicas; desacoplamientos y retornos deben minimizar el recorrido. | RECOMMENDED_MANUFACTURER | VERIFIED | CS-DS | Grounding and Power Supply Decoupling | 34 | La CDB4272 se usa solo como evidencia de implementación, no como obligación normativa. |
| COD-031 | Inicialización | La anterior secuencia por firmware que escribía 03h en registro 07h queda reemplazada por Stand-Alone Mode. | APPROVED_PROJECT_DECISION | SUPERSEDED | PRJ-DEC; CS-DS | DEC-003; DEC-007; Control Port Interface | N/A; 35 | Se conserva como historial; no forma parte del diseño activo y no se documenta ninguna escritura CPEN/PDN tras reset. |
| COD-032 | Stand-Alone | El pin AD0/CS se reutiliza como `I2S_LJ` y debe quedar alto mediante una resistencia pull-up de 10 kΩ a VL para seleccionar I2S. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; CS-DS | DEC-007; Stand-Alone Mode | N/A; 24–26 | La anterior conexión AD0=AGND/dirección `0x10` queda reemplazada. No se seleccionan referencia ni footprint de la resistencia. |
| COD-033 | Referencias | Las menciones a “VREF” en §5.7 deben interpretarse con cautela como FILT+, que es el nombre usado por pinout y Figure 8. | PENDING_VALIDATION | CONFLICT_FOUND | CS-DS | Grounding and Power Supply Decoupling; Pin Descriptions | 34; 5–6 | Inconsistencia terminológica del datasheet. |
| COD-034 | DAC | Para full-scale de salida deben prevalecer AOUT± y la tabla DAC; Figure 15 rotula erróneamente la ecuación con AIN±. | PENDING_VALIDATION | CONFLICT_FOUND | CS-DS | DAC Analog Characteristics; Output Connections | 10; 33 | No trasladar el error de etiqueta al esquemático. |
| COD-035 | VCOM | No copiar el capacitor de VCOM de 10 µF de la CDB4272; el datasheet vigente limita el capacitor principal de VCOM a 1 µF, más 0,1 µF. | MANDATORY_MANUFACTURER | CONFLICT_FOUND | CS-DS; CDB-DS | Typical Connection Diagram; CS4272 schematic | 23; 17 | Prevalece DS593F2 de 2021 sobre la placa de evaluación de 2003. |

## 3. Audio digital

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| AUD-001 | Muestreo | La frecuencia de muestreo inicial debe ser 48 kHz. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados inicialmente | N/A | Corresponde a single-speed mode del CS4272. |
| AUD-002 | Resolución | La resolución efectiva inicial debe ser 24 bits. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ; CS-DS | Requisitos aprobados; Serial Audio Formats | N/A; 20 | El CS4272 admite I2S hasta 24 bits. |
| AUD-003 | Slots | Se deben prever dos slots de 32 bits por frame. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ; ESP-IDF-I2S | Requisitos aprobados; Standard Mode | N/A | Cada slot transportará 24 bits efectivos y padding; no confundir ancho de slot con resolución. |
| AUD-004 | Canales | El enlace inicial debe transportar dos canales, izquierdo y derecho. | APPROVED_PROJECT_DECISION | VERIFIED | ESP-IDF-I2S; I2S-SPEC | Standard Mode; The I2S bus | N/A; 4–5 | El modo estándar usa dos slots. |
| AUD-005 | Roles | El ESP32-S3 debe actuar inicialmente como maestro de audio. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados inicialmente | N/A | Debe generar MCLK, BCLK y LRCK en la propuesta inicial. |
| AUD-006 | Roles | El CS4272 debe actuar inicialmente como esclavo. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ; CS-DS | Requisitos aprobados; Master/Slave Mode | N/A; 27 | LRCK y SCLK/BCLK son entradas del CODEC. |
| AUD-007 | LRCK | LRCK previsto es 48 kHz, igual a Fs. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ; CS-DS | Requisitos aprobados; Master/Slave Mode | N/A; 27 | Derivación directa de la decisión Fs=48 kHz. |
| AUD-008 | BCLK | BCLK previsto es 64 × Fs = 3,072 MHz. | PROPOSED | VERIFIED | PRJ-REQ; CS-DS | Requisitos aprobados; Master/Slave Mode | N/A; 27 | Dos slots × 32 bits; la arquitectura definitiva de reloj sigue pendiente. |
| AUD-009 | MCLK | MCLK previsto es 256 × Fs = 12,288 MHz. | PROPOSED | VERIFIED | CS-DS | Clock Ratio Selection, Table 9 | 29 | Es una relación admitida, no una arquitectura de clock aprobada. |
| AUD-010 | Datos | SDIN del CS4272 debe recibir el DOUT del ESP32-S3; transporta audio hacia el DAC. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions | 6 | Dirección ESP32-S3 → CS4272. |
| AUD-011 | Datos | SDOUT del CS4272 debe alimentar el DIN del ESP32-S3; transporta audio desde el ADC. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Pin Descriptions | 6 | Dirección CS4272 → ESP32-S3. |
| AUD-012 | Compatibilidad | El ESP32-S3 debe configurarse en modo estándar de dos slots, que admite datos/slots de 8, 16, 24 o 32 bits. | MANDATORY_MANUFACTURER | VERIFIED | ESP-IDF-I2S | Clock Terminology; Standard Mode | N/A | La configuración efectiva 24/32 debe validarse con la versión elegida de ESP-IDF. |
| AUD-013 | DMA | El transporte TX y RX debe usar DMA para evitar copia muestra a muestra por CPU. | APPROVED_PROJECT_DECISION | VERIFIED | ESP-IDF-I2S | Introduction; Data Transport | N/A | Dimensionar buffers y latencia en firmware de validación. |
| AUD-014 | Full-duplex | Se debe validar full-duplex estándar con TX y RX compartiendo clocks; ESP-IDF ofrece un ejemplo oficial simplex/full-duplex para ESP32-S3. | PENDING_VALIDATION | REQUIRES_REVIEW | ESP-IDF-I2S | Application Example, Standard TX/RX Usage | N/A | Confirmar asignación de controlador, canales, MCLK y GPIO. |
| AUD-015 | Firmware | Antes de congelar el esquemático se deben medir MCLK, BCLK y LRCK y verificar frecuencia, duty cycle, estabilidad, formato I2S, alineación de 24 bits en slots de 32 y streaming DMA bidireccional. | PENDING_VALIDATION | PENDING | ESP-IDF-I2S; CS-DS | I2S Clock; Serial Audio Port Timing | N/A; 18–20 | Prueba obligatoria de firmware/hardware de bring-up. |
| AUD-016 | Stand-Alone | La combinación inicial debe seleccionar I2S, Single Speed para 4 kHz a 50 kHz y de-emphasis desactivado. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; CS-DS | DEC-007; Stand-Alone Mode | N/A; 24–26 | La frecuencia inicial de 48 kHz pertenece a este rango. |

## 4. Configuración Stand-Alone del CODEC

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| CTL-001 | Función | El CS4272 debe funcionar inicialmente en Stand-Alone Mode y configurarse mediante pines de modo. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; CS-DS | DEC-007; Stand-Alone Mode | N/A; 24–26 | I2S transporta audio; el control port no se implementa. |
| CTL-002 | I2S/LJ | AD0/CS funciona como `I2S_LJ` en Stand-Alone Mode. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Stand-Alone Mode | 24–26 | Usar el nombre `I2S_LJ`, no AD0, en la arquitectura activa. |
| CTL-003 | I2S/LJ | `I2S_LJ` debe quedar alto mediante pull-up de 10 kΩ a VL. | APPROVED_PROJECT_DECISION | VERIFIED | CS-DS; PRJ-DEC | Stand-Alone Mode; DEC-007 | 24–26; N/A | Selecciona formato I2S; no se asignan referencia ni footprint. |
| CTL-004 | M1 | SDA/CDIN funciona como `M1` en Stand-Alone Mode y debe quedar en nivel bajo. | APPROVED_PROJECT_DECISION | VERIFIED | CS-DS; PRJ-DEC | Stand-Alone Mode; DEC-007 | 24–26; N/A | No denominar SDA a este pin en la arquitectura activa. |
| CTL-005 | M0 | SCL/CCLK funciona como `M0` en Stand-Alone Mode y debe quedar en nivel alto. | APPROVED_PROJECT_DECISION | VERIFIED | CS-DS; PRJ-DEC | Stand-Alone Mode; DEC-007 | 24–26; N/A | No denominar SCL a este pin en la arquitectura activa. |
| CTL-006 | Modo | `M1` bajo y `M0` alto deben seleccionar Single Speed, rango de 4 kHz a 50 kHz, con de-emphasis desactivado. | APPROVED_PROJECT_DECISION | VERIFIED | CS-DS; PRJ-DEC | Stand-Alone Mode; DEC-007 | 24–26; N/A | Los niveles deben quedar visibles en el futuro esquemático. |
| CTL-007 | Reset | `CODEC_RST` debe permanecer bajo hasta estabilizar alimentaciones, MCLK, BCLK, LRCK, `I2S_LJ`, `M1` y `M0`; luego puede liberarse. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Recommended Power-Up Sequence; Stand-Alone Mode | 27; 24–26 | SDIN debe mantenerse definido; la secuencia exacta se validará con firmware y mediciones. |
| CTL-008 | Registros | Tras liberar reset no se realizarán escrituras de registros ni lectura de Chip ID. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC | DEC-007 | N/A | No hay CPEN/PDN por firmware en el diseño activo. |
| CTL-009 | Historial | La decisión inicial de usar I2C, SDA/SCL, dirección `0x10` y diagnóstico por registros queda reemplazada. | APPROVED_PROJECT_DECISION | SUPERSEDED | PRJ-DEC | DEC-003; DEC-007 | N/A | Se conserva para trazabilidad; no implementar pull-ups ni reservar GPIO. |
| CTL-010 | Alternativas | I2C y SPI permanecen como capacidades documentadas del control port, no seleccionadas para MB-001. | MANDATORY_MANUFACTURER | NOT_APPLICABLE | CS-DS | Control Port Interface; I2C Mode; SPI Mode | 35–36 | Requerirían una nueva decisión explícita para una revisión futura. |

## 5. USB-C y programación

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| USB-001 | USB nativo | La placa debe usar el USB nativo del ESP32-S3, sin requerir un puente USB-UART para la ruta principal. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ; ESP-IDF-USJ | Requisitos aprobados; Introduction | N/A | UART0 se conserva como respaldo de bring-up. |
| USB-002 | Datos | USB D− debe ir a GPIO19 y USB D+ a GPIO20. | MANDATORY_MANUFACTURER | VERIFIED | ESP-IDF-USJ; ESP-IDF-USB | Hardware Requirements; Hardware Connection | N/A | Mantener polaridad y par diferencial. |
| USB-003 | Programación | MB-001 utilizará USB Serial/JTAG como ruta principal para flashing con `esptool`/`idf.py`, consola bidireccional y JTAG con OpenOCD. | APPROVED_PROJECT_DECISION | VERIFIED | ESP-IDF-USJ | Introduction; Uploading the Application | N/A | La aplicación puede deshabilitar/reconfigurar accidentalmente los pines; conservar BOOT/RESET. |
| USB-004 | OTG | El soporte funcional USB OTG queda fuera del alcance inicial de MB-001 y no debe condicionar el esquemático más allá de documentar que comparte GPIO19/GPIO20 y la PHY interna con USB Serial/JTAG. | APPROVED_PROJECT_DECISION | VERIFIED | ESP-IDF-USB; ESP-ERR; PRJ-REQ | Overview; External PHY Configuration; USBOTG-4289; Alcance inicial | N/A; 9; N/A | Una revisión futura podrá reabrir USB OTG mediante una decisión explícita y validación de hardware/firmware. |
| USB-005 | USB-C CC | Si MB-001 se implementa como sink/UFP con receptáculo USB-C, se deben disponer terminaciones pull-down en CC1 y CC2; topología y valores definitivos requieren revisar el paquete normativo USB-C 2.5. | PENDING_VALIDATION | REQUIRES_REVIEW | USB-C-SPEC | Cable and Connector Specification package | N/A | La landing oficial está catalogada, pero el ZIP normativo no fue auditado localmente. |
| USB-006 | Protección | Se debe prever protección ESD de baja capacitancia en D+/D− y protección apropiada en VBUS/CC; el dispositivo concreto queda pendiente. | PENDING_VALIDATION | PENDING | ESP-HDG; USB-C-SPEC | Power Supply; Cable and Connector Specification package | 5; N/A | No se selecciona protector durante esta tarea. |
| USB-007 | Integridad | Se deben reservar resistencias serie cerca del ESP32-S3 y footprints opcionales de capacitores a masa; 22/33 Ω son solo valores iniciales del fabricante. | RECOMMENDED_MANUFACTURER | REQUIRES_REVIEW | ESP-HDG | USB | 18 | Los valores definitivos requieren simulación/medición y no se congelan aquí. |
| USB-008 | Layout | El par USB debe apuntar a 90 Ω diferencial ±10 %, longitudes apareadas, pocas vías y plano de referencia continuo. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG | USB PCB Layout | 30 | Agregar vías de retorno cuando el par cambie de capa. |
| USB-009 | Alimentación | VBUS debe tratarse como entrada nominal de 5 V y no debe aplicarse directamente a los dominios de 3,3 V. | MANDATORY_MANUFACTURER | VERIFIED | ESP-IDF-USJ; PRJ-REQ | Hardware Requirements; Requisitos aprobados | N/A | Regulación y protección siguen pendientes. |
| USB-010 | Host/cargador | Un host USB puede alimentar, enumerar, programar y ofrecer consola; un cargador puede proporcionar VBUS pero no se debe asumir que ofrece enumeración o datos. | PROPOSED | REQUIRES_REVIEW | ESP-IDF-USJ; ESP-IDF-USB | Introduction; Overview | N/A | Conclusión funcional inferida de la necesidad de un host/PC para USB Serial/JTAG. Validar cargadores y cables reales. |
| USB-011 | Selecciones | Conector, ESD, fusible, filtro VBUS, resistencias serie definitivas y política de shield deben permanecer pendientes. | PENDING_VALIDATION | PENDING | PRJ-REQ; USB-C-SPEC | Requisitos pendientes; Cable and Connector Specification package | N/A | No seleccionar componentes ni conexión de shield aquí. |

## 6. Alimentación

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| PWR-001 | Entrada | `VBUS` debe identificar la entrada nominal de 5 V directamente desde USB-C, antes de protección. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-ARCH; ESP-IDF-USJ | Arquitectura general; Hardware Requirements | N/A | No usar `+5V` para el lado anterior a la protección. |
| PWR-002 | Conversión | `+5V` debe identificar la alimentación protegida y filtrada de la placa; desde ella deben derivar VA y la regulación pendiente hacia `+3V3`. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC; PRJ-ARCH | DEC-008; Alimentación | N/A | El circuito de protección, filtrado y el regulador siguen sin seleccionar. |
| PWR-003A | ESP32-S3 | La alimentación del módulo debe permanecer entre 3,0 V y 3,6 V. | MANDATORY_MANUFACTURER | VERIFIED | ESP-MOD | Recommended Operating Conditions | 27 | La fuente externa debe entregar al menos 0,5 A al módulo. |
| PWR-003B | ESP32-S3 | La rama destinada al módulo será de 3,3 V nominales. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados inicialmente | N/A | Debe cumplir el rango de PWR-003A bajo carga y transitorios. |
| PWR-004A | CODEC | VA debe mantenerse entre 4,75 V y 5,25 V. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | Specified Operating Conditions | 9 | Filtrado y caída de tensión deben conservar VA ≥ 4,75 V. |
| PWR-004B | CODEC | MB-001 conectará VA a una rama nominal de 5 V derivada de `+5V`. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ; PRJ-DEC | Requisitos aprobados; DEC-008 | N/A | La implementación debe cumplir PWR-004A con cables y fuentes USB reales. |
| PWR-005 | CODEC | VD y VL deben alimentarse como ramas documentadas separadamente, ambas a 3,3 V nominales. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ; CS-DS | Requisitos aprobados; Specified Operating Conditions | N/A; 9 | No unir conceptos aunque puedan compartir regulador. |
| PWR-006 | Presupuesto | El presupuesto base debe contemplar ≥0,5 A para el módulo más hasta 53 mA de VA y 28 mA digitales máximos del CS4272, antes de otras cargas. | PROPOSED | VERIFIED | ESP-MOD; CS-DS | Recommended Operating Conditions; DC Electrical Characteristics | 27; 17 | Suma conservadora mínima de 581 mA; no incluye transitorios, LEDs, pérdidas ni placas externas. |
| PWR-007 | Transitorios | La fuente de 3,3 V y su desacoplamiento deben tolerar transitorios de RF sin reset, caída de clocks ni ruido audible. | RECOMMENDED_MANUFACTURER | REQUIRES_REVIEW | ESP-HDG | Power Supply | 5 | El margen dinámico se validará con medición. |
| PWR-008 | VA | La rama VA debe mantener bajo ruido y no caer por debajo de 4,75 V considerando VBUS, protección, filtrado, cable y carga mínimos previstos. | MANDATORY_MANUFACTURER | REQUIRES_REVIEW | CS-DS | Specified Operating Conditions; Grounding and Power Supply Decoupling | 9; 34 | Requiere presupuesto de caída y pruebas con host/cargador. |
| PWR-009 | Separación | Debe estudiarse filtrado/separación entre VBUS, `+5V`, `+3V3`, VA, VD y VL sin crear retornos interrumpidos. | PENDING_VALIDATION | PENDING | CS-DS; PRJ-DEC | Grounding and Power Supply Decoupling; DEC-008 | 34; N/A | No se seleccionan ferritas, resistores o filtros. |
| PWR-010 | Desacoplamiento | Se deben respetar los desacoplamientos locales de ESP-MOD y CS4272, con retorno corto al plano de referencia. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG; CS-DS | Power Supply; Typical Connection Diagram | 5; 23 | Los valores del CODEC están en COD-007 a COD-009. |
| PWR-011 | Expansión | La corriente reservada para placas analógicas externas debe definirse antes de dimensionar regulador, conector y protección. | PENDING_VALIDATION | PENDING | PRJ-REQ | Requisitos pendientes | N/A | No asumir que los 5 V o 3,3 V sobrantes están disponibles. |
| PWR-012 | Selección | Regulador de 3,3 V, protección, fusible y filtros no deben seleccionarse hasta cerrar corriente, caída, ruido, temperatura y disponibilidad USB. | PENDING_VALIDATION | PENDING | PRJ-REQ | Requisitos pendientes | N/A | Decisión requerida antes del esquemático. |

## 7. Analogía y expansión

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| ANA-001 | ADC | Se deben exponer dos canales de entrada diferencial: AINA± y AINB±. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-ARCH; CS-DS | Arquitectura general; Pin Descriptions | N/A; 6 | La futura aplicación puede usar uno o ambos canales. |
| ANA-002 | DAC | Se deben exponer dos canales de salida diferencial: AOUTA± y AOUTB±. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-ARCH; CS-DS | Arquitectura general; Pin Descriptions | N/A; 6 | La futura aplicación puede usar uno o ambos canales. |
| ANA-003 | Uso parcial | Debe conservarse la posibilidad futura de usar solo un canal de entrada y uno de salida sin redefinir el CODEC. | PROPOSED | REQUIRES_REVIEW | PRJ-ARCH; CS-DS | Placas analógicas intercambiables; Pin Descriptions | N/A; 6 | La selección de canales y manejo de los no usados queda pendiente. |
| ANA-004 | Modularidad | Preamplificación, jacks, conversión diferencial/single-ended y salida final deben implementarse en circuitos o placas externas intercambiables. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC | DEC-004 | N/A | La mainboard expone señales diferenciales. |
| ANA-005 | Guitarra | Una guitarra pasiva no debe conectarse directamente al ADC; se requiere adaptación de impedancia, nivel, bias y filtrado en placa externa. | PROPOSED | REQUIRES_REVIEW | CS-DS | ADC Analog Characteristics; Input Connections | 14; 32 | Inferencia por Zin diferencial de 37 kΩ y full-scale típico 1,13×VA. Arquitectura concreta pendiente. |
| ANA-006 | Auriculares | Los auriculares no deben conectarse directamente al DAC porque la carga mínima especificada es 3 kΩ. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DAC Analog Characteristics | 10 | Requiere amplificador de auriculares externo si se desea esa función. |
| ANA-007 | Conversión | Toda conversión single-ended/diferencial y adaptación de impedancia debe quedar fuera de MB-001 hasta seleccionar arquitectura analógica. | PENDING_VALIDATION | PENDING | PRJ-REQ; PRJ-DEC | Requisitos pendientes; DEC-004 | N/A | No seleccionar operacionales ni redes en esta tarea. |
| ANA-008 | Nivel/protección | Control de nivel, protección de jacks, anti-alias adicional y filtros de salida deben definirse en las placas analógicas externas. | PENDING_VALIDATION | PENDING | CS-DS; PRJ-REQ | Input/Output Connections; Requisitos pendientes | 32–33; N/A | Respetar full-scale, Zin y cargas del CODEC. |
| ANA-009 | VCOM | VCOM solo puede usarse como referencia de alta impedancia y no como alimentación o virtual ground cargable. | MANDATORY_MANUFACTURER | VERIFIED | CS-DS | DC Electrical Characteristics | 17 | Límite de fuente/sumidero: 1 µA. |
| ANA-010 | Mute | AMUTEC y BMUTEC deben quedar disponibles para una futura función de mute, con adaptación al dominio de 5 V. | PROPOSED | REQUIRES_REVIEW | CS-DS | Mute Control; DC Electrical Characteristics | 34; 17 | No asignar GPIO o transistor concreto todavía. |
| ANA-011 | Arquitectura | Todas las topologías analógicas concretas, conectores y niveles nominales externos permanecen pendientes. | PENDING_VALIDATION | PENDING | PRJ-REQ | Requisitos pendientes | N/A | No presentar ningún circuito de la CDB4272 como solución aprobada. |

## 8. PCB y montaje

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| PCB-001 | Capas | La primera revisión debe plantearse en dos capas. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC | DEC-005 | N/A | La autorización para diseñarla aún no existe. |
| PCB-002 | Capas | Debe conservarse una revisión futura de cuatro capas. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-DEC | DEC-006 | N/A | Espressif recomienda cuatro capas, aunque admite dos con restricciones. |
| PCB-003 | Masa | En dos capas debe mantenerse referencia/plano de masa continuo para digital, RF, clocks y audio; no interrumpir retornos con particiones arbitrarias. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG; CS-DS | Two-Layer PCB Design; Grounding and Power Supply Decoupling | 22–23; 34 | La geometría concreta requiere revisión conjunta de señales y retornos. |
| PCB-004 | AGND/DGND | No se deben crear planos AGND y DGND separados por defecto; la estrategia debe unir las referencias con baja impedancia y controlar físicamente corrientes. | PROPOSED | REQUIRES_REVIEW | CS-DS; CDB-DS | Typical Connection Diagram; Grounding and Power Supply Decoupling | 23–34; 8 | La CDB usa referencia única y plano extenso; no convierte su layout en obligación. |
| PCB-005 | Zonas | Placement y ruteo deben separar físicamente RF/antena, USB y digital rápido, alimentación y audio analógico sensible. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG; CS-DS | PCB Layout Design; Grounding and Power Supply Decoupling | 20–31; 34 | Separación no significa cortar el plano de retorno. |
| PCB-006 | Antena | El módulo debe ubicarse en el borde y respetar el keepout real de todas las capas. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-RULE; ESP-HDG | Reglas para futuros PCB; PCB Layout for Modules | N/A; 28–31 | La ausencia exacta de cobre se mantiene en revisión según ESP-030. |
| PCB-007 | Clocks | MCLK, BCLK y LRCK deben ser cortas, directas, con referencia continua y alejadas de VCOM, FILT+ y señales analógicas. | RECOMMENDED_MANUFACTURER | VERIFIED | CS-DS | Grounding and Power Supply Decoupling | 34 | Reservar puntos de medición sin crear stubs excesivos. |
| PCB-008 | Retornos | Todo cambio de capa o separación funcional debe conservar camino de retorno continuo; agregar vías de retorno donde corresponda. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG | USB PCB Layout; Two-Layer PCB Design | 30; 22–23 | Especialmente USB y clocks de audio. |
| PCB-009 | Desacoplamiento | Cada capacitor de desacoplamiento debe quedar próximo al pin correspondiente y con conexión corta a masa. | RECOMMENDED_MANUFACTURER | VERIFIED | ESP-HDG; CS-DS | Power Supply; Grounding and Power Supply Decoupling | 5; 34 | El 0,1 µF del CODEC debe ser el más cercano. |
| PCB-010 | Componentes | Resistores y capacitores discretos deben ser preferentemente 0805; 0603 solo cuando exista razón técnica o de espacio. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados inicialmente | N/A | No fuerza tamaños de encapsulados de IC. |
| PCB-011 | Montaje | El diseño debe priorizar montaje manual. | APPROVED_PROJECT_DECISION | VERIFIED | PRJ-REQ | Requisitos aprobados inicialmente | N/A | El proceso manual del módulo requiere validación específica. |
| PCB-012 | CODEC | El TSSOP-28 de paso 0,65 mm debe evaluarse para soldadura manual y retrabajo, respetando el footprint MO-153. | PROPOSED | REQUIRES_REVIEW | CS-DS | Package Dimensions | 46 | No se ha probado físicamente. |
| PCB-013 | Módulo | El pad inferior del módulo debe evaluarse térmica y manufacturablemente; no aplicar reglas del EPAD del chip desnudo al módulo sin fuente específica. | PENDING_VALIDATION | REQUIRES_REVIEW | ESP-MOD | Peripheral Schematics | 41 | Soldarlo es opcional; controlar pasta/vías si se adopta. |
| PCB-014 | Test | Deben preverse puntos de prueba para las señales de diagnóstico enumeradas en la sección siguiente, sin degradar USB, clocks o nodos analógicos. | PROPOSED | REQUIRES_REVIEW | PRJ-RULE; CS-DS | Reglas para futuros firmware; Grounding and Power Supply Decoupling | N/A; 34 | Forma y ubicación se definen durante esquemático/placement. |
| PCB-015 | Autorización | Ningún requisito de esta sección autoriza crear PCB, esquemático, footprint o símbolo. | PENDING_VALIDATION | PENDING | PRJ-RULE | Estado actual del proyecto | N/A | Requiere una tarea explícita posterior. |
| PCB-016 | Antena | No debe autorizarse cobre bajo el área de antena hasta confirmar la geometría exacta del keepout del fabricante; la intención de diseño es mantener esa zona libre. | PENDING_VALIDATION | REQUIRES_REVIEW | ESP-HDG; ESP-MOD | Keepout Zone for Module Antenna; PCB Land Pattern | 31; 45 | La conclusión se mantiene explícitamente pendiente porque el texto local no formula la prohibición literal. |

## 9. Puntos de diagnóstico previstos

| ID | Subsistema | Requisito | Clasificación | Estado | Fuente | Sección | Página | Observaciones |
|---|---|---|---|---|---|---|---:|---|
| DIA-001 | Timing | Prever acceso a `DEBUG_TIMING`; el GPIO definitivo permanece pendiente. | PROPOSED | PENDING | PRJ-RULE | Reglas para futuro firmware | N/A | Señal temporal para osciloscopio. |
| DIA-002 | Audio clock | Prever medición de MCLK. | PROPOSED | REQUIRES_REVIEW | CS-DS | Pin Descriptions | 6 | Evitar stub que degrade el clock. |
| DIA-003 | Audio clock | Prever medición de BCLK/SCLK. | PROPOSED | REQUIRES_REVIEW | CS-DS | Pin Descriptions | 6 | Señal de entrada al CODEC en la propuesta inicial. |
| DIA-004 | Audio clock | Prever medición de LRCK. | PROPOSED | REQUIRES_REVIEW | CS-DS | Pin Descriptions | 6 | Confirmar 48 kHz y fase. |
| DIA-005 | Audio data | Prever medición de SDIN del CS4272. | PROPOSED | REQUIRES_REVIEW | CS-DS | Pin Descriptions | 6 | ESP32-S3 → DAC. |
| DIA-006 | Audio data | Prever medición de SDOUT del CS4272. | PROPOSED | REQUIRES_REVIEW | CS-DS | Pin Descriptions | 6 | ADC → ESP32-S3. |
| DIA-007 | Configuración | Prever verificación de `I2S_LJ`. | PROPOSED | REQUIRES_REVIEW | CS-DS | Stand-Alone Mode | 24–26 | Debe observarse alto a VL mediante 10 kΩ. |
| DIA-008 | Configuración | Prever verificación de `M1` y `M0`. | PROPOSED | REQUIRES_REVIEW | CS-DS | Stand-Alone Mode | 24–26 | Comprobar M1 bajo y M0 alto durante reset y arranque. |
| DIA-009 | Control | Prever medición de CODEC_RST. | PROPOSED | REQUIRES_REVIEW | CS-DS | Recommended Power-Up Sequence | 27 | Necesario para correlacionar inicialización. |
| DIA-010 | USB | Permitir medición de USB D+ únicamente mediante pads inline, pads de componentes serie o puntos de sondeo sin stub; no colocar un testpoint ramificado convencional. | PROPOSED | REQUIRES_REVIEW | ESP-HDG | USB PCB Layout | 30 | La solución debe conservar impedancia, simetría y continuidad del par. |
| DIA-011 | USB | Permitir medición de USB D− únicamente mediante pads inline, pads de componentes serie o puntos de sondeo sin stub; no colocar un testpoint ramificado convencional. | PROPOSED | REQUIRES_REVIEW | ESP-HDG | USB PCB Layout | 30 | Debe conservarse simetría respecto de D+ y evitar ramificaciones. |
| DIA-012 | Alimentación | Prever puntos de prueba diferenciados para `VBUS` y `+5V`. | PROPOSED | VERIFIED | PRJ-ARCH; PRJ-DEC | Alimentación; DEC-008 | N/A | Permiten medir la caída a través de la protección y el filtrado pendientes. |
| DIA-013 | Alimentación | Prever punto de prueba de 3,3 V. | PROPOSED | VERIFIED | PRJ-REQ | Requisitos aprobados inicialmente | N/A | Medir droop durante RF/audio. |
| DIA-014 | Analógico | Prever medición de VCOM con instrumento de alta impedancia. | PROPOSED | REQUIRES_REVIEW | CS-DS | DC Electrical Characteristics | 17 | No cargar el nodo; máximo 1 µA. |
| DIA-015 | Analógico | Prever medición de FILT+ con instrumento de alta impedancia. | PROPOSED | REQUIRES_REVIEW | CS-DS | DC Electrical Characteristics | 17 | Mantener punto y traza alejados de clocks. |
| DIA-016 | Consola | Prever acceso a UART0 TX/RX. | PROPOSED | VERIFIED | ESP-MOD | Pin Definitions | 12 | GPIO43/GPIO44. |
| DIA-017 | Arranque | Prever acceso a BOOT/GPIO0. | PROPOSED | VERIFIED | ESP-MOD | Boot Mode Control | 14 | No añadir capacitancia alta. |
| DIA-018 | Reset | Prever acceso a RESET/EN del ESP32-S3. | PROPOSED | VERIFIED | ESP-MOD | Pin Definitions; Power-up and Reset Timing | 11; 16 | Diferente de CODEC_RST. |

## Open Questions

- ¿Qué lote, revisión de silicio y PW tendrá el módulo ESP32-S3-WROOM-1-N16R8 real?
- ¿Una revisión futura necesitará USB OTG device además de USB Serial/JTAG? Esta función queda fuera del alcance inicial de MB-001.
- ¿Qué GPIO se asignarán finalmente a I2S, CODEC_RST, MUTEC y DEBUG_TIMING?
- ¿Qué fuente generará MCLK=12,288 MHz y con qué precisión/jitter?
- ¿Se reservará una alternativa de clock externo o cristal para pruebas?
- ¿Cómo se garantizarán 5 V dentro de 4,75–5,25 V en VA con cables y fuentes USB reales?
- ¿Qué corriente necesitarán las placas analógicas externas?
- ¿Qué estrategia de mute se usará para AMUTEC/BMUTEC de dominio VA?
- ¿Se soldará el EPAD del módulo y qué proceso manual/reflow se validará?
- ¿Se utilizarán Wi‑Fi/Bluetooth durante streaming de audio y cuál será su impacto en alimentación, RF, DMA y latencia?

## Contradictions Found

1. **AD0 a VA frente al límite de entrada digital.** CS-DS pp. 35–36 indica unir AD0 a VA para obtener nivel alto en I2C, pero p. 9 limita las entradas digitales a `VL + 0,3 V`. Esta contradicción se conserva como descripción del modo I2C no seleccionado y no afecta el diseño activo: en Stand-Alone Mode el pin se usa como `I2S_LJ` con pull-up de 10 kΩ a VL.
2. **Default de PDN.** La tabla rápida de registro 07h en CS-DS p. 37 muestra PDN=0, mientras §8.7.5 p. 44 afirma que PDN está activo por defecto. La contradicción se conserva para trazabilidad del control port no seleccionado; MB-001 no escribirá el registro 07h.
3. **Capacitor de VCOM.** La CDB4272 de 2003 usa 10 µF + 0,1 µF, pero DS593F2 de 2021 limita el capacitor principal a 1 µF. Prevalece el datasheet vigente.
4. **Nombre VREF/FILT+.** CS-DS §5.7 p. 34 usa “VREF”, mientras pinout y Figure 8 denominan el nodo FILT+. Debe confirmarse que la instrucción se refiere a FILT+.
5. **Etiqueta AIN/AOUT.** Figure 15 p. 33 etiqueta el full-scale de salida con AIN±; deben gobernar AOUT± y la tabla DAC p. 10.
6. **Chip revision.** CS-DS p. 44 codifica revisiones A y B ambas como 0, por lo que Chip ID no permite distinguirlas.

No se encontró contradicción normativa entre la PCB inicial de dos capas y la recomendación de cuatro capas: Espressif recomienda cuatro capas, pero documenta condiciones para dos capas. Sí existe un riesgo técnico que debe revisarse antes del esquemático y layout.

## Missing Information

- Identificación física de revisión/PW del módulo que se comprará.
- Confirmación oficial de Cirrus sobre AD0 alto con VA=5 V y VL=3,3 V, únicamente para cerrar la inconsistencia documental del modo I2C no seleccionado.
- Versión exacta de ESP-IDF y configuración de los drivers I2S/USB.
- Resultados de una prueba de generación simultánea MCLK/BCLK/LRCK a 48 kHz.
- Especificaciones auditadas del receptáculo USB-C y del paquete normativo USB Type-C 2.5.
- Presupuesto de corriente completo, incluyendo placas externas y transitorios de RF.
- Requisitos eléctricos de entrada/salida de las futuras placas analógicas.
- Dimensiones mecánicas, enclosure, distancia real a antena, fabricante y stackup de PCB.
- Capacitancia del par USB una vez elegidos conectores y placement.
- Validación del proceso de soldadura manual para módulo WROOM-1 y TSSOP-28.

## Decisions Required Before Schematic

- seleccionar el regulador de 3,3 V;
- definir la corriente disponible para placas externas;
- definir el filtrado de 5 V y 3,3 V y la relación entre VA, VD y VL;
- seleccionar la protección USB/ESD;
- definir fusible o limitación de corriente;
- seleccionar el conector USB-C y revisar CC1/CC2;
- seleccionar conectores de placas hijas y sus corrientes/señales;
- aprobar el pinout definitivo, incluidos I2S, reset, mute y DEBUG_TIMING;
- aprobar la arquitectura definitiva de reloj y su fuente;
- decidir si se reserva clock externo/cristal alternativo;
- definir control/adaptación de AMUTEC y BMUTEC;
- definir estrategia de masa y alimentación de las placas analógicas;
- definir dimensiones de PCB y restricciones mecánicas de antena;
- seleccionar fabricante y stackup;
- definir el uso de Wi‑Fi/Bluetooth durante audio;
- fijar la versión exacta de ESP-IDF para validación;
- validar la biblioteca KiCad oficial de Espressif con KiCad 10;
- definir política de conexión del shield USB-C;
- validar con firmware y mediciones MCLK, BCLK, LRCK, I2S full-duplex, DMA, reset, straps Stand-Alone y USB antes de congelar el esquemático.

Hasta cerrar las decisiones requeridas y mitigar cualquier contradicción que afecte la implementación, MB-001 permanece como borrador técnico y no está autorizada para implementación en KiCad.
