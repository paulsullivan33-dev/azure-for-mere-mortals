# Managed Databases

**Scope:** when to let Azure run your database instead
of installing one on a VM — and which one to pick.

## The real question

You can always install PostgreSQL on a VM. A
**managed** database means Azure installs it, patches
it, backs it up, and fails it over — you get a
connection string. The trade: less control, more money
than a VM doing the same job, far less 3 AM pager duty.

Pick managed when the database matters more than the
server it runs on. Pick a VM when you need a weird
version, a plugin Azure doesn't offer, or the cheapest
possible bill for a toy.

## The lineup

| Service | Plain terms | Pick it when |
|---------|-------------|--------------|
| **Azure SQL Database** | Managed SQL Server | You're a SQL Server shop, or want the best tooling |
| **Cosmos DB** | Globally distributed NoSQL | You need five-nines uptime across continents |
| **PostgreSQL Flexible Server** | Managed Postgres | You want Postgres without the babysitting |
| **MySQL Flexible Server** | Managed MySQL | Same, for MySQL |

"Flexible Server" is the current generation for
Postgres/MySQL — the older "Single Server" is retired.
If a tutorial mentions Single Server, it's out of date.

Creating one (Postgres example; (documented)):

```text
$ az postgres flexible-server create \
    --resource-group rg-demo \
    --name pg-demo-001 \
    --location eastus \
    --admin-user pgadmin \
    --admin-password '<strong-unique-password>' \
    --sku-name Standard_B1ms \
    --tier Burstable \
    --storage-size 32 \
    --version 16
```

`--sku-name Standard_B1ms --tier Burstable` is the
smallest/cheapest shape — fine for learning. The server
name must be globally unique (it's in a public DNS
name), lowercase, 3–63 characters.

**Safety:** that `--admin-password` lands in your shell
history. For anything real, omit it and let the CLI
prompt, or better, use Entra ID authentication and skip
passwords entirely.

## The two gotchas

**Networking.** A new flexible server is reachable from
the internet by default with firewall rules you define —
or you can put it inside your VNet so only your VMs
talk to it. "Database accidentally public" is a classic;
check the firewall rules after creation and default to
VNet-private for anything with real data.

**Backups and point-in-time restore.** Managed means
automated backups (usually 7 days retained, more on
higher tiers). Know two things: how long retention is,
and that you've *tested* a restore. An untested backup
is a rumor.

## When a VM database wins

- Total bill under ~$15/month matters more than
  convenience (a B1s VM running Postgres is cheaper
  than the managed Burstable tier).
- You need an extension or version Azure doesn't offer.
- It's a dev/test database you spin up and down with
  the VM.

For a homelab-style project, Postgres on a B-series VM
is the honest budget answer. For anything you'd be sad
to lose, managed earns its price in backups alone.
