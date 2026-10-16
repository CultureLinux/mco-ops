---
type: mco-daily
date: 2026-10-16
timezone: Europe/Paris
status: action-required
gitlab_latest_minor: 19.5.0
gitlab_last_critical_patch: [19.4.2, 19.3.5, 19.2.8]
rocky_kernel_latest: 5.14.0-687.57.1.el9_8
rocky_kernel_advisory: RHSA-2026:81552
proxmox_kernel_latest_no_subscription: 7.0.14-24-pve
proxmox_pve_manager_latest_no_subscription: 9.2.21
---

# Brief MCO — 16 octobre 2026

## Résumé exécutif

**NOUVEAU — GitLab 19.5.0 publié le 15 octobre.** Nouvelle version mineure Self-Managed, avec changements MCP, gestion des secrets et Kubernetes Agent/KAS. À planifier en recette, distinctement du correctif de sécurité urgent.

**P0 — GitLab :** la dernière **Critical Patch** identifiée reste celle du **12 octobre** : **CVE-2026-97229, RCE serveur via CI/CD sans authentification (CVSS 10.0)**. Correctifs par branche : **19.2.8 / 19.3.5 / 19.4.2**. Ne pas différer la correction en attendant la migration fonctionnelle 19.5.

**P1 — Rocky/RHEL 9 :** pas de kernel plus récent confirmé que **5.14.0-687.57.1.el9_8** (13 octobre ; **RHSA-2026:81552**, 13 CVE).

**P1 — Proxmox VE 9 :** pas de nouvelle version no-subscription confirmée : **7.0.14-24-pve**, **pve-manager 9.2.21**. Maintenir le déploiement pilote compte tenu des régressions matérielles récentes.

## 1. GitLab Self-Managed CE/EE

### Nouveauté du 15 octobre : GitLab 19.5

La version **19.5.0** est annoncée officiellement le **15 octobre 2026**. Points MCO pertinents :
- **Serveur MCP GitLab disponible en GA** : inventorier les intégrations MCP, leurs jetons et autorisations ; restreindre l'exposition réseau selon les besoins.
- **GitLab Secrets Manager GA** (Premium/Ultimate, option/add-on) : centralisation des secrets CI avec périmètre par job et audit ; **ne remplace pas** la rotation des secrets éventuellement exposés.
- **Kubernetes Agent/KAS** : les **nouvelles installations** Linux package/Helm annoncent par défaut **gRPC natif `grpcs://`** au lieu de WebSocket encapsulé. Les agents existants `wss://` continuent de fonctionner ; vérifier proxy, TLS, pare-feu et agent avant migration.
- **GitLab 19.5 est un jalon d'upgrade obligatoire pour les futures montées de version 19.x**. Vérifier les migrations d'arrière-plan et les notes 19.5.0 (KAS, Duo, Geo selon usage).

**Décision MCO :** corriger d'abord toute installation vulnérable sur une branche déjà en place (ex. 19.3.2 → **19.3.5**), puis tester **19.5.0** séparément sur une préproduction. Ne pas assimiler la sortie 19.5.0 à une nouvelle Critical Patch.

### Dernière Critical Patch (12 octobre) : correctifs et CVE

**Versions corrigées officiellement :** **19.2.8 / 19.3.5 / 19.4.2**. GitLab signale que 19.3.4 provoquait un défaut d'affichage des merge requests avec l'intégration Ona : préférer **19.3.5**.

