## Lab 4: Broadcom Exit & Migration Wave Planning

> **Prerequisites:** Complete Lab 1 (Getting Started) before starting this lab
>
> **Code Set:** Use Case 1 & 4 — full program set including `KM*`, `MM*`, `MI*`, `MC*`, `EB*`, `MB*` programs and associated copybooks
>
> **Duration:** 45 minutes
>
> **Difficulty:** Intermediate

---

### Overview

UNFI is transitioning away from Broadcom mainframe tooling. This lab uses IBM Bob to identify obsolete modules and dependencies across the COBOL application, group programs and ADSO screens by business function, and organize them into logical migration waves — producing a structured, low-risk transition plan.

---

### Learning Objectives

By the end of this lab you will be able to:

- Use Bob to produce a full program inventory with dependency mapping
- Identify programs and modules that depend on Broadcom-specific tooling or libraries
- Group programs by business function using Bob's analysis capabilities
- Organize program groups into sequenced migration waves based on dependency order
- Generate a migration wave plan document ready for review and execution

---

### Exercise 1: Build the Program Inventory & Dependency Map

Start with a full picture of what exists and how programs relate to each other.

#### Actions

> Ensure you are in **Z Architect** mode

1. In the chat, enter the following prompt:

```
Analyze the entire workspace and produce a program inventory. For each program, show:
- Program name
- Business area or function (infer from program naming conventions and logic)
- Programs it calls (direct callees)
- Programs that call it (callers)
- Copybooks it depends on
Present the results as a table sorted by business area.
```

2. Approve tool requests. Bob will query the metadata database built during Lab 1 setup.

3. Review the inventory table. This is the foundation for the grouping and wave planning exercises that follow.

#### Expected Results

- ✅ Full program inventory produced
- ✅ Each program's callers, callees, and copybook dependencies listed
- ✅ Business area inferred and assigned for each program
- ✅ Table sortable by business area for grouping review

---

### Exercise 2: Identify Obsolete Modules & Broadcom Dependencies

Surface programs and modules that have Broadcom-specific dependencies or that are candidates for retirement.

#### Actions

> Remain in **Z Architect** mode

1. In the chat, enter the following prompt:

```
Review the program inventory and identify:
1. Programs that appear to be obsolete — never called by any other program and with no recent modification indicators
2. Any programs or copybooks that reference Broadcom-specific tooling, libraries, or naming conventions (e.g. CA-, IDMS, Endeavor references in comments or data names)
3. Programs that duplicate functionality — groups of programs that perform the same business operation
Present findings as three separate lists with a brief rationale for each entry.
```

2. Review the three lists. Flag any programs Bob has incorrectly identified as obsolete and correct it in the chat.

#### Expected Results

- ✅ Obsolete program candidates identified with rationale
- ✅ Broadcom-specific dependencies called out
- ✅ Duplicate functionality groups identified
- ✅ Each finding includes enough context to make a keep/retire decision

---

### Exercise 3: Group Programs by Business Function

Organize the active programs into logical business function groups that will form the basis of migration waves.

#### Actions

> Remain in **Z Architect** mode

1. In the chat, enter the following prompt:

```
Using the program inventory, group all active (non-obsolete) programs by business function. Identify 4–8 functional groups based on what the programs do — for example: Promotions, Item Management, Order Processing, Vendor Management, Reporting.
For each group show:
- Group name
- Programs in the group
- Shared copybooks used across the group
- Entry-point programs (those not called by anything else in the group)
Present as a structured list with one section per group.
```

2. Review the groupings. If a program is in the wrong group, tell Bob in the chat and it will adjust.

#### Expected Results

- ✅ 4–8 business function groups defined
- ✅ Every active program assigned to exactly one group
- ✅ Shared copybooks identified per group
- ✅ Entry-point programs identified for each group

---

### Exercise 4: Sequence Groups into Migration Waves

Order the business function groups into migration waves based on dependency — groups with no dependencies on other groups go first.

#### Actions

> Remain in **Z Architect** mode

1. In the chat, enter the following prompt:

```
Using the business function groups and their dependency relationships, sequence the groups into migration waves. Apply these rules:
- A group can only be in a wave if all groups it depends on are in an earlier wave
- Prefer smaller, self-contained groups in early waves to reduce risk
- Identify any circular dependencies and flag them for manual resolution
Present the result as a wave plan table:
Wave | Group Name | Programs | Dependencies on Prior Waves | Risk Level (Low / Medium / High)
Add a brief narrative after the table explaining the sequencing rationale.
```

2. Review the wave plan. Ask Bob to adjust wave sizing or risk assessments as needed.

#### Expected Results

- ✅ Migration waves sequenced with dependency order respected
- ✅ Each wave lists the programs it contains
- ✅ Circular dependencies flagged if any exist
- ✅ Risk level assigned per wave
- ✅ Sequencing rationale narrative included

---

### Exercise 5: Generate the Migration Wave Plan Document

Produce a formal migration wave plan document that can be shared with stakeholders.

#### Actions

> Remain in **Z Architect** mode

1. In the chat, enter the following prompt:

```
Generate a formal Migration Wave Plan document for the Broadcom exit. Include:
1. Executive Summary — scope, objective, and total program count
2. Obsolete Module List — programs recommended for retirement
3. Business Function Groups — one section per group with program list and shared copybooks
4. Migration Wave Schedule — the wave plan table with dependency rationale
5. Risk & Dependency Notes — circular dependencies, high-risk programs, and recommended mitigations
Save the document as MIGRATION-WAVE-PLAN.md.
```

2. Approve tool requests and wait for the document to be generated.

3. Open `MIGRATION-WAVE-PLAN.md` and use the **Preview** option to review the full document.

#### Expected Results

- ✅ `MIGRATION-WAVE-PLAN.md` created in the workspace
- ✅ All five sections present and populated
- ✅ Document suitable for review by technical and business stakeholders
- ✅ Wave schedule and risk notes are actionable

---

### Key Takeaways

- Bob's metadata database (built during Lab 1) is what makes program-level dependency analysis possible — the scan is the foundation of everything in this lab
- Grouping by business function before sequencing into waves prevents the most common migration mistake: moving programs before their dependencies are ready
- The migration wave plan is a living document — ask Bob to update it as programs are retired or dependencies change
- Identifying obsolete programs early reduces the total migration scope and risk before the first wave begins

::: {.callout-tip}
## Start a New Chat
Please select the **+** sign at the top of the chat window to start a new session before moving to the next lab.
:::
