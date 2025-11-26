# Proyecto Final - Docker & Kubernetes

**Alumno:** ARIANA NOEMI CHOQUE HONORIO

**Fecha:** 25 de noviembre del 2025

**Curso:** Docker & Kubernetes - i-Quattro

> **Objetivo** Desarrollar y emplear los conceptos teoricos y practicos abarcados en clase. 

# 📚 Índice

1. [Links de Docker Hub](#links-de-docker-hub)  
2. [Parte 1: Setup del Ambiente](#parte-1-setup-del-ambiente)  
   - [Explicación técnica](#explicación-técnica)  
   - [Problemas encontrados](#problemas-encontrados)  
3. [Parte 2: Backend v2.1](#parte-2-backend-v21)  
   - [Código agregado](#código-agregado)  
   - [Errores y soluciones](#problemas-encontrados-y-resolución)  
4. [Parte 3: Frontend v2.2](#parte-3-frontend-v22)  
5. [Parte 5: Ingress + MetalLB](#parte-5-ingress--metallb)  
6. [Conclusiones](#conclusiones)
   
## Links de Docker Hub
- Backend v2.1: [https://hub.docker.com/r/arinoemi/springboot-api]
- Frontend v2.2: [https://hub.docker.com/r/arinoemi/angular-frontend]

## Parte 1: Setup del Ambiente
**Ambiente utilizado:**
- *Virtualizador:* VirtualBox 
- *Nombre de VM/Instancia:* ariana-choque-k8s
- *Sistema operativo:* Ubuntu 24.04 LTS
- *Recursos:* 4GB RAM, 2 CPU cores
- *Red configurada:* NAT
- *Rango MetalLB:* 192.168.100.200 - 192.268.100.210
![Caracteristicas Maquina Virtual](parte1_estadoequipo.png)

### Explicación técnica
Se utilizó **MicroK8s** para montar un cluster Kubernetes ligero. Se habilitaron los addons:
| Addon | Función |
|-------|---------|
| dns | Resolución DNS interna |
| ingress | Manejo de tráfico HTTP/HTTPS |
| metallb | Asignación de IP para balanceador |

Se configuró MetalLB con un rango IP estático para exponer servicios al exterior.  

### Problemas encontrados
1. Rango IP mal configurado (10.0.2.15) asignado a la maquina virtual → corregido a 192.168.100.200-192.168.100.210.  
2. Algunos pods tardaron en iniciar → se utilizó `microk8s status --wait-ready` para validar.  

### Capturas de pantalla
![microk8s status](screenshots/parte1-microk8s-status.png)
![Pods running](screenshots/parte1-pods-running.png)
![Pods running](screenshots/parte1-pods-running2.png)
![Frontend via MetalLB](screenshots/parte1-frontend-browser.png)
### Código agregado

## Parte 2: Backend v2.1
**Descripción de cambios realizados:**  
- Actualización del endpoint `/api/info` para mostrar la información del autor.  
- Subida de la imagen Docker a Docker Hub con versión **v2.1** bajo el usuario `arinoemi`. 
Problemas encontrados y resolución
Error de usuario Docker Hub:
Se genero un error con la creacion de la cuenta ariana.choque no existía y generaba duplicidad por lo cual con el uso de logs se pudo identificar y se realizó el cambio → se utilizó arinoemi y se cambió image en deployment.yaml.
```
kubectl rollout restart deployment api -n proyecto-integrador
```
Deploy incorrecto:
La versión antigua del pod seguía corriendo → se aplicó:
### Screenshots
![Docker build](screenshots/parte2-docker-build.png)
![Rollout](screenshots/parte2-rollout.png)
![API Info](screenshots/parte2-api-info.png)

## Parte 3: Frontend v2.2
Descripción de cambios:
- Consumo del endpoint /api/info del backend.
- Visualización de información del sistema en la UI.
- Build final en Docker v2.2.
***Problemas encontrados****
Build sin Internet: Inicialmente la VM no tenía acceso a npm → solucionado configurando NAT y proxy en VirtualBox.

### Screenshots
![Frontend build](screenshots/parte3-frontend-build.png)
![Frontend UI](screenshots/parte3-frontend-ui.png)
![System info display](screenshots/parte3-system-info.png)


## Parte 5: Ingress + MetalLB

**IP del Ingress:** 192.168.100.200 - 192.268.100.210
***Explicación técnica:***
- Configuración de un recurso Ingress para exponer backend y frontend usando rutas HTTP.
- MetalLB asigna una IP fija para el acceso externo desde cualquier navegador.

***Problemas encontrados***
 - TLS error en port-forward:
```
error upgrading connection: error dialing backend: tls: failed to verify certificate
```
**Solución:** usar la IP de MetalLB para acceso externo en el navegador.
### Screenshots
![Ingress config](screenshots/parte5-ingress.png)
![Acceso externo](screenshots/parte5-external-access.png)

## Conclusiones
- Comprender la interacción entre Docker, Kubernetes y MetalLB.
- Actualización de versiones de imágenes Docker en Kubernetes sin downtime.
- Diagnóstico y resolución de errores frecuentes (usuario Docker Hub, TLS, IP inválida).
### Aprendizajes principales
- [Punto 1]
- [Punto 2]
- [Punto 3]

### Dificultades encontradas
- Configuración de rango IP incorrecto → corregido a 192.168.100.200-192.168.100.210.
- Error de imagen Docker → solución: actualizar deployment.yaml y subir imagen correcta.
- Problemas con port-forward y TLS → solución: acceso vía IP de MetalLB.

### Reflexión
Permite entender cómo desplegar aplicaciones reales, manejar versiones y actualizar servicios sin downtime. Este flujo es esencial para asegurar alta disponibilidad y escalabilidad en proyectos de producción reales.
