## Lab 8: COBOL-to-SAP Real-Time Integration via JSON & zOS Connect

> **Prerequisites:** Complete Lab 1 (Lab Setup) before starting this lab
> **Code Set:** Any COBOL workspace — recommended code: `Sample Code`
> **Duration:** 60 min
> **Difficulty:** Advanced

---

### Overview

Many SAP customers today move data from mainframe COBOL programs to SAP by writing flat batch output files, transforming them to JSON offline, and then ingesting them on a schedule. This works — but it is not real-time, it introduces latency, and it requires a fragile file-transfer pipeline that breaks silently.

IBM Bob Premium for Z, paired with zOS Connect, makes it possible to eliminate the batch file entirely and push data to SAP APIs directly from a running COBOL program. There are two paths to get there, and this lab covers both:

| Path | What Changes | When to Use |
|---|---|---|
| **Path A — Modify the COBOL program** | Add `JSON GENERATE` code inside the existing program; call a zOS Connect endpoint instead of writing the file | When you own the program and want the cleanest long-term solution |
| **Path B — Map at the middleware layer** | Leave the COBOL program untouched; configure zOS Connect to transform the existing output-file format to JSON and route it to the SAP API | When the program cannot be changed, or when speed of delivery matters |

By the end of this lab you will be able to:

- Use Bob to analyze a COBOL program and its copybooks, and understand the data structures being produced
- Use Bob's Coding Agent to generate `JSON GENERATE` code that maps a COBOL copybook to a JSON payload
- Understand how to replace a file-write with a zOS Connect API call inside COBOL
- Understand the alternative mapping-only approach in zOS Connect for programs that cannot be changed

---

### Background: Why This Matters for SAP

SAP systems consume data through APIs. Before zOS Connect, the only practical way to feed mainframe data to SAP was via batch files — the COBOL program writes a fixed-format output file, a middleware process picks it up, transforms it to JSON, and calls the SAP API on a schedule. This means:

- Data is always slightly stale (last batch run)
- The file transfer pipeline must be monitored and maintained
- Failures surface late — often hours after the root cause

With Bob and zOS Connect, a COBOL program can produce a JSON payload directly (using the native `JSON GENERATE` verb) and call a zOS Connect-exposed SAP API synchronously — turning a batch feed into a real-time integration.

---

### Exercise 1: Understand the Existing Program and Copybook

Before generating any new code, use Bob to build a complete picture of what data the program already produces and what the target JSON structure needs to look like.

#### Actions

> Ensure you are in **Z Architect** mode

ACTION: Start a new chat. In the chat window, enter the following prompt (substituting your program name):

```
Analyze the COBOL program that currently writes policy output records.
Identify the output copybook it uses, list every field in that copybook
with its COBOL picture clause and its business meaning, and summarize
the data that would need to appear in a JSON payload sent to an SAP API.
```

> **Tip:** If you are using the GenApp sample code, try `LGAPDB01.cbl` and the `LGPOLICY.cpy` copybook as a stand-in for a policy output program.

ACTION: Approve any tool requests Bob raises to read the program and copybook files.

#### Expected Results

- ✅ Bob lists every field in the output copybook with its PIC clause
- ✅ Bob identifies the business meaning of each field (drawing from the Data Dictionary if available)
- ✅ Bob produces a candidate JSON structure showing how the COBOL fields map to JSON keys

---

### Exercise 2 (Path A) — Generate JSON GENERATE Code Inside the COBOL Program

COBOL natively supports JSON output via the `JSON GENERATE` verb (available on IBM Enterprise COBOL 6.1+). Bob can generate the working-storage additions, the `JSON GENERATE` statement, and the error-handling logic needed to produce a JSON string from an existing copybook-based structure.

#### Actions

> Ensure you are in **Z Code** mode

ACTION: In the chat window, enter the following prompt:

```
Using the copybook fields identified in the previous analysis, generate
the COBOL working-storage and PROCEDURE DIVISION code needed to produce
a JSON payload using JSON GENERATE.

Requirements:
- Declare a WS-JSON-OUTPUT PIC X(4096) working-storage field to hold the result
- Declare a WS-JSON-LENGTH BINARY-LONG field for the returned length
- Generate a paragraph named 9000-BUILD-JSON that:
    1. Moves the relevant output fields into a JSON source group item
    2. Executes JSON GENERATE WS-JSON-OUTPUT FROM <group-name>
       COUNT IN WS-JSON-LENGTH
       ON EXCEPTION PERFORM 9900-JSON-ERROR
    3. Ends with EXIT
- Generate a minimal 9900-JSON-ERROR paragraph that sets a return code
Follow the coding standards of the existing programs in the workspace.
```

