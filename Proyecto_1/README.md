# Proyecto 1 - Electromiógrafo

Diseño, implementación y validación experimental de un circuito electromiógrafo (EMG) con amplificadores operacionales, adquirido con **NI myDAQ + LabVIEW** y analizado estadísticamente en **R**. El énfasis del proyecto está en el **diseño del experimento** más que en el circuito en sí.

## Circuito

Basado en un diseño de bajo costo de la literatura (Chowdhury & Roy, ICECIE 2020), con TL072CP. Cada etapa se simuló en Multisim.

| Etapa | Función |
|---|---|
| Amplificador de instrumentación | Entrada diferencial, alta impedancia y rechazo de ruido en modo común |
| 2 filtros paso bajo (en serie) | fc = 1 kHz |
| 2 filtros paso alto (en serie) | fc = 65 Hz |
| Amplificador no inversor | Ganancia de 1000 |

## Experimento

- **Hipótesis nula (H₀):** La ganancia del electromiógrafo no varía significativamente con la frecuencia de la señal de entrada.
- **Procedimiento:** El myDAQ genera la señal en una entrada diferencial (la otra va a tierra) con combinaciones aleatorias de los factores, y LabVIEW mide el Vpp de entrada y salida para calcular la ganancia.
- **Pruebas:** 841 pruebas aleatorias, con 6 repeticiones por combinación.
- **Alimentación:** Fuente DC externa (±15 V), porque el myDAQ no entrega la potencia necesaria.

| Factor | Niveles |
|---|---|
| Tensión de entrada (mVp) | 1, 2, 3, 4 y 5 |
| Frecuencia (Hz) | 1 – 100 Hz (pasos de 10 Hz), 100 Hz – 1 kHz (pasos de 100 Hz), 1 – 9 kHz (pasos de 1 kHz) |
| Forma de onda | Senoidal |

## Resultados

- La respuesta en frecuencia medida concuerda parcialmente con la simulación: la ganancia cae cerca de 300 Hz (en la simulación, cerca de 3 kHz) y aparece un pico inesperado alrededor de 4 kHz.
- La salida tiene ~2.4 Vpp de ruido (circuito armado en protoboard), por lo que las entradas menores a 5 mVp casi no se distinguen del ruido; solo 5 mVp reproduce la forma esperada.
- **ANOVA en R:** La frecuencia, la amplitud de entrada y su interacción son significativos (p < 0.01), con y sin el ruido suprimido. **Se rechaza H₀.**
- En la prueba con electrodos en el antebrazo de un compañero se distingue el músculo activo del inactivo, aunque con mucho ruido.

**Mejora propuesta:** pasar el circuito a una placa soldada para reducir el ruido.

## Contenido

| Archivo / carpeta | Descripción |
|---|---|
| `LabVIEW/` | VI de generación y adquisición de datos |
| `Multisim/` | Simulaciones del circuito |
| `mediciones.xlsx` | Datos obtenidos del experimento |
| `TI_Proyecto1_Entrega.pdf` | Informe final |

**Herramientas:** Multisim, NI myDAQ, LabVIEW, R.