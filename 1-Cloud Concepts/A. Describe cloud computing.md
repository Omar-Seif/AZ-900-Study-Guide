# AZ-900 — Cloud Concepts (25–30% of exam)
## Module: Describe Cloud Computing

> **Exam context:** This module maps to the *smallest-weighted* domain on the exam (25–30%), but it's the foundation everything else builds on — IaaS/PaaS/SaaS, security, governance, and cost management all lean on the vocabulary from this module. Get this rock-solid and the rest of the exam gets easier to reason about.
>
> **Calibration note:** You're starting from zero IT background, so nothing below assumes prior knowledge. Where the source module uses a term without defining it, I've defined it.

---

## 1. What Is Cloud Computing?

**Plain-language definition:** Cloud computing means renting computing power (servers, storage, databases, networking, etc.) from a company like Microsoft, Amazon, or Google — over the internet — instead of buying and owning physical machines yourself.

To understand why that matters, contrast it with the old way:

| | Traditional (on-premises) | Cloud |
|---|---|---|
| Who owns the hardware? | You / your company | The cloud provider (Microsoft, in Azure's case) |
| Where does it live? | A room or building you control ("on-prem" = "on your own premises") | The provider's datacenter, somewhere else |
| How fast can you get more capacity? | Weeks/months (order, ship, install hardware) | Minutes |
| How do you pay? | Buy it upfront | Rent it, pay for what you use |

**Key traits of cloud computing** (the exam likes these as a checklist):
- **On-demand** — you request resources and get them almost instantly.
- **Internet-delivered** — you access it over a network, not by walking into a server room.
- **Elastic** — you can scale up or down as needs change.
- **Broad service range** — not just "servers," but storage, databases, AI/ML, IoT, and more.

**Real-world example (from your module):** A retail company expecting a traffic spike during a big seasonal launch doesn't need to buy extra physical servers months in advance. They spin up extra cloud capacity for the launch window, then scale back down. No idle hardware sitting around the rest of the year.

---

## 2. The Shared Responsibility Model

**The core idea:** When you move to the cloud, responsibility for security and maintenance gets *split* between you (the customer) and Microsoft (the provider). Nobody is 100% responsible for everything anymore.

**Beginner analogy:** Think of the difference between owning a house vs. renting an apartment vs. staying in a hotel.
- **Owning a house (on-premises):** You fix the roof, the plumbing, the locks, everything.
- **Renting an apartment (cloud, roughly IaaS-level):** The landlord maintains the building, the electrical wiring, the physical security of the property. You're still responsible for locking your own door and not leaving your valuables out.
- **Hotel (cloud, roughly SaaS-level):** Almost everything is handled for you — cleaning, security, maintenance. You're mainly responsible for your suitcase and your room key.

**What's ALWAYS yours (the customer), no matter what:**
- Your data and information
- The devices allowed to connect (phones, laptops, etc.)
- The identities/accounts of your people, services, and devices

**What's ALWAYS the provider's (Microsoft), no matter what:**
- The physical datacenter building
- The physical network
- The physical host servers

**What "depends"** (operating systems, network controls, applications, identity/access, infrastructure) — this shifts based on which **service model** you're using:

| | IaaS | PaaS | SaaS |
|---|---|---|---|
| Who has more responsibility? | You (customer) | Split evenly | Microsoft (provider) |
| What Microsoft always covers | Physical security, power, connectivity | Same, plus more | Nearly everything |

> **Preview only — full detail comes in your next module:** IaaS/PaaS/SaaS get their own dedicated coverage soon. For *this* module, you only need to know the concept exists and roughly maps responsibility from "mostly you" (IaaS) to "mostly Microsoft" (SaaS). ⏭️ **SKIM** — don't try to master the three service types here.

**🚩 Exam traps:**
- A question might imply the cloud provider handles "all security." **False.** Microsoft secures the physical infrastructure ("security *of* the cloud"); you secure your data, identities, and access ("security *in* the cloud").
- Don't assume identity/access is 100% the provider's job even in SaaS. You still manage *who* has accounts and what they can do — the provider just runs the authentication platform (e.g., Microsoft Entra ID) underneath it.
- If a question describes "installing SQL Server yourself on a VM you deployed," that's **IaaS thinking** — you own the database patching, not Microsoft. Contrast with "using an Azure SQL Database" (a managed PaaS service), where Microsoft patches the database engine for you.

**🧠 Memory trick:** *"3 D's are always yours: Data, Devices, iDentities."* Everything else slides along a scale depending on IaaS/PaaS/SaaS.

---

## 3. Cloud Deployment Models

This is a different axis from "who's responsible for what" — this is about **where** the cloud infrastructure physically lives and **who else** can use it.

| Model | What it is | Best for |
|---|---|---|
| **Public cloud** | Built and run by a third party (Microsoft); shared infrastructure, open to anyone who pays | No upfront capital cost, fast provisioning, don't need full control |
| **Private cloud** | Dedicated to a single organization — could be on your own site *or* hosted by a third party just for you | Full control, data isolation, strict compliance needs |
| **Hybrid cloud** | Public + private, connected together | Flexibility — keep sensitive workloads private, burst to public cloud for extra capacity or specific services |
| **Multicloud** | Using more than one *public* cloud provider (e.g., Azure + AWS) | Using best-of-breed features from different vendors, or mid-migration between providers |

**🚩 Exam trap — the #1 confusion in this section:** Hybrid cloud and multicloud sound similar but are **not the same thing.**
- **Hybrid** = private + public, working together.
- **Multicloud** = two or more *public* cloud providers.
You can technically be both hybrid *and* multicloud at once (private datacenter + Azure + AWS), but they answer different questions on the exam: "combining private and public" → hybrid. "Using two public vendors" → multicloud.

**🚩 Second trap:** Don't assume "private cloud" always means "in my own building." A private cloud can be hosted by a third party in *their* datacenter — the defining feature is that it's **dedicated to one organization**, not where the hardware physically sits.

### Azure Arc vs. Azure VMware Solution
These two get mixed up constantly because they're both about "Azure + something that isn't purely Azure."

| | Azure Arc | Azure VMware Solution |
|---|---|---|
| What it does | **Manages** resources across public cloud, private/on-prem, and multicloud from one place | **Runs** your existing VMware workloads on Azure infrastructure |
| Think of it as | A remote control / management umbrella | A moving service — picks up your VMware setup and places it in Azure |
| Use case | You have servers everywhere and want one governance/monitoring pane | You're already invested in VMware and want to shift to Azure without rebuilding everything |

**🧠 Memory trick:** *"Arc = manages from afar. VMware Solution = moves your (V)Ware furniture in."*

**Scenario table:**

| Scenario | Correct model/tool |
|---|---|
| A hospital must keep patient data on infrastructure it fully controls, for compliance | Private cloud |
| A startup wants zero upfront hardware cost and to scale fast | Public cloud |
| A bank keeps core transaction systems in its own datacenter but bursts extra reporting workloads to Azure during month-end | Hybrid cloud |
| A company uses Azure for compute but AWS S3 for storage because they started there | Multicloud |
| A company wants one dashboard to manage servers in their own datacenter, Azure, and AWS | Azure Arc |
| A company already runs VMware on-prem and wants to relocate those exact workloads to Azure with minimal rework | Azure VMware Solution |

---

## 4. The Consumption-Based Model & Pricing

**Core idea:** In the cloud, you pay for what you use, when you use it — not for capacity sitting idle.

**CapEx vs. OpEx** (this vocabulary shows up constantly in Azure exams, not just AZ-900):
- **CapEx (Capital Expenditure):** Big upfront spending on physical stuff — buying servers, building a datacenter. Traditional on-prem IT.
- **OpEx (Operational Expenditure):** Ongoing, pay-as-you-go spending over time. **Cloud computing is classified as OpEx.**

**🧠 Memory trick:** *"CapEx = Cash paid upfront (Capital). OpEx = Ongoing Payments (Operate)."*

**Why this matters for capacity planning:**

| | Traditional | Cloud |
|---|---|---|
| Overestimate demand | You paid for idle hardware | You just... don't use the extra capacity, no waste |
| Underestimate demand | App performance suffers until new hardware arrives (weeks/months) | Add more resources in minutes |

**Benefits of consumption-based pricing:**
- No upfront hardware cost
- No paying for unused capacity
- Scale up when demand rises, scale down when it drops
- Easier operational cost planning

**🚩 Exam trap:** A question might describe a company that pays a large sum upfront to reserve capacity for 1–3 years (this is a *real* Azure pricing option called Reserved Instances) and ask if that's still "consumption-based." It's still cloud/OpEx overall, but don't confuse it with the *pure* pay-as-you-go model — reservations trade some flexibility for a discount. (Full pricing model comparisons come later in the "management and governance" domain — flagging it now so it's not a surprise.)

