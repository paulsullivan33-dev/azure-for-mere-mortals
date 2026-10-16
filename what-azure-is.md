# What Azure Is

**Scope:** the cloud in one paragraph, where Azure's
computers live, and the three ways you talk to it.

## The one-paragraph version

Azure is Microsoft renting you computers. Instead of
buying a server, you ask Azure for a virtual machine, it
exists two minutes later in a Microsoft data center, and
you pay for each minute it's on. When you're done, you
delete it and the billing stops. The same idea covers
disks, databases, and networks: everything is a rented
resource, created by an API call, billed by usage.

If that sounds like AWS or Google Cloud — it is the same
idea, with Microsoft's names on everything. The concepts
in this guide (regions, resource groups, virtual networks)
have direct equivalents in the other clouds.

## Regions: where the computers are

Azure's data centers are grouped into **regions** — named
places like `eastus` (Virginia), `westeurope`
(Netherlands), or `centralus` (Iowa). Three things to know:

1. **Pick one region and stay in it.** Resources in the
   same region talk to each other fast and free; traffic
   *between* regions is slower and billed.
2. **Pick one close to you** (or to your users). Virginia
   from Texas is fine; Singapore from Texas is a
   quarter-second round trip you'll feel on every SSH
   keystroke.
3. **Not every service exists in every region.** New or
   exotic services launch in a handful of regions first.
   VMs, storage, and databases are everywhere.

Region names come in two flavors: the display name
("East US") and the short name (`eastus`). Commands want
the short name.

**Check:** list the regions your subscription can see
(requires login; (documented)):

```text
$ az account list-locations --output table
```

## The three ways to talk to Azure

Everything in Azure — portal clicks included — ends up as
an API call. You have three doors into the same building:

| Door | What it is | Best for |
|------|-----------|----------|
| **Portal** | The website, [portal.azure.com](https://portal.azure.com) | Learning, one-off tasks, seeing what's there |
| **CLI** | The `az` command | Scripts, repeatable tasks, this guide |
| **API / SDKs** | REST calls, or libraries in your language | Programs that manage Azure themselves |

The portal is the friendliest way to *learn*; the CLI is
the friendliest way to *repeat*. This guide leans on the
CLI because commands can be checked and copied, and
because "click the blue button" instructions rot faster
than flags do. Anything the CLI does, the portal can do
too — they create the same resources.

## Installing the CLI

Windows (winget), macOS (Homebrew), and Linux (package
manager or pip) are all supported — see [Microsoft's
install page](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
for the current command. Verify it:

```text
$ az --version
azure-cli                         2.x.x
...
```

**Check:** `az --version` prints a version table. If the
shell says "command not found," the install didn't finish
or your PATH needs a new terminal.

Then sign in (opens a browser; (documented)):

```text
$ az login
```

`az login` prints a JSON list of your subscriptions.
Everything from here on assumes you're signed in.

## What Azure is not

- **Not a VPS with training wheels.** A $5 VPS gives you
  one box; Azure gives you a hundred services with
  separate bills. Power and complexity come together.
- **Not free.** There is a free tier (see [Pricing and
  Cost Control](pricing-and-cost-control.md)), but the
  default posture is "everything meters." Respect that
  and you'll be fine; ignore it and you'll learn about
  it from your credit card.
- **Not magic.** A VM in Azure is a VM. SSH works the
  same, Linux is Linux. The cloud part is *provisioning*
  and *billing*, not the computer itself.
