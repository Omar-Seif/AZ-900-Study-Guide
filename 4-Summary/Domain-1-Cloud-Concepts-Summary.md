# AZ-900 Track Summary — Domain 1: Describe Cloud Concepts
**Exam weight: 25–30%** · Covers Modules 1–3 (cloud computing, benefits, service types)

This is a condensed review sheet for the whole first track. For full detail, see the individual module notes. Use this to self-test before the exam.

---

## Core Concepts at a Glance

**Cloud computing** = renting IT resources (compute, storage, databases, networking, plus AI/ML/IoT) over the internet, on-demand. Traits: on-demand, elastic, pay-for-use, broad service range.

**Shared responsibility model** — who secures/manages what:
- **Always yours (the "3 D's"):** Data, Devices, iDentities.
- **Always Microsoft's:** physical datacenter, physical network, physical hosts.
- **Depends on service type:** OS, network controls, applications, identity/access, infrastructure — responsibility slides from "mostly you" (IaaS) to "mostly Microsoft" (SaaS).

**Deployment models:**
| Model | What it is |
|---|---|
| Public | Third-party, shared, open to anyone who pays |
| Private | Dedicated to one org (can be on-site *or* third-party hosted — the point is *dedicated*, not *where*) |
| Hybrid | Private + public, connected |
| Multicloud | Two or more *public* providers (e.g., Azure + AWS) |

- **Azure Arc** = manage resources across all environments from one place.
- **Azure VMware Solution** = relocate existing VMware workloads into Azure.

**Consumption-based model** = pay only for what you use. This is **OpEx** (ongoing operational spend), *not* CapEx (big upfront capital purchase). Cloud avoids over-provisioning waste and long hardware lead times.

**Cloud benefits (Module 2):**
- **High availability** = uptime, backed by **SLAs** (per-service, often financially backed).
- **Scalability** = **vertical** (resize one resource, up/down) vs. **horizontal** (change the *number* of resources, out/in).
- **Reliability** = recovering from failure (often via multi-region redundancy). *Distinct from availability.*
- **Predictability** = performance (autoscaling, load balancing) + cost (real-time tracking, pricing calculator).
- **Manageability** = **OF the cloud** (automatic things happening to resources) vs. **IN the cloud** (interfaces you use: Portal, CLI, PowerShell, APIs).
- **Well-Architected Framework** = 5 pillars: Reliability, Security, Cost Optimization, Operational Excellence, Performance Efficiency.

**Service types (Module 3):**
| | IaaS | PaaS | SaaS |
|---|---|---|---|
| You get | Rented hardware, build everything on top | A ready-made dev platform | The finished application |
| You manage | Most of the stack (OS, patching, config) | Just app code + data | Just data, identity/access, devices |
| Control / Ease | Highest control / Lowest ease | Middle / Middle | Lowest control / Highest ease |
| Example | Virtual Machines | App Service, Azure SQL Database | Microsoft 365, email |

- **Serverless** (e.g., Azure Functions) ≈ the far end of PaaS — no server management, pay only for execution, scales to zero.

---

## Top Exam Traps for This Track

1. **Hybrid vs. multicloud** — hybrid = private + public; multicloud = 2+ *public* providers. Different questions.
2. **Private cloud isn't always on-site** — it can be third-party hosted; the defining trait is "dedicated to one org."
3. **Vertical vs. horizontal scaling** — "more instances" = horizontal (out); "bigger machine" = vertical (up).
4. **Availability vs. reliability** — uptime (SLA) vs. recovering from failure.
5. **Management OF vs. IN the cloud** — automatic vs. you-driven interfaces. The word "automate" can appear in both — look at whether a *person* is choosing a specific tool.
6. **Who owns the OS?** — customer in IaaS, Microsoft in PaaS/SaaS. (The single most-missed shared-responsibility detail.)
7. **CapEx vs. OpEx** — cloud is OpEx.

---

## Consolidated Scenario Table

