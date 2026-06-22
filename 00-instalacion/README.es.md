# 00 - Instalacion: Entorno WSL + Rust desde cero

Esta guia documenta, paso a paso y sin suponer nada, como crear una instancia
separada de Debian en WSL2, dedicada a aprender Rust, dejando intacta
cualquier instancia previa con otros proyectos.

## Requisitos previos
- Windows con WSL2 instalado.
- Una instancia de Debian ya existente en WSL (opcional, no se modifica).

## 1. Crear una nueva instancia de Debian limpia

Desde PowerShell:

    wsl --install -d Debian --name DebianRust

Verificar:

    wsl --list --verbose

## 2. Hostname

Verificar instancia real sin fiarse del prompt:

    echo $WSL_DISTRO_NAME

Editar /etc/wsl.conf:

    [boot]
    systemd=false

    [network]
    hostname = tu-nombre-elegido

Editar /etc/hostname con el nombre elegido. Reiniciar:

    wsl --terminate DebianRust
    wsl -d DebianRust

## 3. Forzar inicio en el home de Linux

    wsl -d DebianRust --cd ~

## 4. Alias en PowerShell

Agregar en $PROFILE:

    function rustenv { wsl -d DebianRust --cd ~ }

## 5. Instalar Rust

    sudo apt update && sudo apt upgrade -y
    sudo apt install build-essential curl -y
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    source "$HOME/.cargo/env"
    rustc --version
    cargo --version

Verificar que quedo permanente:

    cat ~/.bashrc | grep cargo

## 6. Editor: Vim (recomendado para este entorno sin GUI)

Verificar version instalada:

    vim --version | head -5

Debe decir "Huge version" para confirmar soporte completo de sintaxis.

Crear configuracion basica en ~/.vimrc:

    syntax on
    set number
    set tabstop=4
    set shiftwidth=4
    set expandtab
    set autoindent
    filetype plugin indent on

Esto activa resaltado de sintaxis (Rust incluido por defecto en Vim moderno),
numeros de linea e indentacion de 4 espacios.

## 7. Editor: VS Code (opcional, via Remote-WSL)

VS Code corre en Windows (con GUI) y se conecta remotamente a WSL (sin GUI)
mediante la extension "Remote - WSL". El codigo Linux se edita desde la
interfaz grafica de Windows sin necesitar entorno grafico en Linux.

### Instalacion de extensiones (por linea de comandos, desde PowerShell)

    code --install-extension ms-vscode-remote.remote-wsl
    code --install-extension rust-lang.rust-analyzer

### Conectar a WSL

Desde la terminal de WSL, dentro de la carpeta del proyecto:

    code .

Problema conocido: en una instalacion minima de Debian esto puede fallar con
"ERROR: Failed to download the VS Code server. 'wget' not installed."
Solucion:

    sudo apt install wget -y

Luego reintentar code .

### Problema conocido: modal "Sign in to use Agents" bloqueado

Al abrir VS Code puede aparecer un modal que no responde a nada: ni Esc,
ni clic afuera, ni Ctrl+Shift+P, ni cerrar y reabrir la app.

"Agents" es la funcion de asistente de IA agéntico de VS Code (en preview):
permite que un modelo de IA edite archivos y ejecute comandos de forma
autonoma, integrada con GitHub Copilot. Para usarla pide login con GitHub,
y en ciertos casos ese modal queda completamente bloqueado.

Pasos que se probaron y que en conjunto resolvieron el problema
(no se pudo confirmar cual exactamente fue el determinante):

1. Abrir con perfil aislado para descartar corrupcion de configuracion:
   code --user-data-dir="C:\vscode-test-limpio"

2. Borrar el archivo de estado de la app (desde PowerShell):
   Remove-Item "$env:APPDATA\Code\User\globalStorage\state.vscdb"

3. Desactivar la funcion Agents via configuracion (desde PowerShell):
   code "$env:APPDATA\Code\User\settings.json"
   Y agregar dentro de las llaves:
   { "chat.agent.enabled": false }

4. Guardar y reabrir VS Code normalmente.