ACTION: Approve any tool requests. When complete, review the generated code in the chat.

#### Expected Results

- ✅ Working-storage additions declared with correct PIC clauses
- ✅ `JSON GENERATE` statement references the correct group item from the copybook
- ✅ Exception handling paragraph present
- ✅ Code follows the naming and paragraph conventions of the existing workspace

---

### Exercise 3 (Path A) — Replace the File Write with a zOS Connect API Call

Once the JSON payload is built in working-storage, the program needs to POST it to a zOS Connect endpoint instead of writing to a file. zOS Connect exposes SAP APIs (and any other REST API) as callable services from COBOL using standard HTTP verbs over a CICS LINK or native zOS Connect client.

#### Actions

> Remain in **Z Code** mode

ACTION: In the chat window, enter the following prompt:

```
Generate COBOL PROCEDURE DIVISION code for a paragraph named
9100-CALL-ZOSCONNECT that:

1. Uses EXEC CICS LINK to call the zOS Connect service program
   (service name placeholder: ZSAP-POLICY-API)
2. Passes WS-JSON-OUTPUT and WS-JSON-LENGTH in the COMMAREA
3. Checks the CICS response code (RESP / RESP2) after the call
4. Sets WS-RETURN-CODE to 20 on a non-zero RESP
5. Ends with EXIT

Follow the EXEC CICS patterns used in the existing workspace programs.
```

ACTION: Review the generated paragraph. This is the replacement for the existing `WRITE` statement to the output file.

#### Expected Results

- ✅ `EXEC CICS LINK` call with correct COMMAREA structure
- ✅ RESP / RESP2 error checking present
- ✅ Return code set on failure, consistent with workspace conventions

---

### Exercise 4 (Path B) — Understand the Middleware Mapping Alternative

Path B does not change the COBOL program at all. Instead, zOS Connect is configured to:

1. Watch for (or be triggered by) the existing output file the program writes
2. Apply a field-level mapping from the fixed-format record layout to a JSON structure
3. Route the resulting JSON to the SAP API endpoint

This approach is preferred when:
- The program is owned by another team or cannot be changed
- The output file format is already well-defined and stable
- You need to deliver the integration faster than a code change allows

#### Actions

> Switch to **Z Architect** mode

ACTION: In the chat window, enter the following prompt:

```
Review the output record layout produced by the COBOL program analyzed
earlier. Generate a zOS Connect API requester mapping document that:
- Lists the source fields from the fixed-format record with their offset,
  length, and type
- Maps each source field to its target JSON key name
- Notes any data type conversions needed (e.g. packed decimal to number,
  EBCDIC date to ISO 8601)
- Identifies any fields in the SAP API payload that have no direct
  counterpart in the COBOL record and would need a default or derived value
```

ACTION: Approve tool requests as Bob reads the copybook and output record structure.

#### Expected Results

- ✅ Source-to-target mapping table produced
- ✅ Data type conversion requirements identified
- ✅ Gaps between the COBOL record and the SAP API payload called out explicitly
- ✅ Output is suitable as input to a zOS Connect mapping configuration

---

### Exercise 5: Verify the JSON Structure Against the SAP API Contract

Whether you used Path A or Path B, the final step is to confirm that the JSON structure Bob generated actually matches what the SAP API expects.

#### Actions

> Remain in **Z Architect** mode

ACTION: In the chat window, enter:

```
Review the JSON payload structure we generated and compare it against
the SAP API contract (provide the OpenAPI spec or field list if available).
Identify any field name mismatches, missing required fields, wrong data
types, or structural differences that would cause the API call to fail.
```

> **Note:** If you do not have the SAP API spec available during the lab, paste in a representative JSON example from your SAP documentation and Bob will diff against it.

#### Expected Results

- ✅ Field-by-field comparison between generated payload and API contract
- ✅ Mismatches and missing fields identified
- ✅ Suggested fixes listed for any gaps found

---

### Key Takeaways

| What You Did | Why It Matters |
|---|---|
| Used Bob to map a COBOL copybook to a JSON structure | Eliminates manual discovery and speeds up integration design |
| Generated `JSON GENERATE` working-storage and procedure code | Produces working, standards-compliant COBOL ready for review and test |
| Generated a zOS Connect CICS LINK call | Shows the integration point between COBOL and the SAP API |
| Produced a middleware mapping document (Path B) | Provides a no-code-change route for programs that cannot be modified |
| Verified the payload against the SAP API contract | Catches structural mismatches before they reach a test environment |

### Further Reading

- IBM Enterprise COBOL for z/OS Language Reference — `JSON GENERATE` and `JSON PARSE` statements
- IBM zOS Connect EE documentation — API requester configuration and CICS integration
- SAP Integration Suite — REST API consumption patterns
