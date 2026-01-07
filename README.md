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

- Focuses on the source of the change (what's in the transport or SAP Note).

- What it shows: A breakdown by technical object type (Tables, ABAP Programs, Classes, etc.).

- Custom vs. Standard: Shows if the change is mostly your own code (Z/Y) or an SAP patch.

- Usefulness: It helps the development team identify the volume of change. For example, if you see a high volume of "Table Definitions," you know there will be changes to the data structure that could affect many queries.

### 📎 Most-at-risk & Test Coverage by Type

Valuable for QA leaders, crossing risk objects with testing tools (Tosca, ALM o Azure DevOps).

Combines two dimensions:

1- Object Axis (Most-at-risk): Groups critical objects by type (Transactions, Reports, RFCs).

2- Coverage Axis (Test Coverage):

- Hits (Light Blue): Protected. Test case exists.

- Gaps (Dark Blue): At risk. No test exists. (Note: Generally, Hits are green and Gaps are red in standard configurations).

Generally, Hits are indicated in red and Gaps in blue.

How to read it?

- If you see a long bar of Transactions with a lot of red (Gap), it means you have a huge functional security vulnerability in your most frequently used transactions.

- If most of it is green (Hit), you can rest assured that your automatic or manual regression already covers what will be affected.

How are these two charts connected?

- The relationship is one of Cause and Effect:
 
- The Changing Object Summary tells you: "10 Classes and 5 Tables are being changed" (The Cause).

- The LiveCompare engine analyzes which transactions use those classes and tables.

- The Most-at-risk & Test Coverage by Type tells you: "These class changes affect these 15 Transactions. Of those 15, you have tests for 10 (Hits) and you're missing 5 (Gaps)" (The Effect and the Solution).

Practical example:

If in the first graph you see many changes in Table Definitions (Standard), it's very likely that in the second graph you'll see an increase in Transactions (Custom) at risk, because your custom developments depend on those standard tables that SAP has just modified.


## 🔩🔧 Testing Detail Report


![Capture7](https://github.com/user-attachments/assets/bd21812c-bc9e-40be-835b-ff10224d2031)


This report is extremely important for testing. Its importance lies in the graphs below: "Top 5 Application Areas" and "All, Covering and Optimal Tests".

### ✏ Top 5 Application Areas

Shows the top 5 areas to focus testing on, including All tests (Blue), Covering tests (Red), and Optimal tests (Green).

### 🖊 All, Covering and Optimal Tests

A general overview of the Smart Impact Analysis run:

- All: Total tests covering modified SAP elements.

- Covering: Tests for impacted objects.

- Optimal: The most efficient test set.

Both graphs are important for testers because they are related to other tables that specify:

1. the areas where changes were made,
2. the most-at-risk tests,
3. the optimal most-at-risk tests, and
4. the gaps.

### 🖋 "Home" Tab


![Capture8](https://github.com/user-attachments/assets/14a398f1-a8a8-4802-aaaa-acb94dee1a45)


- ALL: All tests applied to areas (APP_AREA).
- COVERING: Tests applied to "Most-at-risk".
- OPTIMAL: Tests providing the most efficient coverage.
- TEST_GAPS: Elements without assigned tests.


### 🖌 "App Area Details" Tab


![Capture9](https://github.com/user-attachments/assets/02cf2988-8064-4c28-bf62-05aec33f8e47)


Displays the selected area (e.g., Financial Accounting). Key fields:

- TEST_REPOSITORY_NAME: Type of repository/test.
- STATUS: Importance (COVERING or OPTIMAL).
- RISK: Risk level of the change.
- TEST_PATH: Location and ID of the test.


### 🖍 "Test Hits & Gaps" Tab


![Capture11](https://github.com/user-attachments/assets/3a8fc8c1-39bc-44dd-8a22-5143e4aacb6d)


Lists all found Hits and Gaps. It is vital to focus on Gaps to ensure full coverage even if "Optimal" tests are executed.


## 📝🗄 Conclusions

- Focus on the Testing Detail Report for a narrow testing scope.

- Prioritize the Top 5 Application Areas. If an area has many "Optimal" tests, its changes might be independent, risking the area's overall functionality.

- Monitor Gaps in the "Home" tab to ensure no critical elements are left untested.

- Verify client-specific ERP testing procedures and ask supervisors for undocumented historical practices or last-minute requirements.





















