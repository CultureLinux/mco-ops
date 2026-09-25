---
type: mco-daily
date: 2026-09-25
generated_at: 2026-09-25T08:10:00+02:00
status: critical
rocky_kernel_latest: 5.14.0-687.50.1.el9_8
rocky_security_advisory: RHSA-2026:70459
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
proxmox_kernel_latest: 7.0.14-19-pve
gitlab_latest_security_release: 19.4.1
gitlab_supported_security_fixes: [19.4.1, 19.3.3, 19.2.7]
critical_cve: 2
high_cve: 2
important_kernel_advisory: true
---

# MCO Daily — 25 septembre 2026

## Résumé exécutif

🔴 **GitLab reste prioritaire** : aucune nouvelle Security Patch Release n'a remplacé les correctifs du 23 septembre. Les branches corrigées restent `19.4.1`, `19.3.3` et `19.2.7`, avec deux RCE CI/CD CVSS 9.9.

🟠 **Nouveau kernel Rocky/RHEL 9 : `5.14.0-687.50.1.el9_8`**. Il correspond à **RHSA-2026:70459**, classé **Important**, et corrige 13 CVE. Ce kernel ne corrige pas les quatre CVE suivies dans **RHSB-2026-011**, qui reste en statut Ongoing.

🟢 **Proxmox stable** : `pve-manager 9.2.20` et kernel `7.0.14-19-pve` restent les dernières versions observées.

---

# GitLab Self-Managed

## Critical Patch Release toujours en vigueur

Versions corrigées actuellement maintenues :

```text
19.4.x -> 19.4.1
19.3.x -> 19.3.3
19.2.x -> 19.2.7
```

GitLab recommande la mise à jour immédiate des installations Self-Managed affectées.

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
- **Action :** 🔴 mise à jour immédiate

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
- **Action :** 🔴 mise à jour immédiate

### 🟠 Haute — CVE-2026-84739

- **CVSS :** 8.7
- **Type :** XSS dans le visualiseur de diff de Merge Request
- **Impact :** exécution de JavaScript dans la session d'un autre utilisateur
- **Authentification :** requise côté attaquant
- **Interaction utilisateur :** requise côté victime
- **Affecté :** `13.11 < 19.2.7`, `19.3 < 19.3.3`, `19.4 < 19.4.1`
- **Action :** mise à jour immédiate

### 🟠 Haute — CVE-2026-92470

- **CVSS :** 7.7
- **Édition :** EE
- **Type :** missing authorization dans Duo AI Job Troubleshooting
- **Impact :** exposition de variables CI/CD sensibles dans des traces debug
- **Authentification :** requise
- **Interaction utilisateur :** aucune
- **Fonction concernée :** Duo AI + traces de jobs en mode debug
- **Affecté :** `18.7 < 19.2.7`, `19.3 < 19.3.3`, `19.4 < 19.4.1`
- **Action :** mise à jour immédiate

## Autres points de sécurité GitLab

| CVE | CVSS | Risque notable |
|---|---:|---|
| CVE-2026-92874 | 5.4 | dépassement du scope d'un token MCP |
| CVE-2026-92530 | 4.3 | spoof d'auteur via Direct Transfer |
| CVE-2026-8937 | 4.3 | lecture de contenus privés via Epic Issues REST API |
| CVE-2026-92529 | 4.3 | contournement de gouvernance Duo Workflow |
| CVE-2026-10518 | 4.3 | lecture de politiques de sécurité privées |
| CVE-2026-4523 | 3.7 | **lecture non authentifiée de traces CI/CD contenant potentiellement des variables sensibles** |
| CVE-2026-92628 | 3.1 | contexte utilisateur incorrect dans `gitlab_search` MCP |

### Signaux à retenir

- **RCE :** CVE-2026-89078, CVE-2026-93577
- **CI/CD :** CVE-2026-89078, CVE-2026-93577, CVE-2026-92470, CVE-2026-4523
- **Secrets/tokens :** CVE-2026-92470, CVE-2026-4523, CVE-2026-92874
- **Sans authentification :** CVE-2026-4523
- **Auth bypass total :** aucun nouveau cas critique identifié dans la release suivie

---

# Rocky Linux / RHEL 9

## Nouveau kernel de sécurité

Le dépôt Rocky Linux 9 BaseOS x86_64 contient désormais :

```text
5.14.0-687.50.1.el9_8
```

Paquet daté du **24 septembre 2026**.

Ce build correspond à **RHSA-2026:70459**, advisory Red Hat classé **Important**, publié le 23 septembre, avec 13 CVE corrigées.

### CVE corrigées par RHSA-2026:70459

```text
CVE-2025-39964
CVE-2026-45894
CVE-2026-45959
CVE-2026-53062
CVE-2026-63823
CVE-2026-68155
CVE-2026-68156
CVE-2026-68157
CVE-2026-68188
CVE-2026-68293
CVE-2026-68391
CVE-2026-72072
CVE-2026-74518
```

### Classement MCO

