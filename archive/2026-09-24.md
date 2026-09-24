---
type: mco-daily
date: 2026-09-24
generated_at: 2026-09-24T08:02:00+02:00
status: warning
rocky_kernel_latest: 5.14.0-687.49.1.el9_8
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
proxmox_kernel_latest: 7.0.14-19-pve
critical_cve: 0
important_cve: 4
---

# MCO Daily — 24 septembre 2026

## Résumé exécutif

🟠 **Pas de nouvelle release significative depuis hier.** Le risque prioritaire reste RHSB-2026-011 : quatre failles réseau kernel classées **Important** par Red Hat, avec correctifs toujours en cours de publication. Rocky 9 reste sur `5.14.0-687.49.1.el9_8`. Côté Proxmox, `7.0.14-19-pve` reste le kernel à cibler et `pve-manager 9.2.20` la dernière version observée.

## Rocky Linux / RHEL 9

Dernier kernel Rocky Linux 9 BaseOS x86_64 observé :

```text
5.14.0-687.49.1.el9_8
```

Paquet daté du **18 septembre 2026**. Aucun kernel Rocky 9 plus récent n'est visible ce matin dans le dépôt officiel BaseOS x86_64.

Le bulletin **RHSB-2026-011**, publié le 19 septembre et mis à jour le 21 septembre, reste en statut **Ongoing**. Red Hat indique accélérer la publication des correctifs.

## CVE kernel prioritaires

| Criticité | CVE | Impact / vecteur | Prérequis | Correctif | Action MCO |
|---|---|---|---|---|---|
| 🟠 Important | CVE-2026-74469 — DiagSpill | Corruption mémoire SCTP ; local → root ; DoS distant possible | SCTP + `sctp_diag`; pas de user namespace requis | En cours de publication | Inventorier SCTP en priorité ; désactiver si inutilisé |
| 🟠 Important | CVE-2026-80844 — DirtyAH6 | OOB mémoire AH6/XFRM ; local → root | AH6/XFRM + unprivileged user namespaces | En cours de publication | Vérifier IPv6 AH/XFRM ; limiter les namespaces si compatible |
| 🟠 Important | CVE-2026-81000 — TUNderflow | Heap overflow TUN/TAP ; local → root | TUN/TAP + unprivileged user namespaces | En cours de publication | Conserver `tun` si VPN/conteneurs/virtualisation en dépendent |
| 🟠 Important | CVE-2026-68121 — PPPoEject | Use-after-free PPPoE ; local → root | PPPoE + unprivileged user namespaces | En cours de publication | Désactiver PPPoE uniquement si inutilisé |

**DiagSpill reste la priorité** : son exploitation locale ne nécessite pas les unprivileged user namespaces, contrairement aux trois autres failles du bulletin.

Contrôle :

```bash
uname -r
dnf check-update kernel
lsmod | egrep '^(sctp|sctp_diag|ah6|tun|pppoe)\b'
sysctl user.max_user_namespaces
```

Ne pas considérer `5.14.0-687.49.1.el9_8` comme le correctif de RHSB-2026-011 tant qu'un advisory Red Hat/Rocky ne l'atteste pas explicitement.

## Proxmox VE

Pas de nouvelle version `pve-manager` significative depuis le brief précédent : la dernière observée reste :

```text
pve-manager 9.2.20
```

Le dernier kernel Proxmox 7.0.14 observé reste :

```text
proxmox-kernel-7.0.14-19-pve
```

Le changelog signé `7.0.14+19` est publié dans les métadonnées officielles Proxmox depuis le **21 septembre 2026**. Aucun build plus récent n'est visible ce matin dans la source officielle consultée.

Je n'attribue pas de CVE précise au build `-19` sans correspondance suffisamment explicite dans les sources officielles.

Contrôle des nœuds :

```bash
uname -r
pveversion -v
apt update
apt list --upgradable 2>/dev/null | egrep 'proxmox-kernel|pve-manager'
```

Un reboot est nécessaire après installation d'un nouveau kernel pour réellement l'activer.

## Actions du jour

### Priorité haute

- [ ] Vérifier `sctp` / `sctp_diag` sur les Rocky/RHEL 9 et désactiver ces modules lorsqu'ils sont inutiles.
- [ ] Continuer à surveiller la publication du kernel/advisory corrigeant explicitement RHSB-2026-011.

### Priorité normale

- [ ] Vérifier les Rocky sous un kernel antérieur à `5.14.0-687.49.1.el9_8`.
- [ ] Vérifier les Proxmox encore sous `7.0.14-18-pve` ou antérieur et planifier `7.0.14-19-pve` avec reboot.
- [ ] Pas d'autre action Proxmox urgente identifiée aujourd'hui.

## Sources

- Red Hat — RHSB-2026-011 : https://access.redhat.com/security/vulnerabilities/RHSB-2026-011
- Red Hat — CVE-2026-74469 : https://access.redhat.com/security/cve/cve-2026-74469
- Red Hat — CVE-2026-80844 : https://access.redhat.com/security/cve/cve-2026-80844
- Rocky Linux 9 BaseOS x86_64 : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- Proxmox — pve-manager : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- Proxmox — kernel 7.0 signé : https://metadata.cdn.proxmox.com/enterprise/changelogs/pve/dists/trixie/pve-enterprise/p/proxmox-kernel-signed-7.0/

> Rapport volontairement concis : aucune nouvelle release Rocky/Proxmox ni nouvelle vulnérabilité prioritaire n'a été identifiée depuis le brief précédent.