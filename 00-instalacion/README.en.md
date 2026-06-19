# 00 - Installation: WSL + Rust environment from scratch

This guide documents, step by step and without assuming anything, how to
create a separate Debian instance in WSL2, dedicated to learning Rust,
while leaving any previous instance with other projects untouched.

## Prerequisites
- Windows with WSL2 installed.
- An existing Debian instance in WSL (optional, won't be modified).

## 1. Create a new clean Debian instance

```powershell
wsl --install -d Debian --name DebianRust
```

Verify:

```powershell
wsl --list --verbose
```

## 2. Hostname (cosmetic, may show the Windows PC name)

Verify the real instance without trusting the prompt:

```bash
echo $WSL_DISTRO_NAME
```

Change hostname (without `hostnamectl`, since WSL runs without systemd by default).

Edit `/etc/wsl.conf`:

```
[boot]
systemd=false

[network]
hostname = your-chosen-name
```

Edit `/etc/hostname` (leave only the chosen name):

```
your-chosen-name
```

Restart the instance:

```powershell
wsl --terminate DebianRust
wsl -d DebianRust
```

## 3. Force startup in the Linux home (not in /mnt/c/...)

```powershell
wsl -d DebianRust --cd ~
```

## 4. PowerShell alias for quick access

Edit `$PROFILE` and add:

```powershell
function rustenv { wsl -d DebianRust --cd ~ }
```

## 5. Install Rust

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential curl -y
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"
rustc --version
cargo --version
```

Verify it persisted:

```bash
cat ~/.bashrc | grep cargo
```
