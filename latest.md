---
type: mco-daily
date: 2026-10-25
generated_at: 2026-10-25T07:59:59+01:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 16
rocky_kernel_latest: "5.14.0-687.59.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.21"
---

# Brief MCO défensif — dimanche 25 octobre 2026

## Résumé exécutif

- **P0 GitLab Self-Managed CE/EE** : deux RCE sans authentification, CVSS 10.0, corrigées par les Critical Patch Releases des 12 et 21 octobre. Les risques CI/CD, accès aux secrets et contournements d'autorisation justifient une mise à jour immédiate.
- **P0 Hyperviseurs Proxmox / Rocky-RHEL 9** : PwnEscape (CVE-2026-99999) permet une sortie de VM dans certaines configurations ; correctifs kernel disponibles. Redémarrage et vérification du kernel actif nécessaires.
- **P1 Proxmox** : six avis de sécurité du 19 octobre corrigés dans qemu-server >= 9.2.11. Un signalement isolé de démarrage avec kernel 7.0.14-25-pve et Secure Boot appelle à contrôler les paquets signés.
- **P1 Nginx / MariaDB / OpenSSH** : conserver les versions corrigées et vérifier les correctifs rétroportés.

**Nouveauté du jour :** aucun nouvel avis officiel daté du 25 octobre n'a été identifié lors de la vérification du matin. Les risques non corrigés des jours précédents restent prioritaires. Absence de nouvel avis identifié ne signifie pas absence de vulnérabilité.

## Comptage CVE et méthode

Les compteurs YAML couvrent **uniquement les CVE distinctes nommément listées dans le tableau ci-dessous**, sur les bulletins GitLab des 12 et 21 octobre, Red Hat PwnEscape et MariaDB 10.11.19 ; ce ne sont **pas des CVE nouvellement publiées aujourd'hui**. Score CVSS v3.1 : **critical >= 9.0**, **important >= 7.0 et < 9.0**. Chaque identifiant CVE est compté une seule fois, même s'il touche plusieurs produits. Pour CVE-2026-99999, retenir le score fournisseur Red Hat 8.8 plutôt que le score CNA 9.3. Les CVE sans score numérique confirmé sont exclues des compteurs, mais pas de la surveillance.

| Gravité | CVE | CVSS | Produit / bulletin |
| --- | --- | ---: | --- |
| Critical | CVE-2026-110264 | 10.0 | GitLab 21/10 |
| Critical | CVE-2026-97229 | 10.0 | GitLab 12/10 |
| Critical | CVE-2026-92262 | 9.9 | MariaDB 10.11.19 |
| Critical | CVE-2026-85746 | 9.9 | MariaDB 10.11.19 |
| Important | CVE-2026-15723 | 8.2 | GitLab 21/10 |
| Important | CVE-2026-112212 | 7.5 | GitLab 21/10 |
| Important | CVE-2026-92883 | 8.7 | GitLab 12/10 |
| Important | CVE-2026-16301 | 8.5 | GitLab 12/10 |
| Important | CVE-2026-14814 | 8.2 | GitLab 12/10 |
| Important | CVE-2026-81839 | 7.7 | GitLab 12/10 |
| Important | CVE-2026-96893 | 7.3 | GitLab 12/10 |
| Important | CVE-2026-99999 | 8.8 | RHEL/Proxmox, score Red Hat |
| Important | CVE-2026-107823 | 7.2 | MariaDB 10.11.19 |
| Important | CVE-2026-107821 | 8.0 | MariaDB 10.11.19 |
| Important | CVE-2026-107818 | 8.4 | MariaDB 10.11.19 |
| Important | CVE-2026-107815 | 8.5 | MariaDB 10.11.19 |
| Important | CVE-2026-107814 | 8.4 | MariaDB 10.11.19 |
| Important | CVE-2026-102523 | 8.0 | MariaDB 10.11.19 |
| Important | CVE-2026-97380 | 8.8 | MariaDB 10.11.19 |
| Important | CVE-2026-86047 | 7.7 | MariaDB 10.11.19 |

## 1. GitLab Self-Managed CE/EE — P0

### Critical Patch du 21 octobre 2026

**Versions corrigées** : 18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3 et 19.5.1. GitLab recommande une mise à jour immédiate des installations Self-Managed affectées, Omnibus, sources ou Helm.

| CVE | CVSS | Vecteur, prérequis et impact |
| --- | ---: | --- |
| **CVE-2026-110264** | **10.0** | **RCE sans authentification ni interaction**, via débordement d'entier du compilateur de regex ; AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H. Versions depuis 7.0, avant les correctifs propres à chaque branche. |
| CVE-2026-15723 | 8.2 | Prise de contrôle d'un compte LDAP par utilisateur authentifié, si OmniAuth SAML et auto-link LDAP sont activés ; aucune interaction de la victime ; depuis 10.1 avant correctifs. |
| CVE-2026-112212 | 7.5 | Empoisonnement de cache OAuth/OIDC **sans authentification**, interaction ultérieure nécessaire ; depuis 18.3 avant correctifs. |
| CVE-2026-12664 | 6.8 | Contournement de portée de token read_api via GraphQL ; token et authentification requis. |
| CVE-2026-16255 | 5.9 | Contournement de verrou d'appartenance SAML/LDAP, GitLab EE, utilisateur authentifié. |
| CVE-2026-105777 | 4.3 | **Exposition de credentials internes** via en-têtes HTTP sous conditions ; avis décrivant un attaquant non authentifié. |
| CVE-2026-13319 | 4.3 | Accès non autorisé à des métadonnées de projets privés, authentification requise. |

