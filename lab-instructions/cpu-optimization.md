## Lab 5: CPU Optimization

> **Prerequisites:** Complete Lab 1 (Getting Started) before starting this lab
>
> **Code Set:** Use Case 1 & 4 — `MMP193.cbl`, `MMP269.cbl`, `KMS150.cbl`, `KMS160.cbl`, and associated copybooks
>
> **Duration:** 60 minutes
>
> **Difficulty:** Intermediate

---

### Overview

CPU consumption is a direct cost driver on z/OS. In this lab you will use IBM Bob to analyze COBOL programs, SQL access patterns, and DB2 usage across the UNFI codebase to identify bottlenecks and receive concrete, prioritized optimization recommendations — reducing CPU consumption and improving overall system performance.

---

### Learning Objectives

By the end of this lab you will be able to:

- Use Bob to identify high-complexity programs that are CPU optimization candidates
- Analyze SQL statements for inefficient access patterns (table scans, missing indexes, repeated fetches)
- Identify repeated DB2 calls that could be consolidated or cached
- Understand loop structures and processing patterns that inflate CPU usage
- Receive and apply Bob's optimization recommendations

---

### Exercise 1: Identify CPU Optimization Candidates

Find the programs most likely to be contributing to high CPU usage based on complexity and DB2 access frequency.

#### Actions

> Ensure you are in **Z Architect** mode

1. In the chat, enter the following prompt:

```
Analyze the workspace and identify the top CPU optimization candidates. For each program, show:
- Cyclomatic complexity score
- Number of DB2 operations (SELECT, INSERT, UPDATE, DELETE)
- Number of file I/O operations
- Whether it contains loops (PERFORM UNTIL, PERFORM VARYING)
- Whether any DB2 operations occur inside loops
Rank the results by overall CPU risk (highest first) and explain the dominant cost driver for the top 5 programs.
```

2. Approve tool requests. Bob will query the metadata database and analyze program structure.

3. Review the ranked list — these are the targets for the optimization exercises that follow.

#### Expected Results

- ✅ Program list ranked by CPU risk
- ✅ Complexity, DB2 operation count, and file I/O shown per program
- ✅ Loop + DB2-inside-loop patterns flagged
- ✅ Top 5 programs have a named dominant cost driver

---

### Exercise 2: Analyze SQL Access Patterns

Drill into the SQL statements of a high-risk program to find inefficient patterns.

#### Actions

> Remain in **Z Architect** mode

1. Using one of the top programs identified in Exercise 1 (e.g. `MMP193.cbl`), enter the following prompt:

```
Analyze all EXEC SQL statements in MMP193.cbl. For each SQL statement:
- Show the full statement
- Identify the table(s) accessed
- Flag any patterns that are likely to cause performance issues: missing WHERE clause predicates, SELECT *, repeated fetches of the same row in a loop, cursors that fetch more rows than needed, or non-sargable predicates
Produce a SQL efficiency report with a severity rating (High / Medium / Low) for each issue found.
```

2. Review the SQL efficiency report. Note the High severity issues — these are the highest-value optimization targets.

#### Expected Results

- ✅ Every EXEC SQL statement in the program listed
- ✅ Inefficient patterns identified with severity ratings
- ✅ Specific table names and column access patterns documented
- ✅ High severity issues clearly flagged for immediate action

---

### Exercise 3: Identify Repeated DB2 Calls & Consolidation Opportunities

Find cases where the same DB2 query is executed multiple times and could be cached or consolidated.

#### Actions

> Remain in **Z Architect** mode

1. In the chat, enter the following prompt:

```
Analyze MMP193.cbl and MMP269.cbl together. Identify:
1. Any DB2 SELECT statements that query the same table with the same key multiple times across the program — these are candidates for result caching in Working Storage
2. Any DB2 cursors that are opened and fetched inside a PERFORM loop — these are candidates for bulk fetch or set-based SQL rewrite
3. Any identical or near-identical SQL statements that appear in multiple programs — these are candidates for consolidation into a shared DB2 stored procedure or service program
Present findings as a prioritized list with a recommended consolidation action for each.
```

2. Review the consolidation opportunities and discuss with your team which are feasible to implement.

#### Expected Results

- ✅ Repeated single-row SELECT candidates identified with caching recommendation
- ✅ Cursor-in-loop patterns flagged with bulk fetch / set-based SQL alternative suggested
- ✅ Cross-program duplicate SQL identified with consolidation approach
- ✅ Findings prioritized by estimated CPU savings

---

### Exercise 4: Get Optimization Recommendations & Apply One

Have Bob generate concrete, implementable optimization recommendations and apply the highest-priority one.

#### Actions

> Switch to **Z Code** mode

1. In the chat, enter the following prompt — referencing the SQL efficiency report from Exercise 2:

```
Based on the SQL efficiency analysis of MMP193.cbl, generate the top 3 optimization recommendations in order of estimated CPU impact. For each recommendation:
- Describe the current inefficient pattern
- Show the current code
- Show the optimized replacement code
- Estimate the CPU saving (e.g. eliminates N DB2 calls per transaction)
Then apply the highest-priority recommendation to MMP193.cbl.
```

2. Approve tool requests as Bob makes the code change.

3. Review the diff in the chat window — green lines are additions, red lines are removals.

#### Expected Results

- ✅ Top 3 recommendations produced with before/after code
- ✅ CPU saving estimate provided per recommendation
- ✅ Highest-priority optimization applied to the program
- ✅ Diff review confirms the change is correct and complete

---

### Exercise 5: Generate a CPU Optimization Report

Produce a formal optimization report documenting findings and recommendations across all analyzed programs.

#### Actions

> Switch back to **Z Architect** mode

1. In the chat, enter the following prompt:

```
Generate a CPU Optimization Report for the UNFI codebase covering the programs analyzed in this session. Include:
1. Executive Summary — total programs analyzed, key findings, and estimated impact
2. High-Priority Issues — SQL patterns, loop structures, and DB2 access inefficiencies rated High severity
3. Consolidation Opportunities — repeated DB2 calls and cross-program duplicate SQL
4. Recommendations Table — ranked by estimated CPU saving with implementation effort (Low / Medium / High)
5. Quick Wins — changes that can be made in under 1 hour with Low effort and measurable impact
Save the report as CPU-OPTIMIZATION-REPORT.md.
```

2. Approve tool requests and wait for the document to be generated.

3. Open `CPU-OPTIMIZATION-REPORT.md` and use the **Preview** option to review.

#### Expected Results

- ✅ `CPU-OPTIMIZATION-REPORT.md` created
- ✅ All five sections present and populated
- ✅ Recommendations ranked by CPU impact
- ✅ Quick wins section identifies immediate low-effort actions

---

### Key Takeaways

- **DB2 calls inside loops** are consistently the highest-impact optimization target in COBOL batch programs — finding and eliminating them is the first place to look
- **Repeated single-row fetches** can almost always be replaced with a single fetch into Working Storage — zero business logic change, significant CPU reduction
- Bob's metadata database makes cross-program analysis possible — patterns that are invisible in a single file become obvious when viewed across the whole application
- The optimization report is a handoff document — it gives the DB2 DBA and the development team a shared, prioritized action list without requiring both to be in the same conversation

::: {.callout-tip}
## Start a New Chat
Please select the **+** sign at the top of the chat window to start a new session before moving to the next lab.
:::
