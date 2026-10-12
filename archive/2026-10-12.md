# Brief MCO — 12 octobre 2026

## Résumé exécutif

**CRITIQUE — GitLab CE/EE : nouvelle Critical Patch Release 19.4.2 / 19.3.4 / 19.2.8.** CVE-2026-97229 est une RCE CI/CD **sans authentification, CVSS 10.0**. Mise à jour immédiate. Attention : 19.3.4 présente un bug connu sur les pages de MR avec intégration Ona activée.

**IMPORTANT — Rocky/RHEL 9 :** kernel Rocky **5.14.0-687.56.1.el9_8**, publié le 8 octobre, remplace 687.54.1. RHSA-2026:76739 porte un lot de 38 CVE kernel.

**PROXMOX :** nouveau changelog kernel **7.0.14-24-pve** daté du 10 octobre dans pve-no-subscription. Validation pilote avant généralisation.

## 1. GitLab Self-Managed CE/EE

Publication officielle du 12 octobre : versions corrigées **19.2.8, 19.3.4, 19.4.2**. Les versions 19.2.x avant 19.2.8, 19.3.x avant 19.3.4 et 19.4.x avant 19.4.2 sont affectées par les failles principales ci-dessous.

| CVE | CVSS | Impact | Vecteur et prérequis |
|---|---:|---|---|
| **CVE-2026-97229** | **10.0 Critical** | **RCE serveur via compilateur regex de configuration CI/CD** | Réseau, **sans authentification**, faible complexité, sans interaction ; toutes les branches citées |
| CVE-2026-92883 | 8.7 High | XSS dans la coloration syntaxique, exécution JS dans session victime | Réseau, authentifié faible privilège, interaction victime ; depuis 19.1 |
| CVE-2026-16301 | 8.5 High | **Bypass d'autorisation** : exécution de pipelines sur branches protégées | Réseau, authentifié rôle Developer, sans interaction ; depuis 18.7 |
| CVE-2026-14814 | 8.2 High | Empoisonnement de cache de fichiers dépôt, contenu CI/CD non attendu, possible exposition variables protégées | Réseau, authentifié faible privilège, complexité élevée, sans interaction |
| CVE-2026-81839 | 7.7 High | Exécution de pipelines avec privilèges du nouveau groupe après transfert | GitLab EE, authentifié faible privilège, complexité élevée, interaction requise |
| CVE-2026-96893 | 7.3 High | Injection d'instructions Duo Agent Platform, commandes attribuées à autrui | GitLab EE, authentifié faible privilège, interaction requise |
| CVE-2026-101168 | 6.2 Medium | **Fuite de tokens/secrets** via logs GitLab Pages | Authentifié avec accès aux logs Pages, privilèges élevés, pas d'interaction |
| CVE-2026-7767 | 5.8 Medium | Fuite potentielle de tokens via redirection proxy Kubernetes | Authentifié contrôlant un agent Kubernetes, complexité élevée, interaction requise |
| CVE-2026-16275 | 5.3 Medium | Lecture non authentifiée de notes confidentielles de projets publics via GraphQL | Réseau, **sans authentification**, sans interaction |
| CVE-2026-84284 | 5.3 Medium | Lecture non authentifiée de contenus work items privés via GraphQL | Réseau, **sans authentification**, sans interaction |
| CVE-2026-85529 | 5.3 Medium | Lecture non authentifiée de notes internes/confidentielles via GraphQL | Réseau, **sans authentification**, sans interaction |

**Urgence :** patcher immédiatement les instances Self-Managed. Pour une instance Omnibus Rocky 9 encore en 19.3.2, préparer sauvegarde, vérifier le verrouillage DNF puis viser **19.3.4** au minimum. Si l'intégration **Ona** est activée, tenir compte du bug connu de 19.3.4 sur les pages MR et vérifier la disponibilité du correctif 19.3.5 avant le déploiement. Ne pas retarder sans mesure compensatoire l'atténuation de la RCE non authentifiée.

