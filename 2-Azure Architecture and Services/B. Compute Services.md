# AZ-900 — Describe Azure Architecture and Services (35–40% of exam)
## Module: Describe Azure Compute Services

> **Exam context — two important scope checks this time:**
>
> 1. **The official skill area is actually "Describe Azure compute *and networking* services"** (a single combined bucket, confirmed against Microsoft's July 20, 2026 study guide). This session's pasted content covers the **compute half only** — VMs, containers, functions, hosting options. **Networking (virtual networks, subnets, peering, DNS, VPN Gateway, ExpressRoute, public/private endpoints) is officially part of this same skill area but hasn't been covered yet** — expect a networking-focused module to complete this bucket. Don't consider this domain sub-area "done" until that arrives.
> 2. **The "AI, machine learning, and IoT/Edge services" unit is NOT on the official AZ-900 skills-measured list at all.** I checked carefully — nothing about AI services, Azure ML, or IoT appears anywhere in the current published skills document. This appears to be extra Microsoft Learn training content that goes beyond what's actually tested on AZ-900. **I've marked that whole section ⏭️ SKIP for exam purposes.** That said, it's genuinely relevant to your later certs — AI-103 (Azure AI Apps and *Agents* Developer) is literally about the "agentic AI" pattern this module briefly introduces, so I've kept a light-touch summary for forward continuity rather than deleting it outright.

---

## 1. Virtual Machines (VMs) — IaaS Compute

**Direct callback to Domain 1:** VMs are the textbook IaaS example you met three modules ago. You control the OS and installed software top-to-bottom; Microsoft handles the physical hardware underneath.

**When VMs make sense:**
- Total control over the OS
- Running custom/legacy software
- Custom hosting configurations
- **Testing/development** — spin up configs fast, discard when done
- **Datacenter extension** — connect an on-prem network into Azure (this is the hybrid cloud pattern from Domain 1, made concrete)
- **Disaster recovery** — failover capacity sitting ready in Azure
- **Lift-and-shift migration** — direct callback to the IaaS use case from Domain 1's service-types module

**Images:** a VM image is a template that already bundles an OS plus tools (e.g., web-hosting components) — lets you deploy consistent VMs fast instead of configuring each one from scratch.

### VM Sizing and Families

You choose size (vCPUs/RAM), storage disks, and networking (VNet, public IP, ports) when provisioning. Sizes are grouped into **families** by workload focus:

| Family | Focus | Typical use |
|---|---|---|
| B-series | Burstable, cost-efficient | Dev/test with occasional CPU spikes |
| D-series | General purpose | Web servers, small-to-medium apps |
| E-series | Memory optimized | In-memory databases, analytics |
| F-series | Compute optimized | CPU-intensive workloads |
| M-series | Large memory | Large enterprise databases |
| L-series | Storage optimized | High-throughput storage/data processing |
| N-series | GPU-enabled | AI training/inference, graphics |

⏭️ **SKIM:** You don't need exact specs per family — just the general "which family fits which workload personality" mapping above.

**Naming convention (worth knowing — this is a realistic exam-style question format):**
`Standard_D2s_v5` breaks down as:
- **D** = family (general purpose)
- **2** = vCPU count
- **s** = supports Premium SSD storage
- **v5** = hardware generation

**🧠 Memory trick:** *"Family, Count, Storage-support, Version — FCSV, read left to right."*

### Scale and Resiliency: Scale Sets vs. Availability Sets

This is the **sharpest trap pair in this section** — both group VMs together, but for entirely different reasons.

| | VM Scale Sets | Availability Sets |
|---|---|---|
| Purpose | **Elasticity** — automatically add/remove identical VMs based on demand | **Resiliency** — reduce chance that one event takes out all your VMs |
| How it works | Centralized config, integrates with load balancing, auto scale in/out | Groups VMs by **update domains** and **fault domains** |
| Cost | You pay for the VM instances (scaling itself has no extra fee) | You pay for the VM instances (grouping itself has no extra fee) |

**Update domain vs. fault domain (within Availability Sets):**
- **Update domain** — VMs that can be rebooted together during *planned* maintenance (so not all your VMs go down for maintenance at once).
- **Fault domain** — VMs that share a potential *power or network* failure point (so a single hardware fault doesn't take out everyone).

**🧠 Memory trick:** *"Update domain = planned (maintenance reboots). Fault domain = unplanned (hardware breaks)."*

**🚩 Exam trap:** A question describing "VMs automatically add/removed based on traffic" is testing **scale sets**, not availability sets — even though both involve "grouping VMs." The giveaway word is **demand-based change in count** (scale sets) vs. **surviving a failure event** (availability sets, no count change).

**Direct callback to Domain 2, Module 1 (core architectural components):** The source material explicitly notes that in regions supporting **Availability Zones**, zone-based designs are often *preferred over* availability sets — because zones give broader isolation (separate physical datacenters, independent power/cooling/network) while availability sets only separate VMs within the *same* datacenter's racks and maintenance groupings. Same underlying goal (resiliency), bigger blast-radius protection with zones.

**🧠 Memory trick for the whole resiliency ladder:** *"Availability Set = different racks, same building. Availability Zone = different buildings, same region. Region Pair = different regions entirely."*

---

## 2. Azure Virtual Desktop

A desktop/app virtualization service — users access full Windows desktops and apps remotely from any device, while data and apps stay in Azure.

**When to use it:** centralized desktop/app access for distributed teams, contractors, or hybrid workers who all need the same standardized tools, policies, and security controls — without building separate VM-based desktop setups per user group.

**Key points:**
- Integrates with **Microsoft Entra ID** for identity/access (direct callback to identity previews from earlier modules).
- Reduces local data exposure — apps/data stay in the cloud session, not on the device.
- Supports both single-session and multi-session Windows experiences.

⏭️ **SKIM:** This is a fairly self-contained, low-trap topic — know what it is and its core use case; no complex comparisons needed here.

---

## 3. Containers

**Core idea:** Like VMs, containers let you run multiple isolated workloads on one host — but containers **don't require their own OS**, making them far lighter and faster to create, scale, and restart than VMs.

| | Virtual Machines | Containers |
|---|---|---|
| OS management | You manage a full OS per VM | No OS to manage — much lighter |
| Startup speed | Slower | Fast — designed for dynamic create/stop |
| Best for | Full control, legacy apps, custom configs | Lightweight, agile, microservices |

**Docker** is the most popular container engine; Azure supports it.

### Three Azure Container Options (increasing orchestration depth)

| Service | What it is | Best for |
|---|---|---|
| **Azure Container Instances** | Fastest, simplest way to run a single container — no VM management | Quick, one-off container runs |
| **Azure Container Apps** | Similar ease-of-use, but adds **built-in load balancing and scaling** | Apps that need to adapt to changing demand without managing infrastructure |
| **Azure Kubernetes Service (AKS)** | Full **container orchestration** — manages the lifecycle of a whole fleet | Complex, multi-container deployments at scale |

**🧠 Memory trick:** *"Instances = one container, fast and simple. Apps = Instances + auto-scaling/load balancing built in. AKS = the conductor managing an entire orchestra of containers."*

**Both Container Instances and Container Apps are PaaS offerings** — direct callback to Domain 1's PaaS section: you upload your container, Azure runs it, you don't manage underlying VMs.

**Microservice architecture use case:** Split an app into independent containers (e.g., front end, back end, storage) so you can scale, update, or replace each piece independently. Example from the module: if only the back end is under load, scale *just* the back end — front end and storage stay untouched.

**🚩 Exam trap:** Don't assume Container Instances and Container Apps are interchangeable in scenario questions — if the scenario mentions "built-in load balancing and automatic scaling without extra configuration," that's the giveaway for **Container Apps**, not plain Container Instances.

---

## 4. Azure Functions

**Direct payoff of the serverless gap-fill from Domain 1, Module 1:** Azure Functions is the concrete, named Azure product behind that "extreme PaaS" concept you previewed early on.

**Core idea:** Event-driven, serverless compute — no VMs or containers to keep running. An event (HTTP request, timer, message from another Azure service) "wakes" the function; when it's done, resources are automatically deallocated. **You're billed only for the CPU time actually used while running.**

**Best fit:** work that's triggered by an event and completes quickly (seconds or less), especially when demand is variable — Functions scale automatically to match.

**Stateless vs. stateful:**
- **Stateless (default):** behaves as if it restarts fresh every time it responds to an event.
- **Stateful (Durable Functions):** the runtime passes context through the function so it can track prior activity across a multistep process.

**Flexibility:** Functions are also a general compute platform — if your needs grow beyond serverless (e.g., you need to run on a virtual network or fully isolate the function), you can deploy outside the pure serverless model.

**🚩 Exam trap:** Don't assume "serverless" means Functions can't handle complex, multistep logic — Durable Functions specifically exist to track state across a longer-running, multistep workflow while still being event-driven.

---

## 5. Compute Types Compared — The Big Picture

This ties Sections 1, 3, and 4 together, and maps directly onto Domain 1's IaaS/PaaS/SaaS control-vs-convenience spectrum:

| | Virtual Machines | Containers | Functions |
|---|---|---|---|
| Service model flavor | IaaS | PaaS (Instances/Apps) or self-managed (AKS) | Serverless (extreme PaaS) |
| You manage | OS, patching, full stack above hardware | Just the app inside the container | Just your code |
| Startup/scale speed | Slowest | Fast | Fastest, scales to zero |
| Billing granularity | Pay while VM runs, regardless of load | Pay for container runtime | Pay only for actual execution time |

**🧠 Memory trick:** *"VMs = you drive the whole car. Containers = you drive a rental with the paperwork handled. Functions = you just call a taxi and pay per ride."*

---

## 6. Application Hosting Options

Three broad paths, forming a spectrum from **most control** to **least operational effort**:

**VMs** (most control, most familiar if new to cloud) → **Containers** (isolate/manage pieces independently) → **Azure App Service** (least operational effort).

### Azure App Service (PaaS — direct callback to Domain 1)

An HTTP-based service for hosting web apps, REST APIs, and mobile back ends — in the language of your choice, without managing infrastructure.

**Built-in benefits (this is where Domain 1's PaaS preview and Domain 2's HA/scalability content both cash out concretely):**
- Automatic scaling and high availability
- Built-in load balancing and traffic manager
- Supports Windows and Linux
- Automated deployment from GitHub, Azure DevOps, or any Git repo (continuous deployment)
- Multi-language support (.NET, .NET Core, Java, PHP, Python, Node.js, and more)

**Four app styles, all sharing the same underlying infrastructure and benefits:**
| Type | What it's for |
|---|---|
| Web apps | Standard web hosting (ASP.NET, Java, Ruby, Node.js, PHP, Python) |
| API apps | REST-based APIs, with Swagger support |
| WebJobs | Background tasks/scripts running in the same context as your app |
| Mobile apps | Backend for iOS/Android apps (data storage, social auth, push notifications) |

⏭️ **SKIM:** The exact language/framework support matrix and Mobile Apps implementation details (specific social auth providers, SDK names) are implementation-level detail, not AZ-900 material. Just know App Service is a flexible, multi-purpose PaaS hosting umbrella with these four flavors underneath it.

**🚩 Exam trap:** A scenario emphasizing "no infrastructure management, automatic scaling, built-in load balancing, supports multiple languages" is describing **App Service**, not Container Apps — even though both offer built-in scaling. The distinguishing detail is usually *HTTP-based web/API/mobile hosting* (App Service) vs. *containerized workloads specifically* (Container Apps).

---

## 7. AI, Machine Learning, and IoT/Edge Services — ⏭️ SKIP for AZ-900, Preview for Later Certs

**As flagged at the top: this entire section is absent from the official AZ-900 skills-measured list.** Treat everything below as background awareness only, not exam-prep material for this specific test.

**Why I kept it anyway:** your track continues into **AI-103 (Azure AI Apps and Agents Developer)**, and this module's "agentic AI" mention is a direct, named preview of that exam's subject matter.

Quick summary for continuity:
- **Azure AI services** — prebuilt APIs for language, speech, vision, document processing. Use when you want intelligent features without training your own model.
- **Azure OpenAI Service** — generative AI (chat, content generation) with built-in security/governance.
- **Agentic AI patterns** — combining a model + instructions + context + tool use for multistep goals, built from Azure AI services/OpenAI Service plus your own app logic. *(This is essentially AI-103's whole domain, in one sentence.)*
- **Azure Machine Learning** — for building/training/managing **custom** models (vs. AI services' prebuilt approach).
- **IoT decision triad** — IoT Hub (bi-directional cloud-device communication), IoT Central (simplified SaaS IoT platform), IoT Edge (runs workloads locally near devices).

**Tie to your Azure credits:** If you want a taste of what's coming in AI-103 without spending exam-prep time on it now, trying a simple Azure AI services API call (e.g., a prebuilt sentiment-analysis or vision endpoint) is a cheap, quick way to see the "prebuilt API" pattern in action — but this is optional exploration, not AZ-900 prep.

---

## Key Takeaways

- **VMs = IaaS.** Sizing = family + vCPUs + RAM + disk; naming pattern like `Standard_D2s_v5` encodes family/vCPU/premium-storage/generation.
- **Scale Sets** (elasticity, auto scale in/out) vs. **Availability Sets** (resiliency, via update domains + fault domains) — different purposes, both group VMs.
- **Availability Zones are often preferred over Availability Sets** where supported, since zones isolate across entire separate datacenters, not just racks within one.
- **Containers** are lighter than VMs (no OS to manage). **Instances** = simplest single-container PaaS. **Container Apps** = adds built-in scaling/load balancing. **AKS** = full orchestration for fleets.
- **Azure Functions** = serverless, event-driven, billed only for execution time; stateless by default, **Durable Functions** for stateful multistep workflows. This is the concrete payoff of Domain 1's serverless gap-fill.
- **App Service** = PaaS hosting umbrella (Web apps/API apps/WebJobs/Mobile apps) with built-in scaling, HA, load balancing, and multi-language support.
- **AI/ML/IoT is NOT on the AZ-900 exam** — noted for track continuity toward AI-103, not for this test.
- **Networking is still pending** — officially part of this same skill bucket, not yet covered.

---

## Practice Questions

**Q1 (Easy).** Which Azure compute option requires you to manage the operating system yourself?

A) Azure Functions
B) Azure Container Instances
C) Azure Virtual Machines
D) Azure App Service

