---
type: mco-daily
date: 2026-09-21
generated_at: 2026-09-21T14:40:00+02:00
status: warning
rocky_kernel_latest: 5.14.0-687.49.1.el9_8
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
critical_cve: 0
important_cve: 4
---

# MCO Daily — 21 septembre 2026

## Résumé exécutif

🟠 **Vigilance kernel Rocky/RHEL 9.**

Red Hat suit actuellement quatre vulnérabilités d'élévation locale de privilèges dans la pile réseau du kernel Linux, toutes classées **Important**. Le bulletin est encore en statut **Ongoing** et Red Hat indique accélérer la publication des correctifs.

Le dernier kernel actuellement visible dans le dépôt Rocky Linux 9 BaseOS x86_64 est :

```text
5.14.0-687.49.1.el9_8
```

Il a été publié dans le dépôt Rocky le **18 septembre 2026**.

⚠️ Ne pas considérer automatiquement ce kernel comme corrigeant les quatre CVE ci-dessous tant que l'advisory correspondant n'est pas publié/confirmé.

Côté Proxmox VE : pas de nouvelle version majeure détectée aujourd'hui. **Proxmox VE 9.2** reste la version courante et `pve-manager 9.2.20` est la dernière version observée dans le dépôt no-subscription consulté.

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

### Vue MCO

| Criticité | CVE | Nom | Impact | Prérequis principaux | Statut |
|---|---|---|---|---|---|
| 🟠 Important | CVE-2026-74469 | DiagSpill | Local → root ; DoS distant possible dans certaines configurations | SCTP + `sctp_diag`; pas besoin de user namespace non privilégié | Correctifs en cours de publication |
| 🟠 Important | CVE-2026-80844 | DirtyAH6 | Local → root | AH6/XFRM + unprivileged user namespaces | Correctifs en cours de publication |
| 🟠 Important | CVE-2026-81000 | TUNderflow | Local → root | TUN/TAP + unprivileged user namespaces | Correctifs en cours de publication |
| 🟠 Important | CVE-2026-68121 | PPPoEject | Local → root | PPPoE + unprivileged user namespaces | Correctifs en cours de publication |

### CVE-2026-74469 — DiagSpill

La plus préoccupante du lot pour un parc serveur.

- sous-système : SCTP diagnostics ;
- élévation locale de privilèges jusqu'à root ;
- ne nécessite pas les unprivileged user namespaces ;
- un déni de service distant est possible dans certaines configurations SCTP avec ASCONF/ADD-IP.

**Action MCO :**

```bash
lsmod | egrep 'sctp|sctp_diag'
```

Si SCTP n'est pas utilisé, envisager le blocage du module après validation fonctionnelle.

### CVE-2026-80844 — DirtyAH6

- sous-système IPv6 AH6/XFRM ;
- corruption mémoire kernel ;
- élévation locale vers root ;
- nécessite les unprivileged user namespaces pour le scénario local décrit par Red Hat.

### CVE-2026-81000 — TUNderflow

- pilote TUN/TAP ;
- integer underflow / heap overflow ;
- élévation locale vers root ;
- nécessite les unprivileged user namespaces.

Attention : TUN/TAP est couramment utilisé par les VPN, conteneurs et certaines configurations réseau de virtualisation. Ne pas désactiver le module sans vérifier les workloads.

### CVE-2026-68121 — PPPoEject

- sous-système PPPoE ;
- use-after-free ;
- élévation locale vers root ;
- nécessite les unprivileged user namespaces.

PPPoE est généralement peu utilisé sur des serveurs d'entreprise, mais doit être vérifié avant blocage.

---

## Mitigation temporaire

Red Hat indique que la désactivation des unprivileged user namespaces bloque les scénarios locaux décrits pour DirtyAH6, TUNderflow et PPPoEject.

Cela **ne protège pas contre DiagSpill**.

Vérification :

```bash
sysctl user.max_user_namespaces
```

Une mitigation possible, uniquement après validation de l'impact sur les conteneurs et autres workloads :

```bash
sysctl -w user.max_user_namespaces=0
```

Ne pas appliquer globalement sans test : Podman, certains runtimes de conteneurs et outils de sandboxing peuvent en dépendre.

---

## Proxmox VE

### État actuel

| Élément | Valeur |
|---|---|
| Version majeure courante | Proxmox VE 9.2 |
| Base | Debian 13.5 Trixie |
| Kernel stable par défaut 9.2 | Linux 7.0 |
| QEMU | 11.0 |
| LXC | 7.0 |
| ZFS | 2.4 |
| Dernier `pve-manager` observé | `9.2.20` |

La version 9.2 introduit notamment :

- Dynamic Load Balancer ;
- workflow HA Arm/Disarm ;
- gestion des modèles CPU personnalisés dans l'interface ;
- WireGuard comme protocole SDN Fabric ;
- filtres BGP/EVPN via route maps et prefix lists ;
- Ceph Tentacle 20.2.

Aucune nouvelle release majeure Proxmox VE n'a été identifiée aujourd'hui.

Pour vérifier un nœud :

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
- [ ] Identifier les serveurs multi-utilisateurs, exposés ou exécutant des workloads permettant des namespaces non privilégiés.

### Priorité normale

- [ ] Vérifier quels serveurs Rocky sont encore sous `5.14.0-687.48.1.el9_8` ou antérieur.
- [ ] Vérifier les mises à jour disponibles via `dnf check-update kernel`.
- [ ] Vérifier les versions Proxmox avec `pveversion -v`.

### Proxmox

🟢 Pas d'intervention urgente identifiée aujourd'hui sur la base des publications consultées.

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

> Ce rapport privilégie les informations ayant un impact opérationnel sur un parc Rocky Linux / Proxmox. Les vulnérabilités de faible criticité ou sans impact plausible pour ce périmètre peuvent être omises afin de réduire le bruit.
