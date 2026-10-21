---
type: mco-daily
date: 2026-10-21
timezone: Europe/Paris
status: action-required
---

# Brief MCO défensif — mercredi 21 octobre 2026

*Veille effectuée le 21 octobre au matin (Europe/Paris). Les éléments ci-dessous distinguent les nouveaux paquets, les avis récents et les correctifs encore non confirmés. Les scores CVSS ne sont indiqués que lorsqu'ils sont publiés par l'éditeur.*

## Résumé exécutif

- **P0 — GitLab CE/EE :** CVE-2026-97229 (**CVSS 10.0**), RCE serveur par CI/CD **sans authentification ni interaction**. Correctifs du 12/10 : **19.2.8 / 19.3.5 / 19.4.2** ; contrôler les versions réellement déployées.
- **P0 — Proxmox :** **CVE-2026-99999 (PwnEscape)**, évasion de VM KVM vers l'hôte, corrigée dans **7.0.14-24-pve** (16/10). Un kernel **7.0.14-25-pve** apparaît dans le dépôt no-subscription au **20/10** ; ses corrections spécifiques/régressions ne sont pas établies ici.
- **P1 — Proxmox :** six avis du **19/10** sur `qemu-server` (fuite de mot de passe cloud-init, contrôles d'accès, QGA/DoS, HMP), tous corrigés dans **qemu-server >= 9.2.11**.
- **P1 — Rocky/RHEL 9 :** RHEL 9 reste **Affected** par PwnEscape, sans erratum correctif Red Hat indiqué au moment de la vérification. Dernier RPM kernel Rocky observé : **5.14.0-687.58.1.el9_8** (15/10).
- **P1 — Nginx :** cinq CVE de sévérité éditeur « medium » corrigées dans **1.30.6 / 1.31.7** (14/10) ; comparer avec les correctifs backportés des distributions.

## 1. Rocky Linux / RHEL 9

### Kernel et errata

- Dernier paquet `kernel-core` observé dans le dépôt Rocky 9 x86_64 : **5.14.0-687.58.1.el9_8**, daté du **15 octobre 2026**. Pas de paquet plus récent confirmé dans l'index consulté.
- **CVE-2026-99999 — PwnEscape** : Red Hat publie une sévérité **Critical**, **CVSS v3.1 8.8**, vecteur **AV:L/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:H**. Un attaquant local ou une VM non fiable peut provoquer corruption mémoire via métadonnées de checksum réseau, avec **exécution de code hôte, fuite mémoire ou DoS**.
- **RHEL 9 kernel : Affected**, sans erratum de correction listé. La présence du kernel Rocky `687.58.1` **ne prouve pas** la correction de cette CVE.
- **Urgence P0** sur les hyperviseurs exécutant des VM non fiables ou mutualisées ; **P1** sur les autres systèmes exposés. Suivre le correctif de l'éditeur, ne pas annoncer de mitigation non validée.
- Vérification défensive : `uname -r`, `rpm -q kernel-core`, `dnf updateinfo list --security`, `dnf updateinfo info --security`. Évaluer les backports via les errata, et non uniquement le numéro de version amont.

Sources :
- Red Hat CVE : https://access.redhat.com/security/cve/cve-2026-99999
- Rocky RPM : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## 2. Proxmox VE 9 et noyau PVE

### P0 — PwnEscape, CVE-2026-99999

- **PSA-2026-00068-1**, daté du **16 octobre** : évasion d'une VM KVM par défaut de validation des métadonnées checksum du réseau virtio ; fuite/écriture mémoire du kernel hôte.
- **Corrigé dans : `proxmox-kernel-7.0.14-24-pve(-signed)`**. Accès préalable à une VM/charge invitée requis ; aucune interaction utilisateur. CVSS Red Hat 8.8 sur RHEL, **ne pas transposer ce score automatiquement à Proxmox**.
- **Nouveauté paquet :** `proxmox-kernel-7.0.14-25-pve` et variante signée observés dans un miroir du dépôt **pve-no-subscription** avec date de fichier **20 octobre 2026**. La nature exacte des changements et les éventuelles régressions du `-25` ne sont **pas confirmées** dans cette collecte.
- **Action P0 :** garantir au minimum le correctif `-24` effectivement démarré ; tester `-25` sur hôte non critique avant généralisation, puis surveiller NIC/virtio, KVM, stockage et journaux kernel. Contrôles : `pveversion -v`, `uname -r`, `apt policy proxmox-kernel-7.0.14-25-pve`.

### P1 — Six avis qemu-server du 19 octobre

| Avis | Impact / prérequis | Version corrigée |
| --- | --- | --- |
| PSA-2026-00069-1 | **Fuite du mot de passe cloud-init** `cipassword` dans la configuration de snapshot accessible selon les permissions API | `qemu-server >= 9.2.11` |
| PSA-2026-00070-1 | **Lecture de snippets** sur stockages non autorisés ; `VM.Config.Cloudinit`, droit de démarrer et accès invité | idem |
| PSA-2026-00071-1 | **Contournement des privilèges cloud-init** ; `VM.Config.Network` suffit à modifier des paramètres d'authentification invités | idem |
| PSA-2026-00072-1 | Consommation mémoire excessive de l'hôte via réponses QEMU Guest Agent ; agent invité contrôlé | idem |
| PSA-2026-00073-1 | **DoS de pvedaemon** par épuisement mémoire via QGA file-read ; agent invité malveillant | idem |
| PSA-2026-00074-1 | **Fuite potentielle d'informations mémoire** via QEMU HMP ; `Sys.Modify` au lieu d'une restriction root | idem |

Ces avis ne publient pas de CVE ni de CVSS chiffré dans le bulletin consulté. Ne pas en inventer. Les risques touchent surtout les environnements multi-utilisateurs, les VM non fiables et les accès API délégués. **Action P1 :** mettre à jour `qemu-server` sur les hôtes concernés et auditer les rôles, tokens, snapshots et snippets.

Sources :
- Proxmox PSA 00069–00071 : https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- Proxmox PSA 00072–00074 : https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- Miroir no-subscription (version `-25`) : https://mirror.itp.uni-frankfurt.de/proxmox-ve/dists/trixie/pve-no-subscription/binary-amd64/

## 3. GitLab Self-Managed CE/EE

Dernière **Critical Patch Release** officiellement confirmée : **12 octobre 2026**, versions **19.4.2, 19.3.5 et 19.2.8**. Version fonctionnelle **19.5.0** publiée le **15 octobre** ; ne pas la confondre avec une nouvelle Critical Patch. Aucun bulletin ultérieur confirmé au moment de la collecte.

| CVE | CVSS | Vecteur, authentification, interaction et impact | Plages affectées (branches concernées) |
| --- | ---: | --- | --- |
| **CVE-2026-97229** | **10.0** | **RCE serveur CI/CD**, réseau, **sans authentification**, **sans interaction**, intégrité/confidentialité/disponibilité élevées | 19.2 <19.2.8 ; 19.3 <19.3.5 ; 19.4 <19.4.2 |
| CVE-2026-92883 | 8.7 | XSS via syntax highlighting ; authentifié, privilèges faibles, interaction de la victime | depuis 19.1, corrigée en 19.2.8/19.3.5/19.4.2 |
| **CVE-2026-16301** | **8.5** | **Contournement d'autorisation CI/CD**, pipelines de branches protégées ; rôle Developer, pas d'interaction | depuis 18.7, mêmes versions correctives |
| **CVE-2026-14814** | **8.2** | Empoisonnement du cache de fichiers de dépôt, exécution de contenu CI/CD et **fuite potentielle de variables protégées** ; authentifié, sans interaction, complexité élevée | versions antérieures à 19.2.8, 19.3 <19.3.5, 19.4 <19.4.2 |
| CVE-2026-81839 | 7.7 | Pipelines avec privilèges indus après transfert de projet ; EE, authentifié, interaction | depuis 18.1, mêmes correctifs |
| CVE-2026-96893 | 7.3 | Injection d'instructions Duo Agent ; EE, authentifié, interaction | depuis 19.1, mêmes correctifs |
| **CVE-2026-101168** | **6.2** | **Fuite de tokens/secrets** dans logs GitLab Pages ; accès authentifié et privilégié aux logs, pas d'interaction | depuis 12.0, mêmes correctifs |
| CVE-2026-7767 | 5.8 | Redirection et fuite de tokens Kubernetes Agent ; agent contrôlé, authentification et interaction | depuis 13.11, mêmes correctifs |
| **CVE-2026-16275 / 84284 / 85529** | **5.3 chacune** | **Lecture GraphQL de notes/données confidentielles sans authentification ni interaction** | plages distinctes selon CVE, consulter le bulletin |

**Action P0 :** si une instance Omnibus est encore en `19.3.2`, installer au minimum `19.3.5` (ou une version plus récente validée), après sauvegarde et contrôle des contraintes de mise à niveau. Tester runners, pipelines, merge requests, secrets et GitLab Pages. Vérifier l'éventuel `versionlock` DNF.

Source officielle : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
Version 19.5 : https://about.gitlab.com/whats-new/

## 4. OpenSSH

- Dernière release amont identifiée : **OpenSSH 10.6 / 10.6p1, 6 octobre 2026**.
- Correctifs de sécurité annoncés : validation renforcée des chemins renvoyés par un serveur SFTP (risque d'écriture hors destination lors d'une copie récursive), corrections de gestion d'état et de conservation d'identifiants GSSAPI, durcissements SSH.
- **Pré-requis** : utilisation des fonctionnalités concernées (notamment SFTP/GSSAPI). Pas de nouvelle CVE chiffrée ni de CVSS du 21 octobre confirmés.
- **Régression/compatibilité** : compression potentiellement moins efficace ; validation plus stricte de certains noms d'utilisateur ; avertissement de dépréciation de `scp -R`.
- **Action P1/P2 :** vérifier les errata OpenSSH du fournisseur RHEL/Rocky plutôt que comparer mécaniquement les versions amont. Tester transferts SFTP, GSSAPI, clés et configuration `sshd`.

Sources : https://www.openssh.org/releasenotes.html et https://www.openssh.org/security.html

## 5. Nginx

Les versions **1.30.6 (stable)** et **1.31.7 (mainline)** corrigent cinq CVE annoncées mi-octobre ; **sévérité éditeur « medium »** pour chacune. Aucun CVSS chiffré officiel n'est indiqué dans la page d'avis consultée.

| CVE | Impact | Versions amont non vulnérables |
| --- | --- | --- |
| CVE-2026-15176 | Dépassement de tampon, `alias` + `rewrite` | 1.30.6+, 1.31.7+ |
| CVE-2026-93479 | Dépassement de tampon, évaluation de scripts | idem |
| **CVE-2026-95101** | **Injection de corps de requête HTTP/3** ; HTTP/3 requis | idem |
| **CVE-2026-97629** | **Falsification de session TLS** avec session tickets | idem |
| CVE-2026-97205 | Use-after-free avec AIO | idem |

Les plages vulnérables diffèrent selon les CVE et remontent parfois à d'anciennes versions : consulter le tableau officiel. Les avis ne confirment pas une RCE exploitable sans authentification pour chacune de ces failles ; ne pas la présumer.

**Action P1 :** contrôler `nginx -v`, `nginx -V`, les modules actifs et la provenance du paquet ; pour Rocky/RHEL vérifier les backports. Tester `nginx -t` et la compatibilité reverse proxy/TLS/HTTP/3 avant mise à jour.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB

- Pour la branche **10.11 LTS**, dernière maintenance officielle identifiée : **10.11.19**, annoncée le **24 août 2026** (la MariaDB Foundation indique une date de build du 21 août). Maintenance de la branche prévue jusqu'en février 2028.
- Corrections de stabilité pertinentes : courses InnoDB, crashs possibles lors de la récupération et du redimensionnement du buffer pool ; correctifs du moteur CONNECT (validation des données distantes, paramètres transmis à curl, restrictions d'accès aux fichiers).
- Aucune **nouvelle CVE du 21 octobre**, aucun CVSS ou correctif de sécurité supplémentaire pour 10.11 confirmé dans les sources officielles consultées. Ne pas assimiler les tickets MDEV à des CVE.
- **Action P1/P2 :** comparer `SELECT VERSION();` aux paquets disponibles, contrôler l'usage du moteur CONNECT, tester sauvegarde/restauration, réplication, Galera et migration sur préproduction avant mise à jour.

Sources :
- https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19
- https://mariadb.com/resources/blog/mariadb-community-server-q3-2026-maintenance-releases/

## Priorités d'exécution

1. **P0 :** corriger les instances GitLab exposées encore vulnérables à CVE-2026-97229 et vérifier les hyperviseurs Proxmox face à PwnEscape.
2. **P1 :** appliquer `qemu-server >= 9.2.11` sur Proxmox ; suivre la correction Red Hat/Rocky de PwnEscape.
3. **P1 :** traiter les cinq CVE Nginx selon l'exposition réelle et les backports.
4. **P2 :** vérifier OpenSSH 10.6p1 et MariaDB 10.11.19 dans les dépôts adaptés à chaque système.

*Fin du rapport. Aucune instruction offensive ; aucun correctif, score CVSS ou régression non confirmé n'est présenté comme acquis.*
