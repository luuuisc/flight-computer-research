# Teoría de cohetería experimental (base para aviónica)

> **Objetivo.** Establecer un lenguaje común y definir **qué medir** para justificar **qué sensores** y **cómo** integrarlos en la computadora de vuelo.

---

## 1) Componentes de un cohete (vista de alto nivel)

### 1.1 Estructura / fuselaje
- **Tubo de cuerpo (airframe).** Envolvente primaria que aloja motor, aviónica y recuperación; dimensionado para cargas aerodinámicas y de eyección. [Partes de un cohete – NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/rocket-parts/).
- **Mamparos (*bulkheads*).** Discos estructurales que cierran/separan compartimentos (p. ej., bahía de aviónica), protegen a la electrónica y transmiten cargas de recuperación. (Ver prácticas de bahía de aviónica, más abajo.)
- **Uniones (*couplers*).** Tramos internos que empalman secciones del fuselaje y permiten formar bahías estancas (aviónica/payload).
- **Bahía de aviónica.** Compartimento sellado y ventilado (puertos barométricos) que alberga altímetro/computadora, baterías y cableado; debe impedir ingreso directo de gases de eyección. (Ver “Altímetros comerciales” para referencias de montaje).

### 1.2 Aerodinámica
- **Cono de nariz.** Geometría de proa que reduce arrastre; su forma influye en el \(C_D\) del vehículo. [Aerodinámica de cohetes – NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/rocket-aerodynamics/).
- **Aletas (o *fin can*).** Superficies estabilizadoras que proporcionan estabilidad direccional; un *fin can* es el conjunto de aletas preensambladas. [Condición de estabilidad – NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/rocket-stability/).
- **Alineación CP–CG.** El centro de presión (**CP**) debe quedar por detrás (a popa) del centro de gravedad (**CG**) para asegurar estabilidad. [Centro de presión – NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/rocket-center-of-pressure/).

### 1.3 Propulsión
- **Motor (sólido COTS o SRAD; híbrido).** Aporta el empuje durante el **ascenso propulsado**; al agotarse, el cohete entra en **ascenso inercial** hasta el apogeo. [Cuatro fuerzas – NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/four-rocket-forces/).

> [!NOTE]
> [Guía de cohetes – NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/guide-to-rockets/)

### 1.4 Recuperación
- **Doble despliegue (dual-deployment).** Dos eventos de eyección: **drogue** en **apogeo** (descenso estable y más rápido) y **paracaídas principal** a baja altitud (p. ej., 150–300 m AGL) para aterrizaje suave. Introducción: [Guía NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/guide-to-rockets/).
- **Compartimentos de recuperación.** Alojan paracaídas, protectores térmicos, cordajes; requieren **pruebas en tierra** para dimensionar la carga de eyección.
- **Cargas de eyección.** Pequeñas cargas de pólvora negra activadas por **canales pirotécnicos**; generan sobrepresión para separar secciones y liberar paracaídas. Ejemplos operativos en manuales de altímetros abajo.

