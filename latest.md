---
type: mco-daily
date: 2026-10-28
generated_at: 2026-10-28T07:59:36+01:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 16
rocky_kernel_latest: "5.14.0-687.60.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — mercredi 28 octobre 2026

## Résumé exécutif

- **NOUVEAU — Proxmox VE 9.2 :** les dépôts officiels `pve-no-subscription` exposent désormais les changelogs **pve-manager 9.2.24**, **kernel 7.0.14-26-pve** et **qemu-server 9.2.13** (publications du 24 octobre). Le kernel -26 apporte des correctifs ZFS, LXC/cgroup, KVM et stabilité ; qualification recommandée avant redémarrage.
- **P0 — GitLab Self-Managed :** deux RCE sans authentification (CVE-2026-110264 et CVE-2026-97229, CVSS 10.0). Les Critical Patch Releases des 12 et 21 octobre restent impératives sur les instances non corrigées.
- **P0 — Hyperviseurs :** PwnEscape CVE-2026-99999, évasion possible d'une VM KVM ; correctif Proxmox depuis 7.0.14-24-pve et erratum RHEL 9 RHSA-2026:86033.
- **P1 — Rocky Linux 9 :** kernel x86_64 le plus récent observé dans BaseOS : `5.14.0-687.60.1.el9_8` (23 octobre). Correctifs précis du passage de .59 à .60 non attribués à un erratum dans cette collecte.
- **P1 — Nginx, MariaDB, OpenSSH :** correctifs amont disponibles, vérifier les paquets et backports sur les hôtes.

**Fenêtre de collecte :** sources officielles consultées le 28 octobre 2026 avant 08:00, heure de Paris. Aucun nouvel avis GitLab daté du 28 octobre n'a été confirmé à cette heure ; une publication ultérieure reste possible. Les versions observées dans les index de changelogs doivent être confirmées par `apt-cache policy` sur chaque nœud.

## Méthode de comptage CVE et limites

Les compteurs du front matter désignent les **CVE distinctes explicitement identifiées et assorties d'un CVSS numérique vérifié** dans les Critical Patch GitLab des 12 et 21 octobre, la fiche Red Hat de PwnEscape et les notes MariaDB 10.11.19. **Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0**. Chaque CVE n'est comptée qu'une fois, même si elle touche plusieurs produits. Pour PwnEscape, le score **Red Hat 8.8** est retenu pour le périmètre RHEL/Rocky, plutôt que le score CNA 9.3. Les avis sans CVSS numérique confirmé (notamment Proxmox et Nginx) sont décrits mais exclus. Les comptes ne sont ni le nombre de CVE apparues aujourd'hui ni l'inventaire exhaustif de toutes les vulnérabilités de ces logiciels.

**4 CVE critiques vérifiées :**

| CVE | CVSS | Source |
| --- | ---: | --- |
| CVE-2026-110264 | 10.0 | GitLab, 21 octobre |
| CVE-2026-97229 | 10.0 | GitLab, 12 octobre |
| CVE-2026-92262 | 9.9 | MariaDB 10.11.19 |
| CVE-2026-85746 | 9.9 | MariaDB 10.11.19 |

**16 CVE importantes vérifiées :**

| CVE | CVSS | Source |
| --- | ---: | --- |
| CVE-2026-15723 | 8.2 | GitLab, 21 octobre |
| CVE-2026-112212 | 7.5 | GitLab, 21 octobre |
| CVE-2026-92883 | 8.7 | GitLab, 12 octobre |
| CVE-2026-16301 | 8.5 | GitLab, 12 octobre |
| CVE-2026-14814 | 8.2 | GitLab, 12 octobre |
| CVE-2026-81839 | 7.7 | GitLab, 12 octobre |
| CVE-2026-96893 | 7.3 | GitLab, 12 octobre |
| CVE-2026-99999 | 8.8 | Red Hat (également Proxmox) |
| CVE-2026-107823 | 7.2 | MariaDB 10.11.19 |
| CVE-2026-107821 | 8.0 | MariaDB 10.11.19 |
| CVE-2026-107818 | 8.4 | MariaDB 10.11.19 |
| CVE-2026-107815 | 8.5 | MariaDB 10.11.19 |
| CVE-2026-107814 | 8.4 | MariaDB 10.11.19 |
| CVE-2026-102523 | 8.0 | MariaDB 10.11.19 |
| CVE-2026-97380 | 8.8 | MariaDB 10.11.19 |
| CVE-2026-86047 | 7.7 | MariaDB 10.11.19 |