<details>
<summary>Answer</summary>

**Correct: C — Virtual Machines.** As IaaS, VM OS management (patching, configuration) is the customer's responsibility.
- A, B, and D are all PaaS/serverless options where Microsoft manages the OS layer.
</details>

---

**Q2 (Easy).** A company wants VMs automatically added or removed based on real-time traffic demand, with built-in load balancing. What should they use?

A) Availability sets
B) VM Scale Sets
C) Fault domains
D) Azure Container Instances

<details>
<summary>Answer</summary>

**Correct: B — VM Scale Sets.** This is the definition of scale sets: centralized, identical, load-balanced VMs that auto scale based on demand.
- A is wrong: availability sets are about resiliency (surviving failures), not demand-based scaling.
- C is wrong: fault domains are a sub-concept *within* availability sets, not a scaling mechanism.
- D is wrong: this describes VM behavior, not containers.
</details>

---

**Q3 (Medium).** Two VMs are placed in different update domains within the same availability set. What does this protect against?

A) A regional natural disaster
B) Both VMs being rebooted simultaneously during planned maintenance
C) A software bug in the application code
D) Data being stored in the wrong geography

<details>
<summary>Answer</summary>

**Correct: B.** Update domains specifically group VMs so they're not all rebooted together during planned maintenance events.
- A is wrong: that's the role of region pairs, a much larger-scale protection.
- C is wrong: update domains address infrastructure maintenance timing, not application-level bugs.
- D is wrong: that's a data-residency concern, unrelated to update domains.
</details>