| CVE / CVSS | Impact | Vecteur, prérequis, interaction | Versions affectées → corrigées |
|---|---|---|---|
| **CVE-2026-97229 — 10.0 Critical** | **RCE serveur**, compilation d'expressions régulières CI/CD | **Réseau, PR:N, UI:N, AC:L : sans authentification**, aucune interaction | 19.2 < 19.2.8 ; 19.3 < 19.3.5 ; 19.4 < 19.4.2 |
| **CVE-2026-92883 — 8.7 High** | XSS via coloration syntaxique d'un dépôt | Réseau, compte faible privilège, **interaction victime requise** | depuis 19.1 ; correctifs 19.2.8/19.3.5/19.4.2 |
| **CVE-2026-16301 — 8.5 High** | **Contournement d'autorisation CI/CD**, pipelines sur branches protégées | Réseau, **Developer authentifié**, UI:N | depuis 18.7 ; mêmes correctifs |
| **CVE-2026-14814 — 8.2 High** | Empoisonnement du cache de fichiers dépôt ; **exposition possible de variables CI protégées** | Réseau, compte faible privilège, complexité élevée, UI:N | anciennes versions jusqu'aux mêmes correctifs |
| **CVE-2026-81839 — 7.7 High** | Exécution de pipelines avec privilèges inappropriés après transfert de projet (EE) | Réseau, compte faible privilège, complexité élevée, **UI:R** | depuis 18.1 ; mêmes correctifs |
| **CVE-2026-96893 — 7.3 High** | Injection d'instructions dans Duo Agent Platform (EE), actions attribuées à d'autres utilisateurs | Réseau, compte faible privilège, **UI:R** | depuis 19.1 ; mêmes correctifs |
| **CVE-2026-101168 — 6.2 Medium** | **Fuite de tokens/secrets** dans les logs GitLab Pages | Accès privilégié aux logs, UI:N | depuis 12.0 ; mêmes correctifs |
| **CVE-2026-7767 — 5.8 Medium** | **Fuite de jetons** via redirection proxy Kubernetes | Authentifié avec contrôle d'un agent, complexité élevée, UI:R | depuis 13.11 ; mêmes correctifs |
| **CVE-2026-16275 / 84284 / 85529 — 5.3 chacune** | **Lecture sans authentification** de notes ou work items confidentiels via GraphQL | Réseau, **PR:N, UI:N** | respectivement depuis 17.4 / 17.11 / 19.2 ; mêmes correctifs |

**RCE sans authentification : P0 immédiat**, même en l'absence de signe d'exploitation. Les versions de départ diffèrent selon les CVE ; la plage exacte figure dans l'avis officiel.

**Action sur l'instance Omnibus :** relever la version installée (dernier état connu : 19.3.2, non vérifié aujourd'hui), inspecter les éventuels `dnf versionlock`, sauvegarder configuration/secrets/base/dépôts, installer le correctif de la branche puis contrôler : accès web et SSH, runners, pipelines, MR, branches protégées, migrations Sidekiq et logs. Si suspicion d'exposition, examiner l'historique CI/CD et envisager la rotation des secrets.

## 2. Rocky Linux / RHEL 9 — kernel

**Dernier kernel x86_64 confirmé dans Rocky BaseOS : `5.14.0-687.57.1.el9_8`**, paquets publiés le **13 octobre 2026**. **RHSA-2026:81552** est classé **Important** et référence **13 CVE kernel**. Pas de nouvelle révision `687.58+` trouvée au contrôle du 16 octobre.

| CVE / CVSS Red Hat | Sous-système / risque | Exposition à qualifier |
|---|---|---|
| **CVE-2026-68123 — 7.0** | Open vSwitch/GSO : sous-débordement et corruption mémoire, DoS possible | Chemin Open vSwitch concerné ; vecteur Red Hat local, complexité élevée, faibles privilèges, UI:N |
| **CVE-2026-72318 — 7.0** | Client CIFS/DFS : lecture hors limites, fuite mémoire / DoS | Références DFS malformées fournies par serveur SMB ; qualification Red Hat locale, AC:H, PR:L, UI:N |
| **CVE-2026-63925 — 7.0** | MACsec XPN : **contournement de protection anti-rejeu** | Uniquement si MACsec XPN activé et conditions de compteur réunies ; ne pas confondre avec auth bypass GitLab |
| **CVE-2026-89811 — 7.0** | AMDGPU/amdkfd : défaut d'invalidation TLB, accès DMA après retrait, défauts mémoire / blocage GPU | GPU AMD et chemins SVM/MES concernés, accès local |
| **CVE-2026-64507 — 6.0** | BPF JIT/Spectre-v2 : protection IBPB incomplète, fuite potentielle | x86/BPF JIT, accès local privilégié, UI:N |

