---
type: mco-daily
date: 2026-10-15
timezone: Europe/Paris
status: action-required
rocky_kernel_latest: 5.14.0-687.57.1.el9_8
rocky_kernel_advisory: RHSA-2026:81552
proxmox_kernel_latest_no_subscription: 7.0.14-24-pve
proxmox_pve_manager_latest_no_subscription: 9.2.21
gitlab_security_release: [19.4.2, 19.3.5, 19.2.8]
---

# Brief MCO — 15 octobre 2026

## Résumé exécutif

**P0 — GitLab :** la Critical Patch du **12 octobre 2026** reste urgente. **CVE-2026-97229 (CVSS 10.0) est une RCE CI/CD exploitable sans authentification**. Cibles GitLab CE/EE : **19.4.2 / 19.3.5 / 19.2.8**. Pas de nouvelle Critical Patch officielle identifiée ce matin.

**P1 — Rocky/RHEL 9 : nouveau kernel `5.14.0-687.57.1.el9_8`**, publié le **13 octobre**, associé à **RHSA-2026:81552 (Important)**, avec **13 CVE kernel** corrigées. Il remplace `687.56.1`.

**P1 — Proxmox :** pas de kernel plus récent que `7.0.14-24-pve` ni de `pve-manager` plus récent que `9.2.21` dans les index no-subscription vérifiés. Validation pilote toujours recommandée compte tenu des kernel panics/régressions matérielles suivis.

## 1. Rocky Linux / RHEL 9 — nouveau kernel et correctifs

### Version disponible

```text
5.14.0-687.57.1.el9_8.x86_64
```

Le dépôt Rocky Linux 9 BaseOS x86_64 publie `kernel`, `kernel-core` et `kernel-modules` **le 13 octobre 2026 vers 20:10 UTC**. L'erratum **RHSA-2026:81552**, classé **Important** par Red Hat, correspond à ce lot de corrections ; la publication Rocky est également répertoriée sous **RLSA-2026:81552**.

### Vulnérabilités à prioriser

| CVE | CVSS Red Hat | Composant / impact | Conditions et exposition |
|---|---:|---|---|
| **CVE-2026-68123** | **7.0** | **Open vSwitch :** underflow mémoire lors du traitement de paquets GSO, corruption mémoire / DoS possible | Exposition si Open vSwitch et chemin GSO concerné sont utilisés ; conditions précises à qualifier selon la configuration |
| **CVE-2026-72318** | **7.0** | **CIFS/SMB DFS :** lecture hors limites, fuite mémoire ou DoS | Risque sur client CIFS traitant une référence DFS malformée ; serveur SMB distant potentiellement non fiable |
| **CVE-2026-46309** | **7.0** | **Intel Xe DRM :** accès GPU à des données résiduelles, fuite d'informations inter-processus | Accès local et GPU Intel concerné, faible privilège, complexité élevée, pas d'interaction |
| **CVE-2026-63925** | **7.0** | **MACsec XPN :** contournement de protection anti-rejeu (authentification par rejeu) | MACsec avec XPN activé ; conditions de wrap du compteur ; ne constitue pas un auth bypass GitLab |
| **CVE-2026-64507** | **6.0** | **BPF JIT / Spectre-v2 :** purge IBPB manquante lors de la réutilisation de mémoire JIT | Local, privilèges élevés, pas d'interaction, contexte x86/BPF JIT |

Les autres CVE incluses dans **RHSA-2026:81552** sont : **CVE-2026-64508** (durcissement BPF JIT), **CVE-2026-68236** (AMD display), **CVE-2026-68245** (amdgpu), **CVE-2026-68278** (DisplayPort MST), **CVE-2026-80887** (vmwgfx), **CVE-2026-89811** (amdkfd), **CVE-2026-89894** (pilote média cx231xx) et **CVE-2026-89970** (NVMe target authentication). Les CVSS individuels non confirmés ici ne sont pas extrapolés.

**Action MCO :** prioriser les serveurs utilisant Open vSwitch, CIFS/DFS, BPF ou NVMe target, puis déployer `687.57.1` selon les fenêtres de maintenance. Contrôler la présence des modules tiers (DKMS), conserver un ancien kernel et vérifier le noyau actif après redémarrage :

```bash
dnf check-update kernel kernel-core kernel-modules
dnf update --security
reboot
uname -r
```

Ne pas interpréter le seul numéro upstream 5.14 comme absence de correctif : Red Hat et Rocky appliquent des backports.

## 2. GitLab Self-Managed CE/EE — urgence toujours active

La **Critical Patch Release du 12 octobre 2026** est toujours la plus récente identifiée. Les cibles par branche sont :

| Branche | Version corrigée |
|---|---|
| 19.2 | **19.2.8** |
| 19.3 | **19.3.5** |
| 19.4 | **19.4.2** |

**19.3.5**, et non 19.3.4, est la cible recommandée pour la branche 19.3 : GitLab a corrigé un dysfonctionnement des pages de merge requests avec l'intégration Ona.

### CVE prioritaires de la Critical Patch

