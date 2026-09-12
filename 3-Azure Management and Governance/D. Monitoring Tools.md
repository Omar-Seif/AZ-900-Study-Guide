# AZ-900 — Describe Azure Management and Governance (30–35% of exam)
## Module: Describe Monitoring Tools in Azure

> **Exam context:** Checked against the official skills list — all 3 learning objectives (Azure Advisor, Azure Service Health, Azure Monitor with Log Analytics/Alerts/Application Insights) match exactly. **This is the last module in the entire AZ-900 curriculum** — no more scope gaps to check after this one.
>
> This module also delivers the **best full-circle moment of the whole track**: Resource Health's job is literally to tell you whether a problem is "Azure's side or yours" — which is the shared responsibility model, the very first real concept you learned, resurfacing as the very last one.

---

## 1. Azure Advisor

Evaluates your Azure resources and makes **recommendations** — think of it as a personalized best-practices guide built into the portal. Each recommendation gives you a suggested action you can **take now, postpone, or dismiss**. You can set up notifications for new recommendations, and the dashboard is filterable by subscription, resource group, or service.

**Five recommendation categories:**

| Category | Focus |
|---|---|
| **Reliability** | Flags configuration risks that threaten uptime |
| **Security** | Detects threats/vulnerabilities that could lead to breaches |
| **Performance** | Identifies changes that speed up applications |
| **Operational Excellence** | Suggests workflow/deployment improvements |
| **Cost** | Finds ways to reduce spending |

**🔗 Callbacks across your whole track:** Reliability recommendations connect to Availability Zones/region pairs (Domain 2) and the "reliability" benefit (Domain 1). Cost recommendations connect to your entire cost management module (Reservations, savings plans, tags). Security recommendations overlap with — but are distinct from — Defender for Cloud.

**🚩 Exam trap — Advisor vs. Azure Policy:** **Advisor recommends; it does not enforce.** This is the flip side of what you learned about Azure Policy, which *can* block noncompliant resources or auto-remediate them. If a scenario describes a tool that "suggests" or "flags for review" without blocking anything, that's Advisor. If it describes something that *prevents* noncompliant resources from being created or automatically fixes them, that's Policy.

**🚩 Exam trap — Advisor vs. Defender for Cloud:** Both touch "security," but at different depths. **Advisor's Security category** gives broad, portfolio-wide best-practice recommendations across all five categories. **Microsoft Defender for Cloud** is a dedicated, continuous security posture management and threat-protection service (Assess/Secure/Defend, secure score, kill-chain analysis). Advisor = quick broad wins; Defender = deep, ongoing security-specific management.

---

## 2. Azure Service Health

Three nested views, narrowing in scope from global all the way down to a single resource:

| View | Scope | What it tells you |
|---|---|---|
| **Azure Status** | Global — all services, all regions | Whether Azure itself is experiencing a widespread outage |
| **Service Health** | Personalized — the specific services/regions **you** actually use | Outages, planned maintenance, and health advisories relevant to your environment; supports alerts |
| **Resource Health** | Individual — a specific resource (e.g., one VM) | Whether that resource is healthy or having a problem, **and whether the issue is on Azure's side or yours** |

**🧠 Memory trick:** *"Status = the whole world. Service Health = your neighborhood. Resource Health = your specific house."*

**🔗 The full-circle moment:** Resource Health determining whether a problem is **"Azure's side or yours"** is a direct, concrete payoff of the **shared responsibility model** — the very first substantial concept you learned back in Domain 1, Module 1. The entire exam essentially bookends on that one idea: who's responsible for what, resurfacing here as a practical diagnostic question rather than an abstract model.

**Operational value:** historical alerts are retained (useful for spotting recurring trends), and Service Health provides direct links to support when an event affects your workloads.

---

## 3. Azure Monitor

A platform for **collecting, analyzing, and acting on** data from your resources and applications — working across **Azure, on-premises, and multicloud environments** alike.

