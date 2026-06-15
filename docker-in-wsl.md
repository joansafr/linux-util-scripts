# 🐳 Instalar Docker Engine nativo en WSL2 (Ubuntu)

Guía para instalar Docker Engine directamente en tu distro Ubuntu de WSL2, **sin depender de Docker Desktop**.

---

## Tabla de contenidos

- [Requisitos previos](#requisitos-previos)
- [1. Desactivar Docker Desktop en la distro](#1-desactivar-docker-desktop-en-la-distro)
- [2. Instalar dependencias](#2-instalar-dependencias)
- [3. Habilitar systemd](#3-habilitar-systemd)
- [4. Instalar Docker Engine](#4-instalar-docker-engine)
- [5. Arrancar el servicio](#5-arrancar-el-servicio)
- [6. Usar Docker sin sudo](#6-usar-docker-sin-sudo)
- [7. Verificar la instalación](#7-verificar-la-instalación)
- [Recomendaciones](#recomendaciones)

---

## Requisitos previos

- Windows 10/11 con **WSL2** habilitado.
- Distro **Ubuntu** instalada desde la Microsoft Store.
- *(Opcional)* Configuración en Docker Desktop instalado previamente.

---

## 1. Desactivar Docker Desktop en la distro

Si tenías Docker Desktop instalado, desactiva la integración con Ubuntu para no mezclar motores:

1. Abre **Docker Desktop → Settings → Resources → WSL Integration**.
2. Desactiva **Ubuntu**.
3. Reinicia el PC.

---

## 2. Instalar dependencias

Abre Ubuntu en WSL y ejecuta:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
```

---

## 3. Habilitar systemd

Si tu distro aún no tiene systemd habilitado:

```bash
printf "[boot]\nsystemd=true\n" | sudo tee /etc/wsl.conf
```

Cierra Ubuntu y desde **PowerShell** ejecuta:

```powershell
wsl --shutdown
```

Vuelve a abrir Ubuntu WSL.

---

## 4. Instalar Docker Engine

### 4.1 Eliminar versiones anteriores

```bash
sudo apt remove -y docker.io docker-doc docker-compose docker-compose-v2 \
  podman-docker containerd runc 2>/dev/null || true
```

### 4.2 Añadir el repositorio oficial de Docker

```bash
sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources > /dev/null <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

### 4.3 Instalar los paquetes

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

> Esto sigue el [método oficial de Docker para Ubuntu](https://docs.docker.com/engine/install/ubuntu/).

---

## 5. Arrancar el servicio

```bash
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker --no-pager
```

---

## 6. Usar Docker sin sudo

```bash
sudo groupadd docker 2>/dev/null || true
sudo usermod -aG docker $USER
newgrp docker
```

> Documentado en los [pasos post-instalación de Docker](https://docs.docker.com/engine/install/linux-postinstall/) para usuarios no root.

---

## 7. Verificar la instalación

Asegúrate de que estás usando el Docker nativo de Ubuntu y no Docker Desktop:

```bash
docker context use default
unset DOCKER_HOST
docker info | grep -E "Operating System|Docker Root Dir|Context"
```

Deberías ver algo como:

```
Operating System: Ubuntu ...
Docker Root Dir: /var/lib/docker
```

---

## Recomendaciones

> **Tip:** Para mejor rendimiento, copia los archivos de tu proyecto dentro del sistema de archivos de Ubuntu (por ejemplo, `~/projects/`) en lugar de acceder a rutas de Windows (`/mnt/c/...`). Las operaciones de I/O son significativamente más rápidas en el filesystem nativo de WSL2.