| Scenario | Answer |
|---|---|
| Pay only for resources used each month, no hardware purchase | Consumption-based model (OpEx) |
| Hospital must fully control infrastructure for compliance | Private cloud |
| Bank keeps core systems on-prem but bursts reporting to Azure | Hybrid cloud |
| Company uses Azure compute + AWS storage | Multicloud |
| One dashboard to manage servers across on-prem, Azure, and AWS | Azure Arc |
| Relocate existing on-prem VMware workloads to Azure | Azure VMware Solution |
| Add 3 more VMs behind a load balancer for a traffic spike | Horizontal scaling (out) |
| Upgrade a VM from 2 to 8 vCPUs | Vertical scaling (up) |
| Auto-failover to another region during a disaster | Reliability |
| Move an on-prem app to the cloud with minimal redesign, full OS control | IaaS (lift-and-shift) |
| Build a web app fast with built-in scaling, no OS management | PaaS |
| Adopt a cloud email platform instead of running a mail server | SaaS |
| Run code triggered by an event, pay only when it runs, scale to zero | Serverless (Azure Functions) |
| Install your own SQL Server on a VM you deployed — who patches it? | You (IaaS-level responsibility) |

---

## Practice Questions

**Q1 (Easy).** Which is always the customer's responsibility regardless of service model?
A) Physical datacenter · B) Physical network · C) Data and identities · D) Physical hosts
<details><summary>Answer</summary>**C.** Data and identities are always yours ("3 D's"). A, B, D are always Microsoft's.</details>

**Q2 (Easy).** A company classifies its cloud spend as an ongoing operational cost rather than an upfront purchase. This is:
A) CapEx · B) OpEx · C) A private cloud · D) An SLA
<details><summary>Answer</summary>**B.** Pay-as-you-go cloud spending is OpEx.</details>

**Q3 (Medium).** A company runs core systems in its own datacenter but temporarily uses Azure for a year-end demand spike. Model?
A) Multicloud · B) Public · C) Hybrid · D) Community
<details><summary>Answer</summary>**C — Hybrid.** Private + public combined. Multicloud would need 2+ *public* providers.</details>

**Q4 (Medium).** In PaaS, who patches the operating system?
A) Customer · B) Provider · C) Shared equally · D) Third-party vendor
<details><summary>Answer</summary>**B — Provider.** The trap answer is "customer" (you still write code, but the OS belongs to Microsoft in PaaS).</details>

**Q5 (Medium).** A system automatically shifts to another region after a regional outage. Which benefit?
A) Scalability · B) Reliability · C) Manageability · D) Sustainability
<details><summary>Answer</summary>**B — Reliability** (recovering from failure via geographic redundancy).</details>

**Q6 (Hard).** An ops team uses PowerShell scripts and monitors resource health in the Azure Portal. Which manageability concept?
A) Management of the cloud · B) Management in the cloud · C) High availability · D) Governance
<details><summary>Answer</summary>**B — Management in the cloud.** Despite the word "automate," a person is actively using specific interfaces (Portal, PowerShell).</details>

**Q7 (Hard — cumulative).** A company runs a private datacenter + Azure App Service (PaaS), where Microsoft patches the platform, and the app auto-adds/removes instances with demand. Which THREE concepts?
A) Public only + vertical scaling + SaaS
B) Hybrid + horizontal scaling + PaaS shared responsibility
C) Multicloud + horizontal scaling + IaaS
D) Private only + reliability + SaaS
<details><summary>Answer</summary>**B.** Private + Azure = hybrid; instance count changing = horizontal; App Service = PaaS (Microsoft patches).</details>

---

### Key Takeaways
- The **shared responsibility model** is the throughline of this entire track — be able to explain it cold.
- Memorize the two scaling pairs (vertical/horizontal) and the two manageability senses (of/in) — both are heavy trap territory.
- IaaS → PaaS → SaaS trades control for convenience, but **data, identity, and devices stay yours** at every level.
