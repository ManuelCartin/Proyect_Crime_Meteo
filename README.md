# Meteorología y Crimen en Costa Rica — Narrativa de Riesgo Operacional (2015–2025)
> **Portafolio de Análisis de Riesgo · Módulo: Logística & Seguridad de Activos**  
> Lenguaje: Python · Datos: OIJ (Organismo de Investigación Judicial) + Open-Meteo · Período: 2015–2025

---

## Descripción

Este proyecto analiza si las condiciones meteorológicas constituyen un **factor de riesgo operacional medible** para la seguridad en Costa Rica, usando 10 años de registros de criminalidad (OIJ) cruzados con datos climáticos diarios de las 7 provincias.

El enfoque **no es predictivo**: no se entrena ningún modelo de machine learning. El objetivo es construir una **narrativa de riesgo basada en evidencia estadística** — el tipo de análisis que informa decisiones de asignación de recursos, gestión de flota y planificación de rutas en operaciones logísticas.

El proyecto parte de una hipótesis intuitiva sobre *ventanas de oportunidad climática* y la refina — y en algunos casos la invierte — con datos reales.

---

## Datos

| Fuente | Descripción | Período | Registros |
|---|---|---|---|
| OIJ (Organismo de Investigación Judicial) | 11 archivos CSV anuales de estadísticas de delitos | 2015–2025 | 530,112 |
| Open-Meteo (historical weather API) | Series climáticas diarias, 7 estaciones provinciales | 2009–2025 | 6,117 días/estación |

**Variables climáticas utilizadas:** temperatura máx/mín, precipitación total diaria (mm), humedad máxima (%), cobertura de nubes promedio (%), visibilidad, presión, índice UV, horas de sol.

**Variables de crimen utilizadas:** tipo de delito, sub-tipo (método), perfil de víctima, franja horaria, provincia, cantón.

---

## Hallazgo previo al análisis: defecto de integridad en la fuente

Antes de cualquier modelado, la auditoría del pipeline detectó que **los 11 archivos anuales del OIJ tienen las columnas desplazadas una posición** respecto a sus nombres declarados: la columna `Victima` contiene en realidad la franja horaria, `SubVictima` contiene el tipo real de víctima, y así hasta `Distrito`, que en realidad contiene el cantón. El campo real de distrito nunca fue exportado.

Este defecto es idéntico en todos los años — es un problema del exportador de datos del OIJ, no de manipulación posterior. **Sin esta corrección, cualquier análisis geográfico o demográfico sobre estos datos queda contaminado silenciosamente.** El pipeline aplica el mapeo correcto antes de cualquier análisis.

---

## Estructura del notebook

El análisis se desarrolla en **9 fases** en [`Weather_Crime_Risk_Narrative (1).ipynb`](Weather_Crime_Risk_Narrative%20(1).ipynb):

```
Fase 1  Descubrimiento del defecto de integridad en la fuente OIJ
Fase 2  Reconstrucción del pipeline nacional (530,112 registros, 85 cantones)
Fase 3  Índice de severidad por sub-tipo de delito + features temporales y climáticos
Fase 4  ¿El clima es un factor de riesgo? (V de Cramér vs. franja horaria)
Fase 5  Volumen de crimen vs. clima: correlación Spearman por provincia
Fase 6  Hipótesis de ventana de oportunidad: interacción noche × lluvia fuerte
Fase 7  Rango óptimo climático para volumen de crimen (humedad, nubosidad, precipitación)
Fase 8  Desglose por tipo de delito vehicular: oportunista vs. planificado
Fase 9  Perfil de la víctima preferida en la ventana climática óptima
```

---

## Resultados principales

### Fase 4 — El clima como factor de riesgo individual

El clima tiene una relación **estadísticamente detectable pero operacionalmente irrelevante** con la severidad de un delito individual:

| Variable | V de Cramér | Interpretación |
|---|---|---|
| Franja horaria | **0.155** | Efecto real y accionable |
| Fin de semana | 0.034 | Efecto marginal |
| Rango de humedad | 0.024 | Ruido estadístico |
| Rango de precipitación | 0.023 | Ruido estadístico |
| Rango de nubosidad | 0.023 | Ruido estadístico |

La **franja horaria nocturna** (18:00–23:59) concentra el **38.9% de delitos con severidad ALTA** — casi el doble que cualquier otra franja. El clima por sí solo no alcanza este efecto.

### Fase 6 — Interacción noche × lluvia fuerte

La combinación de horario nocturno + lluvia fuerte produce una tasa de severidad ALTA del **40.53%**, frente al 39.11% esperado si los efectos fueran puramente aditivos. El valor esperado queda **fuera del intervalo de confianza Wilson** → hay una interacción real, aunque modesta (+1.42 pp). El clima amplifica un riesgo temporal preexistente, no lo crea independientemente.

### Fase 7 — Ventana de oportunidad climática para volumen de crimen

El resultado es **contraintuitivo**: los días con mayor volumen de delitos no son los días de mal tiempo, sino los de clima moderado y despejado.

| Variable | Rango de mayor volumen | Media crímenes/día | Diferencia |
|---|---|---|---|
| Nubosidad | 0–20% (cielo despejado) | ~151/día | +34% vs. días >90% nublado |
| Humedad | 75–85% (moderada-alta) | ~146/día | +22% vs. días >95% humedad |
| Precipitación | Sin patrón | ~133–139/día | variación < 5% |