Autres CVE répertoriées par l'avis : **CVE-2026-46309, 64508, 68236, 68245, 68278, 80887, 89894, 89970**. Les scores non vérifiés individuellement ne sont pas extrapolés. Les scores Red Hat peuvent différer des scores CVE.org/NVD en raison des backports et de l'exposition réelle.

**Action P1 :** vérifier `dnf updateinfo list --security`, `dnf check-update kernel\*`, planifier l'installation de `687.57.1` et le redémarrage contrôlé ; confirmer `uname -r` et la stabilité des modules tiers. Priorité aux serveurs utilisant OVS, SMB/DFS ou fonctionnalités GPU/NVMe concernées.

## 3. Proxmox VE 9 — kernel et stabilité

| Composant | Dernière version confirmée (dépôt **pve-no-subscription**) |
|---|---|
| `proxmox-kernel-7.0` | **7.0.14-24** (10 octobre) |
| `proxmox-kernel-signed-7.0` | **7.0.14+24** |
| `pve-manager` | **9.2.21** (29 septembre) |

Aucun kernel `7.0.14-25+` confirmé dans l'index officiel no-subscription consulté. Les correctifs récents `-22` à `-24` couvrent des régressions **PCIe/NVMe, réseau (dont ixgbe), RTC** et des défauts mémoire **SMB/CIFS, Open vSwitch, Btrfs**. Les CVE kernel récentes documentées par Proxmox comprennent **CVE-2026-97612** (MPLS/OVS) et **CVE-2026-97562** (SMB/CIFS) ; CVSS individuel non confirmé dans le changelog Proxmox.

**Action P1 :** sur les hôtes ayant connu des kernel panics, conserver un kernel précédent bootable ; tester `7.0.14-24-pve` sur un nœud pilote puis contrôler `journalctl -k -b`, erreurs PCIe/AER, NIC, disques NVMe, ZFS/mdadm, démarrage VM et KVM. Généraliser après stabilité constatée. La version candidate exacte dépend du dépôt activé sur chaque hôte.

## Priorités du jour

1. **P0 :** GitLab CE/EE vulnérable → **19.3.5 / 19.4.2 / 19.2.8**, sans attendre l'upgrade 19.5.
2. **P1 :** préparer **GitLab 19.5.0** en préproduction ; vérifier KAS/gRPC, runners, migrations et Secrets Manager/MCP si utilisés.
3. **P1 :** terminer le déploiement Rocky **687.57.1**, RHSA-2026:81552.
4. **P1 :** pilote Proxmox **7.0.14-24-pve** ; surveiller les panics/régressions.

## Sources officielles et vérification (16 octobre 2026)

- GitLab 19.5 (15 octobre) : https://docs.gitlab.com/releases/19/gitlab-19-5-released/
- GitLab Critical Patch (12 octobre) : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- GitLab notes d'upgrade 19 : https://docs.gitlab.com/update/versions/gitlab_19_changes/
- GitLab jalons obligatoires : https://docs.gitlab.com/update/upgrade_paths/
- Rocky Linux 9 BaseOS, paquets kernel : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- Red Hat RHSA-2026:81552 : https://access.redhat.com/errata/RHSA-2026:81552
- Red Hat CVE-2026-68123 : https://access.redhat.com/security/cve/cve-2026-68123
- Red Hat CVE-2026-72318 : https://access.redhat.com/security/cve/cve-2026-72318
- Red Hat CVE-2026-89811 : https://access.redhat.com/security/cve/cve-2026-89811
- Proxmox kernel changelog : https://github.com/proxmox/pve-kernel/blob/master/debian/changelog
- Proxmox no-subscription kernel : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- Proxmox no-subscription pve-manager : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
