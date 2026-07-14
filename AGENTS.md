# AGENTS.md

## 1. Propósito del archivo

Este archivo contiene las reglas permanentes de trabajo para cualquier agente de IA que opere dentro del proyecto Pedales.

Debe leerse antes de analizar, crear o modificar cualquier archivo del proyecto.

Las instrucciones de una tarea puntual complementan este archivo, pero no autorizan a ignorar sus restricciones.

---

## 2. Descripción general del proyecto

Pedales es un proyecto escalable para desarrollar una plataforma modular de procesamiento de audio para guitarra eléctrica.

El proyecto podrá incluir:

- varias placas electrónicas;
- una mainboard digital;
- diferentes etapas de entrada;
- diferentes preamplificadores;
- diferentes etapas de salida;
- placas de controles;
- fuentes de alimentación;
- circuitos analógicos experimentales;
- firmware de validación;
- drivers de hardware;
- bloques DSP;
- efectos digitales;
- aplicaciones de un solo efecto;
- aplicaciones multiefectos.

No debe asumirse que todo el proyecto terminará integrado en una única PCB.

La arquitectura deberá permitir probar y comparar diferentes soluciones de hardware y firmware.

---

## 3. Primer hardware previsto

La primera placa prevista es una mainboard de desarrollo basada en:

- ESP32-S3-WROOM-1-N16R8;
- CODEC de audio CS4272;
- USB-C para alimentación y programación mediante USB nativo;
- comunicación I2S para audio;
- comunicación I2C para configuración y diagnóstico del CODEC;
- entradas y salidas analógicas diferenciales expuestas mediante conectores;
- placas analógicas externas intercambiables.

La mainboard no incluirá inicialmente:

- preamplificador de guitarra;
- jacks de audio;
- amplificador de auriculares;
- conversión diferencial a single-ended;
- circuitos de distorsión analógica;
- etapa de salida final;
- controles de usuario completos.

Estas funciones se diseñarán posteriormente como circuitos o placas independientes.

---

## 4. Parámetros técnicos iniciales

Los parámetros iniciales aprobados son:

- módulo: ESP32-S3-WROOM-1-N16R8;
- CODEC: CS4272;
- frecuencia de muestreo inicial: 48 kHz;
- resolución efectiva: 24 bits;
- slots I2S previstos: 32 bits;
- ESP32-S3 como maestro de audio inicial;
- CS4272 como esclavo inicial;
- I2S para transportar audio;
- I2C para configurar y leer registros del CS4272;
- alimentación analógica VA del CS4272: 5 V;
- alimentación digital VD del CS4272: 3,3 V;
- alimentación lógica VL del CS4272: 3,3 V;
- alimentación del ESP32-S3: 3,3 V;
- primera PCB: dos capas;
- posible revisión futura: cuatro capas;
- ensamblado manual;
- preferencia por componentes 0805;
- uso de 0603 únicamente cuando exista una razón técnica o de espacio.

Estos parámetros pueden cambiar solamente mediante una decisión explícita documentada.

---

## 5. Estado actual del proyecto

La fase actual es:

DOCUMENTACIÓN Y DEFINICIÓN DE REQUISITOS.

En esta fase está prohibido comenzar por iniciativa propia:

- esquemáticos;
- PCB;
- símbolos;
- footprints;
- simulaciones;
- firmware;
- scripts de producción;
- archivos de fabricación.

La creación de cualquiera de esos elementos requiere una tarea explícita posterior.

---

## 6. Método obligatorio de trabajo

El proyecto debe desarrollarse mediante tareas pequeñas y verificables.

Reglas:

1. Trabajar en una sola tarea por vez.
2. Leer primero AGENTS.md y los documentos relevantes de docs.
3. Inspeccionar los archivos existentes antes de modificarlos.
4. Respetar estrictamente el alcance de la tarea.
5. No comenzar automáticamente la siguiente etapa.
6. No modificar archivos no autorizados.
7. No redefinir la arquitectura por iniciativa propia.
8. No sustituir componentes sin autorización.
9. No inventar conexiones, valores, requisitos ni datos técnicos.
10. Informar cualquier duda, contradicción o información faltante.
11. Detenerse cuando la tarea solicitada haya terminado.
12. Informar exactamente qué archivos se crearon o modificaron.

Para tareas complejas, antes de implementar se deberá presentar:

- interpretación del objetivo;
- archivos que se modificarían;
- plan de trabajo;
- dudas bloqueantes;
- criterios de finalización.

La implementación comenzará solamente cuando la tarea lo autorice.

---

## 7. División progresiva del trabajo

El proyecto se ampliará únicamente cuando sea necesario.

No deben crearse de forma preventiva decenas de carpetas, proyectos vacíos o documentos sin utilidad actual.

La evolución prevista será aproximadamente:

1. documentación general;
2. recopilación de fuentes;
3. requisitos del primer hardware;
4. arquitectura;
5. división del esquemático en hojas;
6. diseño y revisión de una hoja por vez;
7. validación del esquemático;
8. asignación y revisión de footprints;
9. placement de PCB por bloques;
10. ruteo por grupos de señales;
11. validación y fabricación;
12. firmware de bring-up;
13. circuitos analógicos externos;
14. efectos DSP y aplicaciones.

Esta lista describe una dirección general y no concede permiso para iniciar ninguna etapa.

