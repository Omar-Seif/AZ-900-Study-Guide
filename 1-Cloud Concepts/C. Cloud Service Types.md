# AZ-900 — Cloud Concepts (25–30% of exam)
## Module: Describe Cloud Service Types (IaaS / PaaS / SaaS)

> **Exam context:** This is the **last module in the "Describe cloud concepts" domain** — once you're solid here, you've fully covered a quarter to a third of the exam. This module also **pays off two previews** you've been carrying since Module 1 (shared responsibility model) and Module 2 (PaaS's built-in scalability/HA). Nothing new conceptually — this is where it all clicks into one picture.
>
> **What's next after this:** Domain 2, "Describe Azure architecture and services," is the **biggest chunk of the exam (35–40%)** — regions, resource groups, subscriptions, compute options, storage, identity, and security. Bigger domain, more modules — good time for a short breather before diving in if you want one.

---

## 1. The Three Service Types — Core Idea

All three are just **different amounts of the stack you manage yourself vs. Microsoft manages for you.** Same shared responsibility model from Module 1 — this module just names the three standard "stopping points" on that spectrum.

| | IaaS | PaaS | SaaS |
|---|---|---|---|
| Full name | Infrastructure as a Service | Platform as a Service | Software as a Service |
| Plain-language | Rent the hardware, you build everything on top | Rent a ready-made workbench + tools, you bring your own project | Rent the finished product, ready to use |
| Your responsibility | Most of the stack (OS, patching, network config, storage config) | Just your app code, data, and access settings | Just your data, identity/access, and device posture |
| Flexibility | Highest | Medium | Lowest |
| Ease of use | Lowest (most setup work) | Medium | Highest |

**🧠 Memory trick (classic industry analogy — worth knowing, comes up in real-world Azure conversations too):** Think of it like getting pizza.
- **On-premises** = you buy the ingredients and bake it yourself in your own oven.
- **IaaS** = you rent a fully-equipped kitchen and bake it yourself.
- **PaaS** = you order a pizza-making kit delivered — dough and toppings ready, you just assemble and bake.
- **SaaS** = you order the pizza fully made — you just eat it.

Each step hands off more of the work to someone else, and each step gives you less control in exchange.

---

## 2. The Responsibility Layer Stack (the #1 testable diagram in this module)

Cloud stacks are usually broken into these layers. Here's who manages each layer under each model:

