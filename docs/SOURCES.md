# Fuentes del proyecto

## Criterio y convenciones

Este documento es el catálogo documental principal del proyecto. Las fuentes oficiales y vigentes del fabricante tienen prioridad sobre cualquier fuente secundaria. Los documentos locales de fabricantes son copias de solo lectura y su presencia no sustituye la consulta de la URL oficial antes de tomar una decisión de diseño.

Estados de auditoría usados: `VERIFIED_OFFICIAL`, `OFFICIAL_VERSION_UNCONFIRMED`, `OFFICIAL_OUTDATED`, `OFFICIAL_REFERENCE_DESIGN`, `WEB_PAGE_EXPORTED_TO_PDF`, `THIRD_PARTY_REFERENCE`, `DUPLICATE`, `CORRUPT`, `UNRELATED` y `REQUIRES_HUMAN_REVIEW`.

Prioridades para recursos faltantes: `REQUIRED_BEFORE_SCHEMATIC`, `REQUIRED_BEFORE_PCB`, `REQUIRED_BEFORE_FIRMWARE`, `FUTURE_REFERENCE` y `OPTIONAL`.

## Documentos locales auditados

Todos los archivos de esta tabla tienen firma PDF válida, pudieron procesarse y producir texto legible. El tamaño está expresado en bytes y el hash usa SHA-256.

