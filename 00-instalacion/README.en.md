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

## 6. Editor: Vim (recommended for this environment without GUI)

Verify the installed version:

    vim --version | head -5

It should say "Huge version" to confirm full syntax support.

Create basic configuration in ~/.vimrc:

    syntax on
    set number
    set tabstop=4
    set shiftwidth=4
    set expandtab
    set autoindent
    filetype plugin indent on

This enables syntax highlighting (Rust included by default in modern Vim),
line numbers and 4-space indentation.

## 7. Editor: VS Code (optional, via Remote-WSL)

VS Code runs on Windows (with GUI) and connects remotely to WSL (without GUI)
via the "Remote - WSL" extension. Linux code is edited from the Windows
graphical interface without needing a graphical environment in Linux.

### Installing extensions (via command line, from PowerShell)

    code --install-extension ms-vscode-remote.remote-wsl
    code --install-extension rust-lang.rust-analyzer

### Connecting to WSL

From the WSL terminal, inside the project folder:

    code .

Known issue: on a minimal Debian installation this may fail with
"ERROR: Failed to download the VS Code server. 'wget' not installed."
Solution:

    sudo apt install wget -y

Then retry code .

### Known issue: "Sign in to use Agents" modal locked

When opening VS Code a modal may appear that does not respond to anything:
not Esc, not clicking outside, not Ctrl+Shift+P, not closing and reopening
the app.

"Agents" is VS Code's agentic AI assistant feature (still in preview): it
allows an AI model to edit files and run commands autonomously, integrated
with GitHub Copilot. To use it, it asks for GitHub login, and in some cases
that modal becomes completely locked.

Steps that were tried and that together resolved the issue
(it could not be confirmed which one exactly was the determining factor):

1. Open with an isolated profile to rule out config corruption:
   code --user-data-dir="C:\vscode-test-clean"

2. Delete the app state file (from PowerShell):
   Remove-Item "$env:APPDATA\Code\User\globalStorage\state.vscdb"

3. Disable the Agents feature via settings (from PowerShell):
   code "$env:APPDATA\Code\User\settings.json"
   And add inside the curly braces:
   { "chat.agent.enabled": false }

4. Save and reopen VS Code normally.