## 1. Proxmox VE 9.2 — changements vérifiés

| Composant | Version vérifiée dans pve-no-subscription | Date index |
| --- | --- | --- |
| Branche Proxmox VE | 9.2 | sortie majeure/mineure 21/05/2026 |
| **pve-manager** | **9.2.24** | 24/10/2026 |
| **Kernel Proxmox** | **7.0.14-26-pve** (paquet source proxmox-kernel-7.0 7.0.14-26) | 24/10/2026 |
| **qemu-server** | **9.2.13** | 24/10/2026 |

**Important : `pve-manager` et le kernel sont des paquets différents.** Le numéro `9.2.24` n'est pas une version du kernel. Le dernier kernel doit être vérifié dans le dépôt effectivement configuré, puis avec `uname -r` après redémarrage.

**Nouveautés du kernel -26 (changelog officiel Proxmox) :** correctifs de données obsolètes en mémoire après rollback/réception ZFS (notamment conteneurs après réplication), course ARC pouvant provoquer un kernel panic sur datasets chiffrés répliqués, crashs possibles L2ARC, héritage des project quotas, processus LXC/cgroup tués immédiatement, erreurs de création de fichiers sous concurrence, course dm-crypt, crash KVM en virtualisation imbriquée, gestion des tables de pages des invités imbriqués, crashs/hangs inotify et io_uring, et lecture hors limites de filtres `tc flower`. Le changelog ne fournit pas de CVSS individuel pour ces corrections ; ne pas les compter comme nouvelles CVE. Le problème NVIDIA open GPU depuis -20 est également corrigé par un revert.

**Risque de régression :** les changements ZFS, cgroup/LXC, KVM et dm-crypt nécessitent une qualification sur les nœuds concernés ; aucune régression généralisée du -26 n'a été confirmée. Un signalement communautaire antérieur concernait un démarrage Secure Boot avec un paquet -25 non signé : vérifier le paquet `-signed`, le bootloader et conserver un kernel de secours avant toute maintenance.

**PwnEscape — PSA-2026-00068-1, CVE-2026-99999 :** évasion possible d'une VM KVM utilisant virtio-net avec un invité non fiable ; correction confirmée depuis **`7.0.14-24-pve(-signed)`**. Il faut exécuter le noyau corrigé, pas seulement l'avoir installé.

**Six avis du 19 octobre — qemu-server >= 9.2.11 (9.2.13 vérifié dans no-subscription) :**

| Avis | Impact | Prérequis / privilèges |
| --- | --- | --- |
| PSA-2026-00069 | Mot de passe cloud-init visible dans des snapshots | accès à la configuration de snapshot |
| PSA-2026-00070 | Lecture de snippets sur stockage non autorisé | VM.Config.Cloudinit, démarrage de VM et accès invité |
| PSA-2026-00071 | Modification de paramètres cloud-init d'authentification | VM.Config.Network ; conditions cloud-init |
| PSA-2026-00072 | Épuisement mémoire hôte via QEMU Guest Agent | contrôle d'une VM avec Guest Agent |
| PSA-2026-00073 | DoS pvedaemon via lecture de fichier Guest Agent | agent invité malveillant |
| PSA-2026-00074 | Divulgation potentielle de mémoire via QEMU HMP | Sys.Modify insuffisamment restreint |

CVSS individuels non publiés dans ces avis. **Contrôles défensifs :** `pveversion -v`, `apt-cache policy pve-manager qemu-server`, `uname -r`, `dpkg -l 'proxmox-kernel*'`, `proxmox-boot-tool status` et état Secure Boot. Après maintenance, vérifier les VM, la réplication ZFS, les conteneurs et les sauvegardes.

Sources officielles :
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/q/qemu-server/
- https://github.com/proxmox/pve-kernel/blob/master/debian/changelog
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5

## 2. GitLab Self-Managed CE/EE — CRITICAL

**Dernière Critical Patch vérifiée : 21 octobre 2026.** Versions corrigées : **18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3 et 19.5.1**. L'éditeur recommande une mise à jour immédiate des instances affectées, quels que soient les modes d'installation.