| ID | título real | fabricante | categoría | versión o revisión | fecha | páginas | tamaño | ruta local | URL oficial | SHA-256 | estado | uso previsto | observaciones |
|---|---|---|---|---|---|---:|---:|---|---|---|---|---|---|
| LOC-001 | CS4272, 24-Bit, 192 kHz Stereo Audio CODEC | Cirrus Logic | NORMATIVE_DATASHEET | DS593F2 / F2 | 2021-10 | 54 | 1113522 | `documentation/official/cirrus_logic/CS4272_DS593F2_OCT2021.pdf` | https://statics.cirrus.com/pubs/proDatasheet/CS4272_F2.pdf | `2131186726423740C97D6577A7D9D72AA673AEB61EB0A569E55A572F17D324FE` | VERIFIED_OFFICIAL | Requisitos eléctricos, interfaces, alimentación, reloj, control y audio del CS4272. | Portada, código DS593F2 e historial F2 de octubre de 2021 confirmados. |
| LOC-002 | ESP32-S3 Series SoC Errata | Espressif Systems | OFFICIAL_ERRATA | v1.3 | 2025-03-31 | 15 | 629088 | `documentation/official/espressif/ESP32-S3_Chip_Errata.pdf` | https://docs.espressif.com/projects/esp-chip-errata/en/latest/esp32s3/esp-chip-errata-en-master-esp32s3.pdf | `D8B6169B0E48E065494D9BED062A75617315F61AEA8936A455E71E459883B43D` | VERIFIED_OFFICIAL | Identificar revisiones afectadas y restricciones del ESP32-S3 antes del diseño y firmware. | Historial de revisión legible; versión y fecha confirmadas. |
| LOC-003 | ESP32-S3 Hardware Design Guidelines | Espressif Systems | OFFICIAL_DESIGN_GUIDE | Release master; historial v1.8 | PDF generado 2026-07-06; v1.8 de 2025-07-03 | 42 | 4191598 | `documentation/official/espressif/ESP32-S3_Hardware_Design_Guidelines.pdf` | https://docs.espressif.com/projects/esp-hardware-design-guidelines/en/latest/esp32s3/esp-hardware-design-guidelines-en-master-esp32s3.pdf | `11C9149E505993C0E7E793C386CCD2A9E2436784A684204BB3116A7EC2AAA2E3` | VERIFIED_OFFICIAL | Checklist de esquemático, alimentación, reset, RF, USB y recomendaciones de PCB. | Exportación oficial de la rama `master`; registrar la fecha de generación además de la última revisión numerada. |
| LOC-004 | ESP32-S3 Series Datasheet | Espressif Systems | NORMATIVE_DATASHEET | v2.2 | 2026-03-05 | 87 | 1098115 | `documentation/official/espressif/ESP32-S3_Series_Datasheet.pdf` | https://www.espressif.com/sites/default/files/documentation/esp32-s3_datasheet_en.pdf | `2D5A7CB7FD559D8D972BD88DB32669C0196D23F22D7AFAAFB0F63D099B589A3F` | VERIFIED_OFFICIAL | Especificaciones del SoC, GPIO, periféricos, alimentación y límites eléctricos. | Portada e historial v2.2 confirmados. |
| LOC-005 | ESP32-S3 Technical Reference Manual | Espressif Systems | OFFICIAL_TECHNICAL_MANUAL | v1.8 | 2026-03-04 | 1531 | 15215232 | `documentation/official/espressif/ESP32-S3_Technical_Reference_Manual.pdf` | https://documentation.espressif.com/esp32-s3_technical_reference_manual_en.pdf | `4484BF8A69035EC42A731C58C64ADA6FBD1F1618C5559409F134D9EA083F444F` | VERIFIED_OFFICIAL | Detalle de periféricos y registros para validación de bajo nivel y firmware futuro. | Portada e historial v1.8 confirmados. |
| LOC-006 | ESP32-S3 esp-dev-kits Documentation | Espressif Systems | OFFICIAL_REFERENCE_DESIGN | Release master | PDF generado 2026-07-14 | 118 | 23428125 | `documentation/official/espressif/ESP32-S3-DevKitC-1_V1.1_Reference_Design.pdf` | https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32s3/esp32-s3-devkitc-1/user_guide_v1.1.html | `4B09DFC756708E0DC33C53B042AFB66997B4D215893FA4B2AD8907213F1B83A2` | WEB_PAGE_EXPORTED_TO_PDF | Consulta complementaria de placas de desarrollo y enlaces a diseños oficiales. | No es un reference design único: es una exportación agregada de la documentación `esp-dev-kits` para varias placas. El nombre local puede inducir a error; no se renombró. |
| LOC-007 | I2S bus specification | NXP Semiconductors | INDUSTRY_SPECIFICATION | UM11732 Rev. 3.0 | 2022-02-17 | 14 | 204649 | `documentation/official/nxp/I2S_Bus_Specification_UM11732_Rev3.pdf` | https://www.nxp.com/docs/en/user-manual/UM11732.pdf | `905D20F3CD7C38F8118A5FAB0AA7F7A65D0FB8608681AF6D8B6156842E603F53` | VERIFIED_OFFICIAL | Referencia normativa complementaria para formato y temporización del bus I2S. | Título, identificador, revisión, fecha y 14 páginas coinciden con la fuente oficial. |
| LOC-008 | CDB4272 Evaluation Board for CS4272 | Cirrus Logic | OFFICIAL_REFERENCE_DESIGN | DS593DB2 | 2003-09 | 29 | 1847905 | `documentation/reference_designs/cirrus_logic/CDB4272_Evaluation_Board.pdf` | https://statics.cirrus.com/pubs/rdDatasheet/CDB4272-2.pdf | `66EB45D50E3E4FE8A3A41EB9592F36A39607A5FC7951E4AAC1AF6960B8A93FF9` | OFFICIAL_REFERENCE_DESIGN | Comparar implementación, desacoplamiento, grounding e interfaces analógicas del CS4272. | Diseño oficial de evaluación; no es especificación normativa de la placa Pedales. Incluye esquemáticos y layout de referencia. |
| LOC-009 | ESP32-S3-DevKitC-1 Schematic | Espressif Systems | OFFICIAL_REFERENCE_DESIGN | V1.1 | 2022-04-13 | 2 | 367119 | `documentation/reference_designs/espressif/ESP32-S3-DevKitC-1_V1.1_Schematic.pdf` | https://dl.espressif.com/dl/schematics/SCH_ESP32-S3-DevKitC-1_V1.1_20220413.pdf | `5F25AE344DD67B9C1C503570ED4BCB3FFD34E99C6931210FC501A9A26683E496` | OFFICIAL_REFERENCE_DESIGN | Referencia de alimentación, USB, programación y uso del módulo en una placa oficial. | Título, revisión V1.1 y fecha 2022-04-13 confirmados. La hoja 2 usa ESP32-S3-WROOM-1 como opción de módulo. No es un datasheet normativo. |
| LOC-010 | ESP32-S3-WROOM-1 & ESP32-S3-WROOM-1U Datasheet | Espressif Systems | NORMATIVE_DATASHEET | v1.8 | 2026-03-02 | 53 | 1280501 | `documentation/reference_designs/espressif/ESP32-S3-WROOM-1_WROOM-1U_Datasheet_v1.8.pdf` | https://documentation.espressif.com/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf | `27D71971DA07C280C6068D08C74720D1A25B8F20CF8494DC1765BDD28D40D435` | VERIFIED_OFFICIAL | Fuente normativa del módulo, pinout, variantes, alimentación, dimensiones, land pattern y keepout. | Incluye ESP32-S3-WROOM-1-N16R8: 16 MB de Flash Quad SPI y 8 MB de PSRAM Octal SPI. Está almacenado bajo `reference_designs`, aunque su categoría real es datasheet normativo; no se movió. |

