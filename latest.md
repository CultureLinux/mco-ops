---
type: mco-daily
date: 2026-10-31
generated_at: 2026-10-31T08:07:05+01:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 16
rocky_kernel_latest: "5.14.0-687.61.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — samedi 31 octobre 2026

## Résumé exécutif
- **P0 — GitLab Self-Managed** : les deux RCE **sans authentification** (CVE-2026-110264 et CVE-2026-97229, CVSS 10.0) restent prioritaires sur les installations non corrigées. La mise à jour du 29 octobre traite aussi **CVE-2026-111943** (certificats eMudhra compromis) : **GitLab + Runner + images CI/CD + AI Gateway si utilisé**.
- **P0/P1 — hyperviseurs** : PwnEscape **CVE-2026-99999** (8.8 Red Hat ; sortie de VM possible) ; kernel Proxmox corrigé depuis 7.0.14-24-pve.
- **P1 — Rocky/RHEL 9** : kernel x86_64 **5.14.0-687.61.1.el9_8**, associé à l'erratum RHSA-2026:90365 (15 CVE).
- **P1 — Proxmox VE 9.2** : versions identifiées en pve-no-subscription : **pve-manager 9.2.24**, **kernel 7.0.14-26-pve**, **qemu-server 9.2.13**.
- **P1 — Nginx/MariaDB/OpenSSH** : appliquer les correctifs selon les modules et branches réellement déployés. Aucune nouvelle publication officielle datée du 31 octobre n'a été confirmée dans les sources vérifiées à 2026-10-31T08:07:05+01:00.

## Périmètre, méthode et limites des compteurs
**Compteurs YAML = CVE distinctes explicitement inscrites dans les deux tableaux de comptage ci-dessous**, y compris celles publiées avant aujourd'hui. Une même CVE présente sur plusieurs produits ne compte qu'une fois. **Critical : CVSS v3.1 >= 9.0 ; Important : 7.0 <= CVSS v3.1 < 9.0.** Pour PwnEscape, score fournisseur Red Hat **8.8**, différent du score CNA **9.3**, retenu pour éviter un double comptage. Les CVE sans score numérique individuellement vérifié ne figurent pas dans les compteurs. Ces chiffres ne sont ni un nombre de nouvelles CVE du jour, ni une mesure de l'exposition effective des serveurs.

### CVE critiques comptées (4)
| CVE | CVSS | Produit | Impact |
| --- | ---: | --- | --- |
| CVE-2026-110264 | 10.0 | GitLab | RCE sans authentification, regexp; 21/10 |
| CVE-2026-97229 | 10.0 | GitLab CI/CD | RCE sans authentification; 12/10 |
| CVE-2026-92262 | 9.9 | MariaDB 10.11.19 | Correction de sécurité |
| CVE-2026-85746 | 9.9 | MariaDB 10.11.19 | Correction de sécurité |

### CVE importantes comptées (16)
| CVE | CVSS | Produit | Impact |
| --- | ---: | --- | --- |
| CVE-2026-15723 | 8.2 | GitLab | Prise de contrôle LDAP avec SAML+auto-link |
| CVE-2026-112212 | 7.5 | GitLab | Cache OAuth/OIDC, PR:N/UI:R |
| CVE-2026-92883 | 8.7 | GitLab | XSS syntax highlighting, PR:L/UI:R |
| CVE-2026-16301 | 8.5 | GitLab | Bypass d'autorisation pipeline protégé, Developer |
| CVE-2026-14814 | 8.2 | GitLab | Empoisonnement cache et secrets CI/CD, PR:L |
| CVE-2026-81839 | 7.7 | GitLab EE | Exécution pipeline sous droits de groupe, PR:L/UI:R |
| CVE-2026-96893 | 7.3 | GitLab EE | Injection d'instructions Duo Agent, PR:L/UI:R |
| CVE-2026-99999 | 8.8 | RHEL/Proxmox kernel | PwnEscape, AV:L/PR:L/UI:N (score Red Hat) |
| CVE-2026-107823 | 7.2 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107821 | 8.0 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107818 | 8.4 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107815 | 8.5 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107814 | 8.4 | MariaDB | Correctif 10.11.19 |
| CVE-2026-102523 | 8.0 | MariaDB | Correctif 10.11.19 |
| CVE-2026-97380 | 8.8 | MariaDB | Correctif 10.11.19 |
| CVE-2026-86047 | 7.7 | MariaDB | Correctif 10.11.19 |

