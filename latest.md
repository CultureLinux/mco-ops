---
type: mco-daily
date: 2026-09-22
generated_at: 2026-09-22T08:31:00+02:00
status: warning
rocky_kernel_latest: 5.14.0-687.49.1.el9_8
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
critical_cve: 0
important_cve: 4
---

# MCO Daily — 22 septembre 2026

## Résumé exécutif

🟠 **Vigilance Rocky/RHEL 9.** Le point prioritaire reste le bulletin Red Hat **RHSB-2026-011** : quatre vulnérabilités kernel réseau classées **Important**, avec élévation locale jusqu'à root. Red Hat indique toujours le bulletin comme **Ongoing** et accélère la publication des correctifs.

Le dernier kernel Rocky 9 BaseOS x86_64 observé reste :

```text
5.14.0-687.49.1.el9_8
```

Il est daté du **18 septembre 2026**. Ne pas considérer ce kernel comme corrigeant automatiquement RHSB-2026-011 tant que l'advisory correspondant n'est pas explicitement publié/confirmé.

Côté Proxmox VE : pas d'alerte MCO significative nouvelle détectée ce matin. Proxmox VE **9.2** reste la version majeure courante et `pve-manager 9.2.20` la dernière version observée dans le dépôt no-subscription consulté.

---

## Rocky Linux / RHEL 9

### Kernel disponible

| Élément | Valeur |
|---|---|
| Dernier kernel Rocky 9 x86_64 observé | `5.14.0-687.49.1.el9_8` |
| Date dépôt | 2026-09-18 |
| Kernel précédent | `5.14.0-687.48.1.el9_8` |
| Action | Vérifier le parc avec `dnf check-update kernel` |

Commandes utiles :

```bash
uname -r
dnf check-update kernel
dnf repoquery --latest-limit=3 kernel
```

---

## Vulnérabilités kernel prioritaires

Bulletin Red Hat : **RHSB-2026-011 — Network Stack Privilege Escalations**.

| Criticité | CVE | Nom | Impact | Prérequis principaux | Correctif |
|---|---|---|---|---|---|
| 🟠 Important | CVE-2026-74469 | DiagSpill | Local → root ; DoS distant possible dans certaines configurations | SCTP + `sctp_diag`; pas besoin de user namespace non privilégié | En cours de publication |
| 🟠 Important | CVE-2026-80844 | DirtyAH6 | Corruption mémoire, local → root | AH6/XFRM + unprivileged user namespaces | En cours de publication |
| 🟠 Important | CVE-2026-81000 | TUNderflow | Heap overflow, local → root | TUN/TAP + unprivileged user namespaces | En cours de publication |
| 🟠 Important | CVE-2026-68121 | PPPoEject | Use-after-free, local → root | PPPoE + unprivileged user namespaces | En cours de publication |

### CVE-2026-74469 — DiagSpill

C'est la plus préoccupante du lot pour un parc serveur :

- sous-système SCTP diagnostics ;
- élévation locale jusqu'à root ;
- pas besoin des unprivileged user namespaces ;
- DoS distant possible dans certaines configurations SCTP avec ASCONF/ADD-IP.

Vérification :

```bash
lsmod | egrep 'sctp|sctp_diag'
```

Si SCTP n'est pas utilisé, envisager le blocage du module après validation fonctionnelle.

### CVE-2026-80844 — DirtyAH6

- sous-système IPv6 AH6/XFRM ;
- corruption mémoire kernel ;
- élévation locale jusqu'à root ;
- nécessite les unprivileged user namespaces pour le scénario local décrit par Red Hat.

### CVE-2026-81000 — TUNderflow

- pilote TUN/TAP ;
- integer underflow / heap overflow ;
- élévation locale jusqu'à root ;
- nécessite les unprivileged user namespaces.

TUN/TAP est fréquemment utilisé par les VPN, conteneurs et configurations de virtualisation : ne pas désactiver le module sans vérifier les workloads.

### CVE-2026-68121 — PPPoEject

- sous-système PPPoE ;
- use-after-free ;
- élévation locale jusqu'à root ;
- nécessite les unprivileged user namespaces.

---

## Mitigation temporaire

Red Hat indique que la désactivation des unprivileged user namespaces bloque les scénarios locaux décrits pour DirtyAH6, TUNderflow et PPPoEject.

Cela **ne protège pas contre DiagSpill**.

Vérification :

```bash
sysctl user.max_user_namespaces
```

Mitigation possible après validation des workloads :

```bash
sysctl -w user.max_user_namespaces=0
```

Attention : Podman et d'autres runtimes/sandboxes peuvent en dépendre.

---

## Proxmox VE

### État actuel

| Élément | Valeur |
|---|---|
| Version majeure courante | Proxmox VE 9.2 |
| Base | Debian 13.5 Trixie |
| Kernel stable par défaut 9.2 | Linux 7.0 |
| Dernier `pve-manager` observé | `9.2.20` |

Aucune nouvelle release majeure ou alerte urgente Proxmox VE n'a été identifiée ce matin.

Contrôle conseillé :

```bash
pveversion -v
apt update
apt list --upgradable
```

---

## Actions du jour

### Priorité haute

- [ ] Vérifier la présence de SCTP / `sctp_diag` sur les Rocky/RHEL 9.
- [ ] Surveiller la publication du kernel/advisory corrigeant RHSB-2026-011.
- [ ] Identifier les serveurs multi-utilisateurs ou utilisant les unprivileged user namespaces.

### Priorité normale

- [ ] Vérifier quels serveurs Rocky sont encore sous `5.14.0-687.48.1.el9_8` ou antérieur.
- [ ] Vérifier les mises à jour kernel disponibles.
- [ ] Vérifier les versions Proxmox avec `pveversion -v`.

### Proxmox

🟢 Pas d'intervention urgente identifiée aujourd'hui.

---

## Sources

- Red Hat — RHSB-2026-011  
  https://access.redhat.com/security/vulnerabilities/RHSB-2026-011

- Rocky Linux 9 BaseOS x86_64 — packages kernel  
  https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

- Proxmox VE 9.2 — annonce officielle  
  https://www.proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2

- Proxmox — changelogs pve-manager  
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

---

> Ce rapport privilégie les informations ayant un impact opérationnel sur un parc Rocky Linux / Proxmox. Les vulnérabilités faibles ou sans impact plausible peuvent être omises afin de réduire le bruit.
