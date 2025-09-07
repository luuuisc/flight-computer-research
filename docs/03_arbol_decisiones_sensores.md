# Árbol de decisiones de sensorización (cohete 500–1000 m)

> **Objetivo.** Seleccionar sensores y rangos/velocidades de muestreo realistas para una computadora de vuelo **SRAD**, tomando como referencia manuales/datasheets de altímetros **COTS** y hojas de datos de sensores.

## 1) Mínimos viables y “nice-to-have”

**Mínimo viable (recomendado)**
- **Barométrico absoluto** (altitud/apogeo). En COTS como **StratoLoggerCF** se registra ~**20 Hz** (altitud/temperatura/voltaje), suficiente para detección robusta de apogeo.  
  Referencia:
    - [StratoLoggerCF – 20 Hz](http://www.perfectflite.com/SLCF.html)
    - [Manual (PDF)](http://www.perfectflite.com/Downloads/StratoLoggerCF%20manual.pdf)
- **IMU 6-ejes** (acelerómetro + giroscopio) para eventos (liftoff/burnout) y estimaciones inerciales. En **Raven** se usan **400/200 Hz**; en **Blue Raven** hasta **500 Hz** inercial.  
  Referencias: 
  - [Raven – manual (PDF)](https://www.featherweightaltimeters.com/uploads/1/0/9/5/109510427/raven_users_manual_2014may20.pdf)
  - [Blue Raven – manual (PDF)](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)
- **Monitoreo de batería** (voltaje) y **continuidad piro** (para e-matches). Presente en COTS (ver manuales anteriores).

**Deseable**
- **GNSS** (recuperación/validación de trayectoria): **u-blox NEO-M8** (3 constelaciones) o **NEO-M9N** (4 constelaciones).  
  Referencias: 
  - [NEO-M8 – datasheet](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)
  - [NEO-M9N – datasheet](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)
- **Magnetómetro** (rumbo) para fusión 9-ejes, p. ej. **ICM-20948**.  
  Referencia: 
  - [ICM-20948 – datasheet](https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf)
- **Acelerómetro “high-g”** dedicado (si \(T/W\) alto o se prevén > ±16 g): **ADXL377/ADXL375 (±200 g)**.  
  Referencias:
  - [ADXL377 – datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/ADXL377.pdf)
  - [ADXL375 – datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/ADXL375.pdf)

---

## 2) Árbol de decisiones

**A. Pico de aceleración esperado**
- **≤ ±16 g** → IMU general con buen rechazo a vibración (**BMI088**), SPI/I²C, VDDIO amplio.  
  Referencia: 
  - [BMI088 – datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmi088-ds001.pdf)
- **> ±16 g** o incertidumbre → añadir **high-g** (ADXL377/375) para evitar saturación y registrar choques (links arriba).

**B. Necesidad de actitud/tilt**
- Solo eventos (apogeo/altitud principal) → **Baro + IMU 6-ejes** es suficiente.  
- Estimar **actitud/tilt/rolido** → **IMU 9-ejes** con DMP.

> [!NOTE]
> **Actitud:** orientación tridimensional del cohete respecto a un marco de referencia terrestre (p. ej., vertical local y Norte). Se expresa como ángulos de Euler (yaw/rumbo, pitch/cabeceo, roll/alabeo) o como cuaterniones.
>
> **Tilt (inclinación):** ángulo entre el eje longitudinal del cohete y la vertical (gravedad). Es un escalar que indica “qué tan inclinado” está el vehículo respecto a la vertical.
>
> **Cálculo rápido (cuasiestático, con acelerómetro):**
> - `pitch ≈ atan2(-ax, sqrt(ay^2 + az^2))`
> - `roll  ≈ atan2( ay, az )`
> - `tilt  ≈ acos( dot(bz_hat, g_hat) )`  ← ángulo entre el eje del cohete y la gravedad (vectores normalizados).

**C. Resolución altimétrica y estabilidad térmica**
- 0–3 km **AGL** con buena estabilidad térmica → **BMP388** (≈ 0.5 m típica); alternativa de muy alta resolución: **MS5611** (~10 cm).  
  Referencias: 
  - [BMP388 – datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmp388-ds001.pdf)
  - [MS5611 – datasheet](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036)

**D. Recuperación y telemetría**
- Solo localización post-vuelo → **GNSS** 1–10 Hz (**NEO-M8/NEO-M9N**, **Quectel L76/L76-LB**).  
  Referencias: 
  - [NEO-M8 – ds](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)
  - [NEO-M9N – ds](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)
  - [Quectel L76 – HW Design (PDF)](https://forums.quectel.com/uploads/short-url/jZHd2ObxH4FndArQyE3pqu7ZZ7e.pdf)
- Telemetría en tiempo real → considerar ecosistemas con radio dedicada. Ejemplo no-SRAD: **Blue Raven** con app/BLE (link arriba).

**E. Entorno mecánico/vibración**
- Estructura rígida/motores con vibración alta → **BMI088** + montaje con aislamiento mecánico (link arriba).

---

## 3) Recomendaciones de muestreo

| Magnitud | Típico COTS | Recomendado SRAD |
|---|---:|---:|
| **Barométrico** | ~**20 Hz** (StratoLoggerCF) | **20–50 Hz** |
| **Acelerómetro/Giróscopo** | **200–400 Hz** (Raven), **500 Hz** (Blue Raven) | **200–1000 Hz** |
| **GNSS** | **1–10 Hz** (NEO-M8/M9N, L76) | **5–10 Hz** |

**Fuentes:** 
- [StratoLoggerCF – 20 Hz](http://www.perfectflite.com/SLCF.html)
- [Raven – manual (PDF)](https://www.featherweightaltimeters.com/uploads/1/0/9/5/109510427/raven_users_manual_2014may20.pdf)
- [Blue Raven – manual (PDF)](https://www.apogeerockets.com/downloads/PDFs/09170-blue_raven_users_manual_september_15.pdf)
- [NEO-M8 – ds](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)
- [NEO-M9N – ds](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)
- [Quectel L76 – HW Design (PDF)](https://forums.quectel.com/uploads/short-url/jZHd2ObxH4FndArQyE3pqu7ZZ7e.pdf)

---

## 4) Candidatos (selección y compra con datasheets)

- **Barométricos**  
    - **Bosch BMP388** — 24-bit, SPI/I²C. [Datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmp388-ds001.pdf)  
    - **TE MS5611-01BA03** — alta resolución (~10 cm). [Datasheet](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036)

- **IMU / 9-ejes**  
    - **Bosch BMI088 (6-ejes)** — alta inmunidad a vibración; SPI/I²C. [Datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmi088-ds001.pdf)  
    - **TDK ICM-20948 (9-ejes + DMP)** — IMU 9-ejes con DMP. [Datasheet](https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf)

- **Acelerómetros high-g (opcional)**  
    - **Analog Devices ADXL377 (±200 g)** — bajo consumo; BW seleccionable. [Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/ADXL377.pdf)

- **GNSS**  
    - **u-blox NEO-M8** — 3 constelaciones concurrentes. [Datasheet](https://content.u-blox.com/sites/default/files/NEO-M8-FW3_DataSheet_UBX-15031086.pdf)  
    - **u-blox NEO-M9N** — 4 constelaciones concurrentes; detección de *jamming/spoofing*. [Datasheet](https://content.u-blox.com/sites/default/files/NEO-M9N-00B_DataSheet_UBX-19014285.pdf)  
    - **Quectel L76/L76-LB** — guía de hardware y diseño de antena. [HW Design (PDF)](https://forums.quectel.com/uploads/short-url/jZHd2ObxH4FndArQyE3pqu7ZZ7e.pdf)

---

## 5) Diagrama de decisión (Mermaid)

```mermaid
%%{init: {"theme":"base","themeVariables":{
  "primaryColor":"#0f766e","primaryTextColor":"#ffffff","primaryBorderColor":"#0d524c",
  "secondaryColor":"#2563eb","tertiaryColor":"#9333ea","lineColor":"#334155",
  "fontFamily":"Inter, ui-sans-serif, system-ui"
}}}%%
flowchart TD
  %% --- Clases de estilo ---
  classDef inicio fill:#0f766e,stroke:#0d524c,stroke-width:2px,color:#ffffff;
  classDef decision fill:#f59e0b,stroke:#b45309,stroke-width:2px,color:#111111;
  classDef proceso fill:#2563eb,stroke:#1e40af,stroke-width:2px,color:#ffffff;
  classDef dato fill:#9333ea,stroke:#6b21a8,stroke-width:2px,color:#ffffff;
  classDef salida fill:#16a34a,stroke:#166534,stroke-width:2px,color:#ffffff;

  %% --- Inicio ---
  A([Perfil 500–1000 m AGL]):::inicio

  %% --- Aceleración pico ---
  A --> B{¿Pico de aceleración <= 16 g?}:::decision
  B -- "Sí" --> C[IMU 6 ejes con buen rechazo a vibración<br/>(BMI088)]:::proceso
  B -- "No / incierto" --> D[IMU 6 ejes +<br/>acelerómetro high-g (ADXL377/375)]:::proceso

  %% --- Necesidad de actitud/tilt ---
  C --> E{¿Requieres actitud / tilt / rolido?}:::decision
  D --> E
  E -- "Sí" --> F[IMU 9 ejes con DMP<br/>(ICM-20948) + fusión]:::proceso
  E -- "No" --> G[Barométrico + IMU 6 ejes es suficiente]:::proceso

  %% --- Altimetría ---
  subgraph ALT["Altimetría"]
    H{Resolución altimétrica objetivo}:::decision
    I[BMP388<br/>submétrica (~0.5 m)]:::dato
    J[MS5611<br/>muy alta (~0.1 m)]:::dato
  end
  G --> H
  F --> H
  H -- "Submétrica" --> I
  H -- "Muy alta" --> J

  %% --- Recuperación / Telemetría ---
  I --> K{¿Solo recuperación post-vuelo?}:::decision
  J --> K
  K -- "Sí" --> L[GNSS 1–10 Hz<br/>(NEO-M8 / NEO-M9N / L76)]:::dato
  K -- "No, telemetría tiempo real" --> M[Altímetro con ecosistema de radio<br/>(p. ej., Blue Raven)]:::proceso

  %% --- Vibración / Montaje ---
  L --> N{¿Entorno con vibración alta?}:::decision
  M --> N
  N -- "Sí" --> O[Montaje con aislamiento mecánico<br/>y priorizar BMI088]:::proceso
  N -- "No" --> P[Montaje estándar]:::proceso

  %% --- Conjunto final ---
  O --> Q[[Conjunto recomendado]]:::salida
  P --> Q
  Q --> R[Barométrico (BMP388 o MS5611)]:::dato
  Q --> S[IMU 6 ejes (BMI088)]:::dato
  Q --> T[Opcional: IMU 9 ejes (ICM-20948)]:::dato
  Q --> U[Opcional: high-g (ADXL377/375)]:::dato
  Q --> V[GNSS 5–10 Hz (NEO-M8/M9N/L76)]:::dato
  Q --> W[Monitoreo de batería + continuidad piro]:::dato
```
---

## 6) Conclusión

Para un perfil de 500–1000 m, el **mínimo viable** queda en: sensor **barométrico** (BMP388 o MS5611) + **IMU 6-ejes** (BMI088), con **monitoreo de batería** y **continuidad piro**. Se recomienda añadir **GNSS 5–10 Hz** (NEO-M8/M9N o L76) para recuperación. Incluir **IMU 9-ejes** (ICM-20948) si se requiere estimar **actitud/tilt**, y **acelerómetro high-g** (ADXL377/375) cuando el pico esperado supere ±16 g o exista incertidumbre. 

