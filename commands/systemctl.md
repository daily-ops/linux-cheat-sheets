#### Enable systemd unit

```
systemctl enable systemd-timesyncd
```

#### Check status of systemd unit

```
systemctl status systemd-timesyncd
```

The description of `Active` status indicates whether the service is running or dead.

```
systemd-timesyncd.service - Network Time Synchronization
     Loaded: loaded (/lib/systemd/system/systemd-timesyncd.service; enabled; ve>
     Active: active (running) since Sun 2025-02-02 18:16:39 AEDT; 2 days ago
       Docs: man:systemd-timesyncd.service(8)
   Main PID: 923 (systemd-timesyn)
     Status: "Initial synchronization to time server 91.189.91.157:123 (ntp.ubu>
      Tasks: 2 (limit: 76696)
     Memory: 1.4M
     CGroup: /system.slice/systemd-timesyncd.service
             └─923 /lib/systemd/systemd-timesyncd
```

#### Print the configuration of systemd unit

```
systemctl cat systemd-timesyncd
```

The top line of the result also indicates the location of the unit file.

```
# /lib/systemd/system/systemd-timesyncd.service
#  SPDX-License-Identifier: LGPL-2.1+
#
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Network Time Synchronization
Documentation=man:systemd-timesyncd.service(8)
ConditionCapability=CAP_SYS_TIME
ConditionVirtualization=!container
DefaultDependencies=no
After=systemd-sysusers.service
Before=time-set.target sysinit.target shutdown.target
Conflicts=shutdown.target
Wants=time-set.target time-sync.target
...
...
...
```

#### Reload systemd unit

```
systemctl daemon-reload
```

NOTE: When editing systemd unit file, a reload is required.
