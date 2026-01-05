# LiveCompare

## 📋 Ciclo de vida de una Aplicacion ERP
A continuacion una representacion de como se maneja el ciclo
de vida de la aplicacion SAP. Vemos que hay una relacion de estrecha entra DEV (Development) y QAS (Quality Assurance), la retroalimentacion genera cambios (Changes)
que DEV implementa y pasa de nuevo a QAS. Las fases terminadas pasan a PRD (Produccion) donde ellos retroalimentan a QAS y ellos a DEV formandose un circuito de mejora buscando cumplir con los requerimientos y la satisfaccion del cliente. 

![Capture1](https://github.com/user-attachments/assets/111caf96-36f2-4982-870a-15231d5c7387)

En cada iteracion de cambios hechos por DEV, QAS busca en todo momento reducir el alcance de las pruebas (testing) enfocándose solo en lo que realmente importa.
En este punto Tricentis desarrolla LiveCompare para determinar que elementos u objetos probar para reducir el tiempo de pruebas, costo, efectividad del testeo e 
implementacion de cambios en ambientes ERP como SAP u Oracle.

## 📋 Descripcion de metodologia LiveCompare
LiveCompare de Tricentis utiliza una metodología basada en inteligencia artificial y análisis de dependencias para filtrar el universo de objetos en SAP.
Con esto busca limitar el rango de pruebas, avocandose en aquellos objetos que son promordiales para las pruebas.
Dado esto, se definen categorias por nivel dado de importancia. El análisis sigue un flujo lógico que va desde el "uso real" hasta el "riesgo crítico":

•	Used (Usados): Son todos los objetos ejecutables (transacciones, reportes, servicios web) que SAP registra como utilizados en un periodo determinado (generalmente los últimos 3 a 12 meses). LiveCompare obtiene esta información del historial de rendimiento (Performance History Data - PHD) del sistema de producción.

•	Impacted (Impactados): Son aquellos objetos de la categoría "Used" que tienen una relación técnica directa o indirecta con los cambios realizados (por ejemplo, en un transporte o una nota SAP). Si cambias una tabla y un reporte usado la consulta, ese reporte queda "impactado".

•	Most at Risk (Más en riesgo): Es un subconjunto de los impactados.6 LiveCompare aplica un "sistema experto" para seleccionar los objetos mínimos necesarios que deben probarse para cubrir el 100% del riesgo. Si diez reportes impactados llaman a la misma función modificada, LiveCompare elegirá el más relevante (el "Most at Risk") para optimizar el esfuerzo.

Tambien se distinguen que en cada barra dos divisiones:
1. Standard (Objetos Estándar): Se refiere a todos los objetos que son propiedad de SAP.

Identificación: Son objetos cuyos nombres NO comienzan con las letras Z o Y (por ejemplo: la transacción VA01, el programa SAPMV45A o tablas como MARA).

Significado en la gráfica: Si tienes muchos objetos estándar en la barra de "Most-at-risk", significa que los cambios (como un Support Pack o una Nota SAP) están afectando los procesos base del sistema. Aquí el riesgo es que una funcionalidad nativa de SAP deje de funcionar.

2. Custom (Objetos Personalizados / Código Z): Se refiere a todos los desarrollos hechos a medida por tu empresa.

Identificación: Son objetos que comienzan con Z o Y, y que se encuentran en el "Customer Name Range". LiveCompare usa un patrón de nombres (Custom Object Naming Patterns) para identificarlos automáticamente.

Significado en la gráfica: Si esta sección es grande en la barra de "Impacted", significa que los cambios técnicos de SAP están "rompiendo" o afectando tus desarrollos propios. Esto es muy común cuando SAP actualiza una función estándar que tu código Z utilizaba.

## Relación y Niveles de Importancia
La importancia de un objeto no es aleatoria; LiveCompare utiliza varios criterios técnicos para priorizarlos:

- Criticalidad de Negocio

  El usuario puede marcar objetos como "Business Critical". Si uno de estos es impactado, automáticamente sube al nivel más alto de riesgo.
  
- Profundidad de Búsqueda

  Se refiere a qué tan "cerca" está el cambio del ejecutable. Un cambio en una pantalla (nivel 1) es más directo que un cambio en una función profunda (nivel 5).

- Uso (Frecuencia)
  
  Los objetos que se usan miles de veces al día en producción tienen mayor peso que aquellos usados una sola vez al mes.

- Costo Funcional
  
  Analiza si el cambio ocurre dentro de la misma área de aplicación (ej. Ventas) o si afecta a otros módulos.

## El Proceso de "Ranking"
Cada objeto Most at Risk recibe un rango (High, Medium, Low) basado en la combinación de su profundidad en el código y su frecuencia de uso. Por ejemplo:
•	Un objeto impactado con Uso Alto y Profundidad Baja (cambio directo) será siempre High Risk.
•	Un objeto con Uso Bajo y mucha Profundidad (muchos niveles de separación del cambio) será Low Risk.

## Diferencia entre "Top-Down" y "Bottom-Up"
LiveCompare adapta su análisis según el volumen de cambios:

•	Bottom-Up: Si hay pocos cambios (<1000 objetos), empieza desde los objetos usados y rastrea hacia abajo para ver si tocan el cambio. Es muy preciso para desarrollos a medida.

•	Top-Down: Si hay muchísimos cambios (como un Support Pack), empieza desde el cambio y rastrea hacia arriba para ver qué ejecutables impacta.

![Capture2](https://github.com/user-attachments/assets/5a90de82-46bb-4a31-9dd2-21f2ef104a99)

## Explicacion de un ejemplo de uso de Tricentis LiveCompare

El primer paso es crear un nuevo proyecto o "New Variant":

![Capture3](https://github.com/user-attachments/assets/4caab8e0-ba5f-4ddb-9e40-c79cf3402189)


Despues de invocar el resultado tenemos una primera parte del panel de indicadores (Dashboard):

![Capture4](https://github.com/user-attachments/assets/dcb08531-338b-4ac2-a661-685e7ba47788)

A la izquierda se presenta la primera grafica de "Used, Impacted & Most-at-risk". En esta grafica se representan los 3 elementos de analisis principal.
Donde se nota la diferencia de muestreo de pruebas de elementos usados a elementos en riesgo. La reduccion de objetos Standard a probar es casi 99% y un valor casi igual de elementos Custom (99%) a testear tambien.

En referencia a la grafica "Most-at-risk & Test Coverage" (Hits & Gaps):

Una vez identificado el riesgo, LiveCompare lo cruza con tu repositorio de pruebas (como Tosca, Azure DevOps o ALM):

Hits (Azul): Son los objetos "Most-at-risk" que ya tienen un caso de prueba asociado. Estás solo hay 30 casos de prueba asociados.

Gaps (Gris): Son los objetos críticos que no tienen prueba (89). Este es tu plan de trabajo: aquí es donde debes crear nuevos casos de prueba para evitar que algo se rompa en producción.















