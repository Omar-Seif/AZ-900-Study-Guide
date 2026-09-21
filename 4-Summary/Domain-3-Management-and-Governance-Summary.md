# AZ-900 Track Summary — Domain 3: Describe Azure Management and Governance
**Exam weight: 30–35%** · Covers Modules 9–12 (cost management, governance/compliance, management/deployment tools, monitoring)

Condensed review sheet for the final track. The recurring theme here is *which mechanisms inherit down the hierarchy and which don't* — that's the highest-yield concept in the whole domain.

---

## 1. Cost Management (Module 9)

**Six cost factors:** resource type, consumption, maintenance (watch orphaned resources), geography, subscription type, Azure Marketplace.

- **🚩 Billing zones ≠ Availability Zones.** Billing zones = geographic grouping of *regions* for **data-transfer pricing**. Inbound data is often free; **outbound is priced by billing zone.**
- **Pricing calculator** = estimate-only, provisions nothing, never charges. **The TCO calculator has been retired.**
- **Cost Management** = **Cost analysis** (visual/reporting) + **Cost alerts** + **Budgets** (can trigger automation, e.g., shut down non-prod at a threshold).
  - **Budget alerts** (anyone, user-defined threshold) · **Credit alerts** (EA prepayment, auto at **90% & 100%**) · **Department spending quota alerts** (EA, configurable %).
- **Cost optimization:** **Reservations** (commit to a specific resource, 1/3 yr, stable workloads) · **Savings plan for compute** (commit to an *hourly spend*, flexible across compute services) · **Spot** (unused capacity, cheapest, interruptible).

## 2. Governance & Compliance (Module 10)

- **Microsoft Purview** = data governance/risk/compliance. **Risk & Compliance** (M365-rooted, protect + regulatory) vs. **Unified Data Governance** (broad estate map, classification, lineage).
- **Azure Policy** = controls **what configurations are allowed** (vs. RBAC = *who can act*). Inherits downward, evaluates **existing** resources retroactively, can auto-remediate (e.g., add missing tags), supports exceptions, applies even to Copilot/AI-proposed changes. **Initiatives** = groups of related policies toward one goal.
- **Resource locks:** **Delete** (blocks deletion only) vs. **ReadOnly** (blocks modify *and* delete, like Reader role). Inherit downward. **Apply regardless of RBAC** — even an Owner must remove the lock first.
- **Service Trust Portal** = docs about Microsoft's own compliance practices (not on the official skills list — light awareness only).

**🌟 The single most valuable table in this domain — what inherits down the hierarchy:**
| Mechanism | Inherits down? | Overridable by high privilege (Owner)? |
|---|---|---|
| RBAC | ✅ Yes | It *is* the permission system |
| **Tags** | ❌ **No** | N/A |
| Azure Policy | ✅ Yes | ❌ No |
| Resource locks | ✅ Yes | ❌ No (must remove lock first) |

*Tags are the deliberate exception — which is exactly why Azure Policy exists to enforce them.*

## 3. Management & Deployment Tools (Module 11)

- **Five front doors:** Portal (GUI, present in every datacenter for resilience), Cloud Shell (browser shell, auto-authenticated), PowerShell (cmdlets), CLI (Bash), Copilot in Azure (AI assistant — not on skills list). **PowerShell and CLI are functionally equivalent — only syntax differs.**
- **All tools funnel through Azure Resource Manager (ARM)** — one management layer that authenticates/authorizes every request, which is *why* results are consistent across tools. RBAC is natively integrated into ARM.
- **Azure Arc** = projects non-Azure resources into ARM so you manage them *as if* in Azure. Servers/Kubernetes/data services/SQL Server = GA; **VMs = preview.**
- **Infrastructure as Code:** **ARM templates** = declarative JSON (can still call imperative scripts). **Bicep** = simpler authoring language that compiles down to the same ARM deployment.

## 4. Monitoring (Module 12)

- **Azure Advisor** = recommendations across 5 categories (Reliability, Security, Performance, Operational Excellence, Cost). **Recommends, doesn't enforce** (contrast: Policy enforces; Defender is deep security-specific).
- **Azure Service Health** = three nested views: **Azure Status** (global) → **Service Health** (your services) → **Resource Health** (one resource, incl. **"Azure's fault or yours"** = shared responsibility model applied).
- **Azure Monitor** = collects logs/metrics across Azure/on-prem/multicloud. **Log Analytics** = query tool. **Alerts** = rule + action group, metric- or log-based. **Application Insights** = web-app monitoring (SDK or no-code agent, availability tests).
  - **Action groups are shared across Advisor, Service Health, and Monitor** — one notification setup for all three.

---

## Cheat-Box: Recommends vs. Enforces vs. Reports

| Tool | Role |
|---|---|
| Azure Advisor | **Recommends** (take/postpone/dismiss) |
| Azure Policy | **Enforces** (block creation, auto-remediate) |
| Resource locks | **Prevents** (delete/modify), overrides RBAC |
| Service Health / Resource Health | **Reports** health status |
| Defender for Cloud | Deep **security** posture + threat response |

## Cheat-Box: Top Trap Pairs