**🔗 Direct callback to Domain 3, Module 3:** This cross-environment reach is the same theme you saw with **Azure Arc** — monitoring, like governance, isn't limited to "Azure-only" resources.

Azure Monitor gathers logs and metrics from applications, operating systems, and network layers, stores that data centrally, and surfaces it through dashboards, queries, and alerts.

### Azure Log Analytics
The **query tool** within the portal — write and run queries against the data Azure Monitor collects. Ranges from simple filtering ("show all errors in the last hour") to advanced trend analysis/visualization over time.

### Azure Monitor Alerts
Notify you when a condition you defined is met. Two parts:
- **Alert rule** — defines the condition.
- **Action group** — controls who gets notified and what happens next.

**Two alert types:**
- **Metric-based** — e.g., email when a VM's CPU stays above 80%.
- **Log-based** — e.g., watch for a specific error pattern across multiple resources.

**🚩 Exam trap — a genuinely unifying detail:** **Action groups are reusable across Azure Monitor, Service Health, AND Azure Advisor.** This is the mechanism tying all three tools in this module together — you don't need a separate notification setup for each; one action group construct serves all three.

### Application Insights
An Azure Monitor **feature** specifically for monitoring **web application** performance and usage — whether that app runs in Azure, on-premises, or another cloud.

**Setup options:** add an SDK to your application code, **or** enable the Application Insights agent with **no code changes** at all.

**What it monitors:**
- Request rates, response times, failure rates
- Dependency calls and their performance
- Page load times, user counts, session trends
- Server performance counters (CPU, memory, network)

**Availability tests:** synthetic requests sent to your app so you know it's responding — **even during low-traffic periods** when real user activity might not reveal a problem quickly.

**🧠 Memory trick:** *"Log Analytics = the query engine for raw data. Alerts = the tripwire on top of that data. Application Insights = a specialized lens focused just on your web app's behavior."*

---

## Master Comparison: The Three Monitoring Tools

| | Azure Advisor | Azure Service Health | Azure Monitor |
|---|---|---|---|
| Core question it answers | "What should I improve?" | "Is Azure — or my specific resource — healthy right now?" | "What is actually happening, in detail, across my resources and apps?" |
| Action type | Recommends (doesn't enforce) | Reports health status + alerts | Collects, queries, alerts, and deep-dives (via Application Insights) |
| Scope | Your resources, 5 categories | Global → your services → one resource | Logs/metrics from apps, OS, and network layers, any environment |
| Shared mechanism | Action groups (shared with Monitor + Service Health) | Action groups (shared with Advisor + Monitor) | Action groups; Log Analytics; Application Insights |

---

## Scenario Table

| Scenario | Correct tool |
|---|---|
| Want a portfolio-wide list of quick, actionable cost-saving suggestions | Azure Advisor (Cost category) |
| Want to know if a widespread Azure outage is happening right now, globally | Azure Status |
| Want to know if a specific VM's current issue is Azure's fault or your own configuration | Resource Health |
| Want alerts scoped to only the specific services/regions your organization uses | Service Health |
| Want to query "all errors in the last hour" across collected log data | Log Analytics |
| Want an email when a VM's CPU exceeds 80% | Azure Monitor metric-based alert |
| Want to detect a recurring error pattern across multiple resources | Azure Monitor log-based alert |
| Want to monitor a web app's response times and dependency call performance | Application Insights |
| Want to confirm a web app is still responding during a quiet overnight period | Application Insights availability test |
| Want one notification configuration reused across Advisor, Monitor, and Service Health | Action groups |

---

## Key Takeaways

- **Azure Advisor**: 5 categories (Reliability, Security, Performance, Operational Excellence, Cost), recommends but doesn't enforce — contrast with Azure Policy (enforces) and Defender for Cloud (deep security-specific management).
- **Azure Service Health**: three nested views — Azure Status (global) → Service Health (your services) → Resource Health (one resource, including whose fault it is). That last point is a direct callback to the shared responsibility model.
- **Azure Monitor**: collects logs/metrics across Azure/on-prem/multicloud (same theme as Azure Arc). **Log Analytics** = query tool. **Alerts** = rule + action group, metric- or log-based. **Application Insights** = web-app-specific monitoring (SDK or agent-based, no-code option available), including availability tests.
- **Action groups are shared infrastructure** across all three tools in this module — a genuinely unifying, testable detail.

---

## Practice Questions

**Q1 (Easy).** Which Azure Advisor category would flag an opportunity to reduce monthly spending?

A) Reliability
B) Operational Excellence
C) Cost
D) Performance

