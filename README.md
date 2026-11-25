# Proyecto 3 · Clasificación solar para operación y mantenimiento

Analítica de Datos I · Universidad Icesi · 2026-02.

**Integrantes:** Miguel Ángel Tabares, Isabella Londoño Lerma y Andrés Felipe Rivas. Ingeniería en Energía Inteligente.

El proyecto anticipa el nivel **bajo, medio o alto de la Planta 1 a 15 minutos**, utilizando información disponible al emitir el pronóstico. Apoya la valoración de periodos de mantenimiento y la revisión de discrepancias entre generación real y anunciada.

## Entregables

| Archivo | Contenido |
|---|---|
| [Notebook](notebooks/Proyecto3_Planta1.ipynb) | Análisis CRISP-DM ejecutado, decisiones, código, métricas y 26 gráficos |
| [Informe final PDF](informe/Informe_Proyecto3_Planta1_IEEE.pdf) | Informe IEEE de 5 páginas, resultados, discusión, conclusiones, referencias y declaración de IAG |
| [Presentación HTML](presentacion/Sustentacion_Proyecto3_Planta1.html) | 12 láminas de contenido y cierre de agradecimiento; autónoma y sin internet |
| [Consigna y guías](consigna/) | Consigna específica y materiales de las semanas 2, 3 y 4 |

El HTML debe descargarse y abrirse en un navegador. GitHub muestra su código, no ejecuta la presentación. Navegar con flechas, espacio, clic o gesto; **F** activa pantalla completa.

```text
Proyecto/
├── consigna/       Consigna PDF y guías HTML de clase
├── datos/          Marcador para colocar los CSV localmente
├── notebooks/      Proyecto3_Planta1.ipynb
├── informe/        Informe_Proyecto3_Planta1_IEEE.pdf
├── presentacion/   Sustentacion_Proyecto3_Planta1.html
├── README.md
├── requirements.txt
├── .gitignore
└── .gitattributes
```

La entrega actual es el repositorio. Los guiones, Word, PDF de diapositivas, guía de estudio, figuras sueltas, tablas auxiliares, ZIP, datos y entorno local quedan excluidos de la versión publicada. Los resultados y gráficos analíticos se conservan dentro del notebook ejecutado. El historial de Git mantiene la trazabilidad de las revisiones anteriores.

## Datos y metodología

