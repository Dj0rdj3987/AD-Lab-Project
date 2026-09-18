# Part 6 — Installing Windows Server and Active Directory Domain Services

With monitoring working end to end, the lab needed the thing it exists to study: a
real Active Directory domain. That starts with a Windows Server VM, promoted to a
**Domain Controller** — the machine that runs the domain, holds its accounts, and
answers the authentication requests the rest of the lab will generate.

## Adding the Active Directory role

Active Directory isn't installed by default; it's a role you add. In **Server
Manager** I went to **Manage → Add Roles and Features**.

![Server Manager → Manage → Add Roles and Features](images/44-add-roles-and-features.png)

The wizard walks through a few screens. At **Server Selection** I confirmed it was
targeting this server:

![Add Roles and Features — Server Selection](images/45-server-selection.png)

At the roles screen I selected **Active Directory Domain Services**. Selecting it
prompts to **Add Features** — the supporting components AD DS needs — which I accepted.

![Selecting Active Directory Domain Services and adding its features](images/46-select-adds-role.png)

From there it's **Next** through the remaining screens to **Install**. When it
finished, the wizard reported **Installation succeeded** under the feature
installation status:

![Feature installation succeeded](images/47-installation-succeeded.png)

## Promoting the server to a Domain Controller

Installing the role puts the bits in place but doesn't create a domain yet. Server
Manager flags this with a yellow warning triangle; clicking it offers **Promote this
server to a domain controller**.

![Promote this server to a domain controller](images/48-promote-to-dc.png)

Because this is a brand-new environment with no existing domain to join, I chose **Add
a new forest** and gave it a root domain name. I used `adlab.local`, then set the
Directory Services Restore Mode password and clicked **Next** through the rest.

![Add a new forest — root domain name adlab.local](images/49-add-new-forest.png)

## Restarting

Promotion finishes with a reboot — Windows can't become a domain controller while
it's running as a standalone server. After the restart, the machine is a DC serving
`adlab.local`.

![The server rebooting to complete the promotion](images/50-dc-restart.png)

## Result

There's now a live Active Directory forest, `adlab.local`, running on its own domain
controller. It's empty — an organisation with no people — so the next step is to give
it the structure and accounts that make it look like a real one.

**Next:** [Part 7 — Creating Organizational Units and Users](07-ad-users-and-ous.md)
