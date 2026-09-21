# Starfish and Vulcan API review

Review snapshot: 19 September 2026. Repository commit `6a4d78d3f0caba6659d193cb5b3d649be48ac60c`.

**60 source rows examined: 32 endpoint rows and 28 field rows. These produce 78 separately tracked findings.** Original comments remain verbatim in the workbook’s source sheets and in the feedback panels below.

**Firmware decision:** one reviewer explicitly confirms a firmware need at endpoint level (`F-013`, Starfish cloudConfig). No exact firmware leaf-node change is approved by the available evidence. The `dataAck` branch is a candidate to discuss, not a confirmed implementation requirement. All other potential code changes remain Developer Clarification.

## Review navigation

Select an endpoint below, open its Issue ID, show the two schema trees, and record the decision in `API_Review_Tracker.xlsx` using the same ID. In Excel, update Developer Confirmation with the owner, date, firmware version, evidence and decision, then update Change Type and Status. Keep both downloaded files in the same folder for relative walkthrough links.

| Change Type | Findings | Meaning in this review |
|---|---:|---|
| Firmware Change | 1 | Reviewer-confirmed need; exact scope still open |
| Developer Clarification | 35 | Runtime, model, release or compatibility decision needed |
| Schema Update | 21 | Reviewer-supported implementation is missing or wrong in schema |
| Documentation Only | 2 | Review/documentation text correction; no schema or code change |
| No Change Required | 19 | Specific source concern already addressed or intentional |

Resolved applies only to the specific no-change finding, not to every aspect of that endpoint. Schema updates remain open until applied and verified.

### First decisions for the developer meeting

