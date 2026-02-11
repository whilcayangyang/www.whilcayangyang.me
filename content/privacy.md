### Privacy Resources

Privacy and security services personally recommended:

- [Proton](https://pr.tn/ref/F2BW3G4P): Privacy-focused tools with encrypted email, VPN, password manager, and secure cloud services.
- [SimpleLogin](https://simplelogin.io/): Email alias service for privacy-first inbox protection.
- [Vaultwarden](https://github.com/dani-garcia/vaultwarden): Lightweight self-hosted password manager for privacy and control.
- [Pi-hole](https://github.com/pi-hole/pi-hole): Network-wide DNS blocking for ads, trackers, and malicious domains.
- [MikroTik](https://mikrotik.com/): Routing and network infrastructure platform for secure, high-performance edge and core deployments.
- [Fedora](https://fedoraproject.org/): Open-source Linux platform focused on security, privacy, and modern tooling.
- [LibreWolf](https://librewolf.net/): Privacy-enhanced browser with hardened defaults and reduced telemetry.


Homelab on Cloud
- GitHub (Repository)
- Terraform (IaC for CloudFlare and AWS)
- Amazon AWS (Cloud & Container Application)
  - EC2 Instances
    - Linux (Distro)
        - Debian 13
        - Wireguard peer to Homelab on premises
    - Docker
        - traefik (load balancer)
        - nginx (web)
        - gatus (availability monitoring)
        - beszel (system monitoring)
        - duin (automated container upgrade)
        - pi-hole (dns server)
        - portainer (container management)
        - vaultwarden (self-hosted password manager)
  - VPC / Security Group / Network ACL / Endpoint
  - S3 Storage
  - SES
  - IAM
  - SSM

- Cloudflare
  - Domain Management
  - WAF
  - Workers
  - CDN


Homelab on premises
- Beelink SER8 (Server Hardware)
    - Linux (Distro)
        - Debian 13
    - Docker
        - traefik (load balancer)
        - beszel agent (system monitoring)
        - duin (automated container upgrade)
        - portainer (container management)
        - streaming (jellyfin, bazarr, prowlarr, radarr, qbittorrent, gluetun)
        - pairdrop (local airdrop)

- MikroTik hAP ax3 (Network Hardware)
    - Routing
    - Firewall
    - DHCP
    - Access Point (EAP Security)
    - Wireguard peer to Homelab on AWS Cloud
    - Wireguard Protonvpn (private browsing)
