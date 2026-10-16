# Troubleshooting

**Scope:** the classic failures, symptom → fix. All
entries are (documented) — written from Microsoft's
documentation and the Azure CLI's help text, not from a
live subscription. Each one is the kind of thing that
costs a beginner an afternoon; now it costs you five
minutes.

## "Operation could not be completed as it results in exceeding approved Total Regional Cores quota."

**Symptom:** `az vm create` fails immediately with a
quota error. New subscriptions get a small vCPU quota
per region (often 10) — you've used it up, usually with
a handful of VMs.

**Fix:** request more in the portal: Subscriptions →
your subscription → Usage + quotas → request an
increase. It's free and usually approved within the
day. Or pick a smaller VM size, or a different region
with spare quota. (Documented.)

## SSH hangs, then "Connection timed out"

**Symptom:** `ssh azureuser@<ip>` sits there and dies.
The VM is running — Azure says so — but nothing
answers on port 22.

**Fix, in order:**

1. **NSG.** The network security group isn't allowing
   your IP on 22. Check: `az network nsg rule list
   --nsg-name <name> --resource-group <rg> --output
   table`. If the allow-SSH rule's source is a stale
   IP (yours changed), update it or widen it
   temporarily. See [Networking
   Basics](networking-basics.md). (Documented.)
2. **The VM's own firewall.** Ubuntu's `ufw` or
   firewalld can block 22 independently of the NSG.
   If you enabled it and locked yourself out, use the
   portal's serial console or redeploy.
3. **Wrong IP.** After a deallocate/start cycle, a
   *dynamic* public IP changes. `az vm show -d` prints
   the current one — make sure you're SSHing to it.

Nine times out of ten, it's the NSG.

## "The requested size is not available in this region"

**Symptom:** VM creation fails because that size (often
a cheap B-series or a GPU size) isn't offered in your
region, or the zone you picked.

**Fix:** `az vm list-skus --location eastus --size
Standard_B --output table` shows what's actually
available there. Either switch sizes or switch
regions. GPU sizes are the worst offenders — they're
scarce everywhere. (Documented.)

## The bill is bigger than expected

**Symptom:** Cost Management shows charges you don't
recognize.

**Fix:** drill down by resource (Cost Management →
Cost analysis → group by Resource). The usual
suspects, in order: a VM that was *stopped* but not
*deallocated*; an unattached disk or static IP left
behind after a VM was deleted; a database tier bigger
than you thought; egress from serving traffic out of
Azure. Delete the resource group for dead experiments
— see the shutdown checklist in [Pricing and Cost
Control](pricing-and-cost-control.md).

## `az login` opens a browser that never finishes

**Symptom:** the CLI prints a code and opens a browser
tab, but nothing completes — common on headless
machines and SSH sessions.

**Fix:** `az login --use-device-code`. The CLI prints a
code and a URL; you open the URL on *another* device
(your phone), enter the code, and the terminal session
authenticates. Made for exactly this situation.

## "Storage account name is already taken" / invalid name

**Symptom:** `az storage account create` rejects the
name.

**Fix:** the name must be globally unique (it's in a
public URL), 3–24 chars, lowercase letters and numbers
only. Append digits until it's unique
(`stmydata001`, `stmydata002`, …) — boring and
effective.

## Deployment (Bicep/ARM) fails with a cryptic template error

**Symptom:** `az deployment group create` fails and the
error mentions a line deep in the template.

**Fix:** first, `az bicep build --file main.bicep` —
syntax errors are free to find locally. For deployment
errors, `az deployment group show` / the portal's
deployment blade shows the failed operation with the
real message, which is usually clearer than the
summary. The most common real cause: a name that
violates some resource's naming rules.