---

**Q4 (Medium).** A company operates in a region that supports Availability Zones. For a resiliency-critical workload, what does the module recommend as the generally preferred design choice?

A) Availability sets, because they are cheaper
B) Zone-based design, because it offers broader failure isolation across separate datacenters
C) VM Scale Sets, because they replace the need for resiliency planning
D) Region pairs only, since zones are redundant with pairs

<details>
<summary>Answer</summary>

**Correct: B.** Where zones are supported, zone-based designs are generally preferred over availability sets because they isolate across entire separate datacenters (independent power/cooling/network), a broader blast-radius protection than update/fault domains within one datacenter.
- A is wrong: availability sets and zones have no described cost difference in the source material; cost isn't the deciding factor here.
- C is wrong: scale sets address elasticity/demand, not failure resiliency — they don't substitute for resiliency planning.
- D is wrong: region pairs and availability zones protect against different scales of disruption (whole-region vs. zone-level) and aren't redundant with each other.
</details>

---

**Q5 (Medium).** Which container option provides built-in load balancing and automatic scaling out of the box, without requiring full orchestration management?

A) Azure Container Instances
B) Azure Container Apps
C) Azure Kubernetes Service
D) Azure Virtual Machines

<details>
<summary>Answer</summary>

**Correct: B — Azure Container Apps.** This is the specific differentiator between Container Apps and plain Container Instances: Apps adds built-in load balancing/scaling; Instances is the simpler, no-frills option.
- A is wrong: Container Instances is the simplest option but doesn't include built-in load balancing/scaling.
- C is wrong: AKS provides full orchestration, which is more management overhead than this scenario calls for.
- D is unrelated — VMs aren't a container option at all.
</details>