| Layer | On-premises | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Applications | You | You | You | **Provider** |
| Data | You | You | You | You *(always yours — Module 1's "3 D's")* |
| Runtime | You | You | **Provider** | Provider |
| Middleware | You | You | **Provider** | Provider |
| Operating System | You | You | **Provider** | Provider |
| Virtualization | You | **Provider** | Provider | Provider |
| Servers | You | **Provider** | Provider | Provider |
| Storage (physical) | You | **Provider** | Provider | Provider |
| Networking (physical) | You | **Provider** | Provider | Provider |

Read it top-to-bottom, left-to-right: as you move from on-premises → IaaS → PaaS → SaaS, the "line" of what Microsoft owns keeps moving *up* the stack, taking over more layers each time.

**🧠 Memory trick:** *"IaaS: they own the building. PaaS: they own the building AND the toolshed. SaaS: they own everything except your stuff inside it."*

**🚩 Exam trap — the classic one:** A question shows this exact table (or describes it in words) and asks "who manages the operating system in PaaS?" The trap answer is "you" (reasoning: "I still write my app, so surely I touch the OS too"). **Wrong — in PaaS, Microsoft manages the OS.** You only touch your application and data. This single row (OS: customer in IaaS, provider in PaaS) is one of the most commonly missed points on the real exam.

**Connection back to Module 1:** This table *is* the shared responsibility model, just drawn out layer-by-layer instead of as "customer vs. provider vs. depends." Same concept, more granular view.

---

## 3. Use Cases — When to Pick Each One

### IaaS
- **Lift-and-shift migration:** Recreate your on-prem setup in the cloud pretty much as-is, then move your workloads over. You keep the same level of control you had on-prem, just on rented hardware.
- **Testing and development:** Need to spin up and tear down full environments fast, with complete control over configuration? IaaS lets you do that.

### PaaS
- **Development framework:** Build or customize cloud apps on top of built-in components — scalability, high availability, and multi-tenancy are already handled for you (direct callback to Module 2's scalability/HA content — PaaS is *how* you get those benefits without building them yourself).
- **Analytics/business intelligence:** Ready-made tools for mining data and finding patterns, without standing up the infrastructure behind them yourself.

### SaaS
- Email and messaging
- Productivity applications
- Finance and expense tracking

**🚩 Exam trap:** Both IaaS and PaaS scenarios mention "testing/development" or "development framework" — don't let the word "development" alone tell you the answer. The distinguishing detail is *control vs. convenience*:
- "We need to replicate exact on-prem configurations and fully control the environment" → **IaaS** (control-focused).
- "We want a framework with built-in scalability so we write less infrastructure code" → **PaaS** (convenience-focused).

**Scenario table:**

| Scenario | Correct service type |
|---|---|
| Moving an on-prem app to the cloud with minimal redesign | IaaS (lift-and-shift) |
| A QA team needs full control to spin up/tear down test environments rapidly | IaaS |
| Developers want to build a web app without managing servers or OS patching | PaaS |
| A team wants a data analytics tool with insights/pattern detection built in | PaaS |
| A company adopts a cloud email platform instead of running its own mail server | SaaS |
| A finance team uses a cloud expense-tracking app they don't install or maintain | SaaS |
| A company already uses VMware on-prem and wants to relocate those exact workloads to Azure *(callback to Module 1)* | Azure VMware Solution — still fundamentally IaaS-style responsibility |

---

## 4. Gap Fill / Nuance: Where Does Serverless Fit?

You met **serverless computing** back in Module 1 as a gap-fill (it wasn't in that module's source text but is officially in scope). Now that you have the full IaaS/PaaS/SaaS picture, here's where it fits: serverless (e.g., Azure Functions) behaves like an **extreme version of PaaS** — Microsoft manages everything below your code, including scaling it to zero automatically. It's not usually tested as a separate 4th category on AZ-900; think of it as "PaaS, but you don't even think about the platform sizing."

⏭️ **SKIM:** You don't need a precise taxonomy debate here — just don't be thrown off if a question describes serverless characteristics and expects you to recognize it aligns closest to the PaaS end of the responsibility spectrum.

---

## Key Takeaways

- **IaaS** = rent hardware, manage almost everything above it. Best for lift-and-shift and full-control test/dev.
- **PaaS** = rent a development platform, manage only your app + data. Best for building apps fast with built-in scalability/HA, or analytics tools.
- **SaaS** = rent the finished application, manage only data/identity/access/devices. Best for email, productivity, finance tools.
- **The layer stack table** is the single most testable artifact in this module — especially "who owns the OS" (customer in IaaS, provider in PaaS/SaaS).
- Control decreases and convenience increases as you move IaaS → PaaS → SaaS — but data, identity, and device management are **always yours**, no matter which one (Module 1 callback).
- Serverless ≈ the far end of PaaS, not a separate tested category.

---

## Practice Questions

**Q1 (Easy).** A company wants to move its on-premises application to Azure with minimal changes, keeping full control over the operating system and configuration. Which service type fits best?

A) SaaS
B) PaaS
C) IaaS
D) Serverless

<details>
<summary>Answer</summary>

**Correct: C — IaaS.** Lift-and-shift with full OS/configuration control is the textbook IaaS use case.
- A is wrong: SaaS gives you a finished app, no infrastructure control at all.
- B is wrong: PaaS hands OS management to Microsoft — the company wants to keep that control.
- D is wrong: serverless abstracts away infrastructure entirely, the opposite of "full control."
</details>

---

**Q2 (Easy).** In a PaaS model, who is responsible for patching the operating system?

A) The customer
B) The cloud provider
C) Shared equally
D) A third-party vendor

<details>
<summary>EAnswer</summary>

**Correct: B — The cloud provider.** In PaaS, Microsoft manages the OS, runtime, and middleware; the customer only manages application code and data.
- A is the classic exam trap — many people assume they still touch the OS because they're still writing code, but the OS layer belongs to the provider in PaaS.
- C and D don't reflect how PaaS responsibility actually splits.
</details>

---

**Q3 (Medium).** A development team wants to build a web application quickly, using built-in scalability and high-availability features, without configuring servers or patching an operating system. Which service type — and which prior benefit from your studies — does this best combine?

