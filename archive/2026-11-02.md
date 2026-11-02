---
type: mco-daily
date: 2026-11-02
generated_at: 2026-11-02T08:09:54+01:00
timezone: Europe/Paris
status: critical
critical_cve: 5
important_cve: 18
rocky_kernel_latest: "5.14.0-687.61.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — lundi 2 novembre 2026

## Résumé exécutif
- **P0 — NOUVEAU : Red Hat a publié RHSA-2026:93412 le 1er novembre 2026**, avec un kernel RHEL 9 **5.14.0-687.64.1.el9_8** corrigeant notamment **CVE-2026-98432 (CVSS 9.8)**, use-after-free IPv4 TCP accessible à distance sans authentification. **Attention : ce correctif RHEL n'est pas encore confirmé dans les dépôts Rocky 9 BaseOS/security consultés.** Le bulletin Red Hat RHSB-2026-016 demeure en état « ongoing ».
- **P0 — Proxmox VE :** CVE-2026-98432 corrigée dans le kernel **7.0.14-25-pve(-signed)** et suivants ; le dernier kernel 7.0 identifié en no-subscription est **7.0.14-26-pve**. Contrôler le kernel *démarré*, pas seulement installé.
- **P0 — GitLab Self-Managed :** RCE sans authentification CVSS 10.0 (CVE-2026-110264 et CVE-2026-97229) et retrait de confiance des certificats eMudhra compromis (CVE-2026-111943). Mettre à jour **GitLab CE/EE, Runner et images CI/CD séparément**.
- **P1 — Proxmox :** six avis PSA-2026-00075 à 00080 (30–31 octobre) ; notamment deux scénarios d'exécution root conditionnelle. **pve-manager 9.2.24**, **pve-xtermjs >= 6.0.0-3**, **qemu-server >= 9.2.11**.
- **P1 — OpenSSH, Nginx, MariaDB :** vérifier les correctifs amont et les backports RPM ; aucune nouvelle release de ces trois projets confirmée depuis la dernière collecte.

## Méthode de comptage
Les nombres `critical_cve` et `important_cve` désignent les **CVE distinctes des deux tableaux de comptage ci-dessous**, et non les CVE nouvelles aujourd'hui ni l'intégralité des CVE des fournisseurs. Une même CVE commune à plusieurs produits est comptée **une seule fois**. Seuils **CVSS v3.1 : critical >= 9.0 ; important >= 7.0 et < 9.0**. Pour RHEL/Rocky, score Red Hat prioritaire en cas de divergence (ex. PwnEscape 8.8 contre score CNA 9.3). Les CVE sans score numérique individuel vérifié sont **exclues**, sans être considérées comme sans risque. Les deux nouvelles CVE importantes ajoutées aujourd'hui sont CVE-2026-89603 et CVE-2026-98466.

### CVE critiques comptées (5)
| CVE | CVSS | Produit / effet | Conditions |
| --- | ---: | --- | --- |
| CVE-2026-98432 | 9.8 | Kernel Linux, UAF IPv4 TCP, DoS / RCE potentielle | AV:N/AC:L/PR:N/UI:N, pas d'authentification ; RHEL 9, Proxmox |
| CVE-2026-110264 | 10.0 | GitLab CE/EE, RCE liée à compilation regexp | AV:N/AC:L/PR:N/UI:N |
| CVE-2026-97229 | 10.0 | GitLab CE/EE, RCE liée aux expressions régulières CI/CD | AV:N/AC:L/PR:N/UI:N |
| CVE-2026-92262 | 9.9 | MariaDB Community 10.11 | corrigée en 10.11.19 ; conditions spécifiques à vérifier |
| CVE-2026-85746 | 9.9 | MariaDB Community 10.11 | corrigée en 10.11.19 ; conditions spécifiques à vérifier |

