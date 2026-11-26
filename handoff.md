# MRT-ES-Event-Integration

Consolidated handoff, updated 26 November 2026. Project: Member Resolution and Employer Servicing provider-event integration for AustralianSuper.

Sections 14 and 15 add the Member Resolution Phase One review and the 25 November continuation-chat implementation/testing status. Repository/source-system changes were not part of the read-only documentation review; the later test-code work was separately authorized in the continuation chat.

## 1. Purpose and proof boundary

- Consolidate the main conversation, both child chats, current repositories, email, SharePoint, Jira and Confluence before continuing in a new project chat.
- The technical proof of concept forwards provider event bodies from Service Bus through a generic function and application programming interface management into the customer relationship management Event Log.
- Commit intent: forward event messages unchanged, bypassing function deserialization, with destination transformation moved out of the legacy Python function. Configuration-driven consumer onboarding is an intended outcome, not yet a demonstrated capability.
- **Demonstrated historically:** authenticated development ingress and Service Bus publication; thirteen ingress security/schema checks.
- **Not demonstrated:** a complete correlated proof-of-concept run through the function, mapper and egress with a successful destination write. A dedicated end-to-end test has since been added and builds, but has not been run.
- Event Log persistence is not evidence that downstream business processing completed.
- The broader Employer Servicing business solution is context, not automatic expansion of this proof of concept.

## 2. Instructions and settled decisions

