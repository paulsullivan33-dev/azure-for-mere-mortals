# Identity Basics

**Scope:** Entra ID in one page — users, service
principals, and the two ways things prove who they are.

## Entra ID, formerly Azure AD

**Microsoft Entra ID** is the identity directory behind
Azure (renamed from Azure Active Directory in 2023 —
old tutorials say "Azure AD," they mean this). It's the
answer to "who are you?" for everything: your login,
your VM's managed identity, the app that deploys your
code. Your tenant *is* an Entra ID directory (see
[Accounts, Subscriptions, Tenants](accounts-subscriptions-tenants.md)).

You get a directory automatically with your Azure
account. The free tier covers single sign-on for a
handful of apps and the basics below — most beginners
never need the paid tiers.

## The three identities

| Identity | Plain terms |
|----------|-------------|
| **User** | A person. You. Has a password and ideally MFA. |
| **Service principal** | An identity for *software*. A deployment script or app that needs Azure access gets one instead of your password. |
| **Managed identity** | A service principal Azure manages for you, attached to a resource. A VM with a managed identity can read a storage account with no stored credentials at all. |

The rule: **people get users, software gets service
principals, and Azure resources get managed identities.**
The moment a password or key sits in a script or repo,
you've built the thing managed identities exist to
replace.

## RBAC: who can do what

**Role-based access control** answers "this identity
may do *what*, *where*?" A **role** (Owner, Contributor,
Reader, and hundreds of fine-grained ones) is assigned
to an identity at a **scope** (management group,
subscription, resource group, or single resource).
Permissions inherit downward: Contributor on the
resource group covers everything inside it.

The beginner-safe pattern: **Contributor on the
resource group** for people working on that project,
**Reader** for people who only need to look. Owner adds
the right to grant access to others — hand it out
sparingly.

```text
$ az role assignment create \
    --assignee user@example.com \
    --role Contributor \
    --scope /subscriptions/<subscription-id>/resourceGroups/rg-demo
```

The scope is the full resource ID of the resource
group — Contributor here covers everything inside it.
(Find your subscription ID with `az account show
--query id --output tsv`.)

**Check:** `az role assignment list --resource-group
rg-demo --output table` shows who has what.

## MFA: not optional

Turn on **multi-factor authentication** for every human
account, starting with yours. It's in Entra ID →
Users → per-user MFA (or Conditional Access on paid
tiers). An Azure subscription is a direct line to your
credit card; a password alone is not enough rope.

## The two credential smells

1. **A key in a repo or chat.** Storage keys, connection
   strings, client secrets — if it's in git, rotate it
   and treat it as compromised. It is.
2. **One identity doing everything.** The deployment
   script using your personal login, the app using the
   storage account's master key. Split them: managed
   identity for the app, service principal with a narrow
   role for the script.

That's the whole page. Identity is the least exciting
chapter and the one that saves you from the worst
mornings.
