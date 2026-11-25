# Proyecto 3 · Clasificación solar para apoyar operación y mantenimiento

**Analítica de Datos I · Universidad Icesi · 2026-02**
Miguel Ángel Tabares, Isabella Londoño Lerma y Andrés Felipe Rivas. Ingeniería en Energía Inteligente.

Anticipamos el nivel **bajo, medio o alto de la Planta 1 a 15 minutos**, con información disponible al emitir el pronóstico. La empresa del caso utiliza esa información para valorar periodos de mantenimiento y revisar discrepancias cuando la generación real es inferior a la anunciada.

**Resultado:** persistencia obtuvo F1 macro **0.795**, frente a **0.770** del KNN seleccionado previamente por validación. Los resultados no justifican reemplazar esa línea base. Se propone un **piloto supervisado**, con telemetría comprobada y autorización humana. Una clase baja no diagnostica avería ni garantiza duración suficiente de una ventana de trabajo.

## Dónde empezar

| Archivo | Contenido |
|---|---|
| [Notebook](notebooks/Proyecto3_Planta1.ipynb) | Las seis fases CRISP-DM, código, decisiones, tablas y 26 figuras ejecutadas |
| [Informe IEEE](informe/Informe_Proyecto3_Planta1_IEEE.pdf) | Cinco páginas, resultados, discusión, recomendación, IAG y diez referencias |
| [Informe editable](informe/Informe_Proyecto3_Planta1_IEEE.docx) | Fuente Word del informe |
| [Diapositivas PDF](presentacion/Sustentacion_Proyecto3_Planta1.pdf) | 12 láminas de contenido y una final de agradecimiento |
| [Presentación HTML](presentacion/Sustentacion_Proyecto3_Planta1.html) | Versión interactiva autónoma, sin internet |
| [Guion vigente](presentacion/Guion_Sustentacion_Proyecto3_Actualizado.pdf) | Tiempos, intervenciones, preguntas y práctica de cambios en vivo |
| [Guion editable](presentacion/Guion_Sustentacion_Proyecto3.docx) | Fuente Word con el mismo reparto |
| [Guía de gráficos](presentacion/Guia_Defensa_Graficos.html) | Explicación de los 26 gráficos reales: ejes, unidades, colores, valores y lectura O&M |

La versión final para proyectar es el HTML, con PDF de entrega equivalente. Funciona sin internet. La interfaz muestra únicamente el contador de diapositivas: sin notas, explicaciones, índice ni cronómetro. Navegar con flechas, espacio o clic (zona izquierda: anterior; resto: siguiente), o deslizar en una pantalla táctil. F activa pantalla completa. Las animaciones revelan registros reales, comparaciones y reglas; respetan movimiento reducido. Los indicadores y las conclusiones están escritos en cada lámina para guiar la exposición. El guion y la guía de gráficos se consultan por separado para estudiar.

## Organización

```text
Proyecto/
├── consigna/       Consigna específica y guías de semanas 2, 3 y 4
├── datos/          CSV originales y dos tablas derivadas; solo locales
├── notebooks/      Un único notebook final
├── informe/        PDF, Word y figuras/tablas exportadas en figuras/
├── presentacion/   PDF, HTML, guion y guía de estudio
├── README.md
├── requirements.txt
└── .gitignore
```

Se mantienen cinco carpetas de trabajo. `.venv/` es el entorno local y `.vscode/` su configuración. El código analítico se concentra en el notebook; no depende de scripts temporales ni módulos privados.

## Datos y decisiones metodológicas

