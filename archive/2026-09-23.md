---
type: mco-daily
date: 2026-09-23
generated_at: 2026-09-23T08:02:00+02:00
status: warning

rocky_kernel_latest: 5.14.0-687.49.1.el9_8

proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
proxmox_kernel_latest: 7.0.14-19-pve

critical_cve: 0
important_cve: 4
---

# MCO Daily — 23 septembre 2026

## Résumé exécutif

🟠 **Vigilance kernel côté Rocky/RHEL ; nouveau kernel Proxmox à intégrer au suivi.**

- Rocky Linux 9 : dernier kernel observé `5.14.0-687.49.1.el9_8`, publié le 18 septembre 2026.
- Red Hat : le bulletin **RHSB-2026-011** reste en statut **Ongoing** avec quatre vulnérabilités réseau kernel classées **Important**.
- Proxmox VE : `pve-manager 9.2.20` reste la dernière version observée.
- Kernel Proxmox : `proxmox-kernel-7.0.14-19-pve` est désormais le dernier kernel 7.0.14 observé dans les dépôts consultés.

---

## Rocky Linux / RHEL 9

### Kernel disponible

Dernier kernel Rocky Linux 9 BaseOS x86_64 observé :

```text
5.14.0-687.49.1.el9_8
```

Date du paquet : **18 septembre 2026**.

Aucun kernel plus récent n'est visible ce matin dans le dépôt officiel Rocky Linux 9 BaseOS x86_64.

Commandes de contrôle :

```bash
uname -r
dnf check-update kernel
dnf repoquery --latest-limit=3 kernel
```

⚠️ Ne pas considérer automatiquement `5.14.0-687.49.1.el9_8` comme corrigeant les quatre CVE de RHSB-2026-011 : le bulletin Red Hat est toujours indiqué comme **Ongoing** et Red Hat recommande de passer à un kernel corrigé dès sa publication.

---

## Vulnérabilités kernel prioritaires Rocky/RHEL

Bulletin suivi : **RHSB-2026-011 — Network Stack Privilege Escalations**.

| Criticité | CVE | Impact / vecteur | Prérequis principaux | Correctif | Action MCO |
|---|---|---|---|---|---|
| 🟠 Important | CVE-2026-74469 — DiagSpill | Élévation locale vers root ; DoS distant possible dans certaines configurations | SCTP + `sctp_diag`; pas besoin de user namespace non privilégié | En cours de publication | Priorité haute : inventorier SCTP et désactiver si inutile |
| 🟠 Important | CVE-2026-80844 — DirtyAH6 | Corruption mémoire AH6/XFRM ; élévation locale vers root | AH6/XFRM + unprivileged user namespaces pour le scénario local décrit | En cours de publication | Vérifier les usages IPsec/AH ; réduire l'exposition des namespaces si possible |
| 🟠 Important | CVE-2026-81000 — TUNderflow | Heap overflow dans TUN/TAP ; élévation locale vers root | TUN/TAP + unprivileged user namespaces | En cours de publication | Ne pas désactiver `tun` sans vérifier VPN/conteneurs/virtualisation |
| 🟠 Important | CVE-2026-68121 — PPPoEject | Use-after-free PPPoE ; élévation locale vers root | PPPoE + unprivileged user namespaces | En cours de publication | Désactiver PPPoE seulement si réellement inutilisé |

### Point le plus sensible

**CVE-2026-74469 / DiagSpill** reste la vulnérabilité la plus préoccupante du lot pour un serveur classique car le scénario local décrit par Red Hat ne dépend pas des unprivileged user namespaces.

Inventaire recommandé :

```bash
lsmod | egrep '^(sctp|sctp_diag|ah6|tun|pppoe)\b'
sysctl user.max_user_namespaces
```

La restriction des unprivileged user namespaces réduit l'exposition aux trois autres vulnérabilités, mais **ne mitige pas DiagSpill**.

---

## Proxmox VE

### Packages / fonctionnalités

État observé :

| Élément | Version |
|---|---|
| Proxmox VE | 9.2 |
| `pve-manager` | `9.2.20` |
| Kernel Proxmox | `7.0.14-19-pve` |

Aucun changelog `pve-manager` plus récent que `9.2.20` n'est visible dans le dépôt no-subscription consulté ce matin.

### Kernel Proxmox spécifique

Le dernier kernel Proxmox 7.0.14 observé est :

```text
proxmox-kernel-7.0.14-19-pve
```

La version signée correspondante est également publiée :

```text
proxmox-kernel-7.0.14-19-pve-signed
```

Les paquets sont datés du **18 septembre 2026** sur les miroirs consultés et leurs changelogs sont présents dans les métadonnées officielles Proxmox depuis le **21 septembre**.

Le suivi MCO doit donc désormais comparer les nœuds à cette version :

```bash
uname -r
pveversion -v
apt update
apt list --upgradable 2>/dev/null | egrep 'proxmox-kernel|pve-manager'
```

### Action MCO Proxmox

🟡 **Action normale :** vérifier quels nœuds sont encore sur `7.0.14-17-pve`, `7.0.14-18-pve` ou antérieur et planifier le passage vers `7.0.14-19-pve` selon votre fenêtre de maintenance.

Un reboot est nécessaire pour démarrer effectivement sur le nouveau kernel après installation.

Je ne classe pas aujourd'hui de CVE précise comme « corrigée par -19 » tant que le lien entre ce build Proxmox et ces CVE n'est pas suffisamment attesté par les sources consultées. Le rapport privilégie la certitude plutôt qu'une attribution spéculative.

---

## Actions du jour

### Priorité haute

- [ ] Vérifier la présence de `sctp` / `sctp_diag` sur les Rocky/RHEL 9.
- [ ] Surveiller l'arrivée de l'advisory/kernel Red Hat corrigeant explicitement RHSB-2026-011.
- [ ] Identifier les hôtes Rocky utilisant des unprivileged user namespaces.

### Priorité normale

- [ ] Vérifier les Rocky encore sous un kernel antérieur à `5.14.0-687.49.1.el9_8`.
- [ ] Vérifier les nœuds Proxmox encore sous `7.0.14-18-pve` ou antérieur.
- [ ] Installer `7.0.14-19-pve` lors de la prochaine fenêtre adaptée, puis redémarrer le nœud pour l'activer.
- [ ] Aucun changement `pve-manager` urgent identifié.

---

## Sources

- Red Hat — RHSB-2026-011
  https://access.redhat.com/security/vulnerabilities/RHSB-2026-011

- Rocky Linux 9 BaseOS x86_64 — packages kernel
  https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

- Proxmox — changelogs pve-manager
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

- Proxmox — changelogs kernel 7.0 / packages signés
  https://metadata.cdn.proxmox.com/enterprise/changelogs/pve/dists/trixie/pve-enterprise/p/proxmox-kernel-signed-7.0/

> Rapport orienté exploitation : seules les informations pouvant entraîner une action MCO ou modifier le niveau de risque sont mises en avant.