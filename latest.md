---
type: mco-daily
date: 2026-10-23
generated_at: 2026-10-23T12:26:57+02:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 6
rocky_kernel_latest: "5.14.0-687.59.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.21"
---

# Brief MCO défensif — vendredi 23 octobre 2026

## Résumé exécutif
- **CRITICAL — GitLab Self-Managed** : Critical Patch du 21 octobre ; corriger immédiatement les instances non à jour, en particulier les RCE sans authentification signalées dans les bulletins d'octobre.
- **CRITICAL — Proxmox / Rocky** : contrôler les correctifs kernel relatifs à PwnEscape sur les hyperviseurs concernés ; vérifier le noyau réellement démarré.
- **WARNING — Proxmox VE 9.2** : contrôler qemu-server >= 9.2.11 et les six avis du 19 octobre.
- **WARNING — Nginx et MariaDB** : appliquer les correctifs de sécurité adaptés à la branche et à la configuration.

## Méthode de comptage
Les champs `critical_cve` et `important_cve` comptent les **CVE distinctes explicitement listées ci-dessous avec un score CVSS numérique**, toutes périodes de publication confondues. Une CVE n'est comptée qu'une fois même si elle affecte plusieurs produits. Règle : **critical = CVSS >= 9.0** ; **important = 7.0 <= CVSS < 9.0**. Pour les scores divergents, la valeur Red Hat est retenue sur les systèmes RHEL/Rocky. Les CVE sans CVSS individuel confirmé sont exclues du comptage, sans être considérées comme sans risque.

**CVE critiques comptées (4)** : CVE-2026-110264 (10.0), CVE-2026-97229 (10.0), CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).

**CVE importantes comptées (6)** : CVE-2026-15723 (8.2), CVE-2026-112212 (7.5), CVE-2026-99999 (Red Hat 8.8), CVE-2026-97380 (8.8), CVE-2026-107815 (8.5), CVE-2026-107818 (8.4).

Les scores et certaines associations CVE/produit proviennent des bulletins des collectes précédentes et ne sont pas tous revalidés individuellement aujourd'hui. Les comptes sont ceux des **CVE documentées dans ce rapport**, et non une prétention à recenser toutes les CVE de ces produits.

## 1. GitLab Self-Managed CE/EE — urgence CRITICAL
La Critical Patch officielle du **21 octobre 2026** publie les correctifs **18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3 et 19.5.1** pour CE/EE. L'éditeur recommande une mise à jour immédiate.

| CVE | CVSS | Impact / conditions / vecteur |
| --- | ---: | --- |
| CVE-2026-110264 | 10.0 | RCE à distance sans authentification ni interaction ; AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H ; compilation d'expressions régulières |
| CVE-2026-97229 | 10.0 | RCE CI/CD sans authentification, signalée dans la Critical Patch du 12 octobre |
| CVE-2026-15723 | 8.2 | Prise de contrôle de compte LDAP sous configuration OmniAuth SAML et liaison LDAP |
| CVE-2026-112212 | 7.5 | Empoisonnement de cache OAuth/OIDC sans authentification, avec interaction ultérieure |
| CVE-2026-12664 | 6.8 | Contournement des restrictions de token read_api |
| CVE-2026-16255 | 5.9 | Contournement des restrictions d'appartenance SAML/LDAP (EE) |
| CVE-2026-105777 | 4.3 | Exposition de credentials internes dans des en-têtes HTTP |
| CVE-2026-13319 | 4.3 | Lecture de métadonnées de projets privés |
| CVE-2026-112378 | 4.3 | Contournement des restrictions d'accès AI Gateway |
| CVE-2026-105711 | 3.5 | Divulgation d'informations via cache |
| CVE-2026-14288 | 3.1 | Contournement de politiques d'approbation |
| CVE-2026-112326 | 3.1 | Suppression non autorisée de constats de vulnérabilité |
| CVE-2026-103515 | 2.7 | Contournement du verrou administratif des fonctions IA |

Les plages de versions affectées, l'authentification, les privilèges et l'interaction varient selon la CVE ; ne pas généraliser le vecteur de la RCE aux autres défauts. Pour une instance encore en 19.3.2, cibler au minimum 19.3.6 après sauvegarde et vérification des runners, intégrations, tokens et pipelines.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 2. Rocky Linux / RHEL 9 — kernel et errata
**Dernier kernel Rocky 9 x86_64 identifié dans le dépôt officiel : `5.14.0-687.59.1.el9_8`**, fichiers datés du 21 octobre. La présence dans le dépôt ne garantit ni son installation ni son démarrage sur un serveur.

L'erratum **RHSA-2026:86033** du 20 octobre a été identifié lors des collectes précédentes. Parmi les vulnérabilités signalées : CVE-2026-99999 (PwnEscape, score Red Hat 8.8), CVE-2026-31678 (Open vSwitch), CVE-2026-64024 (TCP), CVE-2026-64032 (bridge), CVE-2026-80590 (GSO), CVE-2026-89633 (SMB) et CVE-2026-93817 (perf). Les CVSS et prérequis individuels de ces six dernières CVE ne sont pas vérifiés ici ; elles ne sont pas incluses dans le compteur.

