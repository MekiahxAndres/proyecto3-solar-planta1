# Proyecto 3: nivel de generación solar, Planta 1

Analítica de Datos I, Universidad Icesi, 2026-02. Equipo de tres integrantes (nombres pendientes).

## Organización

```text
Proyecto/
├── consigna/       PDF del proyecto y guías de series de tiempo/modelado
├── datos/          CSV originales y tablas derivadas locales, excluidos de Git
├── notebooks/      Proyecto3_Planta1.ipynb: calidad, EDA conjunto y clasificación
├── informe/        Futuro informe IEEE en PDF
├── presentacion/   Futuras diapositivas en PDF
├── README.md
├── requirements.txt
└── .gitignore
```

`.venv/` contiene el entorno Python local y `.vscode/` selecciona el intérprete. Son carpetas de configuración. El análisis, sus gráficas, las decisiones y el registro de IA se concentran en el notebook.

## Avance del 17 de noviembre: revisión del 18

Trabajo en la rama `avance/eda-clima-modelado`, separada de `main`. El notebook integra los dos archivos de Planta 1, distingue ceros de ausencias y conserva una tabla cruda sin interpolación. Incluye cobertura por fuente, EDA de potencia/irradiación/temperaturas, perfiles diarios, mapa día-hora y correlaciones con rezagos, con lecturas breves de cada gráfica.

Están disponibles `Plant_1_Generation_Data.csv` y `Plant_1_Weather_Sensor_Data.csv`, de [Solar Power Generation Data, Ani Kannal, Kaggle](https://www.kaggle.com/anikannal/solar-power-generation-data). El original `Proyecto_Generacion.ipynb` de Tabares se revisó y reprodujo sin modificarlo; la revisión y los aportes integrados se documentan dentro del notebook del equipo.

La clasificación compara clase mayoritaria, persistencia, logística, KNN y árbol, con una muestra común, corte por días (prueba desde 11-jun-2020) y cinco pliegues temporales. Los terciles se calculan de nuevo con el entrenamiento de cada pliegue; el escalado está en Pipeline. La elección se hace con F1 macro de validación, antes de evaluar prueba. Se reportan métricas por clase, `classification_report`, matrices de confusión, curvas de validación, coeficientes, reglas e importancia del árbol.

Resultado de este avance: 1.169 filas de entrenamiento y 310 de prueba. KNN fue seleccionado por validación (F1 macro medio 0,796), pero en prueba obtuvo 0,770 frente a 0,795 de persistencia. El notebook conserva ese resultado y no cambia la elección después de ver la prueba: no hay evidencia aquí para sustituir la línea base por un modelo más complejo.

No se interpolan predictores ni objetivos. El umbral diurno de trabajo es 0,05 kW/m², tomado de la guía antes de ajustar modelos. Define una evaluación diurna retrospectiva, no una medición futura permitida como predictor. El notebook detalla qué filas se excluyen y verifica la disponibilidad temporal de cada rezago.

Al ejecutar se generan **solo localmente**, dentro de `datos/`:

- `Planta1_crudo_unificado.csv`: 3.264 instantes esperados, sumas observadas, clima, contadores y banderas de origen/calidad/ceros, sin filtrar ni rellenar huecos.
- `Planta1_tabla_modelado.csv`: objetivos observados completos, predictores pasados, fecha de emisión, partición y nivel con los cortes de entrenamiento.

El contexto bibliográfico de la Tarea 1, las preguntas analíticas definitivas, la revisión del equipo/docente y los entregables IEEE/presentación siguen pendientes. Los resultados de las guías simuladas y los del procedimiento de Tabares no se copian como resultados del procedimiento corregido.

## Ejecutar

Abrir esta carpeta en VS Code y `notebooks/Proyecto3_Planta1.ipynb`. Elegir el kernel de `.venv`: **Python (Solar Planta 1)**. Ejecutar todas las celdas en orden.

El entorno de este computador ya está instalado (Python 3.14.3). Para otro computador, después de clonar el repositorio:

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m ipykernel install --prefix .venv --name solar-planta1 --display-name "Python (Solar Planta 1)"
```

Cada integrante descarga los dos CSV de Planta 1 y los coloca directamente en `datos/`, con sus nombres originales. El notebook usa rutas relativas. También se puede iniciar Jupyter desde la raíz con `.\.venv\Scripts\python.exe -m jupyter lab`.

## Colaboración y entrega

Repositorio privado del equipo: [MekiahxAndres/proyecto3-solar-planta1](https://github.com/MekiahxAndres/proyecto3-solar-planta1). El propietario agregará después a los otros dos integrantes como colaboradores. Los CSV y `.venv` quedan fuera del repositorio.

Para empezar en otro computador:

```powershell
git clone https://github.com/MekiahxAndres/proyecto3-solar-planta1.git
cd proyecto3-solar-planta1
git switch avance/eda-clima-modelado
```

Después, seguir los pasos de instalación y ubicar los CSV en `datos/`.

Entrega: 24 de noviembre de 2026, 11:59 p. m., ZIP `Proyecto3_Grupo_ApellidosMiembros.zip` con un notebook ejecutado, informe IEEE PDF de máximo cinco páginas y diapositivas PDF. Sin CSV. Sustentación: 25 de noviembre, 12 minutos y 8 de preguntas. El registro de uso de IA se conserva al final del notebook.

La configuración anterior se conserva fuera del proyecto, en `D:/Analitica de Datos/Respaldo_configuracion_2026-11-11/`.
