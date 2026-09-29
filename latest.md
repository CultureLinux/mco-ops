---
type: mco-daily
date: 2026-09-29
generated_at: 2026-09-29T08:26:00+02:00
status: critical
rocky_kernel_latest: 5.14.0-687.52.1.el9_8
rocky_security_advisory: RHSA-2026:71700
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
proxmox_kernel_production: 7.0.14-19-pve
gitlab_latest_security_release: 19.4.1
gitlab_supported_security_fixes: [19.4.1, 19.3.3, 19.2.7]
---

# MCO Daily — 29 septembre 2026

## Résumé exécutif

🔴 **GitLab reste prioritaire** : aucune nouvelle Critical Patch Release identifiée ce matin après celle du 23 septembre. Les versions corrigées restent `19.4.1`, `19.3.3` et `19.2.7`. Deux RCE CI/CD CVSS 9.9 permettent à un utilisateur authentifié à faibles privilèges d'exécuter du code sur le serveur via une expression régulière spécialement forgée.

🟠 **Rocky/RHEL 9** : la cible reste `5.14.0-687.52.1.el9_8`, publiée via **RHSA-2026:71700 (Important)** le 25 septembre avec 17 CVE corrigées. Pas de kernel RHEL/Rocky 9 plus récent pertinent identifié ce matin.

🟢 **Proxmox VE** : pas de nouvelle version de `pve-manager` au-delà de `9.2.20` dans le dépôt no-subscription consulté. Pour le parc, conserver le kernel `7.0.14-19-pve` comme cible production suivie et éviter de promouvoir un build de test sans validation.

---

# GitLab Self-Managed

## Critical Patch Release en vigueur

GitLab a publié le 23 septembre 2026 les versions corrigées :

```text
19.4.x -> 19.4.1
19.3.x -> 19.3.3
19.2.x -> 19.2.7
```

GitLab recommande la mise à niveau immédiate de toute installation affectée.

### Vulnérabilités prioritaires

| Criticité | CVE | Impact / vecteur | Prérequis | Versions affectées | Corrigé |
|---|---|---|---|---|---|
| 🔴 Critical 9.9 | CVE-2026-89078 | **RCE serveur**, double-free du parser regex via configuration CI/CD | réseau, auth requise, faibles privilèges, aucune interaction | 19.2 <19.2.7 ; 19.3 <19.3.3 ; 19.4 <19.4.1 | 19.2.7 / 19.3.3 / 19.4.1 |
| 🔴 Critical 9.9 | CVE-2026-93577 | **RCE serveur**, integer overflow du compilateur regex via configuration CI/CD | réseau, auth requise, faibles privilèges, aucune interaction | mêmes branches | mêmes versions |
| 🟠 High 8.7 | CVE-2026-84739 | XSS dans le diff viewer de Merge Request | attaquant authentifié, interaction de la victime | 13.11 à versions corrigées | mêmes versions |
| 🟠 High 7.7 | CVE-2026-92470 | **fuite de variables CI/CD** depuis des traces debug via Duo AI | GitLab EE, auth requise, aucune interaction | 18.7 à versions corrigées | mêmes versions |
| 🟡 Medium 5.4 | CVE-2026-92874 | dépassement du scope prévu d'un token MCP | auth + token MCP | 18.3 à versions corrigées | mêmes versions |
| 🟢 Low 3.7 | CVE-2026-4523 | **lecture sans authentification de traces CI/CD** pouvant contenir des variables sensibles | réseau, **aucune authentification**, complexité élevée | 15.11 à versions corrigées | mêmes versions |

### Signaux MCO

- **RCE :** CVE-2026-89078, CVE-2026-93577.
- **CI/CD :** RCE via configuration CI/CD et exposition possible de variables/traces.
- **Secrets/tokens :** CVE-2026-92470 et CVE-2026-92874.
- **Sans authentification :** CVE-2026-4523.
- **Auth bypass total critique :** aucun nouveau cas identifié dans la release suivie.

**Action :** toute instance encore en `19.3.2` doit passer au minimum en `19.3.3`. Prévoir la fenêtre adaptée : GitLab indique que cette patch release contient des migrations pouvant provoquer une indisponibilité sur une instance single-node.

---

# Rocky Linux / RHEL 9

## Kernel actuel

```text
5.14.0-687.52.1.el9_8
RHSA-2026:71700 — Important
Publication : 25 septembre 2026
```

L'avis Red Hat référence 17 CVE corrigées :

```text
CVE-2025-40323
CVE-2026-31539
CVE-2026-46199
CVE-2026-46204
CVE-2026-46230
CVE-2026-46311
CVE-2026-52912
CVE-2026-53203
CVE-2026-53290
CVE-2026-64098
CVE-2026-68108
CVE-2026-68121
CVE-2026-68257
CVE-2026-68266
CVE-2026-68267
CVE-2026-68273
CVE-2026-80714
```

### Point de vigilance

**CVE-2026-68121 / PPPoEject** est corrigée par ce build. Le suivi de **CVE-2026-74469 / DiagSpill** reste pertinent tant qu'un correctif RHEL 9 standard explicite n'est pas confirmé dans le flux suivi.

### Action Rocky

```bash
dnf check-update kernel
dnf update kernel
reboot
uname -r
```

**Cible MCO : `5.14.0-687.52.1.el9_8`.**

---

# Proxmox VE

## État observé

```text
Proxmox VE       9.2
pve-manager      9.2.20
kernel suivi     7.0.14-19-pve
```

Le dépôt PVE no-subscription consulté ne montre pas de `pve-manager` supérieur à `9.2.20`.

Un retour récent du forum Proxmox décrit des erreurs DMA/I/O avec certains workloads NVMe sous la famille 7.0.14 ; il s'agit d'un signal opérationnel à surveiller, pas d'un avis de sécurité officiel. Ne pas généraliser le contournement proposé sur le forum sans reproduire le problème.

### Action Proxmox

- conserver `7.0.14-19-pve` comme cible de production suivie ;
- ne pas forcer un kernel provenant d'un canal de test ;
- surveiller les prochains builds 7.0.14 et leur promotion dans les dépôts habituels ;
- sur les nœuds NVMe/IOMMU, surveiller les WARN `dma_iova_link` et erreurs I/O après mise à jour.

---

# Actions du jour

## 🔴 Urgent

- [ ] GitLab : passer toute instance vulnérable vers `19.4.1`, `19.3.3` ou `19.2.7` selon sa branche.

## 🟠 Haute priorité

- [ ] Rocky : vérifier que `5.14.0-687.52.1.el9_8` est installé **et actif après reboot**.
- [ ] Maintenir la surveillance de DiagSpill / CVE-2026-74469.

## 🟡 Surveillance

- [ ] Proxmox : conserver `7.0.14-19-pve` comme référence prod du parc.
- [ ] Surveiller les prochains kernels PVE et les éventuels correctifs DMA/IOMMU.

---

# Sources

- GitLab — Critical Patch Release 19.4.1, 19.3.3, 19.2.7  
  https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-1-released/

- Red Hat Security Data — RHSA-2026:71700  
  https://access.redhat.com/hydra/rest/securitydata/csaf

- Proxmox — pve-manager, dépôt no-subscription  
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

- Proxmox Support Forum — dma_iova_link / kernel 7.0.14  
  https://forum.proxmox.com/threads/warning-drivers-iommu-dma-iommu-c-1953-at-dma_iova_link.186448/
