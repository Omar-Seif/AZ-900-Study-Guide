# AZ-900 — Describe Azure Management and Governance (30–35% of exam)
## Module: Describe Cost Management in Azure

> **Exam context:** Welcome to the final domain — worth 30–35%. I checked this module's 5 learning objectives against the current official skills list (last updated July 20, 2026). Four map directly: factors affecting cost, the pricing calculator, cost management capabilities, and tags. The 5th — "cost optimization options (Reservations, savings plans, Spot pricing)" — **isn't a separate official bullet**, but it isn't a real gap either: Reservations, savings plans, and Spot pricing are already explicitly named inside the "factors that affect cost" bullet (specifically the "Consumption" factor). Treat the dedicated optimization unit as useful elaboration on an already-tested concept, not bonus material to skip.
>
> **What's next:** "Describe features and tools for governance and compliance" (Microsoft Purview, Azure Policy, resource locks) — and Azure Policy gets previewed *this* module already, in the tags section.

---

## 1. Factors That Affect Cost

Six factors, building directly on Domain 1's consumption-based model:

| Factor | Key point |
|---|---|
| **Resource type** | Settings and region both affect price — the same storage account or VM configuration can cost differently just by changing region |
| **Consumption** | Pay-as-you-go is the default; discount mechanisms exist for predictable or interruptible workloads (see Section 4) |
| **Maintenance** | Watch for orphaned resources — deprovisioning a VM doesn't always deprovision its attached storage/networking automatically |
| **Geography** | Region affects both resource cost (power, labor, taxes vary by location) and network traffic cost |
| **Subscription type** | Some subscriptions include usage allowances (e.g., free trial: 12 months of select free products, a spending credit for the first 30 days, 25+ always-free products) |
| **Azure Marketplace** | Third-party solutions — you pay for both the underlying Azure resources *and* the vendor's own billing structure; all Marketplace solutions are certified against Azure policies/standards |

### Geography and Network Traffic — A Genuine Naming Trap

**🚩 Exam trap — the sharpest one in this module:** **"Billing zones" and "Availability Zones" are two completely different concepts that happen to share the word "zone."**

| | Availability Zones (Domain 2, Module 1) | Billing Zones (this module) |
|---|---|---|
| What it is | Physically separate datacenters within a region, for resiliency | A geographical grouping of Azure *regions*, used specifically for data-transfer pricing |
| Purpose | High availability / fault tolerance | Determining the cost of moving data (bandwidth) |

**Bandwidth basics:** Inbound data transfer (into Azure) is often free; **outbound data transfer (leaving Azure) is priced based on billing zones.** Moving data within Europe, for example, costs less than moving it from Europe to Asia or South America.

**🧠 Memory trick:** *"Availability Zones keep your resources up. Billing zones just tell you what it costs to move data out the door."*

---

## 2. The Pricing Calculator

Estimates costs for provisioning resources — compute, storage, networking, and options like storage type/access tier/redundancy — **before** you actually deploy anything.

**🚩 Exam trap:** **The Total Cost of Ownership (TCO) calculator has been retired.** If a question or older resource mentions it, know that the current tool is the **Pricing calculator** — don't reference the TCO calculator as still active.

**Key facts:**
- **Informational only** — prices are estimates.
- **Nothing is provisioned** when you use it, and **you are never charged** through the calculator itself.

**Direct callback to your Storage module:** This is the same calculator you were pointed to back in Domain 1 as a hands-on exercise with your $100 credit — now it's officially named as exam content in its own right.

---

## 3. Microsoft Cost Management

Three pillars: **Cost analysis, Cost alerts, Budgets.**

### Cost Analysis
A visual view of your costs — by billing cycle, region, resource, subscription, resource group, or service. Used to spot spending trends, catch a sudden cost spike after a deployment, find idle resources, and validate that tagging/budgets are working as intended.

### Cost Alerts — Three Types

