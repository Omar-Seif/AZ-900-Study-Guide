# AZ-900 — Describe Azure Management and Governance (30–35% of exam)
## Module: Describe Features and Tools for Managing and Deploying Azure Resources

> **Exam context:** Checked against the official skills list — all 4 learning objectives (portal, Cloud Shell/CLI/PowerShell, Azure Arc, ARM/ARM templates) map directly, and **Bicep fits under the officially-listed "infrastructure as code" bullet** even though it isn't named separately. **One thing that's NOT on the official list: "Copilot in Azure."** It's covered briefly below since it appeared in the source material, but ⏭️ treat it as light awareness only, not exam-critical — similar to the AI-assisted-changes note you saw in the last module.
>
> **This module has the biggest "long-arc payoff" of the whole track so far: Azure Arc.** You first met it as a one-line mention in your very first module (Domain 1), then it resurfaced as the mechanism extending Defender for Cloud to hybrid machines (Domain 2), and now you get its full definition. Worth reading that section slowly — it's a genuine full-circle moment.
>
> **What's next:** "Describe monitoring tools in Azure" (Azure Advisor, Service Health, Azure Monitor) — the final module of the entire exam.

---

## 1. Five Ways to Interact with Azure

Azure portal, Azure PowerShell, Azure CLI, Azure Cloud Shell, and (increasingly) Copilot in Azure. All of them, regardless of which you pick, ultimately go through the same underlying management layer — **Azure Resource Manager** (Section 3) — which is *why* you get consistent results no matter which tool you use.

### Azure Portal
A web-based, unified GUI console — build, manage, and monitor everything from simple web apps to complex deployments, with custom dashboards and accessibility configuration options.

**🔗 Direct callback to Domain 2, Module 1:** The portal maintains **a presence in every Azure datacenter**, making it resilient to individual datacenter failures and reducing latency by being physically close to users wherever they are. This is the same "distributed across regions/datacenters" principle you learned for resiliency, now applied to the management tool itself. It also updates continuously with no maintenance downtime.

### Azure Cloud Shell
A **browser-based shell** — no local install or configuration needed. Supports both Azure PowerShell and the Azure CLI (Bash), so you pick whichever syntax you're comfortable with.

**🔗 Direct callback to Domain 2, Module 4 (RBAC):** Cloud Shell is **authenticated to your Azure credentials automatically** — it inherently knows who you are and exactly what your RBAC permissions allow, without any separate sign-in step.

### Azure PowerShell
Runs commands called **cmdlets**, which call the **Azure REST API** to perform management tasks. Can be run one-off, or scripted together to orchestrate complex, repeatable, automatable actions — from single-resource maintenance to deploying entire multi-resource infrastructures. Installable on Windows, Linux, and Mac, or used via Cloud Shell.

### Azure CLI
**Functionally equivalent to Azure PowerShell** — same capabilities, same access, same underlying API calls. The only real difference is **syntax**: PowerShell uses PowerShell cmdlet syntax, the CLI uses Bash syntax.

**🚩 Exam trap:** Don't overthink PowerShell-vs-CLI scenario questions — there's no meaningful *capability* difference between them. If a question seems to be testing "which one *can* do X," the real answer is usually "both can — it's a matter of language preference," unless the question is specifically about syntax style.

### Copilot in Azure ⏭️ Light awareness only
An AI assistant giving contextual, natural-language guidance for operations tasks (exploring services, drafting commands/scripts), sometimes coordinating multi-step tasks in an agent-like way.

**Important nuance kept from the source material, regardless of exam weight:** Treat Copilot as an operational *assistant*, not an authority — **still validate recommendations, confirm permissions, and review changes before applying them in production.**

---

## 2. Azure Arc — The Big Payoff

**Managing hybrid and multicloud environments gets complicated fast** — Azure's own tools naturally cover Azure resources, but what about your on-premises servers or resources in another public cloud?

**Core mechanism:** Azure Arc works with **Azure Resource Manager** to extend Azure's governance and monitoring to hybrid and multicloud environments — by **projecting your existing non-Azure resources into Azure Resource Manager**, so you manage them *as if* they were running in Azure.