---

**Q6 (Hard).** A company needs a workflow triggered by an incoming REST request that completes in under a second, runs infrequently and unpredictably, and should incur no cost when idle. Which Azure compute option best fits, and why?

A) Azure Virtual Machines, because they offer the most control
B) Azure Kubernetes Service, because it can orchestrate any workload
C) Azure Functions, because it's event-driven, scales automatically, and bills only for execution time
D) Azure Virtual Machine Scale Sets, because they can scale to match demand

<details>
<summary>Answer</summary>

**Correct: C.** Every detail in the scenario — event-triggered (REST request), quick execution, unpredictable/infrequent demand, no idle cost — matches Azure Functions' defining characteristics exactly.
- A is wrong: VMs incur cost whether or not they're actively processing a request; there's no "zero cost when idle" with VMs.
- B is wrong: AKS is for orchestrating fleets of containers, overkill and cost-inefficient for a single quick, infrequent trigger.
- D is wrong: scale sets still involve running VM instances that cost money even at their minimum scale — they don't scale to zero cost the way Functions does.
</details>

---

**Q7 (Hard — cumulative, pulls in Domain 1 & Domain 2 Module 1).** A startup builds a customer-facing web app using Azure App Service (which automatically patches the underlying platform), deploys it in a region with Availability Zones enabled, and configures the app to survive a full regional outage by failing over to a paired region. Which THREE concepts, spanning multiple modules of your studies, does this scenario combine?