---

## 5. Gap Fill: Serverless Computing

**Not in your pasted module, but officially listed under this exact skill area** ("Describe cloud computing") in Microsoft's current skills-measured document — so it's fair game on the exam even though this particular page didn't cover it. It'll likely get fuller treatment in an upcoming module on compute options, but here's a working definition so you're not blindsided:

**Serverless computing:** You write code and run it in the cloud *without* managing any servers — no provisioning, no patching, no capacity planning. You pay only for the compute time your code actually uses, and it automatically scales (including down to zero when nothing's running). Azure's core example is **Azure Functions**.

**🚩 Exam trap:** "Serverless" doesn't mean there are no servers — it means *you* never see or manage them. Microsoft still runs servers underneath; you just don't think about them.

⏭️ **SKIM for now** — just know the definition and that it's the most extreme end of "pay only for what you use." Deeper serverless-vs-VM-vs-container comparisons belong in the Azure architecture/compute module.

---

## Key Takeaways

- **Cloud computing** = renting IT resources over the internet, on-demand, elastic, pay-for-use.
- **Shared responsibility model**: Data, devices, and identities are *always* yours. Physical datacenter/network/hosts are *always* Microsoft's. Everything else depends on IaaS/PaaS/SaaS.
- **Deployment models**: Public (shared, third-party), Private (dedicated, control), Hybrid (private + public combined), Multicloud (2+ public providers). Don't confuse hybrid with multicloud.
- **Azure Arc** manages resources across environments; **Azure VMware Solution** relocates existing VMware workloads into Azure.
- **Consumption-based model** = OpEx, not CapEx. Pay only for what you use; scale in either direction without hardware lead time.
- **Serverless** (gap-fill): no server management, pay only for actual execution time, scales to zero.

---

## Practice Questions

**Q1 (Easy).** A company wants to avoid spending money on physical servers and instead pay only for the computing resources it actually uses each month. Which cloud characteristic does this describe?

A) Shared responsibility model
B) Consumption-based model
C) Private cloud
D) Azure Arc