**Ventana óptima combinada** (nubosidad ≤40% · humedad 70–85% · precipitación ≤15 mm):  
→ 802 días del período (~20.6%) · mediana **151 vs. 133 crímenes/día** fuera de ventana · Mann-Whitney **p = 1.3×10⁻⁴¹**

Mecanismo propuesto: el buen tiempo aumenta la movilidad de personas → más intersecciones ofensor-víctima-ausencia de guardián (Routine Activity Theory, Cohen & Felson, 1979).

### Fase 8 — La ventana climática no aplica igual a todos los delitos

El desglose por tipo de delito vehicular revela una distinción fundamental:

| Segmento | Δ% en ventana óptima | p-valor | Tipo |
|---|---|---|---|
| **Tacha de Vehículo** | **+15.8%** | 1.8×10⁻¹⁹ | Oportunista puro |
| **Asalto con Vehículo** | **+9.7%** | 2.5×10⁻⁵ | Oportunista / exposición |
| Robo de Vehículo (descuido) | +3.8% | 0.079 | Marginal |
| **Robo de Vehículo (asalto directo)** | +0.8% | 0.358 | **Sin señal — planificado** |
| Cocherazo | −5.7% | 0.971 | **Sin señal — posible inversión** |

**Conclusión:** la ventana climática de oportunidad aplica a los **delitos oportunistas** (tacha, asalto en tránsito). Los delitos **planificados** (robo de vehículo con confrontación directa) operan con independencia del clima.

### Fase 9 — Perfil de la víctima preferida en la ventana climática

Índice de sobrerepresentación: proporción de incidentes del perfil en la ventana ÷ tasa base global (20%).

**Top 5 — Perfiles PERSONA:**

| # | Perfil | % en ventana | Índice |
|---|---|---|---|
| 🥇 | Turista Nacional | 35.5% | **1.77×** |
| 🥈 | Turista Extranjero | 31.0% | **1.55×** |
| 🥉 | Cliente Hotel/Motel | 28.4% | 1.42× |
| 4 | Empresario/Comerciante | 26.4% | 1.32× |
| 5 | Finquero | 24.5% | 1.22× |

**Top 5 — Perfiles VEHÍCULO:**

| # | Perfil | % en ventana | Índice |
|---|---|---|---|
| 🥇 | Acuático | 34.8% | **1.74×** |
| 🥈 | Cuadraciclo | 25.0% | 1.25× |
| 🥉 | Furgón / Contenedor | 22.8% | **1.14×** |
| 4 | Rural 4×4 | 21.7% | 1.08× |
| 5 | Carga (general) | 20.6% | 1.03× |

---

## Implicación para logística

> Los días de mejor clima operativo — los que maximizan la actividad de distribución — son los mismos en que furgones y vehículos de carga están más expuestos a tacha y asalto oportunista.

El modelo produce tres señales accionables para una operación logística:

1. **Priorizar franja horaria** sobre pronóstico climático para asignación de escolta o rutas de alto riesgo (la franja nocturna es 14× más predictiva que cualquier variable climática).
2. **Días de clima moderado-despejado = mayor actividad = mayor exposición** para flota en zonas urbanas. No relajar medidas de seguridad "porque hace buen tiempo".
3. **Distinguir riesgo oportunista vs. planificado**: protocolos anti-tacha y anti-asalto en tránsito son los más sensibles al clima. El robo planificado requiere inteligencia de rutas, no ajuste climático.

---

## Estructura de archivos

```
crime climate/
├── Weather_Crime_Risk_Narrative (1).ipynb   ← notebook principal (9 fases)
├── Weather_Crime_all_Country (1).ipynb      ← análisis exploratorio original
├── final/
│   └── df_crime_clima_2015_2025.csv         ← dataset consolidado (530,112 registros × 44 vars)
├── crimen csv/
│   ├── crimen 2010/  (Estadisticas 2015–2019.csv)
│   ├── crimen 2020/  (Estadisticas 2020–2022.csv)
│   └── crimen 2023/  (Estadisticas 2023–2025.csv)
├── clima/
│   └── export-{provincia}.csv               ← 7 series climáticas diarias 2009–2025
├── fig5_ventana_climatica_volumen.png
├── fig6_nubosidad_por_segmento_veh.png
├── fig7_ventana_delta_por_segmento.png
└── fig8_top_victimas_ventana.png
```

---

## Dependencias

```python
pandas >= 2.0
numpy >= 1.24
matplotlib >= 3.7
seaborn >= 0.12
scipy >= 1.10
```

```bash
pip install pandas numpy matplotlib seaborn scipy
```

---

## Contexto del portafolio

Este proyecto forma parte de un portafolio de análisis de riesgo orientado a **logística e Industria 4.0**. El módulo central de este análisis — la ventana de oportunidad climática y el perfil de víctima — es transferible como componente de un sistema de scoring de riesgo por ruta, combinable con datos de tráfico, incidentes históricos y cobertura de red.

Otros módulos del portafolio:
- Análisis de telemetría y dinámica vehicular (Nürburgring Nordschleife)
- Métodos numéricos aplicados a modelos de cuarto de carro (suspensión)

---

## Autor

Análisis, pipeline e hipótesis: trabajo propio  
Datos de crimen: [OIJ — Estadísticas policiales](https://www.oij.go.cr/estadisticas.html)  
Datos climáticos: [Open-Meteo Historical Weather API](https://open-meteo.com)  
Marco teórico de referencia: Cohen, L. E. & Felson, M. (1979). *Social Change and Crime Rate Trends: A Routine Activity Approach*. American Sociological Review, 44(4), 588–608.
