# gestion-analitica-alto-rendimiento



##  Descripción del Proyecto
Este proyecto consiste en el diseño y desarrollo de una base de datos relacional avanzada orientada al alto rendimiento deportivo. A diferencia de las aplicaciones de fitness convencionales, esta arquitectura está diseñada para soportar metodologías de entrenamiento complejas y asimétricas, permitiendo registrar desde esquemas de hipertrofia tradicional hasta protocolos de fuerza máxima con micro-descansos.

El objetivo principal ha sido construir un "motor inteligente" que no solo almacene datos, sino que garantice la integridad transaccional, automatice la auditoría de fatiga y sea capaz de unificar estructuras de datos polimórficas para el análisis de Business Intelligence.

##  Arquitectura y Modelado de Datos
El esquema relacional rompe con el diseño plano habitual mediante la implementación de una jerarquía polimórfica para las métricas de entrenamiento:

* **Core y Sesiones:** Gestión de `Atletas`, catálogo categorizado de `Ejercicios` y registro de `Sesiones` basado en la escala de Esfuerzo Percibido (RPE).
* **Bloques Polimórficos:** La tabla `Bloques_Entrenamiento` actúa como entidad padre. De ella derivan estructuras dependientes según la metodología:
    * `Series_Tradicionales`: Para rutinas estándar (ej. 4x12).
    * `Series_Cluster`: Para técnicas avanzadas que requieren el registro de repeticiones intra-serie y tiempos exactos de micro-descanso.
* **Auditoría:** Tablas dedicadas al registro automático de alertas del sistema (`Auditoria_Fatiga`).

## ⚙️ Diseño Técnico y Lógica de Negocio (Backend SQL)
Para dotar a la base de datos de autonomía y robustez, se han implementado los siguientes mecanismos a nivel de servidor:

* **Transacciones ACID (Stored Procedures):** La inserción de entrenamientos complejos se gestiona mediante procedimientos almacenados (`RegistrarEntrenamientoCluster`). Esto encapsula múltiples inserciones (sesión, bloque, series y micro-pausas) en una única transacción segura, ejecutando un `ROLLBACK` automático en caso de fallo estructural para evitar datos huérfanos.
* **Auditoría Activa (Triggers):** Se ha programado un disparador (`AFTER INSERT`) que evalúa en tiempo real el nivel de RPE de cada nueva sesión. Si un atleta alcanza el fallo muscular o fatiga máxima (RPE 10), el sistema inserta automáticamente una alerta en la tabla de auditoría para el equipo técnico.
* **Optimización de Consultas (Performance Tuning):** Creación estratégica de índices simples y compuestos en claves foráneas y campos de cruce habituales (fechas, cargas, IDs de sesión) para garantizar una latencia mínima al consultar históricos masivos.

##  Analítica Avanzada y Business Intelligence
El proyecto incluye un módulo de consultas analíticas de nivel experto para la extracción de métricas de rendimiento:

1. **Unificación con CTEs y COALESCE:** Mediante vistas complejas (`vw_Analisis_Volumen`) y Common Table Expressions, el sistema calcula el volumen total (tonelaje) de una sesión fusionando matemáticamente las métricas de las series tradicionales y las series cluster, manejando los valores nulos dinámicamente.
2. **Window Functions:** Uso de funciones de partición (`OVER(PARTITION BY)`) para calcular de forma eficiente el porcentaje de impacto de un ejercicio específico sobre la fatiga total de una sesión, sin recurrir a subconsultas anidadas que penalicen el rendimiento.
3. **Reconstrucción Forense:** Consultas diseñadas con `LEFT JOIN` múltiples capaces de reconstruir paso a paso la ejecución cronológica de protocolos avanzados, desglosando, por ejemplo, una rutina de dominadas ejecutada bajo el método Cluster con un esquema exacto de repeticiones de 3-4-4-3 y sus respectivas pausas.

##  Despliegue y Ejecución
El proyecto está encapsulado en un único script SQL compatible con MySQL/MariaDB. 

1. Ejecutar el script principal para levantar la estructura DDL (Tablas, Índices, Vistas).
2. El script incluye la creación del Procedimiento Almacenado y el Trigger.
3. Se inserta un *mock-data* controlado que demuestra la viabilidad del diseño.
4. Al final del archivo se encuentran las *queries* analíticas listas para su ejecución y validación de resultados.
