Estación Meteorológica Inalámbrica
=============================

### Introducción
 
Se presentará una estación meteorológica inalámbrica hogareña, la misma consta de dos dispositivos individuales, una estación meteorológica interna que provee de valores dentro del recinto donde se encuentra, y una estación meteorológica externa que provee los valores exteriores, entre ambos dispositivos se realiza la telemetría exterior y se muestra en un display alfanumérico en la estación interior, con la finalidad de observar a primera vista los datos meteorológicos internos y externos.
Esta estación funciona de forma inalámbrica con enlaces de radiofrecuencia y funciona con pilas primarias en el caso exterior y batería recargable en el caso interior.

El proyecto surge de la idea de practicar la programación en lenguaje C, los protocolos de comunicación serie y el transporte de los datos vía enlaces inalambricos, en este proyecto se abordará la estructura necesaria para realizar una trama de datos básica, su payload y verificación.
También se realizará la puesta en marcha del sistema mediante electrónica de bajo costo y bajo consumo para afrontar el problema energético que conlleva la utilización de pilas y baterías de forma competitiva con productos comerciales de la actualidad.

----------
![enter image description here](https://raw.githubusercontent.com/electgpl/estmet/master/documentos/Estacion%20Meteorologica%20Blur.png)

----------

### Objetivos
 
- Estación meteorológica con medición interna y externa.
- La estación consiste en un sensor externo y uno interno. 
- El sensor externo posee la capacidad de medir Temperatura, Humedad y Luminosidad. 
- El sensor interno mide Temperatura y Humedad. 
- La telemetría se realiza mediante un enlace inalámbrico simplex UHF, el sensor externo funciona mediante celdas primarias alcalinas y posee electrónica de bajo consumo proporcionando una autonomía de 1 año.
- El sensor interno posee un display de LCD de 2 lineas y 16 caracteres que mostrará la Temperatura y Humedad del sensor interno, la Temperatura, Humedad y Luminosidad del sensor externo.
- El sensor interno cuenta con una batería secundaria de iones de litio recargable mediante un conector micro usb.


> **Nota:**
> - El microcontrolador utilizado se basa en core 8051 (para el cual el firmware se encuentra compilado con Keil sobre un microcontrolador de Silabs C8051F832).
> - Se ha realizado también para Microchip sobre diversas plataformas
> - La simulación se realiza para Microchip sobre Proteus.
> - Los diagramas, PCB y Gerber, son realizados con Eagle.

----------

### Diseño conceptual
 
Para realizar nuestra estación, utilizaremos microcontroladores modernos de bajo consumo y bajo costo, que permite trabajar con una buena autonomía utilizando pilas primarias para aumentar al máximo el rendimiento del sistema.
La estación meteorológica utilizara la función especial del microcontrolador para entrar en modo bajo consumo y así extender la batería.
Una vez en bajo consumo se despertara de forma automática mediante el WDT del microcontrolador cada 10 segundos, se tomará una muestra de temperatura, humedad y luminosidad, se enviará mediante el enlace de RF a la estación interna y luego se volverá a dormir entrando en bajo consumo.
El enlace se ha simplificado al máximo para reducir el costo, se utiliza modulación ASK en UHF mediante oscilador a cristal SAW de 433.92MHz y se enviaran los datos mediante UART invertida a 600bps.
En la estación interior, se utilizara la interrupción de dato presente en UART para atender la telemetría exterior, realizar el parse de la trama y dejarla disponible para la utilización.
Se tomará humedad y temperatura interior, y junto con la telemetría exterior se mostrará todo en un dsiplay LCD de 2x16 caracteres alfanuméricos.
La estación interna cuenta con baterías recargables y su controlador de carga para ser conectado directamente con un cable estándar micro usb.

----------
## Implementación
### Especificaciones de Hardware:
#### Dispositivo Interno:
- Sensor de Temperatura y Humedad, DHT11 (comunicación onWire propietaria).
- Receptor modular RWS simplex UHF operando en la banda de 70cm en 433.92MHz, modulación ASK OOK utilizando protocolo RS232, Antena del tipo microstrip IFA 50 Ohms.
- Display LCD 2x16 con controlador HD44780, para mostrar Temp, Hum, (Interna y Externa), Luminosidad (Externa).
- Cargador de Batería tipo Secundaria Li-Ion con micro USB basado en controlador TP4056, Celda 18650, 4800mAh (típico) .
- Microcontrolador de 8bit Silabs C8051F832.
- Circuito impreso hasta 2 Layer con PTH y tecnologia SMD. 
- Aceptación IPC-356, IPC-600, UL-94.

#### Dispositivo Externo:
- Sensor de Temperatura y Humedad, DHT11 (comunicación onWire propietaria).
- Sensor de Luminosidad basado en LDR con acondicionamiento por firmware.
- Transmisor modular TWS simplex UHF operando en la banda de 70cm en 433.92MHz, modulación ASK OOK utilizando protocolo RS232, Antena del tipo microstrip IFA 50 Ohms.
- Batería del tipo primaria alcalina ZnMnO2 tamaño AA, 2500mAh (típico).
- Microcontrolador de 8bit Silabs C8051F832.
- Circuito impreso hasta 2 Layer con PTH y tecnologia SMD. 
- Aceptación IPC-356, IPC-600, UL-94.

### Especificaciones de Firmware:
El firmware debe contar con estándares de programación, encabezado del programa, descripción, autores, versionado, fechas y referencias.
Las funciones deben contar con los valores de ingreso y egreso a la misma como así el tipo de datos y la descripción de la función.
El programa se realizará en lenguaje C.

- Desarrollo de Bibliotecas necesarias (Estandarizado)
- Pruebas Unitarias de cada Biblioteca con el hardware asociado
- Desarrollo de programa principal
- Manejo de errores
- Creación de diagramas de flujo o estado
- Pruebas Integrados del firmware con todas las Bibliotecas y devices

-------
- Biblioteca para sensor DHT11
-- Manejo de Bus OneWire 5 bytes (4 bytes de datos 1 byte de checksum)
-- Formateo de datos de salida Temperatura: entero sin signo de 8bit
-- Formateo de datos de salida Humedad: entero sin signo de 8bit

- Biblioteca de control del LCD
-- Mapeo de los 2 renglones y 16 caracteres.
-- Control de memoria mediante bus de 4bit y control.
-- Entrada mediante string con control xy de caracteres.

- Biblioteca bitbanging para protocolo manchester en los enlaces UHF
-- BitBanging de del protocolo.
-- Realización de Checksum
-- Salida de datos en formato hexadecimal.
-- Entrada de datos en formato hexadecimal.

- Biblioteca del sensor de luminosidad
-- Utilización del conversor analogico digital provisto por el microcontrolador.
-- Configuración de baja velocidad a resolución de 8bit.
-- Salida datos en formato entero.
- Manejo de Baja Energía
-- Apagado de dispositivos periféricos en desuso mediante transistores NMOS.
-- Reloj WDT para función WakeUp del MCU cada 2.3s
-- Si los datos a medir (sensores) no cambian respecto a la medición anterior, no se enviaran datos repetidos mejorando el consumo.

--------

### Especificaciones Técnicas:

Rango de temperatura 0-50°c (precisión ±2°c) 
Rango de humedad 20-90%RH (precisión ±5％) 
Rango de luminosidad 0-100% (precisión ±20％)    
Rango de trabajo hasta 20m Frecuencia de trabajo 433MHz 
Batería de sensor 2xAA Alcalina 
Vida útil de 2 años 
Batería de cuerpo 1x18650
Vida útil Recargable via USB 
Medidas cuerpo 113x67x27.5mm 
Medidas Sensor 91x70x32.5mm 
Gabinete exterior IPX3 
Este equipo no dispone de protección ATEX, por lo que no debe ser usado en atmósferas potencialmente explosivas (polvo, gases inflamables).

--------

### Conclusiones
 
Se ha logrado implementar una estación meteorológica hogareña de bajo consumo y bajo costo que presenta una propuesta comercial respecto a las competidoras de mismas prestaciones.

Se implementó el código necesario para realizar el proyecto sobre core 8051 bajo una plataforma moderna que opera en 16MHz y 16MIPS.
Se realizó todo el tratamiento de la trama, preámbulo, payload, checksum y parseo de datos para realizar el enlace de telemetría entre los dos dispositivos.
Se analizó la velocidad de transferencia de datos sobre UART para lograr la mejor comunicación y alcance sobre módulos de RF de bajo costo.
Se comprobó el diseño de la antena con la ayuda de instrumental adecuado “VNA” para su correcta adaptación.

--------

### Bibliografía
 
[1] “Manual 8051 Keil”, 
http://www.keil.com/support/man/docs/is51/
[2] “Manual Eagle”, 
http://hades.mech.northwestern.edu/images/b/b4/Eagle_Manual.pdf
[3] “Norma ATEX”, 
http://www.insht.es/InshtWeb/Contenidos/Normativa/GuiasTecnicas/Ficheros/ATM%C3%93SFERAS%20EXPLOSIVAS.pdf
[4] “Norma IPX3”, 
http://hades.mech.northwestern.edu/images/b/b4/Eagle_Manual.pdfhttp://www.f2i2.net/documentos/lsi/rbt/guias/guia_bt_anexo_1_sep03R1.pdf
[5] “Norma IPC610”, 
http://www.ipc.org/TOC/IPC-A-610E-Spanish.pdf
[6] “Datasheet DHT11”, 
https://akizukidenshi.com/download/ds/aosong/DHT11.pdf
[7] “Datasheet Modulos Transmisor y Receptor RF UHF”, https://4.imimg.com/data4/AJ/NM/MY-1833510/rf-transmitter-receiver-pair-433-mhz-ask.pdf
[8] “Datasheet LDR”, 
http://kennarar.vma.is/thor/v2011/vgr402/ldr.pdf
[9] “Datasheet C8051F832 Silabs”, https://www.silabs.com/documents/public/data-sheets/C8051F80x-83x.pdf
[10] “Datasheet TP4056”, https://dlnmh9ip6v2uc.cloudfront.net/datasheets/Prototyping/TP4056.pdf