| Alert type | Who it's for | Trigger |
|---|---|---|
| **Budget alerts** | Anyone | User-defined threshold (e.g., 80% of a monthly budget) — configurable in the Azure portal (by cost) or via the Azure Consumption API (by cost *or* consumption usage) |
| **Credit alerts** | Organizations with an **Enterprise Agreement (EA)**, specifically for **Azure Prepayment** | Automatic — fires at **90% and 100%** of the prepayment balance, no configuration needed |
| **Department spending quota alerts** | EA customers specifically | User-configured thresholds (e.g., 50%, 75%) against a department's spending quota |

**🚩 Exam trap:** Don't confuse the three — **Budget alerts** are the general-purpose, user-configurable option available to anyone. **Credit alerts** and **department spending quota alerts** are both **EA-specific**, but credit alerts are automatic at fixed percentages (90/100), while department quota thresholds are configurable by the org.

### Budgets
A spending limit set at a subscription, resource group, service type, or other scope. When the linked budget alert threshold is hit, it appears in Cost Alerts and (if configured) sends an email. **Budgets can also trigger automation** — e.g., automatically shutting down non-production resources once a threshold is crossed.

**🧠 Memory trick:** *"Cost analysis = the mirror (what happened). Cost alerts = the tripwire (something crossed a line). Budgets = the fence you set the tripwire on."*

---

## 4. Tags

Metadata (name/value pairs) attached to resources, used for six purposes: resource management, cost management, operations management (informs SLAs), security (classification level), governance/compliance, and workload optimization/automation.

**Practical starter set:** `Environment`, `Owner`, `CostCenter`, `Workload` — covers most day-to-day filtering, ownership lookup, and automation scoping needs.

**Management:** add/modify/delete via portal, PowerShell, CLI, ARM templates, or REST API.

**🚩 Exam trap — a direct, deliberate contrast with last module:** **Tags do NOT automatically inherit between resource, resource group, and subscription levels.** Apply a tag at one level, and it does *not* automatically show up at another. This is the **opposite** behavior from RBAC permissions, which *do* inherit downward through that same Management Group → Subscription → Resource Group → Resource hierarchy you learned in your identity/access module.

**🧠 Memory trick:** *"RBAC permissions flow downhill automatically. Tags stay exactly where you put them — no automatic inheritance either direction."*

**Forward preview:** **Azure Policy** (fully covered in your next module) can *enforce* tagging rules — requiring specific tags on new resources, or automatically reapplying tags that were removed. Worth flagging now since tags and Policy are a natural pair on the exam.

**Practical note:** You don't have to force every resource to carry every tag — e.g., you might only tag mission-critical resources with `Impact`, treating untagged resources as implicitly non-critical by convention.

---

## 5. Cost Optimization Options

Three commitment-based alternatives to plain pay-as-you-go, each suited to a different workload shape:

| Option | What you commit to | Term | Best for |
|---|---|---|---|
| **Reservations** | Specific resource capacity (a particular VM type, database, storage config) | 1 or 3 years | Stable, predictable, long-running workloads |
| **Azure savings plan for compute** | An **hourly spend amount** on eligible compute services (not a specific resource type) | 1 or 3 years | Steady usage that needs flexibility across different compute services |
| **Spot pricing** | Nothing — uses unused Azure capacity at a discount | None | Fault-tolerant/interruptible workloads (can be evicted when Azure needs the capacity back) |

**🚩 Exam trap:** The Reservations-vs-savings-plan distinction is subtle and testable: **Reservations commit to a specific resource type/capacity. The savings plan commits to a dollar amount per hour**, and Azure automatically applies the best available price across *eligible* compute services as you use them — you're not locked into one VM family.

**Decision guide (directly from the module):**
- 24/7 production SQL workload with stable needs → **Reservations**
- Mixed web/API workload shifting across compute services → **Savings plan for compute**
- Batch rendering or restartable test jobs → **Spot pricing**

**Practical note:** Revisit these choices as usage patterns change — an interruptible workload can become business-critical and need to move off Spot; a stable workload might justify moving from on-demand to a commitment model for better long-term efficiency.

