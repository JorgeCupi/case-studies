# Análisis de riesgo de crédito con Machine Learning. Caso: Mesfix Colombia #
*Un riesgo de crédito es el riesgo de incumplimiento de una deuda que puede surgir de un prestatario que no realiza los pagos requeridos* [[1]](#referencias).

Y es por eso que el análisis del riesgo de crédito tiene una importancia crítica para el sector financiero ya que permite determinar si las personas o instituciones que buscan préstamos tienen una buena probabilidad de pagar su deuda y si lo harán a tiempo. El resultado de dicho análisis la institución financiera determinará:
- Si se realiza un préstamo o no al prestatario,
- Las condiciones del préstamo (montos, tasas de interés, tiempos, penalidades, etc).

La industria financiera se apoya mucho en el uso de tecnología y suelen ser frecuentes adoptadores tempranos a comparación de otras verticales, a tal punto que hay un segmento específico en el mercado tecnológico orientado enteramente a esta vertical. Estamos hablando del movimiento de Tecnología Financiera o 'fintech' por sus siglas en inglés.

## Contexto ##
![Imagen del logo de Mesfix](https://github.com/JorgeCupi/case-studies/blob/master/images/mesfix/mesfixlogo.png?raw=true "Logo de Mesfix")

[Mesfix](https://www.mesfix.com) es una startup colombiana del sector fintech que a través de su plataforma online conecta a empresas (pagadores), proveedores (emisores) y ciudadanos de a pie (inversionistas) en una relación de inversión en la todos ganan.
 
#### ¿Cómo funciona la plataforma? ####
- Una empresa compra productos a sus proveedores y les paga de 30 a 180 días,
- Los proveedores venden las facturas por cobrar en la plataforma de Mesfix,
- A través de una subasta los inversionistas registrados pueden invierten en varias partes de diferentes facturas,
- Al final, los inversionistas reciben un interés acordado y los proveedores cuentan con liquidez necesaria para continuar con sus operaciones.

Mesfix ayuda a que micro y pequeños productores no tengan que sacrificar liquidez o solicitar préstamos para convertirse en proveedores de empresas que realizan pagos incluso en 6 meses. Desde granjeros cosechadores de café y panela a empresas familiares, miles de proveedores se han beneficiado de los casi 3 millones de dolares que de inversión que se han dado en el corto tiempo de vida de la plataforma.

### Objetivos ###
Pensando en la escalabilidad y crecimiento de su negocio, Mesfix desea reemplazar y complementar una serie de procesos actuales usando machine learning para:
-  Mejorar el análisis de riesgo crediticio en nuevas empresas que deseen ingresar en la plataforma,
- Realizar predicciones de mora en deudas de empresas en base a la información que ya tienen al día de hoy. 

### Equipo participante ###
#### Mesfix ####
- Felipe Tascon ([@tascfelo](https://www.twitter.com/tascfelo))
- Juan Sebastian Muñoz
- Manuel Robles 
#### Microsoft ####
- Jorge Cupi ([@jorgecupi](https://www.twitter.com/JorgeCupi))

## Desarrollo ##
Recordemos que tenemos [dos objetivos](#objetivos) claros para los cuales necesitaremos acceder a la base de datos de Mesfix con propósitos diferentes:
- Acceder a la lista de transacciones realizadas en la plataforma para predecir moras de pago en futuras transacciones
- Acceder a la lista de empresas registradas para realizar análisis de riesgo de crédito y aplicarlo en futuras empresas que deseen participar en la plataforma de Mesfix.

### Software y herramientas ###
Para esta solución se usarán las siguientes tecnologías y servicios:
- [MySQL database](https://www.mysql.com/)
- [Azure Machine Learning Studio](https://studio.azureml.net/)
- [Python](https://www.python.org/)
- [Azure App Services](https://azure.microsoft.com/en-us/services/app-service/)

### Metodología DTSP ###
Para el proyecto usaremos partes de la metodología ["Team Data Science Process"](https://github.com/Azure/Microsoft-TDSP) (TDSP) propuesta por Microsoft.
TDSP es una metodología ágil e iterativa orientada hacia proyectos de data science, está diseñada para mejorar la colaboración y eficiencia dentro den un equipo de data science en organizaciones empresariales. TDSP tiene 4 componentes clave para que un proyecto de data science sea exitoso [[2](#referencias)]:
- La definición del ciclo vida,
- Estructura estandarizada del proyecto,
- Infraestructura para la ejecución del proyecto,
- Herramientas para administrar el ciclo de vida.

Específicamente, nos centraremos solamente en usar las recomendaciones para definir el ciclo de vida.

> ***¿Por qué solamente nos enfocamos en un componente de TDSP?**: Básicamente porque TDSP provee componentes para administrar un proyecto desde cero y dado que Mesfix ya cuenta con infraestructura, herramientas propias de repositorio de código, gestión documental y otros no es necesario redundar esfuerzos. En cambio, si aprovecharemos las directrices del manejo del ciclo de vida tomando en cuenta que no tenemos un lineamiento actual*

#### Administración de ciclo de vida del proyecto ####
![Imagen de ciclo de vida de un proyecto de data science de acuerdo a TDSP](https://github.com/JorgeCupi/case-studies/blob/master/images/mesfix/tdsp-lifecycle.jpg?raw=true "Ciclo de vida de un proyecto de data science de acuerdo a TDSP")
- Business understanding [(Exploración del dominio)](#exploración-del-dominio)
- Data acquisition & Understanding [(Adquisición de datos)](#adquisición-de-datos) 
- Modeling [(Modelado)](#modelado) 
- Deployment [(Despliegue) ](#despliegue)
- Customer acceptance [(Aceptación del cliente)](#aceptación-del-cliente)

## Exploración del dominio ##
Este paso es el pilar más fuerte de todo proyecto de data science. No podemos aplicar machine learning o cualquier otro tipo de servicio a un set de datos sin antes tener claro el contexto y dominio del proyecto. 

Estamos en la fase de exploración de dominio y podríamos estar equivocados con aseveraciones iniciales. La ventaja de TDSP es que es una metodología cíclica y siempre podemos volver a realizar este análisis después de cualquier iteración.

Para ganar claridad en el proyecto recordemos nuestros objetivos:
> Objetivo 1: *Mejorar el análisis de riesgo crediticio en nuevas empresas que deseen ingresar en la plataforma*

> Objetivo 2: *Realizar predicciones de mora en deudas de empresas en base a la información que ya tienen al día de hoy*

¿Tenemos en claro el alcance del proyecto? 
Parece que sí, para esta primera iteración descartaremos a uno de los actores principales en el giro de negocio de Mesfix: a los proveedores, y nos enfocaremos solamente entre los dos restantes y la relación entre ellos:
- Empresas pagantes
- Inversionistas
- Transacciones entre empresas e inversionistas

¿Por qué descartar inicialmente a los proveedores? Porque cuando un inversor realiza una subasta está interesado en el pago que le hará la empresa pagante por la factura comprada, no está necesariamente interesado en el detalle de la factura o la naturaleza del proveedor.

Una vez identificadas las relaciones y actores principales la siguiente pregunta que tenemos que hacernos es si estos actores se encuentran en una misma fuente de datos o no, lo cual averiguaremos en la siguiente fase.


## Adquisición de datos ##
>IMPORTANTE: Dada la sensibilidad de la información del esquema de la base de datos de Mesfix. Se hará uso de nombres y valores ficticios de tablas y consultas para los ejercicios de todo el proyecto.

Efectivamente, nos encontramos con que Mesfix tiene multiples fuentes de datos siento MongoDB y MySQL las bases de datos principales en las que almacenan documentos referentes al negocio e información referente a las transacciones respectivamente. 

Descartaremos la base documental porque contiene información sensible de empresas e inversionistas que no podemos usar por la Ley 1581 de 2012 de Habeas Data de protección de datos personales en Colombia [[3]](#referencias). Es más, luego veremos por qué no nos serviría dicha información incluso si pudieramos usarla.

Hagamos una consulta a la base de datos MySQL para ver qué tablas tenemos y cuales son las que nos darán mayor provecho:
```sql
SHOW TABLES from 'Mesfix DB'
```

Perfecto, nos encontramos con mas de 10 tablas de entre las cuales podemos destacar a las tablas de inversionistas, empresas, transacciones, entre otras. Ahora, analizando ambos objetivos del proyecto, queda claro que de todas las tablas de la base de datos de Mesfix existen tres que son fundamentales:
- Tabla de transacciones
- Tabla de empresas
- Tabla de inversionistas

Hagamos una consulta rápida:
```sql
select * from empresas
select * from transacciones
select * from inversionistas
```

¿Por qué estas tres tablas?
Básicamente porque en cruzándolas deberíamos poder encontrar información relevante como:
- Nombre de empresa e inversores
- Número de identificación tributaria (NIT)
- Número de empleados de la empresa
- Ciudad de la empresa
- Ciudad del inversor
- Año de fundación de la empresa
- Dirección de la empresa
- Año y mes de la transacción
- Número de inversores participantes
- Monto de la factura
- Tiempo de pago de la factura
- Etc.

Sin embargo dijimos que no podemos incluir información sensible y que incluso si pudieramos hacerlo, no nos serviria de nada. Toquemos este detalle con algo de profundidad.

Para entender por qué no hace sentido el usar información sensible de una empresa o de los inversionistas hagamos las siguientes preguntas:
- ¿Hay una relación entre el nombre o razon social de una empresa y su riesgo de crédito?
- ¿Afecta el número de teléfono o el NIT de una empresa al análisis de riesgo de crédito?
- ¿Mejoraría nuestro algoritmo al tener como valor la dirección exacta de las empresas registradas?
- ¿Mejorará el rendimiento de nuestro algoritmo al tener el número de cédula de identidad de nuestros inversores?
- ¿Los apellidos de un inversor son datos relevantes para determinar si una empresa le pagará a tiempo a dicho inversor?

La respuesta a todas las preguntas anteriores es NO y un QUIZAS en algunas ocasiones. Por tanto, nuestro siguiente paso en la adquisición de datos sera crear una tabla única que cubra la relación empresa - inversion - inversionista y que contenga información sensible.

[continuara... ]

## Modelado ##

## Despliegue ##

## Aceptación del cliente ##

### Conclusiones ###

### Recomendaciones ###

## Agradecimientos ##

## Recursos adicionales ##

## Referencias ##
[1] Springer. 2014. Bank Management and Control. http://www.springer.com/la/book/9783642403736

[2] Microsoft. 2017 Team Data Science Process overview https://docs.microsoft.com/en-us/azure/machine-learning/data-science-process-overview 

[3] Ministerio de Tecnologías de la Información y las Comunicaciones. 2013. https://www.mintic.gov.co/portal/604/articles-4274_documento.pdf