1. Keep repositories and source systems read-only until the user explicitly agrees to changes. Do not checkout, fetch, pull, reset, commit, push, deploy or run live tests as part of consolidation.
2. Communicate in concise executive-summary bullets. Present one next step or question per response and wait. Use the structured question tool when a decision is needed.
3. Developer subscriptions belong in [shared-service-bus](C:/git/repos/shared-service-bus), not an application deployment script. This ownership decision is settled.
4. The adopted developer entry is development-only, feature-branch-only and must not be merged into other branches/environments. The current branch name uses the plural `feature/SFB/INT-6649-devloper-sb-subscriptions`; the earlier singular spelling is naming history, not a design conflict.
5. Mapping technology is deferred until actual provider event data is available. Liquid templates, Logic Apps, combinations or other approaches remain possible and may differ by event or requirement.
6. Event-type recognition/routing is future work, not a settled immediate implementation target.
7. Test scope remains work in progress and depends on other teams. The immediate requested sequence is to run the new end-to-end scenario, then prioritize Liquid mapping tests using dummy data and representative variants.
8. [INT-6648](https://australiansuper.atlassian.net/browse/INT-6648) end-to-end means end-to-end **of the proof of concept**, not the complete production project.
9. Use the original Employer Servicing solution design as the primary consolidation design source. Retain the copied page as a duplicate. This does not formally approve the whole design.
10. Skip the broader project test-plan review unless the user reverses that decision.
11. No Service Bus purge operations in application code, test hooks or deployment pipelines unless later explicitly approved.
12. Forwarder authentication is deliberately unfinished proof-of-concept work. Its absence alone is not an agreed current defect or permission to expand production scope.
13. Testing is development-only. Start the local function through Visual Studio debugging with breakpoints; run tests separately. Automated test-host startup is not the agreed workflow.
14. Preserve existing working-tree changes and machine-only debugging customizations. Use short names for new files.
15. For code changed in the continuation, do not hard-code configurable values; read them from configuration and fail explicitly when required settings are missing or blank.
16. Keep every generated test-run HTML report; do not automatically delete reports. The user may purge them manually.
17. In interactive troubleshooting/testing, give one instruction or command per response and wait for the user's result.

## 3. Repository baseline as verified 24 November 2026

Verified read-only on 24 November 2026. Remote references below are locally cached, not freshly fetched.

| Repository | Checked-out branch | Current commit | Working tree |
| --- | --- | --- | --- |
| [members](C:/git/repos/members) | `feature/SFB/BDD-ReQnRoll` | `a70c1760` - more features | One existing test-loader edit; preserved |
| [inf-azure-api-management](C:/git/repos/inf-azure-api-management) | `feature/TS/INT-6649-ESMRT-Eventing-POC` | `87053501` | Clean |
| [shared-service-bus](C:/git/repos/shared-service-bus) | `feature/SFB/INT-6649-devloper-sb-subscriptions` | `99d8333` | Clean |

| Repository | Local master | Cached origin/master | Merge-base with current branch |
| --- | --- | --- | --- |
| members | `db65183b` | `0f904175` | `db65183b` |
| inf-azure-api-management | `8b100caa` | `6e88ae9e` | `31a4d960` |
| shared-service-bus | `78b5845f` | `f2aac381` | `7d8e8b80` |

- Earlier claims that local master equals origin/master are no longer current.
- Use merge-base/three-dot comparisons so unrelated later master changes are excluded.
- Members also contains unrelated staging ancestry. Its full `master...HEAD` comparison is **139 files, +9780/-249**, not all proof-of-concept work.
- The members proof branch `feature/TS/INT-6649-ESMRT-Eventing-POC` is at `df03e324`. Its merge-base with cached staging `INT-FIP-BAU-Y26-R52` is still `9d3c1a4b`.
- Isolated proof-and-test comparison `9d3c1a4b...HEAD`: **26 files, +984/-5**. The test branch's merge-base with the proof branch is `df03e324`.
- Original uppercase `Feature/TS/...` spelling was later changed to lowercase. Do not checkout branches to resolve this naming history.

### Relevant commits and areas

**Members proof branch**

- `ef271539`: generic raw-event forwarding, shared stream handling, two Service Bus triggers, and deployment settings.
- `df03e324` - Testing: derived-stream type fix, seekable-stream rewind, leading-slash forwarder path, a third logging-only debug trigger, and a standalone customer relationship management Liquid fragment.
- Existing assembly scanning registers the new command handler; dependency injection is not missing.

**Members test branch, after the proof branch**

- `5395d991`: set up the behavior-driven test project.
- `e58dac5d`: add feature and steps.
- `9bce002a`, `7ac69a56`: smoke-test/project-option checkpoint work.
- `32f180da`: feature and smoke-test setup.
- `a70c1760`: more features, including the publish feature.
- The solution, test project, features, bindings, clients, configuration, scenario context, event builder, telemetry helpers and reporting configuration form the test delta.
- Existing uncommitted [TestConfiguration.cs](C:/git/repos/members/shared/functionapp-02/tests/AS.FIP.Members.BddTests/Support/Config/TestConfiguration.cs) enables comment skipping and trailing commas. Do not lose or silently commit it.

**Application programming interface management**

- `87053501`: four files, +238. Adds forwarder specifications, operation definition and policy; adds Liquid mapping and a second token-validation block to the shared customer relationship management policy.
- The branch's three-dot comparison still isolates this one proof commit despite local master advancing.

**Shared Service Bus**

- `87f87f7`: generic v1 and developer vTS topic/subscription specifications.
- `0c0d1b6`: adds those topic identifiers to the pipeline.
- `99d8333`: adds the adopted developer enquiry subscription.
- Three commits, four files, +40.

## 4. End-to-end flow and ownership

### Technical proof of concept

```text
Provider event / development test client
  -> POST /member/enquiryevent/v1/messages
  -> application programming interface management: schema and token validation
  -> Service Bus: member-enquiry-create-v1 topic
  -> adopted developer subscription
  -> locally debugged generic .NET function
  -> application programming interface management: /mufg-event
  -> customer relationship management interface
  -> intended Dataverse Event Log entity set: as_eventlogs
```

- The current forwarder policy rewrites to bare `/crm/v1`, not `/crm/v1/as_eventlogs`; that destination hop is not proven.
- Legacy Python processing uses its own subscription and the entity-specific destination path. Parallel subscriptions can both receive an event; a completed cutover or destination duplicate-handling strategy has not been demonstrated.
- Generic v1/vTS topic specifications now exist. The inspected ingress still publishes to the member enquiry topic; a separate generic-topic ingress was not established.
- Event Grid is not established as a hop in this verified proof-of-concept chain. Do not insert it into the sequence from naming or assumptions.
- Existing case ingestion into the operational data platform continues separately; that is not the transport hop from Service Bus to Event Log.

### Broader Employer Servicing design

```text
Provider event
  -> application programming interface management
  -> Service Bus
  -> Function App
  -> application programming interface management
  -> Dataverse Event Log
  -> Power Automate
  -> provider enquiry/document retrieval
  -> associated customer relationship management case/task update
```

- Power Automate consumes stored Event Log entries afterwards. It is not the agreed Service Bus ingestion mechanism.
- Customer relationship management owns parent-case business state. Provider/Pega administration tasks are distinct linked records, not the same cross-system case.
- Employer Servicing extends existing case-ingestion mappings into the data platform without automatically changing Member Resolution modelling or adding future strategic reporting.

## 5. Contracts, settings and resources

### Ingress and event envelope

- Development ingress: `https://apim-dev.australiansuper.net.au/member/enquiryevent/v1/messages`.
- Current ingress policy validates content **before** the token, then uses the Service Bus authentication fragment and topic named value.
- Historical responses: invalid body 400; accepted-schema body without an accepted token 401; accepted authenticated event 201.
- Support-app test tokens target Microsoft Graph, not the support application's own identifier. Configured test scope: `https://graph.microsoft.com/.default`.
- Envelope: `metadata` object; optional-at-schema-root `data` array of objects with string `name` and string `value`.
- Schema requires root `metadata`. Within it, required fields are `subscriptionId`, `id`, `publishedTimeStamp`, `deliveredTimeStamp`, `version`, `contextType`, `contextId`, `entityType`, `changeType`, `systemType`.
- Additional described metadata fields: `subscriberId`, `subjectType`, `subjectId`, `subjectReference`, `deliveredStatus`, `deliveredErrorMessage`.
- Accepted `entityType`: `Enquiry`, `Document`, `Case`.
- Accepted `changeType`: `Create`, `Update`, `Delete`, `AddDocument`, `RemoveDocument`.
- Accepted `systemType`: `ENQ`, `MOL`, `UCM`, `IC`, `BP`, `EOL`, `EXT`.
- `contextType`: `Plan`. `subjectType`: `Plan`, `PlanMember`, `PlanEmployer`.
- Schema timestamp patterns require whole seconds with `Z`; `subjectId` must contain 1-38 digits when present. Earlier guide comparisons allow blank subject/delivery fields in circumstances the schema rejects; these discrepancies are not resolved provider-contract decisions.
- These enums permit individual values, not a verified catalogue of valid real-world event combinations.
- The field `metadata.systemType` is not interchangeable with a `data` entry named `enquirySystemType`.

Sources: [ingress policy](C:/git/repos/inf-azure-api-management/TF/apim/apis/fip/fip-member-enquiry-events-api/v1/policy.xml), [ingress specifications](C:/git/repos/inf-azure-api-management/TF/apim/apis/fip/fip-member-enquiry-events-api/specs.tfvars), [validation schema](C:/git/repos/inf-azure-api-management/TF/apim/global_schemas/member_enquiry_create_schema.json).

### Correlation

- Test run identifier is placed in `metadata.id`, `x-correlation-id`, and Service Bus `MessageId`/`CorrelationId`.
- [IngressClient.cs](C:/git/repos/members/shared/functionapp-02/tests/AS.FIP.Members.BddTests/Support/Clients/IngressClient.cs) explicitly sets `BrokerProperties` for the Service Bus identifiers. This does not prove that the ingress derives them for every provider request.
- The function logs message and correlation identifiers. Its command carries only the body stream; the handler does not explicitly carry the incoming correlation header into the forwarded request.
- The event identifier remains in the raw body; do not describe correlation as entirely lost.

### Service Bus

- Configured development namespace: `sbus-aue-dev-fsn-msg-01`; historical resource group: `rg-aue-dev-fsr-msg-01`.
- Topic: `sbust-dev-fmt-mex-member-enquiry-create-v1`.
- Adopted developer subscription: `sbts-dev-fmt-mex-member-enquiry-create-sfb-1`.
- Standard enquiry subscription generated by current specifications: `sbts-dev-fmt-mex-crm-member-enquiry-create-v1`.
- Generic topics: `sbust-{env}-fmt-mex-generic-event-received-v1` and `sbust-{env}-fmt-mex-generic-event-received-vTS`, with corresponding `sbts-...` subscriptions.
- Current topic specifications configure duplicate detection over three minutes. Subscription module defaults are a three-minute lock and maximum delivery count three.
- Broker duplicate detection is not proof of destination idempotency.
- An unfiltered subscription receives topic traffic, not only test messages.
- Developer subscription existence/Active state was historically verified; it was not queried in Azure during current consolidation.

Sources: [enquiry specifications](C:/git/repos/shared-service-bus/TF/service-bus/topics/tenancy/members/member-enquiry-create-v1/specs.tfvars), [name generation](C:/git/repos/shared-service-bus/TF/service-bus/topics/locals.tf), [subscription defaults](C:/git/repos/shared-service-bus/TF/service-bus/topics/main.tf).

### Function configuration and authentication

- Functions: `mufg_mrt_event_processor`, `mufg_generic_event_processor`, and logging-only `ts_debugging_debugger_dejour`.
- Binding settings: `MUFGMRTEventServiceBusV1:TopicName`/`SubscriptionName`, `MUFGGenericEventServiceBusV1:TopicName`/`SubscriptionName`, `TSEventServiceBusV1:TopicName`/`SubscriptionName`.
- Connection setting: `ServiceBusConnection`.
- Deployment settings use double-underscore separators and lowercase `topicName`/`subscriptionName`. Casing alone was not established as a binding defect.
- Forwarding uses `AppSettings.Apim.BaseUrl`, `AppSettings.Apim.Scope`, existing local client-credential or deployed user-assigned managed-identity flows, customer relationship management request timeout and application programming interface management retry settings.
- The shared client adds authorization, `x-asu-auth` and request-timeout headers.
- Selected local routing fields currently point the enquiry trigger at the adopted developer subscription and development ingress host. Generic v1/vTS binding settings also exist locally.
- No matching per-function disable keys were present in the inspected local `Values`. Runtime function selection/running state was not checked.

### Application programming interface management and destination

- Forwarder identifier: `fip-mufg-event-forwarder-api`; configured base path `mufg-event`; version `v1`; operation `POST /*`; product `fip`; subscription key not required.
- Version-set defaults use segment versioning. The function calls `/mufg-event` without `v1`; deployed routing acceptance remains unverified.
- Current forwarder checks for `metadata`, uses internal loopback, and sets `Host` from `InternalApimHost`. Rate limit is five requests per second.
- Ingress named values include `FipMexEnquiryEvent_TopicName`, `FipMexMemberEnquiryCreateClientId`, `FipMexMemberEnquiryCreateAud`, plus shared support/tenant/Service Bus configuration.
- The shared customer relationship management policy uses `CRM_Auth_URL`, tenant and permitted client-application named values, and the proxy fragment.
- Current mapping copies metadata into `as_*` fields and extracts `enquiryCaseType`, `referenceNumber`, `documentId`, `enquiryId` from `data`. It sets `as_integrationdirection = 717620000` and `statuscode = 1`.
- `as_eventdata` is emitted as a nested object. The Member Resolution egress agreement and legacy Python DTO establish a JSON-encoded string as the existing baseline; unchanged Employer Servicing reuse and definitive destination field metadata still need confirmation.
- The customer relationship management specification defines a wildcard entity route; it does **not** explicitly list an `as_eventlogs` operation. The legacy caller and newly reviewed Member Resolution egress agreement both explicitly identify `/crm/v1/as_eventlogs`.
- [INT-6751](https://australiansuper.atlassian.net/browse/INT-6751) explicitly identifies the destination endpoint structure as a dependency on the customer relationship management team.
- Environment aliases need care: application programming interface management `ppd` points to `uat`; its `sit1` topic name uses `sit` despite a `sit1` namespace. Do not normalize these silently.

## 6. Current completion gaps and corrected claims

| Item | Evidence status |
| --- | --- |
| Exact `Stream` type comparison | Fixed in `df03e324` with a derived-stream-compatible type test |
| Validator consuming seekable-stream bytes | Fixed in `df03e324`: original position restored in `finally` |
| Stream retry reuse | Current static risk: same stream reused across new requests without explicit rewind/buffering; not runtime-tested |
| JSON validation | First-token check only; scalars/null accepted; full body/schema not checked by this helper; general non-seekable handling remains constrained |
| Destination route | Current policy still rewrites to bare `/crm/v1`, omitting the entity set |
| Version routing | The original hard-coded path has been replaced by configuration on the later test branch; deployed pipeline setting and deployed route remain unverified |
| Deployment subscription | Members pipeline requests `sbts-{env}-fmt-mex-member-enquiry-create-v1`; shared specifications declare the standard `...-crm-member-enquiry-create-v1` and adopted developer subscription instead |
| Default forwarder deployment selection | Forwarder is absent from the default FIP pipeline `apiIds`; this is not proof it was never manually deployed |
| Caller-token validation order | Customer relationship management policy repeats validation after replacing `Authorization` with the destination token; current static risk, not a newly observed failure |
| Mapping type/escaping and event-specific contract | Existing Member Resolution contract uses serialized-string `as_eventdata`; Employer Servicing reuse and destination metadata not finalized; mapping technology remains deferred |
| Forwarder authentication | Deliberately unfinished proof work; no agreed production-authentication change |
| Publish feature | Committed and present; two telemetry assertion bindings missing |
| Full proof execution | Successful destination write and complete correlated run not demonstrated; new end-to-end scenario builds but has not been run |
| Replay, destination idempotency, dead-letter recovery, cutover and regression | Not demonstrated as complete proof-of-concept behavior; test scope remains unfinalized |

Current implementation sources: [stream transport](C:/git/repos/members/shared/functionapp-02/src/AS.FIP.Core/Connectors/Api/RestClient.cs), [JSON stream check](C:/git/repos/members/shared/functionapp-02/src/AS.FIP.Core/Extensions/JsonExtensions.cs), [triggers](C:/git/repos/members/shared/functionapp-02/src/AS.FIP.Members/GenericFunctions/MUFGEvent/Triggers/MufgEventServiceBusTrigger.cs), [forwarding handler](C:/git/repos/members/shared/functionapp-02/src/AS.FIP.Members/GenericFunctions/MUFGEvent/Handlers/MUFGGenericEventServiceBusMessageHandler.cs), [forwarder policy](C:/git/repos/inf-azure-api-management/TF/apim/apis/fip/fip-generic-mufg-event/v1/policy.xml), [customer relationship management policy](C:/git/repos/inf-azure-api-management/TF/apim/apis/corporate/crm-api/v1/policy.xml), [function deployment settings](C:/git/repos/members/shared/functionapp-02/azure-pipelines/shared-function-app-02-pipeline.yaml).

- November 17 [local as-is documentation](C:/git/repos/INT-6649/poc-doc/INT-6649-ESMRT-Eventing-POC-as-is.md) is historical. Its stream defects, missing infrastructure and absent-test claims are partly superseded. Do not copy them as current facts.
- [EnahancedModelBrainstorm.md](C:/git/repos/INT-6649/poc-doc/EnahancedModelBrainstorm.md) is brainstorming, not an approved target.
- Local diagrams and Confluence diagrams are not guaranteed synchronized; unprovisioned/intended resources must remain distinguished from deployed resources.
- Earlier claims that functions were absent were corrected to "deployed but not running". A later claim that live deployment was broken by proof subscription settings was retracted: those settings were on proof branches, not master, and were absent from the live app/slot at that historical check.
- Current Azure code, policies, app settings, running state and resource existence have not been revalidated.
- The obsolete members inline developer-subscription deployment approach was replaced by shared Terraform specifications. Earlier `sbust-` subscription-name samples and expiry/purge proposals are superseded.

## 7. Historical execution evidence

- Successful development ingress run: `a5d60454-ea76-4318-bf91-32d9d22154a6`.
- Historical telemetry showed ingress request 201, Service Bus publish dependency 201 and correlation traces. This proves ingress-to-Service-Bus publication only.
- Demo chat diagnosed a downstream 404 from the bare destination rewrite. No successful destination write or applied correction is recorded there.
- First developer-subscription pipeline run `985282` timed out waiting for approval.
- Later run `986650` succeeded: one resource added, zero changed/destroyed; subscription verified Active, maximum delivery count three and lock duration three minutes. Other environments were planned but their apply stages were skipped.
- Existing [ingress-report.html](C:/git/repos/members/shared/functionapp-02/tests/AS.FIP.Members.BddTests/bin/Debug/net8.0/TestResults/ingress-report.html) was independently inspected on 24 November:
  - Saved run started `2026-11-23T00:55:30Z`, or 23 November at 11:55 local time.
  - Thirteen scenarios started and finished; all thirteen have recorded step results.
  - All 86 recorded step results are `PASSED`.
  - This corroborates the historical two security and eleven validation successes. It is not a fresh rerun.
- No live test, deployment, purge or destination write was performed during consolidation.

## 8. Test project and observability

- Project: [AS.FIP.Members.BddTests](C:/git/repos/members/shared/functionapp-02/tests/AS.FIP.Members.BddTests).
- Current package/platform baseline: .NET 8, Reqnroll 3.3.4, xUnit v3 3.2.2, Azure Monitor Query 1.7.1; repository core/TokenStore reuse.
- Current source contains two security cases, eleven validation cases, and six publish cases: one telemetry scenario plus five accepted-schema variants.
- The thirteen passing historical cases must not be described as all nineteen current cases passing.
- [IngressPublish.feature](C:/git/repos/members/shared/functionapp-02/tests/AS.FIP.Members.BddTests/Features/IngressPublish.feature) exists in `a70c1760`. Its two Application Insights assertion bindings remain missing.
- Existing [IngressSteps.cs](C:/git/repos/members/shared/functionapp-02/tests/AS.FIP.Members.BddTests/StepDefinitions/IngressSteps.cs) covers sending, status/body assertions and correlation echo, not telemetry validation.
- Configuration loading, mutable event builder, scenario run context, dependency injection, serial execution, scenario output and HTML reporting exist.
- Telemetry query/polling helpers exist. Defaults: 360-second timeout, 15-second polling, two-hour query lookback.
- Query application programming interface management telemetry in `appi-aue-dev-api-02` and function telemetry in `appi-aue-dev-fmb-mex-001`.
- Resource-scoped Log Analytics queries worked historically where direct Kusto proxy connectivity did not.
- Telemetry assertions should distinguish no evidence from an observed wrong result; bounded configurable waiting is the recorded expectation, not fully implemented bindings.
- No current Service Bus peek check or complete destination-completion scenario was found in the handwritten test project.
- A running consumer can complete a message before a peek sees it. Publish-only peeking and end-to-end destination evidence are different proof boundaries.
- Peek checks should locate the exact message and verify correlation/body without removing it. No purge implementation is approved.
- Test-project safeguards include nullable checking, warning/analyzer errors, package auditing and project-only dependency overrides. Existing `CA1716` exception addresses the repository namespace prefix.
- Visual Studio previously overwrote external project-file edits; the user selected Copy always for settings output.
- The final local-settings parser change remains uncommitted. Current folder naming is `Support/Config`, not older `Configuration` descriptions.
- The report is generated under build output and overwritten by later runs.
- A machine-only conditional logging helper/project-user link was used historically. Git's local clean filter still strips single-line `LocalLog.` calls, so physical source reads matter; do not recreate or remove these aids automatically.

## 9. Primary Employer Servicing design and resolved interpretation

Primary source: [original interim solution design](https://australiansuper.atlassian.net/wiki/spaces/PGII/pages/2936406044), now version 72 and explicitly marked APPROVED. This supersedes the earlier version-70 approval-state snapshot.

- Earlier comparison: [copied design](https://australiansuper.atlassian.net/wiki/spaces/PGII/pages/3371664040), version 1, and original version 70 had identical normalized visible text: 2,082 lines.
- That comparison matched all 21 embedded macros and 83 ordered hyperlink targets. Copied diagram references referred to original-hosted resources; the copy had no comments. Version 72 has not been recompared with the copy.
- Earlier original review coverage: all 27 top-level threads and 30 replies. Twenty-three threads resolved; four open statuses comprised two endorsements and two genuinely unanswered questions. Comment statuses were not freshly reverified in the version-72 read.
- Version 72 has populated approval rows. Published approval does not establish a complete provider-confirmed implementation specification or deployed proof behavior.
- Inbound ingestion interpretation is Service Bus -> Function App -> interface management -> Dataverse Event Log; Power Automate follows persistence.
- Interim provider notifications: `Enquiry/Update` and `Enquiry/AddDocument`, covering defined case codes rather than dynamically subscribing per case.
- External enquiry-creation notifications are excluded from this interim business scope, not all generic-framework capabilities.
- Resolved review and sample use `entityType = Enquiry`, `changeType = AddDocument`; one published trigger sentence still says `Document/AddDocument`.
- A later enquiry update containing new documents gets a new document collection. A partial-upload retry may reuse the same unlocked, unexpired collection. Updates without new documents skip document steps.
- Email synchronization exposes messages for manual triage/case creation. Automated case creation relates to the internal escalation form, not general mailbox ingestion.
- The escalation channel is an AustralianSuper Microsoft Forms process for contact-centre staff, not an employer-facing microservice.
- Direct Wrkr assignment/integration and automated Amazon Connect telephony are not established interim scope. Form file attachments are excluded; a recording reference/link can be included under existing access rules.
- Earlier unanswered review questions concerned business meaning of availability and existing reconciliation/dead-letter/failed-message handling. The Member Resolution operational guide now supplies documented daily dead-letter review and manual resubmission; Employer Servicing applicability and live controls remain unverified.
- Daily source/destination reconciliation and discrepancy flags after 24 hours remain proposed, not verified existing controls. Provider historical-message retrieval is an available documented capability, not evidence of a deployed reconciliation job.

## 10. Business requirements and traceability

Sources: [functional requirements](https://australiansuper.atlassian.net/wiki/spaces/PGII/pages/2699427868), version 30, all eight comments; [detailed business requirements](https://australiansuper.atlassian.net/wiki/spaces/PGII/pages/2732851232), version 22, all fourteen comments; [test traceability](https://australiansuper.atlassian.net/wiki/spaces/PGII/pages/3389653397), version 1, full text/no comments.

### Business expectations, not automatically approved proof tests

- Detailed baseline: fifty interim system requirements, eight interim manual requirements and eight future-only rows. Business release notes/sign-offs exist; these are not technical event-contract approval.
- Parent cases and linked provider administration tasks have independent workflows, statuses and service-level tracking. Retain external task identifiers and associate returning updates/documents/outcomes correctly.
- Customer relationship management remains master for parent-case ownership/status; provider parties do not directly own that parent record.
- Updates need origin, timestamp, permissions and stale-information visibility. "Near real time" is not a finalized measurable synchronization interval.
- Outbound requests need acknowledgement, retained originating reference and case-history evidence.
- Provider completion must not automatically close the parent case. An agent validates and closes it with a reason; audit/field history record user, time and old/new values.
- Failures must be visible, mark requests Failed, retain retry capability and lose no data.
- Agent-initiated linking/merging and reopening are specified; automatic duplicate blocking or email-triggered reopening is not thereby finalized.
- Roles and self-allocation are partly defined: team collaboration/allocation, restricted read-only roles and limited existence checks. Exact field-level configuration is not all settled.
- Case/task service levels are separate. Pending pauses case timing and tracks pended time. Follow-up reminders are specified every two days; business/calendar-day interpretation and exact contractual thresholds depend on the Services Schedule.
- Central provider review/internal assignment is described, not direct assignment from customer relationship management to Wrkr.
- Interim calling is manual; telephony and other future integration rows do not expand current proof scope.
- Candidate business tests include association, ownership/status, audit, acknowledgement/reference, failure/retry and manual closure. They are candidates pending team dependencies, not an agreed final suite.

### Unresolved source inconsistencies

- Functional assumption `AS.003` excludes employer-data writeback, but closed question 35 requires writeback to Aaspire.
- Initial email intake is manual, but unmatched-email fallback wording implies automatic case creation. A provisional answer about reopening closed cases does not settle automatic behavior.
- Older escalation guidance varies between discretion, user-set flag/mandatory reason, and revised `REQ39` status-triggered escalation with timestamp and unchanged priority.
- Detailed requirements partly resolve older questions about merging, reopening, roles and manual calling. Do not repeat all old open questions as wholly unanswered.
- Identifier format, closure picklists, detailed status lists, timing/concurrency and some form-field types remain incomplete or inconsistent. An apparent numeric Employer Name field is not an event-contract rule.
- Comments indicate a Services Schedule was shared by email; incomplete retrieved details do not prove it does not exist.

### Requirement mapping and test evidence

- The workbook's `UPDATED Business Requirements` sheet is the interim baseline; historical/future sheets must not be substituted.
- Workbook old-to-new mapping is omitted from Confluence: updated `REQ49` -> old `REQ1`; `REQ13` -> old `REQ5`; `REQ14` -> old `REQ6`; `REQ39` -> revised old `REQ3`; `REQ40` -> revised old `REQ2`.
- Selected text comparisons are verified; binary identity/current remote workbook version is not established.
- Two future rows reuse `REQ67`. Some displayed Jira references navigate to another issue; Templates and Outbound Call labels/targets are interchanged.
- Traceability page matches all fifty interim system identifiers and per-requirement Jira hyperlink target sets.
- **All fifty linked-test cells are empty.** This is requirement-to-story mapping, not populated test traceability or execution evidence; it does not mean no tests exist elsewhere.
- Document "Done" annotations are snapshots, not current independent execution evidence.
- The project test plan was deliberately skipped; do not resume it as an automatic backlog item.

## 11. Provider documentation and contract dependencies

- [Event guide extraction](./event.txt), version 0.18: generic metadata/name-value envelope, but the Member Resolution phase table is empty; no complete Member Resolution/Employer Servicing event-specific catalogue.
- Delivery guidance recommends acknowledgement within 500 milliseconds and describes a five-second delivery timeout. No measured proof latency compliance was established.
- [Enquiry guide extraction](./enq.txt), version 2.8: "ES" means **Enquiry Service**, not Employer Servicing. Document operations were removed from version 2.5 onward; version 2.4 is historical.
- `FileCollectionGUID` was reintroduced only for backward compatibility and is not for new consumers. This does not resolve the separate document-service contract.
- One original Enquiry guide in the local document folder was unreadable; later accessible extracted guide content is the basis of the version 2.8 findings.
- Case-code workbook provides fourteen business codes: `AAS`, `CRS`, `CFS`, `CNR`, `MMA`, `MME`, `FCV`, `TRN`, `ONB`, `PAR`, `SAF`, `WKR`, `OTH`, `OFQ`.
- Version 72 now includes `EMPS`/`EMPSP`, `BF`, `CH` and all fourteen case codes in its activity table. Subscription examples still use `ES`/`BPS` and varying case codes/subject types. Values exist, but their definitive field/filter reconciliation is not established.
- The older risk/action workbook marked payloads/endpoints, event schemas, subscriber types and system/case codes pending. Some later documents supply more information; old "not supplied" markers are not proof of current total absence.
- Still needed to finalize Employer Servicing-specific work: confirmed provider registration/environment, reconciled filter codes/semantics, valid representative payloads and destination-contract differences, consumer acceptance and measurable timing. Member Resolution now supplies the generic management protocol, filter patterns, delivery envelope, destination entity route and serialized-event baseline; these are not wholly missing.

## 12. Source inventory and coverage

Relative evidence links resolve beside this handoff. If this file is opened elsewhere, use the [original evidence directory](C:/Users/sfbotros/.copilot/session-state/b05d0b77-8ff8-4450-aaec-2a955d97a43a/files). Essential decisions and findings are embedded here; a new chat must not assume access to the old session's isolated database.

| Source | Coverage and limitations | Evidence |
| --- | --- | --- |
| Email | 87 keyword-matching Archive messages since 1 October 2026; bodies saved. Broad matches include peripheral messages. Separate sent-date search found no relevant sent email. Not an exhaustive attachment/mailbox review. | [Inventory](./email-inventory.csv), [saved bodies](./emails/) |
| Approved email attachment | Kick-off presentation: fourteen slides and eleven speaker-note files reviewed. Supplies objectives, responsibilities and milestones, not event contracts. Email/SharePoint copies differ in size; binary identity not assumed. | [Text](./kickoff.txt), [presentation](./kickoff.pptx) |
| SharePoint | 1,621 files inventoried across two synced libraries: sixteen and 1,605 files. Selected current project folder: seventeen files. Fourteen text-extracted initially; process-flow labels decoded later. Project plan and meeting recording not read. | [Inventory](./sp-inv.csv), [selected-document text](./sp-text.txt) |
| Process-flow document | One large vector page; 386 labels extracted from 397 text operations. Business process map, not event contract. Every arrow connection was not visually verified. | [Labels](./flow-text.txt) |
| Local project documents | Accessible guides, workbooks and presentations reviewed to their recorded extraction depth; archive/protected files are not all fully read. | [Folder](C:/git/repos/ES-FIP-CRM), [extractions](./es-fip-crm-text/) |
| Jira | Forty-four issues inventoried, including discovery epic and thirty-nine child issues, plus four customer relationship management stories. Read depth varies; metadata-only items are not full reviews. | [Combined inventory](./atl.csv) |
| Confluence | Forty-six pages inventoried after adding twenty Member Resolution sources. Thirteen folder descendants and six linked references read as full Markdown bodies; primary Member Resolution business design read in targeted sections. Diagrams/comments not exhaustively verified. Existing search-metadata entries remain partial; saved Employer Servicing design extraction is the older version-70 snapshot. | [Combined inventory](./atl.csv), [original design text](./sd-a.txt), [copied text](./sd-b.txt), section 14 |
| Child conversations | All fifteen demo-chat turns and all 120 testing-chat turns read from complete local histories. Original attachments and every historical tool log were not independently reread. | Links and history identifiers below |
| Current code | Three repository baselines and relevant physical source/policies/tests/configuration inspected on 24 November. No current Azure deployment validation. | Repository links above |

### Key tracked work, as inventoried on 24 November

- [INT-6580](https://australiansuper.atlassian.net/browse/INT-6580): discovery/impact epic, In Progress.
- [INT-6647](https://australiansuper.atlassian.net/browse/INT-6647): provider subscriber/event strategy, New.
- [INT-6648](https://australiansuper.atlassian.net/browse/INT-6648): generic framework, New; user-owned proof end-to-end boundary.
- [INT-6649](https://australiansuper.atlassian.net/browse/INT-6649): Configuration Driven Consumer Onboarding, Done; 23 November comment still anticipates an end-to-end approval presentation.
- [INT-6751](https://australiansuper.atlassian.net/browse/INT-6751): destination case schema, In Progress; team dependency.
- [INT-6843](https://australiansuper.atlassian.net/browse/INT-6843): showcase, Done; not independent proof approval.
- [INT-6962](https://australiansuper.atlassian.net/browse/INT-6962): developer setup, Done.
- [INT-6963](https://australiansuper.atlassian.net/browse/INT-6963): proof testing/validation, In Progress; development end-to-end demonstration.
- [ESCRM-14](https://australiansuper.atlassian.net/browse/ESCRM-14), [ESCRM-15](https://australiansuper.atlassian.net/browse/ESCRM-15), [ESCRM-38](https://australiansuper.atlassian.net/browse/ESCRM-38), [ESCRM-58](https://australiansuper.atlassian.net/browse/ESCRM-58): reviewed outbound administration request, attachments, send and inbound update stories. Story status alone does not prove integration completion.

### Other primary project sources

- [Proof-of-concept page](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/3446407295), version 18: draft for review; text read, diagrams not freshly revalidated; still mentions Liquid mapping.
- [Eventing and consumer onboarding model](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/3357999870), version 3: assumptions and proposed directions, not all agreed outcomes.
- [Reusable proof design](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/3385163790), version 3: placeholder, no substantive outcomes.
- Other architecture originals/copies remain metadata-only; their authority comparison was not completed and should only be pursued if needed for this proof.
- Email stakeholder observations ask whether onboarding requires new code/deployment and how multiple events/complex mappings work. They are questions, not demonstrated results or automatic implementation commitments.
- Broader regression/testing correspondence concerns team ownership and interim architecture. It does not override the user's proof-specific scope decision.
- Local request examples, Postman files and trace query: [poc-test](C:/git/repos/INT-6649/poc-test). Historical diagrams/design material: [poc-doc](C:/git/repos/INT-6649/poc-doc). These are not fresh execution evidence.

### Conversation provenance

- Main conversation source session: `b05d0b77-8ff8-4450-aaec-2a955d97a43a`; host-session identifier `66aa634f-fc3d-4245-b13b-b6db087bb3d0`.
- [Run Code For Demo](agent-host-session://ahp-session/66aa634f-fc3d-4245-b13b-b6db087bb3d0?chat=65aec653-ccab-4cc9-8cd7-0ea23b806986): local history `706750e0-b902-4e24-b634-da4be3e965a0`, turns 0-14.
- [Testing PoC with Postman and VS Code](agent-host-session://ahp-session/66aa634f-fc3d-4245-b13b-b6db087bb3d0?chat=fbed5051-48ba-471e-9c2e-46a087042d99): local history `dc094009-89f0-4b54-9aef-c2a9ecc1859e`, turns 0-119.
- A bounded fifty-turn chat retrieval was not treated as complete history; local history supplied the remainder.
- The initial handoff recorded 79 evidence findings and eight structured decisions in the original session database. Additional Member Resolution findings are consolidated in section 14; the portable inventory preserves the broader source list.

## 13. Continuation in the new chat

- Source discovery and the 24 November repository baseline verification are complete to the coverage above. Continuation-chat code work after that baseline is summarized in section 15.
- The [MRT-ES-Event-Integration continuation chat](agent-host-session://ahp-session/66aa634f-fc3d-4245-b13b-b6db087bb3d0?chat=6105e1bb-0b36-4811-9498-d4173ea75827) has been created within the current host session. Do not create another to repeat this step. Its reading state has not been checked; reread this updated handoff before relying on the earlier snapshot.
- The user has separately authorized the test/report code work recorded in section 15. Do not treat that authorization as approval for architecture changes, mapping choices, purge behavior, live reruns or deployments beyond the specific next validation step agreed with the user.
- First read this handoff and the source inventories. Preserve the settled decisions and corrected claims.
- Then agree one bounded next step with the user for completing the proof-of-concept end-to-end evidence. Establish destination-contract/deployment facts where required; do not reopen broad production scope or automatically resume test-plan review.

Suggested opening message:

> Continue MRT-ES-Event-Integration from the attached handoff. Read it and its linked inventories, preserve all settled decisions and the read-only boundary, and distinguish verified repository facts from historical execution and unfinalized business requirements. Give me one concise next step for completing the proof-of-concept end-to-end evidence, then wait. Do not modify code, deploy or run live tests yet.

## 14. Member Resolution Phase One technical review, 24 November 2026

### Coverage and evidence boundary

- Enumerated all thirteen page descendants of the [Phase One folder](https://australiansuper.atlassian.net/wiki/spaces/INT/folder/1294337689), with no remaining pagination cursor.
- Read their full returned Markdown bodies: requirements, ingress and egress agreements, engineering and as-built designs, active subscription register, subscription-management design, transition/handover/checklist, monitoring, troubleshooting and vendor support.
- Read six linked references as full Markdown bodies: initial subscribe/delete/retrieval specifications, Power Automate prototype/process, earlier Logic App option and event-interface service catalogue. Read subscription/event-flow sections of the primary Member Resolution business design separately.
- These twenty sources and versions are recorded in [atl.csv](./atl.csv): thirteen folder pages plus seven external references. Diagrams, macros, attachments and comment threads were not exhaustively verified. The newly returned page bodies were not separately persisted as extraction files.
- Compared relevant sections of the provider [event guide version 0.18](./event.txt) with current local interface specifications/policy and legacy Python code. This was a documentation/source review, not a live service test.

### Four distinct identifiers

| Identifier | Meaning and allocation |
| --- | --- |
| Provider `subscriberId` | Consumer registration allocated through provider onboarding. Associated arrangements include delivery endpoint, authentication/token endpoints and testing. Creating a filter subscription does not allocate this identifier. |
| Provider `subscriptionId` | Filter-rule registration returned by successful subscription creation as response `id`. Used for deletion and carried in delivered event metadata. A subscriber can have multiple subscriptions. |
| Event `metadata.id` | Identifier of an individual published event/message; not the subscriber or filter-rule identifier. |
| Azure Service Bus subscription name | Internal broker consumer/fan-out resource such as `sbts-...`; owned/provisioned separately through shared Service Bus infrastructure. It is not a provider registration identifier. |

The application identity used for token authentication is also distinct from the provider subscriber identifier.

### Registration and subscription-management direction

1. The provider onboards the consumer and supplies a subscriber identifier and agreed delivery/authentication arrangements.
2. AustralianSuper Integration, or a proposed approved self-service workflow, calls the provider Events API through its interface-management proxy to create filter subscriptions.
3. A matched provider event is delivered in the opposite direction to AustralianSuper's registered ingress endpoint.

The current [proxy specifications](C:/git/repos/inf-azure-api-management/TF/apim/apis/mufg/mufg-events-api/specs.tfvars) define base path `mufg/event`, version `v1` and environment-specific provider backends. The [operation definition](C:/git/repos/inf-azure-api-management/TF/apim/apis/mufg/mufg-events-api/v1/openapispec.json) uses:

```text
POST   /{contextType}/{contextId}/events/subscribers/{subscriberId}/subscriptions
DELETE /{contextType}/{contextId}/events/subscribers/{subscriberId}/subscriptions/{subscriptionId}
GET    /{contextType}/{contextId}/events/subscribers/{subscriberId}/messages
```

With configured segment versioning, the frontend combines `/mufg/event/v1` with those operation paths. The provider backend base already ends in `/v1/contexts`; absence of `/contexts` in the current frontend operation is not, by itself, an incorrect path. Older Confluence examples are not authoritative current frontend URLs.

- Subscribe response: **201**, body containing `id`, the created filter-subscription identifier.
- Unsubscribe response: **204**, no body.
- Subscription request: `metadata` selectors plus optional `data` array of `{"name": "...", "value": "..."}` selectors.
- Context/fund are path parameters. Metadata selectors include `subjectType`, `entityType`, `changeType`, and optionally `systemType`, subject identifier/reference.
- Existing Member Resolution filters use `entityType=Enquiry`, `Create`/`Update`/`AddDocument`, case codes and, in some filters, `enquirySystemType=MRT`.
- Filters may cover one case type or all cases from a system. The register includes both patterns; copying overlapping filters wholesale is not a settled Employer Servicing requirement.
- Provider guide scopes distinguish subscription management (`events.subscribe`) from historical message retrieval (`events.read`). The [current proxy policy](C:/git/repos/inf-azure-api-management/TF/apim/apis/mufg/mufg-events-api/v1/policy.xml) validates the AustralianSuper caller, invokes shared provider-token handling and certificate authentication. Exact deployed scope values and authorization behavior were not verified.

### Documented allocations, not live state

The [active subscriber/subscription register](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/1544618146), version 55, records:

| Environment | Documented provider subscriber identifier |
| --- | --- |
| Development | Blank |
| System integration testing | `8501a152-ecb9-4671-bea8-10d2247ae200` |
| Quality assurance, CAT6 | `4bb49e28-ce1d-428b-b111-6da684871a0d` |
| Quality assurance, Perfg7 | `ce16f3a7-cd79-42f9-9c43-0878c97bcc63` |
| Production | `8d6156ba-4c7d-4c91-8cf1-d44100163d35` |

The register says pre-production/user acceptance is not used for Member Resolution. It records separate subscription identifiers by environment, fund and filter. Multiple provider systems/environments map to quality assurance; do not infer a one-to-one environment mapping. Some displayed strings contain stray quotation/backtick formatting.

Employer Servicing version 72 uses sample subscriber identifier `8b06847f-39a8-4491-bdf8-c0fdff0e6487`, which also occurs in Member Resolution ingress/egress examples. That is not evidence of a newly allocated Employer Servicing registration. Confirm whether Employer Servicing needs a separate registration or reuses an existing one, and confirm the actual target-environment identifier and delivery configuration.

### Delivery envelope and destination contract

The [ingress agreement](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/1341325675), version 14, and newer event guide establish a single pushed event:

```text
{
  "metadata": {
    "subscriberId": "...",
    "subscriptionId": "...",
    "id": "...",
    "publishedTimeStamp": "...",
    "deliveredTimeStamp": "...",
    "version": "1",
    "contextType": "Plan",
    "contextId": "BF",
    "subjectType": "...",
    "entityType": "Enquiry",
    "changeType": "...",
    "systemType": "ENQ"
  },
  "data": [
    {"name": "enquiryCaseType", "value": "..."},
    {"name": "enquiryId", "value": "..."},
    {"name": "referenceNumber", "value": "..."}
  ]
}
```

This is a structural illustration, not a provider-confirmed Employer Servicing fixture. Document events may additionally supply `documentId`.

- `metadata.systemType=ENQ` denotes the emitting service; it is not interchangeable with enquiry-origin `enquirySystemType`, such as `MRT` or the unfinalized Employer Servicing values.
- The ingress agreement documents token authentication, content type, correlation and optional broker properties. Its older `/member/events/v1/messages` frontend differs from the current proof's `/member/enquiryevent/v1/messages`.
- **201 ingress acceptance means Service Bus publication, not completed destination processing.**
- The [egress agreement](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/1340801030), version 9, explicitly specifies `POST /crm/v1/as_eventlogs`.
- It maps provider subscriber/subscription/message identifiers, context/subject/entity/change/system/timestamps and named data into `as_*` fields.
- `as_eventdata` is the full original event serialized into a JSON string, not a nested object. The [legacy DTO](C:/git/repos/members/shared/functionapp/sys_member_crm_enquiry_event/crm_enquiry_dto.py) confirms `json.dumps(event_data)`. The [legacy caller](C:/git/repos/members/shared/functionapp/sys_member_crm_enquiry_event/__init__.py) confirms the entity route and correlation header.
- The egress response example does not consistently correspond to its request. The agreement/source establishes a reuse baseline, not every definitive destination field type or a newly verified successful write.

```text
Provider enquiry change
  -> MUFG Event service matches provider subscription
  -> registered AustralianSuper ingress
  -> Azure Service Bus topic and internal consumer subscription
  -> Function App
  -> interface-management customer relationship management route
  -> Dataverse Event Log
  -> Power Automate retrieves enquiry/documents and updates business records
```

Use the [as-built design](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/1742176345), agreements and actual caller for the implementation-level baseline. This logical boundary flow is not a new live trace.

### Retrieval, support and tooling

- Event guide version 0.18 documents historical retrieval of events up to **30 days old**, including unsuccessful delivery attempts, for reconciliation/recovery.
- Current operation definition includes `subscriptionId`, `subjectType`, `fromTimeStamp`, `toTimeStamp`, `entityType`, `changeType` and `deliveredStatus` query filters. Retrieval response has an `events` array; this is distinct from the single-object push envelope.
- Retrieval is not itself automatic redelivery or proof of a scheduled reconciliation process. Exact live retention/query behavior has not been tested.
- The [troubleshooting guide](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/2058649849) describes central Integration/Postman subscription management and recording returned identifiers in Confluence.
- The [Power Automate management design](https://australiansuper.atlassian.net/wiki/spaces/INT/pages/1775730695) is Draft; prototype/approval/audit descriptions do not establish a deployed self-service tool.
- Monitoring/support documentation covers the broker, function, interface management and destination. Daily dead-letter review, cause correction and manual resubmission are documented. This narrows earlier claims of wholly missing recovery information, but does not verify live controls or authorize any operation.

### Known documentation and contract drift

1. December 2024 operation drafts and the [primary Member Resolution design](https://australiansuper.atlassian.net/wiki/spaces/PGII/pages/1162706945), version 87, contain older `events`-array push examples, `timeStamp`, `enquiryTypeId` and `/link/...` routes.
2. That approved business design still describes direct Service Bus-to-Power Automate delivery, unlike the function/Event Log baseline in the as-built/egress/current caller.
3. Its Update and AddDocument table examples incorrectly retain `changeType=Create`. The AddDocument narrative also requires clarification about enquiry-origin versus document-origin filtering.
4. Current subscription OpenAPI `data.name` enum omits `enquirySystemType`, although the active register and newer design use it. This is a contract discrepancy; it does not, without live validation, prove the proxy/provider rejects that filter.
5. Current retrieval OpenAPI spells timestamp properties `publishedTimestamp`/`deliveredTimestamp`, while modern push examples use `publishedTimeStamp`/`deliveredTimeStamp`. Do not reuse a retrieval schema as the push contract without reconciling casing.
6. Some engineering/egress/as-built approval states remain pending or internally inconsistent. Published design approval is not a substitute for reconciling actual contracts and recorded behavior.

### What this changes for the proof and MUFG questions

**Established reuse baseline:** onboarding concepts, separate identifiers, subscription POST/DELETE lifecycle, filter/request/response pattern, single-event delivery envelope, historical retrieval, Event Log route and serialized-event mapping, and documented support procedures.

**Employer Servicing adaptation:** its approved design selects static `Enquiry/Update` and `Enquiry/AddDocument` coverage, not external creation events. Confirm applicable fund/subject/system/case selectors and differences from Member Resolution; do not clone every Member Resolution subscription.

**Still outstanding:** actual Employer Servicing registration/reuse decision and environment allocations, delivery configuration, definitive `ES`/`BPS`/`EMPS`/`EMPSP` field meanings, filter semantics for added documents, valid representative event/enquiry/document responses and destination differences, provider confirmation/testing availability and delivery dates.

**Progress versus completion:** the generic technical proof can progress against controlled fixtures and this Member Resolution baseline; it need not wait for a wholly new description of subscriptions or generic contracts. A completed generic transport demonstration still requires resolving/verifying internal forwarding and consumer behavior. Employer Servicing provider-to-destination validation and business sign-off require the confirmed Employer Servicing details and correlated execution evidence. No such execution was performed in this review.

## 15. Continuation-chat test and reporting progress, 25 November 2026

Source: [MRT-ES-Event-Integration continuation chat](agent-host-session://ahp-session/66aa634f-fc3d-4245-b13b-b6db087bb3d0?chat=6105e1bb-0b36-4811-9498-d4173ea75827), through its latest completed response. This records changes and test status, not proof that the new scenario passed.

### Committed and published

The `members` repository is on `feature/SFB/BDD-ReQnRoll`, tracking `origin/feature/SFB/BDD-ReQnRoll` at `d5040bcd`. Two commits were pushed:

- `4c2dc4b9` — `Read MUFG event APIM path from configuration`.
- `d5040bcd` — `Add Application Insights checks and run report to BDD tests`.

The configurable setting is `AppSettings__Apim__MufgEventPath`, bound to `AppSettings.Apim.MufgEventPath`. It has no hard-coded fallback and missing/blank configuration throws. Local `local.settings.json` was updated. The function reads this value from Function App settings, not Azure App Configuration. The deployment pipeline does **not** yet write the setting. The proposed pipeline variable is `$(apim_mufg_event_path)`; its value and environment variable-group entries are still undecided. The local proof route used in debugging is `/member/event-poc/v1/mapper`; `/mufg-event` was the previous default. No pipeline edit was authorized or made.

The `d5040bcd` commit includes the Application Insights checks, polling progress, portal query links, run report, settings changes and the user's `TestConfiguration.cs` parser change. The report template was moved out of ignored `TestResults` into `Support/Reports/report-template.html`. The user's local `launchSettings.json` change remains uncommitted.

### New uncommitted end-to-end test and report work

At the last repository check, the `members` working tree had the following uncommitted/untracked test changes; they were **not committed or pushed**:

- Modified: `StepDefinitions/IngressSteps.cs`, `StepDefinitions/IngressTelemetrySteps.cs`, `Support/Clients/IngressClient.cs`, `Support/Context/ScenarioRun.cs`, `Support/Hooks/ScenarioHooks.cs`, `Support/Kql/TelemetryQueries.cs`, `Support/Reports/ReportWriter.cs`, `Support/Reports/report-template.html`, and local `src/AS.FIP.Members/Properties/launchSettings.json`.
- New: `Features/EndToEnd.feature`, `StepDefinitions/EndToEndSteps.cs`, and `Support/Reports/JsonText.cs`.

The `EndToEnd.feature` scenario is **“Member enquiry event flows from ingress to Egress”**. Its planned evidence checks, in order:

1. Local function host is reachable, otherwise fail with a clear message.
2. Ingress responds `201` and echoes the unique run identifier.
3. Application Insights records ingress and Service Bus publication.
4. Function logs receipt and its post to the mapper, without exceptions.
5. Mapper and Egress requests return `200`.
6. Egress records the mapped event with `as_eventmessageid` equal to the run identifier.

Potential correlation caveat: function log lookup is linked through operation identifiers shared with Mapper; if this association does not hold, the function-log checks may fail even when Mapper/Egress telemetry appears.

The scenario was added, builds with **0 warnings and 0 errors**, and appears in test discovery. It has **not** been run. A preceding manual ingress attempt returned `201` and Visual Studio breakpoints were hit; that did not demonstrate the complete flow and occurred before the current end-to-end checks were wired.

### Report behavior and retained outputs

The current intended report is one styled run-level HTML report, updated after each scenario, with a left side navigation grouped by feature, result/duration indicators, All/Passed/Failed filters, search, and collapsible console-style per-scenario logs. The console prints a `Run report:` file link; an HTML page cannot itself be rendered in the console.

The report includes request/payload panels for data captured by the test and Application Insights:

- Ingress request URL, non-token headers, correlation identifier, broker properties and JSON body; ingress response status, correlation identifier and body.
- Service Bus publish result/target, message properties and body.
- Mapper/Egress request and response bodies when APIM diagnostics capture them.
- JSON traces such as Liquid-mapped output.

Each body is formatted and colour-coded. Payload capture depends on APIM/Application Insights logging; unavailable body telemetry is omitted rather than invented. Query links are real anchors. Test output is also printed to the console and captured in the report. Keep every generated report; no automated cleanup is allowed.

On 26 November the report writer was updated so the right-panel Failure, Application Insights queries, Steps, Requests/messages/payloads, per-hop Telemetry and Console log sections are all collapsed initially, with native expandable summaries. Payload and query-text items remain individually expandable inside their groups. A focused `ReportWriterTests` test passes. The previously generated `TestResults/report-20261126-062916.html` is an earlier static output and was not rewritten; the next generated report will include this UI change.

### Local debug and validation status

- Visual Studio local Function debugging was brought up successfully; `mufg_mrt_event_processor: serviceBusTrigger` was listening. A disabled beneficiary function also logged an unrelated tooling/configuration error.
- The test runner was run separately from the Function host, not as a second simultaneous debug project.
- The most recent build and scenario-discovery check succeeded. The new end-to-end scenario and generated report have not been exercised with real telemetry.
- A focused report-writer unit test has since passed (1/1); no live integration scenario was run for the collapse change.
- Next requested validation is to start the Function under Visual Studio debugging and run the new scenario with detailed console output; only then can its generated report and correlation checks be assessed.
- After that, the user identified **Liquid template mapping tests with dummy data and multiple variants as the most important follow-up test family**. This remains pending and should be recorded as a priority, not represented as implemented.
- Preserve the one-instruction-per-response interaction preference during test troubleshooting.
