# Actualizar el servidor Docker del fork

Desde el clon del fork, en PowerShell:

```powershell
git switch master
git pull --ff-only origin master
```

Si Git terminó correctamente, indicá el commit y la ruta absoluta de los datos de la instalación actual. En esta instalación:

```powershell
$env:FORK_REVISION = git rev-parse HEAD
$env:SUWAYOMI_DATA_DIR = "$env:USERPROFILE/Documents/Manga_Server/data"
Get-Item "$env:SUWAYOMI_DATA_DIR/database.mv.db", "$env:SUWAYOMI_DATA_DIR/downloads" -ErrorAction Stop
docker compose -f docker/fork/compose.yaml build suwayomi
```

Una vez compilada la imagen, detené el servicio y hacé una copia de la carpeta de datos con la base cerrada. Conservá también la referencia a la imagen anterior para poder volver a esa versión. Podés registrar la imagen y detener el servicio desde PowerShell:

```powershell
docker inspect suwayomi --format '{{.Config.Image}}'
docker compose -f docker/fork/compose.yaml stop -t 90 suwayomi
```

Con el respaldo terminado, levantá el servicio:

```powershell
docker compose -f docker/fork/compose.yaml up -d --no-build --pull never suwayomi
docker compose -f docker/fork/compose.yaml logs --tail 100 suwayomi
```

Abrí http://localhost:4567 y verificá la biblioteca y las descargas. Compose usa el proyecto existente `manga_server` y monta la misma carpeta de datos en cada actualización. La imagen queda etiquetada con el commit completo. Si necesitás volver después de una migración, detené el servicio y recuperá juntos la base respaldada y su imagen anterior.