Sources des scores : bulletins GitLab des 12 et 21 octobre, Red Hat CVE-2026-99999 et notes MariaDB 10.11.19. Les conditions de toutes les CVE MariaDB ne sont pas précisées individuellement dans les notes de version : ne pas conclure à une RCE réseau sans authentification du seul score.

## 1. GitLab Self-Managed CE/EE — priorité CRITICAL
**Dernière mise à jour vérifiée : bulletin officiel du 29 octobre 2026.** Versions correctives pour **CVE-2026-111943** : **18.11.14, 19.0.11, 19.1.10, 19.2.10, 19.3.7, 19.4.5, 19.5.3**. Les versions antérieures de chacune de ces branches sont affectées. Le défaut concerne la confiance accordée à **huit certificats intermédiaires eMudhra dont les clés privées sont compromises**. Il ne s'agit pas d'une nouvelle RCE GitLab, et **le score CVSS n'a pas été confirmé dans le bulletin consulté** ; cette CVE est exclue des compteurs. Risque : confiance TLS indue, selon chaîne de certificats et position de l'attaquant. Action urgente : corriger **GitLab CE/EE**, **GitLab Runner auto-hébergé >= 19.5.1**, **GitLab AI Gateway** si utilisé (18.11.8 / 19.0.13 / 19.1.9 / 19.2.5 / 19.3.3 / 19.4.3 / 19.5.3 selon branche) et **reconstruire les images CI/CD** embarquant leur propre magasin de CA. Mettre à jour GitLab seul ne met pas à jour les runners. Vérifier aussi les migrations de base : 19.5.3 et 19.4.5 incluent des migrations pouvant imposer une indisponibilité sur instance mono-nœud.

**RCE antérieures toujours à corriger** :
- **CVE-2026-110264, CVSS 10.0**, correctif du 21 octobre : overflow dans le compilateur de regexp, RCE serveur sous certaines conditions. **AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H** ; aucune authentification, aucun privilège, aucune interaction. Affecte les branches 18.11 avant 18.11.13, 19.0 avant 19.0.10, 19.1 avant 19.1.9, 19.2 avant 19.2.9, 19.3 avant 19.3.6, 19.4 avant 19.4.3, 19.5 avant 19.5.1, ainsi que les versions antérieures indiquées par l'éditeur.
- **CVE-2026-97229, CVSS 10.0**, correctif du 12 octobre : RCE via configuration CI/CD et compilateur de regexp, **AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H**, sans authentification ni interaction. Affecte 19.2 avant 19.2.8, 19.3 avant 19.3.5, 19.4 avant 19.4.2.