**What Azure Arc lets you do:**
- Manage your entire environment together, non-Azure resources included, from one place.
- Manage multicloud/hybrid VMs, Kubernetes clusters, and databases using familiar Azure tools and management capabilities, regardless of where they actually live.
- Blend traditional ITOps with newer DevOps practices as you adopt cloud-native patterns.
- Configure **custom locations** — an abstraction layer on top of Arc-enabled Kubernetes clusters and cluster extensions.

**What it currently manages outside Azure:**

| Resource type | Status |
|---|---|
| Servers | GA |
| Kubernetes clusters | GA |
| Azure data services | GA |
| SQL Server | GA |
| Virtual machines | **Preview** |

**🚩 Exam trap worth knowing:** VM management via Arc is still in **preview**, not GA — a subtle but real distinction, and the exam's own disclaimer notes most questions focus on GA features, so don't assume VM management through Arc is a fully mainstream capability yet.

**🔗 Full-circle connection across your entire track:**
- **Domain 1, Module 1** — Arc was first mentioned as a tool for managing public/private/hybrid/multicloud environments together.
- **Domain 2, Module 4** — Arc resurfaced as the mechanism that extends **Microsoft Defender for Cloud's** full protection plans to non-Azure machines.
- **Here** — you now have the actual definition underneath both of those earlier mentions: Arc works by projecting non-Azure resources into Azure Resource Manager, which is *why* it can extend both governance (Policy, RBAC) and security tooling (Defender) to places that aren't natively Azure.

---

## 3. Azure Resource Manager (ARM)

**The deployment and management layer for all of Azure** — every single interaction with an Azure resource, from any tool, goes through ARM.

**Request flow:** A tool/API/SDK sends a request → ARM **authenticates and authorizes** it → ARM forwards it to the relevant Azure service, which performs the action. Because every tool funnels through this same layer, **you get consistent results and capabilities no matter which tool you used to make the request.**

**🔗 Direct callback to Domain 2, Module 4:** Remember that RBAC is "enforced on any action that passes through Azure Resource Manager"? This module gives you the other half of that same fact: **RBAC is natively integrated into ARM** — it's not a bolt-on, it's built into the same layer that handles every deployment/management action.

**Benefits of ARM:**
- **Declarative templates** instead of scripts — define *what* you want deployed (JSON), not *how* to deploy it step by step.
- Manage, deploy, and monitor a whole solution's resources **as a group**, not individually.
- **Consistent, repeatable re-deployment** throughout your development lifecycle.
- **Dependency management** — resources deploy in the correct order automatically.
- **RBAC natively integrated** — access control applies across all services through the same platform.
- **Tag support** — organize your subscription and support cost reporting (direct callback to your cost management module).

---

## 4. Infrastructure as Code (IaC)

**Core idea:** Manage infrastructure through code/templates instead of manual, click-by-click configuration. It can start simply — scripting with Azure CLI or PowerShell — and grow into fully repeatable environment deployments using ARM templates or Bicep.

### ARM Templates
**Declarative JSON** files describing your desired Azure resources. Azure **validates the template before deployment**, then orchestrates creation — handling correct ordering and parallelizing where possible. You define the *end state*; ARM handles *how* to get there.

**🚩 Exam trap — declarative vs. imperative:** ARM templates are declarative ("deploy this end state"), which is fundamentally different from a traditional script that imperatively lists out each step ("do this, then this, then this"). That said, **templates can still call PowerShell or Bash deployment scripts** for setup steps that need to happen before or after resource creation — so it's not purely one-or-the-other in practice.

**Benefits:** declarative syntax, repeatable results across environments, automatic dependency/parallel orchestration, modularity (reusable components/nested templates), extensibility (calling scripts when needed).

### Bicep
A **declarative language for deploying Azure resources through ARM** — same underlying engine and orchestration as ARM templates, just a simpler, more concise authoring syntax than raw JSON.

