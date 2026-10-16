# Glossary

**ARM template** — Azure Resource Manager template:
the JSON format for declaring infrastructure as code.
Bicep compiles to ARM.

**Bicep** — Azure's infrastructure-as-code language; a
readable layer over ARM templates.

**Blob** — a file in Azure Blob Storage. Containers
hold blobs.

**Container** (storage) — a folder-like grouping of
blobs in a storage account. (Not a Docker container.)

**Egress** — data leaving Azure for the internet.
Billed; ingress (incoming) is free.

**Entra ID** — Microsoft Entra ID, formerly Azure
Active Directory: the identity directory behind Azure.
Your tenant *is* an Entra ID directory.

**Managed identity** — an identity Azure manages for a
resource (e.g. a VM), so it can access other resources
with no stored credentials.

**NSG** — network security group: the firewall. Ordered
allow/deny rules on ports and addresses.

**Region** — a named set of Azure data centers
(`eastus`, `westeurope`). Pick one and stay in it.

**Resource** — anything Azure manages: a VM, a disk, a
database, a network.

**Resource group** — the folder resources live in.
Deleting the group deletes everything in it.

**Service principal** — an identity for software (a
script, an app) that needs Azure access.

**SKU** — stock-keeping unit: the size/tier of a
resource. `Standard_B1s`, `StandardSSD_LRS`,
`Standard` public IPs are all SKUs.

**Subnet** — a slice of a virtual network
(`10.0.1.0/24`). VMs in a subnet talk freely.

**Subscription** — the billing boundary. Usage in one
subscription lands on one bill.

**Tenant** — your Entra ID directory; the "who are
you?" layer. One person can belong to several.

**VNet** — virtual network: your private address space
in the cloud (e.g. `10.0.0.0/16`).