A) IaaS shared responsibility (Domain 1) + VM Scale Sets + sovereign regions
B) PaaS shared responsibility (Domain 1) + Availability Zones (Domain 2, Module 1) + region pairs (Domain 2, Module 1)
C) SaaS shared responsibility (Domain 1) + fault domains + multicloud
D) Serverless (Domain 1) + Container Apps + hybrid cloud

<details>
<summary>Answer</summary>

**Correct: B.** App Service is PaaS — Microsoft automatically patches the platform, matching Domain 1's shared responsibility model. Deploying across Availability Zones and configuring regional failover via a region pair are both concepts from Domain 2, Module 1 on core architectural components.
- A is wrong: App Service is PaaS, not IaaS (Microsoft handles patching here, contradicting IaaS-level self-management), and nothing in the scenario involves VM Scale Sets or sovereign/isolated regions.
- C is wrong: App Service is PaaS, not SaaS (SaaS would mean using someone else's finished application, not building your own on a platform); fault domains apply to availability sets, not zones; and only one cloud provider (Azure) is used, so it isn't multicloud.
- D is wrong: App Service isn't purely serverless (it's PaaS with automatic scaling, a distinct category), Container Apps aren't mentioned in the scenario at all, and there's no private/on-prem component described, so it isn't hybrid.
</details>

---

*Next up: expect a module completing this skill area with Azure virtual networking (VNets, subnets, peering, DNS, VPN Gateway, ExpressRoute, public/private endpoints), followed eventually by Azure storage services and identity/access/security — the remaining pieces of Domain 2. Paste whenever you're ready.*
