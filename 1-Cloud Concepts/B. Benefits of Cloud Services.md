# AZ-900 — Cloud Concepts (25–30% of exam)
## Module: Describe the Benefits of Using Cloud Services

> **Exam context:** Still inside the "Describe cloud concepts" domain (25–30% of the exam). This module covers 4 of the domain's official sub-skills: high availability/scalability, reliability/predictability, security/governance, and manageability. **One heads-up:** the pasted training content includes a 5th unit on **sustainability**, but when I checked Microsoft's current published skills-measured list (updated July 20, 2026), sustainability is *not* listed as a bullet under this skill area. That doesn't mean it's 100% excluded — Microsoft's own disclaimer says "related topics may be covered" — but it means it's lower-yield than everything else in this module. I've marked it ⏭️ SKIM accordingly below.

---

## 1. High Availability & Scalability

**High availability (HA):** Keeping a system up and running as close to 100% of the time as possible, even when something goes wrong. Microsoft backs this with **SLAs (Service-Level Agreements)** — formal, often financially-backed uptime promises, specific to each service (not one blanket number for "all of Azure").

**Scalability:** The ability to adjust resources to meet demand — and, because of the consumption-based model you learned in Module 1, scaling down also means you stop paying for what you don't need.

There are two flavors, and this is the **most testable vocabulary pair** in this whole module:

| | Vertical scaling | Horizontal scaling |
|---|---|---|
| What changes | The *size/power* of a single resource | The *number* of resource instances |
| Direction terms | Scale **up** / scale **down** | Scale **out** / scale **in** |
| Example | Upgrading a VM from 2 vCPUs to 8 vCPUs | Adding 3 more VMs behind a load balancer during a traffic spike |

**🧠 Memory trick:** *"UP/DOWN = VERTICAL (one machine gets taller or shorter). OUT/IN = HORIZONTAL (the crowd spreads wider or narrower)."*

**🚩 Exam trap:** A scenario says "the team added more virtual machine instances to handle Black Friday traffic." Test-takers under time pressure sometimes read "added more power" and pick vertical. It's **horizontal** — the *count* of machines changed, not the size of any one machine.

**Connection back to Module 1:** This is the concrete mechanism *behind* the consumption-based model. Scalability is *how* you avoid paying for idle capacity — you literally shrink/grow resources instead of guessing capacity needs upfront like the old CapEx model forced you to.

---

## 2. Reliability & Predictability

**Reliability:** The ability of a system to **recover from failures** and keep functioning. This is different from availability — availability is about *uptime*, reliability is about *resilience when things break*. Related but distinct ideas, and both can be tested separately.

Azure achieves this mainly through **geographic redundancy** — deploying resources across multiple regions around the world, so a disaster in one region doesn't take your whole application down. Depending on setup, failover to another region can even happen automatically.

**Predictability** has two sides:
- **Performance predictability** — will the app perform consistently under load? Driven by autoscaling (see Section 1) and **load balancing** (spreading traffic across resources so no single one gets overwhelmed).
- **Cost predictability** — can you forecast what you'll spend? Driven by real-time usage tracking, monitoring, and tools like the **Azure Pricing Calculator** (the same one from your Module 1 hands-on tie-in).

**Gap fill — Azure Well-Architected Framework:** Both reliability and predictability sections in your module reference this framework by name without explaining it, so here's the definition (confirmed current against Microsoft's site): it's a set of **5 pillars** Microsoft recommends for evaluating any Azure architecture:
1. Reliability
2. Security
3. Cost Optimization
4. Operational Excellence
5. Performance Efficiency

⏭️ **SKIM:** At AZ-900 level, you just need to recognize the *name* and that it exists as a design framework with these 5 pillars — you won't be asked to apply it in depth. That level of application shows up in role-based exams like the Azure Solutions Architect path.

**🚩 Exam trap:** Don't confuse "reliability" (a *pillar* of the Well-Architected Framework and a general cloud benefit) with "high availability" (a *specific technique/metric*, usually tied to SLA %). A question might describe automatic regional failover and ask which *benefit* this demonstrates — the answer is reliability, not availability, because the key word is "recovering from failure," not "staying up."

---

## 3. Security & Governance

This section is really **Module 1's shared responsibility model, applied**. Quick recap tie-in: your service model (IaaS/PaaS/SaaS) determines how much security work is yours vs. Microsoft's — this module just adds the *tools* that help you manage that responsibility at scale.

**Governance tools mentioned (all previews — full depth comes in the "Azure management and governance" domain later, worth 30–35% of the whole exam):**
- **Templates** — deploy resources in a pre-approved, standardized way so nothing drifts from your technical/regulatory standards.
- **Cloud-based auditing** — automatically flags resources that fall out of compliance and suggests fixes.

