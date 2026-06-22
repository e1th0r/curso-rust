# 01 - Fundamentos: Cargo y tu primer programa

## Que es cargo

cargo es el gestor de proyectos de Rust. Reemplaza varias herramientas que
en otros lenguajes son separadas (npm, pip, make, etc.). Con un solo comando
puedes crear proyectos, compilar, ejecutar y gestionar dependencias.

## Comandos basicos

Crear un nuevo proyecto:

    cargo new nombre-del-proyecto

Compilar el proyecto:

    cargo build

Compilar y ejecutar en un solo paso:

    cargo run

Verificar errores sin compilar del todo (mas rapido):

    cargo check

Compilar en modo produccion (optimizado):

    cargo build --release

## Estructura de un proyecto cargo

Al ejecutar cargo new hello-rust, la salida fue:

    Creating binary (application) `hello-rust` package

Y se generó esta estructura:

    hello-rust/
    ├── Cargo.toml       <- configuracion del proyecto
    ├── Cargo.lock       <- versiones exactas de dependencias (generado automaticamente)
    └── src/
        └── main.rs      <- codigo fuente principal

## Cargo.toml

Contenido real generado por cargo new:

    [package]
    name = "hello-rust"
    version = "0.1.0"
    edition = "2024"

    [dependencies]

- [package]: metadatos del proyecto
- edition: version del lenguaje Rust (2024 es la mas reciente)
- [dependencies]: librerias externas que necesite el proyecto (vacio = ninguna)

## Tu primer programa

Contenido real de src/main.rs generado por cargo new:

    fn main() {
        println!("Hello, world!");
    }

- fn main(): punto de entrada del programa, lo primero que se ejecuta
- println!: macro que imprime texto en pantalla (el ! indica que es una macro,
  no una funcion comun — la diferencia se vera mas adelante)
- Las llaves { } delimitan el cuerpo de la funcion

## Ejecutar el programa

Al correr cargo run, la salida fue:

    Compiling hello-rust v0.1.0 (/home/hector/curso-rust/01-fundamentos/hello-rust)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.65s
    Running `target/debug/hello-rust`
    Hello, world!

- Compiling: compilo el codigo fuente
- Finished 'dev' profile [unoptimized + debuginfo]: modo desarrollo, mas rapido
  de compilar pero sin optimizaciones (para produccion: cargo build --release)
- Running: ejecuto el binario generado
- Hello, world!: salida del programa

## El ejecutable generado

El binario queda en target/debug/:

    -rwxr-xr-x 2 hector hector 4345152 Jun 21 23:02 target/debug/hello-rust

4.3 MB en modo desarrollo (unoptimized + debuginfo). Este tamaño es esperado
y no debe preocupar: incluye simbolos de depuracion y no aplica optimizaciones,
lo que facilita el debugging pero genera binarios mas grandes. Al compilar para
produccion el resultado es considerablemente mas pequeno:

    cargo build --release
    (el binario queda en target/release/hello-rust)

La carpeta target/ no se sube al repositorio (esta en .gitignore).

## Nota sobre git y cargo

cargo new inicializa automaticamente un repositorio git dentro del proyecto.
Si el proyecto vive dentro de otro repositorio git (como este curso), hay que
eliminar ese .git interno para evitar conflictos:

    rm -rf nombre-del-proyecto/.git
