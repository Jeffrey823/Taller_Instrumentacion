# Descripción de los Archivos LabView .vi

## TI_Proyecto3_avance_sinMyDAQ.vi:
- Archivo con Demostración del SPI y DAC en LabView (no se utiliza el myDAQ).

## TI_Proyecto3_ultimo1_conMyDAQ.vi:
- Genera las señales de salida analógica del myDAQ.
- Se mide la señal de entrada con el ADC del myDAQ.
- Se mide el delay entre la señal de salida analógica y la señal digital procesada.
- La señal analógica se convierte a una señal digital de 8-bits de precisión que pasa por un canal SPI en LabView.

## Notas:
- En el .vi se utiliza N samples con una frecuencia de muestreo de 1kHz y 1 sample por iteración.
- La señal senoidal del generador de funciones es de 200Hz y Vp=5V.
- En el .vi y mediciones no se tomó en cuenta que la onda senoidal debía ser de 0V a 5V (Se está utilizando una de -5V a 5V (Vp=5V)).