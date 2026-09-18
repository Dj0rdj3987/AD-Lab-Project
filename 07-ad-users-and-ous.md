# Part 7 — Creating Organizational Units and Users

A fresh domain controller runs an empty domain. To make `adlab.local` resemble a real
organisation and to give the later brute-force attack actual accounts to aim at — it
needs structure: organizational units standing in for departments, and users inside
them.

## Opening Active Directory Users and Computers

After the promotion and reboot, I logged back into the server and opened the tool that
manages all of this: **Server Manager → Tools → Active Directory Users and
Computers** (ADUC).

![Server Manager → Tools → Active Directory Users and Computers](images/51-active-directory-users-and-computers.png)

## Creating an Organizational Unit

Inside ADUC, `adlab.local` is at the top of the tree. To mimic a company's
departments, I right-clicked the domain → **New → Organizational Unit** and created
one called **IT**.

![Creating a new Organizational Unit named IT](images/52-new-ou-it.png)

Organizational units are how real domains stay manageable — they group accounts so
policy and permissions can be applied per department rather than one user at a time.

## Adding a user

With the IT OU in place, I right-clicked it → **New → User** and filled in the name,
surname and logon details. That first account is the first user in the IT OU.

![Creating a new domain user in the IT OU](images/53-new-user.png)

![The first user shown inside the IT organizational unit](images/54-user-in-it-ou.png)

## A second department

To make the domain a little more realistic than a single OU, I repeated the process:
a second organizational unit called **HR**, with another user inside it.

![Creating a second OU, HR, with its own user](images/55-new-ou-hr.png)

## Result

`adlab.local` now has two departments — IT and HR — each with a user account. The
domain has real principals to authenticate, which is exactly what's needed for the
final piece: bringing the Windows 10 target into the domain so it authenticates
against this DC.

**Next:** [Part 8 — Joining the Target Machine to the Domain](08-domain-join.md)
