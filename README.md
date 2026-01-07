 [Ver version en español](README.es.md)

# 💻 Tricentis LiveCompare: Explanation Smart Start App


## 🔄 ERP Application Lifecycle
Below is a representation of how the SAP application lifecycle is managed. There is a close relationship between DEV (Development) and QAS (Quality Assurance); feedback generates Changes that DEV implements and sends back to QAS. Completed phases move to PRD (Production), where user feedback is sent to QAS and then back to DEV, forming a continuous improvement circuit aimed at meeting requirements and ensuring customer satisfaction.


![Capture1](https://github.com/user-attachments/assets/111caf96-36f2-4982-870a-15231d5c7387)


In each iteration of changes made by DEV, QAS constantly seeks to reduce testing scope by focusing only on what truly matters. At this point, Tricentis developed LiveCompare to determine which elements or objects to test, reducing testing time, cost, and increasing the effectiveness of test execution and change implementation in ERP environments like SAP or Oracle.

## 💼 LiveCompare Methodology Description
Tricentis LiveCompare uses a methodology based on AI and dependency analysis to filter the SAP object universe. This aims to limit the testing range by focusing on those objects that are essential for validation. Categories are defined by their level of importance, following a logical flow from "actual usage" to "critical risk":


![Capture2](https://github.com/user-attachments/assets/5a90de82-46bb-4a31-9dd2-21f2ef104a99)


•	Used: All executable objects (transactions, reports, web services) that SAP records as being used in a specific period (usually the last 3 to 12 months). LiveCompare retrieves this from the Performance History Data (PHD) of the production system.

•	Impacted: Objects in the "Used" category that have a direct or indirect technical relationship with the changes made (e.g., in a Transport Request or an SAP Note). If a table is modified and a used report queries it, that report becomes "impacted."

•	Most at Risk: A subset of the impacted objects. LiveCompare applies an "expert system" to select the minimum necessary objects to be tested to cover 100% of the risk. If ten impacted reports call the same modified function, LiveCompare will choose the most relevant one ("Most at Risk") to optimize the effort.

Two divisions are also distinguished within each bar:

1- Standard (SAP Standard Objects): Refers to all objects owned by SAP.

Identification: Objects whose names DO NOT start with Z or Y (e.g., transaction VA01, program SAPMV45A, or table MARA).

Graph Significance: A high number of standard objects in the "Most-at-risk" bar indicates that changes (like a Support Pack) are affecting the system's core processes. The risk here is that native SAP functionality might fail.

2- Custom (Custom Objects / Z-Code): Refers to all developments tailored by your company.

Identification: Objects starting with Z or Y, found in the "Customer Name Range." LiveCompare uses Custom Object Naming Patterns to identify them automatically.

Graph Significance: If this section is large in the "Impacted" bar, it means SAP technical changes are "breaking" or affecting your own developments. This is common when SAP updates a standard function used by your Z-code.


### 📏 Relationship and Importance Levels

Object importance is not random; LiveCompare uses several technical criteria to prioritize them:

- Business Criticality: Users can flag objects as "Business Critical." If one is impacted, it automatically jumps to the highest risk level.

- Search Depth: Refers to how "close" the change is to the executable. A change in a screen (Level 1) is more direct than a change in a deep-nested function (Level 5).

- Usage (Frequency): Objects used thousands of times a day in production carry more weight than those used once a month.

- Functional Cost: Analyzes if the change occurs within the same application area (e.g., Sales) or if it affects other modules.


## 📏 The Ranking Process
Each Most at Risk object receives a rank (High, Medium, Low) based on its code depth and usage frequency:
- An impacted object with High Usage and Low Depth (direct change) will always be High Risk.
- An object with Low Usage and High Depth (many levels of separation) will be Low Risk.


### 📏 "Top-Down" vs. "Bottom-Up"
LiveCompare adapts its analysis based on the volume of changes:

- Bottom-Up: For fewer changes (<1000 objects), it starts from used objects and traces down to see if they touch the change. Highly accurate for custom developments.

- Top-Down: For massive changes (e.g., Support Packs), it starts from the change and traces up to see which executables are impacted.


## 📊 Example: Tricentis LiveCompare Smart Impact Analysis
Below is a simple process for requesting and invoking a report within the Learning module of Tricentis LiveCompare.

1- The first step is to create a "New Variant":


![Capture3](https://github.com/user-attachments/assets/4caab8e0-ba5f-4ddb-9e40-c79cf3402189)


2- After running the analysis, we get the first part of the Dashboard:

![Capture4](https://github.com/user-attachments/assets/dcb08531-338b-4ac2-a661-685e7ba47788)


### 📎 Used, Impacted & Most-at-risk

- This graph represents the three main analysis elements.
- Note the reduction in testing scope. The reduction for Standard objects to be tested is nearly 99%, with a similar 99% reduction for Custom elements.

### 📎 Most-at-risk & Test Coverage

- Once risk is identified, LiveCompare crosses it with your test repository (Tosca, Azure DevOps, or ALM).
- Hits (Blue): Most-at-risk objects that already have an associated test case.
- Gaps (Gray): Critical objects without a test. This is your "to-do" list: where new test cases must be created to prevent production issues.

3- Dashboard Second Part details:


![Capture5](https://github.com/user-attachments/assets/b96c7b17-e51b-48da-b94c-5f567a68d526)


### 📎 Changing Object Summary

- Este gráfico se enfoca en el origen del cambio (lo que viene en el transporte o la Nota SAP). No te dice qué se rompió, sino qué se modificó.

- ¿Qué muestra?: Un desglose por tipo de objeto técnico de todo lo que se está moviendo hacia tu sistema (Tablas, Programas ABAP, Clases, Vistas, Elementos de Datos, etc.).

- Divisiones Custom vs. Standard: Aquí verás si el cambio es mayormente código propio (Z/Y) o si es un parche de SAP que está modificando objetos estándar.

- Utilidad: Sirve para que el equipo de desarrollo identifique el volumen del cambio. Por ejemplo, si ves un volumen alto de "Table Definitions", sabes que habrá cambios en la estructura de datos que podrían afectar muchas consultas.

### 📎 Most-at-risk & Test Coverage by Type

Este gráfico es el más valioso para los líderes de QA y Testing, ya que cruza los objetos en riesgo con tus herramientas de pruebas (como Tosca, ALM o Azure DevOps).

Combina dos dimensiones:

1- Eje de Objetos (Most-at-risk): Agrupa los objetos que LiveCompare determinó como críticos (los que sí o sí debes probar). Los agrupa por tipo de ejecutable (ej. Transacciones, Reportes, RFCs).

2- Eje de Cobertura (Test Coverage): Aquí es de donde las barras de colores toman su escala:

- Hits (azul): Indica que para ese objeto en riesgo ya existe un caso de prueba en tu repositorio. Estás "protegido".

- Gaps (Azul marino): Indica que el objeto está en riesgo y no tienes ninguna prueba creada para él.

Generalmente los Hits se indican color verde y los Gaps en rojo.

¿Cómo leerlo?

- Si ves una barra larga de Transactions con mucho color Rojo (Gap), significa que tienes un agujero de seguridad funcional enorme en tus transacciones más usadas.

- Si la mayoría es Verde (Hit), puedes estar tranquilo porque tu regresión automática o manual ya cubre lo que se va a ver afectado.

¿Cómo se conectan estos dos gráficos?

- La relación es de Causa y Efecto:
- El Changing Object Summary te dice: "Se están cambiando 10 Clases y 5 Tablas" (La Causa).
- El motor de LiveCompare analiza qué transacciones usan esas clases y tablas.
- El Most-at-risk & Test Coverage by Type te dice: "Esos cambios en las clases afectan a estas 15 Transacciones. De esas 15, tienes pruebas para 10 (Hits) y te faltan 5 (Gaps)" (El Efecto y la Solución).

Ejemplo práctico:

Si en el primer gráfico ves muchos cambios en Table Definitions (Standard), es muy probable que en el segundo gráfico veas un incremento en Transactions (Custom) en riesgo, porque tus desarrollos Z dependen de esas tablas estándar que SAP acaba de modificar.


## 🔩🔧 Testing Detail Report


![Capture7](https://github.com/user-attachments/assets/bd21812c-bc9e-40be-835b-ff10224d2031)


Este reporte tiene suma importancia para el testing. Su importancia estriba en los gráficos inferiores "Top 5 Application Areas" y "All, Covering and Optimal Tests".

### ✏ Top 5 Application Areas

Muestra las 5 areas principales donde nos debemos enfocar a probar. Ademas de todas las pruebas (Azul), las pruebas que cubren el cambio (Rojo) y el subgrupo de pruebas (Verde) que va a impactar inmediatamente en la Calidad de los elementos.

### 🖊 All, Covering and Optimal Tests

Esta grafica ya muestra el total de las pruebas que cubre los elementos SAP modificados (columna All), las pruebas a los impactados (columna Covering) y las pruebas optimas (columna Optimal). Mas que todo es un panorama general de la corrida del Smart Impact Analysis.

Ambas graficas son las importantes para los testers ya que están relacionadas con otras tablas donde se especifican:

1. las áreas donde se realizaron cambios,
2. las pruebas a los "Most-at-risk",
3. las pruebas óptimas a los "Most-at-risk" y
4. los "Gaps"

### 🖋 "Home" Tab


![Capture8](https://github.com/user-attachments/assets/14a398f1-a8a8-4802-aaaa-acb94dee1a45)


- La columna "ALL" nos indica todos los tests aplicados a las areas (APP_AREA)
- La columna "COVERING" son las pruebas aplicadas a los "Most-at-risk" 
- La columna "OPTIMAL" son las pruebas que daran el resultado optimo de covertura.
- La columna "TEST_GAPS" indica los elementos que todavia no tienen asignadas pruebas.


### 🖌 "App Area Details" Tab


![Capture9](https://github.com/user-attachments/assets/02cf2988-8064-4c28-bf62-05aec33f8e47)


Esta pestana despliega el area seleccionada. En este ejemplo tenemos a "Financial Accounting", Algunos elementos importantes en este reporte son:

- TEST_REPOSITORY_NAME: indica el tipo repositorio e indirectamente el tipo de prueba.
- STATUS: dice que tipo de prueba por importancia esta desplegada (COVERING o OPTIMAL)
- RISK: el riesgo implicado en el cambio al objeto
- TEST_PATH: la locacion de la prueba y el ID de la prueba


### 🖍 "Test Hits & Gaps" Tab


![Capture11](https://github.com/user-attachments/assets/3a8fc8c1-39bc-44dd-8a22-5143e4aacb6d)


Esta pantalla nos indica todos "Hits" y "Gaps" encontrados. Va a ser muy importante enfocarse en los "Gaps" para tener un panora mas completo del testeo aun si sabemos que hemos llevado a cabo "Optimals" y "Covering".


## 📝🗄 Conclusiones


- Enfocarse en el recurso de "Testing Detail Report" para tener un panorama acotado de testing.
  
- Revisar las 5 Areas de Aplicación, como prioridad. Verificar el área donde se hagan realizado más pruebas (All) y donde haya más considerados "Optimal". Entre más elementos óptimos haya por satisfacer se puede diferir que los cambios implementados en esa área tienen cierta independencia y la funcionalidad del área puede estar en riesgo.
  
- Tomar nota de los "Gaps" en la pestaña "Home". Hay que validar el hecho de que existe la posibilidad que hay elementos importantes sin pruebas y aunque haya pruebas optimas en buen número, verificar este detalle nos puede evitar detalles futuros en el funcionamiento del área. Auxiliarse de la pestaña "Test Hits & Gaps" donde se enumeran los elementos y su cobertura de pruebas.
  
- Verificar los requisitos, procedimientos y políticas de pruebas aplicados al ERP en cuestión de parte del cliente. Pregunta a tu supervisor si hay practicas históricas no documentadas de prioridad de pruebas, además de requerimientos de último momento que solo tus superiores conocen.






















