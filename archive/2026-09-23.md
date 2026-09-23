---
type: mco-daily
date: 2026-09-23
status: warning
rocky_kernel_latest: 5.14.0-687.49.1.el9_8
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
critical_cve: 0
important_cve: 4
---

# MCO Daily — 23 septembre 2026

## Résumé exécutif

🟠 **Pas de nouveau kernel Rocky ni de nouvelle version Proxmox significative depuis hier.** Le point MCO prioritaire reste **RHSB-2026-011** : quatre vulnérabilités réseau kernel classées Important par Red Hat. Le bulletin est toujours **Ongoing** et les correctifs sont annoncés comme en cours de publication.

## Rocky Linux / RHEL 9

Le dernier kernel Rocky Linux 9 BaseOS x86_64 observé reste :

```text
5.14.0-687.49.1.el9_8
```

Date du paquet : **18 septembre 2026**. Aucun kernel plus récent n'est visible ce matin dans le dépôt officiel Rocky 9 BaseOS x86_64.

Ne pas considérer `687.49.1` comme corrigeant automatiquement RHSB-2026-011 : Red Hat demande toujours de passer à un kernel corrigé dès qu'il sera disponible.

## Vulnérabilités kernel prioritaires

| Criticité | CVE | Impact / vecteur | Prérequis | Correctif | Action MCO |
|---|---|---|---|---|---|
| 🟠 Important | CVE-2026-74469 — DiagSpill | Corruption mémoire ; élévation locale ; DoS distant possible avec certaines configurations SCTP | `sctp` + `sctp_diag`; pas de user namespace requis | En cours de publication | Priorité haute : inventorier SCTP et bloquer les modules s'ils sont inutiles |
| 🟠 Important | CVE-2026-80844 — DirtyAH6 | OOB mémoire AH6/XFRM ; local → root ; scénario distant plus difficile sur certains routeurs/passerelles IPv6 AH | AH6/XFRM + user namespaces pour le scénario local | En cours de publication | Vérifier usage IPv6 IPsec/AH ; limiter user namespaces si compatible |
| 🟠 Important | CVE-2026-81000 — TUNderflow | Heap overflow TUN/TAP ; local → root | TUN/TAP + user namespaces | En cours de publication | Ne pas bloquer `tun` sans vérifier VPN/conteneurs/virtualisation |
| 🟠 Important | CVE-2026-68121 — PPPoEject | Use-after-free PPPoE ; local → root | PPPoE + user namespaces | En cours de publication | Bloquer PPPoE uniquement s'il est inutilisé |

### Mitigation temporaire

Red Hat indique que désactiver les unprivileged user namespaces bloque l'exploitation locale de DirtyAH6, TUNderflow et PPPoEject, mais **pas DiagSpill**.

```bash
sysctl user.max_user_namespaces
lsmod | egrep '^(sctp|sctp_diag|ah6|tun|pppoe)\b'
```

Une désactivation globale des user namespaces doit être testée avant déploiement : Podman et d'autres workloads peuvent en dépendre.

## Proxmox VE

🟢 **Pas de nouveauté nécessitant une action MCO ce matin.** Proxmox VE `9.2` reste la version majeure courante et `pve-manager 9.2.20` reste la dernière version visible dans le dépôt no-subscription consulté. Aucun nouveau changelog `pve-manager` postérieur au 14 septembre n'est visible.

Contrôle standard :

```bash
pveversion -v
apt update
apt list --upgradable
```

## Actions du jour

- [ ] Priorité haute : vérifier `sctp` / `sctp_diag` sur les Rocky/RHEL 9 et bloquer ces modules s'ils sont inutiles.
- [ ] Surveiller la sortie du kernel/advisory corrigeant explicitement RHSB-2026-011.
- [ ] Vérifier les hôtes utilisant les unprivileged user namespaces avant toute mitigation.
- [ ] Pas d'action Proxmox urgente identifiée.

## Sources

- Red Hat — RHSB-2026-011 : https://access.redhat.com/security/vulnerabilities/RHSB-2026-011
- Red Hat — CVE-2026-74469 : https://access.redhat.com/security/cve/cve-2026-74469
- Red Hat — CVE-2026-80844 : https://access.redhat.com/security/cve/cve-2026-80844
- Rocky Linux 9 BaseOS x86_64 : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- Proxmox — changelogs pve-manager : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

> Rapport volontairement concis : aucun nouveau correctif kernel Rocky ni changement Proxmox majeur n'a été identifié depuis le brief précédent.
