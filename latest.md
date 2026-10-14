---
type: mco-daily
date: 2026-10-14
timezone: Europe/Paris
status: watch
gitlab_ce_ee_targets: [19.4.2, 19.3.5, 19.2.8]
rocky9_kernel: 5.14.0-687.56.1.el9_8
proxmox9_kernel: 7.0.14-24-pve
proxmox_pve_manager: 9.2.21
---

# Brief MCO — 14 octobre 2026

## Résumé exécutif

**P0 GitLab :** pas de nouvelle Critical Patch Release identifiée depuis le 12 octobre. **CVE-2026-97229, CVSS 10.0 : RCE CI/CD sans authentification** sur les versions non corrigées. Cibles CE/EE : **19.4.2, 19.3.5, 19.2.8**. Ne pas rester sur 19.3.4 : le correctif du bug MR/Ona est inclus dans 19.3.5.

**P1 Rocky Linux 9 :** pas de nouveau kernel depuis le 8 octobre. Dernière cible x86_64 : **5.14.0-687.56.1.el9_8**.

**P1 Proxmox VE 9 :** pas de nouveau kernel identifié depuis le 10 octobre. Dernier changelog no-subscription : **7.0.14-24-pve** ; `pve-manager 9.2.21`. Déploiement pilote recommandé compte tenu des kernel panics/régressions observés sur le parc.

## GitLab Self-Managed CE/EE — sécurité

Dernière publication officielle vérifiée : **12 octobre 2026**, Critical Patch Release **19.4.2 / 19.3.5 / 19.2.8**. GitLab recommande la mise à jour immédiate des versions affectées.

| CVE | CVSS | Composant, impact et conditions |
|---|---:|---|
| **CVE-2026-97229** | **10.0 Critical** | **RCE serveur / CI/CD**, compilateur d'expressions régulières ; réseau, **sans authentification** (PR:N), faible complexité, aucune interaction (UI:N). |
| CVE-2026-92883 | 8.7 High | XSS dans la coloration syntaxique d'un dépôt ; réseau, authentifié à faibles privilèges, interaction victime requise. |
| CVE-2026-16301 | 8.5 High | **Bypass d'autorisation** dans les pipelines descendants sur branches protégées ; réseau, rôle Developer, sans interaction. |
| CVE-2026-14814 | 8.2 High | Empoisonnement du cache des fichiers de dépôt ; exécution de contenu CI/CD inattendu et possible **fuite de variables protégées** ; réseau, authentifié faible privilège, complexité élevée, sans interaction. |
| CVE-2026-81839 | 7.7 High | GitLab EE : pipelines exécutés avec privilèges d'un nouveau groupe après transfert de projet ; authentifié, complexité élevée, interaction requise. |
| CVE-2026-96893 | 7.3 High | GitLab EE / Duo Agent Platform : injection d'instructions pouvant entraîner une exécution de commandes attribuée à un autre utilisateur ; authentifié, interaction requise. |
| CVE-2026-101168 | 6.2 Medium | GitLab Pages : **exposition de tokens et secrets** dans logs et événements d'erreur ; utilisateur authentifié avec accès privilégié aux logs, aucune interaction. |
| CVE-2026-7767 | 5.8 Medium | Proxy Kubernetes : redirection pouvant divulguer des jetons ; utilisateur authentifié contrôlant un agent Kubernetes, complexité élevée, interaction requise. |
| CVE-2026-16275 | 5.3 Medium | GraphQL notes : lecture de notes confidentielles de projets publics **sans authentification**, aucune interaction. |
| CVE-2026-84284 | 5.3 Medium | GraphQL work items : lecture de contenus de projets/groupes privés **sans authentification**, aucune interaction. |
| CVE-2026-85529 | 5.3 Medium | GraphQL : lecture de notes internes/confidentielles de projets publics **sans authentification**, aucune interaction. |

**Versions affectées et corrigées :** pour CVE-2026-97229, les branches 19.2 antérieures à 19.2.8, 19.3 antérieures à 19.3.5 et 19.4 antérieures à 19.4.2 sont affectées. Pour les autres CVE, les versions initiales affectées varient selon la fonctionnalité (par exemple 18.7 pour CVE-2026-16301, 12.0 pour CVE-2026-101168, 17.4 pour CVE-2026-16275). Les trois versions correctives publiées couvrent les branches maintenues ; se référer aux plages individuelles dans l'avis GitLab.

**Action P0 :** sauvegarder GitLab Omnibus et la configuration/secrets, contrôler le verrouillage DNF, passer directement à **19.3.5** si l'instance est toujours en 19.3.2 ; valider Git over SSH, runners, pipelines, merge requests, Pages, GraphQL et authentification. L'intégration Ona activée expose un bug MR dans 19.3.4, corrigé en 19.3.5.

**AI Gateway :** CVE-2026-90970, CVSS 9.9, RCE dans le **Self-Hosted AI Gateway** avec Duo Agent Platform, distinct de la version GitLab CE/EE. Vérifier la présence de ce composant avant de considérer ce risque applicable ; versions AI Gateway corrigées précédemment publiées : 19.2.4, 19.3.2, 19.4.1.

## Rocky Linux / RHEL 9 — kernel

