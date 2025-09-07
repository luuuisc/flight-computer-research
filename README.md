# Investigación para Computadora de Vuelo (Aviónica)
Repositorio técnico del equipo **Propulsion UNAM** para el diseño y documentación de una **computadora de vuelo** (SRAD) orientada a perfiles de 500–1000 m AGL, con respaldo de bibliografía y datasheets.

---

## Índice
- [Objetivos](#objetivos)
- [Alcance](#alcance)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Cómo navegar los documentos](#cómo-navegar-los-documentos)
- [Seguridad y cumplimiento](#seguridad-y-cumplimiento)
- [Licencia](#licencia)
- [Créditos y contacto](#créditos-y-contacto)

---

## Objetivos
1. Consolidar **teoría de cohetería experimental** (componentes, fases, variables a medir) como base de aviónica.  
2. Seleccionar y justificar **sensores y cadencias de muestreo** para una computadora de vuelo **SRAD**.  
3. Evaluar una **computadora de vuelo comercial** (COTS) como referencia/backup y su disponibilidad en México.  

## Alcance
- Enfoque en cohetes estudiantiles/hobby con apogeo **500–1000 m AGL**.  
- Documentación **en español**, compatible con GitHub/VSCode (Markdown + Mermaid + fórmulas inline).  
- Referencias a **manuales COTS** y **datasheets** de sensores como base de decisión.

---

## Estructura del repositorio
```
.
├─ docs/
│  ├─ 00_glosario.md
│  ├─ 01_teoria_coheteria.md
│  ├─ 02_requisitos_actividad_computadora.md
│  ├─ 03_arbol_decisiones_sensores.md
│  └─ 04_componentes_SRAD.md
├─ ref/
│  └─ REFERENCIAS.md
├─ LICENSE
└─ README.md
```

---

## Cómo navegar los documentos
1. **Teoría base** → `docs/01_teoria_coheteria.md`  
   Componentes, fases y variables de medición; ecuaciones y fuerzas relevantes.
2. **Requisitos de la actividad** → `docs/02_requisitos_actividad_computadora.md`  
   Lista de lo solicitado por la práctica y criterios de evaluación/documentación.
3. **Árbol de decisiones de sensorización** → `docs/03_arbol_decisiones_sensores.md`  
   Selección de sensores, rangos y tasas, con diagrama Mermaid y justificación.
4. **Glosario** → `docs/00_glosario.md`  
   Términos clave (COTS, SRAD).
5. **Referencias** → `ref/REFERENCIAS.md`  
   Bibliografía y enlaces a manuales/datasheets utilizados.
---

## Seguridad y cumplimiento
- Este material es **académico**. La ejecución de pruebas de eyección y vuelos debe cumplir **normativa local**, operar con **supervisión**, EPP y **procedimientos de seguridad**.  
- Las **cargas pirotécnicas** requieren **pruebas en tierra**, dimensionamiento conservador y validación por pares antes de vuelo.

---

## Licencia
Consulte `LICENSE` para términos de uso. Cite las fuentes técnicas y respete las licencias de terceros (manuales y datasheets).

---

## Créditos y contacto
Proyecto del equipo **Propulsion UNAM** — coordinación y desarrollo de aviónica.  
Sitio: https://propulsion-unam.com/

