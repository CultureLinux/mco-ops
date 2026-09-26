---
type: mco-daily
date: 2026-09-26
generated_at: 2026-09-26T14:43:00+02:00
status: critical

rocky_kernel_latest: 5.14.0-687.51.1.el9_8
rocky_security_advisory: RHSA-2026:71232

proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
proxmox_kernel_production: 7.0.14-19-pve
proxmox_kernel_test_seen: 7.0.14-20-pve

gitlab_latest_security_release: 19.4.1
gitlab_supported_security_fixes: [19.4.1, 19.3.3, 19.2.7]

critical_cve: 2
high_cve: 2
important_kernel_advisory: true
---

# MCO Daily — 26 septembre 2026

## Résumé exécutif

🔴 **GitLab reste prioritaire** : aucune nouvelle Security/Critical Patch Release n'a remplacé les versions `19.4.1`, `19.3.3` et `19.2.7`. Les deux RCE CI/CD CVSS 9.9 restent le motif d'upgrade immédiat.

🟠 **Rocky/RHEL 9 : nouveau kernel `5.14.0-687.51.1.el9_8`**. Il correspond à **RHSA-2026:71232 (Important)** et corrige notamment **DirtyAH6 (CVE-2026-80844)** et **TUNderflow (CVE-2026-81000)**. En revanche, **DiagSpill (CVE-2026-74469)** et **PPPoEject (CVE-2026-68121)** ne figurent pas dans cet advisory et RHSB-2026-011 reste Ongoing.

🟢 **Proxmox production reste sur `pve-manager 9.2.20` / kernel `7.0.14-19-pve`**. Un build `7.0.14-20` est visible en dépôt de test PDM, mais pas encore dans le canal PVE enterprise/no-subscription observé : ne pas le traiter comme cible de production.

---

# GitLab Self-Managed

## Critical Patch Release toujours en vigueur

Versions corrigées :

```text
19.4.x -> 19.4.1
19.3.x -> 19.3.3
19.2.x -> 19.2.7
```

GitLab recommande toujours la mise à jour immédiate des installations Self-Managed affectées.

### 🔴 Critique — CVE-2026-89078

- **CVSS :** 9.9
- **Type :** double free dans le parseur d'expressions régulières
- **Impact :** RCE sur le serveur GitLab
- **Vecteur :** réseau
- **Authentification :** requise
- **Privilèges :** faibles privilèges suffisants
- **Interaction utilisateur :** aucune
- **Fonction concernée :** configuration CI/CD avec expression régulière spécialement construite
- **Affecté :** `19.2 < 19.2.7`, `19.3 < 19.3.3`, `19.4 < 19.4.1`
- **Corrigé :** `19.2.7`, `19.3.3`, `19.4.1`
- **Action MCO :** 🔴 mise à jour immédiate

### 🔴 Critique — CVE-2026-93577

- **CVSS :** 9.9
- **Type :** integer overflow dans le compilateur d'expressions régulières
- **Impact :** RCE sur le serveur GitLab
- **Vecteur :** réseau
- **Authentification :** requise
- **Privilèges :** faibles privilèges suffisants
- **Interaction utilisateur :** aucune
- **Fonction concernée :** configuration CI/CD avec expression régulière spécialement construite
- **Affecté :** `19.2 < 19.2.7`, `19.3 < 19.3.3`, `19.4 < 19.4.1`
- **Corrigé :** `19.2.7`, `19.3.3`, `19.4.1`
- **Action MCO :** 🔴 mise à jour immédiate

### 🟠 Haute — CVE-2026-84739

- **CVSS :** 8.7
- **Type :** XSS dans le visualiseur de diff de Merge Request
- **Impact :** JavaScript arbitraire dans la session d'un autre utilisateur
- **Prérequis :** attaquant authentifié + interaction victime
- **Corrigé :** `19.2.7`, `19.3.3`, `19.4.1`

### 🟠 Haute — CVE-2026-92470

