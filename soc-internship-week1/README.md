# SOC Internship: Week 1 Projects

My Week 1 work for the Networkwalks internship. It has three parts:

1. **WK1-PM1:** Cybersecurity lab with VirtualBox and Kali Linux
2. **WK1-PMx:** Android-x86 VM on the same network
3. **WK1-PMx:** Wireshark SOC labs (6 labs)

## Lab Summary

| Item | Value |
|---|---|
| Hypervisor | VirtualBox 7.2.14 |
| Attacker VM | Kali Linux 2026.2 (`10.0.0.2/24`) |
| Target VM | Android-x86 9.0 (`10.0.0.9/24`) |
| Network | NAT Network `NatNetwork` (`10.0.0.0/24`) |
| Gateway / DNS | `10.0.0.1` / `8.8.8.8` |

---

## Part 1: Lab Setup (VirtualBox + Kali Linux)

**Goal:** Build a testing lab with Kali on an isolated NAT Network (`10.0.0.0/24`), with clipboard, drag-and-drop, a shared Downloads folder, a static IP and Internet access.

### 1. Install VirtualBox
![VirtualBox version](screenshots/01-virtualbox-version.png)

### 2. Create the NAT Network (`10.0.0.0/24`, DHCP enabled)
![NAT Network](screenshots/02-nat-network.png)

### 3. Import Kali Linux
![Kali imported](screenshots/03-kali-imported.png)

### 4. Attach Kali to the NAT Network
![VM network settings](screenshots/04-vm-network-settings.png)

### 5. Enable shared clipboard and drag-and-drop
![Clipboard and drag-and-drop](screenshots/05-clipboard-dragdrop.png)

### 6. Share the host Downloads folder
![Shared folder settings](screenshots/06-shared-folder-settings.png)

![Shared folder in Kali](screenshots/07-shared-folder-in-kali.png)

### 7. Set the static IP `10.0.0.2/24`
![IPv4 settings](screenshots/08-kali-ipv4-settings.png)

![ip a output](screenshots/09-ip-a.png)

### 8. Test connectivity
Pinged the gateway, the Internet by IP, and a website by name. All had 0% packet loss.

![Ping tests](screenshots/10-ping-tests.png)

### 9. Take a snapshot
![Snapshot](screenshots/11-snapshot.png)

### Issue encountered
Kali would not connect at first. Two NAT Networks were using the same `10.0.0.0/24` range, so the network failed to start, and the Kali connection was stuck on "getting IP configuration".

**Fix:** removed the duplicate NAT Network, then set the static IP and disabled the duplicate-address check:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8 ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

---

## Part 2: Android-x86 VM

**Goal:** Run Android-x86 9.0 in VirtualBox on the same NAT Network as Kali, and confirm both machines can reach each other and the Internet.

| Setting | Value |
|---|---|
| VM name | Android9-Lab |
| Type | Linux, Other Linux (64-bit) |
| Memory / Disk | 2048 MB / 10 GB or more (VDI, dynamic) |
| ISO | android-x86_64-9.0-r2 |
| Network | NAT Network `NatNetwork` |
| IP | `10.0.0.9/24`, gateway `10.0.0.1`, DNS `8.8.8.8` |

**Steps:** downloaded the ISO, created the VM, installed Android-x86 to the virtual disk, attached it to `NatNetwork`, and set a static IP inside Android.

**Test:** Android pinged Kali (`10.0.0.2`) and the Internet (`8.8.8.8`), and Kali pinged Android (`10.0.0.9`). All had 0% packet loss.

![Android and Kali ping test](screenshots/android_work.png)

---

## Part 3: Wireshark SOC Labs

**Goal:** Investigate network captures as a SOC analyst. Each lab had 10 tasks, and I completed all six.

| # | Lab | Skill practised | Score | Status |
|---|---|---|---|---|
| 1 | Identify the Attacker's IP | Finding an attacker in traffic | __/10 | Completed |
| 2 | Follow TCP Stream 1 | Rebuilding a conversation | __/10 | Completed |
| 3 | Follow TCP Stream, Part 2 | Finding hidden data | __/10 | Completed |
| 4 | HTTP Plain Text Credentials | Spotting clear-text passwords | __/10 | Completed |
| 5 | Catch the Brute Force Attacker | Repeated login attempts | __/10 | Completed |
| 6 | Catch the Call Scammer | VoIP call investigation | __/10 | Completed |

### What I learned
- Spotting an attacker's IP by looking at who talks the most and to whom
- Using Follow TCP Stream to read full conversations and find hidden data
- Why plain HTTP is unsafe: usernames and passwords are visible in clear text
- Recognising brute-force attacks from repeated login attempts
- Analysing VoIP traffic to investigate a scam call

---

*For learning and authorised testing only. All Wireshark work was done inside the provided labs.*
