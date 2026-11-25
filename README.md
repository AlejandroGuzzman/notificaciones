# notificaciones

Servidor de notificaciones push ([ntfy](https://github.com/binwiederhier/ntfy)) de la Oficina de IA, Oficialía Mayor de Gobierno del Estado de Tlaxcala. Está compilado desde su código fuente y se publica en internet con un túnel rápido de Cloudflare.

Estancia Profesional de Jesús Alejandro Guzmán Cordero (Ing. en Inteligencia Artificial, IPN UPIIT Tlaxcala), con el Dr. Pedro Aarón Hernández Avalos. ntfy es de Philipp Heckel, con licencia Apache 2.0 / GPLv2.

## Qué hay aquí

| | |
|---|---|
| `ntfy/` | El código fuente original de ntfy v2.37.2, sin cambios. Se puede reemplazar por una descarga del [repositorio oficial](https://github.com/binwiederhier/ntfy/tree/v2.37.2) en esa versión y funciona igual |
| `docker-compose.yml` | Prende dos contenedores: `servidor`, que es ntfy compilado desde `ntfy/`, y `tunel`, el túnel de Cloudflare |
| `server.yml` | La configuración del servidor |
| `README.md` | Este archivo |

## Cómo se prende

```powershell
docker compose up -d
```

La primera vez compila el código, y tarda unos minutos. Después el servidor queda en:

- **Red local:** `http://<IP-de-la-PC>:8080`
- **Internet (HTTPS):** la dirección del túnel, que se ve con

  ```powershell
  docker logs tunel 2>&1 | Select-String "trycloudflare.com"
  ```

  Esa dirección cambia cada vez que se reinicia el túnel. Los túneles rápidos de Cloudflare son para pruebas: no tienen garantía de disponibilidad. Para una dirección fija se usa un túnel con cuenta de Cloudflare.

## Usuarios

Sin usuario no se puede leer ni publicar nada (`deny-all`). Los usuarios se crean una sola vez:

```powershell
docker exec -it servidor ntfy user add --role=admin alex
docker exec -it servidor ntfy user add lector
docker exec servidor ntfy access lector pruebas ro
```

Como el servidor queda en internet, las contraseñas deben ser fuertes. Los usuarios y los mensajes se guardan en el volumen de Docker `notificaciones_datos`, no en este repositorio.

## Probar

```powershell
curl.exe -u alex -H "Title: Prueba" -d "Hola" https://<dirección-del-túnel>/pruebas
```

En el teléfono, en la app ntfy:
1. Ajustes → Avanzado → Protocolo de conexión (*Connection protocol*) → **WebSockets**.
2. **+** → tema `pruebas` → *Usar otro servidor* → la dirección del túnel → usuario y contraseña.

## Clonar en Windows

```powershell
git clone --config core.autocrlf=false https://github.com/AlejandroGuzzman/notificaciones.git
```

Sin `core.autocrlf=false`, Git convierte los saltos de línea del código y la compilación falla. Descargar el ZIP desde GitHub también funciona.
