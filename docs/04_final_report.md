---
title: "Reporte técnico — Computadora de Vuelo SRAD (perfil 500–1000 m AGL)"
author: "Propulsion UNAM"
date: "2025-09-07"
toc: true
toc-depth: 3
numbersections: true
geometry: margin=2.5cm
---
# Reporte técnico — Computadora de Vuelo

- **Autor:** Pérez Castro Luis Ángel
- **Equipo:** Propulsión UNAM
- **Fecha:** 2025-09-07

## 1. Resumen

Este documento integra la **investigación y selección** de componentes para una **computadora de vuelo** (SRAD) dirigida a cohetes con apogeo **500–1000 m AGL**. Se consolidan requisitos de la actividad, criterios de diseño, diagrama de decisión, fichas técnicas por componente (con **datasheets** y alternativas), y una revisión de **computadoras comerciales** (COTS) como referencia/redundancia.

**Resultado principal (mínimo viable):** barométrico (BMP388 o MS5611) + **IMU 6 ejes** (BMI088), con **monitoreo de batería** y **continuidad piro**; **GNSS 5–10 Hz** (NEO-M8/M9N o L76) para recuperación. Si se requiere orientación, añadir **IMU 9 ejes** (ICM-20948); si el pico de aceleración puede superar ±16 g, añadir **acelerómetro high-g** (ADXL377/375).

---

## 2. Alcance y supuestos de misión

- Cohete experimental, apogeo **500–1000 m AGL**.
- Secuencia de vuelo estándar: boost → coast → **apogeo** (drogue) → main (150–300 m AGL) → recuperación.
- **Detección de apogeo** barométrica, con **inhibiciones** (no-fire en boost, alturas/tiempos mínimos).
- Muestreos de referencia: **baro 20–50 Hz**, **inercial 200–1000 Hz**, **GNSS 5–10 Hz**.