Cinq autres CVE de gravité inférieure à 7 touchent AI Gateway, cache, approbations, findings et fonctions IA : CVE-2026-112378, CVE-2026-105711, CVE-2026-14288, CVE-2026-112326, CVE-2026-103515. Vérifier leurs plages affectées et prérequis dans le bulletin.

### Critical Patch du 12 octobre — CI/CD et secrets

**Correctifs de cette vague** : 19.2.8, 19.3.5, 19.4.2. **Privilégier les versions corrigées du 21 octobre** pour couvrir les deux vagues.

| CVE | CVSS | Impact / conditions |
| --- | ---: | --- |
| **CVE-2026-97229** | **10.0** | **RCE CI/CD sans authentification ni interaction**, regex de configuration CI/CD ; 19.2 < 19.2.8, 19.3 < 19.3.5, 19.4 < 19.4.2. |
| CVE-2026-92883 | 8.7 | XSS via coloration syntaxique, utilisateur authentifié, interaction de la victime. |
| CVE-2026-16301 | 8.5 | Contournement d'autorisation de pipelines sur branches protégées ; rôle Developer requis. |
| CVE-2026-14814 | 8.2 | Empoisonnement de cache de fichiers du dépôt, pouvant lancer des pipelines et exposer des variables protégées ; authentification requise. |
| CVE-2026-81839 | 7.7 | Pipelines planifiés avec privilèges d'un nouveau groupe (EE), utilisateur authentifié, interaction selon scénario. |
| CVE-2026-96893 | 7.3 | Injection d'instructions dans GitLab Duo Agent Platform (EE), pouvant entraîner des commandes non voulues ; authentification et interaction. |
| CVE-2026-101168 | 6.2 | **Fuite de tokens/secrets GitLab Pages** via journaux ; accès aux logs requis. |

**Action immédiate** : si une instance est encore en 19.3.2, planifier le passage au minimum à 19.3.6, avec sauvegarde, contrôle des migrations, runners, variables protégées, tokens et journaux. Utiliser les détections défensives publiées par GitLab pour les deux RCE.

Sources officielles :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 2. Rocky Linux / RHEL 9 — kernel, errata et CVE

**Dernier kernel BaseOS Rocky Linux 9 x86_64 identifié dans l'index officiel au 25 octobre : 5.14.0-687.59.1.el9_8**, daté du **21 octobre 2026**. Il s'agit d'un paquet publié, pas de la version démarrée sur les serveurs.

**RHSA-2026:86033**, erratum Red Hat du 20 octobre : corrige notamment **CVE-2026-99999 (PwnEscape)**, ainsi que des défauts Open vSwitch, TCP, bridge, GSO, SMB et perf (CVE-2026-31678, CVE-2026-64024, CVE-2026-64032, CVE-2026-80590, CVE-2026-89633, CVE-2026-93817). Les CVSS individuels des autres CVE ne sont pas inclus dans les compteurs faute de vérification dédiée.

**PwnEscape** : score Red Hat **8.8** (IMPORTANT), vecteur **AV:L/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:H** ; score CNA 9.3, sans privilèges requis selon son évaluation. Depuis une charge locale ou une VM invitée non fiable, risque de lecture/écriture mémoire du noyau hôte, DoS et sortie de VM. Ce n'est **pas** une RCE directement accessible par un port TCP depuis Internet. Red Hat signale un cas d'exploitation active ; traiter les hyperviseurs en priorité.

**Actions** : vérifier les errata disponibles, appliquer le kernel corrigé et redémarrer ; confirmer la version active, l'intégrité des modules tiers et l'état réseau. Contrôles : `dnf updateinfo list --security`, `rpm -q kernel-core`, `uname -r`.

Sources :
- https://access.redhat.com/errata/RHSA-2026:86033
- https://access.redhat.com/security/cve/CVE-2026-99999
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## 3. Proxmox VE — kernel, pve-manager, correctifs, régressions

**Version produit vérifiée : Proxmox VE 9.2.** Le dernier changelog `pve-manager` visible dans l'index officiel **pve-no-subscription** est **9.2.21** (29 septembre). **Le kernel Proxmox est un paquet distinct** : le dernier changelog de la série 7.0 visible dans le même dépôt est **7.0.14-25** (19 octobre), y compris pour la variante signée. La présence du changelog ne garantit pas le paquet installé sur chaque hôte.

**PSA-2026-00068-1 du 16 octobre** : **CVE-2026-99999**, sortie de VM KVM via métadonnées checksum et réseau virtio ; corrigée dans **proxmox-kernel-7.0.14-24-pve(-signed)**. Ne pas confondre correctif kernel et mise à jour de pve-manager.