**Gap-fill worth knowing (not explicit in the source material, but clarifies the relationship the module's own objective asks about):** Bicep files are ultimately transformed into the same ARM template JSON under the hood — Bicep doesn't replace ARM's deployment engine, it's a friendlier way to *author* what ultimately becomes an ARM deployment.

**Benefits of Bicep:** tracks current Azure resource types/API versions, simpler/more readable syntax than JSON, idempotent (repeatable) deployments, the same built-in orchestration as ARM templates, and modularity via reusable Bicep modules.

**🧠 Memory trick:** *"ARM templates and Bicep are two different languages describing the same destination — Bicep is just an easier-to-write map that still gets processed by the same ARM engine."*

---

## Scenario Table

| Scenario | Correct tool |
|---|---|
| Need a graphical console with custom dashboards, accessible from any browser | Azure portal |
| Want a shell experience with zero local install, using either PowerShell or Bash syntax | Azure Cloud Shell |
| Need to script a repeatable deployment of dozens of connected resources | Azure PowerShell or Azure CLI (equivalent capability, syntax preference only) |
| Want AI-drafted commands but still plan to review them before production | Copilot in Azure |
| Need to manage on-premises SQL Server and a Kubernetes cluster in another cloud as if they were in Azure | Azure Arc |
| Need consistent results regardless of whether a colleague uses the portal or the CLI | Azure Resource Manager (same underlying layer for every tool) |
| Want to define desired end-state infrastructure in a file rather than scripting each step | ARM template or Bicep |
| Want the same deployment outcome as an ARM template, but easier to read/write | Bicep |
| Need a pre-deployment script to run before resources are created | ARM template calling a PowerShell/Bash deployment script |

---

## Key Takeaways

- **Portal, Cloud Shell, PowerShell, CLI, and Copilot** are five different front doors — but all requests ultimately pass through the same **Azure Resource Manager** layer, which is why results are consistent across tools.
- **PowerShell and CLI are functionally equivalent** — the only real difference is syntax (cmdlets vs. Bash).
- **Azure Arc** projects non-Azure resources into Azure Resource Manager, extending Azure governance/management (and, from earlier modules, security tooling like Defender for Cloud) to hybrid/multicloud environments. Servers, Kubernetes, Azure data services, and SQL Server are GA; VM management is still preview.
- **ARM** authenticates/authorizes every request, has RBAC natively built in, supports declarative templates, group deployment, dependency ordering, and tagging.
- **ARM templates** = declarative JSON, can still call imperative scripts when needed. **Bicep** = simpler authoring language for the same underlying ARM deployment engine.

---

## Practice Questions

**Q1 (Easy).** What is the primary functional difference between Azure PowerShell and the Azure CLI?

A) The CLI can only manage networking resources; PowerShell manages everything else.
B) They are functionally equivalent; the difference is command syntax (PowerShell cmdlets vs. Bash).
C) PowerShell requires local installation; the CLI does not.
D) The CLI cannot be used within Azure Cloud Shell.

<details>
<summary>Answer</summary>

**Correct: B.** Both call the same underlying Azure REST API and offer the same capabilities — the difference is purely syntax preference.
- A is wrong: neither tool is restricted to a resource category.
- C is wrong: both can be installed locally (Windows/Linux/Mac) or used via Cloud Shell without installation.
- D is wrong: Cloud Shell explicitly supports both PowerShell and the CLI.
</details>

---

**Q2 (Easy).** Why does the Azure portal remain highly resilient to individual datacenter outages?

A) It only runs in one specially protected datacenter.
B) It maintains a presence in every Azure datacenter.
C) It doesn't rely on Azure infrastructure at all.
D) It automatically fails over to Azure Arc during an outage.

<details>
<summary>Answer</summary>

**Correct: B.** The portal maintains a presence in every Azure datacenter, making it resilient to any single datacenter's failure and reducing latency globally.
- A is the opposite of the real design.
- C is incorrect — the portal is very much built on Azure's own infrastructure.
- D is unrelated — Azure Arc has nothing to do with portal availability.
</details>

---

**Q3 (Medium).** A company wants to manage an on-premises SQL Server and a Kubernetes cluster running in another public cloud, using familiar Azure management tools, as if those resources were running in Azure itself. Which service enables this?

A) Azure Resource Manager alone
B) Azure Cloud Shell
C) Azure Arc
D) Azure Policy

<details>
<summary>Answer</summary>