⏭️ **SKIM these two for now** — you'll meet them again by name (Azure Policy, ARM templates) in the governance domain, where they get full treatment. Just recognize the *concept* here: "the cloud can enforce and check your standards automatically."

**Security mapped to service model (direct callback to Module 1):**
- **IaaS** → you get maximum control: you manage OS patches, installed software, everything above the physical layer.
- **PaaS/SaaS** → patches and maintenance are handled for you automatically.

**DDoS protection:** The module states cloud providers are "typically well suited to handle" Distributed Denial of Service attacks — this is because of Azure's scale and built-in network infrastructure protections. There's also a dedicated **Azure DDoS Protection** service for enhanced, workload-specific protection, which you'll encounter later in the networking/security material — no need to go deeper than "know it exists" right now.

**🚩 Exam trap:** Don't assume "cloud providers handle DDoS well" means security is now 100% Microsoft's job. Basic DDoS resilience at the infrastructure level is provider-side, but you still own configuring your own network security controls, application-level protections, and (per Module 1) your data and identities regardless of service model.

---

## 4. Manageability

Two distinct ideas that sound similar and get tested as a pair — this is the **second most-confused vocabulary pair** in this module, right after vertical/horizontal scaling:

| | Management **OF** the cloud | Management **IN** the cloud |
|---|---|---|
| What it means | What the cloud automatically *does* to your resources | The *tools/interfaces* you personally use to manage things |
| Examples | Autoscaling, template-based deployment, automatic health monitoring and replacement of failing resources, automatic alerts | Azure Portal (web UI), CLI (command line), PowerShell, APIs |

**🧠 Memory trick:** *"OF = Operations happening automatically. IN = Interfaces you personally use."* Or even simpler: **"OF" is passive (things happening to your resources), "IN" is active (you doing the managing).**

**🚩 Exam trap:** A question describing "an ops team monitors resource health through the Azure Portal and automates recurring tasks with PowerShell scripts" is testing **management IN the cloud** (tools/interfaces) — even though the word "automate" appears, which might tempt you toward "OF." The giveaway is that a *person* is choosing and using a specific interface (Portal, PowerShell) — that's "in," not "of."

---

## 5. Sustainability ⏭️ SKIM (lower exam priority — see note at top)

Cloud providers operate at large scale, generally improving resource utilization vs. many small, separate on-premises setups. Practices that support both sustainability *and* cost efficiency (notice the overlap with Module 1's consumption model):
- Scaling down when demand drops
- Deallocating unused resources
- Choosing efficient configurations to avoid overprovisioning
- Using monitoring to spot usage trends and optimize over time

**Real-world example from the module:** A dev environment that only needs to run during business hours can be automatically shut down overnight/weekends — same underlying idea as horizontal/vertical scaling, just applied with a sustainability lens instead of a pure cost lens.

---

## Key Takeaways

- **Vertical scaling** = resize one resource (up/down). **Horizontal scaling** = change the number of resources (out/in). This pairing is a top exam trap.
- **High availability** = uptime, backed by SLAs. **Reliability** = recovering from failure (often via multi-region redundancy). Related but distinct.
- **Predictability** covers both performance (autoscaling, load balancing) and cost (real-time tracking, pricing calculator).
- **Security/governance** in the cloud is Module 1's shared responsibility model in action, plus tools (templates, auditing) that enforce standards at scale.
- **Management OF the cloud** = automated things happening to resources. **Management IN the cloud** = the interfaces you use (Portal, CLI, PowerShell, APIs). Second major exam trap pairing.
- **Sustainability** is in the training content but currently absent from Microsoft's official published skills list for this section — know the concept, don't over-invest study time.
- **Well-Architected Framework** (gap-fill): 5 pillars — Reliability, Security, Cost Optimization, Operational Excellence, Performance Efficiency. Recognize it; don't go deep yet.

---

## Practice Questions

**Q1 (Easy).** A team increases the number of virtual machines behind their web application from 3 to 6 to handle a traffic surge. What type of scaling is this?

A) Vertical scaling (scale up)
B) Horizontal scaling (scale out)
C) Vertical scaling (scale down)
D) Horizontal scaling (scale in)

<details>
<summary>Answer</summary>

**Correct: B.** The *number* of VM instances increased — that's horizontal scaling, specifically "scaling out."
- A and C are wrong: vertical scaling means changing the size/power of a single resource, not the count.
- D is wrong: "scale in" means *reducing* the count, the opposite of what happened here.
</details>