<details>
<summary>Answer</summary>

**Correct: B — Consumption-based model.** This is the definition of paying only for what you use, classified as OpEx.
- A is wrong: shared responsibility is about *who secures/manages what*, not about billing.
- C is wrong: private cloud is about dedicated infrastructure, not pricing.
- D is wrong: Azure Arc is a management tool, unrelated to billing model.
</details>

---

**Q2 (Easy).** Which of the following is always the customer's responsibility, regardless of whether they use IaaS, PaaS, or SaaS?

A) The physical datacenter
B) The physical network hardware
C) The organization's data and account identities
D) The underlying host servers

<details>
<summary>Answer</summary>

**Correct: C.** Data and identities are always the customer's responsibility no matter the service model.
- A, B, and D are always Microsoft's responsibility — the customer never manages physical infrastructure in any service model.
</details>

---

**Q3 (Medium).** A company runs its core financial systems in its own private datacenter for regulatory reasons, but temporarily uses Azure to handle a spike in demand during year-end reporting. What cloud deployment model is this?

A) Multicloud
B) Public cloud
C) Hybrid cloud
D) Community cloud

<details>
<summary>Answer</summary>

**Correct: C — Hybrid cloud.** Combining private (core systems, regulatory control) with public (Azure, for a temporary burst) is the textbook hybrid scenario.
- A is wrong: multicloud means using two or more *public* providers — here there's only one public provider (Azure) plus a private datacenter, not two public ones.
- B is wrong: they're not using public cloud exclusively.
- D isn't a term used in AZ-900 — it's a distractor with no real definition here.
</details>

