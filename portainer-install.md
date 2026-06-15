# 🖥️ Instalar Portainer CE con Docker

Guía rápida para desplegar [Portainer Community Edition](https://www.portainer.io/) como contenedor Docker y gestionar tu entorno de forma visual.

---

## Tabla de contenidos

- [Requisitos previos](#requisitos-previos)
- [1. Desplegar Portainer](#1-desplegar-portainer)
- [2. Acceder a la interfaz web](#2-acceder-a-la-interfaz-web)

---

## Requisitos previos

- Docker Engine instalado y en ejecución.
- Puerto **9000** disponible en el host.

---

## 1. Desplegar Portainer

```bash
docker run -d \
  --name=portainer \
  --restart=always \
  -p 9000:9000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

| Parámetro | Descripción |
|-----------|-------------|
| `--restart=always` | Se reinicia automáticamente con Docker |
| `-p 9000:9000` | Expone la UI web en el puerto 9000 |
| `-v /var/run/docker.sock:...` | Permite a Portainer gestionar Docker |
| `-v portainer_data:/data` | Persiste la configuración en un volumen |

---

## 2. Acceder a la interfaz web

1. Abre en tu navegador: `http://<IP_DEL_HOST>:9000`
2. Crea el usuario administrador y establece una contraseña segura.

> **⚠️ Importante:** Debes configurar el usuario admin **dentro de los primeros minutos** tras el despliegue. Si tardas demasiado, Portainer desactiva el registro por seguridad y tendrás que reiniciar el contenedor.