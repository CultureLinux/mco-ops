---
type: mco-daily
date: 2026-10-30
generated_at: 2026-10-30T08:04:53+01:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 16
rocky_kernel_latest: "5.14.0-687.61.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — vendredi 30 octobre 2026

## Résumé exécutif
- **NOUVEAU / P0 — GitLab CE/EE :** bulletin officiel du **29 octobre** confirmant **CVE-2026-111943** : révocation de confiance pour **huit certificats intermédiaires eMudhra compromis**. Mise à jour **GitLab ET Runner ET images CI/CD** nécessaire ; la mise à jour de GitLab seule ne corrige pas les runners.
- **P0 — GitLab :** deux RCE sans authentification **CVE-2026-110264** et **CVE-2026-97229** (CVSS 10.0) corrigées dans les bulletins des 21 et 12 octobre.
- **P1 — Rocky/RHEL 9 :** kernel **5.14.0-687.61.1.el9_8** (RHSA-2026:90365, 15 CVE) ; redémarrage requis.
- **P1 — Proxmox VE 9.2 :** **pve-manager 9.2.24**, **kernel 7.0.14-26-pve**, **qemu-server 9.2.13** identifiés en no-subscription ; vérifier PwnEscape et les avis qemu-server.
- **P1 — Nginx / MariaDB / OpenSSH :** maintenir les correctifs, surveiller les fonctionnalités exposées et les backports.

## Périmètre, comptage et limites
Les compteurs YAML recensent les **CVE distinctes explicitement notées ci-dessous avec un score CVSS v3.1 numérique confirmé dans les avis fournisseurs consultés**, y compris les vulnérabilités antérieures encore pertinentes, et **pas uniquement celles annoncées aujourd'hui**. Seuils : **critical = CVSS >= 9.0** ; **important = 7.0 <= CVSS < 9.0**. Une CVE partagée entre Rocky et Proxmox n'est comptée qu'une fois ; pour PwnEscape, score **Red Hat 8.8** retenu plutôt que le score CNA. Les avis sans score individuel confirmé sont exclus des compteurs, sans présumer qu'ils sont bénins. Ces comptes ne mesurent **pas** l'exposition réelle de vos serveurs.

### CVE critiques comptées (4)
| CVE | CVSS v3.1 | Produit / impact |
| --- | ---: | --- |
| CVE-2026-110264 | 10.0 | GitLab RCE non authentifiée |
| CVE-2026-97229 | 10.0 | GitLab RCE CI/CD |
| CVE-2026-92262 | 9.9 | MariaDB |
| CVE-2026-85746 | 9.9 | MariaDB |

### CVE importantes comptées (16)
| CVE | CVSS v3.1 | Produit / impact |
| --- | ---: | --- |
| CVE-2026-15723 | 8.2 | GitLab SAML/LDAP |
| CVE-2026-112212 | 7.5 | GitLab OAuth/OIDC |
| CVE-2026-92883 | 8.7 | GitLab XSS |
| CVE-2026-16301 | 8.5 | GitLab pipelines protégés |
| CVE-2026-14814 | 8.2 | GitLab CI/CD cache |
| CVE-2026-81839 | 7.7 | GitLab politiques de pipeline |
| CVE-2026-96893 | 7.3 | GitLab Duo Agent |
| CVE-2026-107823 | 7.2 | MariaDB |
| CVE-2026-107821 | 8.0 | MariaDB |
| CVE-2026-107818 | 8.4 | MariaDB |
| CVE-2026-107815 | 8.5 | MariaDB |
| CVE-2026-107814 | 8.4 | MariaDB |
| CVE-2026-102523 | 8.0 | MariaDB |
| CVE-2026-97380 | 8.8 | MariaDB |
| CVE-2026-86047 | 7.7 | MariaDB |
| CVE-2026-99999 | 8.8 | Kernel PwnEscape (Red Hat) |

**CVE-2026-111943 : score CVSS v3.1 non fourni dans le bulletin GitLab consulté ; exclue du comptage**, malgré la priorité opérationnelle élevée liée à la compromission d'autorités de certification. Les 15 CVE de RHSA-2026:90365 sans scores individuels vérifiés, les cinq CVE Nginx et les PSA Proxmox sans CVSS ne sont pas ajoutés aux compteurs. Le champ « version la plus récente » désigne le dernier paquet vérifié dans les dépôts précisés à l'instant de la collecte, pas la version effectivement installée.

