## Watch me do it here:
https://www.loom.com/share/47f037155cd04ca1bde9061ee5886657

# Standard Operating Procedure
## Deploy and Validate Azure Update Manager Patching with Terraform and VS Code


| Field | Details |
|---|---|
| **Lab Title** | Azure Update Manager Patching Lab |
| **Tools Used** | Terraform · VS Code · Azure Update Manager · Azure Portal |
| **Infrastructure** | Azure VMs provisioned via Terraform |
| **Maintenance Window** | 60 minutes · reboot enabled |


---

## Objective

Use Terraform and VS Code to deploy an Azure Update Manager lab, run update assessments to identify missing critical patches across target machines, install the missing updates, and verify that all machines are fully patched with zero pending updates remaining.

---

## Prerequisites

- Active Azure subscription with sufficient permissions
- Terraform installed and accessible from the terminal
- VS Code installed with the lab project folder open
- Azure Update Manager enabled for the target machines
- Azure CLI installed and authenticated

---

## Step 1 — Prepare the Azure Update Manager Lab Infrastructure

Provision the lab environment using Terraform before running any patch assessments.

- Open the Terraform project in **VS Code**
- Review the Terraform files to understand what Azure resources will be provisioned
- Authenticate to Azure:

```cmd
az login
```

- Initialize and apply the Terraform configuration:

```cmd
terraform init
terraform plan
terraform apply
```

- Confirm when prompted by typing `yes`
- Verify the target Azure VMs are provisioned and running before proceeding

> ⚠️ Do not run update assessments until the Terraform infrastructure is fully deployed.

---

## Step 2 — Understand the Purpose of the Update Assessment

Before running the assessment, understand what Azure Update Manager is evaluating.

- Azure Update Manager assesses patch status across **multiple machines simultaneously** — no need to check each one manually
- The assessment report identifies which machines are missing updates and flags critical patches that require immediate remediation
- Treat the assessment report as the source of truth for patch status
- This process scales to environments with many machines — the same workflow applies whether you have 3 VMs or 300

---

## Step 3 — Run the Update Assessment

Execute the assessment to identify missing patches across all target machines.

- In the **Azure Portal**, navigate to **Azure Update Manager**
- Select **Check for updates** on the target machines
- Wait for the assessment to complete
- Review the report output — note which machines are missing updates and identify any critical patches flagged
- Use the results to determine which machines require immediate patching

> ✅ Expected result: Assessment report shows missing critical updates on one or more target machines.

---

## Step 4 — Verify Missing Updates in the Azure Portal

Cross-reference the assessment output with the Azure Portal before starting remediation.

- Navigate to the affected machines in the Azure Portal
- Confirm the portal shows the same missing update status reported by the assessment
- Validate that the machines are currently missing critical updates before beginning installation
- Use this step to confirm assessment data accuracy — do not skip it

> 💡 Always verify assessment data in the portal before patching. A mismatch between the script output and the portal may indicate a stale assessment — re-run if needed.

---

## Step 5 — Start the Update Installation Workflow

Configure and launch the update installation job in Azure Update Manager.

- In **Azure Update Manager**, select the machines that need updates
- Choose **Install updates now** rather than scheduling for a future maintenance window
- Add all target machines to the update job
- Review the list of missing updates displayed before confirming — confirm security updates are included

---

## Step 6 — Configure the Maintenance Window and Reboot Settings

Set the maintenance window and reboot behavior before launching the installation.

- Review the updates listed — confirm security and critical updates are included
- Set the **maintenance window to 60 minutes**
- Enable **reboot if required** so the update process can complete without manual intervention
- Proceed through the wizard and confirm the job configuration before starting

> ⚠️ If reboot is not enabled and a patch requires a restart, the update will not fully apply. Always enable reboot for lab environments.

---

## Step 7 — Install Updates and Monitor Progress

Launch the update job and monitor its status until completion.

- Click **Install** to start the patching process
- Navigate to the **Update Manager history** view
- Refresh periodically to monitor job status — the job will show as **In Progress**
- Continue checking until the job status changes to **Succeeded**

> 💡 Refresh every 2–3 minutes rather than continuously — large patch jobs take time and excessive refreshing adds no value.

---

## Step 8 — Validate Successful Completion and Post-Update Status

Confirm all updates were applied and no pending patches remain.

- Confirm the update job status shows **Succeeded**
- Return to the **machines view** in Azure Update Manager
- Verify there are **no pending critical updates** remaining on any target machine
- Confirm all machines show a clean patch status
- Record the successful completion of the patching activity for documentation

> ✅ Expected result: All machines show 0 pending updates. Update job status: Succeeded.

---

## Cautionary Notes

- Do not assume updates are installed until the job status explicitly shows **Succeeded**
- If a reboot is required, allow it to occur — skipping a required restart leaves the patch incomplete
- Always verify the correct machines are selected before starting installation — patching the wrong systems in a production environment can cause outages
- Review Terraform and PowerShell documentation in the repository before making any changes to the lab setup

---

## Tips for Efficiency

- Use Azure Update Manager reporting instead of checking machines individually — it scales to any number of VMs
- Set the maintenance window to match the expected patch duration — 60 minutes was used in this lab
- Refresh the job history view periodically rather than continuously to reduce manual effort
- Keep Terraform and script files in a version-controlled repository so the lab environment can be reproduced consistently
- Run `terraform destroy` after the lab to avoid unnecessary Azure resource costs:

```cmd
terraform destroy
```

---

## Validation Checklist

| Step | Action | Expected Result | Pass / Fail |
|---|---|---|---|
| Assessment | Run update check on all target VMs | Missing critical updates identified | |
| Portal verification | Confirm missing updates in Azure Portal | Portal matches assessment output | |
| Installation | Launch update job for all machines | Job status: In Progress | |
| Completion | Monitor until job finishes | Job status: Succeeded | |
| Post-patch | Review machines in Update Manager | 0 pending updates on all machines | |

---

## Command Reference

| Tool | Command | Purpose |
|---|---|---|
| Azure CLI | `az login` | Authenticate to Azure |
| Terraform | `terraform init` | Initialize Terraform working directory |
| Terraform | `terraform plan` | Preview resources to be created |
| Terraform | `terraform apply` | Provision Azure lab infrastructure |
| Terraform | `terraform destroy` | Tear down all lab resources after completion |

---

*Sean Keener · Azure Infrastructure Lab Series · [github.com/seankeener](https://github.com/seankeener)*
