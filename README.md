<!--
# Descripción del proyecto

Al operador de telecomunicaciones Interconnect le gustaría poder pronosticar su tasa de cancelación de clientes. Si se descubre que un usuario o usuaria planea irse, se le ofrecerán códigos promocionales y opciones de planes especiales. El equipo de marketing de Interconnect ha recopilado algunos de los datos personales de sus clientes, incluyendo información sobre sus planes y contratos.
## Servicios de Interconnect
Interconnect proporciona principalmente dos tipos de servicios:
1. Comunicación por teléfono fijo. El teléfono se puede conectar a varias líneas de manera simultánea.
2. Internet. La red se puede configurar a través de una línea telefónica (DSL, línea de abonado digital) o a través de un cable de fibra óptica.

Algunos otros servicios que ofrece la empresa incluyen:

- Seguridad en Internet: software antivirus (ProtecciónDeDispositivo) y un bloqueador de sitios web maliciosos (SeguridadEnLínea).
- Una línea de soporte técnico (SoporteTécnico).
- Almacenamiento de archivos en la nube y backup de datos (BackupOnline).
- Streaming de TV (StreamingTV) y directorio de películas (StreamingPelículas)
 
La clientela puede elegir entre un pago mensual o firmar un contrato de 1 o 2 años. Puede utilizar varios métodos de pago y recibir una factura electrónica después de una transacción.

## Descripción de los datos
Los datos consisten en archivos obtenidos de diferentes fuentes:
- contract.csv — información del contrato;
- personal.csv — datos personales del cliente;
- internet.csv — información sobre los servicios de Internet;
- phone.csv — información sobre los servicios telefónicos.
  
En cada archivo, la columna customerID (ID de cliente) contiene un código único asignado a cada cliente. La información del contrato es válida a partir del 1 de febrero de 2020.
-->

## Objetivo de negocio
- Contexto: El operador de telecomunicaciones Interconnect busca mitigar proactivamente la pérdida de su base de clientes y la erosión de sus ingresos recurrentes.
- Meta: Desarrollar e implementar un prototipo predictivo basado en Machine Learning capaz de identificar de manera anticipada la tasa de cancelación (churn) de los usuarios.
- Utilidad: Facultar al equipo de marketing de Interconnect para ejecutar campañas de retención ultra-dirigidas, ofreciendo de forma quirúrgica códigos promocionales y planes especiales de fidelización exclusivamente a los clientes que presentan intenciones de abandonar la compañía, maximizando así la rentabilidad de los incentivos.

## Descripción del dataset
Los datos iniciales se consolidaron mediante la unificación de cuatro fuentes de información distintas, todas vinculadas a través de la columna de identificación clave customerID:
- contract.csv: Información de contratos. Contiene características como fecha de inicio (BeginDate), fecha de baja (EndDate), tipo de contrato (Type: mensual, de 1 o 2 años), facturación electrónica (PaperlessBilling), método de pago (PaymentMethod), cargos mensuales (MonthlyCharges) y cargos totales (TotalCharges). 
- personal.csv: Datos demográficos del cliente (género, si es ciudadano de la tercera edad (SeniorCitizen), si tiene pareja (Partner) o dependientes económicamente).
- internet.csv: Servicios de conectividad web contratados. Detalla el tipo de red (DSL o Fiber optic) y la contratación de servicios adicionales de valor agregado como OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport, StreamingTV y StreamingMovies.
- phone.csv: Información de telefonía fija tradicional, registrando si el cliente dispone de varias líneas de manera simultánea (MultipleLines).