## 1. GitLab Self-Managed CE/EE — P0, nouvelle mise à jour de sécurité

### NOUVEAU : bulletin GitLab du 29 octobre — CVE-2026-111943
Le bulletin officiel **GitLab Patch Release and Security Update** confirme que les versions **19.5.3, 19.4.5, 19.3.7, 19.2.10, 19.1.10, 19.0.11 et 18.11.14** suppriment la confiance envers **huit certificats intermédiaires eMudhra dont les clés privées sont compromises**. Il s'agit d'un **correctif de sécurité**, et non d'une simple mise à jour de bugs.

- **Impact :** des certificats signés par les intermédiaires compromis pourraient permettre l'usurpation d'identité TLS et des attaques de type interception/modification du trafic lorsque l'attaquant peut se placer sur le chemin réseau et qu'une chaîne est encore acceptée.
- **Vecteur / authentification / privilèges / interaction :** risque de validation TLS et d'usurpation de serveur ; pas de RCE GitLab directe établie par ce bulletin ; aucun score CVSS ni vecteur officiel GitLab indiqué. Ne pas inventer de prérequis PR/UI ni de score.
- **Versions CE/EE affectées :** avant 18.11.14 ; branche 19.0 avant 19.0.11 ; 19.1 avant 19.1.10 ; 19.2 avant 19.2.10 ; 19.3 avant 19.3.7 ; 19.4 avant 19.4.5 ; 19.5 avant 19.5.3.
- **GitLab Runner self-hosted :** **19.5.1** requis selon le bulletin. **La mise à jour de GitLab n'actualise pas Runner** ; reconstruire les images de jobs CI/CD embarquant leur propre paquet de certificats CA.
- **GitLab AI Gateway self-hosted :** versions correctives **18.11.8, 19.0.13, 19.1.9, 19.2.5, 19.3.3, 19.4.3 et 19.5.3** selon la branche.
- **Régressions / migrations :** les branches 19.5.3 et 19.4.5 incluent des migrations de base de données ; GitLab avertit d'un arrêt sur une instance mono-nœud pendant la migration. Les numéros 19.5.2 et 19.4.4 ont été sautés.
- **Action :** prioriser GitLab, runners shell/Docker, images de build, bundles CA, accès OAuth/OIDC et services TLS internes ; sauvegarder, vérifier les migrations et les pipelines après mise à jour.

Source officielle : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

### Rappels : Critical Patch Releases des 21 et 12 octobre
| CVE | CVSS | Vecteur, privilèges, interaction, fonction et versions |
| --- | ---: | --- |
| **CVE-2026-110264** | **10.0** | **RCE réseau sans authentification ni interaction** ; AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H ; compilation d'expressions régulières. GitLab CE/EE depuis 7.0, correctifs 18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3, 19.5.1 selon branche. |
| **CVE-2026-97229** | **10.0** | **RCE CI/CD sans authentification ni interaction** ; AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H ; branches 19.2 <19.2.8, 19.3 <19.3.5, 19.4 <19.4.2. |
| CVE-2026-15723 | 8.2 | Prise de contrôle de compte LDAP ; utilisateur authentifié à faible privilège, pas d'interaction de la victime ; OmniAuth SAML + liaison LDAP ; corrigée 19.3.6/19.4.3/19.5.1. |
| CVE-2026-112212 | 7.5 | Cache OAuth/OIDC empoisonnable sans authentification, interaction ultérieure requise ; corrigée 19.3.6/19.4.3/19.5.1. |
| CVE-2026-92883 | 8.7 | XSS via dépôt et coloration syntaxique ; utilisateur authentifié, interaction requise ; corrigée 19.2.8/19.3.5/19.4.2. |
| CVE-2026-16301 | 8.5 | Création de pipelines sur branches protégées ; rôle Developer, pas d'interaction ; corrigée 19.2.8/19.3.5/19.4.2. |
| CVE-2026-14814 | 8.2 | Empoisonnement du cache des fichiers de dépôt, impact CI/CD et variables protégées ; utilisateur à faible privilège, complexité élevée ; corrigée 19.2.8/19.3.5/19.4.2. |
| CVE-2026-81839 | 7.7 | Politique de pipelines planifiés, privilèges d'un projet transféré ; authentification et interaction requises ; corrigée 19.2.8/19.3.5/19.4.2. |
| CVE-2026-96893 | 7.3 | Injection d'instructions dans Duo Agent, exécution de commandes potentielle ; authentification et interaction requises ; corrigée 19.2.8/19.3.5/19.4.2. |

