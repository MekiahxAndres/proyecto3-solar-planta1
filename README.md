# Proyecto 3: nivel de generación solar, Planta 1

Analítica de Datos I, Universidad Icesi, 2026-02. Equipo de tres integrantes (nombres pendientes).

## Organización

```text
Proyecto/
├── consigna/       PDF del proyecto y guía de series de tiempo
├── datos/          CSV locales, sin modificaciones y excluidos de Git
├── notebooks/      Proyecto3_Planta1.ipynb: calidad, EDA, gráficas y futuras etapas
├── informe/        Futuro informe IEEE en PDF
├── presentacion/   Futuras diapositivas en PDF
├── README.md
├── requirements.txt
└── .gitignore
```

`.venv/` contiene el entorno Python local y `.vscode/` selecciona el intérprete. Son carpetas de configuración. El análisis, sus gráficas, las decisiones y el registro de IA se concentran en el notebook.

## Avance del 11 de noviembre

Calidad de datos y EDA del archivo de generación: estructura, fechas, duplicados, cobertura temporal, inversores faltantes, potencia total, distribuciones, perfil diario, mapa día-hora, escala DC/AC, candidatos atípicos y autocorrelación. Las salidas y la interpretación descriptiva se guardan en el notebook.

Está disponible `Plant_1_Generation_Data.csv`. Falta `Plant_1_Weather_Sensor_Data.csv`, requerido en las páginas 3 y 6 de la consigna y en la fila «1 y 3» de la tabla de datos del curso. Ambos pertenecen a [Solar Power Generation Data, de Ani Kannal en Kaggle](https://www.kaggle.com/anikannal/solar-power-generation-data). El segundo aporta irradiación y temperaturas; no se sustituyen con datos simulados. Las relaciones potencia-clima y el filtro de horas de sol quedarán pendientes hasta incorporarlo.

Este es un avance exploratorio, sin modelos entrenados. Las reglas de calidad se presentan para revisión del equipo. El contexto energético, las fuentes de la Tarea 1, las decisiones definitivas de preparación y las etapas de modelado se completarán después.

## Ejecutar

Abrir esta carpeta en VS Code y `notebooks/Proyecto3_Planta1.ipynb`. Elegir el kernel de `.venv`: **Python (Solar Planta 1)**. Ejecutar todas las celdas en orden.

El entorno de este computador ya está instalado (Python 3.14.3). Para otro computador, después de clonar el repositorio:

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

Cada integrante descarga los dos CSV de Planta 1 y los coloca directamente en `datos/`, con sus nombres originales. El notebook usa rutas relativas. También se puede iniciar Jupyter desde la raíz con `.\.venv\Scripts\python.exe -m jupyter lab`.

## Colaboración y entrega

Repositorio privado del equipo: [MekiahxAndres/proyecto3-solar-planta1](https://github.com/MekiahxAndres/proyecto3-solar-planta1). El propietario agregará después a los otros dos integrantes como colaboradores. Los CSV y `.venv` quedan fuera del repositorio.

Para empezar en otro computador:

```powershell
git clone https://github.com/MekiahxAndres/proyecto3-solar-planta1.git
cd proyecto3-solar-planta1
```

Después, seguir los pasos de instalación y ubicar los CSV en `datos/`.

Entrega: 24 de noviembre de 2026, 11:59 p. m., ZIP `Proyecto3_Grupo_ApellidosMiembros.zip` con un notebook ejecutado, informe IEEE PDF de máximo cinco páginas y diapositivas PDF. Sin CSV. Sustentación: 25 de noviembre, 12 minutos y 8 de preguntas. El registro de uso de IA se conserva al final del notebook.

La configuración anterior se conserva fuera del proyecto, en `D:/Analitica de Datos/Respaldo_configuracion_2026-11-11/`.
