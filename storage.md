# Storage

**Scope:** the three kinds of storage — blobs, disks,
files — in plain terms, and which to reach for.

## The three kinds

| Kind | Plain terms | Reach for it when |
|------|-------------|-------------------|
| **Blob** | A giant key-value store for files | Backups, images, logs, anything you `curl` or download |
| **Disk** | A virtual hard drive attached to a VM | The VM's own drive, databases on a VM |
| **Files** | An SMB network share in the cloud | Lift-and-shift file servers, shared config |

They all live in **storage accounts** — the container
that holds them and sets the rules (region, replication,
who can access). One account can hold blobs, files,
queues, and tables at once.

```text
$ az storage account create \
    --name stmydata001 \
    --resource-group rg-demo \
    --location eastus \
    --sku Standard_LRS
```

Name rules are the strictest in Azure: 3–24 characters,
**lowercase letters and numbers only**, globally unique
(it's part of a public URL). `--sku Standard_LRS` means
standard speed, locally redundant (three copies in one
data center). The replication options, cheapest first:
**LRS** (one data center), **ZRS** (three zones in one
region), **GRS** (two regions). Geo-redundancy roughly
doubles the price — pick LRS unless you can say why you
need more.

## Blobs: the workhorse

A **container** holds **blobs** (files). Think S3 bucket
if you've met one:

```text
$ az storage container create \
    --name backups \
    --account-name stmydata001

$ az storage blob upload \
    --account-name stmydata001 \
    --container-name backups \
    --name 2026-10-16.tar.gz \
    --file ./2026-10-16.tar.gz
```

Blob **tiers** trade retrieval speed for price: **Hot**
(default, frequent access), **Cool** (monthly access,
cheaper to store, pricier to read), **Cold** (yearly),
**Archive** (offline — hours to retrieve, pennies to
store). Set the tier per blob:

```text
$ az storage blob upload \
    --account-name stmydata001 \
    --container-name backups \
    --name 2026-10-16.tar.gz \
    --file ./2026-10-16.tar.gz \
    --tier Cool
```

The classic beginner mistake: uploading a terabyte of
backups to Hot and paying for instant access you never
use. Old backups belong in Cool or Archive.

## Disks: VM drives

Every VM gets an **OS disk** automatically. **Data
disks** are extra drives you attach:

```text
$ az vm disk attach \
    --resource-group rg-demo \
    --vm-name vm-demo \
    --name vm-demo-data1 \
    --size-gb 128 \
    --sku StandardSSD_LRS
```

Disk types, cheapest first: **Standard HDD** (spinning,
fine for backups), **Standard SSD** (the default, good
for most things), **Premium SSD** (fast, for databases).
A 128 GB Standard SSD is a few dollars a month whether
the VM is on or not — disks bill while they *exist*,
which is why deallocated VMs still cost a little.

## Files: SMB in the cloud

**Azure Files** is a plain SMB share — mountable from
Windows, macOS, or Linux, or from a VM:

```text
$ az storage share create \
    --name shared \
    --account-name stmydata001
```

Use it when you're moving a file server to the cloud
and don't want to rethink anything. For new projects,
blobs are usually cheaper and simpler.

## Access: keys vs Entra ID

Storage accounts have **access keys** — two long
passwords that open everything. They work everywhere
including scripts, and they're the thing that leaks in
screenshots and git repos. Prefer **Entra ID**
authentication (`--auth-mode login` on CLI commands)
for interactive use: it uses *your* login instead of a
shared key, and access can be revoked per person. If a
key must exist (some legacy tool needs it), you can
regenerate it after any scare:

```text
$ az storage account keys renew \
    --account-name stmydata001 \
    --resource-group rg-demo \
    --key key1
```

**Safety:** treat storage keys like passwords. Never
paste them into chat, tickets, or repos.
