# WSL - Windows Subsystem for Linux

WSL2 runs a real Linux kernel in a lightweight VM. Windows drives are automounted under `/mnt/` (e.g. `D:` -> `/mnt/d`) but without Linux metadata support by default, which means file permissions and ownership are not preserved on Windows filesystem paths.

Reference: [Microsoft WSL configuration docs](https://github.com/MicrosoftDocs/wsl/blob/main/WSL/wsl-config.md)


## Metadata: fixing file permissions on Windows mounts

Without metadata, `chmod`, `chown`, and `sed -i` (which uses a temp file) behave unexpectedly or fail with permission errors.

### Remount on the fly

```bash
sudo umount /mnt/d
sudo mount -t drvfs D: /mnt/d -o metadata
```

### Make it permanent

Add to `/etc/wsl.conf`:

```ini
[automount]
options = "metadata"
```

> **Note:** The official Microsoft docs use `options = "metadata,..."` — spaces around `=` and quotes around the value. The quotes are part of the expected format when passing a comma-separated list of sub-options.

Then restart WSL from PowerShell or CMD:

```powershell
wsl --shutdown
```

## wsl.conf reference

Located at `/etc/wsl.conf` inside the distro. Changes take effect after `wsl --shutdown`.

### [automount]

| Option | Default | Description |
|--------|---------|-------------|
| `enabled` | `true` | Auto-mount Windows drives under `root` |
| `mountFsTab` | `true` | Process `/etc/fstab` on startup |
| `root` | `/mnt/` | Mount point prefix for Windows drives |
| `options` | (none) | DrvFs mount options (comma-separated) |

DrvFs mount sub-options for `options=`:

| Sub-option | Default | Description |
|------------|---------|-------------|
| `metadata` | off | Store Linux permissions in NTFS extended attributes |
| `uid` | 1000 | Default file owner UID |
| `gid` | 1000 | Default file owner GID |
| `umask` | 022 | Permission mask for files and directories |
| `fmask` | 000 | Permission mask for files only |
| `dmask` | 000 | Permission mask for directories only |
| `case` | `off` | Case sensitivity: `off`, `dir`, or `force` |

```ini
[automount]
enabled=true
root=/mnt/
options = "metadata,umask=22,fmask=11"
mountFsTab=true
```

### [boot]

| Option | Default | Description |
|--------|---------|-------------|
| `systemd` | `false` | Enable systemd |
| `command` | (none) | Command to run at startup (as root) |
| `protectBinfmt` | `true` | Prevent systemd from overriding binfmt entries |

```ini
[boot]
systemd=true
```

### [network]

| Option | Default | Description |
|--------|---------|-------------|
| `generateHosts` | `true` | Generate `/etc/hosts` |
| `generateResolvConf` | `true` | Generate `/etc/resolv.conf` |
| `hostname` | Windows hostname | WSL distro hostname |

### [interop]

| Option | Default | Description |
|--------|---------|-------------|
| `enabled` | `true` | Allow launching Windows processes from WSL |
| `appendWindowsPath` | `true` | Add Windows PATH entries to `$PATH` |

### [user]

| Option | Default | Description |
|--------|---------|-------------|
| `default` | (install user) | Default login user |

### [gpu] / [time]

| Option | Default | Description |
|--------|---------|-------------|
| `gpu.enabled` | `true` | GPU access via para-virtualization |
| `time.useWindowsTimezone` | `true` | Sync timezone from Windows |

### Full example

```ini
[user]
default=gbraad

[boot]
systemd=true

[network]
generateResolvConf=false

[automount]
options = "metadata"
```

## .wslconfig reference

Located at `%UserProfile%\.wslconfig` on the Windows side. Controls the WSL2 VM globally across all distros.

### [wsl2]

| Option | Default | Description |
|--------|---------|-------------|
| `memory` | 50% of RAM | VM memory allocation (e.g. `8GB`) |
| `processors` | logical CPU count | VM CPU core count |
| `swap` | 25% of memory | Swap size (e.g. `2GB`) |
| `swapFile` | `%Temp%\swap.vhdx` | Swap file path |
| `localhostForwarding` | `true` | Forward localhost ports to Windows host |
| `networkingMode` | `NAT` | `none`, `nat`, `mirrored`, `virtioproxy` |
| `guiApplications` | `true` | WSLg GUI app support |
| `nestedVirtualization` | `true` | Allow VMs inside WSL |
| `vmIdleTimeout` | `60000` (ms) | Idle time before VM shuts down |
| `defaultVhdSize` | `1TB` | Max distro filesystem size |
| `kernel` | (bundled) | Path to custom kernel |
| `kernelCommandLine` | (none) | Extra kernel parameters |

```ini
[wsl2]
memory=8GB
processors=4
swap=2GB
networkingMode=mirrored
```

> Path values require escaped backslashes: `C:\\Temp\\kernel`

## Common WSL commands

```powershell
wsl --shutdown                          # stop all running distros
wsl --list --verbose                    # list distros and status
wsl --set-default Ubuntu                # set default distro
wsl --set-version Ubuntu 2             # upgrade distro to WSL2
wsl --export Ubuntu ubuntu.tar         # back up distro
wsl --import Ubuntu C:\WSL ubuntu.tar  # restore distro
```