---

**Q4 (Medium).** A company currently manages servers in its own datacenter, in Azure, and in AWS, and wants a single place to apply consistent governance and monitoring policies across all three. Which Azure offering fits this need?

A) Azure VMware Solution
B) Azure Arc
C) Azure Resource Manager
D) Azure Policy alone

<details>
<summary>Answer</summary>

**Correct: B — Azure Arc.** Arc is specifically designed to extend Azure management to resources across on-premises, multicloud, and edge environments.
- A is wrong: VMware Solution is about running VMware workloads *in* Azure, not managing multiple environments.
- C is a distractor: Resource Manager is the deployment/management layer *within* Azure itself, not across other clouds — this term will matter more in the governance domain later.
- D is a partial-truth distractor: Azure Policy is a governance tool but doesn't by itself extend across non-Azure environments — Arc is what makes that possible.
</details>

---

**Q5 (Hard).** A retail company deploys a virtual machine in Azure and installs its own SQL Server database on it, managing patches and updates itself. Separately, it also uses Azure SQL Database (a managed PaaS offering) for another workload. For the VM-hosted SQL Server, who is responsible for patching the database software?

A) Microsoft, because it's hosted in Azure
B) The company, because this is IaaS-style self-management
C) Shared equally between Microsoft and the company
D) Neither — Azure automatically patches all databases

<details>
<summary>Answer</summary>

**Correct: B.** Once you install your own database software on a VM you provisioned, you've taken on IaaS-level responsibility — you own OS and application-level maintenance, including database patching.
- A is wrong: Microsoft only manages the physical infrastructure and virtualization layer at the IaaS level, not software you installed yourself.
- C is wrong: for this *specific* scenario, patching responsibility is entirely on the company — "shared equally" describes PaaS more loosely, not this exact case.
- D is wrong and would only be true for the *Azure SQL Database* (PaaS) example mentioned in the same question — not the self-managed VM scenario being asked about. This distractor is designed to test whether you can tell the two scenarios apart within one question.
</details>

---

**Q6 (Hard).** A startup wants to minimize IT operations overhead entirely — no server management, automatic scaling down to zero cost during idle periods, paying only when code actually executes. Which compute approach best fits this requirement?

A) Azure Virtual Machines
B) Azure Virtual Machine Scale Sets
C) Serverless computing (e.g., Azure Functions)
D) Azure VMware Solution

<details>
<summary>Answer</summary>

**Correct: C.** "No server management" + "scales to zero" + "pay only for execution" is the definition of serverless computing.
- A and B both still involve managing virtual machines (even scale sets, which just automate VM scaling) — there's still a server concept to think about, unlike serverless.
- D is unrelated — it's about relocating existing VMware workloads, not minimizing operations overhead for new code.
</details>

---

*Next up: your next module will likely cover the benefits of cloud services (high availability, scalability, reliability) and cloud service types (IaaS/PaaS/SaaS) in full. Paste it whenever you're ready.*