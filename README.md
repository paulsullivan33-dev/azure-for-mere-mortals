# Azure for Mere Mortals

**Azure** is Microsoft's cloud: rented computers, disks,
databases, and networks, in data centers around the
world, billed by the minute. This guide explains it from
zero — the account hierarchy that confuses everyone, your
first virtual machine, where your files go, how the
networking fits together, and above all how the money
works, so nothing surprises you on the bill.

This guide assumes you can use a terminal and know what a
virtual machine is. Everything Azure adds on top —
subscriptions, resource groups, VNets, Bicep — is
explained from zero.

## What this guide covers

- [**What Azure is**](what-azure-is.md) — the cloud in one
  paragraph, regions, and the portal/CLI/API trinity
- [**Accounts, subscriptions, tenants**](accounts-subscriptions-tenants.md) —
  the hierarchy that confuses everyone
- [**Resource groups and naming**](resource-groups-and-naming.md) —
  the folder everything lives in, and names that won't
  bite you later
- [**Your first Linux VM**](your-first-linux-vm.md) — end
  to end, portal and CLI
- [**Storage**](storage.md) — blobs, disks, and files in
  plain terms
- [**Networking basics**](networking-basics.md) — VNets,
  subnets, NSGs, and public IPs
- [**Managed databases**](managed-databases.md) — when to
  let Azure run your database
- [**Pricing and cost control**](pricing-and-cost-control.md) —
  the calculator, budgets and alerts, and the "shut it
  down" checklist
- [**Identity basics**](identity-basics.md) — Entra ID in
  one page
- [**Deploying with Bicep**](deploying-with-bicep.md) — one
  small template, explained line by line
- [**Troubleshooting**](troubleshooting.md) — symptom →
  fix, with the real error messages
- [**Glossary**](glossary.md) — the terms, collected in one
  place

## What it doesn't cover

The other 200+ Azure services (AI, Kubernetes, IoT, and
friends each deserve their own guide), ARM templates in
depth, hybrid/on-premises setups, and enterprise
governance (policies, management groups, landing zones).
This guide is the 20% of Azure that does 80% of the work
for someone getting started.

## How to use this guide

Chapters marked **(documented)** describe steps that need a
live Azure subscription — creating a real VM, clicking
through the portal — which were written from Microsoft's
documentation and the Azure CLI's own help text, not from
a live run. Everything else was checked on a real machine.
**Check:** blocks show what success looks like.

## Validation notes

- Azure CLI syntax in this guide was validated against the
  real `az` command's built-in help (`az <group> --help`)
  — every flag shown exists and required arguments are
  marked. No `az` command that creates, changes, or bills
  anything was executed: there is no Azure subscription
  behind this guide.
- The Bicep template in [Deploying with
  Bicep](deploying-with-bicep.md) was compiled with the
  real `bicep build` — it is syntactically valid.
- All JSON and YAML snippets were syntax-checked.
- Portal walkthroughs and anything that spends money are
  marked **(documented)** and were cross-checked against
  Microsoft Learn, not run live.
- Service details (free-tier terms, VM series, region
  names) were verified in October 2026; cloud details
  drift — re-check anything that affects money before you
  act on it.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Terraform for Mere Mortals](https://paulsullivan33-dev.github.io/terraform-for-mere-mortals/)
- [Kubernetes for Mere Mortals](https://paulsullivan33-dev.github.io/kubernetes-for-mere-mortals/)
- [GitHub Actions for Mere Mortals](https://paulsullivan33-dev.github.io/github-actions-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/azure-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/azure-for-mere-mortals/blob/main/LICENSE-CODE).
