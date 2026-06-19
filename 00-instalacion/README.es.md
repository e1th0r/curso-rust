# 00 - Instalación: Entorno WSL + Rust desde cero

Esta guía documenta, paso a paso y sin suponer nada, cómo crear una instancia
separada de Debian en WSL2, dedicada a aprender Rust, dejando intacta
cualquier instancia previa con otros proyectos.

## Requisitos previos
- Windows con WSL2 instalado.
- Una instancia de Debian ya existente en WSL (opcional, no se modifica).

## 1. Crear una nueva instancia de Debian limpia

```powershell
wsl --install -d Debian --name DebianRust
```

Verificar:

```powershell
wsl --list --verbose
```

## 2. Hostname (cosmético, puede mostrar el nombre del PC Windows)

Verificar instancia real sin fiarse del prompt:

```bash
echo $WSL_DISTRO_NAME
```

Cambiar hostname (sin `hostnamectl`, ya que WSL corre sin systemd por defecto).

Editar `/etc/wsl.conf`:

```
[boot]
systemd=false

[network]
hostname = tu-nombre-elegido
```

Editar `/etc/hostname` (dejar solo el nombre elegido):

```
tu-nombre-elegido
```

Reiniciar instancia:

```powershell
wsl --terminate DebianRust
wsl -d DebianRust
```

## 3. Forzar inicio en el home de Linux (no en /mnt/c/...)

```powershell
wsl -d DebianRust --cd ~
```

## 4. Alias en PowerShell para entrar rápido

Editar `$PROFILE` y agregar:

```powershell
function rustenv { wsl -d DebianRust --cd ~ }
```

## 5. Instalar Rust

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential curl -y
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"
rustc --version
cargo --version
```

Verificar que quedó permanente:

```bash
cat ~/.bashrc | grep cargo
```
