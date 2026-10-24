---
type: mco-daily
date: 2026-10-24
generated_at: 2026-10-24T08:04:42+02:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 16
rocky_kernel_latest: "5.14.0-687.59.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.21"
---

# Brief MCO défensif — samedi 24 octobre 2026

## Résumé exécutif
- **P0 GitLab Self-Managed CE/EE :** deux RCE critiques sans authentification dans les correctifs des 12 et 21 octobre. Appliquer immédiatement les dernières versions corrigées, particulièrement sur les instances exposées sur Internet.
- **P0 Hyperviseurs :** CVE-2026-99999 (PwnEscape), sortie de VM vers l'hôte via virtio/réseau ; correctifs publiés pour Proxmox et RHEL 9.
- **P1 Proxmox :** six avis `qemu-server` corrigés à partir de `9.2.11`. **Signalement communautaire du 23 octobre :** échec de démarrage avec kernel `7.0.14-25-pve` non signé sur un hôte avec Secure Boot ; vérifier le paquet signé avant reboot, sans généraliser ce cas isolé.
- **P1 Nginx et MariaDB :** maintenir les branches corrigées et vérifier les fonctionnalités réellement exposées.

Aucun nouvel avis officiel daté du 24 octobre n'a été confirmé à l'heure de génération. Le présent brief rappelle les correctifs récents encore prioritaires.

## Méthode de comptage des CVE
Les métadonnées `critical_cve` et `important_cve` recensent **uniquement les CVE à score CVSS v3.x numérique vérifié et nommément reprises dans le tableau de comptage ci-dessous**, à partir des Critical Patches GitLab des 12 et 21 octobre, du bulletin Red Hat sur PwnEscape et de la maintenance MariaDB 10.11.19. Il ne s'agit **ni d'un total de toutes les CVE existantes, ni du nombre de nouvelles CVE publiées aujourd'hui**.

- `critical` : CVSS >= 9.0 ; `important` : 7.0 <= CVSS < 9.0. Les scores inférieurs à 7 et les CVE sans score vérifié ne sont pas comptés.
- Une CVE commune à plusieurs produits n'est comptée **qu'une fois**. Pour CVE-2026-99999, retenir le score Red Hat **8.8** (au lieu du score CNA 9.3), donc **important**.
- Les deux Critical Patches GitLab sont distinguées ; leurs vulnérabilités ne sont pas confondues.

| Niveau | CVE | CVSS | Source |
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
**Versions corrigées :** `18.11.13`, `19.0.10`, `19.1.9`, `19.2.9`, `19.3.6`, `19.4.3`, `19.5.1`. Correctifs recommandés immédiatement par GitLab. Toutes les installations CE/EE concernées, quel que soit le mode de déploiement, doivent être vérifiées.

| CVE | CVSS | Impact, vecteur et prérequis |
| --- | ---: | --- |
| **CVE-2026-110264** | **10.0** | **RCE sans authentification ni interaction**, compilation de regexp ; `AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H`. Versions depuis 7.0, jusqu'aux correctifs de chaque branche. |
| CVE-2026-15723 | 8.2 | Prise de contrôle de compte LDAP ; **utilisateur authentifié** et OmniAuth SAML + liaison LDAP automatique activés ; pas d'interaction de la victime. Depuis 10.1, jusqu'aux correctifs. |
| CVE-2026-112212 | 7.5 | Empoisonnement du cache OAuth/OIDC, **sans authentification**, interaction ultérieure requise ; depuis 18.3 avant correctifs. |
| CVE-2026-12664 | 6.8 | Contournement du périmètre d'un token `read_api` par un utilisateur authentifié ; GraphQL. |
| CVE-2026-16255 | 5.9 | Contournement de verrouillage d'appartenance SAML/LDAP (EE) ; authentification nécessaire. |
| CVE-2026-105777 | 4.3 | **Exposition potentielle de credentials internes** dans les en-têtes de réponse, sous conditions ; l'avis mentionne un attaquant non authentifié. |
| CVE-2026-13319 | 4.3 | Lecture non autorisée de métadonnées de projets privés ; authentification nécessaire. |

Cinq autres CVE de gravité inférieure à 7 concernent AI Gateway, cache de merge requests, approbations, constats de sécurité et contrôle administratif des fonctions IA : `CVE-2026-112378`, `CVE-2026-105711`, `CVE-2026-14288`, `CVE-2026-112326`, `CVE-2026-103515`. Vérifier leurs conditions exactes dans le bulletin.