Le dépôt Rocky 9 BaseOS x86_64 ne présente pas de paquet kernel postérieur à **5.14.0-687.56.1.el9_8**, publié le **8 octobre 2026**. Pas de nouvelle cible kernel ce matin.

L'erratum RHEL **RHSA-2026:76739** du 8 octobre comprend notamment :

| CVE | CVSS Red Hat | Impact et prérequis | État RHEL 9 kernel |
|---|---:|---|---|
| **CVE-2026-74516** | **8.8** | KVM AMD / AVIC / virtualisation imbriquée : invité L1 pouvant interférer avec l'APIC de l'hôte et provoquer un DoS ; dépend de KVM AMD/nested. | Fixed, RHSA-2026:76739 |
| CVE-2026-46317 | 7.0 | KVM ARM64 : use-after-free de structures MMU imbriquées, DoS ou élévation locale ; non applicable aux hôtes x86_64. | Fixed, RHSA-2026:76739 |
| CVE-2026-63994 | 7.0 | Tunnels réseau / PMTUD : use-after-free, crash/DoS possible avec accès local et conditions réseau spécifiques. | Fixed, RHSA-2026:76739 |
| CVE-2026-89481 | 7.1 | Vulnérabilité réseau du lot ; vecteur réseau, privilèges faibles, aucune interaction (selon Red Hat). | Fixed, RHSA-2026:76739 |

**Point de vigilance :** CVE-2026-68432 (VXLAN, Red Hat CVSS 7.0) concerne le contrôle des privilèges entre namespaces réseau avec CAP_NET_ADMIN. Ne pas déduire sa correction de la seule présence dans une liste de CVE : contrôler explicitement le statut produit/erratum Red Hat avant de la déclarer corrigée.

**Action P1 :** terminer le déploiement de `5.14.0-687.56.1.el9_8`, redémarrer en fenêtre contrôlée, vérifier `uname -r` et conserver un noyau de secours. Pour les serveurs sans KVM ARM64, la CVE correspondante n'est pas pertinente matériellement.

## Proxmox VE 9 — noyau et gestionnaire

Le dépôt officiel **pve-no-subscription** référence au plus récent le changelog **proxmox-kernel-7.0 (7.0.14-24)**, daté du **10 octobre 2026**. `pve-manager` reste à **9.2.21** dans l'index contrôlé. Aucun nouveau `-25` n'a été trouvé au moment du contrôle.

Correctifs importants du kernel **7.0.14-24** :

- **PCIe #7969** : retour sur une modification de négociation de vitesse de lien provoquant la disparition de NIC/NVMe ou des erreurs PCIe fatales sur certains hôtes.
- **Intel ixgbe #7874** : corrections de stabilité pour certaines cartes Intel X550/E610.
- **CVE-2026-97612** : métadonnées de fragmentation réseau/Open vSwitch et corruption mémoire potentielle.
- **CVE-2026-97562** : use-after-free SMB/CIFS DFS pendant reconnexion/expiration d'automount.
- **CVE-2026-98279** : récupération Btrfs et prévention de basculements durables en lecture seule.
- Autres correctifs réseau, Btrfs et clés chiffrées dans le changelog officiel.

Le changelog Proxmox ne fournit pas de score CVSS individuel pour ces CVE : ne pas leur attribuer une criticité numérique non vérifiée.

**Action P1 :** valider `7.0.14-24-pve` sur un nœud pilote ; contrôler PCIe/AER, réseau, RTC, NVMe, RAID/stockage, KVM et redémarrage VM. Conserver un kernel antérieur fonctionnel et ne pas généraliser sans validation, particulièrement sur les hôtes ayant connu des kernel panics.

## Priorités opérationnelles

1. **P0 — GitLab** : confirmer la version déployée, patcher immédiatement les branches vulnérables vers 19.3.5 / 19.4.2 / 19.2.8, contrôler les intégrations CI/CD et les secrets.
2. **P1 — Rocky** : terminer le passage à 687.56.1, reboot et validation.
3. **P1 — Proxmox** : qualifier 7.0.14-24 sur pilote, maintenir un retour arrière bootable.
4. **Veille** : suivre une éventuelle nouvelle patch release GitLab aujourd'hui ; aucune publication plus récente que le 12 octobre n'a été confirmée lors du contrôle.

## Sources officielles et complémentaires

- GitLab, Critical Patch Release du 12 octobre : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- GitLab, AI Gateway Critical Patch : https://docs.gitlab.com/releases/patches/other-patches/patch-release-gitlab-ai-gateway-19-4-1-released/
- Rocky Linux 9 BaseOS x86_64, index des RPM kernel : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- Red Hat RHSA-2026:76739 : https://access.redhat.com/errata/RHSA-2026:76739
- Red Hat CVE-2026-74516 : https://access.redhat.com/security/cve/cve-2026-74516
- Red Hat CVE-2026-46317 : https://access.redhat.com/security/cve/cve-2026-46317
- Red Hat CVE-2026-63994 : https://access.redhat.com/security/cve/cve-2026-63994
- Red Hat CVE-2026-68432 : https://access.redhat.com/security/cve/cve-2026-68432
- Proxmox no-subscription, index kernel 7.0 : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- Proxmox no-subscription, index pve-manager : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- Proxmox, changelog détaillé kernel : https://github.com/proxmox/pve-kernel/blob/master/debian/changelog