Fuente: [Ani Kannal, Solar Power Generation Data](https://www.kaggle.com/datasets/anikannal/solar-power-generation-data). Se utilizan exclusivamente los dos CSV de **Planta 1**. Los datos corresponden a India; el caso de aplicación es colombiano y requiere validación local antes de transferir resultados.

| Fuente | Registros | Uso |
|---|---:|---|
| `Plant_1_Generation_Data.csv` | 68778 | Potencia por inversor; 22 equipos |
| `Plant_1_Weather_Sensor_Data.csv` | 3182 | Irradiación y temperaturas ambiente/módulo |

Periodo: 15-may a 17-jun-2020, cada 15 minutos, **34 días y 3264 instantes esperados**. Se interpretan los formatos de fecha de cada archivo y se agrupa por planta/fecha. La unión sobre una rejilla completa conserva instantes ausentes y banderas de procedencia. `AC_OBSERVADA_KW` conserva sumas recibidas; `AC_PLANTA_KW` exige los **22 valores AC válidos**, para no confundir una suma parcial con la potencia de toda la planta.

La producción nula sin irradiación es esperable. La auditoría separa **1333 ceros sin irradiación**, **54 con irradiación baja**, **207 instantes sin potencia completa** y **63 registros de inversores individuales en cero con sol**, estos últimos en 28 instantes y nueve equipos. No hubo ceros completos de planta por encima del umbral. Un registro ausente nunca se convierte en cero.

El umbral de trabajo de la guía, **0.05 kW/m²**, se fijó antes de ajustar modelos. El filtro usa irradiación del objetivo para definir una **evaluación diurna retrospectiva**; no es un predictor futuro permitido. Operar requiere una activación conocida al emitir y nueva validación. No se interpolan objetivos ni predictores. Los candidatos IQR por hora (factor 3, límites aprendidos en train) se conservan sin atribuirles una falla. La mediana DC/AC **10.2202** se documenta como anomalía de escala; no se divide DC arbitrariamente por diez.

Se construyen rezagos **por fecha exacta**, antes de filtrar noches, a 15/30/60 minutos. La fila objetivo `s` se pronostica en `s−15 min`. Potencia/clima de `s`, DC, acumulados e identificadores se excluyen como predictores. Se comparan potencia a 15 min y hora; historia de potencia y hora; historia más los tres sensores pasados. La muestra común permite una comparación justa entre grupos.

Quedan **1479 filas**: **1169** de entrenamiento (27 días, hasta 10-jun) y **310** de prueba (7 días, desde 11-jun). Los terciles finales son **10320.260119 y 18331.648214 kW**: bajo ≤ primer corte, medio > primero y ≤ segundo, alto > segundo. Se recalculan dentro de cada entrenamiento de CV; prueba nunca define los cortes. Distribución train: 390/389/390; prueba: 114/129/67, en orden bajo/medio/alto.

## Modelos y resultados

Las dos líneas base son clase mayoritaria (`DummyClassifier`) y persistencia de la clase anterior. Se comparan con logística, KNN y árbol. `TimeSeriesSplit(n_splits=5)` se aplica sobre días, con ventanas expansivas. Escalado de logística/KNN dentro de `Pipeline`, ajustado solo con train. Criterio previo de selección: **F1 macro medio de CV**; se informa también accuracy, precisión/recall/F1 por clase y kappa.

Selección original por CV: KNN con **21 vecinos**, ponderación por distancia, potencia a 15/30/60 min y hora (F1 CV **0.7958**). Persistencia tuvo CV **0.7506**. Logística: C=10, potencia a 15 min y hora. Árbol de referencia: profundidad máxima 7, mínimo 5 por hoja, historia de potencia y hora.

| Método | Accuracy | F1 macro | Kappa | F1 bajo | F1 medio | F1 alto |
|---|---:|---:|---:|---:|---:|---:|
| Clase mayoritaria | 0.216 | 0.118 | 0.000 | 0.000 | 0.000 | 0.355 |
| Persistencia | 0.803 | 0.795 | 0.695 | 0.886 | 0.767 | 0.731 |
| Logística | 0.790 | 0.782 | 0.675 | 0.877 | 0.752 | 0.716 |
| KNN | 0.774 | 0.770 | 0.658 | 0.877 | 0.722 | 0.713 |
| Árbol | 0.735 | 0.732 | 0.598 | 0.840 | 0.678 | 0.679 |
| Árbol postpodado · revisión | 0.790 | 0.790 | 0.678 | 0.867 | 0.757 | 0.747 |

La postpoda es una **revisión exploratoria**: alpha 0.01 se eligió en CV, con F1 0.7455; redujo el ajuste de 232 a seis hojas y de profundidad 15 a tres. No cambia el candidato seleccionado anteriormente ni convierte los siete días ya consultados en una prueba externa nueva. El historial de referencia del 17-nov (`fb61bc3`) se conserva.

### Respuestas al caso de O&M

- **Calidad:** distinguir ceros físicos y telemetría incompleta evita fabricar periodos de baja generación. Los eventos individuales requieren estados y bitácoras para determinar su causa.
- **Aporte del modelo:** persistencia acertó 249/310 y KNN 240/310. El error real medio/anunciado alto fue 18/129 (14.0 %) y 35/129 (27.1 %), respectivamente. Añadir clima cambió F1 CV en −0.020 KNN, +0.001 logística y −0.005 árbol; no hubo mejora uniforme.
- **Riesgo:** KNN tuvo 53/310 instantes con real inferior al anunciado (17.1 %), frente a 31/310 de persistencia (10.0 %). Sus avisos bajos se cumplieron en 96/105 casos (precisión 91.4 %) y detectaron 96/114 bajos reales (recall 84.2 %). Son discrepancias de clase, no kWh perdidos o fallas confirmadas.

En un piloto, el operador comprobaría fechas, cobertura y entradas antes de mostrar el nivel siguiente. Si faltan entradas: **no evaluable**. Ante un aviso bajo, revisaría duración de la tarea, condiciones y seguridad. Ante real inferior al anunciado, contrastaría clima, inversores y bitácoras. Un modelo que usa potencia pasada puede adaptarse a una reducción sostenida: vigilar degradación requiere una referencia independiente.

## Instalación y ejecución

Entorno verificado: **Python 3.14.3 en Windows**, con versiones fijadas en `requirements.txt`. No hace falta acceso a internet durante el análisis una vez instaladas dependencias y descargados los CSV.

```powershell
git clone https://github.com/MekiahxAndres/proyecto3-solar-planta1.git
cd proyecto3-solar-planta1
git switch main
py -3.14 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m ipykernel install --prefix .venv --name solar-planta1 --display-name "Python (Solar Planta 1)"
.\.venv\Scripts\python.exe -m jupyter lab
```

Cada integrante descarga los dos CSV y los coloca en `datos/`, con sus nombres originales. Abrir `notebooks/Proyecto3_Planta1.ipynb`, seleccionar el kernel del entorno, reiniciar y ejecutar todas las celdas. También se puede ejecutar con:

```powershell
.\.venv\Scripts\python.exe -m jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.kernel_name=solar-planta1 --ExecutePreprocessor.timeout=360 notebooks/Proyecto3_Planta1.ipynb
```

Para el notebook de entrega fuera del repositorio, ubicar los CSV junto a él o en una carpeta `datos/` cercana. Las rutas se resuelven desde la ubicación de ejecución. `EXPORTAR=False` omite figuras/tablas de apoyo; las dos tablas derivadas se guardan localmente junto a los datos. No hacen falta la consigna ni el README para ejecutar el análisis.

Se generan localmente `datos/Planta1_crudo_unificado.csv` y `datos/Planta1_tabla_modelado.csv`. Las figuras para el informe se exportan en PNG 300 dpi/PDF vectorial; las métricas se guardan en `informe/figuras/tabla_resultados.json` y `.tex`. `evidencia_ejecucion.json` conserva tamaños, cortes, hashes y riesgos.

## Auditoría técnica del 22 de noviembre y cierre visual del 24

| Requisito | Evidencia y estado |
|---|---|
| Tarea 1: contexto, física, PR y preguntas | Introducción del notebook/informe, fuentes primarias y P1–P3. No se calcula PR sin capacidad nominal validada. |
| Tarea 2: unión, faltantes, ceros, escala y EDA | Rejilla, contadores, calidad, histogramas/cajas, clima, perfiles, correlaciones y clases. |
| Tarea 3: modelos, ajuste e interpretación | Dos baselines, tres familias, 5 pliegues por días, escalado, train/prueba, reportes, matrices, coeficientes y rutas. |
| Tarea 4: resultados y recomendación | Informe de 5 páginas, resumen de 197 palabras, 4 términos, tabla por clase, matrices, reglas, importancia e IAG. |
| Ejecución | Notebook reiniciado y ejecutado: 52 celdas, 30 de código, 26 gráficos, cero errores de celda. |
| Verificación independiente | Sumas por inversor, clima, rezagos, cortes, etiquetas y fecha de emisión contrastados por otra vía. Hashes originales intactos. Lectura portable comprobada. |
| Sustentación | HTML final sin paneles de estudio, PDF equivalente y guion alineado; 12 de contenido y una final de agradecimiento. Animaciones con datos reales y controles únicamente por teclado, clic o gesto. |
| Git y entrega | CSV, entorno, ZIP y secretos excluidos. El ZIP de entrega contiene solo tres productos. |

**Límites que permanecen:** una planta y 34 días, siete de prueba ya observados, exclusión de telemetría incompleta y activación diurna operativa aún sin validar. No se acreditan ahorros ni diagnóstico de fallas. Se aplicaron las reglas IEEE a dos columnas de la guía; la plantilla específica del curso «Formato presentación documentos» no se identificó y su cotejo queda pendiente. Los PDF genéricos encontrados de otros cursos no se consideran la plantilla oficial. El ensayo cronometrado, comprensión individual y revisión humana no se marcan como realizados por una comprobación automática.

## Sustentación y colaboración

| Integrante | Láminas | Tiempo |
|---|---|---|
| Isabella Londoño Lerma | 1–4 | 0:00–4:00 |
| Miguel Ángel Tabares | 5–8 | 4:00–8:00 |
| Andrés Felipe Rivas | 9–12 | 8:00–12:00 |

La lámina 13 cierra con «Muchas gracias»; las 12 anteriores mantienen el recorrido de doce minutos. Todos deben poder explicar y modificar el notebook sin apoyo de IAG en la defensa. Usar la guía de gráficos para estudiar, el guion para ensayar y los PDF finales para entregar.

La versión revisada se integra en `main`; `avance/eda-clima-modelado` conserva la preparación. Para nuevas contribuciones, crear una rama desde `main`, ejecutar el notebook y revisar el diff antes de integrar. Los colaboradores acceden al repositorio privado una vez el propietario los invite. No subir datos del dataset ni el entorno.

## Entrega del curso

Fecha: **24-nov-2026 a las 11:59 p. m.** Sustentación: **25-nov**, 12 minutos y 8 de preguntas.

ZIP local: `Proyecto3_Grupo_Tabares_Londono_Rivas.zip`, con exactamente:

1. `Proyecto3_Planta1.ipynb` ejecutado y con salidas.
2. `Informe_Proyecto3_Planta1_IEEE.pdf` (máximo cinco páginas).
3. `Sustentacion_Proyecto3_Planta1.pdf`.

Sin CSV, entorno, Word, HTML, guion, figuras sueltas ni carpetas temporales dentro del ZIP. El paquete no se versiona y debe regenerarse si cambia uno de esos tres archivos. El propietario realiza el envío en la plataforma. Un ZIP preparado no equivale a una entrega ya enviada.

El uso real de ChatGPT/Codex está declarado en el informe y en el registro del notebook. La responsabilidad de comprender, revisar y defender las decisiones corresponde a los integrantes.