<details>
<summary>Answer</summary>

**Correct: C — Cost.** This category is specifically focused on finding ways to reduce Azure spending.
- A, B, and D address uptime risk, workflow/deployment improvements, and application speed respectively — not spending.
</details>

---

**Q2 (Easy).** Which Azure Service Health view tells you whether a specific virtual machine's problem originates from Azure's side or your own configuration?

A) Azure Status
B) Service Health
C) Resource Health
D) Azure Advisor

<details>
<summary>Answer</summary>

**Correct: C — Resource Health.** It zooms in on an individual resource and identifies whether the issue is Azure's responsibility or yours.
- A is wrong: Azure Status is global, not resource-specific.
- B is wrong: Service Health is scoped to your services/regions generally, not one specific resource's fault attribution.
- D is wrong: Advisor makes recommendations, it doesn't diagnose live resource health issues.
</details>

---

**Q3 (Medium).** A company wants to be alerted when a virtual machine's CPU usage exceeds 80% for a sustained period. Which Azure Monitor feature and alert type fits?

A) Application Insights availability test
B) Azure Monitor Alerts, metric-based
C) Azure Monitor Alerts, log-based
D) Azure Advisor, Performance category

<details>
<summary>Answer</summary>

**Correct: B — Metric-based alert.** CPU usage is a numeric metric, and this scenario matches the module's own metric-alert example exactly.
- A is wrong: availability tests check whether an app is responding, not CPU thresholds.
- C is wrong: log-based alerts watch for patterns in log data (like a specific error), not a numeric metric threshold.
- D is wrong: Advisor recommends changes; it doesn't provide real-time alerting on live metrics.
</details>

---

**Q4 (Medium).** Which statement correctly distinguishes Azure Advisor from Azure Policy?

A) Advisor enforces compliance automatically; Policy only suggests improvements.
B) Advisor recommends actions you can accept or dismiss; Policy can actively prevent or auto-remediate noncompliant resources.
C) They are functionally identical, differing only in which portal blade displays them.
D) Advisor only applies to cost; Policy only applies to security.

<details>
<summary>Answer</summary>

**Correct: B.** This is the precise distinction: Advisor is a recommendation engine (take it or leave it), while Policy has real enforcement power (blocking creation, auto-remediation).
- A reverses the two tools' actual behavior.
- C is wrong: they serve fundamentally different purposes, not just different UI locations.
- D is wrong: Advisor spans five categories (not just cost), and Policy can govern far more than security (locations, tags, SKUs, etc.).
</details>

---

**Q5 (Hard).** A development team wants to monitor a web application's response times and dependency call performance without modifying any application code. What should they use, and how should they set it up?

A) Log Analytics, by writing a custom query
B) Application Insights, using the agent-based setup instead of an SDK
C) Azure Advisor, Performance category
D) Resource Health, checking the app's individual health status

<details>
<summary>Answer</summary>

**Correct: B.** Application Insights specifically monitors response times and dependency calls, and the agent-based setup enables this without any code changes — exactly matching the "no code modification" requirement.
- A is wrong: Log Analytics queries existing collected data but isn't itself the mechanism for capturing application-specific performance data like dependency calls.
- C is wrong: Advisor gives broad recommendations, not real-time application performance monitoring.
- D is wrong: Resource Health reports on infrastructure resource health, not detailed web application performance metrics like dependency call timing.
</details>

