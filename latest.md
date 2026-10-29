---
type: mco-daily
date: 2026-10-29
generated_at: 2026-10-29T08:06:57+01:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 14
rocky_kernel_latest: "5.14.0-687.61.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — jeudi 29 octobre 2026

## Résumé exécutif

- **NOUVEAU — Rocky Linux/RHEL 9 :** noyau `5.14.0-687.61.1.el9_8` disponible (dépôt Rocky, 27 octobre), lié à **RHSA-2026:90365** du 26 octobre, niveau éditeur *Important*, avec **15 CVE kernel**. Redémarrage nécessaire.
- **NOUVEAU — GitLab :** tags officiels **19.5.3** et **19.4.5** datés du 28 octobre, **19.3.7** du 27 octobre. Le contenu exact de ces patchs n'est pas décrit dans les bulletins officiels consultés : ne pas leur attribuer de CVE non documentée.
- **P0 GitLab :** deux RCE sans authentification (CVE-2026-110264 et CVE-2026-97229, CVSS 10.0) corrigées dans les Critical Patch Releases des 21 et 12 octobre. Vérifier impérativement les instances Self-Managed.
- **P0 hyperviseurs :** PwnEscape (CVE-2026-99999) corrigée dans Proxmox depuis `7.0.14-24-pve` et dans RHEL/Rocky 9 depuis `687.59.1`. `qemu-server >= 9.2.11` corrige six autres avis Proxmox.
- **P1 :** correctifs Nginx 1.30.6/1.31.7 et MariaDB 10.11.19 à vérifier ; OpenSSH 10.6p1 reste la version amont identifiée.

## Méthode et comptage des CVE

Ce rapport compte **uniquement les CVE distinctes dont le score CVSS v3.1 numérique est confirmé et explicitement répertorié dans les tableaux de comptage ci-dessous**, sur toute la période de suivi (pas seulement les CVE du jour). **Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0**. Une même CVE ne compte qu'une fois même si elle concerne Rocky et Proxmox. Le score Red Hat (8.8) est retenu pour PwnEscape plutôt que le score CNA (9.3). Les CVE sans score individuel vérifié (notamment celles de RHSA-2026:90365, les cinq CVE Nginx et les avis Proxmox sans CVE) sont **exclues du comptage**, sans préjuger de leur gravité. Ces compteurs ne mesurent pas les vulnérabilités non corrigées de l'infrastructure : les versions réellement installées ne sont pas accessibles ici.

**Critiques comptées (4) :** CVE-2026-110264 (10.0), CVE-2026-97229 (10.0), CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).

**Importantes comptées (14) :** CVE-2026-15723 (8.2), CVE-2026-112212 (7.5), CVE-2026-92883 (8.7), CVE-2026-16301 (8.5), CVE-2026-14814 (8.2), CVE-2026-107823 (7.2), CVE-2026-107821 (8.0), CVE-2026-107818 (8.4), CVE-2026-107815 (8.5), CVE-2026-107814 (8.4), CVE-2026-102523 (8.0), CVE-2026-97380 (8.8), CVE-2026-86047 (7.7), CVE-2026-99999 (8.8).

## 1. Rocky Linux / RHEL 9 — NOUVEL ERRATUM, priorité P1

| Élément | Vérification |
| --- | --- |
| Kernel Rocky 9 x86_64 le plus récent identifié | **`5.14.0-687.61.1.el9_8`** |
| Dépôt officiel Rocky | Fichier RPM daté **27 octobre 2026, 16:40 UTC** |
| Erratum Red Hat | **RHSA-2026:90365**, publié **26 octobre 2026**, gravité éditeur **Important** |
| Périmètre RHSA | RHEL 9 / 9.8 EUS, kernel ; 15 CVE corrigées |
| Redémarrage | **Obligatoire** pour activer le nouveau kernel |

**CVE explicitement listées dans RHSA-2026:90365 :** CVE-2024-57974 (UDP), CVE-2026-53388 (FUSE), CVE-2026-63978 et CVE-2026-63979 (net/handshake), CVE-2026-64266 (FUSE), CVE-2026-64523 (net/handshake), CVE-2026-68476 (IPVS), CVE-2026-72242 (SELinux/SCTP), CVE-2026-74515 (KVM s390), CVE-2026-80824 (USB), CVE-2026-89638 (SMB client), CVE-2026-89777 (VFIO/PCI), CVE-2026-93262 (RAID5 PPL), CVE-2026-98222 (encrypted keys) et CVE-2026-98320 (netfilter flowtable). Les **CVSS, vecteurs, privilèges et interactions individuels** n'ont pas été vérifiés pour cette série : ne pas supposer une RCE distante ni attribuer artificiellement la gravité de l'erratum à chaque CVE. L'erratum mentionne également une correction de kernel panic liée à des accès mémoire incorrects.