| Niveau | CVE / composant | Impact / vecteur | Action |
|---|---|---|---|
| 🟠 Important | RHSA-2026:70459 — ensemble des 13 CVE | advisory kernel RHEL 9 classé Important | déployer `687.50.1` et redémarrer |
| 🟠 CVSS 7.3 Red Hat | CVE-2025-39964 — AF_ALG crypto | local, faibles privilèges, corruption d'état / DoS / intégrité | patcher ; pas de mitigation Red Hat satisfaisante |
| 🟡 CVSS 7.0 Red Hat | CVE-2026-53062 — dm-cache SMQ | local, complexité élevée, data race / corruption / DoS | patcher si device-mapper cache utilisé |
| 🟡 CVSS 7.0 Red Hat | CVE-2026-68156 — libceph | contexte Ceph client, use-after-free, crash / possible élévation | patcher ; désactiver module `ceph` si inutilisé |
| 🟡 CVSS 7.0 Red Hat | CVE-2026-68157 — libceph | CRUSH map malformée, crash / DoS | patcher, priorité renforcée sur clients Ceph |
| 🟡 CVSS 7.0 Red Hat | CVE-2026-68293 — mlx5 | buffer overflow pilote Mellanox, crash / DoS | patcher sur hôtes mlx5 |
| 🟡 CVSS 7.0 Red Hat | CVE-2026-68391 — Bluetooth | use-after-free local, crash / possible code execution | faible exposition serveur si Bluetooth absent |
| 🟡 CVSS 7.0 Red Hat | CVE-2026-72072 — MACsec/mlx5e | local, UAF, crash / DoS | priorité si MACsec offload utilisé |

Les autres CVE du même advisory restent couvertes par la mise à jour `687.50.1`; aucune n'a été identifiée ce matin comme nécessitant une action distincte supérieure à l'advisory global Important.

### Attention : RHSB-2026-011 reste distinct

Le nouveau kernel `687.50.1` **ne liste pas** les quatre CVE suivantes dans RHSA-2026:70459 :

- CVE-2026-74469 — DiagSpill
- CVE-2026-80844 — DirtyAH6
- CVE-2026-81000 — TUNderflow
- CVE-2026-68121 — PPPoEject

Red Hat continue d'afficher **RHSB-2026-011** comme **Ongoing**. Il ne faut donc pas considérer `687.50.1` comme le correctif de ce bulletin.

Contrôle :

```bash
uname -r
dnf check-update kernel
dnf repoquery --latest-limit=3 kernel
lsmod | egrep '^(sctp|sctp_diag|ah6|tun|pppoe|ceph)\b'
```

**Action MCO Rocky :** installer `5.14.0-687.50.1.el9_8` selon la fenêtre de maintenance et prévoir un reboot, tout en maintenant la surveillance spécifique de RHSB-2026-011.

---

# Proxmox VE

État observé ce matin :

```text
Proxmox VE        9.2
pve-manager       9.2.20
kernel cible      7.0.14-19-pve
```

Aucun `pve-manager` plus récent que `9.2.20` n'est visible dans le dépôt no-subscription officiel consulté.

Le dernier changelog kernel signé 7.0 reste :

```text
proxmox-kernel-signed-7.0_7.0.14+19
```

Aucun build `+20` ou supérieur n'est visible dans les métadonnées officielles Proxmox consultées ce matin.

Contrôle :

```bash
uname -r
pveversion -v
apt update
apt list --upgradable 2>/dev/null | egrep 'proxmox-kernel|pve-manager'
```

**Action MCO Proxmox :** pas de nouvelle urgence aujourd'hui ; poursuivre le passage vers `7.0.14-19-pve` sur les nœuds encore en version antérieure, avec reboot pour activation.

---

# Actions du jour

## 🔴 Urgent

- [ ] Mettre à jour tout GitLab Self-Managed encore sous `19.3.2` vers **`19.3.3` minimum** (ou `19.4.1` si la montée de branche est prévue).
- [ ] Prioriser les GitLab exposés à Internet et les instances où des développeurs peuvent modifier la configuration CI/CD.
- [ ] Vérifier l'exposition de secrets CI/CD et Duo AI sur EE.

## 🟠 Haute priorité

- [ ] Déployer le nouveau kernel Rocky `5.14.0-687.50.1.el9_8` issu de RHSA-2026:70459.
- [ ] Planifier le reboot des Rocky après installation.
- [ ] Continuer à surveiller séparément RHSB-2026-011.

## 🟡 Normale

- [ ] Vérifier les Proxmox sous `7.0.14-18-pve` ou antérieur et planifier `7.0.14-19-pve`.

---

# Sources

- GitLab — Critical Patch Release 19.4.1, 19.3.3, 19.2.7
  https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-1-released/

- GitLab — Release and maintenance policy
  https://docs.gitlab.com/policy/maintenance/

- Red Hat — RHSA-2026:70459 / Security Data
  https://access.redhat.com/hydra/rest/securitydata/csaf

- Red Hat — RHSB-2026-011
  https://access.redhat.com/security/vulnerabilities/RHSB-2026-011

- Rocky Linux 9 BaseOS x86_64
  https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

- Proxmox — pve-manager changelogs
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

- Proxmox — kernel 7.0 signé
  https://metadata.cdn.proxmox.com/enterprise/changelogs/pve/dists/trixie/pve-enterprise/p/proxmox-kernel-signed-7.0/

> Rapport orienté exploitation : seuls les changements susceptibles de modifier le niveau de risque ou d'entraîner une action MCO sont mis en avant.