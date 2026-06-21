---
title: "proxmox: minha setup atual"
description: "Como eu configurei meu homelab com ThinkCentre M720q rodando Proxmox, LXC containers e gerenciamento de VMs."
date: "2024-12"
tag: "homelab"
---

<!-- Meu homelab roda em um ThinkCentre M720q — compacto, silencioso e econômico. Perfeito pra ficar ligado 24/7.

## Hardware

- **CPU**: Intel Core i5-8400T (6 cores, 35W TDP)
- **RAM**: 16GB DDR4
- **Storage**: SSD NVMe 256GB + HD 1TB

## Por que Proxmox?

Proxmox VE combina KVM e LXC em uma interface web. Eu uso principalmente LXC porque é mais leve que VMs completas pra serviços simples.

## Containers rodando

| ID  | Nome        | RAM   | Função               |
|-----|-------------|-------|----------------------|
| 101 | gabsite     | 512MB | site pessoal + nginx |
| 102 | pihole      | 128MB | DNS ad-blocking      |
| 103 | vaultwarden | 256MB | gerenciador de senhas|

Todos expostos via Cloudflare Tunnel — sem abrir portas no roteador. -->


# Por enquanto é só template