| A | vs. | B |
|---|---|---|
| Billing zones (data-transfer pricing) | | Availability Zones (resiliency) |
| Reservations (commit to a resource) | | Savings plan (commit to hourly spend) |
| Budget alert (anyone) | | Credit alert (EA, auto 90/100%) |
| Tags (don't inherit) | | RBAC/Policy/locks (do inherit) |
| Advisor (recommends) | | Policy (enforces) |
| Delete lock (blocks delete only) | | ReadOnly lock (blocks modify + delete) |
| ARM templates (JSON) | | Bicep (simpler syntax, same engine) |
| PowerShell (cmdlets) | | CLI (Bash) — equivalent capability |

---

## Consolidated Scenario Table

| Scenario | Answer |
|---|---|
| Estimate a new app's cost before deploying | Pricing calculator |
| Moving data Europe→Asia costs more than within Europe | Billing zones |
| Auto alert at 90% of an EA prepayment, no setup | Credit alert |
| Custom alert at 80% of a monthly budget | Budget alert |
| Auto shut down non-prod resources at a spend threshold | Budget with automation |
| 24/7 stable production database, lowest cost | Reservations |
| Usage shifts across compute services but is steady overall | Savings plan for compute |
| Interruptible batch job, lowest cost | Spot pricing |
| Auto-add a missing tag to non-compliant resources | Azure Policy (auto-remediation) |
| Unified sensitive-data map across Azure, on-prem SQL, and S3 | Purview (Unified Data Governance) |
| Prevent deletion of a critical resource but still allow updates | Delete lock |
| Owner needs to delete a locked resource | Must remove the lock first |
| Manage on-prem SQL Server + another cloud's K8s as if in Azure | Azure Arc |
| Consistent results whether using Portal or CLI | Azure Resource Manager (shared layer) |
| Define end-state infrastructure, easier to read than JSON | Bicep |
| Portfolio-wide quick cost-saving suggestions | Azure Advisor (Cost) |
| Is a widespread Azure outage happening globally right now? | Azure Status |
| Is this VM's issue Azure's fault or mine? | Resource Health |
| Email when a VM's CPU exceeds 80% | Azure Monitor metric alert |
| Monitor a web app's response times, no code changes | Application Insights (agent-based) |
| One notification setup reused across Advisor/Monitor/Service Health | Action groups |

---

## Practice Questions

**Q1 (Easy).** Which tool has been retired?
A) Pricing calculator · B) Cost Management · C) TCO calculator · D) Azure Advisor
<details><summary>Answer</summary>**C — TCO calculator.**</details>

**Q2 (Easy).** Which resource lock allows modification but blocks deletion?
A) ReadOnly · B) Delete (CanNotDelete) · C) Contributor lock · D) Owner lock
<details><summary>Answer</summary>**B — Delete lock.** ReadOnly blocks both. C/D aren't lock types.</details>

**Q3 (Medium).** A tag `CostCenter: Marketing` is applied to a resource group. What's true?
A) All resources inside inherit it · B) Only the resource group has it; resources don't auto-inherit · C) It propagates up to the subscription · D) Tags can't be applied to resource groups
<details><summary>Answer</summary>**B.** Tags don't inherit — the opposite of RBAC.</details>

**Q4 (Medium).** Azure Policy restricts VM sizes. Six months later, pre-existing oversized VMs are found. What happens?
A) Nothing — only new resources are evaluated · B) Policy flags those pre-existing VMs as non-compliant too · C) They're auto-deleted · D) Policy ignores them
<details><summary>Answer</summary>**B.** Policy evaluates existing resources retroactively.</details>

**Q5 (Medium).** Best distinction of Azure Policy from RBAC?
A) RBAC controls compliant configs; Policy controls access · B) RBAC controls who can act; Policy controls what configs are allowed · C) Identical, different blade · D) Policy is a subset of RBAC
<details><summary>Answer</summary>**B.** Who (RBAC) vs. what-is-allowed (Policy), operating independently.</details>

**Q6 (Medium).** PowerShell vs. Azure CLI — key difference?
A) CLI only does networking · B) Functionally equivalent; syntax differs (cmdlets vs. Bash) · C) PowerShell needs local install; CLI doesn't · D) CLI can't run in Cloud Shell
<details><summary>Answer</summary>**B.** Same API, same capabilities, syntax preference only.</details>

**Q7 (Hard).** Bicep's relationship to ARM templates?
A) Separate engine, bypasses ARM · B) Simpler authoring language processed through the same ARM engine as JSON · C) ARM templates are being removed · D) Bicep is networking-only
<details><summary>Answer</summary>**B.** Bicep compiles down to the same ARM deployment.</details>

**Q8 (Hard — cumulative).** RBAC permissions at a management group auto-apply downward, but a tag applied there does NOT. Why?
A) Misconfiguration — tags should inherit · B) RBAC is designed to inherit; tags are designed NOT to — both intentional · C) Tags only fail to inherit above subscriptions · D) Both fail; it's an error
<details><summary>Answer</summary>**B.** Intentional, documented behavior for both.</details>

**Q9 (Hard — grand finale, whole-exam).** A PaaS web app across two Availability Zones, tagged and Policy-enforced, with a Delete lock and Application Insights. Resource Health shows a slowdown is the company's own app code, not Azure. Which foundational concept explains that distinction?
A) Consumption-based pricing · B) Shared responsibility model · C) Hybrid cloud · D) Horizontal scaling
<details><summary>Answer</summary>**B — Shared responsibility model.** Resource Health's "Azure's side or yours" is that Domain-1 model applied as a live diagnostic — the exam bookends on it.</details>

---

### Key Takeaways
- **Inheritance is the domain's spine:** RBAC, Policy, and locks inherit down and can't be bypassed by high privilege; **tags are the one exception** that doesn't inherit.
- **Recommends vs. enforces vs. reports:** Advisor recommends, Policy/locks enforce, Service Health/Monitor report.
- **Everything routes through ARM**, and **Azure Arc** extends that management reach beyond Azure — the through-line connecting governance, tooling, and monitoring.