**Rappel PwnEscape :** CVE-2026-99999, CVSS Red Hat **8.8**, vecteur **local**, complexité faible, privilèges faibles, interaction nulle, portée changée, confidentialité/intégrité/disponibilité élevées. Correctif antérieur RHSA-2026:86033, kernel `5.14.0-687.59.1.el9_8` (20–21 octobre). Les noyaux .60.1 et .61.1 sont postérieurs. L'exploitation concerne l'isolation invité/hôte selon le contexte de virtualisation, pas un service TCP accessible sans authentification.

**Actions défensives :** `dnf --refresh updateinfo list --security`, `dnf updateinfo info RHSA-2026:90365`, `dnf update kernel\*` selon politique de maintenance, puis redémarrage contrôlé ; vérifier `rpm -q kernel-core`, `uname -r`, modules externes, stockage, réseau, Icinga et journaux. Contrôler l'espace disponible dans `/boot` avant mise à jour. Sur Rocky, confirmer le mapping d'errata local, les miroirs et la disponibilité effective du RPM.

Sources officielles :
- https://access.redhat.com/errata/RHSA-2026:90365
- https://access.redhat.com/errata/RHSA-2026:86033
- https://access.redhat.com/security/cve/cve-2026-99999
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## 2. Proxmox VE — kernel distinct de pve-manager

| Composant | Dernière version vérifiée dans **pve-no-subscription** | Commentaire |
| --- | --- | --- |
| Proxmox VE | **9.2** | Branche de référence |
| **pve-manager** | **9.2.24** | Changelog présent, 24 octobre |
| **Kernel Proxmox** | **7.0.14-26-pve** | Paquet `proxmox-kernel-7.0_7.0.14-26`, 24 octobre |
| **qemu-server** | **9.2.13** | Changelog présent, 24 octobre |
| Seuil correctif PwnEscape | **7.0.14-24-pve(-signed)** | PSA-2026-00068 |
| Seuil correctif six avis VM | **qemu-server >= 9.2.11** | PSA-2026-00069 à 00074 |

**PwnEscape, CVE-2026-99999 :** Proxmox confirme une sortie de VM KVM possible depuis l'invité par mauvaise validation de métadonnées de checksum réseau virtio. Le correctif `-24` et les versions postérieures sont à privilégier ; sur les hôtes Secure Boot, vérifier l'installation de la variante **signée** et l'amorçage avant redémarrage. Le signalement communautaire isolé de difficulté de boot du `-25` ne suffit pas à conclure à une régression générale du `-26` ; aucune régression du `-26` n'est confirmée dans les sources examinées.

**Six avis du 19 octobre (sans CVSS individuels confirmés) :**

| Avis | Impact | Prérequis |
| --- | --- | --- |
| PSA-2026-00069 | Fuite du mot de passe cloud-init dans la configuration de snapshot | Accès autorisé à l'endpoint de configuration de snapshot |
| PSA-2026-00070 | Lecture de snippets d'un stockage non autorisé | VM.Config.Cloudinit + droit de démarrer la VM + accès invité |
| PSA-2026-00071 | Modification non autorisée de paramètres cloud-init d'authentification | VM.Config.Network |
| PSA-2026-00072 | Épuisement mémoire hôte via réponses QEMU Guest Agent | Contrôle d'une VM avec agent activé |
| PSA-2026-00073 | DoS pvedaemon via lecture QEMU Guest Agent | Agent invité malveillant |
| PSA-2026-00074 | Exposition potentielle de mémoire via commandes QEMU HMP | Privilège Sys.Modify, contrôle de privilège insuffisant |

**Action :** comparer `pveversion -v`, `uname -r`, `apt policy pve-manager qemu-server`, vérifier les dépôts réellement activés et le kernel signé, puis déployer en fenêtre de maintenance. Ne pas confondre version de `pve-manager`, version du paquet `proxmox-kernel` et `uname -r`. La disponibilité en dépôt n'est pas une preuve d'installation.