Fuente: [A. Kannal, Solar Power Generation Data, Kaggle](https://www.kaggle.com/datasets/anikannal/solar-power-generation-data). Cada integrante o evaluador descarga los dos CSV de Planta 1 y los coloca en `datos/`:

- `Plant_1_Generation_Data.csv`: 68778 registros de generación de 22 inversores.
- `Plant_1_Weather_Sensor_Data.csv`: 3182 registros de irradiación y temperaturas.

Periodo: 15-may a 17-jun-2020, cada 15 minutos; 34 días y 3264 instantes esperados. Los datos corresponden a India; el caso empresarial colombiano no acredita transferencia automática a una instalación local.

La generación se agrupa por planta y fecha y se une al clima sobre una rejilla completa. `AC_PLANTA_KW` exige los 22 valores AC válidos. Se distinguen ceros medidos de ausencias: 1333 ceros sin irradiación, 54 con irradiación baja y 207 instantes sin potencia completa. Los 63 registros de inversores individuales en cero con sol requieren revisión y no representan 63 apagones de planta.

No se interpolan objetivos ni predictores. Se conservan candidatos atípicos IQR sin atribuirles una falla. La mediana DC/AC de 10.2202 se documenta como anomalía de escala, sin corregirla arbitrariamente.

Los rezagos se construyen por fecha exacta, antes de filtrar noches, a 15/30/60 minutos. La fila objetivo `s` se pronostica en `s−15 min`. Potencia y clima del objetivo, DC y energías acumuladas no son predictores permitidos. El umbral diurno de 0.05 kW/m² define una evaluación retrospectiva; una activación operativa conocida al emitir requiere nueva validación.

La muestra común tiene 1479 filas: 1169 de entrenamiento (27 días) y 310 de prueba (7 días, desde 11-jun). Los terciles finales de entrenamiento son 10320.260119 y 18331.648214 kW. Se recalculan dentro de cada entrenamiento de los 5 pliegues temporales por días; prueba no define cortes ni escalado. La semilla es 42.

## Resultados y recomendación

| Método | Accuracy en prueba | F1 macro en prueba |
|---|---:|---:|
| Clase mayoritaria | 0.216 | 0.118 |
| Persistencia | **0.803** | **0.795** |
| Regresión logística | 0.790 | 0.782 |
| KNN | 0.774 | 0.770 |
| Árbol de referencia | 0.735 | 0.732 |
| Árbol postpodado · revisión exploratoria | 0.790 | 0.790 |

KNN fue seleccionado por validación temporal: 21 vecinos, ponderación por distancia, historia de potencia y hora; F1 CV 0.7958. Persistencia obtuvo CV 0.7506, pero funcionó mejor en los días de prueba. No se cambia retrospectivamente la selección mirando el test.

La postpoda con alpha 0.01 elegido en CV redujo el árbol de 232 a 6 hojas. Se presenta como revisión exploratoria sobre una prueba ya consultada, no como validación externa nueva.

KNN tuvo 53/310 instantes con nivel real inferior al anunciado, frente a 31/310 de persistencia. Cumplió 96/105 avisos bajos (precisión 91.4 %) y detectó 96/114 bajos reales (recall 84.2 %).

Se recomienda **un piloto supervisado**, conservando persistencia como referencia. Datos incompletos producen un estado no evaluable. El operador verifica condiciones, duración y seguridad antes de intervenir; las discrepancias requieren contrastar clima, inversores y bitácoras. No se demuestran averías, duración de ventanas, kWh perdidos ni ahorros. Una planta y 34 días limitan la generalización.

## Reproducir el notebook

Entorno verificado: Python 3.14.3 en Windows. Las versiones directas se fijan en `requirements.txt`.

```powershell
git clone https://github.com/MekiahxAndres/proyecto3-solar-planta1.git
cd proyecto3-solar-planta1
py -3.14 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m ipykernel install --prefix .venv --name solar-planta1 --display-name "Python (Solar Planta 1)"
.\.venv\Scripts\python.exe -m jupyter lab
```

Colocar los CSV en `datos/`, abrir el notebook, elegir el kernel del entorno, reiniciar y ejecutar todas las celdas. También se puede ejecutar desde la raíz con:

```powershell
.\.venv\Scripts\python.exe -m jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.kernel_name=solar-planta1 --ExecutePreprocessor.timeout=360 notebooks/Proyecto3_Planta1.ipynb
```

El notebook no requiere guiones, Word, figuras externas ni scripts privados. Sus gráficos permanecen en las salidas. Por defecto `EXPORTAR=False`: no crea figuras ni tablas auxiliares. Con `EXPORTAR=True` pueden regenerarse localmente en `informe/figuras/`, ignoradas por Git. Las tablas derivadas `Planta1_crudo_unificado.csv` y `Planta1_tabla_modelado.csv` se guardan localmente en `datos/` y tampoco se publican.

## Verificación y uso de IAG

El notebook incluye controles de sumas, fechas, rezagos, cortes por pliegue, ausencia de fuga temporal y hashes de originales. Se conserva ejecutado de principio a fin, con las salidas visibles. El informe tiene 5 páginas y el HTML 13 láminas. El cambio a entrega por repositorio no altera los modelos ni sus resultados.

La sustentación ya se realizó, según confirmación del equipo. El uso real de ChatGPT/Codex se declara en el informe y en el registro del notebook. La responsabilidad de comprender y defender las decisiones corresponde a los integrantes.