---

## 8. Reglas para futuros esquemáticos

Cuando comience el diseño electrónico:

- el esquemático deberá dividirse en bloques u hojas manejables;
- se trabajará en una hoja por tarea;
- cada hoja deberá tener una responsabilidad clara;
- las interfaces entre hojas deberán documentarse;
- una hoja validada no podrá modificarse sin autorización;
- el PCB no comenzará hasta que el esquemático esté aprobado;
- ERC cero no demuestra por sí solo que el circuito sea eléctricamente correcto;
- toda conexión no evidente deberá contrastarse con documentación técnica;
- los esquemáticos deberán ser legibles y no contener textos superpuestos.

---

## 9. Reglas para futuros PCB

Cuando comience el PCB:

- no se cambiará la lógica del esquemático desde el editor PCB;
- el placement se validará antes del ruteo;
- el ruteo se realizará por clases funcionales;
- las conexiones abiertas deberán quedar en cero antes de fabricar;
- DRC cero no demuestra por sí solo buen layout;
- deberán revisarse retornos de corriente, planos, desacoplamientos y keepouts;
- el keepout de la antena del ESP32 deberá ser real y aplicarse a todas las capas;
- los relojes digitales deberán mantenerse alejados de nodos analógicos sensibles;
- la serigrafía deberá ser legible y útil para montaje y diagnóstico.

---

## 10. Reglas para futuro firmware

Cuando comience el firmware:

- se utilizará ESP-IDF como base inicial;
- el soporte de hardware deberá separarse de los algoritmos DSP;
- los efectos deberán poder probarse de forma modular;
- el pinout oficial no podrá cambiarse solo desde el código;
- todo cambio de GPIO deberá actualizar la documentación correspondiente;
- deberán conservarse logs de consola;
- se reservará un GPIO para mediciones temporales con osciloscopio;
- deberán registrarse errores de I2S, underrun, overrun y fallos del CODEC;
- las pruebas de hardware deberán preceder al desarrollo de efectos complejos.

---

## 11. Jerarquía de fuentes técnicas

Usar las fuentes en este orden de prioridad:

1. datasheet oficial y vigente del fabricante;
2. errata oficial;
3. hardware design guidelines oficiales;
4. technical reference manual oficial;
5. placas de evaluación y reference designs oficiales;
6. application notes oficiales;
7. documentación oficial de KiCad o ESP-IDF;
8. artículos técnicos de fabricantes reconocidos;
9. proyectos abiertos de terceros;
10. foros, blogs, videos y publicaciones comerciales.

Los proyectos, foros y videos pueden aportar ideas, pero no sustituyen una especificación oficial.

Cuando dos fuentes se contradigan:

- no elegir arbitrariamente;
- registrar la contradicción;
- identificar versión y fecha;
- solicitar revisión antes de implementar.

---

## 12. Gestión de documentación

Cada documento debe tener una función clara.

- README.md: introducción general.
- docs/PROJECT_SCOPE.md: alcance y límites.
- docs/REQUIREMENTS.md: requisitos vigentes.
- docs/ARCHITECTURE.md: división funcional del sistema.
- docs/DESIGN_DECISIONS.md: decisiones aprobadas y justificación.
- docs/SOURCES.md: índice de documentación y referencias.
- AGENTS.md: reglas de trabajo para agentes de IA.

No duplicar información técnica en múltiples documentos.

Cuando sea necesario repetir un dato, referenciar su fuente principal.

No presentar una propuesta como decisión aprobada.

Distinguir claramente entre:

- APROBADO;
- PROPUESTO;
- PENDIENTE;
- DESCARTADO;
- REQUIERE VALIDACIÓN.

---

## 13. Gestión de archivos

- Trabajar únicamente dentro de la carpeta raíz del proyecto.
- No borrar archivos sin autorización.
- No mover archivos validados sin autorización.
- No crear copias con nombres ambiguos como final, final2 o nuevo.
- Usar nombres descriptivos y estables.
- No generar archivos temporales dentro del repositorio.
- No modificar documentación oficial almacenada en references.
- Los documentos de references deben tratarse como archivos de solo lectura.
- No descargar documentos duplicados sin comprobar antes su existencia.

---

## 14. Honestidad de validación

No declarar que un diseño está validado solamente porque:

- abre en KiCad;
- ERC no informa errores;
- DRC no informa errores;
- compila;
- genera Gerbers;
- una simulación converge.

Distinguir entre:

- creado;
- implementado;
- revisado;
- verificado por herramienta;
- verificado contra datasheet;
- probado físicamente;
- validado.

Toda limitación o validación pendiente debe informarse explícitamente.

---

## 15. Formato de respuesta al terminar una tarea

La respuesta final deberá incluir:

1. resumen del trabajo;
2. archivos creados;
3. archivos modificados;
4. archivos inspeccionados;
5. decisiones o supuestos realizados;
6. validaciones ejecutadas;
7. advertencias o pendientes;
8. confirmación de que no se trabajó fuera del alcance;
9. ruta absoluta del proyecto.

No afirmar que una tarea está completa si existen errores que impiden cumplir su objetivo.

---

## 16. Regla final

Ante una duda técnica importante:

DETENERSE, DOCUMENTARLA Y SOLICITAR REVISIÓN.

No resolver silenciosamente una incertidumbre mediante una suposición.

---

