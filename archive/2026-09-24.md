---
type: mco-daily
date: 2026-09-24
generated_at: 2026-09-24T09:21:00+02:00
status: critical
rocky_kernel_latest: 5.14.0-687.49.1.el9_8
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.20
proxmox_kernel_latest: 7.0.14-19-pve
gitlab_latest_security_release: 19.4.1
gitlab_supported_security_fixes: [19.4.1, 19.3.3, 19.2.7]
critical_cve: 2
high_cve: 2
important_kernel_cve: 4
---

# MCO Daily — 24 septembre 2026

## Résumé exécutif

🔴 **Action urgente GitLab Self-Managed.** GitLab a publié le 23 septembre les versions **19.4.1, 19.3.3 et 19.2.7** avec deux vulnérabilités **RCE serveur CVSS 9.9** dans le traitement d'expressions régulières CI/CD. Toute instance affectée doit être mise à jour immédiatement.

🟠 Côté Rocky/RHEL, **RHSB-2026-011** reste en cours avec quatre vulnérabilités kernel réseau classées Important. Aucun kernel Rocky plus récent que `5.14.0-687.49.1.el9_8` n'est visible.

🟢 Côté Proxmox, pas de nouvelle release critique : `pve-manager 9.2.20` et `7.0.14-19-pve` restent les versions observées.

---

# GitLab Self-Managed

## Critical Patch Release du 23 septembre 2026

Versions corrigées : `19.4.1`, `19.3.3`, `19.2.7`.

GitLab recommande explicitement la mise à jour immédiate de toutes les installations Self-Managed affectées.

### 🔴 Critique — CVE-2026-89078

- **Type :** Double Free dans le parseur d'expressions régulières
- **CVSS :** 9.9
- **Éditions :** CE / EE
- **Impact :** exécution de code arbitraire sur le serveur GitLab
- **Vecteur :** réseau, complexité faible, authentification requise, faibles privilèges suffisants, aucune interaction utilisateur
- **Fonction concernée :** expression régulière spécialement construite dans une configuration CI/CD
- **Versions affectées :** `19.2.x < 19.2.7`, `19.3.x < 19.3.3`, `19.4.x < 19.4.1`
- **Versions corrigées :** `19.2.7`, `19.3.3`, `19.4.1`
- **Action MCO :** 🔴 mise à jour immédiate

### 🔴 Critique — CVE-2026-93577

- **Type :** Integer Overflow dans le compilateur d'expressions régulières
- **CVSS :** 9.9
- **Éditions :** CE / EE
- **Impact :** exécution de code arbitraire sur le serveur GitLab
- **Vecteur :** réseau, complexité faible, authentification requise, faibles privilèges suffisants, aucune interaction utilisateur
- **Fonction concernée :** expression régulière spécialement construite dans une configuration CI/CD
- **Versions affectées :** `19.2.x < 19.2.7`, `19.3.x < 19.3.3`, `19.4.x < 19.4.1`
- **Versions corrigées :** `19.2.7`, `19.3.3`, `19.4.1`
- **Action MCO :** 🔴 mise à jour immédiate

### 🟠 Haute — CVE-2026-84739

- **Type :** XSS dans le visualiseur de diff de Merge Request
- **CVSS :** 8.7
- **Éditions :** CE / EE
- **Impact :** JavaScript arbitraire dans la session navigateur d'un autre utilisateur
- **Prérequis :** attaquant authentifié ; interaction de la victime requise ; affichage d'un diff de MR contenant des composants de chemin spécialement construits
- **Versions affectées :** `13.11` jusqu'à `< 19.2.7`, `19.3.x < 19.3.3`, `19.4.x < 19.4.1`
- **Action MCO :** mise à jour immédiate

### 🟠 Haute — CVE-2026-92470

- **Type :** Missing Authorization dans Duo AI Job Troubleshooting
- **CVSS :** 7.7
- **Édition :** EE
- **Impact :** lecture de valeurs sensibles de variables CI/CD présentes dans des traces de jobs en mode debug
- **Prérequis :** utilisateur authentifié ; fonctionnalité Duo AI concernée ; traces debug exposant des variables
- **Versions affectées :** `18.7` jusqu'à `< 19.2.7`, `19.3.x < 19.3.3`, `19.4.x < 19.4.1`
- **Action MCO :** mise à jour immédiate, particulièrement si Duo et des secrets CI/CD sont utilisés

## Autres correctifs GitLab à noter

| CVE | CVSS | Sujet | Risque |
|---|---:|---|---|
| CVE-2026-92874 | 5.4 | Scope MCP API | token MCP permettant des actions au-delà du périmètre prévu |
| CVE-2026-92530 | 4.3 | Direct Transfer user mapping | usurpation d'auteur de Merge Request |
| CVE-2026-8937 | 4.3 | Epic Issues REST API | lecture de contenus privés |
| CVE-2026-92529 | 4.3 | Duo Workflow governance | contournement de politiques AI |
| CVE-2026-10518 | 4.3 | GraphQL memberRoles | lecture de politiques de sécurité privées |
| CVE-2026-4523 | 3.7 | GraphQL CI job trace API | **lecture non authentifiée de traces CI/CD contenant potentiellement des variables sensibles** |
| CVE-2026-92628 | 3.1 | MCP `gitlab_search` race condition | résultats retournés sous un mauvais contexte utilisateur |

