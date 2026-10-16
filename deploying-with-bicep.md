# Deploying with Bicep

**Scope:** one small Bicep template that builds the VM
from [Your First Linux VM](your-first-linux-vm.md),
explained line by line. The template was compiled with
the real `bicep build` — it is syntactically valid.
Deploying it needs a live subscription (documented).

## Why Bicep

Clicking through the portal doesn't scale, and a shell
script full of `az` commands is fragile — re-running it
creates duplicates or errors. **Bicep** is Azure's
infrastructure-as-code language: you declare what you
want, and Azure figures out the order and skips what
already exists. It's a thin, readable layer over ARM
templates (the older JSON format — if you meet ARM,
Bicep compiles to it).

The template lives at
[`examples/main.bicep`](https://github.com/paulsullivan33-dev/azure-for-mere-mortals/blob/main/examples/main.bicep)
in this repo. It builds the same neighborhood `az vm create`
made: VNet, public IP, NSG with an SSH rule, network
interface, and the VM.

## Line by line

```bicep
targetScope = 'resourceGroup'
```

Everything deploys into one resource group — the
nuke-button unit from [Resource Groups and
Naming](resource-groups-and-naming.md).

```bicep
@description('Azure region for all resources.')
param location string = 'eastus'
```

**Parameters** are the template's inputs, with defaults.
`location`, `namePrefix`, `adminUsername`, and
`vmSize` all have defaults; `sshPublicKey` doesn't —
you must supply it, because a default SSH key would be
everyone's key.

```bicep
var vnetName = '${namePrefix}-vnet'
```

**Variables** build the resource names from the
prefix: `demo-vnet`, `demo-pip`, `demo-nsg`,
`demo-nic`, `demo-vm`. Same naming habit as the CLI
chapter.

```bicep
resource vnet 'Microsoft.Network/virtualNetworks@2023-09-01' = {
  name: vnetName
  location: location
  properties: { ... }
}
```

A **resource** block: the type (`Microsoft.Network/
virtualNetworks`), an API version (`@2023-09-01` —
Bicep needs one; newer versions add features), and the
properties. The five resources are the VNet
(`10.0.0.0/16` with one `10.0.1.0/24` subnet), a
Standard-SKU static public IP, an NSG allowing inbound
TCP 22, a network interface wiring the three together,
and the VM itself.

Notice what the template *doesn't* say: no ordering.
Bicep reads the references — `nic` uses `vnet.properties
.subnets[0].id` and `pip.id`, so the NIC deploys after
both — and builds the dependency graph itself. You
declare the *what*; Azure handles the *when*.

The VM's `osProfile` disables password auth and
installs your SSH key, and `storageProfile` picks the
Ubuntu 22.04 image with a Standard SSD OS disk — the
same choices as the CLI chapter.

```bicep
output publicIp string = pip.properties.ipAddress
output sshCommand string = 'ssh ${adminUsername}@${pip.properties.ipAddress}'
```

**Outputs** print after deployment — here, the IP and
the exact SSH command. Small thing, big convenience.

## Build it, then deploy it

Compile (no subscription needed — this was actually
run):

```text
$ az bicep build --file examples/main.bicep
```

**Check:** no output means success, and a
`main.json` appears beside it — the compiled ARM
template. (Bicep is installed on demand: `az bicep
install` fetches it the first time.)

Deploy (needs a live subscription; (documented)):

```text
$ az deployment group create \
    --resource-group rg-demo \
    --template-file examples/main.bicep \
    --parameters sshPublicKey="$(cat ~/.ssh/id_rsa.pub)"
```

Re-run it with a different `vmSize` and Bicep updates
the VM in place instead of creating a second one —
that's the whole point of declaring rather than
scripting. And when the experiment ends, the group
delete still nukes everything, Bicep-made or not.

## Bicep vs the CLI

| | CLI script | Bicep |
|---|---|---|
| Re-running | Errors or duplicates | Updates in place |
| Ordering | You sequence the commands | Inferred from references |
| Readability | Fine for 5 lines, soup at 50 | Structured at any size |
| Learning curve | You already know it | An afternoon |

Start with the CLI. Reach for Bicep when you're
rebuilding the same thing twice — which, in the cloud,
is about a week in.