**Direct callback to Domain 1:** These three options are concrete implementations of "comparing cloud pricing models" — the consumption-based model isn't just plain pay-as-you-go; it has a whole spectrum of commitment-vs-flexibility tradeoffs underneath it.

---

## Scenario Table

| Scenario | Correct concept |
|---|---|
| Estimating monthly cost of a new web app before deploying it | Pricing calculator |
| Finding out that moving data from Europe to Asia costs more than staying within Europe | Billing zones (data transfer pricing) |
| Wanting an alert automatically fired at 90% of an EA prepayment balance, no setup needed | Credit alert |
| Wanting a custom alert at 80% of a monthly dev/test budget | Budget alert |
| Automatically shutting down non-production resources when spending crosses a threshold | Budget with triggered automation |
| Needing to filter costs by team, find a resource's owner, and scope automation | Tags (Environment/Owner/CostCenter/Workload) |
| Wanting a tag automatically applied to every new resource in a subscription | Azure Policy enforcing tagging (preview of next module) |
| A 24/7 production database with stable, predictable usage | Reservations |
| A workload that shifts across several compute services but has steady overall usage | Azure savings plan for compute |
| A batch job that can tolerate being interrupted, prioritizing lowest cost | Spot pricing |

---

## Key Takeaways

- **Billing zones ≠ Availability Zones** — same word, entirely different purpose (data-transfer pricing vs. resiliency). One of the sharpest naming traps in the whole exam.
- **TCO calculator is retired** — use the **Pricing calculator**, which is estimate-only and never provisions or charges anything.
- **Cost Management** = Cost analysis (the mirror) + Cost alerts (Budget/Credit/Department quota — general vs. EA-specific vs. EA-configurable) + Budgets (the spending limit, which can trigger automation).
- **Tags do NOT inherit** across resource/resource group/subscription — the direct opposite of RBAC permission inheritance from your last module. Azure Policy can enforce tagging rules.
- **Reservations** (commit to a specific resource) vs. **savings plan** (commit to an hourly spend, flexible across services) vs. **Spot** (no commitment, cheapest, interruptible) — pick based on workload predictability and interruption tolerance.

---

## Practice Questions

**Q1 (Easy).** What is the key difference between Availability Zones and billing zones?

A) They are the same concept with two different names.
B) Availability Zones protect against physical failures; billing zones determine data-transfer pricing.
C) Billing zones are a subset of Availability Zones.
D) Availability Zones only apply to storage, while billing zones apply to compute.

<details>
<summary>Answer</summary>

**Correct: B.** Availability Zones are physically separate datacenters for resiliency; billing zones are a geographic grouping of regions used purely for data-transfer pricing — unrelated purposes despite the shared word "zone."
- A is wrong: they are entirely distinct concepts.
- C is wrong: neither is a subset of the other.
- D is wrong: Availability Zones apply broadly (compute, storage, etc.), and billing zones apply to network data transfer, not compute specifically.
</details>

---

**Q2 (Easy).** Which tool has been retired and should no longer be referenced for Azure cost estimation?

A) Pricing calculator
B) Cost Management
C) Total Cost of Ownership (TCO) calculator
D) Azure Advisor

<details>
<summary>Answer</summary>

**Correct: C — the TCO calculator.** It has been retired; the Pricing calculator is the current tool for cost estimation.
- A is the current, active tool.
- B and D are unrelated, still-active tools (cost monitoring and recommendations, respectively).
</details>

---

**Q3 (Medium).** A company using an Enterprise Agreement wants to be automatically notified when its Azure Prepayment balance is nearly exhausted, without configuring any custom thresholds. Which alert type fits?

A) Budget alert
B) Credit alert
C) Department spending quota alert
D) Cost analysis alert

<details>
<summary>Answer</summary>

**Correct: B — Credit alert.** These fire automatically at 90% and 100% of the Azure Prepayment balance, with no configuration required.
- A is wrong: budget alerts require the user to define a threshold.
- C is wrong: department spending quota alerts are also EA-specific but apply to department quotas, not the overall prepayment balance, and require configured thresholds.
- D isn't a real alert type — cost analysis is a visualization feature, not an alert mechanism.
</details>