- [F-013: identify the actual Starfish cloudConfig firmware gaps](#f-013).
- [F-011: resolve app-install Q3/Q4 release conflict](#f-011).
- [F-009B: decide whether nested Starfish interface setters are committed](#f-009b).
- [E-003: resolve CA-certificate local REST versus MQTT support](#e-003).
- [E-008 / E-009: confirm dedicated FX9600 Gen2X endpoints](#e-008).
- [E-024: establish FX9600 region-setter release status](#e-024).

### Endpoint table of contents

| Endpoint | Issue IDs |
|---|---|
| [/cloud/app-led](#endpoint-01) | [F-001](#f-001), [F-002](#f-002), [F-002A](#f-002a) |
| [/cloud/apps/install](#endpoint-02) | [F-011](#f-011) |
| [/cloud/apps/{appname}/autostart](#endpoint-03) | [F-003](#f-003) |
| [/cloud/apps/{appname}/pass-through](#endpoint-04) | [F-012](#f-012), [F-012A](#f-012a) |
| [/cloud/apps/{appname}/start](#endpoint-05) | [F-004](#f-004) |
| [/cloud/apps/{appname}/stop](#endpoint-06) | [F-005](#f-005) |
| [/cloud/apps/{appname}/uninstall](#endpoint-07) | [F-006](#f-006) |
| [/cloud/bleConfig](#endpoint-08) | [E-001](#e-001), [E-002](#e-002) |
| [/cloud/caCertificates](#endpoint-09) | [E-004](#e-004), [E-003](#e-003), [E-005](#e-005) |
| [/cloud/certificates](#endpoint-10) | [F-007](#f-007) |
| [/cloud/certificates/{certname}](#endpoint-11) | [F-008](#f-008), [F-008A](#f-008a) |
| [/cloud/cloudConfig](#endpoint-12) | [F-013](#f-013), [F-013A](#f-013a), [F-013B](#f-013b) |
| [/cloud/config](#endpoint-13) | [F-014](#f-014), [F-014A](#f-014a), [F-014B](#f-014b), [F-014C](#f-014c), [F-015](#f-015), [F-015A](#f-015a), [F-015B](#f-015b) |
| [/cloud/eSimConfig](#endpoint-14) | [E-006](#e-006), [E-007](#e-007) |
| [/cloud/gpi](#endpoint-15) | [F-016](#f-016) |
| [/cloud/impinjGen2X](#endpoint-16) | [E-008](#e-008), [E-009](#e-009) |
| [/cloud/localRestLogin](#endpoint-17) | [E-010](#e-010) |
| [/cloud/logs/{logType}](#endpoint-18) | [E-014](#e-014), [E-016](#e-016), [E-029](#e-029), [E-011](#e-011), [E-012](#e-012), [E-013](#e-013), [E-015](#e-015), [E-017](#e-017), [E-030](#e-030) |
| [/cloud/mode](#endpoint-19) | [F-017](#f-017), [F-017A](#f-017a), [F-018](#f-018), [F-018A](#f-018a) |
| [/cloud/nameAndDescription](#endpoint-20) | [E-031](#e-031), [E-032](#e-032) |
| [/cloud/network](#endpoint-21) | [F-019](#f-019), [F-009](#f-009), [F-009A](#f-009a), [F-009B](#f-009b), [F-020](#f-020), [F-020A](#f-020a) |
| [/cloud/networkInterfaces](#endpoint-22) | [E-018](#e-018) |
| [/cloud/ntpServer](#endpoint-23) | [F-021](#f-021) |
| [/cloud/os](#endpoint-24) | [F-022](#f-022), [F-022A](#f-022a) |
| [/cloud/pass-through](#endpoint-25) | [E-019](#e-019) |
| [/cloud/preSelection](#endpoint-26) | [E-020](#e-020), [E-021](#e-021) |
| [/cloud/readPoints](#endpoint-27) | [E-022](#e-022) |
| [/cloud/readerCapabilities](#endpoint-28) | [F-023](#f-023), [F-023A](#f-023a) |
| [/cloud/readerLocation](#endpoint-29) | [E-023](#e-023) |
| [/cloud/region](#endpoint-30) | [F-024](#f-024), [E-024](#e-024) |
| [/cloud/stack-led](#endpoint-31) | [E-025](#e-025), [E-026](#e-026) |
| [/cloud/start](#endpoint-32) | [F-025](#f-025) |
| [/cloud/status](#endpoint-33) | [F-026](#f-026), [F-026A](#f-026a) |
| [/cloud/stop](#endpoint-34) | [F-010](#f-010) |
| [/cloud/supportedStandardList](#endpoint-35) | [F-027](#f-027), [F-028](#f-028) |
| [/cloud/updatePassword](#endpoint-36) | [E-027](#e-027) |
| [/cloud/wifiNetworks](#endpoint-37) | [E-028](#e-028) |

## Evidence and interpretation

- [Original feedback workbook](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/Unified_FXR_Starfish_Review.xlsx): preserved exactly, including source limitations.
- [Starfish comparison source, FXR series.json](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json): despite its filename, contents reference FX9600, FX7500 and ATR7000. It is a multi-model legacy specification, not an FX9600-only contract. Model applicability is checked against its narrative and reviewer feedback.
- [Vulcan documentation and schemas, FXR_60-90_rest_api.yaml](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml): includes operation narratives, examples and reusable schema components.
- No firmware source, reader access, live response captures or approved implementation tickets were supplied. “Current behavior” distinguishes documented contracts from reviewer-reported behavior.
- The workbook’s api_field_details links do not exist in this repository snapshot. Comparisons below were reconstructed from the actual specifications; those missing detail files are not claimed as evidence.
- Missing source text: endpoint row 32 ends at “cu”; field row 16 is truncated; Kamali field rows 17–28 were not provided. No missing text has been inferred.
- Schema trees show selected branches with their parent nodes. Bold nodes are the review focus, not automatically firmware defects. `oneOf`/`anyOf`/`allOf` are schema alternatives, not JSON payload keys. Exact JSON instance paths and JSON Pointers are listed separately.
- “Absent” means absent from this schema. Unless additionalProperties forbids it, a missing named property need not even be rejected by schema validation. It never independently proves firmware rejection.
- JSON Pointer `~1` encodes `/`. A missing-operation pointer identifies where an operation would be documented; it is not a firmware source-code location.
- Parameter, request-body and success-response contracts were inspected for every source operation. Trees focus on the commented discrepancy. Error schemas are not used to infer feature support.

<a id="endpoint-01"></a>
## /cloud/app-led

<a id="f-001"></a>
### F-001 — GET — LED response wrapper

**Schema Update · Open · Platform: Starfish**  
Source: `Field review` source row 1 (Excel row 7); original endpoint `GET /cloud/app-led`.

#### 1. Issue

Starfish 200 response is a bare string enum; Vulcan returns an object with status. Both reviewers say the readers return status.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/app-led`
  - Success response body
    - **`$`** — type="string"; enum=["DEFAULT","NON_DEFAULT"]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/app-led`
  - Success response body
    - **`$`** — type="object"
      - **`status`** — type="string"; enum=["DEFAULT","NON_DEFAULT"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/paths/~1cloud~1app-led/get/responses/200/content/text~1html/schema` |
| Vulcan | `$` | `#/components/schemas/GetAppledResponse` |
| Vulcan | `$["status"]` | `#/components/schemas/GetAppledResponse/properties/status` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [text/html]: type="string"; enum=["DEFAULT","NON_DEFAULT"]. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Starfish should describe the existing {"status":"DEFAULT"} / {"status":"NON_DEFAULT"} object.

#### 6. Required action

Replace the Starfish response schema with an object containing status and update the example. Confirm requiredness from a response capture.

#### 7. Developer question

Is status always present on FX9600, including the default state, and are additional fields ever returned?

Illustrative JSON shape for discussion; not a newly captured reader response:

```json
{"status":"DEFAULT"}
```

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: Incorrect in documentation
> Needed: yes
> Fix: Same as vulcan.
>
> Kamali:
> Why: both starfish and vulcan are returning status
> Needed: no change is needed..check if schema is properly updated .

</details>

**Record decision:** tracker Issue ID `F-001`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-002"></a>
### F-002 — PUT — LED request field location

**Schema Update · Open · Platform: Starfish**  
Source: `Field review` source row 2 (Excel row 8); original endpoint `PUT /cloud/app-led`.

#### 1. Issue

Starfish defines color/seconds/flash as query parameters with no body; Vulcan defines a JSON body. Both reviews explicitly confirm JSON-body support on Starfish.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/app-led`
  - Parameters
    - **query `color`** — {"type": "string", "enum": ["red", "amber", "green", "off"]}; required=true
    - **query `seconds`** — {"type": "integer"}; required=true
    - **query `flash`** — {"type": "boolean"}; required=false
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/app-led`
  - Parameters: none declared
  - JSON request body
    - **`$`** — type="object"; required=["color","flash","seconds"]
      - **`color`** — type="string"; enum=["red","amber","green","off"]
      - **`flash`** — type="boolean"
      - **`seconds`** — type="number"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `query color` | `#/paths/~1cloud~1app-led/put/parameters/0` |
| Starfish | `query seconds` | `#/paths/~1cloud~1app-led/put/parameters/1` |
| Starfish | `query flash` | `#/paths/~1cloud~1app-led/put/parameters/2` |
| Vulcan | `$` | `#/components/schemas/SetAppledRequest` |
| Vulcan | `$["color"]` | `#/components/schemas/SetAppledRequest/properties/color` |
| Vulcan | `$["flash"]` | `#/components/schemas/SetAppledRequest/properties/flash` |
| Vulcan | `$["seconds"]` | `#/components/schemas/SetAppledRequest/properties/seconds` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: query color, query seconds, query flash. Request: none declared. Success: 200 [text/html]: type="string"; default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object"; required=["color","flash","seconds"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Represent the working Starfish JSON body. Query compatibility is a separate decision in F-002A.

#### 6. Required action

Move the documented Starfish fields to requestBody and update examples. Do not introduce an adapter or firmware change for already-working JSON requests.

#### 7. Developer question

For the JSON body, is flash required or optional on FX9600, and must seconds be an integer? Starfish currently marks flash optional and seconds integer; Vulcan requires flash and uses number.

Illustrative JSON shape for discussion; not a newly captured reader response:

```json
{"color":"green","flash":false,"seconds":10}
```

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: Incorrect in documentation
> Needed: yes
> Fix: Same as vulcan. The parameters are intended to be part of the body
>
> Kamali:
> Why: If color,flash,seconds are provided in json body the command works in starfish..
> Needed: may need a schema update in starfish..or need to check if both json body and query params need to be supported in vulcan

Source limitation: The original claim that a body/query adapter is mandatory is not supported by these comments.

</details>

**Record decision:** tracker Issue ID `F-002`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-002a"></a>
### F-002A — PUT — LED query compatibility requirement

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 2 (Excel row 8); original endpoint `PUT /cloud/app-led`.

#### 1. Issue

Kamali asks whether Vulcan must also accept Starfish query parameters. JSON body already works on both; the current schemas do not establish compatibility requirements.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/app-led`
  - Parameters
    - **query `color`** — {"type": "string", "enum": ["red", "amber", "green", "off"]}; required=true
    - **query `seconds`** — {"type": "integer"}; required=true
    - **query `flash`** — {"type": "boolean"}; required=false
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/app-led`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["color","flash","seconds"]
      - **REVIEW `color`** — type="string"; enum=["red","amber","green","off"]
      - **REVIEW `flash`** — type="boolean"
      - **REVIEW `seconds`** — type="number"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `query color` | `#/paths/~1cloud~1app-led/put/parameters/0` |
| Starfish | `query seconds` | `#/paths/~1cloud~1app-led/put/parameters/1` |
| Starfish | `query flash` | `#/paths/~1cloud~1app-led/put/parameters/2` |
| Vulcan | `$` | `#/components/schemas/SetAppledRequest` |
| Vulcan | `$["color"]` | `#/components/schemas/SetAppledRequest/properties/color` |
| Vulcan | `$["flash"]` | `#/components/schemas/SetAppledRequest/properties/flash` |
| Vulcan | `$["seconds"]` | `#/components/schemas/SetAppledRequest/properties/seconds` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: query color, query seconds, query flash. Request: none declared. Success: 200 [text/html]: type="string"; default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object"; required=["color","flash","seconds"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Keep JSON body as the documented contract. Add query support only if backward compatibility is explicitly required.

#### 6. Required action

Decide whether query-only and mixed query/body requests are supported, rejected or deprecated. If new support is required, specify precedence, validation and release.

#### 7. Developer question

Must FXR60/FXR90 accept legacy ?color=green&flash=false&seconds=10 requests? Does FX9600 still accept them, and what wins if both query and body values are supplied?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: Incorrect in documentation
> Needed: yes
> Fix: Same as vulcan. The parameters are intended to be part of the body
>
> Kamali:
> Why: If color,flash,seconds are provided in json body the command works in starfish..
> Needed: may need a schema update in starfish..or need to check if both json body and query params need to be supported in vulcan

Source limitation: The original claim that a body/query adapter is mandatory is not supported by these comments.

</details>

**Record decision:** tracker Issue ID `F-002A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-02"></a>
## /cloud/apps/install

<a id="f-011"></a>
### F-011 — PUT — Application-download retry release

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 11 (Excel row 17); original endpoint `PUT /cloud/apps/install`.

#### 1. Issue

Starfish has retry.type=randomWait, policy.retries 1–50, wait bounds and timeouts. Vulcan omits retry/timeouts. Kamali names both Q3 and Q4 without a year or firmware version. The current Vulcan operation description explicitly says download retry/timeout settings are not supported.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/apps/install`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["url","filename","authenticationType"]
      - **REVIEW `verifyPeer`** — type="boolean"; default=true
      - **REVIEW `verifyHost`** — type="boolean"; default=true
      - **REVIEW `headers`** — type="object"
      - **REVIEW `retry`** — type="object"
        - **REVIEW `type`** — type="string"; enum=["randomWait"]
        - **REVIEW `policy`** — type="object"
          - **REVIEW `retries`** — type="integer"; minimum=1; maximum=50; default=1
          - **REVIEW `wait`** — type="object"
            - **REVIEW `min`** — type="integer"; minimum=0; maximum=3600; default=30
            - **REVIEW `max`** — type="integer"; minimum=1; maximum=3600; default=300
      - **REVIEW `timeouts`** — type="object"
        - **REVIEW `connection`** — type="integer"; minimum=1; maximum=3600; default=60
        - **REVIEW `read`** — type="integer"; minimum=1; maximum=3600; default=600

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/apps/install`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["url","filename","authenticationType"]
      - **REVIEW `verifyPeer`** — type="boolean"
      - **REVIEW `verifyHost`** — type="boolean"
      - **REVIEW `publicKeyFileLocation`** — type="string"
      - **REVIEW `privateKeyFileLocation`** — type="string"
      - **REVIEW `installedCertificateType`** — type="string"
      - **REVIEW `installedCertificateName`** — type="string"
      - **REVIEW `headers`** — type="object"; additionalProperties={"type":"string"}

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["verifyPeer"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/verifyPeer` |
| Starfish | `$["verifyHost"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/verifyHost` |
| Starfish | `$["headers"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/headers` |
| Starfish | `$["retry"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/retry` |
| Starfish | `$["retry"]["type"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/retry/properties/type` |
| Starfish | `$["retry"]["policy"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/retry/properties/policy` |
| Starfish | `$["retry"]["policy"]["retries"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/retry/properties/policy/properties/retries` |
| Starfish | `$["retry"]["policy"]["wait"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/retry/properties/policy/properties/wait` |
| Starfish | `$["retry"]["policy"]["wait"]["min"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/retry/properties/policy/properties/wait/properties/min` |
| Starfish | `$["retry"]["policy"]["wait"]["max"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/retry/properties/policy/properties/wait/properties/max` |
| Starfish | `$["timeouts"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/timeouts` |
| Starfish | `$["timeouts"]["connection"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/timeouts/properties/connection` |
| Starfish | `$["timeouts"]["read"]` | `#/paths/~1cloud~1apps~1install/put/requestBody/content/application~1json/schema/properties/timeouts/properties/read` |
| Vulcan | `$["verifyPeer"]` | `#/components/schemas/SetInstalluserappRequest/properties/verifyPeer` |
| Vulcan | `$["verifyHost"]` | `#/components/schemas/SetInstalluserappRequest/properties/verifyHost` |
| Vulcan | `$["publicKeyFileLocation"]` | `#/components/schemas/SetInstalluserappRequest/properties/publicKeyFileLocation` |
| Vulcan | `$["privateKeyFileLocation"]` | `#/components/schemas/SetInstalluserappRequest/properties/privateKeyFileLocation` |
| Vulcan | `$["installedCertificateType"]` | `#/components/schemas/SetInstalluserappRequest/properties/installedCertificateType` |
| Vulcan | `$["installedCertificateName"]` | `#/components/schemas/SetInstalluserappRequest/properties/installedCertificateName` |
| Vulcan | `$["headers"]` | `#/components/schemas/SetInstalluserappRequest/properties/headers` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["url","filename","authenticationType"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["url","filename","authenticationType"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Publish the fields only for firmware releases that support them; determine whether current FXR behavior already supports them.

#### 6. Required action

Resolve Q3/Q4 and capture FXR requests. If implemented, update its schema. If planned, identify firmware work and release. Also verify certificate/header differences rather than copying them wholesale.

#### 7. Developer question

Which FXR firmware supports retry and timeouts for app installation? Is the committed release Q3 or Q4, in which year? Are the Starfish bounds/defaults identical on FXR?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 has backoff policy to support resonate
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Why: These have been added in starfish for resonate. We will be supporting this in Q3.
> Needed: will be supported from Q4

Source limitation: Kamali gives two different release quarters.

</details>

**Record decision:** tracker Issue ID `F-011`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-03"></a>
## /cloud/apps/{appname}/autostart

<a id="f-003"></a>
### F-003 — PUT — Redundant REST appname body

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 3 (Excel row 9); original endpoint `PUT /cloud/apps/{appname}/autostart`.

#### 1. Issue

Both schemas require path appname. Vulcan also declares optional body appname; Kamali confirms the request works without it. Starfish keeps body autostart.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/apps/{appname}/autostart`
  - Parameters
    - **path `appname`** — {"type": "string", "example": "sample"}; required=true
  - JSON request body
    - **`$`** — type="object"
      - **`autostart`** — type="boolean"; default=true

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/apps/{appname}/autostart`
  - Parameters
    - **path `appname`** — {"type": "string"}; required=true
  - JSON request body
    - **`$`** — type="object"; required=["autostart"]
      - **`appname`** — type="string"
      - **`autostart`** — type="boolean"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1autostart/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1apps~1{appname}~1autostart/put/requestBody/content/application~1json/schema` |
| Starfish | `$["autostart"]` | `#/paths/~1cloud~1apps~1{appname}~1autostart/put/requestBody/content/application~1json/schema/properties/autostart` |
| Vulcan | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1autostart/parameters/0` |
| Vulcan | `$` | `#/components/schemas/SetAutostartuserappRequest` |
| Vulcan | `$["appname"]` | `#/components/schemas/SetAutostartuserappRequest/properties/appname` |
| Vulcan | `$["autostart"]` | `#/components/schemas/SetAutostartuserappRequest/properties/autostart` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path appname. Request: type="object". Success: 200 []: no body schema. |
| Vulcan | Parameters: path appname. Request: type="object"; required=["autostart"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use path appname for local REST and body autostart. MQTT payload routing remains separate.

#### 6. Required action

Remove redundant Vulcan REST body appname and its examples. Retain autostart and its required flag.

#### 7. Developer question

Does path appname take precedence if an old client also sends a different body appname? Record deprecation behavior without requiring the redundant field.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
> Fix: All app based requests have appname + request in the url for local rest requests but have the "appname" key for mqtt type requests
>
> Kamali:
> Why: appname is not required in the payload..works even when removed..needs a schema update
> Needed: needs schema update

Source limitation: Starfish “No changes required” does not remove the FXR schema action.

</details>

**Record decision:** tracker Issue ID `F-003`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-04"></a>
## /cloud/apps/{appname}/pass-through

<a id="f-012"></a>
### F-012 — PUT — Vulcan command supports string or object

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 12 (Excel row 18); original endpoint `PUT /cloud/apps/{appname}/pass-through`.

#### 1. Issue

Starfish command is string. Vulcan schema permits only object {message}, but Kamali states its code accepts both string and object.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/apps/{appname}/pass-through`
  - Parameters
    - **path `appname`** — {"type": "string", "example": "sample"}; required=true
  - JSON request body
    - **`$`** — type="object"; required=["userapp","command"]
      - **`userapp`** — type="string"
      - **`command`** — type="string"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/apps/{appname}/pass-through`
  - Parameters
    - **path `appname`** — {"type": "string"}; required=true
  - JSON request body
    - **`$`** — type="object"
      - **`command`** — type="object"
        - **`message`** — type="string"
      - **`userapp`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/put/requestBody/content/application~1json/schema` |
| Starfish | `$["userapp"]` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/put/requestBody/content/application~1json/schema/properties/userapp` |
| Starfish | `$["command"]` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/put/requestBody/content/application~1json/schema/properties/command` |
| Vulcan | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/parameters/0` |
| Vulcan | `$` | `#/components/schemas/SetReqtouserappRequest` |
| Vulcan | `$["command"]` | `#/components/schemas/SetReqtouserappRequest/properties/command` |
| Vulcan | `$["command"]["message"]` | `#/components/schemas/SetReqtouserappRequest/properties/command/properties/message` |
| Vulcan | `$["userapp"]` | `#/components/schemas/SetReqtouserappRequest/properties/userapp` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path appname. Request: type="object"; required=["userapp","command"]. Success: 200 [application/json]: type="object"; required=["response"]. |
| Vulcan | Parameters: path appname. Request: type="object". Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Vulcan command should allow string or object; retain known object fields until a broader object contract is confirmed.

#### 6. Required action

Use a string/object union for Vulcan command and show one example of each supported form.

#### 7. Developer question

Is the accepted object specifically {message:string}, or any JSON object? Is REST userapp required when appname is already in the path?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: code supports both object and string.needs schema update
> Needed: needs schema update to specify either object or string

Source limitation: Kamali’s FXR review does not explicitly establish both forms on Starfish. Object properties and REST userapp requirement still need confirmation.

</details>

**Record decision:** tracker Issue ID `F-012`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-012a"></a>
### F-012A — PUT — Starfish pass-through parity decision

**Developer Clarification · Open · Platform: Starfish**  
Source: `Field review` source row 12 (Excel row 18); original endpoint `PUT /cloud/apps/{appname}/pass-through`.

#### 1. Issue

Starfish only documents command:string and marks userapp/command required. Kamali’s dual-type statement is in the FXR review and does not establish Starfish object support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/apps/{appname}/pass-through`
  - Parameters
    - **path `appname`** — {"type": "string", "example": "sample"}; required=true
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["userapp","command"]
      - **REVIEW `userapp`** — type="string"
      - **REVIEW `command`** — type="string"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/apps/{appname}/pass-through`
  - Parameters
    - **path `appname`** — {"type": "string"}; required=true
  - JSON request body
    - **REVIEW `$`** — type="object"
      - **REVIEW `command`** — type="object"
        - **REVIEW `message`** — type="string"
      - **REVIEW `userapp`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/put/requestBody/content/application~1json/schema` |
| Starfish | `$["userapp"]` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/put/requestBody/content/application~1json/schema/properties/userapp` |
| Starfish | `$["command"]` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/put/requestBody/content/application~1json/schema/properties/command` |
| Vulcan | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1pass-through/parameters/0` |
| Vulcan | `$` | `#/components/schemas/SetReqtouserappRequest` |
| Vulcan | `$["command"]` | `#/components/schemas/SetReqtouserappRequest/properties/command` |
| Vulcan | `$["command"]["message"]` | `#/components/schemas/SetReqtouserappRequest/properties/command/properties/message` |
| Vulcan | `$["userapp"]` | `#/components/schemas/SetReqtouserappRequest/properties/userapp` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path appname. Request: type="object"; required=["userapp","command"]. Success: 200 [application/json]: type="object"; required=["response"]. |
| Vulcan | Parameters: path appname. Request: type="object". Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the verified Starfish string form until object support or an explicit parity requirement is confirmed.

#### 6. Required action

Confirm object acceptance on FX9600 before copying the Vulcan union. Record userapp requiredness and its relation to path appname.

#### 7. Developer question

Does FX9600 accept a JSON object in command at this REST URL? If not, is adding it required, or is a string-only platform contract intentional?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: code supports both object and string.needs schema update
> Needed: needs schema update to specify either object or string

Source limitation: Kamali’s FXR review does not explicitly establish both forms on Starfish. Object properties and REST userapp requirement still need confirmation.

</details>

**Record decision:** tracker Issue ID `F-012A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-05"></a>
## /cloud/apps/{appname}/start

<a id="f-004"></a>
### F-004 — PUT — Redundant REST appname body

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 4 (Excel row 10); original endpoint `PUT /cloud/apps/{appname}/start`.

#### 1. Issue

Both schemas require path appname. Vulcan also declares optional body appname; Kamali confirms the request works without it. Starfish has no request body.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/apps/{appname}/start`
  - Parameters
    - **path `appname`** — {"type": "string", "example": "sample"}; required=true
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/apps/{appname}/start`
  - Parameters
    - **path `appname`** — {"type": "string"}; required=true
  - JSON request body
    - **`$`** — type="object"
      - **`appname`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1start/parameters/0` |
| Vulcan | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1start/parameters/0` |
| Vulcan | `$` | `#/components/schemas/SetStartuserappRequest` |
| Vulcan | `$["appname"]` | `#/components/schemas/SetStartuserappRequest/properties/appname` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path appname. Request: none declared. Success: 200 []: no body schema. |
| Vulcan | Parameters: path appname. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use path appname for local REST; omit the redundant appname-only body. MQTT payload routing remains separate.

#### 6. Required action

Remove redundant Vulcan REST body appname and its examples. Remove the body schema if no other REST fields are accepted.

#### 7. Developer question

Does path appname take precedence if an old client also sends a different body appname? Record deprecation behavior without requiring the redundant field.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: appname is not required in the payload..works even when removed..needs a schema update
> Needed: needs schema update

</details>

**Record decision:** tracker Issue ID `F-004`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-06"></a>
## /cloud/apps/{appname}/stop

<a id="f-005"></a>
### F-005 — PUT — Redundant REST appname body

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 5 (Excel row 11); original endpoint `PUT /cloud/apps/{appname}/stop`.

#### 1. Issue

Both schemas require path appname. Vulcan also declares optional body appname; Kamali confirms the request works without it. Starfish has no request body.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/apps/{appname}/stop`
  - Parameters
    - **path `appname`** — {"type": "string", "example": "sampl"}; required=true
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/apps/{appname}/stop`
  - Parameters
    - **path `appname`** — {"type": "string"}; required=true
  - JSON request body
    - **`$`** — type="object"
      - **`appname`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1stop/parameters/0` |
| Vulcan | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1stop/parameters/0` |
| Vulcan | `$` | `#/components/schemas/SetStopuserappRequest` |
| Vulcan | `$["appname"]` | `#/components/schemas/SetStopuserappRequest/properties/appname` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path appname. Request: none declared. Success: 200 []: no body schema. |
| Vulcan | Parameters: path appname. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use path appname for local REST; omit the redundant appname-only body. MQTT payload routing remains separate.

#### 6. Required action

Remove redundant Vulcan REST body appname and its examples. Remove the body schema if no other REST fields are accepted.

#### 7. Developer question

Does path appname take precedence if an old client also sends a different body appname? Record deprecation behavior without requiring the redundant field.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: appname is not required in the payload..works even when removed..needs a schema update
> Needed: needs schema update

</details>

**Record decision:** tracker Issue ID `F-005`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-07"></a>
## /cloud/apps/{appname}/uninstall

<a id="f-006"></a>
### F-006 — PUT — Redundant REST appname body

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 6 (Excel row 12); original endpoint `PUT /cloud/apps/{appname}/uninstall`.

#### 1. Issue

Both schemas require path appname. Vulcan also declares optional body appname; Kamali confirms the request works without it. Starfish has no request body.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/apps/{appname}/uninstall`
  - Parameters
    - **path `appname`** — {"type": "string"}; required=true
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/apps/{appname}/uninstall`
  - Parameters
    - **path `appname`** — {"type": "string"}; required=true
  - JSON request body
    - **`$`** — type="object"
      - **`appname`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1uninstall/parameters/0` |
| Vulcan | `path appname` | `#/paths/~1cloud~1apps~1{appname}~1uninstall/parameters/0` |
| Vulcan | `$` | `#/components/schemas/SetUninstalluserappRequest` |
| Vulcan | `$["appname"]` | `#/components/schemas/SetUninstalluserappRequest/properties/appname` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path appname. Request: none declared. Success: 200 []: no body schema. |
| Vulcan | Parameters: path appname. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use path appname for local REST; omit the redundant appname-only body. MQTT payload routing remains separate.

#### 6. Required action

Remove redundant Vulcan REST body appname and its examples. Remove the body schema if no other REST fields are accepted.

#### 7. Developer question

Does path appname take precedence if an old client also sends a different body appname? Record deprecation behavior without requiring the redundant field.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: appname is not required in the payload..works even when removed..needs a schema update
> Needed: needs schema update

</details>

**Record decision:** tracker Issue ID `F-006`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-08"></a>
## /cloud/bleConfig

<a id="e-001"></a>
### E-001 — GET — BLE path spelling in the review

**Documentation Only · Open · Platform: Vulcan**  
Source: `Endpoint review` source row 1 (Excel row 7); original endpoint `GET /cloud/ble-config`.

#### 1. Issue

Workbook uses /cloud/ble-config; the current Vulcan schema and narrative use /cloud/bleConfig. Both reviewers exclude FX9600.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/ble-config`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/bleConfig`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"
      - **`ble`** — type="object"; required=["enable","scanIntervalSec","additionalFilters","protocols"]
        - **`enable`** — type="boolean"
        - **`scanIntervalSec`** — type="integer"; minimum=0; maximum=300

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1ble-config/get` |
| Vulcan | `$["ble"]` | `#/components/schemas/GetBleConfigResponse/properties/ble` |
| Vulcan | `$["ble"]["enable"]` | `#/components/schemas/GetBleConfigResponse/properties/ble/properties/enable` |
| Vulcan | `$["ble"]["scanIntervalSec"]` | `#/components/schemas/GetBleConfigResponse/properties/ble/properties/scanIntervalSec` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewers confirm FXR-only support; Vulcan operation description and path agree on /cloud/bleConfig.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use the documented /cloud/bleConfig spelling and retain FXR60/FXR90 applicability.

#### 6. Required action

Correct the endpoint label in the consolidated review and cross-references; do not create an alias endpoint.

#### 7. Developer question

No behavior decision is needed for the spelling correction. If /cloud/ble-config is intended as a supported alias, provide an actual request result and firmware version.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr90/60

Source limitation: Path spelling retained from source; verify against the approved schema.

</details>

**Record decision:** tracker Issue ID `E-001`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-002"></a>
### E-002 — PUT — BLE path spelling in the review

**Documentation Only · Open · Platform: Vulcan**  
Source: `Endpoint review` source row 2 (Excel row 8); original endpoint `PUT /cloud/ble-config`.

#### 1. Issue

Workbook uses /cloud/ble-config; the current Vulcan schema and narrative use /cloud/bleConfig. Both reviewers exclude FX9600.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/ble-config`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/bleConfig`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["ble"]
      - **`ble`** — type="object"; required=["enable","scanIntervalSec","additionalFilters","protocols"]
        - **`enable`** — type="boolean"
        - **`scanIntervalSec`** — type="integer"; minimum=0; maximum=300

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1ble-config/put` |
| Vulcan | `$["ble"]` | `#/components/schemas/SetBleConfigRequest/properties/ble` |
| Vulcan | `$["ble"]["enable"]` | `#/components/schemas/SetBleConfigRequest/properties/ble/properties/enable` |
| Vulcan | `$["ble"]["scanIntervalSec"]` | `#/components/schemas/SetBleConfigRequest/properties/ble/properties/scanIntervalSec` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewers confirm FXR-only support; Vulcan operation description and path agree on /cloud/bleConfig.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["ble"]. Success: 200 []: no body schema. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use the documented /cloud/bleConfig spelling and retain FXR60/FXR90 applicability.

#### 6. Required action

Correct the endpoint label in the consolidated review and cross-references; do not create an alias endpoint.

#### 7. Developer question

No behavior decision is needed for the spelling correction. If /cloud/ble-config is intended as a supported alias, provide an actual request result and firmware version.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr90/60

Source limitation: Path spelling retained from source; verify against the approved schema.

</details>

**Record decision:** tracker Issue ID `E-002`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-09"></a>
## /cloud/caCertificates

<a id="e-004"></a>
### E-004 — DELETE — CA-certificate REST mapping and path contract

**Developer Clarification · Open · Platform: Both**  
Source: `Endpoint review` source row 4 (Excel row 10); original endpoint `DELETE /cloud/caCertificates/{caname}`.

#### 1. Issue

Starfish says FX9600 supports this REST operation; Kamali says local REST mapping is missing while MQTT exists. Starfish has no operation in this snapshot. The workbook path /cloud/caCertificates/{caname} is absent in both files. Vulcan instead uses DELETE /cloud/caCertificates with JSON name; its description says caname is rejected.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `DELETE /cloud/caCertificates`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `DELETE /cloud/caCertificates`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["name"]
      - **REVIEW `name`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1caCertificates/delete` |
| Vulcan | `$` | `#/components/schemas/deleteCACertificate.v1` |
| Vulcan | `$["name"]` | `#/components/schemas/deleteCACertificate.v1/properties/name` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["name"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Public REST support and the accepted URL/body must be established separately for each method and firmware release.

#### 6. Required action

Obtain a local REST request/response on FX9600. If supported, add its schema. If absent, decide whether REST exposure is an approved firmware requirement. Preserve MQTT scope separately.

#### 7. Developer question

On which FX9600 firmware does DELETE CA-certificate management work over local REST? Is the accepted URL /cloud/caCertificates or /cloud/caCertificates/{caname}, and is the certificate name in JSON name or the path? If no mapping exists, is adding it required?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (undocumented)
>
> Kamali:
> not supported in fx9600 (local rest mapping missing ,mqtt available)

Source limitation: MQTT availability does not confirm local REST support.

</details>

**Record decision:** tracker Issue ID `E-004`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-003"></a>
### E-003 — GET — CA-certificate REST mapping and path contract

**Developer Clarification · Open · Platform: Both**  
Source: `Endpoint review` source row 3 (Excel row 9); original endpoint `GET /cloud/caCertificates`.

#### 1. Issue

Starfish says FX9600 supports this REST operation; Kamali says local REST mapping is missing while MQTT exists. Starfish has no operation in this snapshot.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/caCertificates`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/caCertificates`
  - Parameters: none declared
  - Success response body
    - **REVIEW `$`** — type="array"
      - **REVIEW `[]`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1caCertificates/get` |
| Vulcan | `$` | `#/components/schemas/caCertificateList.v1` |
| Vulcan | `$[*]` | `#/components/schemas/caCertificateList.v1/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="array". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Public REST support and the accepted URL/body must be established separately for each method and firmware release.

#### 6. Required action

Obtain a local REST request/response on FX9600. If supported, add its schema. If absent, decide whether REST exposure is an approved firmware requirement. Preserve MQTT scope separately.

#### 7. Developer question

On which FX9600 firmware does GET CA-certificate management work over local REST? Is the accepted URL /cloud/caCertificates or /cloud/caCertificates/{caname}, and is the certificate name in JSON name or the path? If no mapping exists, is adding it required?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (undocumented)
>
> Kamali:
> not supported in fx9600 (local rest mapping missing ,mqtt available)

Source limitation: MQTT availability does not confirm local REST support.

</details>

**Record decision:** tracker Issue ID `E-003`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-005"></a>
### E-005 — PUT — CA-certificate REST mapping and path contract

**Developer Clarification · Open · Platform: Both**  
Source: `Endpoint review` source row 5 (Excel row 11); original endpoint `PUT /cloud/caCertificates/{caname}`.

#### 1. Issue

Starfish says FX9600 supports this REST operation; Kamali says local REST mapping is missing while MQTT exists. Starfish has no operation in this snapshot. The workbook path /cloud/caCertificates/{caname} is absent in both files. Vulcan instead uses PUT /cloud/caCertificates with JSON name and content.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/caCertificates`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/caCertificates`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["name","content"]
      - **REVIEW `name`** — type="string"
      - **REVIEW `content`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1caCertificates/put` |
| Vulcan | `$` | `#/components/schemas/installCACertificate.v1` |
| Vulcan | `$["name"]` | `#/components/schemas/installCACertificate.v1/properties/name` |
| Vulcan | `$["content"]` | `#/components/schemas/installCACertificate.v1/properties/content` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["name","content"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Public REST support and the accepted URL/body must be established separately for each method and firmware release.

#### 6. Required action

Obtain a local REST request/response on FX9600. If supported, add its schema. If absent, decide whether REST exposure is an approved firmware requirement. Preserve MQTT scope separately.

#### 7. Developer question

On which FX9600 firmware does PUT CA-certificate management work over local REST? Is the accepted URL /cloud/caCertificates or /cloud/caCertificates/{caname}, and is the certificate name in JSON name or the path? If no mapping exists, is adding it required?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (undocumented)
>
> Kamali:
> not supported in fx9600 (local rest mapping missing ,mqtt available)

Source limitation: MQTT availability does not confirm local REST support.

</details>

**Record decision:** tracker Issue ID `E-005`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-10"></a>
## /cloud/certificates

<a id="f-007"></a>
### F-007 — GET — Certificate-list array wrapper

**Schema Update · Open · Platform: Starfish**  
Source: `Field review` source row 7 (Excel row 13); original endpoint `GET /cloud/certificates`.

#### 1. Issue

Starfish describes one certificate object; Vulcan describes an array of those objects. Both reviewers confirm array responses.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/certificates`
  - Success response body
    - **`$`** — type="object"; required=["name","type","installTime","issuerName","publickey","serial","subjectName","validityStart","validityEnd"]
      - **`name`** — type="string"
      - **`type`** — type="string"; enum=["server","client","app"]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/certificates`
  - Success response body
    - **`$`** — type="array"
      - **`[]`** — type="object"; required=["installTime","issuerName","name","publickey","serial","subjectName","type","validityEnd","validityStart"]
        - **`name`** — type="string"
        - **`type`** — type="string"; enum=["server","client","app"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/paths/~1cloud~1certificates/get/responses/200/content/application~1json/schema` |
| Starfish | `$["name"]` | `#/paths/~1cloud~1certificates/get/responses/200/content/application~1json/schema/properties/name` |
| Starfish | `$["type"]` | `#/paths/~1cloud~1certificates/get/responses/200/content/application~1json/schema/properties/type` |
| Vulcan | `$` | `#/components/schemas/GetCertificatesResponse` |
| Vulcan | `$[*]` | `#/components/schemas/GetCertificatesResponse/items` |
| Vulcan | `$[*]["name"]` | `#/components/schemas/GetCertificatesResponse/items/properties/name` |
| Vulcan | `$[*]["type"]` | `#/components/schemas/GetCertificatesResponse/items/properties/type` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["name","type","installTime","issuerName","publickey","serial","subjectName","validityStart","validityEnd"]. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="array". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

GET /cloud/certificates returns an array, including [] when no certificates are installed if confirmed.

#### 6. Required action

Wrap the Starfish certificate object schema in type: array/items and update its example. Preserve certificate field names.

#### 7. Developer question

Does FX9600 return [] for an empty list and retain the same required fields on every certificate item?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: Incorrect in documentation
> Needed: yes
> Fix: Response must be an array of certificate obects
>
> Kamali:
> Why: Both the readers are returning array of objects.may need schema updation
> Needed: may need schema updation for starfish

Source limitation: Both reviews identify an array response; do not retain the original single-object client workaround.

</details>

**Record decision:** tracker Issue ID `F-007`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-11"></a>
## /cloud/certificates/{certname}

<a id="f-008"></a>
### F-008 — DELETE — Certificate type location already corrected

**No Change Required · Resolved · Platform: Both**  
Source: `Field review` source row 8 (Excel row 14); original endpoint `DELETE /cloud/certificates/{certname}`.

#### 1. Issue

The old comment reports a Vulcan query parameter. Both current schemas now require JSON body type (client or app) and path certname. Vulcan narrative explicitly places certname in the path and type in the body; it does not establish rejection of alternate forms.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `DELETE /cloud/certificates/{certname}`
  - Parameters
    - **path `certname`** — {"type": "string"}; required=true
  - JSON request body
    - **`$`** — type="object"; required=["type"]
      - **`type`** — type="string"; enum=["client","app"]

**Vulcan — FXR60 / FXR90**

- `DELETE /cloud/certificates/{certname}`
  - Parameters
    - **path `certname`** — {"type": "string"}; required=true
  - JSON request body
    - **`$`** — type="object"; required=["type"]
      - **`type`** — type="string"; enum=["client","app"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path certname` | `#/paths/~1cloud~1certificates~1{certname}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1certificates~1{certname}/delete/requestBody/content/application~1json/schema` |
| Starfish | `$["type"]` | `#/paths/~1cloud~1certificates~1{certname}/delete/requestBody/content/application~1json/schema/properties/type` |
| Vulcan | `path certname` | `#/paths/~1cloud~1certificates~1{certname}/parameters/0` |
| Vulcan | `$` | `#/components/schemas/DelCertificateRequest` |
| Vulcan | `$["type"]` | `#/components/schemas/DelCertificateRequest/properties/type` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewers confirm JSON-body support; both current schemas place type in the body with matching enum.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path certname. Request: type="object"; required=["type"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: path certname. Request: type="object"; required=["type"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current Vulcan JSON type contract; no further location edit is needed for this finding.

#### 6. Required action

Close the obsolete body-versus-query discrepancy. Starfish body-certname compatibility remains separate in F-008A.

#### 7. Developer question

None for the current type-location finding.

Illustrative JSON shape for discussion; not a newly captured reader response:

```json
{"type":"client"}
```

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: 9600 supports both "certname" as the path parameter and as a body parameter
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Why: the command accepts json body in both readers..may need schema update
> Needed: may need schema updation .

Source limitation: Starfish comment discusses certname, not type. Retain that distinction; do not infer optional or required fields.

</details>

**Record decision:** tracker Issue ID `F-008`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-008a"></a>
### F-008A — DELETE — Starfish certificate-name body compatibility

**Developer Clarification · Open · Platform: Starfish**  
Source: `Field review` source row 8 (Excel row 14); original endpoint `DELETE /cloud/certificates/{certname}`.

#### 1. Issue

Starfish reviewer says certname works in path and body, but its schema declares only path certname. This does not establish type optionality or query support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `DELETE /cloud/certificates/{certname}`
  - Parameters
    - **path `certname`** — {"type": "string"}; required=true
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["type"]
      - **REVIEW `type`** — type="string"; enum=["client","app"]

**Vulcan — FXR60 / FXR90**

- `DELETE /cloud/certificates/{certname}`
  - Parameters
    - **path `certname`** — {"type": "string"}; required=true
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["type"]
      - **REVIEW `type`** — type="string"; enum=["client","app"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path certname` | `#/paths/~1cloud~1certificates~1{certname}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1certificates~1{certname}/delete/requestBody/content/application~1json/schema` |
| Starfish | `$["type"]` | `#/paths/~1cloud~1certificates~1{certname}/delete/requestBody/content/application~1json/schema/properties/type` |
| Vulcan | `path certname` | `#/paths/~1cloud~1certificates~1{certname}/parameters/0` |
| Vulcan | `$` | `#/components/schemas/DelCertificateRequest` |
| Vulcan | `$["type"]` | `#/components/schemas/DelCertificateRequest/properties/type` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path certname. Request: type="object"; required=["type"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: path certname. Request: type="object"; required=["type"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Describe the actually supported Starfish name-routing alternatives without changing the confirmed body type requirement.

#### 6. Required action

Obtain requests covering path name, body name and conflicting names. Decide whether to document a supported alias or retain path-only usage.

#### 7. Developer question

On FX9600, at which exact URL is body certname accepted? Is path certname still required, and which name wins when they conflict? Keep this separate from body type.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: 9600 supports both "certname" as the path parameter and as a body parameter
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Why: the command accepts json body in both readers..may need schema update
> Needed: may need schema updation .

Source limitation: Starfish comment discusses certname, not type. Retain that distinction; do not infer optional or required fields.

</details>

**Record decision:** tracker Issue ID `F-008A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-12"></a>
## /cloud/cloudConfig

<a id="f-013"></a>
### F-013 — PUT — Starfish firmware parity statement needs a field list

**Firmware Change · Open · Platform: Starfish**  
Source: `Field review` source row 13 (Excel row 19); original endpoint `PUT /cloud/cloudConfig`.

#### 1. Issue

Starfish explicitly states "FX9600 requires firmware changes to match FX90". That confirms a general firmware need, but does not identify any approved field-level change. dataAck is a visible candidate only.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/cloudConfig`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - **REVIEW `endpointConfig`** — type="object"
        - `data` — type="object"
          - `event` — type="object"
            - `connections` — type="array"
              - `[]` — type="object"
                - **REVIEW `additionalOptions`** — type="object"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/cloudConfig`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["endpointConfig"]
      - **REVIEW `endpointConfig`** — type="object"
        - `data` — type="object"
          - `event` — type="object"
            - `connections` — type="array"
              - `[]` — type="object"
                - **REVIEW `additionalOptions`** — type="object"
                  - **REVIEW `dataAck`** — type="object"
                    - **REVIEW `enable`** — type="boolean"
                    - **REVIEW `responseTopic`** — type="string"
                    - **REVIEW `correlationFieldName`** — type="string"

> **Confirmed review area:** `endpointConfig` on FX9600. **Unconfirmed candidate:** `data.event.connections[*].additionalOptions.dataAck`. The comment does not establish a firmware modification at this leaf.

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["endpointConfig"]` | `#/components/schemas/importCloudConfigReq/properties/endpointConfig` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["additionalOptions"]` | `#/components/schemas/event.v1/properties/connections/items/properties/additionalOptions` |
| Vulcan | `$["endpointConfig"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["additionalOptions"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/additionalOptions` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["additionalOptions"]["dataAck"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/additionalOptions/properties/dataAck` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["additionalOptions"]["dataAck"]["enable"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/additionalOptions/properties/dataAck/properties/enable` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["additionalOptions"]["dataAck"]["responseTopic"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/additionalOptions/properties/dataAck/properties/responseTopic` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["additionalOptions"]["dataAck"]["correlationFieldName"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/additionalOptions/properties/dataAck/properties/correlationFieldName` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Explicit Starfish field-review row 13 firmware statement. Endpoint-level need is confirmed in source; exact affected leaf nodes, expected behavior and release remain unconfirmed.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [text/html, application/json]: default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object"; required=["endpointConfig"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Firmware scope must be defined by the Starfish owner. No specific dataAck or enableLocalRest implementation change is approved by the available comment.

#### 6. Required action

Obtain a field-by-field gap list, current/expected runtime behavior, implementation ticket and target version. Highlight endpointConfig as the confirmed review area; keep candidate leaf nodes unconfirmed.

#### 7. Developer question

Which exact endpointConfig nodes require FX9600 code changes? Is data.event.connections[].additionalOptions.dataAck one of them, and what acknowledgement behavior, protocol support and release are required?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: FX9600 requires firmware changes to match FX90
>
> Kamali:
> Why: All fields supported in starfish are supported by vulcan as well…fields like dataAck has been added in vulcan for end to end data acknowledgment.enableLocalRest is not needed .needs to be removed from schema. dataAck field needed.other fields should be same
> Needed: needs schema update for missing fields.

Source limitation: Comments concern opposite directions of compatibility; do not assume complete parity or a direct contradiction without a field-level list.

</details>

**Record decision:** tracker Issue ID `F-013`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-013a"></a>
### F-013A — PUT — Remove unsupported enableLocalRest from Vulcan schema

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 13 (Excel row 19); original endpoint `PUT /cloud/cloudConfig`.

#### 1. Issue

Vulcan cloudConfig exposes enableLocalRest under both control.commandResponse and management.commandResponse. Kamali explicitly says it is not needed and must be removed. The operation narrative says local REST is always enabled and not configurable, contradicting the exposed property.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/cloudConfig`
  - Parameters: none declared
  - JSON request body
    - **Target field absent from declared properties**. Absence alone does not prove firmware rejection.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/cloudConfig`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["endpointConfig"]
      - `endpointConfig` — type="object"
        - `control` — type="object"
          - `commandResponse` — type="object"
            - **`enableLocalRest`** — type="boolean"
        - `management` — type="object"
          - `commandResponse` — type="object"
            - **`enableLocalRest`** — type="boolean"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `No target field declared` | `#/paths/~1cloud~1cloudConfig/put` |
| Vulcan | `$["endpointConfig"]["control"]["commandResponse"]["enableLocalRest"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/control/properties/commandResponse/properties/enableLocalRest` |
| Vulcan | `$["endpointConfig"]["management"]["commandResponse"]["enableLocalRest"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/management/properties/commandResponse/properties/enableLocalRest` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [text/html, application/json]: default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object"; required=["endpointConfig"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Remove these schema claims while retaining the existing local REST functionality and dataAck.

#### 6. Required action

Delete the two enableLocalRest properties from SetImportcloudconfigRequest and related examples. Do not disable REST in firmware.

#### 7. Developer question

Does removal apply to both commandResponse locations shown here, and how does firmware handle a supplied legacy enableLocalRest key?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: FX9600 requires firmware changes to match FX90
>
> Kamali:
> Why: All fields supported in starfish are supported by vulcan as well…fields like dataAck has been added in vulcan for end to end data acknowledgment.enableLocalRest is not needed .needs to be removed from schema. dataAck field needed.other fields should be same
> Needed: needs schema update for missing fields.

Source limitation: Comments concern opposite directions of compatibility; do not assume complete parity or a direct contradiction without a field-level list.

</details>

**Record decision:** tracker Issue ID `F-013A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-013b"></a>
### F-013B — PUT — Missing supported connection option fields

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 13 (Excel row 19); original endpoint `PUT /cloud/cloudConfig`.

#### 1. Issue

Starfish uses protocol-specific options branches including basicAuthentication, additional.retain and AWS additional.alpnProtocolNames. Vulcan has a flatter options schema that omits those named fields. Kamali says Starfish-supported fields are already supported on Vulcan.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/cloudConfig`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - `endpointConfig` — type="object"
        - `data` — type="object"
          - `event` — type="object"
            - `connections` — type="array"
              - `[]` — type="object"
                - **`options`** — type/constraints not specified here
                  - **`/anyOf[0]`** — type="object"; required=["endpoint","enableSecurity","additional","publishTopic"]
                    - **`security`** — type="object"; required=["keyFormat","keyAlgorithm","CACertificateFileLocation","publicKeyFileLocation","privateKeyFileLocation","verifyHostName","verifyPeer","installedCertificateName","installedCertificateType"]
                    - **`basicAuthentication`** — type="object"; required=["username","password"]
                    - **`additional`** — type="object"; required=["keepAlive","cleanSession","debug","reconnectDelay","reconnectDelayMax","clientId","qos"]
                      - **`retain`** — type="boolean"; default=false
                  - **`/anyOf[6]`** — type="object"; required=["endpoint"]
                    - **`additional`** — type="object"
                      - **`retain`** — type="boolean"; default=false
                      - **`alpnProtocolNames`** — type="string"
                    - **`security`** — type="object"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/cloudConfig`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["endpointConfig"]
      - `endpointConfig` — type="object"
        - `data` — type="object"
          - `event` — type="object"
            - `connections` — type="array"
              - `[]` — type="object"
                - **`options`** — type="object"
                  - **`additional`** — type="object"
                  - **`security`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/event.v1/properties/connections/items/properties/options` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/mqtt.v1` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/mqttSecurity.v1` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["basicAuthentication"]` | `#/components/schemas/basicAuthentication.v1` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/mqttAdditionalOptions.v1` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["retain"]` | `#/components/schemas/mqttAdditionalOptions.v1/properties/retain` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/aws.v1` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/aws.v1/properties/additional` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["retain"]` | `#/components/schemas/aws.v1/properties/additional/properties/retain` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["alpnProtocolNames"]` | `#/components/schemas/aws.v1/properties/additional/properties/alpnProtocolNames` |
| Starfish | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/aws.v1/properties/security` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options/properties/additional` |
| Vulcan | `$["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/SetImportcloudconfigRequest/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options/properties/security` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [text/html, application/json]: default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object"; required=["endpointConfig"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Correct Vulcan schemas to expose the implemented protocol-specific options; preserve dataAck as an additional Vulcan feature.

#### 6. Required action

Add supported authentication/additional/security variants to the appropriate Vulcan connection schemas after confirming exact object nesting and protocol applicability. Do not copy all Starfish required flags blindly.

#### 7. Developer question

For MQTT, HTTP, TCP/IP, WebSocket, Azure and AWS connections, which Starfish option branches are accepted unchanged on FXR? Confirm basicAuthentication, additional.retain, additional.alpnProtocolNames and security requiredness.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: FX9600 requires firmware changes to match FX90
>
> Kamali:
> Why: All fields supported in starfish are supported by vulcan as well…fields like dataAck has been added in vulcan for end to end data acknowledgment.enableLocalRest is not needed .needs to be removed from schema. dataAck field needed.other fields should be same
> Needed: needs schema update for missing fields.

Source limitation: Comments concern opposite directions of compatibility; do not assume complete parity or a direct contradiction without a field-level list.

</details>

**Record decision:** tracker Issue ID `F-013B`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-13"></a>
## /cloud/config

<a id="f-014"></a>
### F-014 — GET — Config xml field is already present

**No Change Required · Resolved · Platform: Vulcan**  
Source: `Field review` source row 14 (Excel row 20); original endpoint `GET /cloud/config`.

#### 1. Issue

Both current GET /cloud/config schemas include top-level xml:string. The earlier missing-xml comment is stale.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/config`
  - Success response body
    - `$` — type="object"
      - **`xml`** — type="string"

**Vulcan — FXR60 / FXR90**

- `GET /cloud/config`
  - Success response body
    - `$` — type="object"
      - **`xml`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["xml"]` | `#/components/schemas/readerConfigResponse/properties/xml` |
| Vulcan | `$["xml"]` | `#/components/schemas/GetConfigResponse/properties/xml` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Kamali says xml is supported; both current schema properties contain xml:string.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the present xml field; no duplicate addition is needed.

#### 6. Required action

Close the xml-presence subfinding only. Other config branches remain open below.

#### 7. Developer question

None for xml presence.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: All fields supported in starfish are supported by vulcan as well…fields like dataAck has been added in vulcan for end to end data acknowledgment.enableLocalRest is not needed .needs to be removed from schema. dataAck field needed.other fields should be same
> Needed: needs schema update for fields like xml,GPIO LED ,enableLocalRest

Source limitation: Starfish’s no-change comment is not evidence that the FXR schema is complete.

</details>

**Record decision:** tracker Issue ID `F-014`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-014a"></a>
### F-014A — GET — GPIO debounce missing from Vulcan config

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 14 (Excel row 20); original endpoint `GET /cloud/config`.

#### 1. Issue

Starfish GPIO-LED includes GPIDebounce for ports 1–4 with number, minimum 0 and default 50. Vulcan’s shared GPIOLEDConfig.v1 has action mappings and defaults but no GPIDebounce.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/config`
  - Success response body
    - `$` — type="object"
      - **`GPIO-LED`** — type="object"
        - **`GPIDebounce`** — type="object"
          - **`1`** — type="number"; minimum=0; default=50
          - **`2`** — type="number"; minimum=0; default=50
          - **`3`** — type="number"; minimum=0; default=50
          - **`4`** — type="number"; minimum=0; default=50

**Vulcan — FXR60 / FXR90**

- `GET /cloud/config`
  - Success response body
    - `$` — type="object"
      - **`GPIO-LED`** — type/constraints not specified here
        - **`/anyOf[1]`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["GPIO-LED"]` | `#/components/schemas/readerConfigResponse/properties/GPIO-LED` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]` | `#/components/schemas/readerConfigResponse/properties/GPIO-LED/properties/GPIDebounce` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["1"]` | `#/components/schemas/readerConfigResponse/properties/GPIO-LED/properties/GPIDebounce/properties/1` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["2"]` | `#/components/schemas/readerConfigResponse/properties/GPIO-LED/properties/GPIDebounce/properties/2` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["3"]` | `#/components/schemas/readerConfigResponse/properties/GPIO-LED/properties/GPIDebounce/properties/3` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["4"]` | `#/components/schemas/readerConfigResponse/properties/GPIO-LED/properties/GPIDebounce/properties/4` |
| Vulcan | `$["GPIO-LED"]` | `#/components/schemas/GetConfigResponse/properties/GPIO-LED` |
| Vulcan | `$["GPIO-LED"]` | `#/components/schemas/GPIOLEDConfig.v1` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Represent existing supported debounce configuration on FXR per Kamali’s all-fields-supported statement; confirm exact numeric range and model applicability.

#### 6. Required action

Add the confirmed GPIDebounce branch to GPIOLEDConfig.v1 and verify both GET and PUT references. Do not add already-present GPIO action mappings again.

#### 7. Developer question

Does FXR60 and FXR90 use GPIO-LED.GPIDebounce keys 1–4 in milliseconds with minimum 0/default 50? Are values integers, and what is the maximum?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: All fields supported in starfish are supported by vulcan as well…fields like dataAck has been added in vulcan for end to end data acknowledgment.enableLocalRest is not needed .needs to be removed from schema. dataAck field needed.other fields should be same
> Needed: needs schema update for fields like xml,GPIO LED ,enableLocalRest

Source limitation: Starfish’s no-change comment is not evidence that the FXR schema is complete.

</details>

**Record decision:** tracker Issue ID `F-014A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-014b"></a>
### F-014B — GET — Config response enableLocalRest

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 14 (Excel row 20); original endpoint `GET /cloud/config`.

#### 1. Issue

GET config still defines READER-GATEWAY.endpointConfig.management.commandResponse.enableLocalRest. Kamali says to remove enableLocalRest and retain dataAck.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/config`
  - Success response body
    - **Target field absent from declared properties**. Absence alone does not prove firmware rejection.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/config`
  - Success response body
    - `$` — type="object"
      - `READER-GATEWAY` — type="object"
        - `endpointConfig` — type="object"
          - `data` — type="object"
            - `event` — type="object"
              - `connections` — type="array"
                - `[]` — type="object"
                  - `additionalOptions` — type="object"
                    - **`dataAck`** — type="object"
          - `management` — type="object"
            - `commandResponse` — type="object"
              - **`enableLocalRest`** — type="boolean"
            - `event` — type="object"
              - `connections` — type="array"
                - `[]` — type="object"
                  - `additionalOptions` — type="object"
                    - **`dataAck`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `No target field declared` | `#/paths/~1cloud~1config/get` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["additionalOptions"]["dataAck"]` | `#/components/schemas/GetConfigResponse/properties/READER-GATEWAY/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/additionalOptions/properties/dataAck` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["management"]["commandResponse"]["enableLocalRest"]` | `#/components/schemas/GetConfigResponse/properties/READER-GATEWAY/properties/endpointConfig/properties/management/properties/commandResponse/properties/enableLocalRest` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["management"]["event"]["connections"][*]["additionalOptions"]["dataAck"]` | `#/components/schemas/GetConfigResponse/properties/READER-GATEWAY/properties/endpointConfig/properties/management/properties/event/properties/connections/items/properties/additionalOptions/properties/dataAck` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Remove the unsupported enableLocalRest response claim; keep the supported dataAck branches.

#### 6. Required action

Remove that property from GetConfigResponse and align its example. Coordinate with F-013A for the setter schema.

#### 7. Developer question

Is enableLocalRest absent from actual GET /cloud/config responses on all target FXR releases? Identify any legacy release that still returns it.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: All fields supported in starfish are supported by vulcan as well…fields like dataAck has been added in vulcan for end to end data acknowledgment.enableLocalRest is not needed .needs to be removed from schema. dataAck field needed.other fields should be same
> Needed: needs schema update for fields like xml,GPIO LED ,enableLocalRest

Source limitation: Starfish’s no-change comment is not evidence that the FXR schema is complete.

</details>

**Record decision:** tracker Issue ID `F-014B`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-014c"></a>
### F-014C — GET — Config response connection options

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 14 (Excel row 20); original endpoint `GET /cloud/config`.

#### 1. Issue

GET config repeats Starfish’s protocol-specific connection options under READER-GATEWAY. Vulcan’s response lacks several named authentication/additional option fields, although Kamali reports support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/config`
  - Success response body
    - `$` — type="object"
      - `READER-GATEWAY` — type="object"
        - `endpointConfig` — type="object"
          - `data` — type="object"
            - `event` — type="object"
              - `connections` — type="array"
                - `[]` — type="object"
                  - **`options`** — type/constraints not specified here
                    - **`/anyOf[0]`** — type="object"; required=["endpoint","enableSecurity","additional","publishTopic"]
                      - **`security`** — type="object"; required=["keyFormat","keyAlgorithm","CACertificateFileLocation","publicKeyFileLocation","privateKeyFileLocation","verifyHostName","verifyPeer","installedCertificateName","installedCertificateType"]
                      - **`basicAuthentication`** — type="object"; required=["username","password"]
                      - **`additional`** — type="object"; required=["keepAlive","cleanSession","debug","reconnectDelay","reconnectDelayMax","clientId","qos"]
                        - **`retain`** — type="boolean"; default=false
                    - **`/anyOf[6]`** — type="object"; required=["endpoint"]
                      - **`additional`** — type="object"
                        - **`retain`** — type="boolean"; default=false
                        - **`alpnProtocolNames`** — type="string"
                      - **`security`** — type="object"

**Vulcan — FXR60 / FXR90**

- `GET /cloud/config`
  - Success response body
    - `$` — type="object"
      - `READER-GATEWAY` — type="object"
        - `endpointConfig` — type="object"
          - `data` — type="object"
            - `event` — type="object"
              - `connections` — type="array"
                - `[]` — type="object"
                  - **`options`** — type="object"
                    - **`additional`** — type="object"
                    - **`security`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/event.v1/properties/connections/items/properties/options` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/mqtt.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/mqttSecurity.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["basicAuthentication"]` | `#/components/schemas/basicAuthentication.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/mqttAdditionalOptions.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["retain"]` | `#/components/schemas/mqttAdditionalOptions.v1/properties/retain` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/aws.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/aws.v1/properties/additional` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["retain"]` | `#/components/schemas/aws.v1/properties/additional/properties/retain` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["alpnProtocolNames"]` | `#/components/schemas/aws.v1/properties/additional/properties/alpnProtocolNames` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/aws.v1/properties/security` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/GetConfigResponse/properties/READER-GATEWAY/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/GetConfigResponse/properties/READER-GATEWAY/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options/properties/additional` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/GetConfigResponse/properties/READER-GATEWAY/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options/properties/security` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Expose the actual returned protocol-specific settings consistently with cloudConfig.

#### 6. Required action

Align GetConfigResponse connection options with the confirmed setting schemas; verify redaction of password/certificate content independently.

#### 7. Developer question

Which basicAuthentication, retain, ALPN and security values are returned, omitted or redacted by FXR GET /cloud/config? Do the data/control/management channels use the same shapes?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: All fields supported in starfish are supported by vulcan as well…fields like dataAck has been added in vulcan for end to end data acknowledgment.enableLocalRest is not needed .needs to be removed from schema. dataAck field needed.other fields should be same
> Needed: needs schema update for fields like xml,GPIO LED ,enableLocalRest

Source limitation: Starfish’s no-change comment is not evidence that the FXR schema is complete.

</details>

**Record decision:** tracker Issue ID `F-014C`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-015"></a>
### F-015 — PUT — Config request xml and claimed GPI 3/4 events

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 15 (Excel row 21); original endpoint `PUT /cloud/config`.

#### 1. Issue

Both current PUT schemas contain xml. Neither GPIO-LED action map defines GPI_3_H/L or GPI_4_H/L in this snapshot. Four physical GPI pins do not establish support for those event names.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/config`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - **REVIEW `xml`** — type="string"
      - **REVIEW `GPIO-LED`** — type="object"
        - **REVIEW `GPI_1_H`** — type="array"
        - **REVIEW `GPI_1_L`** — type="array"
        - **REVIEW `GPI_2_H`** — type="array"
        - **REVIEW `GPI_2_L`** — type="array"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/config`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - **REVIEW `xml`** — type="string"
      - **REVIEW `GPIO-LED`** — type="object"
        - **REVIEW `GPI_1_H`** — type="array"
        - **REVIEW `GPI_1_L`** — type="array"
        - **REVIEW `GPI_2_H`** — type="array"
        - **REVIEW `GPI_2_L`** — type="array"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["xml"]` | `#/components/schemas/readerConfig/properties/xml` |
| Starfish | `$["GPIO-LED"]` | `#/components/schemas/readerConfig/properties/GPIO-LED` |
| Starfish | `$["GPIO-LED"]["GPI_1_H"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPI_1_H` |
| Starfish | `$["GPIO-LED"]["GPI_1_L"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPI_1_L` |
| Starfish | `$["GPIO-LED"]["GPI_2_H"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPI_2_H` |
| Starfish | `$["GPIO-LED"]["GPI_2_L"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPI_2_L` |
| Vulcan | `$["xml"]` | `#/components/schemas/SetConfigMqttRequest/properties/xml` |
| Vulcan | `$["GPIO-LED"]` | `#/components/schemas/GPIOLEDConfig.v1` |
| Vulcan | `$["GPIO-LED"]["GPI_1_H"]` | `#/components/schemas/gpoledAction.v1` |
| Vulcan | `$["GPIO-LED"]["GPI_1_L"]` | `#/components/schemas/gpoledAction.v1` |
| Vulcan | `$["GPIO-LED"]["GPI_2_H"]` | `#/components/schemas/gpoledAction.v1` |
| Vulcan | `$["GPIO-LED"]["GPI_2_L"]` | `#/components/schemas/gpoledAction.v1` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [text/html]: default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain xml. Establish the exact supported trigger keys per model before adding GPI 3/4 mappings.

#### 6. Required action

Ask for accepted configuration payloads for GPI 3/4 triggers. If supported, update schemas. If absent and required, define a firmware feature; otherwise document the restriction.

#### 7. Developer question

Are GPIO-LED.GPI_3_H, GPI_3_L, GPI_4_H and GPI_4_L accepted on FX9600, FXR60 and FXR90? The source’s FXR-only claim is not reflected in either current action map.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: All fields supported in starfish are supported in vulcan..may need schema update.
> Needed: may need schema update

Source limitation: The reviews say FX9600 has four GPI ports; port count alone does not confirm each event name.

</details>

**Record decision:** tracker Issue ID `F-015`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-015a"></a>
### F-015A — PUT — GPIO debounce missing from Vulcan config

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 15 (Excel row 21); original endpoint `PUT /cloud/config`.

#### 1. Issue

Starfish GPIO-LED includes GPIDebounce for ports 1–4 with number, minimum 0 and default 50. Vulcan’s shared GPIOLEDConfig.v1 has action mappings and defaults but no GPIDebounce.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/config`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - **`GPIO-LED`** — type="object"
        - **`GPIDebounce`** — type="object"
          - **`1`** — type="number"; minimum=0; default=50
          - **`2`** — type="number"; minimum=0; default=50
          - **`3`** — type="number"; minimum=0; default=50
          - **`4`** — type="number"; minimum=0; default=50

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/config`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - **`GPIO-LED`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["GPIO-LED"]` | `#/components/schemas/readerConfig/properties/GPIO-LED` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPIDebounce` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["1"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPIDebounce/properties/1` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["2"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPIDebounce/properties/2` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["3"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPIDebounce/properties/3` |
| Starfish | `$["GPIO-LED"]["GPIDebounce"]["4"]` | `#/components/schemas/readerConfig/properties/GPIO-LED/properties/GPIDebounce/properties/4` |
| Vulcan | `$["GPIO-LED"]` | `#/components/schemas/GPIOLEDConfig.v1` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [text/html]: default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Represent existing supported debounce configuration on FXR per Kamali’s all-fields-supported statement; confirm exact numeric range and model applicability.

#### 6. Required action

Add the confirmed GPIDebounce branch to GPIOLEDConfig.v1 and verify both GET and PUT references. Do not add already-present GPIO action mappings again.

#### 7. Developer question

Does FXR60 and FXR90 use GPIO-LED.GPIDebounce keys 1–4 in milliseconds with minimum 0/default 50? Are values integers, and what is the maximum?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: All fields supported in starfish are supported in vulcan..may need schema update.
> Needed: may need schema update

Source limitation: The reviews say FX9600 has four GPI ports; port count alone does not confirm each event name.

</details>

**Record decision:** tracker Issue ID `F-015A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-015b"></a>
### F-015B — PUT — Config request connection options

**Schema Update · Open · Platform: Vulcan**  
Source: `Field review` source row 15 (Excel row 21); original endpoint `PUT /cloud/config`.

#### 1. Issue

PUT config embeds the cloud connection contract under READER-GATEWAY. Vulcan omits named protocol-specific Starfish options despite Kamali reporting support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/config`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - `READER-GATEWAY` — type="object"
        - `endpointConfig` — type="object"
          - `data` — type="object"
            - `event` — type="object"
              - `connections` — type="array"
                - `[]` — type="object"
                  - **`options`** — type/constraints not specified here
                    - **`/anyOf[0]`** — type="object"; required=["endpoint","enableSecurity","additional","publishTopic"]
                      - **`security`** — type="object"; required=["keyFormat","keyAlgorithm","CACertificateFileLocation","publicKeyFileLocation","privateKeyFileLocation","verifyHostName","verifyPeer","installedCertificateName","installedCertificateType"]
                      - **`basicAuthentication`** — type="object"; required=["username","password"]
                      - **`additional`** — type="object"; required=["keepAlive","cleanSession","debug","reconnectDelay","reconnectDelayMax","clientId","qos"]
                        - **`retain`** — type="boolean"; default=false
                    - **`/anyOf[6]`** — type="object"; required=["endpoint"]
                      - **`additional`** — type="object"
                        - **`retain`** — type="boolean"; default=false
                        - **`alpnProtocolNames`** — type="string"
                      - **`security`** — type="object"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/config`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - `READER-GATEWAY` — type="object"
        - `endpointConfig` — type="object"
          - `data` — type="object"
            - `event` — type="object"
              - `connections` — type="array"
                - `[]` — type="object"
                  - **`options`** — type="object"
                    - **`additional`** — type="object"
                    - **`security`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/event.v1/properties/connections/items/properties/options` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/mqtt.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/mqttSecurity.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["basicAuthentication"]` | `#/components/schemas/basicAuthentication.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/mqttAdditionalOptions.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["retain"]` | `#/components/schemas/mqttAdditionalOptions.v1/properties/retain` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/aws.v1` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/aws.v1/properties/additional` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["retain"]` | `#/components/schemas/aws.v1/properties/additional/properties/retain` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]["alpnProtocolNames"]` | `#/components/schemas/aws.v1/properties/additional/properties/alpnProtocolNames` |
| Starfish | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/aws.v1/properties/security` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]` | `#/components/schemas/SetConfigMqttRequest/properties/READER-GATEWAY/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["additional"]` | `#/components/schemas/SetConfigMqttRequest/properties/READER-GATEWAY/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options/properties/additional` |
| Vulcan | `$["READER-GATEWAY"]["endpointConfig"]["data"]["event"]["connections"][*]["options"]["security"]` | `#/components/schemas/SetConfigMqttRequest/properties/READER-GATEWAY/properties/endpointConfig/properties/data/properties/event/properties/connections/items/properties/options/properties/security` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [text/html]: default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use the implemented connection option shapes across import/config surfaces.

#### 6. Required action

Add the confirmed supported options to SetConfigMqttRequest as used by this REST endpoint. A schema name containing Mqtt does not by itself prove a firmware difference.

#### 7. Developer question

Are all Starfish MQTT/HTTP/TCPIP/WebSocket/Azure/AWS option branches supported inside FXR PUT /cloud/config, with the same nesting and requiredness as cloudConfig?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: yes
> Needed: No changes required
>
> Kamali:
> Why: All fields supported in starfish are supported in vulcan..may need schema update.
> Needed: may need schema update

Source limitation: The reviews say FX9600 has four GPI ports; port count alone does not confirm each event name.

</details>

**Record decision:** tracker Issue ID `F-015B`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-14"></a>
## /cloud/eSimConfig

<a id="e-006"></a>
### E-006 — GET — eSIM model applicability

**Developer Clarification · Open · Platform: Vulcan**  
Source: `Endpoint review` source row 6 (Excel row 12); original endpoint `GET /cloud/eSimConfig`.

#### 1. Issue

Both reviewers describe FXR60/FXR90 support, but the current Vulcan narrative explicitly says FXR90 only. Starfish has no eSIM operation.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/eSimConfig`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/eSimConfig`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"
      - **REVIEW `eid`** — type="string"
      - **REVIEW `imei`** — type="string"
      - **REVIEW `profiles`** — type="array"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1eSimConfig/get` |
| Vulcan | `$["eid"]` | `#/components/schemas/GetEsimConfigResponse/properties/eid` |
| Vulcan | `$["imei"]` | `#/components/schemas/GetEsimConfigResponse/properties/imei` |
| Vulcan | `$["profiles"]` | `#/components/schemas/GetEsimConfigResponse/properties/profiles` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Keep FX9600 excluded. Establish whether any FXR60 variant supports this eSIM operation.

#### 6. Required action

Resolve the model scope before changing Applies To or promising FXR60 support.

#### 7. Developer question

Does eSIM configuration apply exclusively to FXR90 as the current description states, or to a specific FXR60 SKU too? Identify the supported model and firmware.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr90/60

</details>

**Record decision:** tracker Issue ID `E-006`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-007"></a>
### E-007 — PUT — eSIM model applicability

**Developer Clarification · Open · Platform: Vulcan**  
Source: `Endpoint review` source row 7 (Excel row 13); original endpoint `PUT /cloud/eSimConfig`.

#### 1. Issue

Both reviewers describe FXR60/FXR90 support, but the current Vulcan narrative explicitly says FXR90 only. Starfish has no eSIM operation.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/eSimConfig`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/eSimConfig`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["operation","profileNickName"]
      - **REVIEW `operation`** — type="string"; enum=["add","delete","enable","disable"]
      - **REVIEW `profileNickName`** — type="string"
      - **REVIEW `activationID`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1eSimConfig/put` |
| Vulcan | `$["operation"]` | `#/components/schemas/SetEsimConfigRequest/properties/operation` |
| Vulcan | `$["profileNickName"]` | `#/components/schemas/SetEsimConfigRequest/properties/profileNickName` |
| Vulcan | `$["activationID"]` | `#/components/schemas/SetEsimConfigRequest/properties/activationID` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["operation","profileNickName"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Keep FX9600 excluded. Establish whether any FXR60 variant supports this eSIM operation.

#### 6. Required action

Resolve the model scope before changing Applies To or promising FXR60 support.

#### 7. Developer question

Does eSIM configuration apply exclusively to FXR90 as the current description states, or to a specific FXR60 SKU too? Identify the supported model and firmware.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr90/60

</details>

**Record decision:** tracker Issue ID `E-007`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-15"></a>
## /cloud/gpi

<a id="f-016"></a>
### F-016 — GET — Missing fourth Starfish GPI field

**Schema Update · Open · Platform: Starfish**  
Source: `Field review` source row 16 (Excel row 22); original endpoint `GET /cloud/gpi`.

#### 1. Issue

Starfish GET gpi defines keys 1,2,3 and requires only 1,2, while its description says FX9600 has four GPIs. Vulcan defines/requires 1–4. Both comments agree FX9600/FXR90 have four.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/gpi`
  - Success response body
    - **`$`** — type="object"; required=["1","2"]
      - **`1`** — type="string"; enum=["HIGH","LOW"]
      - **`2`** — type="string"; enum=["LOW","HIGH"]
      - **`3`** — type="string"; enum=["HIGH","LOW"]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/gpi`
  - Success response body
    - **`$`** — type="object"; required=["1","2","3","4"]
      - **`1`** — type="string"; enum=["HIGH","LOW"]
      - **`2`** — type="string"; enum=["HIGH","LOW"]
      - **`3`** — type="string"; enum=["HIGH","LOW"]
      - **`4`** — type="string"; enum=["HIGH","LOW"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/paths/~1cloud~1gpi/get/responses/200/content/application~1json/schema` |
| Starfish | `$["1"]` | `#/paths/~1cloud~1gpi/get/responses/200/content/application~1json/schema/properties/1` |
| Starfish | `$["2"]` | `#/paths/~1cloud~1gpi/get/responses/200/content/application~1json/schema/properties/2` |
| Starfish | `$["3"]` | `#/paths/~1cloud~1gpi/get/responses/200/content/application~1json/schema/properties/3` |
| Vulcan | `$` | `#/components/schemas/GetGPIStatusResponse` |
| Vulcan | `$["1"]` | `#/components/schemas/GetGPIStatusResponse/properties/1` |
| Vulcan | `$["2"]` | `#/components/schemas/GetGPIStatusResponse/properties/2` |
| Vulcan | `$["3"]` | `#/components/schemas/GetGPIStatusResponse/properties/3` |
| Vulcan | `$["4"]` | `#/components/schemas/GetGPIStatusResponse/properties/4` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["1","2"]. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["1","2","3","4"]. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

The FX9600-specific response must represent all four reported pins; do not apply FX7500’s two-pin model to FX9600.

#### 6. Required action

Add key 4 to the FX9600 response branch and use model-specific requiredness. Obtain the truncated remainder and confirm FXR60 port applicability before closing.

#### 7. Developer question

Provide complete Kamali row 16 and a GET gpi response for FX9600 and FXR60. Are all four keys always returned, and does pin 4 use HIGH/LOW?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: response is same FX90
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Why: due to hardware difference.fxr90 has 4 gpi pins,fx7500 has only 2.fx9600 has 4
> Needed: The difference is due to hardware .schema may need to be upd

Source limitation: Both comments align for FXR90/FX9600. FX7500’s two ports must not be applied to FX9600.

</details>

**Record decision:** tracker Issue ID `F-016`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-16"></a>
## /cloud/impinjGen2X

<a id="e-008"></a>
### E-008 — GET — Dedicated Gen2X endpoint support

**Developer Clarification · Open · Platform: Starfish**  
Source: `Endpoint review` source row 8 (Excel row 14); original endpoint `GET /cloud/impinjGen2X`.

#### 1. Issue

Starfish reports undocumented FX9600 support, while Kamali says FXR60/FXR90 only. The dedicated operation is absent from the Starfish specification.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/impinjGen2X`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/impinjGen2X`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"
      - **REVIEW `fastID`** — type="object"; required=["enabled"]
        - **REVIEW `enabled`** — type="boolean"
      - **REVIEW `tagProtect`** — type="object"; required=["action","password"]
      - **REVIEW `tagFocus`** — type="object"; required=["enabled"]
      - **REVIEW `tagQuieting`** — type="object"; additionalProperties=false

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1impinjGen2X/get` |
| Vulcan | `$["fastID"]` | `#/components/schemas/GetImpinjGen2XResponse/properties/fastID` |
| Vulcan | `$["fastID"]["enabled"]` | `#/components/schemas/GetImpinjGen2XResponse/properties/fastID/properties/enabled` |
| Vulcan | `$["tagProtect"]` | `#/components/schemas/GetImpinjGen2XResponse/properties/tagProtect` |
| Vulcan | `$["tagFocus"]` | `#/components/schemas/GetImpinjGen2XResponse/properties/tagFocus` |
| Vulcan | `$["tagQuieting"]` | `#/components/schemas/impinjTagQuieting.v1` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Establish GET /cloud/impinjGen2X support independently of applyImpinjGen2X in /start and impinjGen2X in /status.

#### 6. Required action

Request a firmware-versioned local REST example. Add schema if implemented; create a firmware requirement only if the missing dedicated operation is approved.

#### 7. Developer question

Does FX9600 implement GET /cloud/impinjGen2X on the target release? Supply the accepted request/returned object and status code, or confirm that only start/status Gen2X integration exists.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (undocumented)
>
> Kamali:
> supported only in fxr90/60

Source limitation: Gen2X fields in /start and /status do not establish support for this endpoint.

</details>

**Record decision:** tracker Issue ID `E-008`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-009"></a>
### E-009 — PUT — Dedicated Gen2X endpoint support

**Developer Clarification · Open · Platform: Starfish**  
Source: `Endpoint review` source row 9 (Excel row 15); original endpoint `PUT /cloud/impinjGen2X`.

#### 1. Issue

Starfish reports undocumented FX9600 support, while Kamali says FXR60/FXR90 only. The dedicated operation is absent from the Starfish specification.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/impinjGen2X`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/impinjGen2X`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - **REVIEW `fastID`** — type="object"; required=["enabled"]
        - **REVIEW `enabled`** — type="boolean"
      - **REVIEW `tagProtect`** — type/constraints not specified here
      - **REVIEW `tagFocus`** — type="object"; required=["enabled"]
      - **REVIEW `tagQuieting`** — type="object"; additionalProperties=false

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1impinjGen2X/put` |
| Vulcan | `$["fastID"]` | `#/components/schemas/SetImpinjGen2XRequest/properties/fastID` |
| Vulcan | `$["fastID"]["enabled"]` | `#/components/schemas/SetImpinjGen2XRequest/properties/fastID/properties/enabled` |
| Vulcan | `$["tagProtect"]` | `#/components/schemas/SetImpinjGen2XRequest/properties/tagProtect` |
| Vulcan | `$["tagFocus"]` | `#/components/schemas/SetImpinjGen2XRequest/properties/tagFocus` |
| Vulcan | `$["tagQuieting"]` | `#/components/schemas/impinjTagQuieting.v1` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object"; required=["message"]. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Establish PUT /cloud/impinjGen2X support independently of applyImpinjGen2X in /start and impinjGen2X in /status.

#### 6. Required action

Request a firmware-versioned local REST example. Add schema if implemented; create a firmware requirement only if the missing dedicated operation is approved.

#### 7. Developer question

Does FX9600 implement PUT /cloud/impinjGen2X on the target release? Supply the accepted request/returned object and status code, or confirm that only start/status Gen2X integration exists.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (undocumented)
>
> Kamali:
> supported only in fxr90/60

Source limitation: Gen2X fields in /start and /status do not establish support for this endpoint.

</details>

**Record decision:** tracker Issue ID `E-009`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-17"></a>
## /cloud/localRestLogin

<a id="e-010"></a>
### E-010 — GET — Missing Starfish login operation

**Schema Update · Open · Platform: Starfish**  
Source: `Endpoint review` source row 10 (Excel row 16); original endpoint `GET /cloud/localRestLogin`.

#### 1. Issue

Both reviewers confirm FX9600 local REST login, but Starfish paths omit GET /cloud/localRestLogin. Vulcan documents code and message.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/localRestLogin`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/localRestLogin`
  - Parameters: none declared
  - Success response body
    - **`$`** — type="object"
      - **`code`** — type="number"
      - **`message`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1localRestLogin/get` |
| Vulcan | `$` | `#/components/schemas/LocalrestloginResponse` |
| Vulcan | `$["code"]` | `#/components/schemas/LocalrestloginResponse/properties/code` |
| Vulcan | `$["message"]` | `#/components/schemas/LocalrestloginResponse/properties/message` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document the existing Starfish authentication operation using its actual response and authentication scheme.

#### 6. Required action

Add the missing Starfish path and narrative after obtaining its token response. Do not copy a live token or infer identical token semantics.

#### 7. Developer question

For the supported FX9600 firmware, does this GET use Basic authentication and return {code,message} as Vulcan does? Supply a redacted example and error responses.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (undocumented)
>
> Kamali:
> supported in fx9600 undocumented

</details>

**Record decision:** tracker Issue ID `E-010`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-18"></a>
## /cloud/logs/{logType}

<a id="e-014"></a>
### E-014 — DELETE — Concrete log URL is already represented

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 14 (Excel row 20); original endpoint `DELETE /cloud/logs/radioPacketLog`.

#### 1. Issue

Starfish represents this URL as /cloud/logs/{logType}; its enum includes radioPacketLog. Vulcan documents the concrete URL. Both reviewers report support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `DELETE /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `DELETE /cloud/logs/radioPacketLog`
  - Parameters: none declared
  - Request body: **not declared**

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Vulcan | `No target field declared` | `#/paths/~1cloud~1logs~1radioPacketLog/delete` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer statements confirm support; both operation definitions represent the concrete URL. This closure covers route presence only.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: no body schema. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain both documentation representations. They describe the same concrete route for this log type.

#### 6. Required action

No endpoint implementation change. In a unified index, link the concrete URL to the parameterized Starfish operation. Response-format and DELETE-enum questions are tracked in E-030 and E-029.

#### 7. Developer question

None for this route-support finding. See E-029/E-030 for unresolved contracts.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented)
>
> Kamali:
> supported in fx9600 undocumented

Source limitation: Reviewers disagree on documented versus undocumented, not on support.

</details>

**Record decision:** tracker Issue ID `E-014`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-016"></a>
### E-016 — DELETE — Concrete log URL is already represented

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 16 (Excel row 22); original endpoint `DELETE /cloud/logs/syslog`.

#### 1. Issue

Starfish represents this URL as /cloud/logs/{logType}; its enum includes syslog. Vulcan documents the concrete URL. Both reviewers report support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `DELETE /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `DELETE /cloud/logs/syslog`
  - Parameters: none declared
  - Request body: **not declared**

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Vulcan | `No target field declared` | `#/paths/~1cloud~1logs~1syslog/delete` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer statements confirm support; both operation definitions represent the concrete URL. This closure covers route presence only.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: no body schema. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain both documentation representations. They describe the same concrete route for this log type.

#### 6. Required action

No endpoint implementation change. In a unified index, link the concrete URL to the parameterized Starfish operation. Response-format and DELETE-enum questions are tracked in E-030 and E-029.

#### 7. Developer question

None for this route-support finding. See E-029/E-030 for unresolved contracts.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented)
>
> Kamali:
> supported in fx9600 undocumented

Source limitation: Reviewers disagree on documented versus undocumented, not on support.

</details>

**Record decision:** tracker Issue ID `E-016`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-029"></a>
### E-029 — DELETE — Log DELETE route and supported log types

**Developer Clarification · Open · Platform: Both**  
Source: `Endpoint review` source row 29 (Excel row 35); original endpoint `DELETE /cloud/logs/{logType}`.

#### 1. Issue

Starfish DELETE /cloud/logs/{logType} allows five enum values, but its reviewer identifies only syslog and radioPacketLog for DELETE. Vulcan explicitly defines DELETE for those two concrete URLs. The placeholder is not a literal command.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `DELETE /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `DELETE /cloud/logs/syslog`
  - Parameters: none declared
  - Request body: **not declared**

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Vulcan | `No target field declared` | `#/paths/~1cloud~1logs~1syslog/delete` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: no body schema. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Keep the two represented purge operations; establish whether RcLog, RgErrorLog and RgWarningLog are truly deletable.

#### 6. Required action

Correct the review assumption that a templated route is invalid. Ask Starfish to reconcile the DELETE enum; narrow it if documentation is wrong or define an approved firmware extension if required.

#### 7. Developer question

Do FX9600 DELETE requests for RcLog, RgErrorLog and RgWarningLog succeed on the target firmware? Should the enum be limited to syslog and radioPacketLog? For FXR, are any other concrete purge URLs supported?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Confirm if FXR supports this (undocumented), or firmware confirms FXR does not support it.
>
> Kamali:
> I don’t think this is a valid command

Source limitation: {logType} is a path placeholder. Starfish row 11 lists syslog and radioPacketLog for DELETE.

</details>

**Record decision:** tracker Issue ID `E-029`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-011"></a>
### E-011 — GET — Concrete log URL is already represented

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 11 (Excel row 17); original endpoint `GET /cloud/logs/RcLog`.

#### 1. Issue

Starfish represents this URL as /cloud/logs/{logType}; its enum includes RcLog. Vulcan documents the concrete URL. Both reviewers report support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Success response body
    - **`$`** — type/constraints not specified here

**Vulcan — FXR60 / FXR90**

- `GET /cloud/logs/RcLog`
  - Parameters: none declared
  - Success response body
    - **`$`** — type="object"
      - **`binary`** — type="string"
      - **`filename`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1logs~1{logType}/get/responses/200/content/application~1gzip/schema` |
| Vulcan | `$` | `#/components/schemas/GetRcLogResponse` |
| Vulcan | `$["binary"]` | `#/components/schemas/GetRcLogResponse/properties/binary` |
| Vulcan | `$["filename"]` | `#/components/schemas/GetRcLogResponse/properties/filename` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer statements confirm support; both operation definitions represent the concrete URL. This closure covers route presence only.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 [application/gzip]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain both documentation representations. They describe the same concrete route for this log type.

#### 6. Required action

No endpoint implementation change. In a unified index, link the concrete URL to the parameterized Starfish operation. Response-format and DELETE-enum questions are tracked in E-030 and E-029.

#### 7. Developer question

None for this route-support finding. See E-029/E-030 for unresolved contracts.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented) 9600 supports Delete /cloud/logs/{logType} for log types "syslog" "radioPacketLog" and Get /cloud/logs/{logType} for log types "syslog" "radioPacketLog" "RgErrorLog" "RgWarningLog" "RcLog"
>
> Kamali:
> supported in fx9600 undocumented

Source limitation: Reviewers disagree on documented versus undocumented, not on support.

</details>

**Record decision:** tracker Issue ID `E-011`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-012"></a>
### E-012 — GET — Concrete log URL is already represented

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 12 (Excel row 18); original endpoint `GET /cloud/logs/RgErrorLog`.

#### 1. Issue

Starfish represents this URL as /cloud/logs/{logType}; its enum includes RgErrorLog. Vulcan documents the concrete URL. Both reviewers report support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Success response body
    - **`$`** — type/constraints not specified here

**Vulcan — FXR60 / FXR90**

- `GET /cloud/logs/RgErrorLog`
  - Parameters: none declared
  - Success response body
    - **`$`** — type="object"
      - **`binary`** — type="string"
      - **`filename`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1logs~1{logType}/get/responses/200/content/application~1gzip/schema` |
| Vulcan | `$` | `#/components/schemas/GetRgErrorLogResponse` |
| Vulcan | `$["binary"]` | `#/components/schemas/GetRgErrorLogResponse/properties/binary` |
| Vulcan | `$["filename"]` | `#/components/schemas/GetRgErrorLogResponse/properties/filename` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer statements confirm support; both operation definitions represent the concrete URL. This closure covers route presence only.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 [application/gzip]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain both documentation representations. They describe the same concrete route for this log type.

#### 6. Required action

No endpoint implementation change. In a unified index, link the concrete URL to the parameterized Starfish operation. Response-format and DELETE-enum questions are tracked in E-030 and E-029.

#### 7. Developer question

None for this route-support finding. See E-029/E-030 for unresolved contracts.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented)
>
> Kamali:
> supported in fx9600 undocumented

Source limitation: Reviewers disagree on documented versus undocumented, not on support.

</details>

**Record decision:** tracker Issue ID `E-012`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-013"></a>
### E-013 — GET — Concrete log URL is already represented

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 13 (Excel row 19); original endpoint `GET /cloud/logs/RgWarningLog`.

#### 1. Issue

Starfish represents this URL as /cloud/logs/{logType}; its enum includes RgWarningLog. Vulcan documents the concrete URL. Both reviewers report support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Success response body
    - **`$`** — type/constraints not specified here

**Vulcan — FXR60 / FXR90**

- `GET /cloud/logs/RgWarningLog`
  - Parameters: none declared
  - Success response body
    - **`$`** — type="object"
      - **`binary`** — type="string"
      - **`filename`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1logs~1{logType}/get/responses/200/content/application~1gzip/schema` |
| Vulcan | `$` | `#/components/schemas/GetRgWarningLogResponse` |
| Vulcan | `$["binary"]` | `#/components/schemas/GetRgWarningLogResponse/properties/binary` |
| Vulcan | `$["filename"]` | `#/components/schemas/GetRgWarningLogResponse/properties/filename` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer statements confirm support; both operation definitions represent the concrete URL. This closure covers route presence only.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 [application/gzip]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain both documentation representations. They describe the same concrete route for this log type.

#### 6. Required action

No endpoint implementation change. In a unified index, link the concrete URL to the parameterized Starfish operation. Response-format and DELETE-enum questions are tracked in E-030 and E-029.

#### 7. Developer question

None for this route-support finding. See E-029/E-030 for unresolved contracts.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented)
>
> Kamali:
> supported in fx9600 undocumented

Source limitation: Reviewers disagree on documented versus undocumented, not on support.

</details>

**Record decision:** tracker Issue ID `E-013`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-015"></a>
### E-015 — GET — Concrete log URL is already represented

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 15 (Excel row 21); original endpoint `GET /cloud/logs/radioPacketLog`.

#### 1. Issue

Starfish represents this URL as /cloud/logs/{logType}; its enum includes radioPacketLog. Vulcan documents the concrete URL. Both reviewers report support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Success response body
    - **`$`** — type/constraints not specified here

**Vulcan — FXR60 / FXR90**

- `GET /cloud/logs/radioPacketLog`
  - Parameters: none declared
  - Success response body
    - **`$`** — type="object"
      - **`binary`** — type="string"
      - **`filename`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1logs~1{logType}/get/responses/200/content/application~1gzip/schema` |
| Vulcan | `$` | `#/components/schemas/GetRadioPacketLogResponse` |
| Vulcan | `$["binary"]` | `#/components/schemas/GetRadioPacketLogResponse/properties/binary` |
| Vulcan | `$["filename"]` | `#/components/schemas/GetRadioPacketLogResponse/properties/filename` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer statements confirm support; both operation definitions represent the concrete URL. This closure covers route presence only.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 [application/gzip]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain both documentation representations. They describe the same concrete route for this log type.

#### 6. Required action

No endpoint implementation change. In a unified index, link the concrete URL to the parameterized Starfish operation. Response-format and DELETE-enum questions are tracked in E-030 and E-029.

#### 7. Developer question

None for this route-support finding. See E-029/E-030 for unresolved contracts.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented)
>
> Kamali:
> supported in fx9600 undocumented

Source limitation: Reviewers disagree on documented versus undocumented, not on support.

</details>

**Record decision:** tracker Issue ID `E-015`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-017"></a>
### E-017 — GET — Concrete log URL is already represented

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 17 (Excel row 23); original endpoint `GET /cloud/logs/syslog`.

#### 1. Issue

Starfish represents this URL as /cloud/logs/{logType}; its enum includes syslog. Vulcan documents the concrete URL. Both reviewers report support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Success response body
    - **`$`** — type/constraints not specified here

**Vulcan — FXR60 / FXR90**

- `GET /cloud/logs/syslog`
  - Parameters: none declared
  - Success response body
    - **`$`** — type="object"
      - **`binary`** — type="string"
      - **`filename`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1logs~1{logType}/get/responses/200/content/application~1gzip/schema` |
| Vulcan | `$` | `#/components/schemas/GetLogsSyslogResponse` |
| Vulcan | `$["binary"]` | `#/components/schemas/GetLogsSyslogResponse/properties/binary` |
| Vulcan | `$["filename"]` | `#/components/schemas/GetLogsSyslogResponse/properties/filename` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer statements confirm support; both operation definitions represent the concrete URL. This closure covers route presence only.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 [application/gzip]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain both documentation representations. They describe the same concrete route for this log type.

#### 6. Required action

No endpoint implementation change. In a unified index, link the concrete URL to the parameterized Starfish operation. Response-format and DELETE-enum questions are tracked in E-030 and E-029.

#### 7. Developer question

None for this route-support finding. See E-029/E-030 for unresolved contracts.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented)
>
> Kamali:
> supported in fx9600 undocumented

Source limitation: Reviewers disagree on documented versus undocumented, not on support.

</details>

**Record decision:** tracker Issue ID `E-017`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-030"></a>
### E-030 — GET — Log retrieval response format and route mapping

**Developer Clarification · Open · Platform: Both**  
Source: `Endpoint review` source row 30 (Excel row 36); original endpoint `GET /cloud/logs/{logType}`.

#### 1. Issue

Starfish GET /cloud/logs/{logType} lists five values and application/gzip content with no explicit schema type. Vulcan has all five concrete GET URLs with JSON filename and Base64 binary. GET/PUT /cloud/logs configures logging and is a different route.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/logs/{logType}`
  - Parameters
    - **path `logType`** — {"type": "string", "enum": ["syslog", "radioPacketLog", "RgErrorLog", "RgWarningLog", "RcLog"]}; required=true
  - Success response body
    - **REVIEW `$`** — type/constraints not specified here

**Vulcan — FXR60 / FXR90**

- `GET /cloud/logs/RcLog`
  - Parameters: none declared
  - Success response body
    - **REVIEW `$`** — type="object"
      - **REVIEW `binary`** — type="string"
      - **REVIEW `filename`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `path logType` | `#/paths/~1cloud~1logs~1{logType}/parameters/0` |
| Starfish | `$` | `#/paths/~1cloud~1logs~1{logType}/get/responses/200/content/application~1gzip/schema` |
| Vulcan | `$` | `#/components/schemas/GetRcLogResponse` |
| Vulcan | `$["binary"]` | `#/components/schemas/GetRcLogResponse/properties/binary` |
| Vulcan | `$["filename"]` | `#/components/schemas/GetRcLogResponse/properties/filename` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: path logType. Request: none declared. Success: 200 [application/gzip]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document each platform’s actual response media type and wrapper; keep concrete and templated route representations equivalent where logType matches.

#### 6. Required action

Retain the existing routes. Capture response headers/body for the five types on FX9600 and FXR before unifying the download contract.

#### 7. Developer question

Does FX9600 return an HTTP binary attachment or a JSON object containing filename and Base64 binary for each listed logType? Are any response differences intentional?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Confirm if FXR supports this (undocumented), or firmware confirms FXR does not support it.
>
> Kamali:
> is this the same as /cloud/logs (we have GET and PUT for this) .supported by all platforms

Source limitation: A parameterized path describes concrete URLs; /cloud/logs is not automatically equivalent.

</details>

**Record decision:** tracker Issue ID `E-030`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-19"></a>
## /cloud/mode

<a id="f-017"></a>
### F-017 — GET — Mode model applicability and inventory protocol

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 17 (Excel row 23); original endpoint `GET /cloud/mode`.

#### 1. Issue

Starfish’s multi-model schema includes DIRECTIONALITY and beams; its beams description restricts them to ATR7000. Vulcan omits those but defines inventoryProtocol.mode GEN2X/GEN2/HYBRID. Both current READ branches use wordCount, not wordCounter.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/mode`
  - Success response body
    - `$` — type="object"; required=["type"]
      - **REVIEW `type`** — type="string"; enum=["SIMPLE","INVENTORY","PORTAL","CONVEYOR","CUSTOM","DIRECTIONALITY"]; default="SIMPLE"
      - `accesses` — type/constraints not specified here
        - `/oneOf[0]` — type="array"
          - `[]` — type/constraints not specified here
            - `/anyOf[0]` — type="object"; required=["type"]
              - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                - **REVIEW `wordCount`** — type="integer"
        - `/oneOf[1]` — type="array"
          - `[]` — type="array"
            - `[]` — type/constraints not specified here
              - `/anyOf[0]` — type="object"; required=["type"]
                - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                  - **REVIEW `wordCount`** — type="integer"
      - **REVIEW `beams`** — type="array"

**Vulcan — FXR60 / FXR90**

- `GET /cloud/mode`
  - Success response body
    - `$` — type="object"; required=["type"]
      - **REVIEW `type`** — type="string"; enum=["SIMPLE","INVENTORY","PORTAL","CONVEYOR","CUSTOM"]; default="SIMPLE"
      - **REVIEW `inventoryProtocol`** — type="object"; required=["mode"]
        - **REVIEW `mode`** — type="string"; enum=["GEN2X","GEN2","HYBRID"]
      - `accesses` — type/constraints not specified here
        - `/oneOf[0]` — type="array"
          - `[]` — type/constraints not specified here
            - `/anyOf[0]` — type="object"; required=["type"]
              - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                - **REVIEW `wordCount`** — type="integer"
        - `/oneOf[1]` — type="array"
          - `[]` — type="array"
            - `[]` — type/constraints not specified here
              - `/anyOf[0]` — type="object"; required=["type"]
                - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                  - **REVIEW `wordCount`** — type="integer"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["type"]` | `#/components/schemas/operatingMode.v1/properties/type` |
| Starfish | `$["accesses"][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |
| Starfish | `$["accesses"][*][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |
| Starfish | `$["beams"]` | `#/components/schemas/operatingMode.v1/properties/beams` |
| Vulcan | `$["type"]` | `#/components/schemas/operatingMode.v1/properties/type` |
| Vulcan | `$["inventoryProtocol"]` | `#/components/schemas/inventoryProtocol.v1` |
| Vulcan | `$["inventoryProtocol"]["mode"]` | `#/components/schemas/inventoryProtocol.v1/properties/mode` |
| Vulcan | `$["accesses"][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |
| Vulcan | `$["accesses"][*][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object"; required=["type"]. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object"; required=["type"]. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document ATR7000-only fields separately; establish FX9600 versus FXR inventoryProtocol support. Do not rename already-matching wordCount.

#### 6. Required action

Obtain missing Kamali review and method-specific samples. For PUT, the Starfish comment discusses responses and is not a request-support confirmation.

#### 7. Developer question

For GET responses, does FX9600 support inventoryProtocol, and is DIRECTIONALITY/beams excluded from FX9600 as the ATR7000 note implies? Is wordCount the wire name on both target releases?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: document is matching firmware
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Starfish’s firmware-match statement does not prove platform parity.

</details>

**Record decision:** tracker Issue ID `F-017`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-017a"></a>
### F-017A — GET — Mode tag metadata names and representation

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 17 (Excel row 23); original endpoint `GET /cloud/mode`.

#### 1. Issue

Starfish tagMetaData items combine type [string,object] with an enum of strings, including RESERVED, and object antennaPortNames. Vulcan uses oneOf string/object, omits RESERVED, adds READERLOCATION and antennaNames/gpsCoordinates. The Starfish enum may exclude its documented object form.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/mode`
  - Success response body
    - `$` — type="object"; required=["type"]
      - **REVIEW `tagMetaData`** — type="array"
        - **REVIEW `[]`** — type=["string","object"]; enum=["RSSI","PHASE","SEEN_COUNT","ANTENNA","CHANNEL","PC","XPC","CRC","EPC","TID","USER","RESERVED","MAC","HOSTNAME"]
          - **REVIEW `userDefined`** — type="string"
          - **REVIEW `antennaPortNames`** — type="array"

**Vulcan — FXR60 / FXR90**

- `GET /cloud/mode`
  - Success response body
    - `$` — type="object"; required=["type"]
      - **REVIEW `tagMetaData`** — type="array"
        - **REVIEW `[]`** — type/constraints not specified here
          - **REVIEW `/oneOf[0]`** — type="string"; enum=["RSSI","PHASE","SEEN_COUNT","ANTENNA","CHANNEL","PC","XPC","CRC","EPC","TID","USER","MAC","HOSTNAME","READERLOCATION"]
          - **REVIEW `/oneOf[1]`** — type="object"
            - **REVIEW `userDefined`** — type="string"
            - **REVIEW `antennaNames`** — type="array"
            - **REVIEW `gpsCoordinates`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["tagMetaData"]` | `#/components/schemas/tagMetaData.v1` |
| Starfish | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items` |
| Starfish | `$["tagMetaData"][*]["userDefined"]` | `#/components/schemas/tagMetaData.v1/items/properties/userDefined` |
| Starfish | `$["tagMetaData"][*]["antennaPortNames"]` | `#/components/schemas/tagMetaData.v1/items/properties/antennaPortNames` |
| Vulcan | `$["tagMetaData"]` | `#/components/schemas/tagMetaData.v1` |
| Vulcan | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items` |
| Vulcan | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items/oneOf/0` |
| Vulcan | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1` |
| Vulcan | `$["tagMetaData"][*]["userDefined"]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1/properties/userDefined` |
| Vulcan | `$["tagMetaData"][*]["antennaNames"]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1/properties/antennaNames` |
| Vulcan | `$["tagMetaData"][*]["gpsCoordinates"]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1/properties/gpsCoordinates` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object"; required=["type"]. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object"; required=["type"]. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use the exact accepted values and object keys for each platform, with a schema that permits the implemented object form.

#### 6. Required action

Verify metadata tokens and object inputs/outputs. Correct schema modeling if already supported; request firmware parity only for an approved missing capability.

#### 7. Developer question

Which tokens and object keys are accepted/returned on each model: RESERVED, READERLOCATION, antennaPortNames, antennaNames and gpsCoordinates? Does Starfish accept metadata objects despite its string enum? Supply a method-specific example.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: document is matching firmware
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Starfish’s firmware-match statement does not prove platform parity.

</details>

**Record decision:** tracker Issue ID `F-017A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-018"></a>
### F-018 — PUT — Mode model applicability and inventory protocol

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 18 (Excel row 24); original endpoint `PUT /cloud/mode`.

#### 1. Issue

Starfish’s multi-model schema includes DIRECTIONALITY and beams; its beams description restricts them to ATR7000. Vulcan omits those but defines inventoryProtocol.mode GEN2X/GEN2/HYBRID. Both current READ branches use wordCount, not wordCounter.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/mode`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["type"]
      - **REVIEW `type`** — type="string"; enum=["SIMPLE","INVENTORY","PORTAL","CONVEYOR","CUSTOM","DIRECTIONALITY"]; default="SIMPLE"
      - `accesses` — type/constraints not specified here
        - `/oneOf[0]` — type="array"
          - `[]` — type/constraints not specified here
            - `/anyOf[0]` — type="object"; required=["type"]
              - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                - **REVIEW `wordCount`** — type="integer"
        - `/oneOf[1]` — type="array"
          - `[]` — type="array"
            - `[]` — type/constraints not specified here
              - `/anyOf[0]` — type="object"; required=["type"]
                - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                  - **REVIEW `wordCount`** — type="integer"
      - **REVIEW `beams`** — type="array"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/mode`
  - Parameters: none declared
  - JSON request body
    - `$` — type/constraints not specified here
      - `/allOf[0]` — type="object"; required=["type"]
        - **REVIEW `type`** — type="string"; enum=["SIMPLE","INVENTORY","PORTAL","CONVEYOR","CUSTOM"]; default="SIMPLE"
        - **REVIEW `inventoryProtocol`** — type="object"; required=["mode"]
          - **REVIEW `mode`** — type="string"; enum=["GEN2X","GEN2","HYBRID"]
        - `accesses` — type/constraints not specified here
          - `/oneOf[0]` — type="array"
            - `[]` — type/constraints not specified here
              - `/anyOf[0]` — type="object"; required=["type"]
                - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                  - **REVIEW `wordCount`** — type="integer"
          - `/oneOf[1]` — type="array"
            - `[]` — type="array"
              - `[]` — type/constraints not specified here
                - `/anyOf[0]` — type="object"; required=["type"]
                  - `config` — type="object"; required=["membank","wordPointer","wordCount"]
                    - **REVIEW `wordCount`** — type="integer"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["type"]` | `#/components/schemas/operatingMode.v1/properties/type` |
| Starfish | `$["accesses"][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |
| Starfish | `$["accesses"][*][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |
| Starfish | `$["beams"]` | `#/components/schemas/operatingMode.v1/properties/beams` |
| Vulcan | `$["type"]` | `#/components/schemas/operatingMode.v1/properties/type` |
| Vulcan | `$["inventoryProtocol"]` | `#/components/schemas/inventoryProtocol.v1` |
| Vulcan | `$["inventoryProtocol"]["mode"]` | `#/components/schemas/inventoryProtocol.v1/properties/mode` |
| Vulcan | `$["accesses"][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |
| Vulcan | `$["accesses"][*][*]["config"]["wordCount"]` | `#/components/schemas/access_cmd_read.v1/properties/config/properties/wordCount` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["type"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type/constraints not specified here. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document ATR7000-only fields separately; establish FX9600 versus FXR inventoryProtocol support. Do not rename already-matching wordCount.

#### 6. Required action

Obtain missing Kamali review and method-specific samples. For PUT, the Starfish comment discusses responses and is not a request-support confirmation.

#### 7. Developer question

For PUT requests, does FX9600 support inventoryProtocol, and is DIRECTIONALITY/beams excluded from FX9600 as the ATR7000 note implies? Is wordCount the wire name on both target releases?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: response of FX9600 is same as FX90 but ATR7000 has Directionality mode to support portal directionality
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: The Starfish comment says response although the row concerns a request.

</details>

**Record decision:** tracker Issue ID `F-018`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-018a"></a>
### F-018A — PUT — Mode tag metadata names and representation

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 18 (Excel row 24); original endpoint `PUT /cloud/mode`.

#### 1. Issue

Starfish tagMetaData items combine type [string,object] with an enum of strings, including RESERVED, and object antennaPortNames. Vulcan uses oneOf string/object, omits RESERVED, adds READERLOCATION and antennaNames/gpsCoordinates. The Starfish enum may exclude its documented object form.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/mode`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["type"]
      - **REVIEW `tagMetaData`** — type="array"
        - **REVIEW `[]`** — type=["string","object"]; enum=["RSSI","PHASE","SEEN_COUNT","ANTENNA","CHANNEL","PC","XPC","CRC","EPC","TID","USER","RESERVED","MAC","HOSTNAME"]
          - **REVIEW `userDefined`** — type="string"
          - **REVIEW `antennaPortNames`** — type="array"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/mode`
  - Parameters: none declared
  - JSON request body
    - `$` — type/constraints not specified here
      - `/allOf[0]` — type="object"; required=["type"]
        - **REVIEW `tagMetaData`** — type="array"
          - **REVIEW `[]`** — type/constraints not specified here
            - **REVIEW `/oneOf[0]`** — type="string"; enum=["RSSI","PHASE","SEEN_COUNT","ANTENNA","CHANNEL","PC","XPC","CRC","EPC","TID","USER","MAC","HOSTNAME","READERLOCATION"]
            - **REVIEW `/oneOf[1]`** — type="object"
              - **REVIEW `userDefined`** — type="string"
              - **REVIEW `antennaNames`** — type="array"
              - **REVIEW `gpsCoordinates`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["tagMetaData"]` | `#/components/schemas/tagMetaData.v1` |
| Starfish | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items` |
| Starfish | `$["tagMetaData"][*]["userDefined"]` | `#/components/schemas/tagMetaData.v1/items/properties/userDefined` |
| Starfish | `$["tagMetaData"][*]["antennaPortNames"]` | `#/components/schemas/tagMetaData.v1/items/properties/antennaPortNames` |
| Vulcan | `$["tagMetaData"]` | `#/components/schemas/tagMetaData.v1` |
| Vulcan | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items` |
| Vulcan | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items/oneOf/0` |
| Vulcan | `$["tagMetaData"][*]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1` |
| Vulcan | `$["tagMetaData"][*]["userDefined"]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1/properties/userDefined` |
| Vulcan | `$["tagMetaData"][*]["antennaNames"]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1/properties/antennaNames` |
| Vulcan | `$["tagMetaData"][*]["gpsCoordinates"]` | `#/components/schemas/tagMetaData.v1/items/oneOf/1/properties/gpsCoordinates` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["type"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type/constraints not specified here. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use the exact accepted values and object keys for each platform, with a schema that permits the implemented object form.

#### 6. Required action

Verify metadata tokens and object inputs/outputs. Correct schema modeling if already supported; request firmware parity only for an approved missing capability.

#### 7. Developer question

Which tokens and object keys are accepted/returned on each model: RESERVED, READERLOCATION, antennaPortNames, antennaNames and gpsCoordinates? Does Starfish accept metadata objects despite its string enum? Supply a method-specific example.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: response of FX9600 is same as FX90 but ATR7000 has Directionality mode to support portal directionality
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: The Starfish comment says response although the row concerns a request.

</details>

**Record decision:** tracker Issue ID `F-018A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-20"></a>
## /cloud/nameAndDescription

<a id="e-031"></a>
### E-031 — GET — Intentional platform scope

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 31 (Excel row 37); original endpoint `GET /cloud/nameAndDescription`.

#### 1. Issue

Starfish defines the operation and Vulcan does not. Reviewer feedback supports that platform boundary.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/nameAndDescription`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"; required=["name","description"]
      - **`name`** — type="string"
      - **`description`** — type="string"

**Vulcan — FXR60 / FXR90**

- `GET /cloud/nameAndDescription`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["name"]` | `#/components/schemas/getNameAndDescription/properties/name` |
| Starfish | `$["description"]` | `#/components/schemas/getNameAndDescription/properties/description` |
| Vulcan | `Operation absent` | `#/paths/~1cloud~1nameAndDescription/get` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository path presence and reviewer platform-scope statement. For readerLocation, Starfish explicitly excludes FX9600; Kamali confirms FXR support without independently excluding FX9600.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["name","description"]. |
| Vulcan | Operation absent. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current platform-specific API scope.

#### 6. Required action

Keep the endpoint limited to the documented platform. No cross-platform feature implementation is requested by the source comment.

#### 7. Developer question

None unless the product requirement changes. A parity request would need a separate approved requirement.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Confirm if FXR supports this (undocumented), or firmware confirms FXR does not support it.
>
> Kamali:
> currently not supported in fxr90/60

</details>

**Record decision:** tracker Issue ID `E-031`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-032"></a>
### E-032 — PUT — Truncated PUT name-and-description feedback

**Developer Clarification · Open · Platform: Vulcan**  
Source: `Endpoint review` source row 32 (Excel row 38); original endpoint `PUT /cloud/nameAndDescription`.

#### 1. Issue

Starfish defines a required name/description body; Vulcan has no operation. Kamali’s PUT comment is only "cu". GET feedback does not establish PUT support.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/nameAndDescription`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["name","description"]
      - **REVIEW `name`** — type="string"
      - **REVIEW `description`** — type="string"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/nameAndDescription`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/components/schemas/setNameAndDescription` |
| Starfish | `$["name"]` | `#/components/schemas/setNameAndDescription/properties/name` |
| Starfish | `$["description"]` | `#/components/schemas/setNameAndDescription/properties/description` |
| Vulcan | `Operation absent` | `#/paths/~1cloud~1nameAndDescription/put` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["name","description"]. Success: 200 []: no body schema. |
| Vulcan | Operation absent. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Determine the FXR PUT support decision from the complete comment and a firmware-versioned contract.

#### 6. Required action

Obtain the remainder of Kamali’s source row 32. Keep this open; do not infer the missing text from GET.

#### 7. Developer question

What is the full PUT /cloud/nameAndDescription comment beginning "cu"? Is the operation intentionally unsupported on FXR60/FXR90, already implemented, or a requested firmware addition?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Confirm if FXR supports this (undocumented), or firmware confirms FXR does not support it.
>
> Kamali:
> cu

Source limitation: Kamali source ends at “cu”.

</details>

**Record decision:** tracker Issue ID `E-032`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-21"></a>
## /cloud/network

<a id="f-019"></a>
### F-019 — GET — Network GET body exists on both

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 19 (Excel row 25); original endpoint `GET /cloud/network`.

#### 1. Issue

The old comment says Vulcan has no body, but both current schemas have optional JSON interface. Starfish enum is all/eth0/mlan0; Vulcan also lists bnep0/wan0/uap0. No query parameter is declared in either.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/network`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"
      - **REVIEW `interface`** — type="string"; enum=["all","eth0","mlan0"]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/network`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"
      - **REVIEW `interface`** — type="string"; enum=["eth0","mlan0","bnep0","wan0","uap0","all"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/paths/~1cloud~1network/get/requestBody/content/application~1json/schema` |
| Starfish | `$["interface"]` | `#/paths/~1cloud~1network/get/requestBody/content/application~1json/schema/properties/interface` |
| Vulcan | `$` | `#/components/schemas/GetNetworkRequest` |
| Vulcan | `$["interface"]` | `#/components/schemas/GetNetworkRequest/properties/interface` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document a GET body only where implemented, with model-specific selector values and no invented query alternative.

#### 6. Required action

Correct the stale comparison statement. Obtain captures for omitted body and each supported selector, including transport/client compatibility.

#### 7. Developer question

Do both readers accept {"interface":"eth0"} in a GET body? What does an omitted body return? Are extra Vulcan selectors limited by model, and is any query alternative supported?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 matches the FX90
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Do not assume a GET parameter is query-only or body-only without confirmation.

</details>

**Record decision:** tracker Issue ID `F-019`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-009"></a>
### F-009 — PUT — Starfish network schema advertises unreleased nesting

**Schema Update · Open · Platform: Starfish**  
Source: `Field review` source row 9 (Excel row 15); original endpoint `PUT /cloud/network`.

#### 1. Issue

Starfish requires networkInterface and documents nested eth0/mlan0 setters. Its reviewer says local REST currently accepts only flat fields and nested setters are not implemented. Vulcan supports platform-specific interface payloads.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/network`
  - Parameters: none declared
  - JSON request body
    - **`$`** — type="object"; required=["dhcp","macAdress","hostName","networkInterface"]
      - **`dhcp`** — type="boolean"
      - **`macAdress`** — type="string"
      - **`hostName`** — type="string"
      - **`networkInterface`** — type="object"
        - **`eth0`** — type="object"
        - **`mlan0`** — type="object"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/network`
  - Parameters: none declared
  - JSON request body
    - **`$`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/components/schemas/networkconfig.v1` |
| Starfish | `$["dhcp"]` | `#/components/schemas/networkconfig.v1/properties/dhcp` |
| Starfish | `$["macAdress"]` | `#/components/schemas/networkconfig.v1/properties/macAdress` |
| Starfish | `$["hostName"]` | `#/components/schemas/networkconfig.v1/properties/hostName` |
| Starfish | `$["networkInterface"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface` |
| Starfish | `$["networkInterface"]["eth0"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/eth0` |
| Starfish | `$["networkInterface"]["mlan0"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/mlan0` |
| Vulcan | `$` | `#/components/schemas/UpdateNetworkRequest` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["dhcp","macAdress","hostName","networkInterface"]. Success: 200 [text/html]: type="string"; default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Describe the existing FX9600 flat request, without requiring networkInterface. Keep Vulcan interface configuration model-specific.

#### 6. Required action

Remove unreleased nested setters from the current Starfish schema/required list, after resolving exact wire spellings in F-009A. Keep future nesting separate in F-009B.

#### 7. Developer question

Does the approved current FX9600 request contain only flat host/IP/DHCP fields, with no required networkInterface? Supply a successful current-release request and field requiredness.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: in FX9600 its wrongly documented for localrest it does not support setting eth, mlan network interface FX9600 supports only following field in the payload hostname, ipAddress, gatewayAddress, subnetMask, dnsAddress, dhcp, macAddress
> Needed: yes
> Fix: set network in FX9600 is yet to implement for setting ETH, MLAN like Vulcan
>
> Kamali:
> Why: vulcan supports ethernet/wifi/bluetooth and wan.
> Needed: yes it is needed since hardware is different between vulcan and starfish response and payload will be different.

Source limitation: Verify exact key spelling (hostname/hostName and macAddress) against firmware.

</details>

**Record decision:** tracker Issue ID `F-009`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-009a"></a>
### F-009A — PUT — Network wire-key spelling

**Developer Clarification · Open · Platform: Starfish**  
Source: `Field review` source row 9 (Excel row 15); original endpoint `PUT /cloud/network`.

#### 1. Issue

Starfish schema uses macAdress and hostName; the reviewer writes macAddress and hostname. Changing case or correcting a spelling can break wire compatibility.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/network`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["dhcp","macAdress","hostName","networkInterface"]
      - **REVIEW `macAdress`** — type="string"
      - **REVIEW `hostName`** — type="string"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/network`
  - Parameters: none declared
  - JSON request body
    - **Target field absent from declared properties**. Absence alone does not prove firmware rejection.

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["macAdress"]` | `#/components/schemas/networkconfig.v1/properties/macAdress` |
| Starfish | `$["hostName"]` | `#/components/schemas/networkconfig.v1/properties/hostName` |
| Vulcan | `No target field declared` | `#/paths/~1cloud~1network/put` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["dhcp","macAdress","hostName","networkInterface"]. Success: 200 [text/html]: type="string"; default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use the exact accepted wire keys for each platform and firmware.

#### 6. Required action

Verify all three spellings against a request capture before editing the flat schema.

#### 7. Developer question

Does FX9600 accept macAdress or macAddress, and hostName or hostname? Are legacy aliases accepted? Are MAC-address changes writable or is that field informational?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: in FX9600 its wrongly documented for localrest it does not support setting eth, mlan network interface FX9600 supports only following field in the payload hostname, ipAddress, gatewayAddress, subnetMask, dnsAddress, dhcp, macAddress
> Needed: yes
> Fix: set network in FX9600 is yet to implement for setting ETH, MLAN like Vulcan
>
> Kamali:
> Why: vulcan supports ethernet/wifi/bluetooth and wan.
> Needed: yes it is needed since hardware is different between vulcan and starfish response and payload will be different.

Source limitation: Verify exact key spelling (hostname/hostName and macAddress) against firmware.

</details>

**Record decision:** tracker Issue ID `F-009A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-009b"></a>
### F-009B — PUT — Future Starfish interface setters

**Developer Clarification · Open · Platform: Starfish**  
Source: `Field review` source row 9 (Excel row 15); original endpoint `PUT /cloud/network`.

#### 1. Issue

The reviewer says ETH/MLAN setters like Vulcan are yet to be implemented; no approved target release or field-level requirement is supplied. Hardware differences are explicitly intentional.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/network`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["dhcp","macAdress","hostName","networkInterface"]
      - **REVIEW `networkInterface`** — type="object"
        - **REVIEW `eth0`** — type="object"
          - **REVIEW `IPV4`** — type="object"; required=["dhcp"]
          - **REVIEW `IPV6`** — type="object"
          - **REVIEW `isEnabled`** — type="boolean"
        - **REVIEW `mlan0`** — type="object"
          - **REVIEW `IPV4`** — type="object"; required=["dhcp"]
          - **REVIEW `IPV6`** — type="object"
          - **REVIEW `isEnabled`** — type="boolean"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/network`
  - Parameters: none declared
  - JSON request body
    - **Target field absent from declared properties**. Absence alone does not prove firmware rejection.

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["networkInterface"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface` |
| Starfish | `$["networkInterface"]["eth0"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/eth0` |
| Starfish | `$["networkInterface"]["eth0"]["IPV4"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/eth0/properties/IPV4` |
| Starfish | `$["networkInterface"]["eth0"]["IPV6"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/eth0/properties/IPV6` |
| Starfish | `$["networkInterface"]["eth0"]["isEnabled"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/eth0/properties/isEnabled` |
| Starfish | `$["networkInterface"]["mlan0"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/mlan0` |
| Starfish | `$["networkInterface"]["mlan0"]["IPV4"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/mlan0/properties/IPV4` |
| Starfish | `$["networkInterface"]["mlan0"]["IPV6"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/mlan0/properties/IPV6` |
| Starfish | `$["networkInterface"]["mlan0"]["isEnabled"]` | `#/components/schemas/networkconfig.v1/properties/networkInterface/properties/mlan0/properties/isEnabled` |
| Vulcan | `No target field declared` | `#/paths/~1cloud~1network/put` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["dhcp","macAdress","hostName","networkInterface"]. Success: 200 [text/html]: type="string"; default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Treat nested interface setting as future only if approved; do not require BLE/WAN/hotspot parity.

#### 6. Required action

Ask for the requirement/ticket and scope of eth0/mlan0 support. Define IPv4/IPv6 and enable/disable behavior before assigning firmware work.

#### 7. Developer question

Is nested networkInterface.eth0/mlan0 configuration an approved FX9600 firmware deliverable? Which fields, validation rules and release are committed?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: in FX9600 its wrongly documented for localrest it does not support setting eth, mlan network interface FX9600 supports only following field in the payload hostname, ipAddress, gatewayAddress, subnetMask, dnsAddress, dhcp, macAddress
> Needed: yes
> Fix: set network in FX9600 is yet to implement for setting ETH, MLAN like Vulcan
>
> Kamali:
> Why: vulcan supports ethernet/wifi/bluetooth and wan.
> Needed: yes it is needed since hardware is different between vulcan and starfish response and payload will be different.

Source limitation: Verify exact key spelling (hostname/hostName and macAddress) against firmware.

</details>

**Record decision:** tracker Issue ID `F-009B`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-020"></a>
### F-020 — PUT — Network response comment has the wrong method

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 20 (Excel row 26); original endpoint `PUT /cloud/network`.

#### 1. Issue

Original row says PUT RESPONSE, but cited detail filenames and interface variants describe GET. Current PUT success schemas are strings on both; the GET schemas contain network interfaces.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/network`
  - Success response body
    - **REVIEW `$`** — type="string"; default="Command Successful"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/network`
  - Success response body
    - **REVIEW `$`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/paths/~1cloud~1network/put/responses/200/content/text~1html/schema` |
| Vulcan | `$` | `#/components/schemas/UpdateNetworkResponse` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["dhcp","macAdress","hostName","networkInterface"]. Success: 200 [text/html]: type="string"; default="Command Successful". |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Assign the response-shape question to the correct operation without silently rewriting the original comment.

#### 6. Required action

Retain the source method in the tracker and request correction to GET if intended. Review GET variants separately in F-020A.

#### 7. Developer question

Should field row 20 be GET /cloud/network RESPONSE? Current PUT schemas are string acknowledgements; are there undocumented PUT response objects to review?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 does not support ble
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Original method PUT retained. BLE absence alone does not resolve WAN, hotspot or wrapper differences.

</details>

**Record decision:** tracker Issue ID `F-020`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-020a"></a>
### F-020A — PUT — Network GET response variants and model scope

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 20 (Excel row 26); original endpoint `PUT /cloud/network`.

#### 1. Issue

Starfish GET has oneOf flat legacy, all interfaces, eth0-only and mlan0-only forms. Vulcan has a single object with eth0/mlan0/bnep0/wan0/uap0. The source only explains BLE absence, leaving wrapper and other interfaces unresolved.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/network`
  - Success response body
    - **REVIEW `$`** — type/constraints not specified here
      - **REVIEW `/oneOf[0]`** — type="object"; required=["hostName","ipAddress","gatewayAddress","subnetMask","dnsAddress","dhcp","macAddress"]
        - **REVIEW `hostName`** — type="string"
      - **REVIEW `/oneOf[1]`** — type="object"; required=["hostName","networkInterface"]
        - **REVIEW `hostName`** — type="string"
        - **REVIEW `networkInterface`** — type="object"; required=["eth0","mlan0"]
          - **REVIEW `eth0`** — type="object"; required=["IPV4","IPV6","Status","isEnabled","macAddress","security"]
          - **REVIEW `mlan0`** — type="object"; required=["IPV4","IPV6","Status","isEnabled","macAddress","accesspoint"]
      - **REVIEW `/oneOf[2]`** — type="object"; required=["hostName","networkInterface"]
        - **REVIEW `hostName`** — type="string"
        - **REVIEW `networkInterface`** — type="object"; required=["eth0"]
          - **REVIEW `eth0`** — type="object"; required=["IPV4","IPV6","Status","isEnabled","macAddress","security"]
      - **REVIEW `/oneOf[3]`** — type="object"; required=["hostName","networkInterface"]
        - **REVIEW `hostName`** — type="string"
        - **REVIEW `networkInterface`** — type="object"; required=["mlan0"]
          - **REVIEW `mlan0`** — type="object"; required=["IPV4","IPV6","Status","isEnabled","macAddress","accesspoint"]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/network`
  - Success response body
    - **REVIEW `$`** — type="object"
      - **REVIEW `hostName`** — type="string"
      - **REVIEW `networkInterface`** — type="object"
        - **REVIEW `eth0`** — type="object"
        - **REVIEW `mlan0`** — type="object"
        - **REVIEW `bnep0`** — type="object"
        - **REVIEW `wan0`** — type="object"
        - **REVIEW `uap0`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/paths/~1cloud~1network/get/responses/200/content/application~1json/schema` |
| Starfish | `$` | `#/components/schemas/readernetwork.default.v1` |
| Starfish | `$["hostName"]` | `#/components/schemas/readernetwork.default.v1/properties/hostName` |
| Starfish | `$` | `#/components/schemas/readerNetworkDetailsALL` |
| Starfish | `$["hostName"]` | `#/components/schemas/readerNetworkDetailsALL/properties/hostName` |
| Starfish | `$["networkInterface"]` | `#/components/schemas/readerNetworkDetailsALL/properties/networkInterface` |
| Starfish | `$["networkInterface"]["eth0"]` | `#/components/schemas/readerNetworkDetailsALL/properties/networkInterface/properties/eth0` |
| Starfish | `$["networkInterface"]["mlan0"]` | `#/components/schemas/readerNetworkDetailsALL/properties/networkInterface/properties/mlan0` |
| Starfish | `$` | `#/components/schemas/readernetwork.eth0.v1` |
| Starfish | `$["hostName"]` | `#/components/schemas/readernetwork.eth0.v1/properties/hostName` |
| Starfish | `$["networkInterface"]` | `#/components/schemas/readernetwork.eth0.v1/properties/networkInterface` |
| Starfish | `$["networkInterface"]["eth0"]` | `#/components/schemas/readernetwork.eth0.v1/properties/networkInterface/properties/eth0` |
| Starfish | `$` | `#/components/schemas/readernetwork.mlan0.v1` |
| Starfish | `$["hostName"]` | `#/components/schemas/readernetwork.mlan0.v1/properties/hostName` |
| Starfish | `$["networkInterface"]` | `#/components/schemas/readernetwork.mlan0.v1/properties/networkInterface` |
| Starfish | `$["networkInterface"]["mlan0"]` | `#/components/schemas/readernetwork.mlan0.v1/properties/networkInterface/properties/mlan0` |
| Vulcan | `$` | `#/components/schemas/GetNetworkResponse` |
| Vulcan | `$["hostName"]` | `#/components/schemas/GetNetworkResponse/properties/hostName` |
| Vulcan | `$["networkInterface"]` | `#/components/schemas/GetNetworkResponse/properties/networkInterface` |
| Vulcan | `$["networkInterface"]["eth0"]` | `#/components/schemas/GetNetworkResponse/properties/networkInterface/properties/eth0` |
| Vulcan | `$["networkInterface"]["mlan0"]` | `#/components/schemas/GetNetworkResponse/properties/networkInterface/properties/mlan0` |
| Vulcan | `$["networkInterface"]["bnep0"]` | `#/components/schemas/GetNetworkResponse/properties/networkInterface/properties/bnep0` |
| Vulcan | `$["networkInterface"]["wan0"]` | `#/components/schemas/GetNetworkResponse/properties/networkInterface/properties/wan0` |
| Vulcan | `$["networkInterface"]["uap0"]` | `#/components/schemas/GetNetworkResponse/properties/networkInterface/properties/uap0` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type/constraints not specified here. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document actual selector-dependent response shapes and supported interfaces per model. Do not claim all branches apply to FX9600.

#### 6. Required action

Capture omitted/all/eth0/mlan0 responses and confirm WAN/hotspot scope. Keep interface capability differences separate from schema-wrapper corrections.

#### 7. Developer question

On FX9600, which selector/release produces the flat versus nested response? On FXR, which interfaces are returned for each selector and model? Are uap0 and wan0 intentionally unavailable on FX9600?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 does not support ble
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Original method PUT retained. BLE absence alone does not resolve WAN, hotspot or wrapper differences.

</details>

**Record decision:** tracker Issue ID `F-020A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-22"></a>
## /cloud/networkInterfaces

<a id="e-018"></a>
### E-018 — GET — Intentional platform scope

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 18 (Excel row 24); original endpoint `GET /cloud/networkInterfaces`.

#### 1. Issue

Vulcan defines the operation and Starfish does not. Reviewer feedback supports that platform boundary.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/networkInterfaces`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/networkInterfaces`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"; required=["availableNetworkInterfaces"]
      - **`availableNetworkInterfaces`** — type="array"; minItems=0; default=[]
        - **`[]`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1networkInterfaces/get` |
| Vulcan | `$["availableNetworkInterfaces"]` | `#/components/schemas/GetNetworkinterfacesResponse/properties/availableNetworkInterfaces` |
| Vulcan | `$["availableNetworkInterfaces"][*]` | `#/components/schemas/GetNetworkinterfacesResponse/properties/availableNetworkInterfaces/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository path presence and reviewer platform-scope statement. For readerLocation, Starfish explicitly excludes FX9600; Kamali confirms FXR support without independently excluding FX9600.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["availableNetworkInterfaces"]. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current platform-specific API scope.

#### 6. Required action

Keep the endpoint limited to the documented platform. No cross-platform feature implementation is requested by the source comment.

#### 7. Developer question

None unless the product requirement changes. A parity request would need a separate approved requirement.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> not supported in fx9600

</details>

**Record decision:** tracker Issue ID `E-018`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-23"></a>
## /cloud/ntpServer

<a id="f-021"></a>
### F-021 — PUT — NTP server aliases and second server

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 21 (Excel row 27); original endpoint `PUT /cloud/ntpServer`.

#### 1. Issue

Starfish requires server:string. Vulcan oneOf accepts required server or required server1, each with optional server2. Starfish reviewer confirms its single field; Kamali’s row is missing.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/ntpServer`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["server"]
      - **REVIEW `server`** — type="string"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/ntpServer`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type/constraints not specified here
      - **REVIEW `/oneOf[0]`** — type="object"; required=["server"]
        - **REVIEW `server`** — type="string"
        - **REVIEW `server2`** — type="string"
      - **REVIEW `/oneOf[1]`** — type="object"; required=["server1"]
        - **REVIEW `server1`** — type="string"
        - **REVIEW `server2`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/components/schemas/setNTPServer` |
| Starfish | `$["server"]` | `#/components/schemas/setNTPServer/properties/server` |
| Vulcan | `$` | `#/components/schemas/UpdateNtpServerRequest` |
| Vulcan | `$` | `#/components/schemas/UpdateNtpServerRequest/oneOf/0` |
| Vulcan | `$["server"]` | `#/components/schemas/UpdateNtpServerRequest/oneOf/0/properties/server` |
| Vulcan | `$["server2"]` | `#/components/schemas/UpdateNtpServerRequest/oneOf/0/properties/server2` |
| Vulcan | `$` | `#/components/schemas/UpdateNtpServerRequest/oneOf/1` |
| Vulcan | `$["server1"]` | `#/components/schemas/UpdateNtpServerRequest/oneOf/1/properties/server1` |
| Vulcan | `$["server2"]` | `#/components/schemas/UpdateNtpServerRequest/oneOf/1/properties/server2` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["server"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type/constraints not specified here. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Keep the reported Starfish single-server contract; establish FXR aliases and fallback semantics.

#### 6. Required action

Confirm server/server1 precedence, server2 behavior and whether both aliases can coexist. Correct oneOf if its exclusivity does not match runtime.

#### 7. Developer question

Does FXR accept server, server1 and server2 as documented? What happens when server and server1 are both sent, or server2 is sent alone? Is FX9600 single-server behavior intentional?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 has only one field server
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

</details>

**Record decision:** tracker Issue ID `F-021`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-24"></a>
## /cloud/os

<a id="f-022"></a>
### F-022 — PUT — OS download retry and timeout support

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 22 (Excel row 28); original endpoint `PUT /cloud/os`.

#### 1. Issue

Starfish OS upgrade defines retry/timeouts with backoff bounds; Vulcan omits them. App-install timing from F-011 is not evidence for this endpoint. The Vulcan operation description also explicitly says HTTPS retry and timeout options are not supported.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/os`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["url","authenticationType"]
      - **REVIEW `retry`** — type="object"
        - **REVIEW `type`** — type="string"; enum=["randomWait"]
        - **REVIEW `policy`** — type="object"
          - **REVIEW `retries`** — type="integer"; minimum=1; maximum=50; default=1
          - **REVIEW `wait`** — type="object"
            - **REVIEW `min`** — type="integer"; minimum=0; maximum=3600; default=30
            - **REVIEW `max`** — type="integer"; minimum=1; maximum=3600; default=300
      - **REVIEW `timeouts`** — type="object"
        - **REVIEW `connection`** — type="integer"; minimum=1; maximum=3600; default=60
        - **REVIEW `read`** — type="integer"; minimum=1; maximum=3600; default=600

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/os`
  - Parameters: none declared
  - JSON request body
    - **Target field absent from declared properties**. Absence alone does not prove firmware rejection.

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["retry"]` | `#/components/schemas/os_update.v1/properties/retry` |
| Starfish | `$["retry"]["type"]` | `#/components/schemas/os_update.v1/properties/retry/properties/type` |
| Starfish | `$["retry"]["policy"]` | `#/components/schemas/os_update.v1/properties/retry/properties/policy` |
| Starfish | `$["retry"]["policy"]["retries"]` | `#/components/schemas/os_update.v1/properties/retry/properties/policy/properties/retries` |
| Starfish | `$["retry"]["policy"]["wait"]` | `#/components/schemas/os_update.v1/properties/retry/properties/policy/properties/wait` |
| Starfish | `$["retry"]["policy"]["wait"]["min"]` | `#/components/schemas/os_update.v1/properties/retry/properties/policy/properties/wait/properties/min` |
| Starfish | `$["retry"]["policy"]["wait"]["max"]` | `#/components/schemas/os_update.v1/properties/retry/properties/policy/properties/wait/properties/max` |
| Starfish | `$["timeouts"]` | `#/components/schemas/os_update.v1/properties/timeouts` |
| Starfish | `$["timeouts"]["connection"]` | `#/components/schemas/os_update.v1/properties/timeouts/properties/connection` |
| Starfish | `$["timeouts"]["read"]` | `#/components/schemas/os_update.v1/properties/timeouts/properties/read` |
| Vulcan | `No target field declared` | `#/paths/~1cloud~1os/put` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["url","authenticationType"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["url","authenticationType"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Determine OS-specific support and target firmware before documenting parity.

#### 6. Required action

Obtain the missing Kamali OS review and test contract. Update schema if implemented; record firmware work only if an approved missing capability is confirmed.

#### 7. Developer question

Which FXR OS-upgrade firmware accepts retry.type=randomWait, policy.retries/wait and timeouts.connection/read? Are the Starfish bounds/defaults identical, and what release introduced them?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 has backoff policy to support resonate
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

</details>

**Record decision:** tracker Issue ID `F-022`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-022a"></a>
### F-022A — PUT — OS authentication keys and transport differences

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 22 (Excel row 28); original endpoint `PUT /cloud/os`.

#### 1. Issue

Starfish schema uses options for credentials; Vulcan schema still uses authenticationOptions. Vulcan constrains URL schemes to scp/https/sftp/ftps and adds key/certificate fields. The original transfer_protocol claim is not a field in either current request schema. Vulcan narrative recommends authenticationOptions with options as a fallback, but only authenticationOptions is modeled.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/os`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["url","authenticationType"]
      - **REVIEW `url`** — type="string"
      - **REVIEW `options`** — type="object"
        - **REVIEW `username`** — type="string"
        - **REVIEW `password`** — type="string"
      - **REVIEW `headers`** — type="object"

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/os`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["url","authenticationType"]
      - **REVIEW `authenticationOptions`** — type="object"; required=["username","password"]
        - **REVIEW `username`** — type="string"
        - **REVIEW `password`** — type="string"
      - **REVIEW `url`** — type="string"; format="uri"
        - **REVIEW `/oneOf[0]`** — pattern="^scp://.+"
        - **REVIEW `/oneOf[1]`** — pattern="^https://.+"
        - **REVIEW `/oneOf[2]`** — pattern="^sftp://.+"
        - **REVIEW `/oneOf[3]`** — pattern="^ftps://.+"
      - **REVIEW `publicKeyFileLocation`** — type="string"
      - **REVIEW `privateKeyFileLocation`** — type="string"
      - **REVIEW `installedCertificateType`** — type="string"
      - **REVIEW `installedCertificateName`** — type="string"
      - **REVIEW `headers`** — type="object"; additionalProperties={"type":"string"}

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["url"]` | `#/components/schemas/os_update.v1/properties/url` |
| Starfish | `$["options"]` | `#/components/schemas/os_update.v1/properties/options` |
| Starfish | `$["options"]["username"]` | `#/components/schemas/os_update.v1/properties/options/properties/username` |
| Starfish | `$["options"]["password"]` | `#/components/schemas/os_update.v1/properties/options/properties/password` |
| Starfish | `$["headers"]` | `#/components/schemas/os_update.v1/properties/headers` |
| Vulcan | `$["authenticationOptions"]` | `#/components/schemas/SetOsRequest/properties/authenticationOptions` |
| Vulcan | `$["authenticationOptions"]["username"]` | `#/components/schemas/SetOsRequest/properties/authenticationOptions/properties/username` |
| Vulcan | `$["authenticationOptions"]["password"]` | `#/components/schemas/SetOsRequest/properties/authenticationOptions/properties/password` |
| Vulcan | `$["url"]` | `#/components/schemas/SetOsRequest/properties/url` |
| Vulcan | `$["url"]` | `#/components/schemas/SetOsRequest/properties/url/oneOf/0` |
| Vulcan | `$["url"]` | `#/components/schemas/SetOsRequest/properties/url/oneOf/1` |
| Vulcan | `$["url"]` | `#/components/schemas/SetOsRequest/properties/url/oneOf/2` |
| Vulcan | `$["url"]` | `#/components/schemas/SetOsRequest/properties/url/oneOf/3` |
| Vulcan | `$["publicKeyFileLocation"]` | `#/components/schemas/SetOsRequest/properties/publicKeyFileLocation` |
| Vulcan | `$["privateKeyFileLocation"]` | `#/components/schemas/SetOsRequest/properties/privateKeyFileLocation` |
| Vulcan | `$["installedCertificateType"]` | `#/components/schemas/SetOsRequest/properties/installedCertificateType` |
| Vulcan | `$["installedCertificateName"]` | `#/components/schemas/SetOsRequest/properties/installedCertificateName` |
| Vulcan | `$["headers"]` | `#/components/schemas/SetOsRequest/properties/headers` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object"; required=["url","authenticationType"]. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["url","authenticationType"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Use the accepted credential key and transport-specific options for the target release.

#### 6. Required action

Reconcile Vulcan narrative/examples with its schema and capture accepted options versus authenticationOptions. Verify certificate and header fields independently.

#### 7. Developer question

For FXR PUT /cloud/os, is options or authenticationOptions accepted today? Are the listed URL schemes and installed/public/private certificate fields valid for each transport? Is transfer_protocol obsolete or an actual missing field?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 has backoff policy to support resonate
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

</details>

**Record decision:** tracker Issue ID `F-022A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-25"></a>
## /cloud/pass-through

<a id="e-019"></a>
### E-019 — PUT — Missing Starfish component pass-through documentation

**Schema Update · Open · Platform: Starfish**  
Source: `Endpoint review` source row 19 (Excel row 25); original endpoint `PUT /cloud/pass-through`.

#### 1. Issue

Both reviewers report FX9600 support. The provided Starfish file omits PUT /cloud/pass-through although its app-specific pass-through operation exists. Vulcan accepts component and payload.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/pass-through`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/pass-through`
  - Parameters: none declared
  - JSON request body
    - **`$`** — type="object"
      - **`component`** — type="string"; enum=["RC"]
      - **`payload`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1pass-through/put` |
| Vulcan | `$` | `#/components/schemas/SetPassthruRequest` |
| Vulcan | `$["component"]` | `#/components/schemas/SetPassthruRequest/properties/component` |
| Vulcan | `$["payload"]` | `#/components/schemas/SetPassthruRequest/properties/payload` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Add documentation for the existing component operation without conflating it with /cloud/apps/{appname}/pass-through.

#### 6. Required action

Locate the documented Starfish reference mentioned by its reviewer and obtain the accepted component payload before adding the missing operation to this file.

#### 7. Developer question

For FX9600 component pass-through, are component="RC" and payload strings accepted at this exact URL? Supply the existing reference or a redacted transaction.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (documented)
>
> Kamali:
> supported in fx9600 undocumented

</details>

**Record decision:** tracker Issue ID `E-019`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-26"></a>
## /cloud/preSelection

<a id="e-020"></a>
### E-020 — GET — Intentional platform scope

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 20 (Excel row 26); original endpoint `GET /cloud/preSelection`.

#### 1. Issue

Vulcan defines the operation and Starfish does not. Reviewer feedback supports that platform boundary.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/preSelection`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/preSelection`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"
      - **`preSelection`** — type="string"; enum=["enabled","disabled"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1preSelection/get` |
| Vulcan | `$["preSelection"]` | `#/components/schemas/GetPreSelectionResponse/properties/preSelection` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository path presence and reviewer platform-scope statement. For readerLocation, Starfish explicitly excludes FX9600; Kamali confirms FXR support without independently excluding FX9600.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current platform-specific API scope.

#### 6. Required action

Keep the endpoint limited to the documented platform. No cross-platform feature implementation is requested by the source comment.

#### 7. Developer question

None unless the product requirement changes. A parity request would need a separate approved requirement.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr90/60

</details>

**Record decision:** tracker Issue ID `E-020`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-021"></a>
### E-021 — PUT — Intentional platform scope

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 21 (Excel row 27); original endpoint `PUT /cloud/preSelection`.

#### 1. Issue

Vulcan defines the operation and Starfish does not. Reviewer feedback supports that platform boundary.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/preSelection`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/preSelection`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"
      - **`preSelection`** — type="boolean"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1preSelection/put` |
| Vulcan | `$["preSelection"]` | `#/components/schemas/SetPreSelectionRequest/properties/preSelection` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository path presence and reviewer platform-scope statement. For readerLocation, Starfish explicitly excludes FX9600; Kamali confirms FXR support without independently excluding FX9600.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current platform-specific API scope.

#### 6. Required action

Keep the endpoint limited to the documented platform. No cross-platform feature implementation is requested by the source comment.

#### 7. Developer question

None unless the product requirement changes. A parity request would need a separate approved requirement.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr90/60

</details>

**Record decision:** tracker Issue ID `E-021`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-27"></a>
## /cloud/readPoints

<a id="e-022"></a>
### E-022 — GET — Internal read-points support versus public REST

**Developer Clarification · Open · Platform: Starfish**  
Source: `Endpoint review` source row 22 (Excel row 28); original endpoint `GET /cloud/readPoints`.

#### 1. Issue

Starfish says readPoints is internal and not exposed externally; Kamali reports missing local REST mapping and available MQTT. Only Vulcan has this public path.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/readPoints`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/readPoints`
  - Parameters: none declared
  - Success response body
    - **REVIEW `$`** — type="array"
      - **REVIEW `[]`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1readPoints/get` |
| Vulcan | `$` | `#/components/schemas/GetReadpointsResponse` |
| Vulcan | `$[*]` | `#/components/schemas/GetReadpointsResponse/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="array". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Keep FX9600 out of the public REST reference unless external exposure is explicitly required and released.

#### 6. Required action

Record the exposure decision. If internal/MQTT-only is intentional, close as No Change Required. If external REST is required, define a mapping change and its release.

#### 7. Developer question

Is FX9600 GET /cloud/readPoints intentionally internal/MQTT-only, or must it become a public authenticated local REST endpoint? If required, identify the output contract and release.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this internally (undocumented) API is not exposed to outside
>
> Kamali:
> not supported in fx9600 (local rest mapping missing,mqtt available)

Source limitation: Internal support and public REST availability are different.

</details>

**Record decision:** tracker Issue ID `E-022`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-28"></a>
## /cloud/readerCapabilities

<a id="f-023"></a>
### F-023 — GET — Capabilities response wire shapes and case

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 23 (Excel row 29); original endpoint `GET /cloud/readerCapabilities`.

#### 1. Issue

Starfish apiSupported.versions is an object, appLedColors has mixed case and antenna types are uppercase. Vulcan versions is an array, appLEDColors differs in case and antenna types are lowercase. Starfish says its document matches firmware; no Kamali response is supplied.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/readerCapabilities`
  - Success response body
    - `$` — type="object"; required=["capabilities"]
      - `capabilities` — type="object"; required list at exact pointer below
        - `antennas` — type="array"; minItems=1; maxItems=8
          - `[]` — type="object"; required=["port","type"]
            - **REVIEW `type`** — type="string"; enum=["INTERNAL","EXTERNAL"]
        - `apiSupported` — type="object"; required=["versions"]
          - **REVIEW `versions`** — type="object"; required=["documentation","version"]
            - **REVIEW `documentation`** — type="string"
            - **REVIEW `version`** — type="string"; enum=["v1"]
        - **REVIEW `appLedColors`** — type="array"; minItems=1
          - **REVIEW `[]`** — type="string"; enum=["RED","GREEN","AMBER"]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/readerCapabilities`
  - Success response body
    - `$` — type="object"
      - `capabilities` — type="object"
        - `antennas` — type="array"
          - `[]` — type="object"; required=["port","type"]
            - **REVIEW `type`** — type="string"; enum=["external","internal"]
        - `apiSupported` — type="object"
          - **REVIEW `versions`** — type="array"
            - **REVIEW `[]`** — type="object"
              - **REVIEW `documentation`** — type="string"
              - **REVIEW `version`** — type="string"
        - **REVIEW `appLEDColors`** — type="array"
          - **REVIEW `[]`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["capabilities"]["antennas"][*]["type"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/antennas/items/properties/type` |
| Starfish | `$["capabilities"]["apiSupported"]["versions"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/apiSupported/properties/versions` |
| Starfish | `$["capabilities"]["apiSupported"]["versions"]["documentation"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/apiSupported/properties/versions/properties/documentation` |
| Starfish | `$["capabilities"]["apiSupported"]["versions"]["version"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/apiSupported/properties/versions/properties/version` |
| Starfish | `$["capabilities"]["appLedColors"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/appLedColors` |
| Starfish | `$["capabilities"]["appLedColors"][*]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/appLedColors/items` |
| Vulcan | `$["capabilities"]["antennas"][*]["type"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/antennas/items/properties/type` |
| Vulcan | `$["capabilities"]["apiSupported"]["versions"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/apiSupported/properties/versions` |
| Vulcan | `$["capabilities"]["apiSupported"]["versions"][*]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/apiSupported/properties/versions/items` |
| Vulcan | `$["capabilities"]["apiSupported"]["versions"][*]["documentation"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/apiSupported/properties/versions/items/properties/documentation` |
| Vulcan | `$["capabilities"]["apiSupported"]["versions"][*]["version"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/apiSupported/properties/versions/items/properties/version` |
| Vulcan | `$["capabilities"]["appLEDColors"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/appLEDColors` |
| Vulcan | `$["capabilities"]["appLEDColors"][*]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/appLEDColors/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["capabilities"]. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Preserve real wire differences unless a compatibility change is explicitly approved.

#### 6. Required action

Capture both capability responses; classify each difference as an intentional contract or schema error before standardizing names.

#### 7. Developer question

Does FXR actually return versions as an array, appLEDColors and lowercase antenna types, while FX9600 returns object/appLedColors/uppercase? If so, should documentation retain these platform variants?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 document matches the firmware
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Real array/object and case differences must not be dismissed as schema noise.

</details>

**Record decision:** tracker Issue ID `F-023`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-023a"></a>
### F-023A — GET — Capabilities are model-dependent

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 23 (Excel row 29); original endpoint `GET /cloud/readerCapabilities`.

#### 1. Issue

Vulcan adds WAN/SIM/network-type, stackLED and gen2xFeaturesSupported. Starfish is a multi-model schema and even lists BLUETOOTH as an interface type, which must not be read as FX9600 BLE-scanning support. Several counters are integer on Starfish and number on Vulcan.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/readerCapabilities`
  - Success response body
    - `$` — type="object"; required=["capabilities"]
      - `capabilities` — type="object"; required list at exact pointer below
        - **REVIEW `maxAppLEDs`** — type="integer"
        - `networkInterfaces` — type="array"
          - **REVIEW `[]`** — type="object"; required=["802.1x","internal","ipAssignment","ipStack","type"]
            - **REVIEW `type`** — type="string"; enum=["ETHERNET","WIFI","BLUETOOTH"]
        - **REVIEW `numGPIs`** — type="integer"
        - **REVIEW `numGPOs`** — type="integer"

**Vulcan — FXR60 / FXR90**

- `GET /cloud/readerCapabilities`
  - Success response body
    - `$` — type="object"
      - `capabilities` — type="object"
        - **REVIEW `stackLED`** — type/constraints not specified here
        - **REVIEW `maxAppLEDs`** — type="number"
        - `networkInterfaces` — type="array"
          - **REVIEW `[]`** — type="object"; required=["type","internal","ipStack","ipAssignment","802.1x"]
            - **REVIEW `type`** — type="string"; enum=["ETHERNET","WIFI","BLUETOOTH","WAN"]
            - **REVIEW `supportedSim`** — type="array"
              - **REVIEW `[]`** — type="string"; enum=["Physical SIM","Embedded SIM"]
            - **REVIEW `NetworkType`** — type="array"
              - **REVIEW `[]`** — type="string"; enum=["AUTO","LTE","NR5G"]
        - **REVIEW `numGPIs`** — type="number"
        - **REVIEW `numGPOs`** — type="number"
        - **REVIEW `gen2xFeaturesSupported`** — type="array"
          - **REVIEW `[]`** — type="string"; enum=["FASTID","TAGFOCUS","TAGQUIETING","PROTECTEDMODE"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["capabilities"]["maxAppLEDs"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/maxAppLEDs` |
| Starfish | `$["capabilities"]["networkInterfaces"][*]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/networkInterfaces/items` |
| Starfish | `$["capabilities"]["networkInterfaces"][*]["type"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/networkInterfaces/items/properties/type` |
| Starfish | `$["capabilities"]["numGPIs"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/numGPIs` |
| Starfish | `$["capabilities"]["numGPOs"]` | `#/components/schemas/getReaderCapabilites/properties/capabilities/properties/numGPOs` |
| Vulcan | `$["capabilities"]["stackLED"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/stackLED` |
| Vulcan | `$["capabilities"]["maxAppLEDs"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/maxAppLEDs` |
| Vulcan | `$["capabilities"]["networkInterfaces"][*]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/networkInterfaces/items` |
| Vulcan | `$["capabilities"]["networkInterfaces"][*]["type"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/networkInterfaces/items/properties/type` |
| Vulcan | `$["capabilities"]["networkInterfaces"][*]["supportedSim"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/networkInterfaces/items/properties/supportedSim` |
| Vulcan | `$["capabilities"]["networkInterfaces"][*]["supportedSim"][*]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/networkInterfaces/items/properties/supportedSim/items` |
| Vulcan | `$["capabilities"]["networkInterfaces"][*]["NetworkType"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/networkInterfaces/items/properties/NetworkType` |
| Vulcan | `$["capabilities"]["networkInterfaces"][*]["NetworkType"][*]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/networkInterfaces/items/properties/NetworkType/items` |
| Vulcan | `$["capabilities"]["numGPIs"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/numGPIs` |
| Vulcan | `$["capabilities"]["numGPOs"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/numGPOs` |
| Vulcan | `$["capabilities"]["gen2xFeaturesSupported"]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/gen2xFeaturesSupported` |
| Vulcan | `$["capabilities"]["gen2xFeaturesSupported"][*]` | `#/components/schemas/GetReadercapabilitiesResponse/properties/capabilities/properties/gen2xFeaturesSupported/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["capabilities"]. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Scope capability values to model/firmware and use the real numeric types.

#### 6. Required action

Obtain FX9600/FXR60/FXR90 capability samples. Keep radio-scanning support distinct from a Bluetooth network interface.

#### 7. Developer question

Which WAN/SIM/stack LED/Gen2X capability keys are returned by each model? Are count fields always integers? Does BLUETOOTH describe networking rather than BLE scanning on the relevant platform?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 document matches the firmware
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Real array/object and case differences must not be dismissed as schema noise.

</details>

**Record decision:** tracker Issue ID `F-023A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-29"></a>
## /cloud/readerLocation

<a id="e-023"></a>
### E-023 — GET — Intentional platform scope

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 23 (Excel row 29); original endpoint `GET /cloud/readerLocation`.

#### 1. Issue

Vulcan defines the operation and Starfish does not. Reviewer feedback supports that platform boundary.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/readerLocation`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/readerLocation`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"
      - **`lastReportedTime`** — type="string"
      - **`latitude`** — type="string"
      - **`longitude`** — type="string"
      - **`satellitesUsed`** — type="integer"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1readerLocation/get` |
| Vulcan | `$["lastReportedTime"]` | `#/components/schemas/GetGpsCoordinatesResponse/properties/lastReportedTime` |
| Vulcan | `$["latitude"]` | `#/components/schemas/GetGpsCoordinatesResponse/properties/latitude` |
| Vulcan | `$["longitude"]` | `#/components/schemas/GetGpsCoordinatesResponse/properties/longitude` |
| Vulcan | `$["satellitesUsed"]` | `#/components/schemas/GetGpsCoordinatesResponse/properties/satellitesUsed` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository path presence and reviewer platform-scope statement. For readerLocation, Starfish explicitly excludes FX9600; Kamali confirms FXR support without independently excluding FX9600.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current platform-specific API scope.

#### 6. Required action

Keep the endpoint limited to the documented platform. No cross-platform feature implementation is requested by the source comment.

#### 7. Developer question

None unless the product requirement changes. A parity request would need a separate approved requirement.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported in fxr90/60

Source limitation: Kamali confirms FXR support but does not explicitly deny FX9600 support.

</details>

**Record decision:** tracker Issue ID `E-023`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-30"></a>
## /cloud/region

<a id="f-024"></a>
### F-024 — GET — Region response fields and numeric types

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 24 (Excel row 30); original endpoint `GET /cloud/region`.

#### 1. Issue

Vulcan adds FrequencyHopping/country, uses channelData strings and unconstrained numeric power fields. Starfish channelData items are numbers and min/max power are integer enums [0]/[300]. Starfish says its document matches firmware.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/region`
  - Success response body
    - **REVIEW `$`** — type="object"; required=["region","lbtEnabled"]
      - **REVIEW `region`** — type="string"
      - **REVIEW `regulatoryStandard`** — type="string"
      - **REVIEW `lbtEnabled`** — type="boolean"; default=false
      - **REVIEW `channelData`** — type="array"
        - **REVIEW `[]`** — type="number"
      - **REVIEW `minTxPowerSupported`** — type="integer"; enum=[0]
      - **REVIEW `maxTxPowerSupported`** — type="integer"; enum=[300]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/region`
  - Success response body
    - **REVIEW `$`** — type="object"
      - **REVIEW `FrequencyHopping`** — type="boolean"
      - **REVIEW `region`** — type="string"
      - **REVIEW `regulatoryStandard`** — type="string"
      - **REVIEW `lbtEnabled`** — type="boolean"
      - **REVIEW `channelData`** — type="array"
        - **REVIEW `[]`** — type="string"
      - **REVIEW `country`** — type="string"
      - **REVIEW `minTxPowerSupported`** — type="number"
      - **REVIEW `maxTxPowerSupported`** — type="number"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/components/schemas/regionconfig.v1` |
| Starfish | `$["region"]` | `#/components/schemas/regionconfig.v1/properties/region` |
| Starfish | `$["regulatoryStandard"]` | `#/components/schemas/regionconfig.v1/properties/regulatoryStandard` |
| Starfish | `$["lbtEnabled"]` | `#/components/schemas/regionconfig.v1/properties/lbtEnabled` |
| Starfish | `$["channelData"]` | `#/components/schemas/regionconfig.v1/properties/channelData` |
| Starfish | `$["channelData"][*]` | `#/components/schemas/regionconfig.v1/properties/channelData/items` |
| Starfish | `$["minTxPowerSupported"]` | `#/components/schemas/regionconfig.v1/properties/minTxPowerSupported` |
| Starfish | `$["maxTxPowerSupported"]` | `#/components/schemas/regionconfig.v1/properties/maxTxPowerSupported` |
| Vulcan | `$` | `#/components/schemas/GetRegionResponse` |
| Vulcan | `$["FrequencyHopping"]` | `#/components/schemas/GetRegionResponse/properties/FrequencyHopping` |
| Vulcan | `$["region"]` | `#/components/schemas/GetRegionResponse/properties/region` |
| Vulcan | `$["regulatoryStandard"]` | `#/components/schemas/GetRegionResponse/properties/regulatoryStandard` |
| Vulcan | `$["lbtEnabled"]` | `#/components/schemas/GetRegionResponse/properties/lbtEnabled` |
| Vulcan | `$["channelData"]` | `#/components/schemas/GetRegionResponse/properties/channelData` |
| Vulcan | `$["channelData"][*]` | `#/components/schemas/GetRegionResponse/properties/channelData/items` |
| Vulcan | `$["country"]` | `#/components/schemas/GetRegionResponse/properties/country` |
| Vulcan | `$["minTxPowerSupported"]` | `#/components/schemas/GetRegionResponse/properties/minTxPowerSupported` |
| Vulcan | `$["maxTxPowerSupported"]` | `#/components/schemas/GetRegionResponse/properties/maxTxPowerSupported` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["region","lbtEnabled"]. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document exact response types, units and regulatory/model limits without treating example power values as universal unless confirmed.

#### 6. Required action

Obtain paired region responses and confirm field presence, channel units and power constraints.

#### 7. Developer question

Are channelData elements strings on FXR and numbers on FX9600? Are 0 and 300 fixed FX9600 power values or examples, and are country/FrequencyHopping truly FXR-only?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 document is matching firmware
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

</details>

**Record decision:** tracker Issue ID `F-024`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-024"></a>
### E-024 — PUT — Region setter release status

**Developer Clarification · Open · Platform: Starfish**  
Source: `Endpoint review` source row 24 (Excel row 30); original endpoint `PUT /cloud/region`.

#### 1. Issue

Starfish feedback simultaneously says supported and implementation ongoing. PUT /cloud/region is absent in the supplied Starfish file. Vulcan defines country, standardname, isLBT and channeldata.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/region`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/region`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"; required=["country","standardname"]
      - **REVIEW `country`** — type="string"
      - **REVIEW `standardname`** — type="string"
      - **REVIEW `isLBT`** — type="boolean"
      - **REVIEW `channeldata`** — type="array"
        - **REVIEW `[]`** — type="integer"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1region/put` |
| Vulcan | `$` | `#/components/schemas/SetRegionRequest` |
| Vulcan | `$["country"]` | `#/components/schemas/SetRegionRequest/properties/country` |
| Vulcan | `$["standardname"]` | `#/components/schemas/SetRegionRequest/properties/standardname` |
| Vulcan | `$["isLBT"]` | `#/components/schemas/SetRegionRequest/properties/isLBT` |
| Vulcan | `$["channeldata"]` | `#/components/schemas/SetRegionRequest/properties/channeldata` |
| Vulcan | `$["channeldata"][*]` | `#/components/schemas/SetRegionRequest/properties/channeldata/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["country","standardname"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Establish whether a released FX9600 setter exists and which request keys it accepts.

#### 6. Required action

Obtain the implementation ticket, release state and exact FX9600 request. Keep any future support labeled unreleased until confirmed.

#### 7. Developer question

Has FX9600 PUT /cloud/region shipped? Provide the firmware version and accepted keys, or identify the pending implementation and approved acceptance criteria.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> FX9600 supports this (undocumented) FX9600 currently implementation is going on
>
> Kamali:
> supported in fxr90/60 .

Source limitation: Starfish says both supported and implementation in progress; Kamali only confirms FXR support.

</details>

**Record decision:** tracker Issue ID `E-024`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-31"></a>
## /cloud/stack-led

<a id="e-025"></a>
### E-025 — GET — Stack LED scope already narrowed

**No Change Required · Resolved · Platform: Vulcan**  
Source: `Endpoint review` source row 25 (Excel row 31); original endpoint `GET /cloud/stack-led`.

#### 1. Issue

Kamali restricts support to FXR60; the current narrative is more specific: FXR60 Premium only, excluding other FXR60 variants and FXR90. Starfish omits it.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/stack-led`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/stack-led`
  - Parameters: none declared
  - Success response body
    - `$` — type/constraints not specified here
      - `/oneOf[0]` — type="object"; required=["status"]; additionalProperties=false
        - **`status`** — type="string"; enum=["DEFAULT"]
      - `/oneOf[1]` — type="object"; required=["status","color","flash","seconds"]
        - **`status`** — type="string"; enum=["NON_DEFAULT"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1stack-led/get` |
| Vulcan | `$["status"]` | `#/components/schemas/GetStackledResponse/oneOf/0/properties/status` |
| Vulcan | `$["status"]` | `#/components/schemas/GetStackledResponse/oneOf/1/properties/status` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both current operation descriptions explicitly exclude FXR90; reviewer comments agree on FXR60 scope.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type/constraints not specified here. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain FXR60 Premium applicability as documented.

#### 6. Required action

No additional scope correction is needed in this snapshot. Preserve the SKU restriction in the unified index.

#### 7. Developer question

None for the requested FXR90 exclusion. Reopen only if the Premium SKU restriction is disputed.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr60

</details>

**Record decision:** tracker Issue ID `E-025`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="e-026"></a>
### E-026 — PUT — Stack LED scope already narrowed

**No Change Required · Resolved · Platform: Vulcan**  
Source: `Endpoint review` source row 26 (Excel row 32); original endpoint `PUT /cloud/stack-led`.

#### 1. Issue

Kamali restricts support to FXR60; the current narrative is more specific: FXR60 Premium only, excluding other FXR60 variants and FXR90. Starfish omits it.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/stack-led`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/stack-led`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["color","flash","seconds"]
      - **`color`** — type="string"; enum=["red","amber","green","blue","off"]
      - **`brightness`** — type="string"; enum=["low","med","high"]
      - **`flash`** — type="boolean"
      - **`seconds`** — type="integer"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1stack-led/put` |
| Vulcan | `$["color"]` | `#/components/schemas/SetStackledRequest/properties/color` |
| Vulcan | `$["brightness"]` | `#/components/schemas/SetStackledRequest/properties/brightness` |
| Vulcan | `$["flash"]` | `#/components/schemas/SetStackledRequest/properties/flash` |
| Vulcan | `$["seconds"]` | `#/components/schemas/SetStackledRequest/properties/seconds` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both current operation descriptions explicitly exclude FXR90; reviewer comments agree on FXR60 scope.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["color","flash","seconds"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain FXR60 Premium applicability as documented.

#### 6. Required action

No additional scope correction is needed in this snapshot. Preserve the SKU restriction in the unified index.

#### 7. Developer question

None for the requested FXR90 exclusion. Reopen only if the Premium SKU restriction is disputed.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> supported only in fxr60

</details>

**Record decision:** tracker Issue ID `E-026`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-32"></a>
## /cloud/start

<a id="f-025"></a>
### F-025 — PUT — Starfish start Gen2X flag missing

**Schema Update · Open · Platform: Starfish**  
Source: `Field review` source row 25 (Excel row 31); original endpoint `PUT /cloud/start`.

#### 1. Issue

Starfish /start schema only lists doNotPersistState, but its reviewer explicitly says applyImpinjGen2X exists and scanType does not. Vulcan has both.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/start`
  - Parameters: none declared
  - JSON request body
    - **`$`** — type="object"
      - **`doNotPersistState`** — type="boolean"; default=true

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/start`
  - Parameters: none declared
  - JSON request body
    - **`$`** — type="object"
      - **`scanType`** — type="array"; minItems=1
        - **`[]`** — type="string"; enum=["ble","rfid"]
      - **`doNotPersistState`** — type="boolean"
      - **`applyImpinjGen2X`** — type="boolean"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/components/schemas/start` |
| Starfish | `$["doNotPersistState"]` | `#/components/schemas/start/properties/doNotPersistState` |
| Vulcan | `$` | `#/components/schemas/StartInventoryRequest` |
| Vulcan | `$["scanType"]` | `#/components/schemas/StartInventoryRequest/properties/scanType` |
| Vulcan | `$["scanType"][*]` | `#/components/schemas/StartInventoryRequest/properties/scanType/items` |
| Vulcan | `$["doNotPersistState"]` | `#/components/schemas/StartInventoryRequest/properties/doNotPersistState` |
| Vulcan | `$["applyImpinjGen2X"]` | `#/components/schemas/StartInventoryRequest/properties/applyImpinjGen2X` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 []: no body schema. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Add the implemented FX9600 applyImpinjGen2X flag without adding scanType or assuming the dedicated Gen2X endpoint exists.

#### 6. Required action

Add the Starfish request property and example, after confirming boolean type, default and per-start semantics.

#### 7. Developer question

Is applyImpinjGen2X boolean on FX9600, what is its default when omitted, and is its effect limited to the current start/session? Provide a successful request.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 has applyImpinjGen2X in payload and does not have scanType, documentation needs to be updated for applyImpinjGen2X for FX9600
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: Starfish “No changes required” conflicts with its own explicit documentation-update comment. Endpoint Gen2X support remains a separate question.

</details>

**Record decision:** tracker Issue ID `F-025`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-33"></a>
## /cloud/status

<a id="f-026"></a>
### F-026 — GET — Starfish Gen2X status missing

**Schema Update · Open · Platform: Starfish**  
Source: `Field review` source row 26 (Excel row 32); original endpoint `GET /cloud/status`.

#### 1. Issue

Starfish reviewer confirms impinjGen2X in status and no ble. Its current normal status schema lacks impinjGen2X; Vulcan defines isActive and feature.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/status`
  - Success response body
    - **Target field absent from declared properties**. Absence alone does not prove firmware rejection.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/status`
  - Success response body
    - `$` — type="object"
      - **`impinjGen2X`** — type="object"
        - **`isActive`** — type="boolean"
        - **`feature`** — type="string"; enum=["none","fastId","tagFocus","tagProtect","tagQuieting"]
      - **`ble`** — type="object"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `No target field declared` | `#/paths/~1cloud~1status/get` |
| Vulcan | `$["impinjGen2X"]` | `#/components/schemas/GetStatusResponse/properties/impinjGen2X` |
| Vulcan | `$["impinjGen2X"]["isActive"]` | `#/components/schemas/GetStatusResponse/properties/impinjGen2X/properties/isActive` |
| Vulcan | `$["impinjGen2X"]["feature"]` | `#/components/schemas/GetStatusResponse/properties/impinjGen2X/properties/feature` |
| Vulcan | `$["ble"]` | `#/components/schemas/GetStatusResponse/properties/ble` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type/constraints not specified here, 202 [application/json]: type="object". |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Describe the actual FX9600 Gen2X status without copying BLE or unverified feature names.

#### 6. Required action

Add impinjGen2X to the Starfish normal response branch after capturing its field names and enum values.

#### 7. Developer question

Does FX9600 return impinjGen2X.isActive and feature with the same spellings/values as Vulcan? Does isActive mean configuration loaded or feature enabled?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 reports impinjGen2X in status but not ble
> Needed: yes
> Fix: FX9600 documetation needs to be updated
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: The comment resolves Gen2X/BLE only, not the entire status schema.

</details>

**Record decision:** tracker Issue ID `F-026`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-026a"></a>
### F-026A — GET — Status spelling, NTP and upgrade variants

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 26 (Excel row 32); original endpoint `GET /cloud/status`.

#### 1. Issue

Starfish spells radioActivitiy, permits unknown, uses ntp object or NOT_CONFIGURED, and has a separate firmware-progress response branch. Vulcan spells radioActivity, has object-only ntp, and lacks the upgrade branch. Gen2X/BLE comments do not resolve these differences.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/status`
  - Success response body
    - `$` — type/constraints not specified here
      - `/oneOf[0]` — type="object"; required=["uptime","systemTime","ram","flash","cpu","radioConnection","antennas","temperature","radioActivitiy","powerSource","powerNegotiation","ntp"]
        - `cpu` — type="object"
          - **REVIEW `user`** — type="integer"
          - **REVIEW `system`** — type="integer"
        - **REVIEW `temperature`** — type="integer"; format="int32"
        - **REVIEW `radioActivitiy`** — type="string"; enum=["active","inactive","unknown"]
        - **REVIEW `ntp`** — type/constraints not specified here
          - **REVIEW `/oneOf[0]`** — type="object"; required=["offset","reach"]
            - **REVIEW `offset`** — type="number"; format="float"
            - **REVIEW `reach`** — type="integer"
          - **REVIEW `/oneOf[1]`** — type="string"; enum=["NOT_CONFIGURED"]
        - `interfaceConnectionStatus` — type="object"; required=["data"]
          - `data` — type="array"
            - `[]` — type="object"; required=["connectionError","connectionStatus","description","interface"]
              - **REVIEW `connectionStatus`** — type="string"; enum=["Connected","disconnected"]
      - `/oneOf[1]` — type="object"
        - **REVIEW `status`** — type="string"; enum=["rebooting"]
        - **REVIEW `imageDownloadProgress`** — type="number"; minimum=0; maximum=100
        - **REVIEW `overallUpdateProgress`** — type="number"; minimum=0; maximum=100
        - **REVIEW `updateProgressDetails`** — type="object"
          - **REVIEW `os`** — type="number"; minimum=0; maximum=100
          - **REVIEW `rootFileSystem`** — type="number"; minimum=0; maximum=100
          - **REVIEW `applications`** — type="number"; minimum=0; maximum=100
          - **REVIEW `radioFirmware`** — type="number"; minimum=0; maximum=100
          - **REVIEW `platform`** — type="number"; minimum=0; maximum=100
      - **REVIEW `status`** — type="string"; enum=["rebooting"]
      - **REVIEW `imageDownloadProgress`** — type="number"; minimum=0; maximum=100
      - **REVIEW `overallUpdateProgress`** — type="number"; minimum=0; maximum=100
      - **REVIEW `updateProgressDetails`** — type="object"
        - **REVIEW `os`** — type="number"; minimum=0; maximum=100
        - **REVIEW `rootFileSystem`** — type="number"; minimum=0; maximum=100
        - **REVIEW `applications`** — type="number"; minimum=0; maximum=100
        - **REVIEW `radioFirmware`** — type="number"; minimum=0; maximum=100
        - **REVIEW `platform`** — type="number"; minimum=0; maximum=100

**Vulcan — FXR60 / FXR90**

- `GET /cloud/status`
  - Success response body
    - `$` — type="object"
      - `cpu` — type="object"
        - **REVIEW `system`** — type="number"
        - **REVIEW `user`** — type="number"
      - `interfaceConnectionStatus` — type="object"
        - `data` — type="array"
          - `[]` — type="object"
            - **REVIEW `connectionStatus`** — type="string"
      - **REVIEW `ntp`** — type="object"
        - **REVIEW `offset`** — type="number"
        - **REVIEW `reach`** — type="number"
      - **REVIEW `radioActivity`** — type="string"; enum=["active","inactive"]
      - **REVIEW `temperature`** — type="number"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["cpu"]["user"]` | `#/components/schemas/cpustats.v1/properties/user` |
| Starfish | `$["cpu"]["system"]` | `#/components/schemas/cpustats.v1/properties/system` |
| Starfish | `$["temperature"]` | `#/components/schemas/readerstats.v1/properties/temperature` |
| Starfish | `$["radioActivitiy"]` | `#/components/schemas/readerstats.v1/properties/radioActivitiy` |
| Starfish | `$["ntp"]` | `#/components/schemas/readerstats.v1/properties/ntp` |
| Starfish | `$["ntp"]` | `#/components/schemas/ntpstats.v1` |
| Starfish | `$["ntp"]["offset"]` | `#/components/schemas/ntpstats.v1/properties/offset` |
| Starfish | `$["ntp"]["reach"]` | `#/components/schemas/ntpstats.v1/properties/reach` |
| Starfish | `$["ntp"]` | `#/components/schemas/readerstats.v1/properties/ntp/oneOf/1` |
| Starfish | `$["interfaceConnectionStatus"]["data"][*]["connectionStatus"]` | `#/components/schemas/interfaceConnectionStatus.v1/properties/data/items/properties/connectionStatus` |
| Starfish | `$["status"]` | `#/components/schemas/readerupgradestatus.v1/properties/status` |
| Starfish | `$["imageDownloadProgress"]` | `#/components/schemas/readerupgradestatus.v1/properties/imageDownloadProgress` |
| Starfish | `$["overallUpdateProgress"]` | `#/components/schemas/readerupgradestatus.v1/properties/overallUpdateProgress` |
| Starfish | `$["updateProgressDetails"]` | `#/components/schemas/readerupgradestatus.v1/properties/updateProgressDetails` |
| Starfish | `$["updateProgressDetails"]["os"]` | `#/components/schemas/readerupgradestatus.v1/properties/updateProgressDetails/properties/os` |
| Starfish | `$["updateProgressDetails"]["rootFileSystem"]` | `#/components/schemas/readerupgradestatus.v1/properties/updateProgressDetails/properties/rootFileSystem` |
| Starfish | `$["updateProgressDetails"]["applications"]` | `#/components/schemas/readerupgradestatus.v1/properties/updateProgressDetails/properties/applications` |
| Starfish | `$["updateProgressDetails"]["radioFirmware"]` | `#/components/schemas/readerupgradestatus.v1/properties/updateProgressDetails/properties/radioFirmware` |
| Starfish | `$["updateProgressDetails"]["platform"]` | `#/components/schemas/readerupgradestatus.v1/properties/updateProgressDetails/properties/platform` |
| Vulcan | `$["cpu"]["system"]` | `#/components/schemas/GetStatusResponse/properties/cpu/properties/system` |
| Vulcan | `$["cpu"]["user"]` | `#/components/schemas/GetStatusResponse/properties/cpu/properties/user` |
| Vulcan | `$["interfaceConnectionStatus"]["data"][*]["connectionStatus"]` | `#/components/schemas/GetStatusResponse/properties/interfaceConnectionStatus/properties/data/items/properties/connectionStatus` |
| Vulcan | `$["ntp"]` | `#/components/schemas/GetStatusResponse/properties/ntp` |
| Vulcan | `$["ntp"]["offset"]` | `#/components/schemas/GetStatusResponse/properties/ntp/properties/offset` |
| Vulcan | `$["ntp"]["reach"]` | `#/components/schemas/GetStatusResponse/properties/ntp/properties/reach` |
| Vulcan | `$["radioActivity"]` | `#/components/schemas/GetStatusResponse/properties/radioActivity` |
| Vulcan | `$["temperature"]` | `#/components/schemas/GetStatusResponse/properties/temperature` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type/constraints not specified here, 202 [application/json]: type="object". |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Match actual status responses during normal operation, no NTP and firmware upgrade; retain intentional platform variants.

#### 6. Required action

Capture these states on both platforms before correcting names or broadening schemas. Check numeric constraints and connection-status case too.

#### 7. Developer question

Which spelling is actually emitted by FX9600: radioActivitiy or radioActivity? How does FXR represent unconfigured NTP and upgrade progress? Are unknown and Connected/disconnected case variants real wire values?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 reports impinjGen2X in status but not ble
> Needed: yes
> Fix: FX9600 documetation needs to be updated
>
> Kamali:
> Not provided in the pasted Kamali review.

Source limitation: The comment resolves Gen2X/BLE only, not the entire status schema.

</details>

**Record decision:** tracker Issue ID `F-026A`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-34"></a>
## /cloud/stop

<a id="f-010"></a>
### F-010 — PUT — Intentional stop payload difference

**No Change Required · Resolved · Platform: Both**  
Source: `Field review` source row 10 (Excel row 16); original endpoint `PUT /cloud/stop`.

#### 1. Issue

Starfish has no stop body. Vulcan optionally accepts scanType array with ble/rfid values and minItems 1. Both reviewer comments explain BLE as the reason.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/stop`
  - Parameters: none declared
  - Request body: **not declared**

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/stop`
  - Parameters: none declared
  - JSON request body
    - **`$`** — type="object"
      - **`scanType`** — type="array"; minItems=1
        - **`[]`** — type="string"; enum=["ble","rfid"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `No target field declared` | `#/paths/~1cloud~1stop/put` |
| Vulcan | `$` | `#/components/schemas/StopInventoryRequest` |
| Vulcan | `$["scanType"]` | `#/components/schemas/StopInventoryRequest/properties/scanType` |
| Vulcan | `$["scanType"][*]` | `#/components/schemas/StopInventoryRequest/properties/scanType/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Both reviewer comments directly confirm the difference; the schemas match those comments.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: none declared. Success: 200 []: no body schema. |
| Vulcan | Parameters: none declared. Request: type="object". Success: 200 []: no body schema. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Keep bodyless FX9600 stop and the documented Vulcan scan selector.

#### 6. Required action

Retain the platform-specific request contracts. Do not add BLE or scanType to FX9600 to force parity.

#### 7. Developer question

None for this documented platform difference.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 doest not accept payload for stop
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Why: support for ble added in vulcan.so scanType field added
> Needed: yes this is needed

</details>

**Record decision:** tracker Issue ID `F-010`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-35"></a>
## /cloud/supportedStandardList

<a id="f-027"></a>
### F-027 — GET — Supported standards region location and enum

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 27 (Excel row 33); original endpoint `GET /cloud/supportedStandardList`.

#### 1. Issue

Starfish GET uses optional JSON body region with enum [Argentina]. Vulcan uses optional query region:string and links the supported-region list. The current sources establish a real location difference.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/supportedStandardList`
  - Parameters: none declared
  - JSON request body
    - **REVIEW `$`** — type="object"
      - **REVIEW `region`** — type="string"; enum=["Argentina"]

**Vulcan — FXR60 / FXR90**

- `GET /cloud/supportedStandardList`
  - Parameters
    - **query `region`** — {"type": "string"}; required=none
  - Request body: **not declared**

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$` | `#/components/schemas/SupportedRegionStandard_command` |
| Starfish | `$["region"]` | `#/components/schemas/SupportedRegionStandard_command/properties/region` |
| Vulcan | `query region` | `#/paths/~1cloud~1supportedStandardList/get/parameters/0` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object". |
| Vulcan | Parameters: query region. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Preserve the accepted parameter location and actual allowed country set per platform.

#### 6. Required action

Obtain the missing Kamali review and FX9600 requests for multiple countries. Correct an example-as-enum error only if confirmed.

#### 7. Developer question

Does FX9600 require region in the GET JSON body while FXR accepts it in query? Is Argentina the only allowed value or an incorrectly constrained example? What happens when region is omitted?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 document is matching firmware
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

</details>

**Record decision:** tracker Issue ID `F-027`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="f-028"></a>
### F-028 — GET — Standards response case and boolean representation

**Developer Clarification · Open · Platform: Both**  
Source: `Field review` source row 28 (Excel row 34); original endpoint `GET /cloud/supportedStandardList`.

#### 1. Issue

Starfish uses channelData:number[] and booleans. Vulcan uses channeldata:string[], string enums "true"/"false" for configurability and adds isChannelSelectable. The difference goes beyond field presence.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/supportedStandardList`
  - Success response body
    - `$` — type="object"
      - **REVIEW `SupportedStandards`** — type="array"
        - **REVIEW `[]`** — type="object"
          - **REVIEW `StandardName`** — type="string"; default="UNDEFINED"
          - **REVIEW `isLBTConfigurable`** — type="boolean"; default=false
          - **REVIEW `channelData`** — type="array"
            - **REVIEW `[]`** — type="number"
          - **REVIEW `isHoppingConfigurable`** — type="boolean"; default=false

**Vulcan — FXR60 / FXR90**

- `GET /cloud/supportedStandardList`
  - Success response body
    - `$` — type="object"
      - **REVIEW `SupportedStandards`** — type="array"
        - **REVIEW `[]`** — type="object"
          - **REVIEW `StandardName`** — type="string"
          - **REVIEW `channeldata`** — type="array"
            - **REVIEW `[]`** — type="string"
          - **REVIEW `isChannelSelectable`** — type="string"; enum=["true","false"]
          - **REVIEW `isHoppingConfigurable`** — type="string"; enum=["true","false"]
          - **REVIEW `isLBTConfigurable`** — type="string"; enum=["true","false"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `$["SupportedStandards"]` | `#/components/schemas/SupportedStandardList_response/properties/SupportedStandards` |
| Starfish | `$["SupportedStandards"][*]` | `#/components/schemas/SupportedStandardList_response/properties/SupportedStandards/items` |
| Starfish | `$["SupportedStandards"][*]["StandardName"]` | `#/components/schemas/SupportedStandardList_response/properties/SupportedStandards/items/properties/StandardName` |
| Starfish | `$["SupportedStandards"][*]["isLBTConfigurable"]` | `#/components/schemas/SupportedStandardList_response/properties/SupportedStandards/items/properties/isLBTConfigurable` |
| Starfish | `$["SupportedStandards"][*]["channelData"]` | `#/components/schemas/SupportedStandardList_response/properties/SupportedStandards/items/properties/channelData` |
| Starfish | `$["SupportedStandards"][*]["channelData"][*]` | `#/components/schemas/SupportedStandardList_response/properties/SupportedStandards/items/properties/channelData/items` |
| Starfish | `$["SupportedStandards"][*]["isHoppingConfigurable"]` | `#/components/schemas/SupportedStandardList_response/properties/SupportedStandards/items/properties/isHoppingConfigurable` |
| Vulcan | `$["SupportedStandards"]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards` |
| Vulcan | `$["SupportedStandards"][*]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards/items` |
| Vulcan | `$["SupportedStandards"][*]["StandardName"]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards/items/properties/StandardName` |
| Vulcan | `$["SupportedStandards"][*]["channeldata"]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards/items/properties/channeldata` |
| Vulcan | `$["SupportedStandards"][*]["channeldata"][*]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards/items/properties/channeldata/items` |
| Vulcan | `$["SupportedStandards"][*]["isChannelSelectable"]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards/items/properties/isChannelSelectable` |
| Vulcan | `$["SupportedStandards"][*]["isHoppingConfigurable"]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards/items/properties/isHoppingConfigurable` |
| Vulcan | `$["SupportedStandards"][*]["isLBTConfigurable"]` | `#/components/schemas/GetSupportedstandardlistResponse/properties/SupportedStandards/items/properties/isLBTConfigurable` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository schema and original reviewer statements; no live reader test supplied.

| Platform | Full operation context |
|---|---|
| Starfish | Parameters: none declared. Request: type="object". Success: 200 [application/json]: type="object". |
| Vulcan | Parameters: query region. Request: none declared. Success: 200 [application/json]: type="object". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Document exact key case, element types and JSON boolean-versus-string values for each model.

#### 6. Required action

Capture both responses and align schemas to the wire. Do not automatically normalize names or coerce JSON types in firmware.

#### 7. Developer question

Are FXR isLBTConfigurable/isHoppingConfigurable/isChannelSelectable strings, and are channeldata values strings? Does FX9600 omit isChannelSelectable and use numeric channelData with booleans?

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Why: FX9600 document is matching firmware
> Needed: yes
> Fix: No changes required
>
> Kamali:
> Not provided in the pasted Kamali review.

</details>

**Record decision:** tracker Issue ID `F-028`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-36"></a>
## /cloud/updatePassword

<a id="e-027"></a>
### E-027 — PUT — Intentional platform scope

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 27 (Excel row 33); original endpoint `PUT /cloud/updatePassword`.

#### 1. Issue

Vulcan defines the operation and Starfish does not. Reviewer feedback supports that platform boundary.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `PUT /cloud/updatePassword`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `PUT /cloud/updatePassword`
  - Parameters: none declared
  - JSON request body
    - `$` — type="object"; required=["userName","currentPassword","newPassword"]
      - **`userName`** — type="string"; enum=["admin","rfidadm"]
      - **`currentPassword`** — type="string"
      - **`newPassword`** — type="string"

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1updatePassword/put` |
| Vulcan | `$["userName"]` | `#/components/schemas/UpdatePasswordRequest/properties/userName` |
| Vulcan | `$["currentPassword"]` | `#/components/schemas/UpdatePasswordRequest/properties/currentPassword` |
| Vulcan | `$["newPassword"]` | `#/components/schemas/UpdatePasswordRequest/properties/newPassword` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository path presence and reviewer platform-scope statement. For readerLocation, Starfish explicitly excludes FX9600; Kamali confirms FXR support without independently excluding FX9600.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: type="object"; required=["userName","currentPassword","newPassword"]. Success: 200 [application/json]: type="string". |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current platform-specific API scope.

#### 6. Required action

Keep the endpoint limited to the documented platform. No cross-platform feature implementation is requested by the source comment.

#### 7. Developer question

None unless the product requirement changes. A parity request would need a separate approved requirement.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> firmware confirms FX9600 does not support it.
>
> Kamali:
> not supported in fx9600

</details>

**Record decision:** tracker Issue ID `E-027`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

<a id="endpoint-37"></a>
## /cloud/wifiNetworks

<a id="e-028"></a>
### E-028 — GET — Intentional platform scope

**No Change Required · Resolved · Platform: Both**  
Source: `Endpoint review` source row 28 (Excel row 34); original endpoint `GET /cloud/wifiNetworks`.

#### 1. Issue

Vulcan defines the operation and Starfish does not. Reviewer feedback supports that platform boundary.

#### 2. Schema comparison

**Starfish — FX9600 view of multi-model source**

- `GET /cloud/wifiNetworks`
  - **ABSENT from supplied specification**. Firmware support must come from developer evidence.

**Vulcan — FXR60 / FXR90**

- `GET /cloud/wifiNetworks`
  - Parameters: none declared
  - Success response body
    - `$` — type="object"; required=["availableWifiNetworks"]
      - **`availableWifiNetworks`** — type="array"
        - **`[]`** — type="object"; required=["capabilities","configuration","essid","signalStrength"]

#### 3. Exact location

| Platform | JSON instance path / parameter | Source JSON Pointer |
|---|---|---|
| Starfish | `Operation absent` | `#/paths/~1cloud~1wifiNetworks/get` |
| Vulcan | `$["availableWifiNetworks"]` | `#/components/schemas/GetAvailablewifinetworksResponse/properties/availableWifiNetworks` |
| Vulcan | `$["availableWifiNetworks"][*]` | `#/components/schemas/GetAvailablewifinetworksResponse/properties/availableWifiNetworks/items` |

Files: [Starfish](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR%20series.json), [Vulcan](https://github.com/immanuel2504/Fxr-unification/blob/6a4d78d3f0caba6659d193cb5b3d649be48ac60c/FXR_60-90_rest_api.yaml). The pointer identifies the exact node within that file.

#### 4. Current behavior

**Evidence:** Repository path presence and reviewer platform-scope statement. For readerLocation, Starfish explicitly excludes FX9600; Kamali confirms FXR support without independently excluding FX9600.

| Platform | Full operation context |
|---|---|
| Starfish | Operation absent. |
| Vulcan | Parameters: none declared. Request: none declared. Success: 200 [application/json]: type="object"; required=["availableWifiNetworks"]. |

The compared field contracts are shown in the trees. Reviewer statements establishing or disputing runtime support are preserved below.

#### 5. Expected behavior

Retain the current platform-specific API scope.

#### 6. Required action

Keep the endpoint limited to the documented platform. No cross-platform feature implementation is requested by the source comment.

#### 7. Developer question

None unless the product requirement changes. A parity request would need a separate approved requirement.

<details>
<summary>Original feedback and source limitation</summary>

> Starfish:
> Firmware confirms FX9600 does not support it.
>
> Kamali:
> not supported in fx9600

</details>

**Record decision:** tracker Issue ID `E-028`. Developer response: not yet recorded.

[Back to endpoint navigation](#review-navigation)

---

## Coverage reconciliation

| Source sheet | Source rows | Findings | Unmapped rows |
|---|---:|---:|---:|
| Endpoint review | 32 | 32 | 0 |
| Field review | 28 | 46 | 0 |

Suffixes (for example F-013A) split independent decisions from one source row. They do not count as extra original comments. Every supplied reviewer cell is retained in the original-feedback sheets. All source endpoints remain traceable even where current schema paths differ.