## Fuentes oficiales registradas sin copia local

Estas fuentes se consultan en línea. No es necesario convertirlas a PDF ni almacenar copias durante la fase actual.

| ID | título | organización | categoría | URL oficial | uso previsto |
|---|---|---|---|---|---|
| WEB-001 | CS4272 Product Page | Cirrus Logic | NORMATIVE_DATASHEET | https://www.cirrus.com/products/cs4272 | Página canónica del producto y punto de acceso a documentación vigente. |
| WEB-002 | ESP32-S3 Hardware Reference Index | Espressif Systems | OFFICIAL_TOOL_DOCUMENTATION | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/hw-reference/index.html | Índice canónico de datasheet, TRM, guía, errata, módulos y placas. |
| WEB-003 | ESP32-S3-DevKitC-1 v1.1 User Guide | Espressif Systems | OFFICIAL_REFERENCE_DESIGN | https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32s3/esp32-s3-devkitc-1/user_guide_v1.1.html | Referencia de placa y enlaces a esquema, PCB, dimensiones y paquete de diseño. |
| WEB-004 | Espressif KiCad Library | Espressif Systems | OFFICIAL_TOOL_DOCUMENTATION | https://github.com/espressif/kicad-libraries | Recurso oficial pendiente de validar contra la versión de KiCad instalada antes de utilizarlo. |
| WEB-005 | ESP-IDF I2S API for ESP32-S3 | Espressif Systems | OFFICIAL_SOFTWARE_DOCUMENTATION | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-reference/peripherals/i2s.html | Configuración futura del periférico I2S. |
| WEB-006 | ESP-IDF `i2s_std` Example | Espressif Systems | OFFICIAL_SOFTWARE_DOCUMENTATION | https://github.com/espressif/esp-idf/blob/master/examples/peripherals/i2s/i2s_basic/i2s_std/main/i2s_std_example_main.c | Ejemplo oficial futuro de I2S estándar. |
| WEB-007 | USB Serial/JTAG Console | Espressif Systems | OFFICIAL_SOFTWARE_DOCUMENTATION | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-guides/usb-serial-jtag-console.html | Programación, consola y diagnóstico por USB nativo. |
| WEB-008 | USB Device Stack | Espressif Systems | OFFICIAL_SOFTWARE_DOCUMENTATION | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-reference/peripherals/usb_device.html | Desarrollo futuro de dispositivo USB. |
| WEB-009 | GPIO API for ESP32-S3 | Espressif Systems | OFFICIAL_SOFTWARE_DOCUMENTATION | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-reference/peripherals/gpio.html | Configuración futura de GPIO y validación del pinout aprobado. |
| WEB-010 | KiCad 10 Schematic Editor | KiCad | OFFICIAL_TOOL_DOCUMENTATION | https://docs.kicad.org/10.0/en/eeschema/eeschema.html | Referencia oficial para esquemáticos futuros. |
| WEB-011 | KiCad 10 PCB Editor | KiCad | OFFICIAL_TOOL_DOCUMENTATION | https://docs.kicad.org/10.0/en/pcbnew/pcbnew.html | Referencia oficial para PCB futura. |
| WEB-012 | KiCad 10 Command-Line Interface | KiCad | OFFICIAL_TOOL_DOCUMENTATION | https://docs.kicad.org/10.0/en/cli/cli.html | Automatización y verificaciones futuras autorizadas. |
| WEB-013 | KiCad 10 Manual | KiCad | OFFICIAL_TOOL_DOCUMENTATION | https://docs.kicad.org/10.0/en/kicad/kicad.html | Manual general de la herramienta. |
| WEB-014 | USB Type-C Cable and Connector Specification, Release 2.5 | USB-IF | FUTURE_REFERENCE | https://www.usb.org/document-library/usb-type-cr-cable-and-connector-specification-release-25 | Referencia futura para conector, detección de corriente y requisitos USB-C. |
| WEB-015 | USB 2.0 Specification | USB-IF | FUTURE_REFERENCE | https://www.usb.org/document-library/usb-20-specification | Referencia futura para la interfaz USB 2.0. |