## Metodología
El flujo de ingeniería de datos y modelado se implementó bajo las siguientes fases estrictas de control:
- Ingeniería de Características y Target: Se extrajo la variable objetivo (exited) a partir de la columna EndDate: los clientes con fecha de baja activa se etiquetaron con 1 (cancelaron) y los clientes vigentes marcados con "No" se codificaron con 0. Adicionalmente, se calculó la duración total del cliente dentro de la compañía para enriquecer el modelo.
- Preprocesamiento y Limpieza: Se convirtieron variables críticas como TotalCharges a tipos numéricos continuos. Las ausencias estructurales de información derivadas del cruce de tablas (por ejemplo, clientes telefónicos que no consumen Internet) fueron tratadas estratégicamente mediante la imputación categórica de una nueva clase denominada 'no_service'.
- Codificación y Escalado: Para evitar que las diferencias de magnitudes numéricas sesgaran a los algoritmos, se estandarizaron las variables de cargos financieros (MonthlyCharges, TotalCharges) usando StandardScaler. Las variables categóricas nominales se transformaron mediante técnicas de codificación aptas para evitar la colinealidad.
- Segmentación: Los datos limpios se dividieron bajo el enfoque clásico en conjuntos de entrenamiento (train features) y de prueba (test features) para evaluar objetivamente la capacidad de generalización.

## Modelos utilizados
Para identificar la arquitectura con mayor robustez frente al problema de clasificación binaria, se entrenó y comparó un abanico diverso de algoritmos, optimizándolos mediante búsqueda de cuadrícula con validación cruzada (GridSearchCV de 5 pliegues):
- Línea base (Baseline): DummyClassifier (para fijar el rendimiento de una predicción aleatoria o por clase mayoritaria).
- Modelos Paramétricos y Tradicionales: Regresión Logística (LogisticRegression) y Árboles de Decisión (DecisionTreeClassifier).
- Algoritmos de Ensamble y Potenciación de Gradiente (Gradient Boosting):
  - Regresión logística
  - Árbol de decisión
  - RandomForestClassifier
  - LightGBM (LGBMClassifier)
  - CatBoost (CatBoostClassifier)
  - XGBoost (XGBClassifier)

## Métricas
El éxito industrial y la validación técnica del proyecto se rigieron por las siguientes métricas de evaluación:
- Métrica Primaria: Área Bajo la Curva ROC (AUC-ROC). Esta métrica evalúa la capacidad intrínseca del modelo para discriminar de forma correcta entre un cliente propenso a la cancelación y uno leal, independientemente del umbral de clasificación elegido.
- Métricas Secundarias de Control: Exactitud (Accuracy), Sensibilidad (Recall), Precisión (Precision), puntuación F1 y la puntuación de Precisión Promedio (APS) a fin de auditar la tasa de falsos negativos y falsos positivos en las matrices de confusión.

## Resultados
- Los modelos tradicionales como la Regresión Logística y los Árboles de Decisión mostraron un desempeño moderado pero con dificultades notables de falsos negativos (baja sensibilidad).
- Los hiperparámetros óptimos para los modelos avanzados fueron localizados con éxito; por ejemplo, para DecisionTreeClassifier se determinó un límite de profundidad de 6 (max_depth: 6) y un criterio de entropía, mientras que para los modelos basados en árboles potenciados como CatBoost y LightGBM se estabilizó la tasa de aprendizaje (learning_rate) y regularizaciones para mitigar el sobreajuste.
- Resultado del Ganador: Las arquitecturas de potenciación por gradiente (particularmente CatBoost y LightGBM) demostraron el desempeño más robusto, alcanzando de manera consistente métricas de AUC-ROC en el set de prueba superiores a 0.82 y 0.84 respectivamente, superando holgadamente el desempeño del clasificador base y garantizando una excelente lectura de patrones de negocio no lineales.

## Conclusiones
- Viabilidad del Proyecto: El proyecto de ciencia de datos demostró de forma concluyente que la información transaccional y contractual provista por el departamento de marketing posee un alto poder predictivo para modelar el comportamiento de abandono.
- Diagnóstico del Perfil de Riesgo: El análisis exploratorio intermedio reflejó que factores vinculados al tipo de contrato (usuarios con renovación mes a mes) y cargos financieros elevados incrementan significativamente el peligro de deserción.
- Impacto Comercial: Al desplegar el modelo ganador de Gradient Boosting, Interconnect contará con un sistema inteligente de alerta temprana automatizado. Esto les permitirá interceptar a una gran porción de clientes en riesgo antes de que formalicen su baja, optimizando la asignación de presupuestos promocionales del equipo de marketing y resguardando de forma directa la rentabilidad de la compañía.