### CVE importantes comptées (18)
| CVE | CVSS | Produit / effet |
| --- | ---: | --- |
| CVE-2026-15723 | 8.2 | GitLab, prise de compte LDAP/SAML sous configuration concernée |
| CVE-2026-112212 | 7.5 | GitLab, empoisonnement OAuth/OIDC, interaction ultérieure |
| CVE-2026-92883 | 8.7 | GitLab, XSS coloration syntaxique |
| CVE-2026-16301 | 8.5 | GitLab, contournement de pipelines protégés |
| CVE-2026-14814 | 8.2 | GitLab, exposition de secrets via cache CI/CD |
| CVE-2026-81839 | 7.7 | GitLab EE, pipelines avec privilèges d'un autre groupe |
| CVE-2026-96893 | 7.3 | GitLab EE, injection d'instructions Duo Agent |
| CVE-2026-99999 | 8.8 | Kernel RHEL/Proxmox, PwnEscape, sortie de VM ; score Red Hat |
| CVE-2026-107823 | 7.2 | MariaDB 10.11.19 |
| CVE-2026-107821 | 8.0 | MariaDB 10.11.19 |
| CVE-2026-107818 | 8.4 | MariaDB 10.11.19 |
| CVE-2026-107815 | 8.5 | MariaDB 10.11.19 |
| CVE-2026-107814 | 8.4 | MariaDB 10.11.19 |
| CVE-2026-102523 | 8.0 | MariaDB 10.11.19 |
| CVE-2026-97380 | 8.8 | MariaDB 10.11.19 |
| CVE-2026-86047 | 7.7 | MariaDB 10.11.19 |
| **CVE-2026-89603** | **7.8** | **NOUVEAU dans le comptage : RHEL 9 kernel, contournement local de filtre seccomp après ptrace/TSYNC ; PR:L/UI:N** |
| **CVE-2026-98466** | **8.2** | **NOUVEAU dans le comptage : RHEL 9 kernel, isolation hyperviseur pKVM ARM64 ; PR:H/UI:N, non pertinent pour x86_64** |

## 1. RHEL 9 / Rocky Linux 9 — changement majeur du jour
**RHSA-2026:93412** a été publié le **1er novembre 2026 à 22:09 UTC** selon les données CSAF Red Hat reprises dans l'index GCVE ; le portail Red Hat référence explicitement cet erratum comme correctif de **CVE-2026-98466**. Le bulletin couvre un **kernel RHEL 9 5.14.0-687.64.1.el9_8**, classé **Important** par Red Hat, et inclut **CVE-2026-98432 (9.8)**, **CVE-2026-89603 (7.8)**, **CVE-2026-98466 (8.2)** ainsi que d'autres CVE de composants SCTP, IPVS, iSCSI, MPTCP, SMB, seccomp, virtio-fs, KVM et NVMe-oF. Les scores des autres CVE ne sont pas tous revérifiés : elles ne sont pas ajoutées aux compteurs. **L'avis RHSA-2026:93412 est nouveau ; CVE-2026-98432 était déjà comptée hier.**

**CVE-2026-98432 :** Red Hat RHSB-2026-016 (30 octobre) et fiche CVE (modifiée le 30 octobre), CVSS **9.8**, vecteur **CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H**. Une condition de concurrence IPv4 TCP peut déclencher un use-after-free. **Sans authentification, privilèges ni interaction**, atteignable depuis le réseau ; impact **DoS / corruption mémoire**, **RCE potentielle** nécessitant des conditions de timing/gestion mémoire. Ne pas présenter la RCE comme fiable ou observée en exploitation active. Le bulletin Red Hat donne une mesure temporaire de filtrage de paquets IPv4 avec options ; elle réduit l'exposition mais ne constitue pas un correctif, et doit être qualifiée avant production.

**Rocky Linux 9 :** dernier `kernel-core` observé dans le dépôt officiel **BaseOS 9 x86_64** : **5.14.0-687.61.1.el9_8**, daté du **27 octobre**. Dans le dépôt distinct **Rocky 9.8/security**, dernier `kernel-core` identifié : **5.14.0-687.61.1.el9_8.0.1**, daté du **30 octobre**. **Aucun `687.64.1` Rocky n'a été trouvé dans ces deux index à l'heure de la collecte.** Ne pas déduire que le kernel Rocky `687.61.1` ou `687.61.1.0.1` corrige CVE-2026-98432 sans avis officiel explicite. Les dépôts activés peuvent différer selon les machines.

**Autres errata :** RHSA-2026:90365 du 26 octobre, kernel RHEL 9.8, 15 CVE, `5.14.0-687.61.1.el9_8` ; RHSA-2026:86033 et PwnEscape CVE-2026-99999, risque d'évasion VM sous prérequis locaux, CVSS Red Hat 8.8. Les conditions d'exposition sont distinctes de la nouvelle RCE réseau.

**Actions :** vérifier `dnf repolist`, `dnf updateinfo list --security`, `dnf updateinfo info --cve CVE-2026-98432`, `rpm -q kernel-core`, `uname -r` et espace disponible dans `/boot`. Sur RHEL, vérifier l'éligibilité de la branche et la disponibilité de RHSA-2026:93412 avant installation et redémarrage. Sur Rocky, surveiller les miroirs et errata ; ne pas installer arbitrairement un RPM RHEL sur Rocky.