**Six avis du 19 octobre**, tous corrigés dans **qemu-server >= 9.2.11** :

| Avis | Impact / prérequis |
| --- | --- |
| PSA-2026-00069-1 | Mot de passe cloud-init non masqué dans la configuration de snapshot ; droits de lecture requis. |
| PSA-2026-00070-1 | Lecture de snippets d'autres stockages ; droits VM.Config.Cloudinit, démarrage de VM et accès invité. |
| PSA-2026-00071-1 | Modification de paramètres d'authentification cloud-init avec VM.Config.Network au lieu de VM.Config.Cloudinit. |
| PSA-2026-00072-1 | Épuisement mémoire hôte via réponses Guest Agent non bornées, depuis une VM avec agent configuré. |
| PSA-2026-00073-1 | DoS de pvedaemon via lecture Guest Agent et allocation mémoire excessive. |
| PSA-2026-00074-1 | Vérifications de privilèges HMP incomplètes ; fuite potentielle de disposition mémoire avec Sys.Modify. |

Scores CVSS individuels non publiés ou non confirmés : **pas de comptage CVE pour ces six PSA**.

**Régression à surveiller :** un **signalement communautaire isolé du 23 octobre** décrit un échec de démarrage avec kernel 7.0.14-25-pve non signé et Secure Boot. Ce n'est pas une régression générale confirmée. Contrôler les paquets `-signed`, le bootloader et le chemin de retour arrière avant de redémarrer. Ne pas désactiver Secure Boot par défaut.

**Actions** : `pveversion -v`, `uname -r`, `apt-cache policy pve-manager qemu-server`, `proxmox-boot-tool status` si applicable ; contrôler les délégations et les journaux après mise à jour.

Sources :
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-signed-7.0/
- https://forum.proxmox.com/threads/shim-failure-7-0-14-25.187228/

## 4. OpenSSH

**Dernière version amont vérifiée : 10.6p1, publiée le 6 octobre 2026.** Correctifs et durcissements concernant SFTP, GSSAPI et la compression SSH ; aucun nouvel avis officiel daté du 25 octobre identifié. Les paquets Rocky/RHEL peuvent intégrer des correctifs rétroportés sans suivre le numéro amont. Vérifier `rpm -q openssh-server`, les errata de la distribution, l'authentification, les restrictions de clés et l'exposition réseau.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx

**Versions corrigées : 1.30.6+ et 1.31.7+**. Cinq avis officiels récents de gravité **medium** :

| CVE | Fonctionnalité et impact |
| --- | --- |
| CVE-2026-15176 | Débordement mémoire avec alias/rewrite. |
| CVE-2026-93479 | Débordement mémoire dans l'évaluation de scripts. |
| CVE-2026-95101 | Injection de corps de requête HTTP/3. |
| CVE-2026-97629 | Falsification de session avec tickets TLS. |
| CVE-2026-97205 | Use-after-free avec AIO. |

La page officielle précise les versions affectées ; les scores CVSS numériques, privilèges et interactions ne sont pas tous vérifiés, donc ces CVE sont **hors compteurs**. Exposition conditionnée par les modules et directives. Contrôler `nginx -V`, `nginx -T` et les backports RPM.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB — LTS 10.11

**Version de maintenance officielle vérifiée : 10.11.19, publiée le 24 août 2026**. Correctifs de sécurité répertoriés par l'éditeur :

| CVE | CVSS v3.1 |
| --- | ---: |
| CVE-2026-92262 | 9.9 |
| CVE-2026-85746 | 9.9 |
| CVE-2026-97380 | 8.8 |
| CVE-2026-107815 | 8.5 |
| CVE-2026-107818 | 8.4 |
| CVE-2026-107814 | 8.4 |
| CVE-2026-107821 | 8.0 |
| CVE-2026-102523 | 8.0 |
| CVE-2026-86047 | 7.7 |
| CVE-2026-107823 | 7.2 |

**Version corrigée : 10.11.19** pour cette branche. Les prérequis, vecteurs, privilèges et interactions individuels ne sont pas précisés par le tableau de publication ; consulter les fiches CVE avant de qualifier l'exploitabilité. **CVSS élevé ne signifie pas RCE sans authentification.** Contrôler `SELECT VERSION();`, les paquets, CONNECT, Galera, réplication et chiffrement.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Actions prioritaires

1. **P0 :** patcher GitLab et examiner les signaux de tentative d'exploitation, pipelines, runners et secrets.
2. **P0 :** vérifier les kernels Proxmox/Rocky réellement démarrés, puis appliquer les correctifs PwnEscape.
3. **P1 :** contrôler qemu-server >= 9.2.11 et le boot Secure Boot sur les nœuds Proxmox avant reboot.
4. **P1 :** vérifier Nginx, MariaDB et OpenSSH en tenant compte des modules, branches et correctifs rétroportés.

Rapport strictement défensif. Aucune procédure d'exploitation.