| CVE | CVSS | Risque / fonctionnalité | Vecteur et prérequis |
|---|---:|---|---|
| **CVE-2026-97229** | **10.0 Critical** | **RCE serveur, compilation regex de configuration CI/CD** | **AV:N / PR:N / UI:N**, faible complexité ; **sans authentification** |
| **CVE-2026-92883** | **8.7 High** | XSS dans la coloration syntaxique des dépôts | Réseau, authentifié faible privilège, interaction victime requise |
| **CVE-2026-16301** | **8.5 High** | **Auth bypass fonctionnel / contrôle d'accès CI/CD** : Developer peut déclencher des pipelines sur branches protégées | Réseau, rôle Developer, sans interaction |
| **CVE-2026-14814** | **8.2 High** | Empoisonnement du cache de fichiers dépôt ; exécution de contenu inattendu par pipeline, **fuite possible de variables protégées** | Réseau, authentifié faible privilège, complexité élevée, sans interaction |
| **CVE-2026-81839** | **7.7 High** | Pipelines avec privilèges du nouveau groupe après transfert | EE, authentifié, complexité élevée, interaction requise |
| **CVE-2026-96893** | **7.3 High** | Injection d'instructions dans Duo Agent Platform | EE, authentifié, interaction requise |
| **CVE-2026-101168** | **6.2 Medium** | **Exposition de tokens/secrets** dans logs GitLab Pages | Accès privilégié aux logs Pages requis, sans interaction |
| **CVE-2026-7767** | **5.8 Medium** | **Fuite possible de tokens** via redirection proxy Kubernetes | Authentifié, contrôle d'un agent Kubernetes, complexité élevée, interaction requise |
| **CVE-2026-16275 / 84284 / 85529** | **5.3 Medium** | **Lecture de données confidentielles GraphQL sans authentification** | Réseau, **PR:N / UI:N** |

**Versions affectées :** pour CVE-2026-97229, **19.2 avant 19.2.8**, **19.3 avant 19.3.5**, **19.4 avant 19.4.2**. Les bornes de début des autres CVE varient : par exemple CVE-2026-16301 depuis 18.7 et CVE-2026-101168 depuis 12.0, jusqu'aux mêmes correctifs sur les branches maintenues. Consulter l'avis officiel pour chaque plage précise.

**Action MCO P0 :** vérifier la version Omnibus effective, retirer un éventuel verrouillage DNF sur la version vulnérable, sauvegarder GitLab, puis mettre à jour immédiatement vers la version corrigée de la branche. Après mise à jour : vérifier l'accès web/SSH, les runners, les pipelines, les merge requests, les branches protégées et les journaux. Si exposition passée suspectée, examiner les journaux et envisager la rotation des secrets CI/CD.

**Self-Hosted AI Gateway :** la CVE-2026-90970 (CVSS 9.9, RCE) concerne ce composant distinct de GitLab CE/EE ; appliquer la version AI Gateway corrigée si Duo Agent Platform auto-hébergé est utilisé. Aucun nouvel avis AI Gateway n'a été confirmé aujourd'hui.

## 3. Proxmox VE 9 — stabilité et maintenance

| Composant | Dernière version référencée dans les index no-subscription contrôlés |
|---|---|
| `pve-manager` | **9.2.21** (29 septembre) |
| `proxmox-kernel-7.0` | **7.0.14-24** (10 octobre) |
| `proxmox-kernel-signed-7.0` | **7.0.14+24** (10 octobre) |
| `pve-qemu-kvm` | **11.1.1-2** (10 octobre) |

Pas de nouvelle version de kernel 7.0 après `-24` dans l'index officiel vérifié. Les métadonnées ne remplacent pas un `apt policy` sur chaque hôte pour déterminer la version candidate exacte.

**Points de vigilance :** les correctifs récents des kernels `-22` à `-24` concernent notamment les pilotes réseau, PCIe/NVMe, RTC, SMB/CIFS et Btrfs. Vu les kernel panics déjà rencontrés sur plusieurs hôtes, conserver un noyau précédent bootable et valider `-24` sur un nœud pilote avant déploiement en masse. Vérifier le stockage, les NIC Intel/Broadcom/Realtek, les erreurs PCIe/AER, le démarrage des VM et la stabilité KVM après reboot.

**Action MCO P1 :** inventorier `pveversion -v`, `uname -r`, `apt policy proxmox-kernel-7.0 pve-manager pve-qemu-kvm` ; déployer en pilote, puis généraliser seulement après validation.

## 4. Plan d'action

1. **P0 GitLab :** corriger la RCE CI/CD non authentifiée CVE-2026-97229 en passant à 19.4.2, 19.3.5 ou 19.2.8.
2. **P1 Rocky :** remplacer 687.56.1 par **687.57.1**, RHSA-2026:81552, et redémarrer après contrôle.
3. **P1 Proxmox :** valider `7.0.14-24-pve` et `pve-qemu-kvm 11.1.1-2` sur pilote ; ne pas accélérer les changements sur les hôtes instables.

## Sources

- [Rocky Linux 9 BaseOS x86_64 — paquets kernel](https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D)
- [Red Hat — RHSA-2026:81552](https://access.redhat.com/errata/RHSA-2026:81552)
- [Red Hat — CVE-2026-68123 (Open vSwitch)](https://access.redhat.com/security/cve/cve-2026-68123)
- [Red Hat — CVE-2026-72318 (CIFS/DFS)](https://access.redhat.com/security/cve/cve-2026-72318)
- [Red Hat — CVE-2026-63925 (MACsec XPN)](https://access.redhat.com/security/cve/cve-2026-63925)
- [Red Hat — CVE-2026-64507 (BPF JIT)](https://access.redhat.com/security/cve/cve-2026-64507)
- [GitLab — Critical Patch Release du 12 octobre 2026](https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/)
- [Proxmox — kernel 7.0 no-subscription](https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/)
- [Proxmox — kernel signé 7.0 no-subscription](https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-signed-7.0/)
- [Proxmox — pve-manager](https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/)
- [Proxmox — pve-qemu-kvm](https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-qemu-kvm/)