Les vulnérabilités antérieures CI/CD CVE-2026-89078 et CVE-2026-93577 (CVSS 9.9) restent pertinentes pour les installations qui n'ont pas reçu les patchs précédents. CVE-2026-90970 (CVSS 9.9) concerne spécifiquement le **Self-Hosted AI Gateway**, dont les versions corrigées sont 19.2.4, 19.3.2 et 19.4.1.

## 2. Rocky Linux / RHEL 9

**Nouvelle cible Rocky 9 x86_64 : `5.14.0-687.56.1.el9_8`**, paquet publié le 8 octobre. L'erratum Red Hat **RHSA-2026:76739** indique un correctif kernel pour de nombreuses CVE ; la branche RHEL de l'erratum référence aussi 687.55.1, tandis que Rocky publie 687.56.1.

**CVE du lot RHSA-2026:76739 (38) :**
CVE-2025-40149, CVE-2026-23442, CVE-2026-43332, CVE-2026-45861, CVE-2026-46076, CVE-2026-46275, CVE-2026-46317, CVE-2026-52942, CVE-2026-52986, CVE-2026-53049, CVE-2026-53357, CVE-2026-63829, CVE-2026-63920, CVE-2026-63922, CVE-2026-63924, CVE-2026-63984, CVE-2026-63992, CVE-2026-63993, CVE-2026-63994, CVE-2026-64385, CVE-2026-64557, CVE-2026-68432, CVE-2026-72052, CVE-2026-72099, CVE-2026-72255, CVE-2026-72289, CVE-2026-74516, CVE-2026-74569, CVE-2026-74669, CVE-2026-74746, CVE-2026-80792, CVE-2026-80921, CVE-2026-89480, CVE-2026-89481, CVE-2026-89972, CVE-2026-90227, CVE-2026-93288, CVE-2026-97417.

**Priorisation :** l'erratum inclut des corrections kernel importantes ; la criticité/CVSS individuelle et les prérequis de chaque CVE doivent être lus dans les fiches Red Hat correspondantes, sans leur attribuer artificiellement un score commun. La présence de fonctionnalités kernel spécifiques (KVM, réseau, pilotes) conditionne l'exposition. Prioriser les serveurs multi-utilisateurs, exposés et hyperviseurs.

**Action MCO :** déployer le kernel du dépôt Rocky, redémarrer en fenêtre contrôlée et vérifier `uname -r`, modules et services critiques. Conserver un noyau de secours.

## 3. Proxmox VE

Le dépôt officiel de changelogs pve-no-subscription référence **proxmox-kernel-7.0 7.0.14-24** (10 octobre), après 7.0.14-23 (8 octobre). Cela confirme la disponibilité des métadonnées de release ; vérifier `apt policy` local pour la version exacte du paquet signé et son dépôt avant déploiement.

**Action MCO :** évaluer les changements entre 7.0.14-23 et -24 ; ne pas déduire du seul index de changelogs que toutes les régressions sont corrigées. Pilote sur un nœud, contrôle réseau (bnxt_en, r8169), RTC, PCIe/AER, stockage, KVM et redémarrage VM. Conserver un noyau précédent bootable, particulièrement pour les hôtes ayant subi des kernel panics.

## Actions prioritaires

1. **P0 GitLab** : corriger CVE-2026-97229, CVSS 10.0, RCE sans authentification ; vérifier aussi CI/CD, GraphQL, tokens/secrets, autorisations branches protégées.
2. **P1 Rocky** : passer à 5.14.0-687.56.1.el9_8, avec reboot et vérification.
3. **P1 Proxmox** : valider 7.0.14-24-pve en pilote avant généralisation.

## Sources officielles

- GitLab Critical Patch du 12 octobre : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- Rocky 9 BaseOS x86_64 : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D
- Red Hat RHSA-2026:76739 : https://access.redhat.com/errata/RHSA-2026:76739
- Red Hat CVE-2026-46317 : https://access.redhat.com/security/cve/cve-2026-46317
- Proxmox kernel 7.0 no-subscription changelogs : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
