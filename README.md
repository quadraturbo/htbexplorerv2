# htbExplorer v2 — Hack The Box Terminal Client (API v4)

Cliente de terminal escrito en Bash para interactuar con **Hack The Box Labs** directamente desde la consola, adaptado a la **API v4**.

> **Créditos y atribución**
>
> Este proyecto está basado en **htbExplorer**, creado originalmente por **Marcelo Vázquez (@s4vitar)**. La idea, concepto y proyecto original pertenecen a su autor. Esta versión es una **adaptación/migración comunitaria a la API v4**, manteniendo como referencia el trabajo original.
>
> Proyecto original: [s4vitar/htbExplorer](https://github.com/s4vitar/htbExplorer)

## ¿Qué aporta esta versión?

La versión original de htbExplorer utilizaba la API disponible en su momento. Esta adaptación actualiza el cliente para trabajar con **Hack The Box Labs API v4**, utilizando autenticación mediante **Bearer App Token** y endpoints actuales de la plataforma.

Además, se ha mantenido el enfoque original: una herramienta ligera, orientada a terminal y sin necesidad de Python, `html2text` ni privilegios `root`.

## Características

- Listar máquinas activas y retiradas.
- Filtrar máquinas por sistema operativo: Linux, Windows, FreeBSD, OpenBSD y otros.
- Listar máquinas propias y máquinas propias activas.
- Consultar la máquina actualmente spawneada.
- Buscar máquinas por nombre o palabra clave.
- Comprobar si una IP corresponde a tu máquina activa.
- Spawnear una máquina.
- Terminar una máquina.
- Reiniciar una máquina.
- Extender el tiempo de una máquina.
- Enviar flags.
- Buscar información básica de usuarios.
- Descargar la configuración `.ovpn` de tu servidor VPN.
- Salida en tablas directamente desde la terminal.

## Requisitos

Necesitas un sistema con Bash y estas dependencias:

```bash
curl
jq
awk
```

En Debian/Ubuntu:

```bash
sudo apt install curl jq gawk
```

En Arch Linux:

```bash
sudo pacman -S curl jq gawk
```

También necesitas una cuenta de Hack The Box con un **App Token** válido para la plataforma.

## Instalación

Clona el repositorio y dale permisos de ejecución al script:

```bash
git clone https://github.com/TU-USUARIO/htbExplorer.git
cd htbExplorer
chmod +x htbExplorer
```

## Configuración del token

La herramienta busca el token en este orden:

1. Variable de entorno `HTB_TOKEN`.
2. Archivo `~/.config/htbExplorer/token` (respetando `XDG_CONFIG_HOME` si está definido).
3. Valor definido directamente en la variable `API_TOKEN` del script.

### Opción recomendada: variable de entorno

```bash
export HTB_TOKEN='TU_APP_TOKEN'
```

Después:

```bash
./htbExplorer -e active_machines
```

### Guardar el token en un archivo

```bash
mkdir -p ~/.config/htbExplorer
printf '%s\n' 'TU_APP_TOKEN' > ~/.config/htbExplorer/token
chmod 600 ~/.config/htbExplorer/token
```

De esta forma no necesitas exportar la variable en cada sesión.

> **Importante:** no subas nunca tu App Token al repositorio. Si un token queda expuesto, revócalo y genera uno nuevo.

## Uso

Mostrar la ayuda:

```bash
./htbExplorer -h
```

### Exploración

Listar todas las máquinas:

```bash
./htbExplorer -e all_machines
```

Listar máquinas activas:

```bash
./htbExplorer -e active_machines
```

Listar máquinas retiradas:

```bash
./htbExplorer -e retired_machines
```

Listar tu máquina activa:

```bash
./htbExplorer -e spawned_machines
```

Listar máquinas que has obtenido:

```bash
./htbExplorer -e owned_machines
```

Filtrar por sistema operativo:

```bash
./htbExplorer -e active_linux_machines
./htbExplorer -e active_windows_machines
./htbExplorer -e active_freebsd_machines
./htbExplorer -e active_openbsd_machines
./htbExplorer -e active_other_machines
```

También existen los equivalentes para máquinas retiradas, por ejemplo:

```bash
./htbExplorer -e retired_linux_machines
```

### Buscar una máquina

```bash
./htbExplorer -s Rope
```

La búsqueda por nombre admite coincidencias parciales.

### Comprobar una IP

La API v4 solo proporciona la IP de la máquina que tienes actualmente activa/spawneada:

```bash
./htbExplorer -i 10.10.11.10
```

### Acciones sobre máquinas

Spawnear:

```bash
./htbExplorer -d Aragog
```

Terminar:

```bash
./htbExplorer -k Hawk
```

Reiniciar:

```bash
./htbExplorer -r Mantis
```

Extender tiempo:

```bash
./htbExplorer -x Legacy
```

### Buscar usuarios

```bash
./htbExplorer -u s4vitar
```

### Enviar una flag

```bash
./htbExplorer -f 'Bucket=flag123'
```

### Descargar la VPN

```bash
./htbExplorer -v htb.ovpn
```

Por defecto se utiliza el servidor asignado a tu cuenta.

También puedes indicar explícitamente el servidor mediante variables de entorno:

```bash
export HTB_VPN_ID=123
```

Para solicitar configuración TCP:

```bash
export HTB_VPN_ID=123
export HTB_VPN_TCP=1
./htbExplorer -v htb-tcp.ovpn
```

## Variables de entorno

| Variable | Descripción |
|---|---|
| `HTB_TOKEN` | App Token utilizado para autenticarse contra la API v4. |
| `HTB_API_URL` | Permite cambiar la URL base de la API. Por defecto: `https://labs.hackthebox.com/api/v4`. |
| `HTB_VPN_ID` | ID del servidor VPN que quieres utilizar. |
| `HTB_VPN_TCP` | Usa `1` para solicitar la configuración TCP en la descarga de la VPN. |
| `XDG_CONFIG_HOME` | Cambia la ubicación base usada para el archivo de token. |

## Opciones disponibles

| Opción | Función | Ejemplo |
|---|---|---|
| `-e` | Modo de exploración | `-e active_machines` |
| `-s` | Buscar máquina por nombre | `-s Rope` |
| `-i` | Comprobar la IP de tu máquina activa | `-i 10.10.11.10` |
| `-u` | Buscar usuario | `-u s4vitar` |
| `-d` | Spawnear máquina | `-d Aragog` |
| `-k` | Terminar máquina | `-k Hawk` |
| `-r` | Reiniciar máquina | `-r Mantis` |
| `-x` | Extender tiempo | `-x Legacy` |
| `-f` | Enviar flag | `-f 'Bucket=flag123'` |
| `-v` | Descargar VPN | `-v htb.ovpn` |
| `-h` | Mostrar ayuda | `-h` |

## Cambios respecto al proyecto original

Esta versión está orientada a la API v4 y, por tanto, algunos comportamientos del cliente original ya no están disponibles.

Entre los cambios principales:

- Autenticación mediante `Authorization: Bearer ...`.
- Migración de consultas al API v4.
- Uso de `/machine/paginated` y `/machine/list/retired/paginated` para el listado de máquinas.
- Resolución de máquinas mediante `/machine/profile/<nombre>`.
- Acciones de máquinas mediante endpoints `/vm/...`.
- Consulta de usuario mediante `/search/fetch` y `/user/profile/basic/<id>`.
- Descarga VPN mediante `/connections/servers` y `/access/ovpnfile/...`.
- Eliminadas las funciones antiguas de **assign** y **ShoutBox**, ya que esta versión no las utiliza.
- La búsqueda por IP queda limitada a la máquina actualmente activa debido a la información expuesta por la API v4.

## Seguridad

Este proyecto necesita un token personal de Hack The Box para realizar determinadas operaciones.

Recomendaciones básicas:

```bash
# Nunca hagas esto dentro de un repositorio público:
HTB_TOKEN='TU_TOKEN_REAL'
```

Usa variables de entorno o el archivo de configuración local y evita incluir credenciales en commits, capturas de pantalla o logs.

Para comprobar rápidamente si estás a punto de subir un secreto:

```bash
git diff --cached
```

## Limitaciones

El comportamiento de la herramienta depende de los endpoints y permisos disponibles en Hack The Box. Si la API cambia, algunos comandos pueden dejar de funcionar hasta adaptar nuevamente las rutas o las estructuras JSON.

La API v4 tampoco expone toda la información que estaba disponible en versiones anteriores, por lo que algunas funciones del htbExplorer original no se pueden conservar exactamente igual.

## Disclaimer

Este proyecto es una herramienta comunitaria destinada a facilitar la interacción con Hack The Box desde la terminal.

No está afiliado oficialmente con Hack The Box ni pretende sustituir sus servicios oficiales.

Utiliza la herramienta únicamente dentro de los límites de tu cuenta, permisos y las reglas de Hack The Box.

## Créditos

Todo el reconocimiento por el proyecto original **htbExplorer** corresponde a **Marcelo Vázquez (@s4vitar)**.

La adaptación a API v4 publicada en este repositorio parte de ese trabajo original y se distribuye con el objetivo de mantener su utilidad frente a los cambios de la plataforma.

⭐ Si este proyecto te resulta útil, considera dar crédito también al proyecto original:

**S4vitar — htbExplorer**  
https://github.com/s4vitar/htbExplorer

---

### Autor de esta adaptación

```text
Adaptación API v4: @quadraturbo
```
