---
{"dg-publish":true,"dg-path":"Aigent Capabilities/NerdRack.md","permalink":"/aigent-capabilities/nerd-rack/","dg-note-properties":{"type":["asset"]}}
---

## Syncthing Access
https://syncthing.adsvi.se/

## Give IDEAi SSH key to control server
```
Host nerdrack
    HostName 172.245.57.189
    User healmiy
    Port 2525
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/KB/Private/PnC/id_ed25519
```
- [x] give IDEAi aigents access to control NerdRack server {{operonId:: no2em7u}} {{status:: Default.Complete}} {{priority:: House}} {{dateCompleted:: 2026-07-06}} {{contexts:: asset}} {{note:: only need to type in `ssh nerdrack`}} {{datetimeCreated:: 2026-07-06T16:40:30}} {{datetimeModified:: 2026-07-23T02:07:31}}

## Fast Note Sync central node
https://fns.adsvi.se/