Autres points notables **hors seuil de comptage** : CVE-2026-105777 (exposition d'identifiants internes), CVE-2026-12664 (contournement de restrictions du token read_api), CVE-2026-101168 (secrets/tokens dans les journaux GitLab Pages), CVE-2026-7767 (tokens Kubernetes redirigés), et CVE-2026-14288 (contournement de politique d'approbation). Les vecteurs et versions exactes de ces CVE sont détaillés par GitLab. **Ne pas déduire de l'absence de score élevé une absence de risque pour les secrets.**

Sources officielles :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 2. Rocky Linux / RHEL 9 — P1, kernel et errata
- **Kernel Rocky 9 x86_64 le plus récent vérifié dans BaseOS : 5.14.0-687.61.1.el9_8**, fichier daté du **27 octobre 2026 à 16:40 UTC** ; pas de version plus récente identifiée lors de cette vérification.
- **RHSA-2026:90365**, publié le **26 octobre**, niveau Red Hat **Important**, apporte **15 correctifs CVE** (FUSE, UDP, net/handshake, IPVS, SELinux, USB, SMB, VFIO, RAID5, KEYS, netfilter). Les 15 identifiants incluent CVE-2024-57974 et 14 CVE-2026 ; les scores individuels ne sont pas tous vérifiés et ne sont pas ajoutés aux compteurs.
- **PwnEscape CVE-2026-99999**, score Red Hat 8.8, concernait l'isolation VM/hôte. Le correctif avait été identifié depuis le kernel 687.59.1 ; la version 687.61.1 est postérieure.
- **Prérequis :** les défauts du noyau varient selon les fonctionnalités et permissions ; ne pas les assimiler globalement à des RCE réseau sans authentification.
- **Action :** vérifier les errata disponibles et installés, l'espace /boot, les modules tiers, puis redémarrer ; vérifier ensuite le kernel démarré (uname -r) et les journaux. Un RPM installé n'implique pas que le noyau soit actif.

Sources officielles :
- https://access.redhat.com/errata/RHSA-2026:90365
- https://access.redhat.com/errata/RHSA-2026:86033
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## 3. Proxmox VE 9.2 — P1, noyau distinct du manager
| Élément | Version vérifiée / état |
| --- | --- |
| Branche Proxmox VE | **9.2** (annonce du 21 mai 2026) |
| **pve-manager** | **9.2.24**, dépôt **pve-no-subscription**, changelog daté du 24 octobre |
| **Kernel Proxmox** | **7.0.14-26-pve**, dépôt **pve-no-subscription**, changelog daté du 24 octobre |
| **qemu-server** | **9.2.13**, dépôt **pve-no-subscription**, changelog daté du 24 octobre |
| Correction kernel PwnEscape | depuis **7.0.14-24-pve** selon les avis précédents |
| Correction six PSA qemu-server | **>= 9.2.11** |

Le numéro de version **pve-manager** ne décrit **pas** la version du kernel démarré. Les changelogs attestent de la présence de versions dans le dépôt consulté, pas de leur installation sur les nœuds. Aucune régression généralisée du kernel -26 n'a été confirmée ici ; contrôler le Secure Boot, les paquets signés, ZFS, HA, stockage et réseau avant redémarrage. Un signalement isolé antérieur concernait le démarrage Secure Boot du -25 : il ne suffit pas à conclure que le -26 est affecté.

Six avis Proxmox du **19 octobre**, corrigés dans qemu-server >= 9.2.11 : **PSA-2026-00069** (secret cloud-init dans snapshot), **00070** (snippets de stockage et permissions), **00071** (contournement des permissions cloud-init), **00072** (épuisement mémoire via réponses Guest Agent), **00073** (déni de service pvedaemon via Guest Agent), **00074** (divulgation potentielle via commandes QEMU HMP). Prérequis : droits délégués, accès à l'invité ou à l'agent selon l'avis. Scores CVSS individuels non confirmés.

