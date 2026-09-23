## Lab 6: COBOL Code & Data Analysis

> **Prerequisites:** Complete Lab 1 (Getting Started) before starting this lab
>
> **Code Set:** Use Case 1 & 4 — full program set including `KM*`, `MM*`, `MI*`, `MC*`, `EB*`, `MB*` programs and associated copybooks
>
> **Duration:** 60 minutes
>
> **Difficulty:** Intermediate

---

### Overview

In this lab you will perform in-depth analysis of COBOL code and associated data structures to understand impact, dependencies, data flows, and business logic across the UNFI application. Rather than following a fixed sequence, you will work through a set of guided prompts organized by topic — each introducing a different analytical lens. Use these prompts as a starting point and write your own variations to dig deeper into areas that interest you.

---

### Learning Objectives

By the end of this lab you will be able to:

- Generate call graphs and understand program-to-program relationships
- Trace how data fields move across programs and paragraphs
- Perform impact analysis before making a field or copybook change
- Identify complexity hotspots and technical debt across the codebase
- Use Bob to explain business logic embedded in COBOL programs in plain English
- Generate structured program documentation using the Program Documentation workflow
- Extract and document business rules using the Extract Business Rules workflow

---

### Actions (General)

For all exercises in this lab:

1. Ensure you have completed the workspace scan and Agent.md initialization from Lab 1
2. Use **Z Architect** mode for all analysis and documentation exercises
3. Copy the prompt from the code block and paste it directly into the Bob chat
4. Approve any tool requests — or enable **Auto Approve** in the **Permissions** dropdown for longer-running prompts
5. Review the output, then continue to the next exercise or try a variation of your own

> **Tip:** Start a fresh chat between unrelated exercises to avoid context from one analysis carrying into the next. Click the **+** button at the top of the chat panel to open a new chat.

---

### Exercise 1: Call Graphs & Program Relationships

Call graphs show which programs invoke which other programs. They reveal the structure of the application, identify entry points, and let you trace execution paths from start to finish.

> Ensure you are in **Z Architect** mode

**1. Full application call graph**

```
Generate a call graph for the entire workspace showing which programs call which other programs. Identify the top-level entry points and the most-called utility programs.
```

**2. Call chain from a specific program**

```
Show me the full call chain starting from MMP193.cbl — every program it directly or transitively calls, down to the deepest dependency.
```

**3. Reverse call graph — who calls this program?**

```
Which programs in the workspace call KMS150.cbl? Show me the reverse call graph with a summary table.
```

**4. Entry point discovery**

```
Which programs are never called by any other program in the workspace? Show them as entry points with their inferred business function.
```

---

### Exercise 2: Data Flow Analysis

Data flow analysis traces how a specific field moves through the application — where it is assigned, where it is read, and which paragraphs or programs it passes through.

> Ensure you are in **Z Architect** mode

**1. Cross-program field trace**

```
Trace the data flow of a key promotion field across all programs — where is it written and where is it read? Pick the most widely used promotion-related field from the copybooks and trace it.
```

**2. Variable flow within a program**

```
Show me a variable data flow graph for the main working-storage fields inside MMP193.cbl — which paragraphs write them and which paragraphs read them?
```

**3. Inter-paragraph data flow**

```
Which variables in MMP269.cbl are written in one paragraph and read in another? Show the data flow between paragraphs as a diagram.
```

**4. Return code trace**

```
Identify all return code fields used across the workspace. For each one, trace which programs set it and which programs check it.
```

---

### Exercise 3: Impact Analysis

Impact analysis answers "if I change this, what else breaks?" before you touch a single line of code.

> Ensure you are in **Z Architect** mode

**1. Copybook change impact**

```
Show me all programs that would be affected if I change MMLW17.cpy. Include direct users and any programs that use those programs transitively.
```

**2. Most-included copybook**

```
Which copybook in the workspace is included by the most programs? Draw a dependency graph ranked by inclusion count.
```

**3. Field size change impact**

```
What would be the impact of increasing the size of a key item identifier field (e.g. an item number or vendor number) by 2 characters? Which programs and copybooks would need to change?
```

**4. Full include tree for a program**

```
Draw the full include tree for MMP193.cbl showing every copybook it depends on, directly or transitively.
```

---

### Exercise 4: Business Logic Explanation

Use Bob to explain what programs actually do in plain English — translating decades of COBOL into readable business logic.

> Ensure you are in **Z Architect** mode

**1. Explain a program end-to-end**

```
Explain MMP193.cbl in plain English. What does it do, what business process does it support, what data does it read and write, and what are the key decision points in the logic?
```