**Correct: C — Azure Arc.** Arc specifically projects non-Azure resources (including on-prem SQL Server and multicloud Kubernetes clusters) into Azure Resource Manager, enabling management as if they were native Azure resources.
- A is wrong: ARM is the layer Arc works *with*, but ARM alone doesn't reach outside Azure without Arc's projection mechanism.
- B is wrong: Cloud Shell is just a shell interface for running commands, not a hybrid/multicloud management extension.
- D is wrong: Azure Policy enforces compliance rules; it doesn't project non-Azure resources into Azure's management plane on its own (though it can then apply to Arc-managed resources once they're projected in).
</details>

---

**Q4 (Medium).** Which Azure Arc-manageable resource type is currently in preview rather than generally available?

A) Servers
B) Kubernetes clusters
C) SQL Server
D) Virtual machines

<details>
<summary>Answer</summary>

**Correct: D — Virtual machines.** VM management through Azure Arc is currently in preview.
- A, B, and C are all generally available (GA) resource types manageable through Arc.
</details>

---

**Q5 (Medium).** Why do the Azure portal, CLI, and PowerShell all produce consistent results when performing the same management action?

A) Each tool has its own separate management engine that happens to behave identically.
B) All requests, regardless of tool, are authenticated and processed through the same Azure Resource Manager layer.
C) Microsoft manually synchronizes behavior between the tools on a weekly basis.
D) Consistency is only guaranteed within Azure Cloud Shell, not across standalone tools.

<details>
<summary>Answer</summary>

**Correct: B.** Every tool sends requests through the same Azure Resource Manager, which authenticates, authorizes, and forwards the request — one shared layer, not independently-behaving tools.
- A is wrong: there isn't a separate engine per tool; they share ARM.
- C is wrong: consistency comes from architecture, not manual synchronization.
- D is wrong: consistency applies across all tools, not just within Cloud Shell.
</details>

---

**Q6 (Hard).** What is the relationship between Bicep and ARM templates?

A) Bicep is an entirely separate deployment engine that bypasses Azure Resource Manager.
B) Bicep is a simpler, more concise authoring language that is ultimately processed through the same ARM deployment engine as JSON-based ARM templates.
C) ARM templates are being fully replaced and will stop functioning once Bicep is adopted.
D) Bicep can only be used for networking resources, while ARM templates handle everything else.

<details>
<summary>Answer</summary>

**Correct: B.** Bicep offers simpler syntax but relies on the same underlying ARM orchestration and deployment engine as raw JSON ARM templates — it's an alternative authoring experience, not a different deployment mechanism.
- A is wrong: Bicep still deploys through Azure Resource Manager, not around it.
- C is wrong: nothing in the source material suggests ARM templates are being deprecated — both coexist as valid options.
- D is wrong: Bicep applies to any Azure resource, not a specific category.
</details>

---

**Q7 (Hard — cumulative, pulls together Domain 2 and Domain 3).** A company enforces RBAC permissions, applies an Azure Policy requiring specific tags, and uses Azure Arc to manage an on-premises server. All three of these ultimately rely on which common Azure layer?

A) Azure Cloud Shell
B) Azure Resource Manager
C) Azure Advisor
D) Microsoft Purview

<details>
<summary>Answer</summary>

**Correct: B — Azure Resource Manager.** RBAC is natively integrated into ARM (Domain 2), Azure Policy inherits and evaluates through the same management layer (Domain 3), and Azure Arc specifically works *with* ARM to project non-Azure resources into that same layer. All three governance/management mechanisms across your studies converge on ARM as the common foundation.
- A is wrong: Cloud Shell is just one interface for issuing commands, not the underlying management layer itself.
- C is wrong: Azure Advisor provides recommendations (covered in your next module) — unrelated to enforcing RBAC, Policy, or Arc's projection mechanism.
- D is wrong: Purview is about data governance/classification, not the resource management layer these three mechanisms share.
</details>

---

*Next up: "Describe monitoring tools in Azure" — Azure Advisor, Azure Service Health, and Azure Monitor (including Log Analytics, Azure Monitor alerts, and Application Insights). This is the final module of the entire AZ-900 exam. Paste whenever you're ready.*