Pour PwnEscape : vecteur local depuis un environnement invité/une charge contrôlée, privilèges faibles, aucune interaction selon la qualification précédente ; impact possible sur l'isolation des VM. Ne pas qualifier toutes les CVE kernel de RCE réseau sans authentification.

Actions : vérifier `dnf updateinfo list --security`, la version des RPM kernel, puis planifier un redémarrage et contrôler `uname -r`, les modules tiers, le réseau et les logs.

Sources :
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://access.redhat.com/errata/RHSA-2026:86033

## 3. Proxmox VE — kernel, manager et qemu-server
**Proxmox VE 9.2** est la branche majeure/mineure vérifiée. **`pve-manager 9.2.21`** est la dernière version identifiée dans le dépôt officiel **pve-no-subscription** consulté. Les changelogs **9.2.22 et 9.2.23** apparaissent dans **pve-test** et ne sont donc pas présentés comme disponibles en no-subscription.

**Le kernel Proxmox est un paquet distinct de pve-manager.** Le kernel `7.0.14-25-pve` a été signalé disponible en no-subscription dans les collectes précédentes ; correctif PwnEscape indiqué depuis `7.0.14-24-pve`. La disponibilité précise du dernier kernel n'a pas été revalidée aujourd'hui, et aucune régression propre au -25 n'est confirmée.

Six avis Proxmox du 19 octobre, corrigés selon les bulletins précédents par **`qemu-server >= 9.2.11`** :
- PSA-2026-00069 : exposition de mot de passe cloud-init dans des snapshots.
- PSA-2026-00070 : accès non autorisé à des snippets, selon privilèges délégués et accès invité.
- PSA-2026-00071 : contournement de permissions cloud-init.
- PSA-2026-00072 : réponses QEMU Guest Agent sans limite, épuisement mémoire hôte.
- PSA-2026-00073 : déni de service pvedaemon via Guest Agent.
- PSA-2026-00074 : contrôle de privilèges insuffisant des commandes QEMU HMP.

CVSS individuels non vérifiés. Contrôler `pveversion -v`, `uname -r`, les dépôts APT et les droits des utilisateurs avant déploiement ; surveiller les régressions après mise à jour.

Sources :
- https://proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://sg3.metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-test/p/pve-manager/
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/

## 4. OpenSSH
Version amont **10.6p1** signalée lors de la collecte précédente (6 octobre), avec corrections SFTP, GSSAPI et compression SSH. La version amont n'a pas été revalidée aujourd'hui. Aucune nouvelle CVE du 23 octobre confirmée dans la présente collecte. Sur Rocky/RHEL, tenir compte des backports de sécurité et des errata RPM plutôt que de comparer seulement le numéro amont.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx
L'éditeur confirme les cinq CVE suivantes, corrigées dans **1.30.6+ et 1.31.7+** :

| CVE | Gravité éditeur | Impact et condition |
| --- | --- | --- |
| CVE-2026-15176 | medium | Buffer overflow avec alias/rewrite |
| CVE-2026-93479 | medium | Buffer overflow lors de l'évaluation de scripts |
| CVE-2026-95101 | medium | Injection de corps de requête HTTP/3 |
| CVE-2026-97629 | medium | Falsification de session via tickets TLS |
| CVE-2026-97205 | medium | Use-after-free avec AIO |

Les CVSS numériques, prérequis exacts et privilèges individuels ne sont pas confirmés ici : CVE exclues des compteurs numériques. Vérifier les modules et fonctionnalités réellement activés, les paquets installés et les éventuels correctifs rétroportés.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB
L'éditeur confirme la sortie de **MariaDB Community Server 10.11.19** le **24 août 2026**. CVE associées dans les notes de sécurité collectées précédemment :

| CVE | CVSS rapporté | Version corrective rapportée |
| --- | ---: | --- |
| CVE-2026-92262 | 9.9 | 10.11.19 |
| CVE-2026-85746 | 9.9 | 10.11.19 |
| CVE-2026-97380 | 8.8 | 10.11.19 |
| CVE-2026-107815 | 8.5 | 10.11.19 |
| CVE-2026-107818 | 8.4 | 10.11.19 |

Les prérequis précis, privilèges et interactions de ces CVE n'ont pas été revalidés individuellement dans la collecte du jour. Ne pas inférer une RCE sans authentification du seul score CVSS. Vérifier version serveur, RPM et paramètres CONNECT, Galera, chiffrement et réplication.

Sources :
- https://mariadb.com/resources/blog/mariadb-community-server-q3-2026-maintenance-releases/
- https://mariadb.com/docs/release-notes/community-server

## Priorités opérationnelles
1. **CRITICAL** : patcher GitLab, sauvegarder et valider CI/CD, runners et secrets.
2. **CRITICAL** : contrôler les correctifs de sécurité des kernels Proxmox et Rocky/RHEL, puis le kernel effectivement démarré.
3. **WARNING** : contrôler `qemu-server >= 9.2.11` sur Proxmox et les délégations de privilèges.
4. **WARNING** : mettre à jour Nginx et MariaDB selon les modules, branches et errata réellement utilisés.
5. Vérifier les correctifs rétroportés OpenSSH et les configurations SSH.

Rapport strictement défensif. Aucun mode opératoire d'exploitation.