---

**Q6 (Hard — cumulative, pulls in Domain 3, Module 3).** A company uses Azure Monitor to collect logs and metrics not just from Azure resources, but also from an on-premises server and a VM running in another public cloud. Which earlier concept from your studies makes this cross-environment reach possible?

A) Azure Advisor's recommendation engine
B) The same hybrid/multicloud extension theme you saw with Azure Arc
C) Azure Policy's inheritance model
D) Resource locks

<details>
<summary>Answer</summary>

**Correct: B.** Azure Monitor's ability to reach beyond Azure mirrors the same underlying theme as Azure Arc — Azure's management and monitoring capabilities extending to hybrid and multicloud resources, not staying limited to native Azure infrastructure.
- A is wrong: Advisor's recommendation engine is unrelated to cross-environment data collection.
- C is wrong: Policy's inheritance model governs compliance rules down a hierarchy, not monitoring data collection across environments.
- D is wrong: resource locks prevent deletion/modification, entirely unrelated to monitoring reach.
</details>

---

**Q7 (Hard — grand finale, cumulative across the ENTIRE exam).** A company deploys a web app on Azure App Service (PaaS) across two Availability Zones, tags all resources with `Environment` and `Owner`, applies an Azure Policy requiring those tags, sets a Delete lock on the production resource group, and uses Application Insights to monitor the app's performance. When a customer reports slow response times, Resource Health shows the issue originates from the company's own application code, not Azure's infrastructure. Which foundational concept, present since Domain 1, explains why Resource Health can make that specific distinction?

A) The consumption-based pricing model
B) The shared responsibility model
C) The hybrid cloud deployment model
D) Horizontal scaling

<details>
<summary>Answer</summary>

**Correct: B — The shared responsibility model.** From your very first module, you learned that responsibility for infrastructure vs. application-layer issues splits between Microsoft and the customer depending on the service model (here, PaaS — Microsoft manages the platform, the customer owns their application code). Resource Health's "Azure's side or yours" determination is exactly this model applied as a live diagnostic tool, closing the loop from the first concept of the exam to its very last.
- A is wrong: pricing model is unrelated to diagnosing whose infrastructure layer is at fault.
- C is wrong: no private on-premises component is described in this scenario — it's entirely within Azure.
- D is wrong: scaling addresses capacity/demand, not fault attribution between provider and customer.
</details>

---

## 🎉 AZ-900 Curriculum Complete!

You've now covered all three domains of the Microsoft Azure Fundamentals exam:
- **Domain 1 — Describe cloud concepts (25–30%):** cloud computing basics, shared responsibility, deployment models, consumption-based pricing, cloud benefits, and IaaS/PaaS/SaaS.
- **Domain 2 — Describe Azure architecture and services (35–40%):** core architecture (regions/zones/resource hierarchy), compute, networking, storage, and identity/access/security.
- **Domain 3 — Describe Azure management and governance (30–35%):** cost management, governance/compliance, resource management/deployment tools, and monitoring.

A few threads ran through the entire track and are worth being able to explain cold, since they're the concepts most likely to show up in combined, scenario-style questions on the real exam:
- The **shared responsibility model** (Domain 1) — resurfaced in service types, defense-in-depth, and just now in Resource Health.
- The **Management Group → Subscription → Resource Group → Resource hierarchy** (Domain 2) — resurfaced for RBAC, storage secondary-region logic, tags, Policy, and resource locks, each with different inheritance behavior.
- **Azure Arc** (first mentioned in Domain 1) — paid off fully across Defender for Cloud and the management/deployment module.

*You mentioned your career goals target Gulf and Egyptian job markets — happy to help you think through how AZ-900 fits into your broader plan from here, whether that's a full practice exam, revisiting any domain that felt shaky, or moving on to AI-103 next in your track.*
