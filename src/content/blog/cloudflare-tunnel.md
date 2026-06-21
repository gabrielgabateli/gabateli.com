---
title: "configurando cloudflare tunnel no homelab"
description: "Como expor serviços do homelab sem abrir portas no roteador, usando Cloudflare Tunnel gratuitamente."
date: "2025-01"
tag: "homelab"
---

<!-- Queria acessar meus serviços de fora de casa sem mexer no roteador. A solução foi o `cloudflared`.

## Por que não abrir porta no roteador?

- Expõe o IP residencial diretamente
- SSL manual é chato
- IP dinâmico quebra tudo

Com o tunnel, o tráfego passa pelos servidores da Cloudflare. Zero portas abertas, SSL grátis.

## Instalando

```bash
curl -L https://pkg.cloudflare.com/cloudflare-main.gpg \
  | sudo tee /usr/share/keyrings/cloudflare-main.gpg > /dev/null

echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] \
  https://pkg.cloudflare.com/cloudflared any main' \
  | sudo tee /etc/apt/sources.list.d/cloudflared.list

sudo apt update && sudo apt install cloudflared
cloudflared tunnel login
cloudflared tunnel create meu-homelab -->


# Por enquanto é só template