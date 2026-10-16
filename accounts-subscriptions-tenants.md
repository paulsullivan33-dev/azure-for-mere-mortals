# Accounts, Subscriptions, Tenants

**Scope:** the three-layer identity of Azure billing and
access — the part that confuses everyone exactly once.

## The layer cake

Azure answers three questions about everything you do,
and each question has its own layer:

| Question | Layer | Example |
|----------|-------|---------|
| *Who are you?* | **Tenant** (your Entra ID directory) | `paul-sullivan-family` |
| *Who pays?* | **Subscription** | `Pay-As-You-Go` |
| *What's it for?* | **Resource group** | `rg-webshop-prod` |

**Tenant** is your organization's identity directory —
the list of users and what they're allowed to do. One
person can belong to several tenants (work, personal, a
client's), and you switch between them. Think of it as
*the company*.

**Subscription** is the billing boundary. All usage in a
subscription lands on one bill, and spending limits and
budgets attach here. One tenant can hold many
subscriptions (prod, dev, that experiment you're about
to forget). Think of it as *the credit card*.

**Resource group** is the folder resources live in (next
chapter). Think of it as *the project folder*.

The classic confusion: "I created a VM, why can't my
coworker see it?" — because it's in *your* subscription
and they only have access to theirs. Or: "which account
am I in?" — you mean subscription, and `az account show`
tells you.

## Seeing where you are

```text
$ az account list --output table
Name             CloudName    SubscriptionId                        State    IsDefault
---------------  -----------  ------------------------------------  -------  -----------
Pay-As-You-Go    AzureCloud   00000000-0000-0000-0000-000000000000  Enabled  True
```

```text
$ az account show --query "{name:name, tenant:tenantId}" --output table
```

**Check:** `IsDefault: True` marks the subscription
commands will use. Switch it with (documented):

```text
$ az account set --subscription "Pay-As-You-Go"
```

Name or ID both work. After switching, re-run `az
account show` and confirm the name changed — every
resource you create from now on bills to *that*
subscription.

## Management groups (one paragraph)

Above subscriptions sit **management groups**: folders
for subscriptions, so a company can apply one rule ("all
VMs must be in these regions") to fifty subscriptions at
once. You will not touch these until you have several
subscriptions. Know they exist; move on.

## The beginner's setup

One tenant (the default one Azure made for you), one
pay-as-you-go subscription, and resource groups per
project. That's the whole thing. Add subscriptions when
you have a reason — separating work from personal, or
walling off an experiment so its bill can't surprise
you — not before.