**Attention historique :** l'avis Proxmox sur le contournement d'authentification des versions PVE 7 en fin de vie (CVE-2023-54391) ne s'applique pas aux versions PVE 9 corrigées. Ne pas exposer l'API de gestion à des réseaux non fiables.

Sources officielles :
- https://www.proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/q/qemu-server/
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5

## 4. OpenSSH — P1
**Dernière version amont vérifiée : 10.6p1 (6 octobre 2026).** Corrections de validation de chemins SFTP, gestion des credentials GSSAPI, atténuation de fuites de secrets par compression SSH partagée, durcissement des noms d'utilisateurs, vérification de l'option authorized_keys restrict et limites de taille de paquets compressés. Certaines failles nécessitent un serveur SFTP malveillant, GSSAPI activé ou des sessions de compression partagées ; aucune nouvelle CVE du 30 octobre n'a été confirmée. Sur RHEL/Rocky, les versions RPM utilisent des backports ; contrôler les errata plutôt que le seul numéro amont.

Source officielle : https://www.openssh.com/releasenotes.html

## 5. Nginx — P1
Cinq avis de gravité **medium** confirmés par Nginx ; **versions non vulnérables : 1.30.6+ et 1.31.7+** pour ces cinq CVE.

| CVE | Fonctionnalité / impact | Gravité Nginx |
| --- | --- | --- |
| CVE-2026-15176 | Débordement mémoire avec alias et rewrite | medium |
| CVE-2026-93479 | Débordement mémoire en évaluation de scripts | medium |
| CVE-2026-95101 | Injection de corps de requête HTTP/3 | medium |
| CVE-2026-97629 | Falsification de session avec tickets TLS | medium |
| CVE-2026-97205 | Use-after-free avec AIO | medium |

Les prérequis dépendent des modules et directives activés. **Scores CVSS numériques non confirmés ici** : exclus des compteurs. Vérifier les correctifs rétroportés par le distributeur, les modules réellement chargés et la configuration TLS/HTTP3/AIO.

Source officielle : https://nginx.org/en/security_advisories.html

## 6. MariaDB — P1
**MariaDB 10.11.19 LTS**, version stable publiée le **24 août 2026**, inclut les correctifs de sécurité confirmés par l'éditeur. **CVE critiques :** CVE-2026-92262 et CVE-2026-85746 (CVSS 9.9). **CVE importantes :** CVE-2026-107823 (7.2), CVE-2026-107821 (8.0), CVE-2026-107818 (8.4), CVE-2026-107815 (8.5), CVE-2026-107814 (8.4), CVE-2026-102523 (8.0), CVE-2026-97380 (8.8), CVE-2026-86047 (7.7). Les autres CVE corrigées sont listées dans les notes officielles.

Les prérequis précis (authentification, privilèges, interaction et fonctionnalités SQL/stockage/réplication) sont propres à chaque CVE ; **un CVSS 9.9 n'est pas la preuve d'une RCE distante sans authentification**. Vérifier le paquet serveur réellement exécuté, les RPM de distribution et leurs backports, puis valider les réplications et sauvegardes.

Source officielle : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Actions prioritaires
1. **P0 aujourd'hui :** déployer le correctif GitLab du 29 octobre ; mettre à jour **séparément Runner 19.5.1 et les images CI/CD** pour supprimer les intermédiaires CA compromis ; traiter les migrations GitLab.
2. **P0 :** vérifier que les deux RCE GitLab d'octobre sont corrigées ; rechercher les anomalies de compilation d'expressions régulières et d'exécution CI/CD, contrôler les secrets et tokens.
3. **P1 :** déployer le kernel Rocky 687.61.1, avec vérification de /boot, redémarrage et contrôle du noyau démarré.
4. **P1 :** contrôler pve-manager, kernel Proxmox et qemu-server **séparément** sur tous les nœuds ; prévoir une qualification de démarrage et HA.
5. **P1 :** vérifier les correctifs Nginx, MariaDB et OpenSSH, les modules exposés et les backports.

**Rapport strictement défensif : aucune instruction offensive.**