---

**Q2 (Easy).** Which term describes a system's ability to recover from failure and continue functioning?

A) Availability
B) Scalability
C) Reliability
D) Predictability

<details>
<summary>Answer</summary>

**Correct: C — Reliability.** This is the specific definition given in the module.
- A (availability) is about uptime/SLAs, not recovery from failure specifically.
- B (scalability) is about adjusting resources to meet demand.
- D (predictability) is about forecasting performance or cost, not recovery.
</details>

---

**Q3 (Medium).** A company's operations team uses Azure PowerShell scripts to automate recurring maintenance tasks and checks resource health through the Azure Portal. Which manageability concept does this best represent?

A) Management of the cloud
B) Management in the cloud
C) High availability
D) Governance

<details>
<summary>Answer</summary>

**Correct: B — Management in the cloud.** The team is actively using specific tools/interfaces (PowerShell, Portal) — that's the defining trait of "in."
- A is the trap answer: "automate" sounds like it belongs with "of" (automatic things happening to resources), but the key detail is that people are *choosing and operating specific interfaces*, which is "in."
- C and D are unrelated to this specific scenario — no uptime guarantee or compliance/auditing is described.
</details>

---

**Q4 (Medium).** A retail company deploys its application across two Azure regions so that if one region experiences an outage, traffic automatically shifts to the other region with no manual intervention. Which cloud benefit is being demonstrated?

A) Scalability
B) Reliability
C) Manageability
D) Sustainability

<details>
<summary>Answer</summary>

**Correct: B — Reliability.** Automatic recovery from a regional failure is the textbook definition of reliability, achieved here through geographic redundancy.
- A is wrong: scalability is about adjusting resource *size or count* for demand, not failover between regions.
- C is wrong: manageability is about tools/automation for managing resources, not failure recovery specifically.
- D is wrong and a pure distractor — nothing about resource efficiency or waste reduction is described here.
</details>

---

**Q5 (Hard).** A company wants to forecast its monthly Azure spend with confidence and avoid unexpected billing surprises. Which combination of Azure concepts best supports this goal?

A) Horizontal scaling and Azure Arc
B) Real-time usage tracking, monitoring, and the Azure Pricing Calculator
C) Azure VMware Solution and vertical scaling
D) DDoS protection and resource locks

<details>
<summary>Answer</summary>

**Correct: B.** This is cost predictability specifically — tracking usage in real time, monitoring for efficiency, and using the pricing calculator to forecast costs, exactly as described in the module.
- A is wrong: horizontal scaling affects capacity, not cost forecasting directly, and Azure Arc is a cross-environment management tool, unrelated to cost prediction.
- C is wrong: both are unrelated to cost forecasting — VMware Solution is about workload migration, vertical scaling is about resource sizing.
- D is wrong: these are security/governance tools (from Section 3 and later modules), not cost-prediction tools.
</details>

---

**Q6 (Hard — cumulative, pulls in Module 1).** A company runs a private on-premises datacenter for its core banking system but deploys its customer-facing mobile app backend on Azure App Service (a PaaS offering), where Microsoft automatically patches the underlying platform. During a marketing campaign, the app backend automatically adds more instances to handle increased load, then scales back down afterward. Which THREE concepts are all being demonstrated? (Select the best combined answer.)

A) Public cloud only
B) Hybrid cloud deployment model + PaaS shared responsibility (Microsoft handles patching) + horizontal scaling
C) Multicloud deployment model + IaaS shared responsibility + vertical scaling
D) Private cloud only + manageability

<details>
<summary>Answer</summary>

**Correct: B.** This scenario deliberately combines concepts from both modules: (1) private datacenter + Azure together = **hybrid cloud** (Module 1); (2) Azure App Service is PaaS, and "Microsoft automatically patches the platform" is the PaaS point on the **shared responsibility model** (Module 1); (3) "adds more instances... then scales back down" is **horizontal scaling** (this module).
- A is incomplete — there's also a private datacenter involved, so "public cloud only" misses the hybrid element.
- C is wrong on two counts: there's only one public provider (Azure) so it's not multicloud, and App Service being PaaS with automatic patching is the opposite of IaaS-level self-management. The instance count changing (not resource size) is horizontal, not vertical.
- D is wrong: the Azure App Service portion is public cloud, not private, so "private cloud only" misses that half of the picture.
</details>

---

*Next up: likely "Describe cloud service types" (IaaS/PaaS/SaaS in full) — this will formalize the shared-responsibility spectrum you've been previewing across both modules so far. Paste it whenever you're ready.*