### Critical Patch du 12 octobre 2026 — CI/CD et secrets
| CVE | CVSS | Impact / prérequis |
| --- | ---: | --- |
| **CVE-2026-97229** | **10.0** | **RCE CI/CD sans authentification ni interaction**, regexp de configuration CI/CD ; versions 19.2 avant 19.2.8, 19.3 avant 19.3.5 et 19.4 avant 19.4.2. |
| CVE-2026-92883 | 8.7 | XSS dans coloration syntaxique ; utilisateur authentifié, interaction victime. |
| CVE-2026-16301 | 8.5 | Contournement d'autorisation de pipelines sur branches protégées ; rôle Developer requis. |
| CVE-2026-14814 | 8.2 | Empoisonnement de cache de fichiers du dépôt pouvant déclencher des pipelines et exposer des variables protégées ; utilisateur authentifié. |
| CVE-2026-81839 | 7.7 | Exécution de pipelines planifiés avec privilèges d'un nouveau groupe (EE), sous conditions ; utilisateur authentifié, interaction requise selon vecteur. |
| CVE-2026-96893 | 7.3 | Injection d'instructions dans GitLab Duo Agent Platform (EE), pouvant provoquer des commandes non voulues ; authentification et interaction. |
| CVE-2026-101168 | 6.2 | **Fuite de tokens/secrets GitLab Pages** via journaux, nécessitant l'accès aux logs. |

Correctifs de cette première vague : `19.2.8`, `19.3.5`, `19.4.2` ; **préférer désormais les versions du 21 octobre** pour couvrir les deux vagues. Vérifier l'intégrité des pipelines, les secrets, les runners et les journaux de sécurité. La mise à jour d'une instance mononœud peut occasionner une interruption pendant les migrations ; GitLab signale des migrations pour 19.3.6/19.4.3.

Sources officielles :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 2. Rocky Linux / RHEL 9 — kernel, errata, CVE
**Dernier kernel Rocky 9 BaseOS x86_64 observé dans l'index officiel au 24 octobre : `5.14.0-687.59.1.el9_8`**, publié le **21 octobre 2026**. Il s'agit d'une version de paquet disponible, **pas** d'une confirmation de son déploiement sur les machines.

**RHSA-2026:86033**, émis le **20 octobre**, corrige notamment `CVE-2026-99999` et d'autres défauts kernel (`CVE-2026-31678` Open vSwitch, `CVE-2026-64024` TCP, `CVE-2026-64032` bridge, `CVE-2026-80590` GSO, `CVE-2026-89633` SMB, `CVE-2026-93817` perf). Ces CVE supplémentaires sont **hors compteurs** : scores individuels non revalidés.

**PwnEscape — CVE-2026-99999 :** Red Hat attribue **CVSS 8.8** (`AV:L/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:H`), contre **9.3** pour la CNA. Depuis un invité non fiable ou une charge locale capable de transmettre des métadonnées de checksum malformées, risque de corruption mémoire, exécution de code hôte, divulgation ou DoS. Aucune interaction utilisateur ; privilèges faibles selon Red Hat. **Ne pas assimiler à une RCE directement exposée sur un port TCP.**

Action : `dnf updateinfo list --security`, `dnf update --security` après validation, puis redémarrage planifié ; contrôler `uname -r`, les pilotes et les journaux. Vérifier les backports et la version réellement démarrée.

Sources :
- https://access.redhat.com/errata/RHSA-2026:86033
- https://access.redhat.com/security/cve/CVE-2026-99999
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## 3. Proxmox VE — kernel, manager, correctifs et régressions
**Version de produit vérifiée : Proxmox VE 9.2.** **Dernier changelog `pve-manager` visible dans l'index officiel `pve-no-subscription` : `9.2.21`** (29 septembre). Ce n'est pas le numéro du kernel ; la version candidate peut varier selon les dépôts et miroirs de chaque hôte.

**Kernel Proxmox :** `proxmox-kernel-7.0.14-24-pve(-signed)` corrige **CVE-2026-99999**, selon l'avis officiel **PSA-2026-00068-1 du 16 octobre**. La série `7.0.14-25-pve` a été observée dans les dépôts et discussions ; **elle n'est pas assimilable à `pve-manager 9.2.21`**.

**Régression à surveiller (signalement isolé du 23 octobre, non confirmé comme défaut général) :** un utilisateur rapporte un échec de démarrage avec `7.0.14-25-pve` non signé sur machine Secure Boot/GRUB. Avant toute maintenance, contrôler `proxmox-kernel-*-signed`, la configuration Secure Boot, le chargeur, le kernel sélectionné et un chemin de récupération. Ne pas désactiver Secure Boot comme solution générique ; préférer un kernel signé pris en charge.

**Six avis Proxmox du 19 octobre, corrigés dans `qemu-server >= 9.2.11` :**