**Autres scénarios prioritaires** :
- **CVE-2026-15723 (8.2)** : prise de contrôle de compte LDAP par utilisateur authentifié, **PR:L/UI:N**, si OmniAuth SAML et liaison LDAP automatique sont tous deux activés ; correctifs 19.3.6/19.4.3/19.5.1.
- **CVE-2026-112212 (7.5)** : empoisonnement de découverte OAuth/OIDC **PR:N/UI:R** ; mêmes versions correctives.
- **CVE-2026-16301 (8.5)** : un Developer peut lancer des pipelines sur branches protégées ; **PR:L/UI:N**, correctifs 19.2.8/19.3.5/19.4.2.
- **CVE-2026-14814 (8.2)** : empoisonnement de cache de fichiers pouvant entraîner l'exécution de contenu dans des pipelines et l'exposition de variables protégées ; **PR:L/UI:N**, mêmes versions.
- **CVE-2026-81839 (7.7)** : exécution de pipelines avec privilèges d'un autre groupe dans GitLab EE ; **PR:L/UI:R**.
- **CVE-2026-96893 (7.3)** : injection d'instructions dans Duo Agent, exécution de commandes potentielle au nom d'autres utilisateurs ; **PR:L/UI:R**.
- **CVE-2026-92883 (8.7)** : XSS de coloration syntaxique, **PR:L/UI:R**.
- **CVE-2026-105777 (4.3)** : exposition de credentials internes dans les en-têtes ; l'avis décrit un scénario sans authentification mais son vecteur CVSS indique PR:L. **Incohérence du bulletin à clarifier** : ne pas présenter le prérequis comme certain.
- **CVE-2026-101168 (6.2)** : exposition de tokens/secrets dans les logs GitLab Pages ; accès préalable aux logs requis.
- **CVE-2026-12664 (6.8)** : contournement de portée d'un token read_api ; authentification requise.

**Action** : priorité à la mise à jour de toute instance 19.3.2 vers **19.3.7 ou une branche supportée plus récente**, avec sauvegarde, vérification des migrations, runners, variables protégées, secrets, intégrations SAML/LDAP, journaux et pipelines.

Sources officielles :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 2. Rocky Linux / RHEL 9 — kernel, errata, CVE
**Dernier paquet kernel x86_64 identifié dans le dépôt Rocky Linux 9 BaseOS : `5.14.0-687.61.1.el9_8`**, daté du **27 octobre 2026**. **RHSA-2026:90365**, émis le **26 octobre**, classé **Important** par Red Hat : 15 CVE affectant notamment FUSE, UDP, SELinux, USB, RAID5, VFIO, netfilter et la gestion de clés.

CVE référencées par cet erratum (scores individuels non tous vérifiés, donc non inclus dans les compteurs) : **CVE-2024-57974, CVE-2026-53388, CVE-2026-63978, CVE-2026-63979, CVE-2026-64266, CVE-2026-64523, CVE-2026-68476, CVE-2026-72242, CVE-2026-74515, CVE-2026-80824, CVE-2026-89638, CVE-2026-89777, CVE-2026-93262, CVE-2026-98222, CVE-2026-98320**.

**PwnEscape — CVE-2026-99999** : Red Hat classe le risque **Important 8.8**, vecteur **AV:L/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:H**, tandis que le CNA donne **9.3** et **PR:N**. Un invité non fiable ou un acteur local peut, dans les conditions décrites, corrompre la mémoire du kernel hôte et potentiellement franchir l'isolation. Ne pas assimiler ce scénario à une RCE réseau sans authentification. La disponibilité d'un RPM n'implique pas que le serveur a redémarré dessus.

**Actions** : `dnf updateinfo list --security`, `dnf --refresh check-update kernel\*`, `rpm -q kernel-core`, `uname -r`. Contrôler l'espace de `/boot` avant mise à jour, prévoir redémarrage, puis vérifier noyau, modules et réseau.

Sources :
- https://access.redhat.com/errata/RHSA-2026:90365
- https://access.redhat.com/security/cve/cve-2026-99999
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## 3. Proxmox VE — kernel distinct de pve-manager
**Branche vérifiée : Proxmox VE 9.2.** Dernières entrées de changelog observées dans **pve-no-subscription** : **pve-manager 9.2.24**, **proxmox-kernel-7.0 7.0.14-26** (noyau démarrable `7.0.14-26-pve`) et **qemu-server 9.2.13** ; changelogs datés du **24 octobre**. Ce sont trois paquets distincts. La version effectivement installée dépend du dépôt APT et des mises à jour locales.

**PwnEscape CVE-2026-99999** : correctif kernel Proxmox annoncé depuis **7.0.14-24-pve** ; contrôler le paquet signé avec Secure Boot. Un signalement isolé de démarrage avec `7.0.14-25-pve` sous Secure Boot a été mentionné les jours précédents, sans preuve de régression généralisée. Vérifier la chaîne de boot et conserver une entrée de noyau de secours.