- **CVSS :** 7.7
- **Édition :** EE
- **Type :** missing authorization dans Duo AI Job Troubleshooting
- **Impact :** exposition de variables CI/CD sensibles dans des traces debug
- **Authentification :** requise
- **Interaction utilisateur :** aucune
- **Fonction concernée :** Duo AI + traces de jobs debug
- **Corrigé :** `19.2.7`, `19.3.3`, `19.4.1`

### Signaux à retenir

- **RCE :** CVE-2026-89078, CVE-2026-93577
- **CI/CD :** CVE-2026-89078, CVE-2026-93577, CVE-2026-92470, CVE-2026-4523
- **Secrets/tokens :** CVE-2026-92470, CVE-2026-4523, CVE-2026-92874
- **Sans authentification :** CVE-2026-4523
- **Auth bypass total :** aucun nouveau cas critique identifié dans la release suivie

**Action MCO :** toute instance Self-Managed encore sous `19.3.2` doit passer au minimum à `19.3.3`.

---

# Rocky Linux / RHEL 9

## Nouveau kernel de sécurité

Dernier kernel Rocky Linux 9 BaseOS x86_64 observé :

```text
5.14.0-687.51.1.el9_8
```

Le paquet est daté du **25 septembre 2026**.

Il correspond à **RHSA-2026:71232**, advisory Red Hat classé **Important**, publié le 24 septembre 2026.

### CVE corrigées par RHSA-2026:71232

```text
CVE-2026-23007
CVE-2026-53005
CVE-2026-63802
CVE-2026-63831
CVE-2026-64053
CVE-2026-64383
CVE-2026-64534
CVE-2026-64564
CVE-2026-68201
CVE-2026-72261
CVE-2026-80844
CVE-2026-81000
CVE-2026-89846
```

### Classement MCO des plus significatives

| Criticité | CVE | Impact / vecteur | Prérequis | Correctif |
|---|---|---|---|---|
| 🟠 Important / CVSS 8.3 | **CVE-2026-80844 — DirtyAH6** | corruption mémoire AH6/XFRM, élévation locale vers root | utilisateur local + unprivileged user namespaces + IPv6 AH/XFRM | **687.51.1** |
| 🟠 Important / CVSS 7.8 | **CVE-2026-81000 — TUNderflow** | OOB write TUN/TAP, corruption mémoire, possible code execution/root | utilisateur local à faibles privilèges + TUN/TAP / OVS | **687.51.1** |
| 🟠 Important / CVSS Red Hat 7.0 | **CVE-2026-89846 — qla2xxx** | lecture hors limites, fuite mémoire kernel / crash | exposition Fibre Channel qla2xxx ; Red Hat score AV:L/AC:H | **687.51.1** |

Les autres CVE du même advisory sont couvertes par la mise à jour `687.51.1`; aucune n'a été identifiée aujourd'hui comme nécessitant une action supérieure à l'advisory global Important.

## RHSB-2026-011 : correction partielle

Le bulletin reste **Ongoing**.

État du lot réseau :

| CVE | État avec `687.51.1` |
|---|---|
| CVE-2026-80844 — DirtyAH6 | ✅ corrigée via RHSA-2026:71232 |
| CVE-2026-81000 — TUNderflow | ✅ corrigée via RHSA-2026:71232 |
| CVE-2026-74469 — DiagSpill | ⚠️ pas listée dans RHSA-2026:71232 |
| CVE-2026-68121 — PPPoEject | ⚠️ pas listée dans RHSA-2026:71232 |

### Vulnérabilités restant à surveiller

**CVE-2026-74469 — DiagSpill**
- Important, CVSS Red Hat 8.3
- SCTP / `sctp_diag`
- vecteur réseau dans l'évaluation Red Hat
- peut conduire à corruption mémoire, DoS et potentiellement exécution de code
- priorité renforcée car le bulletin Red Hat indique qu'elle ne dépend pas des unprivileged user namespaces pour le scénario local décrit

**CVE-2026-68121 — PPPoEject**
- Important, CVSS Red Hat 7.3
- local, faibles privilèges
- use-after-free / corruption mémoire
- nécessite PPPoE et, pour le scénario du bulletin, unprivileged user namespaces