| Avis | Impact / conditions |
| --- | --- |
| PSA-2026-00069-1 | Mot de passe cloud-init visible dans la configuration de snapshot, selon droits de lecture. |
| PSA-2026-00070-1 | Lecture de snippets non autorisés avec droits VM.Config.Cloudinit, démarrage de VM et accès invité. |
| PSA-2026-00071-1 | Contournement de permissions cloud-init via VM.Config.Network. |
| PSA-2026-00072-1 | Réponses QEMU Guest Agent sans limites ; épuisement mémoire hôte depuis une VM dotée d'un agent. |
| PSA-2026-00073-1 | DoS de pvedaemon via lecture Guest Agent malveillante. |
| PSA-2026-00074-1 | Commandes QEMU HMP insuffisamment protégées, divulgation potentielle de mémoire, privilège Sys.Modify. |

Scores CVSS non publiés/confirmés dans les avis consultés ; ces PSA ne sont pas des CVE comptées. Vérifier `pveversion -v`, `uname -r`, `apt-cache policy pve-manager qemu-server`, `proxmox-boot-tool status` si pertinent, et les régressions avant/après reboot.

Sources :
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- https://forum.proxmox.com/threads/shim-failure-7-0-14-25.187228/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

## 4. OpenSSH
**Dernière version amont vérifiée : `10.6p1`, publiée le 6 octobre 2026.** Notes de sécurité et corrections SFTP/GSSAPI/compression. Aucun nouvel avis officiel daté du 24 octobre confirmé dans cette collecte. Sur Rocky/RHEL, les versions de RPM intègrent des correctifs rétroportés ; vérifier les errata plutôt que comparer seulement à `10.6p1`. Examiner authentification, clés, restrictions `authorized_keys` et exposition réseau.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx
**Versions corrigées : `1.30.6+` et `1.31.7+`.** Cinq avis officiels récents de gravité **medium** :

| CVE | Fonctionnalité / impact |
| --- | --- |
| CVE-2026-15176 | Buffer overflow avec alias/rewrite. |
| CVE-2026-93479 | Buffer overflow d'évaluation de scripts. |
| CVE-2026-95101 | Injection de corps de requête HTTP/3. |
| CVE-2026-97629 | Falsification de session via tickets TLS. |
| CVE-2026-97205 | Use-after-free avec AIO. |

Scores CVSS numériques, authentification et prérequis individuels non confirmés dans la page d'avis ; **exclus des compteurs**. L'exposition dépend des directives/modules activés. Contrôler `nginx -V`, `nginx -T` et les versions RPM/backports.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB — branche LTS 10.11
**Version stable vérifiée : `10.11.19`, publiée le 24 août 2026.** Les notes officielles associent notamment les CVE suivantes à cette maintenance :

| CVE | CVSS | Correctif |
| --- | ---: | --- |
| CVE-2026-92262 | 9.9 | 10.11.19 |
| CVE-2026-85746 | 9.9 | 10.11.19 |
| CVE-2026-97380 | 8.8 | 10.11.19 |
| CVE-2026-107815 | 8.5 | 10.11.19 |
| CVE-2026-107818 | 8.4 | 10.11.19 |
| CVE-2026-107814 | 8.4 | 10.11.19 |
| CVE-2026-107821 | 8.0 | 10.11.19 |
| CVE-2026-102523 | 8.0 | 10.11.19 |
| CVE-2026-86047 | 7.7 | 10.11.19 |
| CVE-2026-107823 | 7.2 | 10.11.19 |

Les autres CVE de la version ont des scores inférieurs à 7 et ne figurent pas dans le comptage. Les conditions d'exploitation (authentification, privilèges, interaction, plugins, réplication) doivent être vérifiées CVE par CVE ; **ne pas conclure à une RCE sans authentification sur la seule base du CVSS**. Vérifier `SELECT VERSION();`, l'origine des paquets, les plugins CONNECT, Galera et les paramètres de réplication.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Priorités opérationnelles
1. **P0 :** mettre à jour GitLab Self-Managed et vérifier les tentatives d'exploitation, pipelines, tokens et secrets.
2. **P0 :** confirmer les kernels réellement démarrés sur les hyperviseurs Proxmox et les hôtes Rocky/RHEL, puis appliquer les correctifs PwnEscape.
3. **P1 :** contrôler `qemu-server >= 9.2.11` et tester le démarrage des kernels Proxmox signés avant maintenance.
4. **P1 :** vérifier Nginx, MariaDB et OpenSSH avec prise en compte des modules activés et des correctifs rétroportés.

**Rapport défensif uniquement ; aucune instruction offensive.**
