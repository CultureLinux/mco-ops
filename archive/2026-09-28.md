---
type: mco-daily
date: 2026-09-28
generated_at: 2026-09-28T06:23:00+02:00
status: critical
rocky_kernel_latest: 5.14.0-687.52.1.el9_8
rocky_security_advisory: RHSA-2026:71700
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
proxmox_kernel_production: 7.0.14-19-pve
proxmox_kernel_test_seen: 7.0.14-20-pve
gitlab_latest_security_release: 19.4.1
gitlab_supported_security_fixes: [19.4.1, 19.3.3, 19.2.7]
---

# MCO Daily — 28 septembre 2026

## Résumé exécutif

🔴 **GitLab reste prioritaire** : aucune nouvelle Security/Critical Patch Release n'a remplacé `19.4.1`, `19.3.3` et `19.2.7`. Les deux RCE CI/CD CVSS 9.9 restent le motif d'upgrade immédiat.

🟠 **Rocky/RHEL 9 : nouveau kernel `5.14.0-687.52.1.el9_8`** via **RHSA-2026:71700 (Important)**. Ce build corrige notamment **PPPoEject / CVE-2026-68121**. Après DirtyAH6 et TUNderflow corrigées par `687.51.1`, il ne reste plus que **DiagSpill / CVE-2026-74469** non corrigée explicitement dans le flux RHEL 9 standard consulté.

🟢 **Proxmox production reste stable** : `pve-manager 9.2.20` et `7.0.14-19-pve`. Le build `7.0.14-20` reste cantonné au canal de test observé.

---

# GitLab Self-Managed

## Critical Patch Release toujours en vigueur

Versions corrigées :

```text
19.4.x -> 19.4.1
19.3.x -> 19.3.3
19.2.x -> 19.2.7
```

### Vulnérabilités prioritaires

| Criticité | CVE | Impact | Prérequis | Corrigé |
|---|---|---|---|---|
| 🔴 9.9 | CVE-2026-89078 | RCE serveur via parser regex | auth requise, faibles privilèges, CI/CD regex, aucune interaction | 19.2.7 / 19.3.3 / 19.4.1 |
| 🔴 9.9 | CVE-2026-93577 | RCE serveur via integer overflow regex | auth requise, faibles privilèges, CI/CD regex, aucune interaction | 19.2.7 / 19.3.3 / 19.4.1 |
| 🟠 8.7 | CVE-2026-84739 | XSS diff Merge Request | attaquant authentifié + interaction victime | mêmes versions |
| 🟠 7.7 | CVE-2026-92470 | fuite de variables CI/CD via Duo AI | EE, auth requise, traces debug | mêmes versions |

### Signaux à retenir

- **RCE :** CVE-2026-89078, CVE-2026-93577
- **CI/CD :** CVE-2026-89078, CVE-2026-93577, CVE-2026-92470, CVE-2026-4523
- **Secrets/tokens :** CVE-2026-92470, CVE-2026-4523, CVE-2026-92874
- **Sans authentification :** CVE-2026-4523
- **Auth bypass total :** aucun nouveau cas critique identifié dans la release suivie

**Action MCO :** toute instance encore en `19.3.2` doit passer au minimum en `19.3.3`.

---

# Rocky Linux / RHEL 9

## Nouveau kernel

```text
5.14.0-687.52.1.el9_8
```

Le paquet est présent dans Rocky BaseOS x86_64 depuis le **25 septembre 2026**.

Il correspond à **RHSA-2026:71700**, classé **Important**, avec 17 CVE corrigées :

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

## CVE prioritaire nouvellement corrigée

### 🟠 CVE-2026-68121 — PPPoEject

- **Criticité Red Hat :** Important
- **CVSS :** 7.3
- **Impact :** use-after-free / corruption mémoire ; crash/DoS et comportement indéfini, avec exécution de code potentielle selon exploitation
- **Vecteur :** local
- **Privilèges :** faibles
- **Interaction utilisateur :** aucune
- **Fonction concernée :** PPPoE
- **Correctif RHEL 9 standard :** `RHSA-2026:71700` / kernel `5.14.0-687.52.1.el9_8`

## État RHSB-2026-011

| CVE | État RHEL 9 standard observé |
|---|---|
| CVE-2026-80844 — DirtyAH6 | ✅ corrigée par `687.51.1` |
| CVE-2026-81000 — TUNderflow | ✅ corrigée par `687.51.1` |
| CVE-2026-68121 — PPPoEject | ✅ corrigée par `687.52.1` |
| CVE-2026-74469 — DiagSpill | ⚠️ toujours à surveiller |

### DiagSpill reste le point résiduel

- **CVE :** CVE-2026-74469
- **Criticité Red Hat :** Important
- **CVSS :** 8.3
- **Sous-système :** SCTP / `sctp_diag`
- **Impact :** out-of-bounds write, corruption mémoire, DoS ; potentiel d'exécution de code selon contexte
- **Particularité :** le bulletin Red Hat indique que le scénario local ne dépend pas des unprivileged user namespaces

### Action MCO Rocky

```bash
dnf check-update kernel
dnf update kernel
reboot
uname -r
```

**Cible actuelle : `5.14.0-687.52.1.el9_8`.**

Après activation de ce kernel, maintenir la surveillance de DiagSpill jusqu'à publication d'un correctif RHEL 9 standard explicite.

---

# Proxmox VE

## État production observé

```text
Proxmox VE       9.2
pve-manager      9.2.20
kernel prod      7.0.14-19-pve
```

Aucun `pve-manager` supérieur à `9.2.20` n'est visible dans le dépôt PVE no-subscription consulté.

Le kernel `7.0.14-20` reste observé dans un canal de test, mais pas encore comme cible de production dans les métadonnées PVE no-subscription/enterprise consultées.

### Action MCO Proxmox

- conserver `7.0.14-19-pve` comme cible production actuelle ;
- ne pas forcer `7.0.14-20` depuis un dépôt de test ;
- surveiller sa promotion vers les dépôts PVE habituels.

---

# Actions du jour

## 🔴 Urgent

- [ ] Mettre à jour GitLab vers `19.4.1`, `19.3.3` ou `19.2.7` selon la branche.

## 🟠 Haute priorité

- [ ] Déployer Rocky `5.14.0-687.52.1.el9_8`.
- [ ] Rebooter afin d'activer le nouveau kernel.
- [ ] Considérer PPPoEject comme corrigée après activation de `687.52.1`.
- [ ] Maintenir la surveillance de DiagSpill.

## 🟡 Normale

- [ ] Maintenir Proxmox sur `7.0.14-19-pve` en cible prod.
- [ ] Surveiller la promotion de `7.0.14-20` hors canal de test.

---

# Sources

- GitLab — Critical Patch Release 19.4.1, 19.3.3, 19.2.7
  https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-1-released/

- Red Hat — RHSA-2026:71700 / Security Data
  https://access.redhat.com/hydra/rest/securitydata/csaf

- Red Hat — RHSB-2026-011
  https://access.redhat.com/security/vulnerabilities/RHSB-2026-011

- Red Hat — CVE-2026-68121
  https://access.redhat.com/security/cve/cve-2026-68121

- Red Hat — CVE-2026-74469
  https://access.redhat.com/security/cve/cve-2026-74469

- Rocky Linux 9 BaseOS x86_64
  https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

- Proxmox — pve-manager
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

- Proxmox — kernel PVE enterprise
  https://metadata.cdn.proxmox.com/enterprise/changelogs/pve/dists/trixie/pve-enterprise/p/proxmox-kernel-signed-7.0/