A) IaaS; consumption-based model
B) PaaS; scalability and high availability (Module 2)
C) SaaS; shared responsibility model
D) IaaS; reliability

<details>
<summary>Answer</summary>

**Correct: B.** This is PaaS by definition (no server/OS management), and the "built-in scalability and high availability" phrase is a direct callback to Module 2's benefits — PaaS is one of the main ways you actually *get* those benefits without building them yourself.
- A is wrong: IaaS would still require the team to manage servers/OS, contradicting the scenario.
- C is wrong: SaaS wouldn't let a team "build a web application" at all — SaaS is a finished product, not a dev platform.
- D pairs IaaS with the wrong benefit and the wrong service type for this scenario.
</details>

---

**Q4 (Medium).** Which of the following is a SaaS characteristic, according to the shared responsibility model?

A) The customer manages the operating system and middleware.
B) The customer manages only data, identity/access, and device posture.
C) The customer manages virtualization and physical servers.
D) The provider has no responsibility for application maintenance.

<details>
<summary>Answer</summary>

**Correct: B.** SaaS pushes almost the entire stack to the provider — the customer's remaining responsibility is data, identity/access, and device posture, matching Module 1's "3 D's are always yours" rule.
- A describes IaaS, not SaaS.
- C describes on-premises or, partially, a misunderstanding of IaaS — customers never manage physical servers/virtualization in any cloud model.
- D is backwards — in SaaS, the provider handles *almost all* application maintenance, not none.
</details>

---

**Q5 (Hard — cumulative, pulls in Modules 1 & 2).** A company runs its regulated core banking system in its own private datacenter. It also uses Azure App Service (PaaS) to host a customer-facing web app, which automatically adds instances during peak login times each morning and scales back down overnight. Which combination of concepts does this scenario demonstrate?

A) Public cloud only + vertical scaling + SaaS shared responsibility
B) Hybrid cloud deployment model + horizontal scaling + PaaS shared responsibility
C) Multicloud deployment model + horizontal scaling + IaaS shared responsibility
D) Private cloud only + reliability + SaaS shared responsibility

<details>
<summary>Answer</summary>

**Correct: B.** Private datacenter + Azure = **hybrid cloud** (Module 1). "Adds instances... scales back down" = changing the *count* of resources = **horizontal scaling** (Module 2). Azure App Service is **PaaS**, where Microsoft manages OS/runtime/middleware and the company only manages its app and data (Module 1 + this module).
- A is wrong on all three counts: it's not public-only (there's a private datacenter too), instance count changing is horizontal not vertical, and App Service is PaaS, not SaaS.
- C is wrong: only one public provider (Azure) is involved, so it's not multicloud; and App Service being PaaS means it's not IaaS-level responsibility.
- D is wrong: the App Service portion is public cloud (via Azure), so "private cloud only" ignores half the scenario; and "reliability" isn't the concept being tested here — "scaling" is more precise.
</details>

---

**Q6 (Hard — domain wrap-up).** Which of the following statements about cloud service types is FALSE?

A) In IaaS, the customer is responsible for OS patching.
B) In SaaS, the customer never has any security responsibility.
C) In PaaS, the customer manages application code and data but not the underlying OS.
D) Moving from IaaS to SaaS generally means giving up flexibility in exchange for ease of use.

<details>
<summary>Answer</summary>

**Correct: B is the FALSE statement (so it's the answer to select).** Even in SaaS, the customer still owns security responsibilities around data, identity/access management, and device posture — "never has any security responsibility" is too absolute and incorrect.
- A is a true statement (not the answer) — this is standard IaaS responsibility.
- C is a true statement (not the answer) — this matches the layer table exactly.
- D is a true statement (not the answer) — this is the core flexibility-vs-ease-of-use tradeoff across the whole spectrum.
</details>

---

## Domain 1 Complete ✅
You've now covered all of "Describe cloud concepts" (25–30% of the exam): cloud computing basics, shared responsibility, deployment models, consumption-based pricing, benefits (HA, scalability, reliability, predictability, security, governance, manageability), and now the three service types. The shared responsibility model has been the throughline across all three modules — it's worth being able to explain it cold before moving on.

*Next up: Domain 2, "Describe Azure architecture and services" (35–40% — the biggest domain). Expect modules on regions/availability zones, resource groups/subscriptions/management groups, compute options, storage, and identity/security. Paste the first module whenever you're ready.*