### Action MCO Rocky

```bash
dnf check-update kernel
dnf update kernel
reboot
uname -r
```

**Cible immédiate : `5.14.0-687.51.1.el9_8`.**

Après déploiement, maintenir la surveillance de DiagSpill et PPPoEject tant qu'un advisory RHEL 9 standard ne confirme pas leur correction.

---

# Proxmox VE

## État production observé

```text
Proxmox VE       9.2
pve-manager      9.2.20
kernel prod      7.0.14-19-pve
```

Aucun `pve-manager` supérieur à `9.2.20` n'est visible dans le dépôt PVE no-subscription officiel consulté.

Le dernier kernel signé visible dans le canal **PVE enterprise** reste :

```text
proxmox-kernel-signed-7.0_7.0.14+19
```

## Kernel 7.0.14-20 aperçu en test

Un changelog :

```text
proxmox-kernel-7.0_7.0.14-20
```

est apparu le **25 septembre** dans un dépôt **pdm-test**.

Il n'est pas encore visible dans les métadonnées PVE enterprise/no-subscription consultées. Il est donc à considérer comme **signal de prépublication/test**, pas comme cible MCO de production.

### Action MCO Proxmox

- conserver `7.0.14-19-pve` comme cible production actuelle ;
- ne pas forcer `7.0.14-20` depuis un dépôt de test ;
- surveiller sa promotion vers les dépôts PVE habituels.

Contrôle :

```bash
uname -r
pveversion -v
apt update
apt list --upgradable 2>/dev/null | egrep 'proxmox-kernel|pve-manager'
```

---

# Actions du jour

## 🔴 Urgent

- [ ] Mettre à jour les GitLab Self-Managed affectés vers `19.4.1`, `19.3.3` ou `19.2.7`.
- [ ] Prioriser les instances GitLab exposées à Internet et les utilisateurs pouvant modifier la CI/CD.

## 🟠 Haute priorité

- [ ] Déployer Rocky `5.14.0-687.51.1.el9_8`.
- [ ] Rebooter les Rocky afin d'activer le kernel.
- [ ] Considérer DirtyAH6 et TUNderflow comme corrigées après activation de `687.51.1`.
- [ ] Continuer à surveiller DiagSpill et PPPoEject.

## 🟡 Normale

- [ ] Maintenir Proxmox sur la cible `7.0.14-19-pve`.
- [ ] Surveiller la promotion de `7.0.14-20` hors dépôt de test.

---

# Sources

- GitLab — Critical Patch Release 19.4.1, 19.3.3, 19.2.7
  https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-1-released/

- Red Hat — RHSA-2026:71232 / Security Data
  https://access.redhat.com/hydra/rest/securitydata/csaf

- Red Hat — RHSB-2026-011
  https://access.redhat.com/security/vulnerabilities/RHSB-2026-011

- Red Hat — CVE-2026-80844
  https://access.redhat.com/security/cve/cve-2026-80844

- Red Hat — CVE-2026-81000
  https://access.redhat.com/security/cve/cve-2026-81000

- Red Hat — CVE-2026-74469
  https://access.redhat.com/security/cve/cve-2026-74469

- Red Hat — CVE-2026-68121
  https://access.redhat.com/security/cve/cve-2026-68121

- Rocky Linux 9 BaseOS x86_64
  https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

- Proxmox — pve-manager
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

- Proxmox — kernel PVE enterprise
  https://metadata.cdn.proxmox.com/enterprise/changelogs/pve/dists/trixie/pve-enterprise/p/proxmox-kernel-signed-7.0/

- Proxmox — kernel 7.0 test
  https://sg3.metadata.cdn.proxmox.com/download/changelogs/pdm/dists/trixie/pdm-test/p/proxmox-kernel-7.0/

> Rapport orienté exploitation : seules les nouveautés susceptibles de modifier le niveau de risque ou d'entraîner une action MCO sont mises en avant.
