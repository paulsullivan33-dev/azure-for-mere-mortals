# Pricing and Cost Control

**Scope:** how Azure charges, the free tier, and the
habits that keep the bill boring. Read this chapter
before creating anything that isn't in a free tier.

## How the meter runs

Nearly everything in Azure is **pay as you go**: a per-minute
(or per-hour, per-GB, per-million-requests) price, added
up continuously. A VM bills for compute while it's
*allocated* — running **or** merely stopped-but-allocated
(see [Your First Linux VM](your-first-linux-vm.md)).
Disks bill while they *exist*. A public static IP bills
while it's *assigned*. Storage bills per GB-month plus
per-operation fees. Data *leaving* Azure (egress) bills
per GB; data entering is free.

Two consequences:

1. **Creating is cheap; leaving running is the cost.**
   The bill comes from duration, not from the act of
   creating.
2. **Deletion is the only off switch that always works.**
   Deallocate the VM *and* delete the resource group when
   the experiment is over.

## The free account

New accounts get two things (terms as of October 2026 —
re-check before relying on them):

- **$200 credit** for the first 30 days, usable on most
  services. When it's gone or expired, services pause
  rather than charging you — the free account has a
  **spending limit** that disables resources instead of
  billing overages.
- **12 months of limited free services** (small VMs,
  storage, databases) plus a set of **always-free**
  services (small quotas that never expire).

The spending limit is the beginner's best friend: it
means the worst case is "my stuff turned off," not "I
owe $4,000." You can remove the limit to go pay-as-you-go
— do that deliberately, not accidentally.

## The calculator

Before creating anything significant, price it:
[azure.microsoft.com/pricing/calculator](https://azure.microsoft.com/pricing/calculator/).
Pick the service, region, and size; it shows the monthly
number. It's an estimate, not a quote — real usage
varies — but it kills the most common surprise ("I
didn't know a GPU VM was $3/hour").

Ballpark figures to calibrate your intuition (East US,
pay-as-you-go, October 2026 — always re-check):

| Thing | Rough monthly cost |
|-------|--------------------|
| B1s VM (1 vCPU, 1 GB) | ~$8 |
| D2s VM (2 vCPU, 8 GB) | ~$70 |
| 128 GB Standard SSD disk | ~$5 |
| 1 TB blob storage (Hot) | ~$18 |
| 1 TB blob storage (Archive) | ~$1 |

## Budgets and alerts: the seatbelt

Set a **budget** on every subscription you care about.
A budget doesn't *stop* spending — it *tells you* when
spending crosses a line. Set the alert at 50% ("heads
up") and 100% ("look now"):

```text
$ az consumption budget create \
    --resource-group rg-demo \
    --budget-name monthly-50 \
    --amount 50 \
    --time-grain Monthly \
    --start-date 2026-11-01 \
    --end-date 2027-11-01
```

**Check:** `az consumption budget list --output table`
shows your budget. Then in the portal
(Cost Management → Budgets), attach an email action
group to the alerts — a budget nobody hears about is
decoration.

Also glance at **Cost Management** in the portal weekly
while learning. It breaks the bill down by resource, so
"what is that $23?" has an answer in two clicks. The
CLI equivalent:

```text
$ az consumption usage list --output table
```

## The "shut it down" checklist

Run this whenever an experiment ends:

1. `az vm deallocate` every VM (stops compute billing).
2. Delete the resource group (`az group delete --yes`).
3. Check Cost Management a day later — charges trail by
   up to 24 hours.
4. Look for orphans: unattached disks, static IPs, and
   snapshots bill quietly. `az disk list` and `az
   network public-ip list` with a critical eye.

And the standing habits:

- **One resource group per experiment** — the nuke
  button only works if experiments are separated.
- **Smallest size that works** — B-series for learning,
  scale up when measurements say so.
- **Deallocate, don't just stop** — `az vm stop` keeps
  billing.
- **Archive old blobs** — Hot storage for last year's
  backups is money on fire.
- **Never leave a GPU VM running unattended.** They
  bill dollars per hour, not cents per month.

## The bill shock hall of fame

Real patterns, so you recognize them early:

- **The forgotten VM.** Created for a tutorial, never
  deleted. $70/month for a D-series doing nothing.
- **The egress surprise.** Serving large downloads or
  backups *out* of Azure to the internet. Ingress is
  free; egress is not.
- **The geo-redundant default.** Some services default
  to GRS replication — twice the storage price for
  protection you didn't ask for. Check the SKU.
- **The public database.** Not a bill problem — a
  security one — but it belongs here because the fix
  (VNet integration) is cheapest on day one.
