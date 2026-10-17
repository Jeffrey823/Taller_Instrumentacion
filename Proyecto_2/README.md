# Proyecto 2 - Medidor de pH

Diseño, implementación y validación experimental de un circuito acondicionador para un electrodo de pH con amplificadores operacionales, instrumentado con **NI myDAQ + LabVIEW** y analizado estadísticamente en **R**. El electrodo se emula con una señal generada por el myDAQ.

## Principio

El electrodo de pH produce un voltaje dado por:

```
V_pH = -(pH - 7) · 59.16 mV/pH
```

Para pH de 0 a 14 el electrodo entrega entre **-414.12 mV y +414.12 mV**, con una resistencia de salida muy alta (50 MΩ – 500 MΩ). Por eso necesita una etapa de acople con alta impedancia de entrada y un offset para que la salida quede en el rango positivo de un ADC (0 – 5 V).

## Circuito

Basado en un medidor de pH genérico de bajo costo, simulado en Multisim.

| Etapa | Descripción |
|---|---|
| Filtro y acople | TL081 (impedancia de entrada de 1 TΩ). Filtro paso bajo RC a la entrada: fc = 1/(2πRC) ≈ 8 Hz con 200 MΩ y 100 pF (depende de la resistencia del electrodo) |
| Amplificación | No inversor con ganancia 6: ±414.12 mV → ±2.485 V |
| Offset | Referencia de 2.5 V con TL431 y buffer TL081 |
| Sumador e inversor | LM324N con resistencias de 10 kΩ; suma la señal amplificada con el offset |

**Salida:** 0.015 V – 4.985 V. Cerca de 0 V indica una basicidad muy alta, 2.5 V corresponde a pH 7 (neutro) y cerca de 5 V indica una acidez muy alta:

```
V_CD = -(pH - 7) · 59.16 · 6 / 1000 + 2.5        pH = 7 + 1000 · (2.5 - V_CD) / (59.16 · 6)
```

## Experimento

- **Hipótesis nula (H₀):** El valor CD a la salida del circuito no varía sistemáticamente con el valor de pH simulado a la entrada.
- **Procedimiento:** LabVIEW lee las combinaciones de factores desde un archivo de Excel y las ingresa al myDAQ de forma aleatoria. Una entrada diferencial va a tierra y la otra recibe la señal (nivel CD + señal alterna). El myDAQ también alimenta el circuito, mide el Vpp de entrada y salida (ganancia en CA) y el valor CD de salida, y guarda los datos para el análisis en R.
- **Pruebas:** 3 × 5 × 15 = 225 combinaciones, con 3 repeticiones cada una: **675 mediciones aleatorias**.

| Factor | Niveles |
|---|---|
| Tensión de entrada (Vp) | 0.02, 0.04 y 0.06 (5 %, 10 % y 15 % del CD máximo de 0.40 V; evalúa la tolerancia al ruido) |
| Frecuencia (Hz) | 1, 7, 10, 70 y 100 (uno en banda plana, dos cerca de fc y dos fuera de la banda de paso) |
| Valor CD (V) | 15 niveles de -0.40 a 0.40 V, uno por cada valor de pH de 0 a 14 |

## Resultados

- **ANOVA en CA (ganancia):** La frecuencia es el factor más significativo (p < 2e-16) y la tensión de entrada también lo es, en menor magnitud (p ≈ 0.04).
- **ANOVA en CD (salida):** Frecuencia, tensión de entrada y valor CD son significativos (p < 2e-16), y también la interacción frecuencia:Vin.
- **Respuesta en frecuencia:** La forma y la frecuencia de corte (~8 Hz) coinciden con la simulación. Con más amplitud de entrada la ganancia sube ligeramente, sobre todo a bajas frecuencias, porque el filtro paso bajo atenúa el ruido de frecuencias altas.
- **Salida CD:** Casi no depende de la amplitud (máximo ~3 mV de diferencia, 0.06 % de 5 V). Es lineal respecto al voltaje del electrodo.
- **Offset real:** El promedio de la salida CD es ~2.44 V y no 2.5 V, porque el TL431 no entrega exactamente 2.5 V. Hay que tenerlo en cuenta al convertir voltaje a pH. Además, en el circuito físico la ganancia llega a ser un poco mayor a 6.
- **Se rechaza H₀:** La salida CD varía sistemáticamente con el pH simulado.

## Contenido

| Archivo / carpeta | Descripción |
|---|---|
| `LabVIEW/` | VI de instrumentación y adquisición de datos |
| `Multisim/` | Simulaciones del circuito |
| `Medicion_pH_comandos_R.txt` | Comandos de R usados en el análisis |
| `TI_Proyecto_2_Entrega.pdf` | Informe final |

**Herramientas:** Multisim, NI myDAQ, LabVIEW, R.