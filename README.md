# Proyecto 3: clasificación solar para apoyar operación y mantenimiento, Planta 1

Analítica de Datos I, Universidad Icesi, 2026-02. Miguel Ángel Tabares, Isabella Londoño Lerma y Andrés Felipe Rivas, Ingeniería en Energía Inteligente.

## Organización

```text
Proyecto/
├── consigna/       PDF del proyecto y guías de semanas 2, 3 y 4
├── datos/          CSV originales y tablas derivadas locales, excluidos de Git
├── notebooks/      Proyecto3_Planta1.ipynb: calidad, EDA conjunto y clasificación
├── informe/        Informe IEEE en PDF y Word; figuras/tablas en figuras/
├── presentacion/   Diapositivas PDF y guion de sustentación en PDF/Word
├── README.md
├── requirements.txt
└── .gitignore
```

`.venv/` contiene el entorno Python local y `.vscode/` selecciona el intérprete. Son carpetas de configuración. El análisis, sus gráficas, las decisiones y el registro de IA se concentran en el notebook.

## Notebook integrado del 21 de noviembre

Trabajo en la rama `avance/eda-clima-modelado`, separada de `main`. Un solo notebook reúne las seis fases CRISP-DM: contexto con fuentes, tres preguntas analíticas, unión cruda de las dos fuentes, calidad y EDA, preparación, modelado, evaluación y propuesta de uso en O&M. Se mantienen las cinco carpetas de trabajo; las figuras exportadas se agrupan dentro de `informe/figuras/`.

La clasificación a 15 minutos apoya la identificación de candidatos a periodos de baja generación. No garantiza la duración de una ventana ni autoriza mantenimiento. Una clase baja no equivale a potencia cero o avería. Los registros incompletos se distinguen de los ceros físicos; no se imputan como baja generación.