> Referencias base (didácticas y de producto) usadas en este proyecto aparecen a lo largo del documento:
> - **NASA BGA (cohetes):** estabilidad, aerodinámica y fuerzas.
> - **PerfectFlite StratoLoggerCF:** 
>   - [Página 20 Hz](http://www.perfectflite.com/SLCF.html)
>   - [Manual PDF](http://www.perfectflite.com/Downloads/StratoLoggerCF%20manual.pdf)  
> - **Featherweight Raven (manual):** 
>   - [Manual PDF](https://www.featherweightaltimeters.com/uploads/1/0/9/5/109510427/raven_users_manual_2014may20.pdf)
> - **Featherweight Blue Raven (manual):** 
>   - [Manual PDF](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)

---

## 3. Requisitos de la actividad

Para **cada componente** de la computadora de vuelo, documentar:

- **Descripción y uso**
- **Rango de operación** (tensión, corriente, temperatura)
- **Requerimientos de hardware** (buses, pines, conexiones especiales)
- **Cómo se programa** (si aplica: protocolo, SDK, ejemplo mínimo)
- **Justificación** (criterios de selección y comparación)
- **Dónde conseguirlo** + **al menos una alternativa**
- **Hoja de datos (datasheet)**

Adicionalmente:
- Definir **COTS** y **SRAD**.
- Incluir **computadora de vuelo comercial** (características + compra desde México).

---

## 4. Arquitectura a alto nivel

```mermaid
flowchart LR
  subgraph Sensado
    B[BMP388 o MS5611<br/>Barométrico 20–50 Hz]
    I6[BMI088<br/>IMU 6 ejes 200–1000 Hz]
    I9[ICM-20948<br/>IMU 9 ejes (opc.)]
    HG[ADXL377/375<br/>High-g (opc.)]
    G[GNSS NEO-M8/M9N o L76<br/>5–10 Hz]
  end
  subgraph Cómputo
    MCU[MCU ARM<br/>SPI/I2C/UART, timers]
    FLASH[Flash SPI<br/>Log de vuelo]
  end
  subgraph Actuación
    PYRO[Canales piro<br/>drogue/main]
  end
  subgraph Energía
    BAT[Batería LiPo]
    REG[Regulación 5V/3V3]
    MON[Monitoreo de voltaje]
  end

  B-->MCU; I6-->MCU; I9-->MCU; HG-->MCU; G-->MCU
  MCU-->FLASH; MCU-->PYRO
  BAT-->REG-->MCU; REG-->Sensado; MCU-->MON
```

> El **árbol de decisiones** detallado se incluye en `03_arbol_decisiones_sensores.md`. Aquí se parte de sus conclusiones para justificar la selección.

---

## 5. Definiciones

* **COTS (Commercial Off-The-Shelf):** equipos/componentes comerciales listos para uso sin desarrollo a medida (ej.: altímetros PerfectFlite/Featherweight).
* **SRAD (Student Researched And Developed):** equipos investigados y desarrollados por estudiantes (ej.: esta computadora de vuelo).

---

## 6. Fichas técnicas por componente

> **Formato uniforme**. Cada ficha incluye: descripción, rango, HW, programación (si aplica), justificación, proveedor/alternativa y **datasheet**.

### 6.1 Barométrico — Opción A: Bosch **BMP388**

- **Descripción / uso.** Sensor barométrico digital; altitud y detección de apogeo (20–50 Hz).
- **Rango de operación.** Tensión nominal **3.3 V** (ver datasheet para min/max), consumo bajo; temperatura amplia.
- **Requerimientos HW.** **SPI/I²C**; orificios de presión en bahía; evitar flujo directo de gases de eyección.
- **Programación.** Lecturas de presión/temperatura; compensación interna; filtro IIR/oversampling ajustables.
- **Justificación.** Resolución submétrica (\~0.5 m típica), buen comportamiento térmico (TCO bajo), bajo consumo.
- **Proveedor/alternativa.** Mouser/Digi-Key MX; alternativa: **MS5611**.
- **Datasheet.** [Datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmp388-ds001.pdf)

---

### 6.2 Barométrico — Opción B: TE **MS5611-01BA03**

- **Descripción / uso.** Altímetro de muy alta resolución (\~0.1 m típico) para apogeo/ventanas.
- **Rango de operación.** 3.3 V nominal (ver datasheet); consumo moderado; temperatura amplia.
- **Requerimientos HW.** **SPI/I²C**; diseño de puertos baro estable; apantallamiento básico.
- **Programación.** Conversión ADC 24-bit; coeficientes de calibración; cálculo de presión/altitud.
- **Justificación.** Precisión excelente en rango 0–3 km AGL; estándar en modelismo de alta resolución.
- **Proveedor/alternativa.** Mouser/Digi-Key MX; alternativa: **BMP388**.
- **Datasheet.** [Datasheet](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036)

---

### 6.3 IMU 6 ejes — Bosch **BMI088**

- **Descripción / uso.** Acelerómetro + giroscopio con **alta inmunidad a vibración**; detección de liftoff/burnout y dinámica.
- **Rango de operación.** 3.3 V nominal (ver datasheet); rangos programables (al menos ±16 g y ±2000 °/s).
- **Requerimientos HW.** **SPI/I²C**; layout compacto, tierra sólida, desacoplos cercanos; posible montaje con **aislamiento mecánico**.
- **Programación.** Configuración de ODR/filtros; lectura síncrona (SPI) a 200–1000 Hz.
- **Justificación.** Robusto ante vibración; adecuado como inercial principal para 500–1000 m.
- **Proveedor/alternativa.** Mouser/Digi-Key MX; alternativas: **ICM-42688-P**, **LSM6DS** (según disponibilidad).
- **Datasheet.** [Datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmi088-ds001.pdf)

---

### 6.4 IMU 9 ejes (opcional) — TDK **ICM-20948**

- **Descripción / uso.** Acel-Gyro-Mag con DMP integrado; estimación de **actitud/tilt/rumbo** mediante fusión.
- **Rango de operación.** 3.3 V nominal (ver datasheet); rangos configurables.
- **Requerimientos HW.** **SPI/I²C**; magnetómetro requiere cuidado contra corrientes parásitas/cables.
- **Programación.** Lecturas crudas o DMP; filtro complementario/Kalman/AHRS a 10–50 Hz (inercial interno a 200–1000 Hz).
- **Justificación.** Útil para validaciones de seguridad por inclinación y análisis post-vuelo.
- **Proveedor/alternativa.** Mouser/Digi-Key MX; alternativa: **ISM330 + magnetómetro externo**.
- **Datasheet.** [Datasheet](https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf)

---

### 6.5 Acelerómetro **high-g** (opcional) — ADI **ADXL377 / ADXL375**

- **Descripción / uso.** Medición de picos de aceleración (encendido/choques) cuando > ±16 g.
- **Rango de operación.** 3.3 V nominal (ver datasheet); **ADXL377** analógico (±200 g), **ADXL375** digital (SPI, ±200 g).
- **Requerimientos HW.** Acondicionamiento analógico (377) o **SPI** (375); fijación rígida.
- **Programación.** (375) lectura SPI y detección de eventos; (377) muestreo ADC.
- **Justificación.** Evita saturación de la IMU principal y enriquece el análisis de eventos.
- **Proveedor/alternativa.** Mouser/Digi-Key MX; alternativa: **ADXL372**.
- **Datasheets.**
    - ADXL377: [Datasheet ADXL377](https://www.analog.com/media/en/technical-documentation/data-sheets/ADXL377.pdf)
    - ADXL375: [Datasheet ADXL375](https://www.analog.com/media/en/technical-documentation/data-sheets/ADXL375.pdf)

---

### 6.6 GNSS — u-blox **NEO-M8** / **NEO-M9N** (alternativa: **Quectel L76/L76-LB**)

- **Descripción / uso.** Recuperación y validación de trayectoria; **1–10 Hz**.
- **Rango de operación.** 3.3 V nominal (ver datasheet); antena activa con **SAW+LNA** recomendada.
- **Requerimientos HW.** **UART/I²C**; plano de tierra, separación de RF respecto a piro y cables de alta corriente.
- **Programación.** Protocolo **UBX/NMEA** (u-blox) o binario (Quectel); lectura de `lat/lon/alt/speed`.
- **Justificación.** Mejora de recuperación; M9N admite 4 constelaciones y detección básica de **jamming/spoofing**.
- **Proveedor/alternativa.** Mouser/Digi-Key MX; módulos L76/L76-LB como opción económica.
- **Datasheets/Docs.**

    * NEO-M8: [Datasheet NEO-M8](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)
    * NEO-M9N: [Datasheet NEO-M9N](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)
    * Quectel L76 Hardware Design: [Datasheet Quectel L76](https://forums.quectel.com/uploads/short-url/jZHd2ObxH4FndArQyE3pqu7ZZ7e.pdf)

---

### 6.7 Altímetros / Computadoras COTS (referencia y redundancia)

> **Objetivo:** referenciar capacidades reales de equipos comerciales (tasas, lógica piro, registro), y evaluar su **disponibilidad** para adquirir desde México.

#### PerfectFlite **StratoLoggerCF**

* **Uso:** altímetro con **20 Hz** (altitud, temperatura, voltaje), control de **drogue/main**.
* **Referencias:** 
    - [Página 20 Hz](http://www.perfectflite.com/SLCF.html)
    - [Manual PDF](http://www.perfectflite.com/Downloads/StratoLoggerCF%20manual.pdf)
* **Notas:** ejemplo de ventanas e inhibiciones; referencia para detección de apogeo barométrico.

#### Featherweight **Raven** (clásico)

* **Uso:** inercial hasta **200–400 Hz**, baro **20 Hz**; **4 salidas piro** configurables.
* **Referencias:** [Manual PDF](https://www.featherweightaltimeters.com/uploads/1/0/9/5/109510427/raven_users_manual_2014may20.pdf)
* **Notas:** muy flexible; útil como banco de comparación.

#### Featherweight **Blue Raven**

* **Uso:** inercial **500 Hz**, registro **50 Hz**, app de configuración/descarga (BLE).
* **Referencias:** [Manual PDF](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)
* **Notas:** ecosistema moderno; ejemplo de telemetría/app.

> **Disponibilidad en MX:** confirmar envío y tiempos con los fabricantes/distribuidores antes de la compra (costos de importación/aduana pueden aplicar).

---

## 7. Requisitos de interfaz y energía

* **Buses:** SPI (baro/IMU/flash), I²C (sensores alternos), UART (GNSS/telemetría).
* **Temporización:** timers para muestreo determinístico (200–1000 Hz inercial; 20–50 Hz baro).
* **Alimentación:** batería **LiPo**; regulación **5 V** (piro/aux) y **3.3 V** (lógica/sensores).
* **Picos piro:** dimensionar drivers (corriente y tiempo de conducción) y protecciones (TVS, fusibles, MOSFETs apropiados).
* **EMC & puesta a tierra:** planos sólidos, retorno corto, desacoplo por dispositivo, separación RF/alto-corriente.

---

## 8. Conclusiones

La arquitectura propuesta cumple el **mínimo viable** para 500–1000 m y deja rutas claras de ampliación (actitud/telemetría/high-g). La selección se basa en **prácticas COTS** (tasas y lógicas verificadas en StratoLoggerCF/Raven/Blue Raven) y en **sensores** con soporte estable y disponibilidad en MX.

---

## 9. Anexos

### A. Plantilla por componente

```markdown
### [Nombre del componente] — [Candidato(s)]
**Descripción / uso.**  
**Rango de operación.** [V, mA, °C]  
**Requerimientos eléctricos / conexiones.** [SPI/I²C/UART, pines, filtros, montaje]  
**Programación (si aplica).** [driver/SDK, ejemplo mínimo]  
**Justificación.** [criterios, riesgos, comparación]  
**Proveedor en MX.** [distribuidor] — **Alternativa.** [opción]  
**Datasheet.** [URL]  
**Observaciones.** [EMC, pruebas, mecánica]
```

### B. Referencias clave (enlace rápido)

* **StratoLoggerCF (20 Hz / manual):**
  - [Datasheet StratoLoggerCF](http://www.perfectflite.com/SLCF.html)
  - [Manual PDF](http://www.perfectflite.com/Downloads/StratoLoggerCF%20manual.pdf)
* **Raven (manual):** [Datasheet Raven](https://www.featherweightaltimeters.com/uploads/1/0/9/5/109510427/raven_users_manual_2014may20.pdf)
* **Blue Raven (manual):** [Blue Raven Manual](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)
* **BMP388 (datasheet):** [Datasheet BMP388](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmp388-ds001.pdf)
* **MS5611 (datasheet):** [Datasheet MS5611](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036)
* **BMI088 (datasheet):** [Datasheet BMI088](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmi088-ds001.pdf)
* **ICM-20948 (datasheet):** [Datasheet ICM-20948](https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf)
* **NEO-M8 (datasheet):** [Datasheet NEO-M8](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)
* **NEO-M9N (datasheet):** [Datasheet NEO-M9N](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)
* **Quectel L76 (HW Design):** [Datasheet Quectel L76](https://forums.quectel.com/uploads/short-url/jZHd2ObxH4FndArQyE3pqu7ZZ7e.pdf)

---