Sources :
- https://access.redhat.com/errata/RHSA-2026:93412
- https://access.redhat.com/security/cve/cve-2026-98432
- https://access.redhat.com/security/cve/cve-2026-89603
- https://access.redhat.com/security/cve/cve-2026-98466
- https://access.redhat.com/security/vulnerabilities/RHSB-2026-016
- https://db.gcve.eu/vuln/RHSA-2026%3A93412 (miroir CSAF Red Hat, car page erratum directe momentanément inaccessible lors de la collecte)
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://download.rockylinux.org/pub/rocky/9.8/security/x86_64/os/Packages/k/

## 2. Proxmox VE 9.2 — kernel distinct de pve-manager
Versions observées dans les index officiels **pve-no-subscription** : **pve-manager 9.2.24**, **proxmox-kernel-7.0 7.0.14-26** (kernel démarrable `7.0.14-26-pve`), **qemu-server 9.2.13**. Ce sont des paquets **différents** ; vérifier `uname -r` après redémarrage. Aucun nouveau correctif ou régression générale du -26 confirmé le 2 novembre.

**PSA-2026-00080 (31 octobre) :** CVE-2026-98432, UAF IPv4 TCP, **corrigée à partir de proxmox-kernel-7.0.14-25-pve(-signed)** ; `7.0.14-24-pve` ne corrige pas cette CVE. La branche kernel 6.8/6.14/6.17 n'a pas de correctif équivalent confirmé ici ; ne pas extrapoler. **Secure Boot** : contrôler le paquet signé et la sélection du kernel au boot. Un signalement communautaire isolé de problème de démarrage ne prouve pas une régression générale.

| Avis Proxmox | Date | Impact / vecteur / privilèges / interaction | Correction |
| --- | --- | --- | --- |
| PSA-2026-00075 | 30/10 | xterm.js, requêtes POST au nom d'une victime via lien piégé ; interaction utilisateur requise | pve-xtermjs >= 6.0.0-3 |
| PSA-2026-00076 | 30/10 | informations de pool accessibles sans Pool.Audit | pve-manager >= 9.2.23 |
| PSA-2026-00077 | 30/10 | injection configuration réseau, **exécution root** par utilisateur/token avec **Sys.Modify** sur le nœud ; **pas anonyme** | pve-manager >= 9.2.23 |
| PSA-2026-00078 | 30/10 | XSS dans consent-text, auteur hautement privilégié | pve-manager >= 9.2.24 |
| PSA-2026-00079 | 30/10 | **exécution root** si **root@pam** connecté ouvre un lien piégé ; **UI:R** | pve-manager >= 9.2.24 |
| PSA-2026-00080 | 31/10 | CVE-2026-98432, réseau IPv4 sans auth, DoS / RCE potentielle, 9.8 | kernel >= 7.0.14-25-pve(-signed) |

Les avis 00075–00079 ne comportent pas de CVSS individuel vérifié, donc exclus des compteurs. Rappels : PSA-2026-00068 / PwnEscape corrigé depuis kernel 7.0.14-24-pve ; PSA-2026-00069 à 00074 (cloud-init, QEMU Guest Agent, HMP) corrigés par **qemu-server >= 9.2.11**. Auditer `Sys.Modify`, `Pool.Audit`, droits cloud-init, tokens et connexions `root@pam`.

Sources :
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/q/qemu-server/

## 3. GitLab Self-Managed CE/EE — CRITICAL
**Dernière Security Update officielle vérifiée : 29 octobre 2026.** CVE-2026-111943 : **huit certificats intermédiaires eMudhra aux clés privées compromises** ; retrait de confiance dans les magasins CA fournis avec GitLab. Cela expose la confiance TLS selon les certificats et services concernés, **ce n'est pas une nouvelle RCE**. CVSS individuel non vérifié, non compté.

**Versions corrigées CE/EE : 18.11.14, 19.0.11, 19.1.10, 19.2.10, 19.3.7, 19.4.5, 19.5.3**. Affectées : branches antérieures à ces versions (voir bulletin pour les versions plus anciennes). **GitLab Runner self-hosted >= 19.5.1** à mettre à jour **séparément** ; reconstruire les images de jobs CI/CD qui incluent leur propre `ca-certificates`. **AI Gateway** auto-hébergé : 18.11.8, 19.0.13, 19.1.9, 19.2.5, 19.3.3, 19.4.3, 19.5.3 ou ultérieures selon branche.

**RCE critiques antérieures, toujours P0 sur versions non corrigées :**
- **CVE-2026-110264 (10.0)** : compilation regexp, RCE serveur **AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H**, **sans authentification ni interaction**. Versions affectées : branches 7.0 avant 18.11.13, 19.0 < 19.0.10, 19.1 < 19.1.9, 19.2 < 19.2.9, 19.3 < 19.3.6, 19.4 < 19.4.3, 19.5 < 19.5.1.
- **CVE-2026-97229 (10.0)** : regexp de configuration CI/CD, RCE serveur **AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H**, **sans authentification ni interaction**. Branches affectées selon Critical Patch du 12 octobre : 19.2 < 19.2.8, 19.3 < 19.3.5, 19.4 < 19.4.2.