### Points particulièrement sensibles

- **RCE :** CVE-2026-89078 et CVE-2026-93577.
- **CI/CD / secrets :** CVE-2026-92470 et CVE-2026-4523.
- **Sans authentification :** CVE-2026-4523 peut exposer des traces CI/CD ; complexité d'exploitation élevée.
- **MCP / tokens :** CVE-2026-92874 touche l'application des scopes de tokens MCP.

### Impact de l'upgrade

- **Mono-nœud :** interruption pendant l'upgrade pendant les migrations requises.
- **Multi-nœuds :** zéro interruption possible avec la procédure zero-downtime GitLab.
- Des post-deploy migrations sont incluses notamment dans `19.3.3` et `19.2.7`.

Vérification :

```bash
gitlab-rake gitlab:env:info | grep 'GitLab version'
rpm -qa | grep gitlab
```

**Cible minimale :**

```text
19.4.x -> 19.4.1
19.3.x -> 19.3.3
19.2.x -> 19.2.7
```

---

# Rocky Linux / RHEL 9

Dernier kernel Rocky Linux 9 BaseOS x86_64 observé :

```text
5.14.0-687.49.1.el9_8
```

Le paquet est daté du **18 septembre 2026**. Aucun kernel Rocky 9 plus récent n'est visible ce matin dans le dépôt officiel consulté.

Le bulletin **RHSB-2026-011** reste en statut **Ongoing**.

## Vulnérabilités kernel prioritaires

| Criticité | CVE | Impact | Prérequis | Correctif |
|---|---|---|---|---|
| 🟠 Important | CVE-2026-74469 — DiagSpill | local → root ; DoS distant possible | SCTP + `sctp_diag`; pas de user namespace requis | en cours de publication |
| 🟠 Important | CVE-2026-80844 — DirtyAH6 | corruption mémoire ; local → root | AH6/XFRM + unprivileged user namespaces | en cours de publication |
| 🟠 Important | CVE-2026-81000 — TUNderflow | heap overflow ; local → root | TUN/TAP + unprivileged user namespaces | en cours de publication |
| 🟠 Important | CVE-2026-68121 — PPPoEject | use-after-free ; local → root | PPPoE + unprivileged user namespaces | en cours de publication |

**DiagSpill reste prioritaire** car son scénario local ne nécessite pas les unprivileged user namespaces.

```bash
uname -r
dnf check-update kernel
lsmod | egrep '^(sctp|sctp_diag|ah6|tun|pppoe)\b'
sysctl user.max_user_namespaces
```

Ne pas considérer `5.14.0-687.49.1.el9_8` comme corrigeant RHSB-2026-011 tant qu'un advisory Red Hat/Rocky ne l'atteste pas explicitement.

---

# Proxmox VE

État observé :

```text
Proxmox VE        9.2
pve-manager       9.2.20
kernel cible      7.0.14-19-pve
```

Aucun `pve-manager` plus récent que `9.2.20` n'est visible dans le dépôt no-subscription consulté.

Le dernier changelog signé kernel 7.0 observé est `proxmox-kernel-signed-7.0_7.0.14+19`, publié dans les métadonnées officielles Proxmox le **21 septembre 2026**. Aucun build kernel plus récent n'est visible dans cette source ce matin.

```bash
uname -r
pveversion -v
apt update
apt list --upgradable 2>/dev/null | egrep 'proxmox-kernel|pve-manager'
```

Un reboot est nécessaire pour activer un nouveau kernel après installation.

---

# Actions du jour

## 🔴 Urgent

- [ ] **Mettre à jour immédiatement les GitLab Self-Managed affectés** vers `19.4.1`, `19.3.3` ou `19.2.7` selon la branche.
- [ ] Prioriser les instances GitLab exposées à Internet et celles où des utilisateurs non administrateurs peuvent modifier des configurations CI/CD.
- [ ] Vérifier l'exposition des secrets CI/CD et l'usage de Duo AI sur EE.

## 🟠 Haute priorité

- [ ] Vérifier `sctp` / `sctp_diag` sur Rocky/RHEL 9.
- [ ] Continuer à surveiller la publication du correctif Red Hat pour RHSB-2026-011.

## 🟡 Normale

- [ ] Vérifier les Rocky sous un kernel antérieur à `5.14.0-687.49.1.el9_8`.
- [ ] Vérifier les Proxmox encore sous `7.0.14-18-pve` ou antérieur et planifier le passage à `7.0.14-19-pve`.

---

# Sources

- GitLab — Critical Patch Release 19.4.1, 19.3.3, 19.2.7
  https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-1-released/

- Red Hat — RHSB-2026-011
  https://access.redhat.com/security/vulnerabilities/RHSB-2026-011

- Rocky Linux 9 BaseOS x86_64
  https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

- Proxmox — pve-manager changelogs
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

- Proxmox — kernel 7.0 signé
  https://metadata.cdn.proxmox.com/enterprise/changelogs/pve/dists/trixie/pve-enterprise/p/proxmox-kernel-signed-7.0/

> Rapport orienté exploitation : les éléments sont priorisés selon leur impact réel sur un parc Rocky Linux, Proxmox et GitLab Self-Managed.