| CVE | CVSS | Vecteur / authentification / interaction | Impact et versions |
| --- | ---: | --- | --- |
| **CVE-2026-110264** | **10.0** | **AV:N/AC:L/PR:N/UI:N** | **RCE serveur sans authentification**, compilation de regex ; CE/EE depuis 7.0 avant le correctif propre à chaque branche |
| CVE-2026-15723 | 8.2 | AV:N/AC:H/PR:L/UI:N | **Prise de contrôle de compte LDAP** si OmniAuth SAML + auto-link LDAP ; depuis 10.1 avant 19.3.6/19.4.3/19.5.1 |
| CVE-2026-112212 | 7.5 | AV:N/AC:H/PR:N/UI:R | Empoisonnement de cache OAuth/OIDC ; depuis 18.3 avant les correctifs |
| CVE-2026-12664 | 6.8 | PR:L, token read_api | Contournement du périmètre GraphQL du token |
| CVE-2026-16255 | 5.9 | PR:L, EE avec verrou SAML/LDAP | Contournement de restrictions d'appartenance |
| CVE-2026-105777 | 4.3 | **Description « unauthenticated », CVSS publié PR:L : divergence** | Exposition de credentials internes dans les en-têtes ; ne pas affirmer PR:N sans clarification |
| CVE-2026-13319 | 4.3 | authentifié | Lecture de métadonnées de projets privés |

