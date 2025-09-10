# vps-manifests
Kubernetes manifests for Oracle VPS


````markdown
# MicroK8s en Oracle VPS con Nginx Ingress, Certificados, Prometheus y Grafana

Esta guía describe cómo instalar MicroK8s en un VPS de Oracle y habilitar addons clave para producción.

---

## Requisitos

- VPS Oracle Linux / Ubuntu 20.04+ (64-bit)
- Usuario con permisos `sudo`
- Internet activo
- Puertos 80 y 443 abiertos

---

## Actualizar sistema

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install snapd -y
````

---

## Instalar MicroK8s

```bash
sudo snap install microk8s --classic --channel=1.27/stable
microk8s status --wait-ready
```

---

## Configurar usuario para MicroK8s

```bash
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
newgrp microk8s
microk8s kubectl get nodes
```

---

## Habilitar addons

### DNS y almacenamiento

```bash
microk8s enable dns storage
```

### Nginx Ingress

```bash
microk8s enable ingress
microk8s enable metallb
```

### Cert-manager (TLS automático)

```bash
microk8s enable cert-manager
```

> Configura un `ClusterIssuer` de Let's Encrypt para dominios públicos.

### Prometheus y Grafana

```bash
microk8s helm3 repo add prometheus-community https://prometheus-community.github.io/helm-charts
microk8s helm3 repo add grafana https://grafana.github.io/helm-charts
microk8s helm3 repo update
microk8s helm3 install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
microk8s helm3 install grafana grafana/grafana --namespace monitoring --create-namespace

```