## Documentos faltantes o que requieren revisión

| ID | recurso | prioridad | estado | acción requerida |
|---|---|---|---|---|
| PEND-001 | ESP32-S3-DevKitC-1 v1.1 Reference Design ZIP | OPTIONAL | PENDIENTE | El user guide oficial enlaza un ZIP de diseño. No fue descargado ni es necesario mientras el esquema PDF local sea suficiente; auditar su contenido si se incorpora manualmente. |
| PEND-002 | Biblioteca KiCad oficial de Espressif | REQUIRED_BEFORE_SCHEMATIC | REQUIRES_HUMAN_REVIEW | Validar compatibilidad con la versión de KiCad instalada antes de instalar o copiar símbolos y footprints. |
| PEND-003 | USB Type-C Cable and Connector Specification | FUTURE_REFERENCE | PENDIENTE | Consultar la versión oficial vigente antes de diseñar la futura hoja USB-C; no se descargó el paquete. |
| PEND-004 | USB 2.0 Specification | FUTURE_REFERENCE | PENDIENTE | Consultar la versión oficial vigente antes de diseñar la interfaz USB; no se descargó el paquete. |
| PEND-005 | Documentación ESP-IDF registrada como WEB-005 a WEB-009 | REQUIRED_BEFORE_FIRMWARE | PENDIENTE | Confirmar la versión estable de ESP-IDF elegida antes del firmware; las páginas web ya están catalogadas y no requieren copia local. |

## Duplicados, versiones antiguas o archivos problemáticos

No se detectaron archivos duplicados por SHA-256, archivos corruptos ni PDFs que fueran páginas HTML renombradas.

| ID | archivo | clasificación | observación |
|---|---|---|---|
| PROB-001 | `documentation/official/espressif/ESP32-S3-DevKitC-1_V1.1_Reference_Design.pdf` | WEB_PAGE_EXPORTED_TO_PDF | Exportación oficial agregada de `esp-dev-kits`, no un paquete de reference design específico. Requiere conservar esta distinción al consultarlo. |
| PROB-002 | `documentation/reference_designs/espressif/ESP32-S3-WROOM-1_WROOM-1U_Datasheet_v1.8.pdf` | REQUIRES_HUMAN_REVIEW | El contenido es oficial y está verificado, pero la ubicación bajo `reference_designs` no coincide con su categoría normativa. No se movió ni renombró. |

Los proyectos de inspiración de terceros pueden aportar ideas, pero no son fuentes normativas y deberán validarse siempre contra las fuentes primarias registradas aquí.