---

**Q4 (Medium).** A company applies the tag `CostCenter: Marketing` to a resource group. Which statement is TRUE?

A) All resources within that resource group automatically inherit the `CostCenter: Marketing` tag.
B) The tag applies only to the resource group itself; resources inside it do not automatically receive the tag.
C) The tag automatically propagates up to the subscription level as well.
D) Tags cannot be applied at the resource group level at all.

<details>
<summary>Answer</summary>

**Correct: B.** Tags do not inherit between resource, resource group, and subscription levels — applying a tag at one level does not automatically apply it elsewhere.
- A is the trap answer — it assumes inheritance behavior similar to RBAC, but tags explicitly don't work that way.
- C is wrong for the same reason — no automatic propagation in either direction.
- D is wrong: tags can be applied at any level, including resource groups.
</details>

---

**Q5 (Medium).** A company runs a production SQL database 24/7 with stable, predictable resource needs. Which cost optimization option is the best fit?

A) Spot pricing
B) Azure savings plan for compute
C) Reservations
D) Pay-as-you-go with no changes

<details>
<summary>Answer</summary>

**Correct: C — Reservations.** Stable, predictable, long-running workloads are the textbook use case for committing to specific resource capacity via a 1- or 3-year Reservation.
- A is wrong: Spot pricing risks eviction, unsuitable for a production database that needs to stay running.
- B is wrong: savings plans are better suited to workloads that shift across different compute services, not a single stable resource type.
- D is technically possible but leaves cost savings on the table given how predictable this workload is.
</details>

---

**Q6 (Hard).** Which statement correctly distinguishes Reservations from the Azure savings plan for compute?

A) Reservations commit to an hourly spend amount; savings plans commit to a specific VM type.
B) Reservations commit to specific resource capacity; savings plans commit to an hourly spend amount applied flexibly across eligible compute services.
C) Both commit to the exact same thing; the only difference is contract length.
D) Reservations apply only to storage; savings plans apply only to compute.

<details>
<summary>Answer</summary>

**Correct: B.** This is the precise distinction: Reservations lock in a specific resource type/capacity, while savings plans commit to a dollar-per-hour spend that Azure applies automatically to whichever eligible compute usage occurs.
- A reverses the definitions.
- C is wrong: the commitment *type* differs (resource vs. spend amount), not just the term length.
- D is wrong: Reservations can apply to VMs, databases, and storage — not storage exclusively — and savings plans are specifically compute-focused, not storage.
</details>

---

**Q7 (Hard — cumulative, pulls in the identity/access module).** A company notices that RBAC permissions assigned at a management group automatically apply to every subscription beneath it, but a cost-tracking tag applied at the same management group does NOT automatically appear on those subscriptions. What explains this difference?

A) This is a misconfiguration — tags should inherit exactly like RBAC permissions.
B) RBAC permissions are designed to inherit down the management hierarchy; tags are explicitly designed NOT to inherit between levels — two different, intentional behaviors within the same hierarchy.
C) Tags only fail to inherit when applied above the subscription level; they inherit normally below that.
D) RBAC and tags both fail to inherit; the scenario describes an error state.

<details>
<summary>Answer</summary>

**Correct: B.** This is expected, intentional behavior — RBAC's inheritance model and tags' non-inheritance model are simply different by design, both operating within the same Management Group → Subscription → Resource Group → Resource hierarchy.
- A is wrong: this isn't a misconfiguration; it's documented, expected behavior.
- C is wrong: tags don't inherit at any level in this hierarchy, not just above subscriptions.
- D is wrong: RBAC inheritance is working correctly in this scenario; only the tag behavior is non-inheriting, and that's by design.
</details>

---

*Next up: "Describe features and tools in Azure for governance and compliance" — Microsoft Purview, Azure Policy (including tag enforcement, following up on this module), and resource locks. Paste whenever you're ready.*