**Six avis Proxmox du 19 octobre**, corrigés à partir de **qemu-server 9.2.11** : **PSA-2026-00069** (mot de passe cloud-init dans snapshots), **00070** (lecture de snippets hors périmètre), **00071** (contrôles de privilèges cloud-init), **00072** (épuisement mémoire via QEMU Guest Agent), **00073** (DoS pvedaemon) et **00074** (commandes HMP et mémoire). Exploitation conditionnée selon avis par des privilèges VM, l'accès invité ou des configurations spécifiques ; scores CVSS individuels non confirmés, donc non comptés. **Auth bypass historique** : PSA-2026-00043, **CVE-2023-54391**, affecte surtout PVE 7 en fin de vie ; aucune version PVE 9 supportée n'est concernée selon Proxmox.

**Actions** : `pveversion -v`, `uname -r`, `apt policy pve-manager qemu-server proxmox-kernel-7.0`, vérifier les droits délégués et tester les sauvegardes, migrations, stockage et démarrage après patch.

Sources :
- https://proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/q/qemu-server/
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4

## 4. OpenSSH
**Version amont vérifiée : 10.6p1 du 6 octobre 2026**. Les notes amont décrivent des corrections de sécurité (SFTP, GSSAPI, compression, contrôles des noms d'utilisateurs) et des modifications de compatibilité. Aucune nouvelle CVE numériquement classée du 31 octobre vérifiée dans la source. Sur Rocky/RHEL, utiliser les errata RPM et les backports : une version RPM 9.x peut contenir les correctifs pertinents. Vérifier `rpm -q openssh openssh-server`, `sshd -T` et les politiques d'accès.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx
Bulletins officiels vérifiés : **CVE-2026-15176** (alias/rewrite), **CVE-2026-93479** (évaluation de scripts), **CVE-2026-95101** (HTTP/3), **CVE-2026-97629** (tickets TLS), **CVE-2026-97205** (AIO). Gravité fournisseur : **medium** pour ces cinq avis. Branches corrigées **1.30.6+ / 1.31.7+**. Les scores CVSS v3.1 numériques et les conditions exactes de toutes les CVE n'ont pas été vérifiés, donc non comptés. Exposition dépendant des modules, directives et protocoles utilisés ; attention aux correctifs rétroportés par les distributions.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB
**MariaDB Community Server 10.11.19 LTS**, sortie **24 août 2026**, est une version GA documentant de nombreux correctifs de sécurité. Les **10 CVE CVSS >= 7** figurant dans les tableaux de comptage incluent deux critiques (9.9) et huit importantes (7.2–8.8). Les autres CVE de la note de version, dont plusieurs < 7, ne sont pas comptées. La présence d'une CVE dans le changelog ne signifie pas qu'elle est exploitable à distance sans authentification : les prérequis et composants concernés doivent être consultés par CVE. Vérifier version, moteur CONNECT, privilèges, Galera, réplication et sauvegardes avant mise à jour.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Plan d'action défensif
1. **P0 GitLab** : corriger serveur, Runner et CA des images CI/CD, puis contrôler les migrations et les traces de compromission ; revoir les secrets et tokens si une exposition est suspectée.
2. **P0 hyperviseurs** : confirmer que le noyau Proxmox corrigé contre PwnEscape est réellement démarré ; valider Secure Boot et capacité de retour arrière.
3. **P1 Rocky/RHEL** : déployer le kernel corrigé après contrôle de `/boot` et redémarrer.
4. **P1 Proxmox** : `qemu-server >= 9.2.11`, vérifier la version effective et les permissions déléguées.
5. **P1 Nginx/MariaDB/OpenSSH** : appliquer les correctifs des branches maintenues et les backports fournisseur.

Aucune instruction offensive. Sources vérifiées le 31 octobre 2026, heure de Paris.
