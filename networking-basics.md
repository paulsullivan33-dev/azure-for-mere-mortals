# Networking Basics

**Scope:** VNets, subnets, NSGs, and public IPs — the
four pieces that decide what can talk to what.

## The four pieces

| Piece | Plain terms |
|-------|-------------|
| **VNet** (virtual network) | Your private network in the cloud — an address range like `10.0.0.0/16` that only your resources use |
| **Subnet** | A slice of the VNet (`10.0.1.0/24`) — VMs in the same subnet talk freely |
| **NSG** (network security group) | The firewall: ordered allow/deny rules on ports, attached to subnets or network cards |
| **Public IP** | The internet-facing address, if a resource needs one |

`az vm create` builds all four for you with sane
defaults. This chapter is for when the defaults aren't
what you want — or when SSH stops working and you need
to know where to look.

## NSGs: the firewall you'll actually touch

An NSG is a list of rules, evaluated in priority order
(lowest number first). Each rule: direction (inbound /
outbound), port, source, allow or deny. The default VM
NSG allows inbound SSH (port 22) from anywhere and
denies everything else inbound.

Open a port — say, a web server on 80:

```text
$ az vm open-port \
    --resource-group rg-demo \
    --name vm-demo \
    --port 80
```

That's the shortcut. The explicit version, for when you
need control over the source:

```text
$ az network nsg rule create \
    --resource-group rg-demo \
    --nsg-name vm-demoNSG \
    --name allow-http \
    --priority 1001 \
    --direction Inbound \
    --access Allow \
    --protocol Tcp \
    --destination-port-ranges 80 \
    --source-address-prefixes 203.0.113.0/24
```

**Check:** `az network nsg rule list --resource-group
rg-demo --nsg-name vm-demoNSG --output table` shows
your rule. Lower priority number wins — a deny at 900
beats an allow at 1000.

**Safety:** "allow port 22 from *" is the default and
it's how bots find you. Your VM has SSH keys (not
passwords) if you followed [Your First Linux
VM](your-first-linux-vm.md), which is the important
part — but restricting SSH to your IP is strictly
better. Your IP changes; a `*` rule doesn't.

## Public IPs: dynamic vs static

A **dynamic** public IP (the default) can change when
the VM is deallocated and restarted. A **static** IP
stays put — and costs a small amount per month whether
the VM runs or not.

```text
$ az network public-ip create \
    --resource-group rg-demo \
    --name pip-web \
    --allocation-method Static \
    --sku Standard
```

**SKU** here means the feature tier: **Standard** is
zone-redundant and the modern default; **Basic** is
cheaper but being phased out for new features. For a
new IP, pick Standard.

Most VMs don't need a public IP at all — only the ones
you SSH into or serve traffic from. Everything else
stays private inside the VNet, which is both cheaper
and safer.

## DNS, briefly

Azure gives every public IP an optional DNS name
(`mylabel.eastus.cloudapp.azure.com`). For a real
domain, point your registrar's A record at the static
IP, or use **Azure DNS** to host the zone. Either way:
static IP first, DNS second — DNS pointing at an
address that changes is a special kind of pain.

## VNet peering (one paragraph)

Two VNets are isolated from each other by default.
**Peering** connects them so resources talk privately,
as if one network. You'll meet this when you have more
than one VNet — a second project, or a hub-and-spoke
setup. Until then: one VNet per project, subnets to
separate tiers (web / app / data), NSGs on each.