Sources officielles :
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/q/qemu-server/
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5

## 3. GitLab Self-Managed CE/EE — P0 immédiat

**Nouveaux tags officiels observés :** `v19.5.3-ee` et `v19.4.5-ee` datés du **28 octobre** ; `v19.5.2-ee`, `v19.4.4-ee` et `v19.3.7-ee` du **27 octobre**. La documentation GitLab confirme également 19.5.2, 19.4.4 et 19.3.7 dans ses mappings Helm. **Attention :** aucun bulletin officiel détaillant les CVE corrigées spécifiquement par ces tags des 27–28 octobre n'a pu être retrouvé dans la collecte. Il faut vérifier les notes de patch, le changelog et les paquets disponibles avant de qualifier ces releases de « Security Patch » ou de leur attribuer des CVE. Les deux Critical Patch Releases documentées les plus récentes restent celles des **12 et 21 octobre**.

| CVE | CVSS | Impact, vecteur et prérequis | Versions affectées / corrigées |
| --- | ---: | --- | --- |
| **CVE-2026-110264** | **10.0** | **RCE serveur sans authentification ni interaction**, compilation de regex ; **AV:N/AC:L/PR:N/UI:N** | Depuis 7.0, avant les correctifs de chaque branche : **18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3, 19.5.1** |
| **CVE-2026-97229** | **10.0** | **RCE CI/CD sans authentification ni interaction** via regex de configuration ; **AV:N/AC:L/PR:N/UI:N** | 19.2 < 19.2.8, 19.3 < 19.3.5, 19.4 < 19.4.2 |
| CVE-2026-15723 | 8.2 | Prise de contrôle d'un compte LDAP ; authentification initiale requise, pas d'interaction de la victime ; OmniAuth SAML + LDAP auto-link activés | Depuis 10.1, avant 19.3.6 / 19.4.3 / 19.5.1 |
| CVE-2026-112212 | 7.5 | Empoisonnement du cache de découverte OAuth/OIDC ; sans authentification, interaction ultérieure requise | Depuis 18.3, avant 19.3.6 / 19.4.3 / 19.5.1 |
| CVE-2026-92883 | 8.7 | XSS via coloration syntaxique d'un dépôt ; utilisateur authentifié, interaction de la victime | 19.1 à avant 19.2.8, 19.3.5 ou 19.4.2 selon branche |
| CVE-2026-16301 | 8.5 | Déclenchement non autorisé de pipelines sur branches protégées ; rôle Developer | Depuis 18.7, avant 19.2.8 / 19.3.5 / 19.4.2 |
| CVE-2026-14814 | 8.2 | Cache empoisonné de fichiers bruts, exécution de contenu dans CI/CD et **risque de fuite de variables protégées** ; compte avec privilèges faibles | Avant 19.2.8 / 19.3.5 / 19.4.2 |

**Autres points de sécurité GitLab confirmés dans le bulletin du 21 octobre :** CVE-2026-12664 (CVSS 6.8, contournement de restrictions du token `read_api`, compte authentifié), CVE-2026-16255 (5.9, contournement de verrou SAML/LDAP EE), CVE-2026-105777 (4.3, **fuite de credentials internes dans les en-têtes HTTP**), CVE-2026-13319 (4.3, accès à des métadonnées privées), ainsi que des défauts d'autorisation GraphQL, d'AI Gateway, de cache et de gouvernance. Leurs prérequis et branches affectées doivent être examinés individuellement ; ne pas leur attribuer le vecteur de la RCE.

**Action P0 :** pour toute instance encore en 19.3.2, mettre à jour sans délai au minimum au-delà des seuils des deux RCE (19.3.6 ou version corrective plus récente vérifiée), sauvegarder puis valider runners, pipelines, tokens, secrets, SAML/LDAP, journaux et détecteurs d'exploitation publiés par GitLab. Privilégier la dernière maintenance **vérifiée** de la branche et le chemin officiel d'upgrade.

Sources officielles :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- https://gitlab.com/gitlab-org/gitlab/-/tags/v19.5.3-ee
- https://gitlab.com/gitlab-org/gitlab/-/tags/v19.4.5-ee
- https://gitlab.com/gitlab-org/gitlab/-/tags/v19.3.7-ee
- https://docs.gitlab.com/charts/installation/version_mappings/

## 4. OpenSSH