Autres correctifs du 21 octobre : CVE-2026-112378 (4.3, accès AI Gateway), CVE-2026-105711 (3.5, cache et confidentialité), CVE-2026-14288 (3.1, politique d'approbation), CVE-2026-112326 (3.1, suppression de constats), CVE-2026-103515 (2.7, verrou IA).

**Critical Patch du 12 octobre (versions initialement corrigées : 19.2.8, 19.3.5, 19.4.2) :**

| CVE | CVSS | Impact et conditions |
| --- | ---: | --- |
| **CVE-2026-97229** | **10.0** | **RCE CI/CD sans authentification ni interaction**, AV:N/AC:L/PR:N/UI:N ; 19.2 < 19.2.8, 19.3 < 19.3.5, 19.4 < 19.4.2 |
| CVE-2026-92883 | 8.7 | XSS via coloration syntaxique ; PR:L, UI:R |
| CVE-2026-16301 | 8.5 | Developer authentifié déclenchant des pipelines sur branches protégées ; PR:L, UI:N |
| CVE-2026-14814 | 8.2 | Empoisonnement de cache de fichiers source, **risque d'exécution CI/CD et de fuite de variables protégées** ; PR:L |
| CVE-2026-81839 | 7.7 | EE, pipelines planifiés après transfert de projet ; PR:L, UI:R |
| CVE-2026-96893 | 7.3 | EE Duo Agent Platform, injection d'instructions ; PR:L, UI:R |
| CVE-2026-101168 | 6.2 | Fuite de tokens/secrets dans les logs GitLab Pages ; accès aux journaux requis |

Autres vulnérabilités de lecture de données privées sans authentification : CVE-2026-16275, CVE-2026-84284 et CVE-2026-85529 (5.3 chacune), selon versions et API GraphQL.

**Action P0 :** une instance encore en `19.3.2` doit atteindre au minimum `19.3.6`, en suivant le chemin de migration GitLab, après sauvegarde. Contrôler migrations, runners, variables CI/CD, tokens, logs Pages et les détections défensives publiées par GitLab pour les deux RCE. Ne pas considérer un correctif du 12 octobre comme suffisant pour la vulnérabilité du 21 octobre.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- https://docs.gitlab.com/releases/

## 3. Rocky Linux / RHEL 9 — kernel, errata, CVE

**Dernier kernel Rocky 9 x86_64 vérifié dans BaseOS : `5.14.0-687.60.1.el9_8`**, daté du **23 octobre 2026 à 18:54 UTC**. L'erratum détaillant les différences entre `687.59.1` et `687.60.1` n'a pas été confirmé : ne pas attribuer à ce nouveau build des CVE non vérifiées.

**RHSA-2026:86033 du 20 octobre 2026** : erratum RHEL 9 kernel **`5.14.0-687.59.1.el9_8`**, classé Important. Il corrige notamment CVE-2026-99999 (PwnEscape), ainsi que des vulnérabilités Open vSwitch, TCP, bridge, SMB, perf, Bluetooth et IPv4 (CVE-2026-31678, CVE-2026-64024, CVE-2026-64032, CVE-2026-89633, CVE-2026-93817, entre autres). Les CVSS individuels des autres CVE ne sont pas inclus faute de vérification détaillée.

**PwnEscape :** CVSS Red Hat **8.8**, vecteur **AV:L/AC:L/PR:L/UI:N**, impacts élevés confidentialité/intégrité/disponibilité. Un attaquant local ou invité VM non fiable peut exploiter des métadonnées checksum IPv4 malformées pour corrompre la mémoire hôte. Le CNA attribue 9.3 avec PR:N ; les deux notations diffèrent. Red Hat classe ce défaut comme exploité activement. Le correctif nécessite un redémarrage.

**Contrôles :** `dnf updateinfo list --security`, `dnf list --showduplicates kernel-core`, `rpm -q kernel-core`, `uname -r`. Vérifier les errata propres à la distribution, l'architecture et les canaux EUS/SAP/RT ; valider les modules tiers et le réseau après redémarrage.

Sources :
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://access.redhat.com/errata/RHSA-2026:86033
- https://access.redhat.com/security/cve/cve-2026-99999

## 4. OpenSSH

**Version amont vérifiée : 10.6p1 (6 octobre 2026)**. Notes officielles : correctifs de sécurité et de stabilité concernant notamment SFTP, GSSAPI, compression et changements de compatibilité. L'option `scp -R` fait l'objet d'une dépréciation progressive. Aucune nouvelle CVE du 28 octobre confirmée avant cette collecte. Les CVSS et prérequis individuels non confirmés ne sont pas inventés. Sur Rocky/RHEL, vérifier les backports RPM et les errata avant de comparer la version au numéro amont.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx

Cinq avis de gravité **medium** chez l'éditeur, corrigés dans **1.30.6+ et 1.31.7+** :

| CVE | Fonctionnalité / impact | Plages affectées publiées |
| --- | --- | --- |
| CVE-2026-15176 | Débordement mémoire avec alias/rewrite | 0.1.29–1.31.6 |
| CVE-2026-93479 | Débordement mémoire lors de l'évaluation de scripts | 1.30.0–1.30.5 et 1.31.2–1.31.6 |
| CVE-2026-95101 | Injection de corps de requête HTTP/3 | 1.25.0–1.31.6 |
| CVE-2026-97629 | Falsification de session via tickets TLS | 1.23.2–1.31.6 |
| CVE-2026-97205 | Use-after-free avec AIO | 0.8.11–1.31.6 |

Les CVSS numériques et vecteurs complets ne sont pas vérifiés ici ; aucune de ces CVE n'entre dans les compteurs. L'exposition dépend des modules et directives activés. Vérifier `nginx -V`, `nginx -T`, les correctifs rétroportés et les options HTTP/3, AIO et tickets TLS.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB 10.11 LTS

**Version 10.11.19**, publiée le **24 août 2026**, confirmée par les notes officielles. **21 CVE** y sont documentées, dont **2 critiques et 8 importantes** au sens du barème de ce rapport.

- **Critiques :** CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).
- **Importantes :** CVE-2026-107823 (7.2), CVE-2026-107821 (8.0), CVE-2026-107818 (8.4), CVE-2026-107815 (8.5), CVE-2026-107814 (8.4), CVE-2026-102523 (8.0), CVE-2026-97380 (8.8), CVE-2026-86047 (7.7).

Les vecteurs, privilèges, authentifications et interactions exacts ne sont pas détaillés dans le tableau de la release ; **un CVSS élevé ne signifie pas une RCE réseau sans authentification**. Vérifier `SELECT VERSION();`, les paquets, les comptes, l'exposition réseau, et les fonctions CONNECT, Galera, chiffrement et réplication. Sauvegarder et tester une restauration avant mise à jour.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Priorités opérationnelles

1. **P0** Mettre à jour GitLab Self-Managed contre les deux RCE, contrôler les runners, les pipelines et les secrets.
2. **P0** Vérifier que les hyperviseurs Proxmox et RHEL/Rocky exécutent réellement un kernel corrigé contre PwnEscape.
3. **P1 — NOUVEAU** Qualifier les paquets Proxmox **pve-manager 9.2.24**, **kernel 7.0.14-26** et **qemu-server 9.2.13**, avec tests ZFS/LXC/KVM et Secure Boot.
4. **P1** Vérifier les correctifs Nginx et MariaDB, puis les errata OpenSSH rétroportés.

Rapport exclusivement défensif. Aucune commande d'exploitation. Aucun inventaire des versions installées sur les hôtes n'a été effectué.