### 1.5 Aviónica
- **Computadora de vuelo / altímetro.** Integra sensórica (barómetro, IMU; opcional GNSS), **canales pirotécnicos** para eyección, registro de datos y, si aplica, telemetría. 
- **Canales pirotécnicos.** Salidas eléctricas que energizan encendedores (e-matches) para detonar **cargas de eyección** en apogeo y a baja altitud. (Ver manuales: [StratoLoggerCF](http://www.perfectflite.com/altimeters.html), [Raven](https://www.featherweightaltimeters.com/uploads/1/0/9/5/109510427/raven_users_manual_2014may20.pdf), [Blue Raven](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)).
- **Telemetría/descarga de datos.** Algunos sistemas ofrecen captura inercial de alta tasa y app de configuración/descarga (p. ej., [Blue Raven – producto](https://www.featherweightaltimeters.com/blue-raven-altimeter.html)).

> [!IMPORTANT]
> En aviación, el **GNSS** (Sistema Global de Navegación por Satélite) es un sistema de satélites que provee información precisa de posicionamiento, navegación y cronometraje para guiar aeronaves en todas las fases de vuelo.

### 1.6 Alimentación
- **Baterías (LiPo/Li-ion/9 V).** Deben cubrir consumo continuo y picos para accionar e-matches; selección en función de tensiones admitidas y corriente de salida del altímetro/computadora. (Ver especificaciones de cada altímetro)

### 1.7 Carga útil (si aplica)
- **Payload.** Instrumentación/experimentos no vinculados al despliegue; si controla la eyección, se considera aviónica y debe ir en bahía adecuada.

---

## 2) Fases típicas del vuelo

1. **Pre-lanzamiento:** armado, verificación de continuidad piro y verticalidad.  
2. **Ignición y ascenso propulsado:** empuje > peso; aceleración positiva.  
3. **Ascenso inercial (coast):** motor agotado; ascenso por inercia hasta apogeo.  
4. **Apogeo:** velocidad ≈ 0; disparo del **drogue**.  
5. **Descenso bajo drogue:** descenso estable con menor carga de apertura del principal.  
6. **Despliegue principal:** a baja altitud (p. ej., 150–300 m AGL).  
7. **Aterrizaje y recuperación.**

Referencia: [Guía de cohetes – NASA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/guide-to-rockets/).

---

## 3) Variables a medir (directas y derivadas)

**Directas (sensores típicos)**  
- **Aceleración 3-ejes (IMU).** Detección de despegue/burnout y estimación de velocidad/altitud por integración. Rangos típicos programables: ±2 a ±16 g; giroscopio ±250 a ±2000 °/s. (Ej. IMU robusta a vibración: [Bosch BMI088 – datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmi088-ds001.pdf)).  
- **Velocidad angular 3-ejes (giroscopio).** Rolido/oscilaciones y eventos anómalos.  
- **Magnetómetro.** Rumbo; soporte a fusión 9-ejes (p. ej., [ICM-20948 – datasheet](https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf)).  
- **Presión barométrica / altitud.** Cálculo de apogeo y ventanas; altímetros comerciales registran ~**20 Hz** (altitud, temperatura, voltaje): [StratoLoggerCF – manual](http://www.perfectflite.com/Downloads/StratoLoggerCF%20manual.pdf).  
- **GNSS.** Lat/long/alt y velocidad para recuperación (p. ej., [u-blox NEO-M9N – datasheet](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)).  
- **Temperatura.** Compensaciones (baro/IMU) y ambiente.  
- **Voltaje de batería y continuidad piro.** Salud del sistema y habilitación de eventos.

**Derivadas / estimadas**  
- **Velocidad** y **altura** integradas (IMU+baro), **presión dinámica**, **Mach estimado**, **apogeo previsto**.
- **Eventos:** burnout (umbral inercial); apogeo cuando $\frac{dh}{dt} \approx 0$; altitud del paracaídas principal; e **inhibiciones** (ventanas de seguridad).

---

## 4) Orden de magnitud y requisitos de muestreo (perfil 500–1000 m AGL)

> [!NOTE]
> AGL es una abreviatura del inglés "Above Ground Level", que significa "Sobre el Nivel del Suelo"

- **Cadencias recomendadas (fusión sensorial)**  
  - **IMU:** **200–1000 Hz** para captar dinámica rápida y burnout. Ejemplo moderno con alta tasa: [Blue Raven – manual (500 Hz inercial)](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf).  
  - **Barométrico:** **≈20–50 Hz**, coherente con COTS ~**20 Hz**: [StratoLoggerCF – 20 Hz](http://www.perfectflite.com/SLCF.html).  
  - **GNSS:** **1–10 Hz**, según módulo (p. ej., [NEO-M9N – datasheet](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf) / [NEO-M8 – datasheet](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)).
- **Rangos recomendados**  
  - **IMU:** ≥ ±16 g y ±2000 °/s; añadir **high-g** si T/W o transitorios lo justifican.  
  - **Baro:** estabilidad térmica y **resolución submétrica** (p. ej., [MS5611 – ~10 cm, datasheet](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036); alternativa moderna: [BMP388 – datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmp388-ds001.pdf)).

---

## 5) Ecuaciones

**Sumatoria de fuerzas (eje de vuelo)**  
$$
T - D - W = m\,a
$$

**Arrastre aerodinámico**  
$$
D = \tfrac{1}{2}\,\rho\,V^2\,C_D\,A
$$

**Presión dinámica**  
$$
\bar{q} = \tfrac{1}{2}\,\rho\,V^2
$$

Referencia de fuerzas y aerodinámica: [Guías NASA BGA](https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/learn-about-aerodynamics/).

---

## 6) Descripción técnica de componentes

### 6.1 Sensores barométricos (altitud)
- **TE MS5611-01BA03.** Altímetro de **alta resolución (~10 cm)**; ADC 24-bit; SPI/I²C; bajo consumo. 
  - **Uso:** apogeo y ventana del principal. [Datasheet (TE)](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036).  
- **Bosch BMP388.** Barómetro digital con precisión submétrica (~0.5 m típica), buen TCO; SPI/I²C. 
  - **Uso:** alternativa moderna y de bajo consumo. [Datasheet (Bosch)](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmp388-ds001.pdf).
  > [!IMPORTANT]
  > TCO significa Temperature Coefficient of Offset (coeficiente térmico de offset). Un TCO bajo reduce la deriva térmica de la altitud. El BMP388 está pensado para medición de altura estable en un rango amplio de temperatura

### 6.2 IMU / inerciales
- **Bosch BMI088 (6-ejes).** IMU con **alta inmunidad a vibración**; SPI/I²C; VDDIO 1.2–3.6 V. 
  - **Uso:** aceleración/velocidad angular robustas; detección de burnout.
    - [Datasheet (Bosch)](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmi088-ds001.pdf).  
- **TDK ICM-20948 (9-ejes + DMP).** Acel/Gyro/Mag con procesador de movimiento; SPI/I²C.
  - **Uso:** estimación de actitud/rumbo con fusión 9-ejes. 
    - [Datasheet (TDK)](https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf).

### 6.3 GNSS (recuperación y validación de trayectoria)
- **u-blox NEO-M9N.** Recepción concurrente de **4 GNSS**; mitigación de *jamming/spoofing*.
  > [!NOTE]
  > **Jamming / Spoofing**
  > - **Jamming:** interferencia RF (intencional o accidental) en la banda GNSS que eleva el ruido y bloquea o degrada la recepción.
  > - **Spoofing:** transmisión de señales GNSS falsas pero coherentes que engañan al receptor y desvían su posición/tiempo.

  - **Uso:** localización y datos de trayectoria. 
    - [Datasheet](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)
    - [Integration Manual](https://content.u-blox.com/sites/default/files/NEO-M9N_Integrationmanual_UBX-19014286.pdf)
    - [Página producto](https://www.u-blox.com/en/product/neo-m9n-module).  
- **u-blox NEO-M8 (familia).** Recepción concurrente de **hasta 3 GNSS**; alta sensibilidad. 
  - **Uso:** alternativa probada y económica. 
    - [Datasheet](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)
    - [Resumen de producto](https://www.u-blox.com/en/product/neo-m8-series).  
- **Quectel L76/L76-LB.** Módulos Multi-GNSS compactos, con guías HW/protocolo. 
  - **Uso:** solución económica con documentación.
    - [Página producto L76](https://www.quectel.com/product/gnss-l76/)
    - [Hardware Design (PDF)](https://forums.quectel.com/uploads/short-url/jZHd2ObxH4FndArQyE3pqu7ZZ7e.pdf)
    - [Protocol Spec (PDF)](https://www.quectel.com/content/uploads/2024/04/Quectel_L76-LBL26-LBLC86L_GNSS_Protocol_Specification_V1.1.pdf).

### 6.4 Altímetros / computadoras comerciales
- **PerfectFlite StratoLoggerCF.** Registra **20 Hz** (altitud, temperatura, voltaje) y controla eyección (drogue/main). 
  - **Uso:** referencia COTS clásica; útil para ventanas/inhibiciones
    - [Manual (PDF)](http://www.perfectflite.com/SLCF.html)  
- **Featherweight Raven (clásico).** hasta **200 Hz**, baro **20 Hz**; 4 salidas piro. 
  - **Uso:** lógica avanzada y tamaño compacto. 
    - [Manual (PDF)](https://www.featherweightaltimeters.com/uploads/1/0/9/5/109510427/raven_users_manual_2014may20.pdf)
    - [Página producto](https://www.featherweightaltimeters.com/raven-altimeter.html).  
- **Featherweight Blue Raven.** Inercial hasta **500 Hz** y registro de eventos a **50 Hz**; app de configuración/descarga. 
  - **Uso:** ejemplo moderno de alta tasa.
    - [Manual (PDF)](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)
    - [Página producto](https://www.featherweightaltimeters.com/blue-raven-altimeter.html).

### 6.5 Canales pirotécnicos y cargas de eyección
- **Definición.** Salidas de la computadora/altímetro que energizan e-matches para detonar **cargas de eyección** (pólvora negra) y liberar paracaídas (apogeo/principal).  
- **Dimensionamiento.** La masa de pólvora se calcula a partir del **volumen libre** y la **presión objetivo**, y se valida en **pruebas en tierra** para asegurar expulsión fiable sin dañar estructura.

### 6.6 Alimentación
- **Baterías (LiPo/Li-ion/9 V).** Selección por **rango de tensión** del sistema y **corriente de pico** requerida por canales piro; verificar guías y manuales del altímetro/computadora.

---

## 7) Implicaciones para la computadora de vuelo

- **Sensado mínimo (500–1000 m).** Barométrico + IMU 6-ejes, monitoreo de batería, continuidad piro; **deseable** GNSS para recuperación.  
- **Registro.** ≥ **20 Hz** en baro y ≥ **200 Hz** en IMU para eventos; almacenamiento no volátil y descarga.
- **Eventos críticos.** **Apogeo** (drogue) y **principal** (baja altitud) gobernados por **canales pirotécnicos** con **ventanas/inhibiciones**.
- **Telemetría (opcional).** Enlace en tiempo real para estado y rastreo; plataformas modernas integran captura de alta tasa y herramientas de análisis (ver [Blue Raven](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)).
