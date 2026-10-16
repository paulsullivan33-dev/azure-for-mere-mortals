# Your First Linux VM

**Scope:** a real Ubuntu VM, end to end, two ways.
Portal steps are (documented); CLI syntax was validated
against the real `az` help. Nothing here was run against
a live subscription.

## Before you start

You need: a subscription (see [Accounts, Subscriptions,
Tenants](accounts-subscriptions-tenants.md)), the CLI
installed and `az login` done, and a resource group:

```text
$ az group create --name rg-firstvm --location eastus
```

## The CLI way

One command creates the VM, its disk, its network card,
its public IP, and the network security group rule for
SSH:

```text
$ az vm create \
    --resource-group rg-firstvm \
    --name vm-demo \
    --image Ubuntu2204 \
    --size Standard_B1s \
    --generate-ssh-keys \
    --admin-username azureuser
```

What each flag does:

- `--image Ubuntu2204` — the OS. `Ubuntu2204` is a
  shortcut for the latest Ubuntu 22.04 LTS image.
  `Debian11`, `RHEL8`, and Windows images work the same
  way; `az vm image list --output table` shows what's
  available.
- `--size Standard_B1s` — the hardware. **B-series**
  machines are burstable and the cheapest way to run a
  small Linux box (1 vCPU, 1 GB RAM on the B1s). D-series
  is the general-purpose step up.
- `--generate-ssh-keys` — creates `~/.ssh/id_rsa` if you
  don't have one and installs the public half on the VM.
  No password to forget, no password to brute-force.
- `--admin-username azureuser` — the login name. (Not
  `root`, not `admin` — both are blocked.)

The command takes a few minutes and prints JSON,
including `publicIpAddress`. Then:

```text
$ ssh azureuser@<publicIpAddress>
```

**Check:** you get an Ubuntu prompt. `nproc` says 1,
`free -h` shows ~1 GB. That's your $7-or-so-a-month box
(B1s pricing varies by region; see [Pricing and Cost
Control](pricing-and-cost-control.md)).

## The portal way (documented)

[portal.azure.com](https://portal.azure.com) → search
"Virtual machines" → **Create**. The wizard walks
through: subscription, resource group, name, region,
image, size — the same choices as the flags above. On
the Networking tab, leave the defaults (it makes the
NSG SSH rule for you). **Review + create**, wait for
deployment, then find the public IP on the VM's
Overview page and SSH in.

The portal is slower but shows you every option the CLI
picks by default — worth doing once, so the flags mean
something.

## Stopping the meter

This is the part beginners get wrong. **"Stopped" is not
"off":**

| Command | VM state | Billed for compute? |
|---------|----------|---------------------|
| `az vm stop` | Stopped (still allocated) | **Yes** |
| `az vm deallocate` | Stopped (deallocated) | **No** |

```text
$ az vm deallocate --resource-group rg-firstvm --name vm-demo
```

Deallocate releases the hardware; the disk still exists
(and disks bill a little — see
[Storage](storage.md)), but the per-minute compute
charge stops. Restart with `az vm start`. Note the
public IP may change after deallocation unless you made
it static.

Done with the experiment? Nuke the group:

```text
$ az group delete --name rg-firstvm --yes --no-wait
```

**Check:** `az group show --name rg-firstvm` errors
with "not found." Nothing left, nothing billing.

## What the create command made for you

`az vm create` quietly built a small neighborhood:
the VM, an OS disk, a virtual network (`vnet`), a
subnet, a network interface, a public IP, and a network
security group with an SSH rule. Run `az resource list
--resource-group rg-firstvm --output table` to see them
all. [Networking Basics](networking-basics.md) explains
what each piece does — for now, know that deleting the
group removes the whole neighborhood at once.
