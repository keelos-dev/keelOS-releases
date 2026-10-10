# keelOS releases

## What is keelOS

keelOS is a virtualization host for the one server at home or in a small office: a NAS, a router, a
Windows desktop and a few Linux servers on one box, each in its own VM. It is built on FreeBSD, bhyve
and ZFS.

What sets it apart: **restart the host, your VMs carry on.** A small hypervisor (keel, about 100 KB)
is loaded by UEFI before the host system and holds guest memory and vCPU state. When the host system
is upgraded and restarted, the VMs pause for about 25 seconds and then carry on where they were: no
shutdown, no dropped TCP connections. On our development server: 100 host restarts in a row without a
failure, 17 VMs resumed together after one restart.

- **Passthrough that survives a host restart**: give a whole disk controller or NVMe drive to a NAS
  VM. GPU passthrough and SR-IOV network functions work too (a VM with a VF is still restarted with the
  host; keeping it running is in testing).
- **Windows 11 without workarounds**: virtual TPM 2.0 and Secure Boot; BitLocker works.
- **ZFS underneath**: VM disks on ZFS, snapshots of the whole guest.
- **Web page, command line and API**: one program, no database; consoles in the browser and over RDP;
  English, Chinese and Japanese.
- **Upgrades with a way back**: two slots on the boot disk; if a new version does not come up, the
  next boot returns to the old one.

## Links

- Website: https://keelos.dev (English, 中文, 日本語)
- Status: in development; test releases are published under [Releases](../../releases), with the
  SHA-256 of each ISO and release notes.
- Get notified: Watch → Custom → Releases, or subscribe to [the feed](../../releases.atom).
- Bugs, questions, hardware reports: [Issues](../../issues).
- Requirements: 64-bit Intel CPU with VT-x and EPT (VT-d for passthrough). AMD is not supported yet.

## Quickstart

About fifteen minutes from download to the first VM. The full guide with special cases:
https://keelos.dev/quickstart/ ([中文](https://keelos.dev/zh/quickstart/), [日本語](https://keelos.dev/ja/quickstart/)).

1. **Download** `keelos-VERSION.iso` from [Releases](../../releases) and check it against the `.sha256`
   next to it. The version is the commit and the build time (UTC): a later time is newer.
2. **Make a boot stick**: write the ISO to a USB stick of 1 GB or more with
   [Rufus](https://rufus.ie/) (choose **DD image** mode), balenaEtcher or `dd`. On a server, mount the ISO
   as the BMC's virtual media instead. Firmware: UEFI boot with CSM/Legacy off, Secure Boot off, VT-x
   and VT-d on.
3. **Install**: boot the stick, press Enter to install (or choose *try only* to run from memory and
   write nothing), pick one disk (512-byte sectors, 4 GiB or more; a disk with data is erased only
   after you type its name) and a network port, then remove the stick and reboot.
4. **Set up in the browser**: open the address shown on the console (e.g. `https://192.168.1.10/`) and
   set the admin password right away (it is root's too; `keel admin reset` on the console if you
   forget it). Then *Storage → Make the pool zones*, *Network → Physical ports → Probe*, upload an
   installer under *Storage → ISO images*, and *Virtual machines → New virtual machine*.

**kRDPclient**, the console client for the VMs (Windows), is under *Downloads* in keelOS's web page.
The zip downloaded there already knows this host: address, certificate and an access token of yours
(revoke it in the token list if needed), so it connects without setup. It can also forward USB devices
to a VM. Any other RDP client still reaches a VM's screen.

**keelagent.exe**, from the same *Downloads* page, is for Windows VMs that have a GPU passed through
(experimental). Inside the VM it captures the screen and encodes it on the VM's own GPU (H.264), and
the host only forwards that stream to kRDPclient: smoother video at less CPU. Tick *Channel for a guest
encoding agent* on the VM's screen settings (takes effect at a cold start), then run keelagent.exe in
the VM and install it from its window: it adds the IVSHMEM driver and a service, and uninstalls the
same way. Without it, the host encodes the screen as before.

Then restart the host once (`shutdown -r now`) and watch the VMs pause and carry on. Upgrades:
*System → Upgrade* with a newer ISO; if the new version does not come up, the next boot returns to the
old one.

**More details** (features, tested hardware, documentation, articles): **https://keelos.dev**

## Licence

keelOS is not open source and is free to use; see https://keelos.dev/about/ for the licence, what keelOS
sends over the network (nothing on its own) and how your VMs stay readable without it.

## Support keelOS

If keelOS is useful to you, you can [sponsor its development](https://github.com/sponsors/keelos-dev)
(the ❤ Sponsor button at the top of this page). Sponsoring is voluntary: keelOS works the same without
it, and it is not a condition of the commercial licence.

如果 keelOS 对你有用，欢迎[赞助开发](https://github.com/sponsors/keelos-dev)。赞助完全自愿，不赞助也照样使用，也不是商业许可的条件。