Version amont **10.6p1**, sortie le **6 octobre 2026** et toujours en tête des notes de version officielles consultées. Modifications de sécurité concernant notamment SFTP, GSSAPI, compression et validation des noms d'utilisateur. Aucune nouvelle CVE OpenSSH datée du 29 octobre n'est confirmée. Les numéros amont ne suffisent pas à qualifier les RPM Rocky/RHEL : contrôler les **backports** et errata distribués, la configuration `sshd`, les méthodes d'authentification et les ports exposés.

Source officielle : https://www.openssh.com/releasenotes.html

## 5. Nginx

Correctifs amont **1.30.6+ (stable)** et **1.31.7+ (mainline)**, pour cinq avis de gravité officielle *medium* :

| CVE | Fonctionnalité / impact | Condition principale |
| --- | --- | --- |
| CVE-2026-15176 | Débordement mémoire | `alias` avec `rewrite` |
| CVE-2026-93479 | Débordement mémoire | Évaluation de scripts Nginx |
| CVE-2026-95101 | Injection de corps HTTP | HTTP/3 |
| CVE-2026-97629 | Falsification de session | Tickets de session TLS |
| CVE-2026-97205 | Use-after-free | I/O asynchrones AIO |

Les CVSS individuels n'étant pas établis ici, ces CVE ne sont pas comptées dans les seuils numériques. Vérifier `nginx -V`, les modules compilés, la configuration effective et les éventuels backports de distribution. Attention : les vulnérabilités PHP-FPM ne sont pas automatiquement des CVE Nginx.

Source officielle : https://nginx.org/en/security_advisories.html

## 6. MariaDB

La version **10.11.19 LTS** (GA, **24 août 2026**) est confirmée dans les notes officielles de la branche 10.11. Les correctifs de sécurité incluent :

| CVE | CVSS v3.1 | Gravité selon la règle du rapport |
| --- | ---: | --- |
| **CVE-2026-92262** | **9.9** | **Critique** |
| **CVE-2026-85746** | **9.9** | **Critique** |
| CVE-2026-97380 | 8.8 | Importante |
| CVE-2026-107815 | 8.5 | Importante |
| CVE-2026-107818 | 8.4 | Importante |
| CVE-2026-107814 | 8.4 | Importante |
| CVE-2026-107821 | 8.0 | Importante |
| CVE-2026-102523 | 8.0 | Importante |
| CVE-2026-86047 | 7.7 | Importante |
| CVE-2026-107823 | 7.2 | Importante |

Les notes de version listent d'autres CVE sous le seuil de 7.0. Les **vecteurs, authentifications, privilèges, interactions et fonctionnalités exactes** des CVE MariaDB ne sont pas détaillés dans le tableau officiel consulté ; ils doivent être confirmés dans chaque fiche CVE avant d'affirmer qu'une RCE réseau sans authentification est possible. Les problèmes corrigés concernent aussi InnoDB, CONNECT, Galera, chiffrement et réplication.

**Action :** comparer `SELECT VERSION();` et les paquets effectivement installés à la branche maintenue, vérifier les correctifs de distribution, les plugins chargés et les restrictions réseau ; prévoir sauvegarde et tests de réplication avant mise à jour.

Source officielle : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Ordre de traitement défensif

1. **P0 GitLab :** mettre à jour les instances vulnérables aux deux RCE sans authentification ; vérifier l'intégrité des pipelines et la confidentialité des secrets.
2. **P0/P1 hyperviseurs :** confirmer kernel Proxmox corrigé, `qemu-server >= 9.2.11`, kernel signé si Secure Boot et absence de régressions après redémarrage.
3. **P1 Rocky/RHEL 9 :** qualifier et déployer **RHSA-2026:90365** / kernel **687.61.1**, avec redémarrage et contrôles d'exploitation.
4. **P1 Nginx / MariaDB / OpenSSH :** vérifier les versions corrigées, la configuration réellement exposée et les backports.
5. Surveiller les prochains bulletins GitLab décrivant les changements précis des tags **19.5.3 / 19.4.5 / 19.3.7**.

**Limites :** ce rapport ne dispose pas de l'inventaire des paquets effectivement installés sur les hôtes ; l'urgence par machine dépend de la version et des fonctions exposées. Les CVSS non vérifiés sont volontairement exclus des compteurs. Aucun mode opératoire offensif n'est fourni.
