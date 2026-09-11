# Part 2 — Installing Splunk Enterprise

With the network sorted out, the next step was to actually get Splunk onto the Ubuntu VM. Before doing that, though, I wanted an easy way to move the (fairly large) Splunk installer over from my host machine, so I set up a VirtualBox shared folder first.

## Setting up a shared folder between host and VM

VirtualBox shared folders rely on Guest Additions, so the first step was installing the Guest Additions ISO package:

```bash
sudo apt-get install virtualbox-guestadditions-iso
```

![Installing the VirtualBox Guest Additions ISO package](images/09-install-guest-additions.png)

With that in place, I pointed VirtualBox's shared folder settings at a folder on the host containing the Splunk installer:

![VirtualBox shared folder settings, pointing at the Splunk folder on the host](images/10-vbox-shared-folder-settings.png)

By default, a regular user on the VM can't access shared folders — you need to be a member of the `vboxsf` group. So I tried adding my `splunk` user to that group:

```bash
sudo adduser splunk vboxsf
```

![Adding the splunk user to the vboxsf group](images/11-adduser-vboxsf.png)

That failed, though — the `vboxsf` group didn't exist yet, because the guest-side VirtualBox utilities weren't installed. The fix was to install `virtualbox-guest-utils`, which creates that group:

```bash
sudo apt-get install virtualbox-guest-utils
```

![The "group vboxsf does not exist" error, and the fix](images/12-vboxsf-group-error-and-fix.png)

With the guest utilities in place, I ran the `adduser` command again — this time it succeeded — and created a `share` directory to mount the folder into:

![Adding the splunk user to vboxsf successfully, and creating a share directory](images/13-adduser-vboxsf-retry.png)

## Mounting the shared folder

Next was mounting the shared folder itself:

```bash
sudo mount -t vboxsf -o uid=1000,gid=1000 Splunk
```

![Mounting the vboxsf shared folder](images/14-mount-vboxsf-attempt.png)

I also went back and double-checked the VirtualBox shared folder settings, this time with **Auto Mount** enabled, so the folder would be available automatically going forward:

![Shared folder settings with auto mount enabled](images/15-vbox-shared-folder-automount.png)

With auto mount in place, the shared folder showed up on its own at `/media/sf_Splunk`, with the Splunk installer already sitting inside it:

```bash
sudo ls /media/sf_Splunk
```

![The auto-mounted shared folder, containing the Splunk .deb installer](images/16-automounted-folder-contents.png)

I mounted it a second time, this time directly into the `share` directory I'd created under my home folder, so I had a convenient local path to work from:

```bash
sudo mount -t vboxsf -o uid=1000,gid=1000 Splunk share/
```

![Mounting the shared folder into the share directory](images/17-mount-vboxsf-to-share.png)

A quick `ls -la` confirmed the installer — a 1.3 GB `.deb` package — was right there in `~/share`:

![The Splunk installer inside the share directory](images/18-share-folder-contents.png)

## Installing Splunk

With the installer accessible, I ran the actual installation:

```bash
sudo dpkg -i splunk-10.4.3-4174a2deda5d-linux-amd64.deb
```

![Installing Splunk with dpkg](images/19-dpkg-install-splunk.png)

Splunk installs itself into `/opt/splunk`:

![Contents of /opt/splunk after installation](images/20-opt-splunk-contents.png)

Splunk shouldn't run as root, so I switched into the dedicated `splunk` service account before doing anything further:

```bash
sudo -u splunk bash
```

![Switching to the splunk service user](images/21-switch-to-splunk-user.png)

From there, I moved into Splunk's `bin` directory and started it for the first time:

```bash
cd bin
./splunk start
```

![Moving into the bin directory](images/22-cd-bin.png)

![Starting Splunk for the first time](images/23-splunk-start.png)

## Enabling Splunk to start on boot

The last thing I wanted to confirm was that Splunk would actually come back up on its own every time I started the VM, rather than needing to be started manually after every reboot:

```bash
sudo ./splunk enable boot-start -user splunk
```

![Enabling Splunk to start on boot (1 of 2)](images/24-enable-boot-start-1.png)

![Enabling Splunk to start on boot (2 of 2)](images/25-enable-boot-start-2.png)

At this point the Splunk server was fully installed, running under its own dedicated user, and set to come up automatically at `192.168.10.10`. Next, I turned to the Windows side of the lab and started preparing the target machine.

**Next:** [Part 3 — Preparing the Target Machine](03-target-machine-setup.md)