Están disponibles `Plant_1_Generation_Data.csv` y `Plant_1_Weather_Sensor_Data.csv`, de [Solar Power Generation Data, Ani Kannal, Kaggle](https://www.kaggle.com/anikannal/solar-power-generation-data). El original `Proyecto_Generacion.ipynb` de Tabares se revisó y reprodujo sin modificarlo; la revisión y los aportes integrados se documentan dentro del notebook del equipo.

La clasificación compara clase mayoritaria, persistencia, logística, KNN y árbol, con una muestra común, corte por días (prueba desde 11-jun-2020) y cinco pliegues temporales. Los terciles se calculan de nuevo con el entrenamiento de cada pliegue; el escalado está en Pipeline. Incluye matrices por fila en porcentaje y conteos, reportes por clase, curvas de validación, explicación de los 21 vecinos del KNN y dos rutas completas del árbol con números de nodo.

La revisión añade postpoda del árbol con `ccp_alpha` elegido solo en CV sobre entrenamiento, además de la profundidad y el tamaño mínimo de hoja de la referencia. Se deja crecer el árbol y se compara su complejidad con la versión podada. El árbol completo se exporta a PDF vectorial para ampliar; las hojas no se fuerzan a tener impureza cero.

El benchmark del 17-nov (`fb61bc3`) se conserva: 1.169 filas de entrenamiento y 310 de prueba. KNN fue seleccionado por validación (F1 macro medio 0,796), pero en prueba obtuvo 0,770 frente a 0,795 de persistencia. La postpoda figura como revisión exploratoria separada. Los siete días de prueba ya se consultaron en el avance: volver a evaluarlos no es una nueva prueba externa. No se cambia la selección mirando sus resultados; para confirmar mejoras hacen falta nuevos periodos.

La discusión cuantifica la fiabilidad y cobertura de los avisos de nivel bajo, las discrepancias con generación real inferior a la anunciada y sus riesgos. Incluye límites, impactos, propuesta de piloto supervisado y autoauditoría con evidencias. La revisión humana y la comprensión de los tres integrantes permanecen pendientes de confirmar.

No se interpolan predictores ni objetivos. El umbral diurno de trabajo es 0,05 kW/m², tomado de la guía antes de ajustar modelos. Define una evaluación diurna retrospectiva, no una medición futura permitida como predictor. El notebook detalla qué filas se excluyen y verifica la disponibilidad temporal de cada rezago.

Al ejecutar se generan **solo localmente**, dentro de `datos/`:

- `Planta1_crudo_unificado.csv`: 3.264 instantes esperados, sumas observadas, clima, contadores y banderas de origen/calidad/ceros, sin filtrar ni rellenar huecos.
- `Planta1_tabla_modelado.csv`: objetivos observados completos, predictores pasados, fecha de emisión, partición y nivel con los cortes de entrenamiento.

El contexto bibliográfico y las tres preguntas ya están incorporados. El informe IEEE se preparó el 21-nov en cinco páginas: `informe/Informe_Proyecto3_Planta1_IEEE.pdf`, con su fuente editable `.docx`. Incluye resumen de 197 palabras, cuatro términos, cuatro figuras, tabla con los seis métodos/configuraciones, discusión por pregunta, despliegue supervisado y diez referencias con enlaces. Las métricas se contrastaron con las exportadas por el notebook y las cinco páginas se revisaron visualmente.

Se aplicó una maquetación IEEE a dos columnas y las reglas de la guía. No se encontró identificada la plantilla específica del curso: queda pendiente cotejarla cuando esté disponible, junto con la revisión de las decisiones e interpretaciones por los tres integrantes. La auditoría dentro del notebook conserva el estado de su ejecución anterior a redactar el informe. Queda pendiente el ensayo del equipo. Los resultados simulados de las guías y los del procedimiento de Tabares no se copian como resultados del procedimiento corregido.

## Diapositivas y guion de sustentación

`presentacion/Sustentacion_Proyecto3_Planta1.pdf` contiene 12 láminas para el recorrido de doce minutos y tres de apoyo para preguntas. Las gráficas se adaptaron con los mismos datos y resultados del informe; la comparación distingue CV de prueba y conserva la postpoda como revisión exploratoria. La presentación editable se guardó en Spaces y está enlazada en el chat de preparación, con las tablas y notas del expositor editables.

El reparto propuesto es Miguel Ángel Tabares, láminas 1–4 (0:00–4:00); Isabella Londoño Lerma, 5–8 (4:00–8:00); Andrés Felipe Rivas, 9–12 (8:00–12:00). El tiempo total objetivo es 720 segundos, con 240 por persona. Debe comprobarse con un ensayo cronometrado.

`presentacion/Guion_Sustentacion_Proyecto3.pdf` reúne el texto de cada intervención, tiempos por lámina, qué señalar, transiciones, diez preguntas de defensa y práctica de cambios en vivo. Su fuente editable es `Guion_Sustentacion_Proyecto3.docx`. El guion tiene siete páginas y es material de preparación; no sustituye los tres productos finales solicitados para el ZIP. Las láminas A1–A3 se consultan únicamente durante las preguntas. Los tres integrantes deben comprender el proyecto completo y revisar lo que van a sustentar.

## Ejecutar

Abrir esta carpeta en VS Code y `notebooks/Proyecto3_Planta1.ipynb`. Elegir el kernel de `.venv`: **Python (Solar Planta 1)**. Ejecutar todas las celdas en orden.

El entorno de este computador ya está instalado (Python 3.14.3). Para otro computador, después de clonar el repositorio:

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m ipykernel install --prefix .venv --name solar-planta1 --display-name "Python (Solar Planta 1)"
```

Cada integrante descarga los dos CSV de Planta 1 y los coloca directamente en `datos/`, con sus nombres originales. El notebook usa rutas relativas. También se puede iniciar Jupyter desde la raíz con `.\.venv\Scripts\python.exe -m jupyter lab`.

Para ejecutar el notebook fuera del repositorio, basta con ubicar los dos CSV junto a él o en una carpeta `datos/` cercana y seleccionar un entorno con las dependencias. La búsqueda no depende de `consigna/` ni de `requirements.txt`. `EXPORTAR=False` desactiva las figuras y tablas auxiliares; las dos tablas de datos se guardan en la carpeta local de datos. No se necesita acceso a Internet durante el análisis.

Las figuras para el informe se generan directamente en PNG a 300 dpi y PDF vectorial; la tabla de resultados se exporta a JSON y LaTeX con las mismas cifras calculadas. El informe debe seleccionar 4–5 figuras relevantes, sin capturas del notebook. Estos archivos de apoyo no sustituyen los tres productos finales.

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