**Autres impacts à prioriser selon configuration :** CVE-2026-15723 (8.2), prise de compte LDAP via liaison SAML/LDAP ; CVE-2026-112212 (7.5), cache OAuth/OIDC (PR:N/UI:R) ; CVE-2026-16301 (8.5), pipelines protégés (PR:L) ; CVE-2026-14814 (8.2), secrets CI/CD dans cache (PR:L) ; CVE-2026-81839 (7.7), privilèges de pipelines EE (PR:L/UI:R) ; CVE-2026-96893 (7.3), Duo Agent (PR:L/UI:R) ; CVE-2026-92883 (8.7), XSS (PR:L/UI:R). Vérifier les prérequis et branches exactes par bulletin avant décision. Sur une installation 19.3.2, viser **au moins 19.3.7** sur cette branche après sauvegarde et respect du chemin de mise à jour. Vérifier runners, variables protégées, secrets/tokens, SAML/LDAP et journaux CI/CD.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 4. OpenSSH
**Dernière version amont vérifiée : 10.6/10.6p1 (6 octobre 2026)**. Correctifs/durcissements SFTP (validation de chemins renvoyés par serveur), GSSAPI (credentials après succès), compression et validation des noms d'utilisateurs. Aucun nouveau bulletin CVE chiffré confirmé aujourd'hui. Sur Rocky/RHEL, la version RPM peut conserver un numéro amont plus ancien avec backports : comparer les **errata de distribution**, pas seulement `ssh -V`. Vérifier les méthodes d'authentification et les accès réseau.

Source : https://www.openssh.org/releasenotes.html

## 5. Nginx
Avis éditeur toujours actifs pour les branches non corrigées, **gravité éditeur medium** : CVE-2026-15176 (alias/rewrite), CVE-2026-93479 (scripts), CVE-2026-95101 (HTTP/3), CVE-2026-97629 (tickets TLS), CVE-2026-97205 (AIO). Versions amont non vulnérables indiquées **1.30.6+ et 1.31.7+** ; prérequis dépendants des directives/modules. Pas de CVSS v3.1 individuel confirmé pour ces cinq avis, donc exclus des compteurs. Sur Nginx fourni par RPM, contrôler les backports et la configuration réellement active.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB
**MariaDB Community 10.11.19**, sortie **24 août 2026**, série LTS. La documentation officielle de cette release liste **CVE-2026-92262 et CVE-2026-85746 (9.9 chacune)** ainsi que **8 CVE importantes (7.2–8.8)** figurant dans le tableau. Plusieurs autres CVE ont des scores inférieurs à 7.0. **Un score élevé ne signifie pas nécessairement une RCE réseau anonyme** : les privilèges, interactions, moteurs et fonctionnalités nécessaires doivent être vérifiés individuellement. Contrôler versions, CONNECT, Galera, réplication, sauvegardes et paquets RPM. Aucun nouveau correctif de la branche 10.11 vérifié aujourd'hui.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Priorités opérationnelles
1. **P0 — RHEL 9 :** qualifier **RHSA-2026:93412 / 5.14.0-687.64.1.el9_8** et redémarrer après contrôles de compatibilité ; **Rocky 9 :** ne pas supposer le correctif disponible, suivre les dépôts et errata pour CVE-2026-98432. Prioriser les services TCP IPv4 exposés.
2. **P0 — Proxmox :** mettre à jour et démarrer **7.0.14-25-pve ou plus récent**, vérifier Secure Boot et le noyau signé ; corriger pve-manager/pve-xtermjs et auditer les permissions déléguées.
3. **P0 — GitLab :** corriger CE/EE, Runner, AI Gateway si utilisé et images CI/CD ; auditer secrets/tokens et pipelines.
4. **P1 — Autres :** appliquer les correctifs qemu-server, Nginx, MariaDB, OpenSSH selon les branches et backports, puis contrôler la santé des services.

**Limites de la collecte :** versions publiques de dépôts, pas les versions effectivement installées sur les hôtes ; le portail direct de RHSA-2026:93412 était momentanément inaccessible, mais la fiche CVE Red Hat renvoie à cet erratum et les données CSAF Red Hat sont reprises dans un index public. La disponibilité de la build RHEL ne prouve pas sa publication sur Rocky. Aucune instruction offensive.