**2. Summarize a program family**

```
The KMS* programs appear to be related. Analyze all KMS programs in the workspace and explain what business function this program family supports, how they relate to each other, and what data they share.
```

---

### Exercise 5: Generate Program Documentation (Workflow)

The **Generate Program Documentation** workflow produces structured, comprehensive documentation for a COBOL program — covering program logic, business rules, data usage, and call relationships. This is more thorough than a conversational explanation because the workflow guides Bob through a consistent documentation structure every time.

> Ensure you are in **Z Architect** mode

#### Actions

1. Select the **Workflows** icon.

2. Select the workflow titled **Generate program documentation**. You can use the dropdown to review what the workflow is used for.

3. Bob will ask you to select a program. Click **Browse Files** to select a COBOL source file, then click **Continue with selection**.

   **ACTION:** Select **Browse Files → Use Case 1 & 4 → MMP193.cbl → select file → Continue with selection**.

4. Approve any tool requests that appear as Bob reads the source file, queries the metadata database, and assembles the documentation.

5. When complete, a documentation file will be written to your workspace. Open it and review the generated content.

> **Tip:** Try running the workflow again on a different program — for example `KMS150.cbl` — to compare the depth of documentation produced for programs of different complexity.

#### Expected Results

- ✅ Structured program documentation generated covering logic, business rules, and data flow
- ✅ Call relationships and copybook dependencies included
- ✅ Documentation file written to the workspace and visible in the Explorer panel

---

### Exercise 6: Extract Business Rules (Workflow)

The **Extract Business Rules** workflow goes beyond a conversational summary. It systematically scans the program, identifies distinct business areas, and produces structured documentation for each business rule — including the conditions, validations, calculations, and decision logic it enforces.

> Ensure you are in **Z Architect** mode

#### Actions

1. Select the **Workflows** icon.

2. Select the workflow titled **Extract business rules**. You can use the dropdown to review what the workflow is used for.

3. Bob will ask you to select a program. Click **Browse Files** to select a COBOL source file, then click **Continue with selection**.

   **ACTION:** Select **Browse Files → Use Case 1 & 4 → KMS150.cbl → select file → Continue with selection**.

4. Approve any tool requests that appear as Bob analyzes the program logic.

5. When complete, review the extracted business rules. Each rule will include the business area it belongs to, a plain-English description, and a reference to the relevant paragraph or section in the source code.

> **Tip:** After reviewing the output, try a follow-up prompt in the same chat:
> ```
> Are there any business rules in KMS150.cbl that appear to be duplicated or overlapping with rules in other KMS programs?
> ```

#### Expected Results

- ✅ Business areas identified and grouped
- ✅ Each business rule documented with plain-English description and source location
- ✅ Conditions, validations, and decision logic captured in structured format

---

### Exercise 7: Complexity & Technical Debt

Cyclomatic complexity measures the number of independent paths through a program — the higher the score, the harder it is to test, maintain, and modernize.

> Ensure you are in **Z Architect** mode

**1. Application-wide complexity ranking**

```
Rank all programs in the workspace by cyclomatic complexity. Show the top 10 most complex programs and for each one, name the paragraph that contributes most to the complexity score.
```

**2. Technical debt assessment**

```
For the top 5 most complex programs, produce a technical debt assessment. For each program, identify: dead paragraphs (never PERFORMed), unused Working-Storage variables, and any copybooks included but not referenced in the Procedure Division. Summarize the debt findings per program.
```

**3. Modernization priority ranking**

```
Based on complexity, dead code, and DB2 access patterns, which 3 programs in the workspace should be prioritized for modernization first? For each one, explain why and suggest the most impactful first step.
```

---

### Key Takeaways

- **Z Architect mode + the metadata database** is what makes application-wide analysis possible — every prompt here relies on the scan completed in Lab 1
- **Call graphs** and **data flow** analysis are the two most powerful tools for understanding an unfamiliar codebase quickly
- **Impact analysis before any change** is a best practice — it takes seconds with Bob and prevents hours of debugging downstream
- **Business logic explanation** prompts are valuable not just for developers but for business analysts and architects who need to understand what the code does without reading COBOL
- The **Generate Program Documentation** workflow produces consistent, structured documentation every time — more reliable than ad-hoc conversational prompts
- The **Extract Business Rules** workflow surfaces the decision logic embedded in COBOL in a format that business analysts and architects can act on directly
- Every prompt in this lab is a starting point — replace program names, field names, and copybook names with the ones relevant to your investigation

::: {.callout-tip}
## Start a New Chat
Please select the **+** sign at the top of the chat window to start a new session before moving to the next lab.
:::
