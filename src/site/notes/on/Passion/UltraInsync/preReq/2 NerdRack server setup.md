---
{"dg-publish":true,"dg-path":"UltraInsync/preReq/2 NerdRack server setup.md","permalink":"/ultra-insync/pre-req/2-nerd-rack-server-setup/","tags":["decks"],"created":"2026-07-05T01:45:31","updated":"2026-07-10T14:53:31","dg-note-properties":{"ID":"scn_ca064d2f","Class":"Scene","Act":1,"cssclasses":["cornell-border","cornell-left","cornell-livepreview","wide-page"],"operonId":"vlv34iw","operonProjectStage":"Default.Working","priority":"Zero","assignees":["[[Healmiy]]"],"description":"have an always on device as central node for Vertex Proxy, Headscale, Syncthing etc","datetimeCreated":"2026-07-05T01:45:31","timestamp":"2026-07-10T14:53:31","stakeholder":["[[AGENTS|aigents]]"],"Status":"Working","type":["asset"],"progress":0,"directSubtaskCount":1,"directDoneSubtaskCount":0,"directOpenSubtaskCount":1,"treeDescendantCount":1,"treeDoneDescendantCount":0,"treeOpenDescendantCount":1,"trackers":["2026-07-05T01:58:27/2026-07-05T02:05:54"],"timeSpent":"0 hours 7 minutes","totalDuration":2,"tags":["decks"],"canvas":["[[3a1. SyncThings Setup.canvas]]"],"3a1. SyncThings Setup":[],"Chapter":"3a1. SyncThings Setup"}}
---


## Must have:
- [x] [[inBox/BiB/Syncthing\|Syncthing]] - act as central node, especially for mobile devices
```
sudo apt-get update
sudo apt-get install syncthing

systemctl cat syncthing@.service
sudo systemctl daemon-reload
sudo systemctl enable syncthing@healmiy
sudo systemctl start syncthing@healmiy

sudo systemctl status syncthing@healmiy
```

- [x] Cloudflare 
```
sudo apt-get update && sudo apt-get install cloudflared
sudo reboot
cloudflared --version
```

- [x] Tailscale
```
curl -fsSL https://tailscale.com/install.sh  && sudo tailscale up
sudo tailscale set --ssh
```

- [x] UFW
```
sudo ufw allow 2525/tcp              # SSH
sudo ufw allow 41641/udp             # Tailscale WireGuard
sudo ufw allow 22420/tcp             # Syncthing sync
sudo ufw allow 22420/udp             # Syncthing sync
sudo ufw allow in on tailscale0      # All Tailscale traffic
```

```
sudo ufw default deny incoming
sudo ufw default allow outgoing

ip link show

sudo ufw show added      # or below line if ufw is not yet activated
sudo ufw status numbered
sudo ufw app list
```

## SSH Hardening
Created drop-in config at `/etc/ssh/sshd_config.d/hardened.conf`:
```
Port 2525
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
AllowUsers healmiy
X11Forwarding no
```
Validated with sshd -t and restarted sshd. Confirmed listening on port 2525 (IPv4 + IPv6), port 22 no longer open.
### on M2AirMie
```
mkdir -p ~/.ssh
touch ~/.ssh/config
chmod 600 ~/.ssh/config
```
paste 👇 

<div style="padding: 12px 16px; margin: 1rem 0; border-radius: 8px; border: 1px dashed var(--text-muted); background: var(--background-secondary); display: flex; align-items: center; gap: 10px; color: var(--text-muted); font-size: 0.9em;"><svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" style="flex-shrink: 0;"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect><path d="M7 11V7a5 5 0 0 1 10 0v4"></path></svg><span>Protected block: <code>NerdRack#Give IDEAi SSH key to control server</code></span></div>


- [ ] Give Aigents (IDEAi), access to NerdRack server via SSH key Authentication (just use `ssh adsvise`)
	- [x] M2AirMie
	- [ ] [[AiM1\|AiM1]]
- [x] Fast Note Sync
	- [x] AiM1
	- [x] EmiPhone
	- [x] iPadM3mi


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/ultra-insync/pre-req/4-sync-thing-pk-mx-kb-folder-to-all-devices/#devices" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



## Devices
Main - [[M2AirMie\|M2AirMie]]
	Phone - EmiPhone - PKMxKB vault stored in MicroSD card
	iPad - iPadM3mi

</div></div>
