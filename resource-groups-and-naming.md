# Resource Groups and Naming

**Scope:** the folder everything lives in, and names that
won't bite you later.

## The one rule

**Every resource lives in exactly one resource group,
and deleting the group deletes everything in it.**

That's the whole chapter, really. A resource group is a
folder with a superpower: `az group delete` is the "nuke
the entire experiment" button. Spun up a VM, a database,
and a network to try something? Delete the group and all
three are gone, billing stops, nothing lingers. This is
your primary defense against the forgotten-resource bill
(see [Pricing and Cost Control](pricing-and-cost-control.md)).

```text
$ az group create --name rg-demo --location eastus
```

```text
$ az group delete --name rg-demo --yes --no-wait
```

**Check:** after creating, `az group show --name
rg-demo --query name` prints `"rg-demo"`. After
deleting, the same command errors — that's success.

`--yes` skips the "are you sure" prompt; `--no-wait`
returns immediately instead of blocking until Azure
finishes deleting. Without `--no-wait`, the command
sits there until every resource is gone.

## One group per thing

Make a group per project or experiment: `rg-blog`,
`rg-minecraft-test`, `rg-client-acme`. Not one giant
group for everything (then the nuke button is too
dangerous to use) and not one group per resource (then
you have fifty groups). Per *thing you're working on* is
the sweet spot.

A resource group has a **location**, but it's mostly
metadata — it decides where the group's *bookkeeping*
lives, not where your resources run. Your VM's region is
set on the VM. Still, put the group in the same region
as its resources; future you will thank present you.

## Naming that won't bite you

Azure names have rules, and the rules differ per
resource — some allow dashes, some don't; some are
globally unique (storage accounts), some only need to be
unique in the group. Rather than memorizing, follow
three habits:

1. **Lowercase, dashes, numbers.** `rg-blog-prod`
   works almost everywhere. Some resources are picky
   (storage account names must be 3–24 lowercase letters
   and numbers, no dashes at all), so lowercase and
   dashes is the safest default.
2. **Prefix with the type.** `rg-` for groups, `vm-`
   for VMs, `st` for storage, `vnet-` for networks.
   When you're staring at a list of forty resources,
   the prefix tells you what each one is.
3. **Include the purpose.** `rg-blog-prod` vs
   `rg-blog-test` — the day you delete the wrong one is
   the day this habit pays for itself.

Keep names short: some resources cap at 24 characters
(storage accounts) or 64 (most others). The
`prefix-purpose-env` pattern usually fits.

## Tags: the label maker

**Tags** are key=value labels on resources: `env=prod`,
`owner=paul`, `project=blog`. They cost nothing and do
two jobs: they show up in cost reports (so you can see
what the blog actually costs), and they let you find
things (`az resource list --tag project=blog`).

```text
$ az group create --name rg-blog --location eastus \
    --tags project=blog env=prod
```

Tag at creation. Retrofitting tags across forty
resources is nobody's idea of fun.
