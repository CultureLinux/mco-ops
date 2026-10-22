---
type: mco-daily
date: 2026-10-22
timezone: Europe/Paris
status: action-required
---

# Brief MCO — jeudi 22 octobre 2026

## Résumé exécutif
- **P0 GitLab Self-Managed** : Critical Patch du 21 octobre, CVE-2026-110264 (CVSS 10.0), exécution de code à distance sans authentification ni interaction. Mettre à jour immédiatement.
- **P0 Rocky Linux / RHEL 9** : PwnEscape (CVE-2026-99999) corrigée selon RHSA-2026:86033 ; kernel Rocky identifié : 5.14.0-687.59.1.el9_8.
- **P1 Proxmox VE 9** : kernel 7.0.14-25-pve disponible ; six avis du 19 octobre corrigés dans qemu-server >= 9.2.11.
- **P1 Nginx** : cinq vulnérabilités corrigées dans 1.30.6 et 1.31.7.

## GitLab Self-Managed CE/EE — P0
Critical Patch du 21 octobre 2026. Versions corrigées : 18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3 et 19.5.1.

| CVE | CVSS | Impact / prérequis |
| --- | ---: | --- |
| CVE-2026-110264 | 10.0 | RCE ; AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H ; aucune authentification ni interaction ; débordement d'entier lors de la compilation d'une expression régulière ; versions depuis 7.0 avant correctif adapté |
| CVE-2026-15723 | 8.2 | Prise de contrôle de compte LDAP dans certaines configurations OmniAuth SAML / liaison LDAP |
| CVE-2026-112212 | 7.5 | Empoisonnement de cache OAuth/OIDC sans authentification ; interaction ultérieure |
| CVE-2026-12664 | 6.8 | Contournement des restrictions de token read_api |
| CVE-2026-16255 | 5.9 | Contournement des restrictions d'appartenance SAML/LDAP (EE) |
| CVE-2026-105777 | 4.3 | Exposition de credentials internes dans des en-têtes HTTP |
| CVE-2026-13319 | 4.3 | Accès non autorisé à des métadonnées de projets privés |

Les plages affectées et conditions exactes des autres CVE sont propres à chaque bulletin ; ne pas déduire qu'elles affectent toutes les branches depuis 7.0. L'ancienne CVE-2026-97229 (CVSS 10.0) concerne aussi la RCE CI/CD sans authentification ; vérifier sa correction.

**Urgence : P0**. Pour une instance en 19.3.2, cibler au minimum 19.3.6 après sauvegarde et contrôle de compatibilité des runners et pipelines.

Source officielle : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/

## Rocky Linux / RHEL 9 — P0
PwnEscape, CVE-2026-99999 : CVSS Red Hat 8.8, CNA 9.3. Vecteur local depuis une charge ou VM contrôlée, privilèges faibles, aucune interaction. Correctif Red Hat RHSA-2026:86033 du 20 octobre ; kernel Rocky identifié dans les dépôts officiels le 21 octobre : **5.14.0-687.59.1.el9_8**.

Vérifier les errata effectivement appliqués, mettre à jour les hôtes concernés, redémarrer et contrôler uname -r, modules tiers, réseau et journaux. Ne pas confondre présence du paquet et noyau démarré.

Sources :
- https://access.redhat.com/errata/RHSA-2026:86033
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## Proxmox VE 9 — P1
Kernel **7.0.14-25-pve** observé dans no-subscription le 20 octobre ; correctif PwnEscape présent dès **7.0.14-24-pve**. Les régressions spécifiques au -25 ne sont pas confirmées.

Six avis du 19 octobre (PSA-2026-00069 à PSA-2026-00074), corrigés par **qemu-server >= 9.2.11** :
- 00069 : divulgation du mot de passe cloud-init dans une configuration de snapshot.
- 00070 : lecture de snippets sur stockage non autorisé (privilèges VM.Config.Cloudinit, droit de démarrer une VM et accès invité).
- 00071 : contournement de permissions cloud-init avec VM.Config.Network.
- 00072 : réponses QEMU Guest Agent sans limites de taille, épuisement mémoire hôte.
- 00073 : lecture via QEMU Guest Agent pouvant épuiser la mémoire de pvedaemon.
- 00074 : contrôles de privilèges insuffisants pour QEMU HMP, divulgation potentielle d'informations mémoire.

CVSS individuels non confirmés. Vérifier versions des paquets, privilèges délégués et impacts sur VM avant redémarrage.

Source : https://pve.proxmox.com/wiki/Security_Advisories

## OpenSSH
Version amont **10.6p1**, publiée le 6 octobre. Correctifs concernant SFTP, GSSAPI et compression SSH. Pas de nouvelle CVE datée du 21/22 octobre confirmée dans cette collecte. Sur Rocky/RHEL, privilégier la vérification des errata backportés et des paquets installés ; contrôler exposition SSH et authentification.

Source : https://www.openssh.com/security.html

## Nginx — P1
Versions corrigées : **1.30.6** et **1.31.7** (14 octobre).
| CVE | Impact | Gravité officielle |
| --- | --- | --- |
| CVE-2026-15176 | Débordement mémoire avec alias et rewrite | Medium |
| CVE-2026-93479 | Débordement mémoire lors de l'évaluation de scripts | Medium |
| CVE-2026-95101 | Injection de corps de requête HTTP/3 | Medium |
| CVE-2026-97629 | Falsification de session TLS | Medium |
| CVE-2026-97205 | Use-after-free avec AIO | Medium |

CVSS individuels et conditions d'exploitation détaillées non tous confirmés. Vérifier modules et directives effectivement activés, ainsi que les correctifs backportés par la distribution.

Source : https://nginx.org/en/security_advisories.html

## MariaDB
Branche LTS 10.11 : version de maintenance identifiée **10.11.19** (août 2026). CVE documentées corrigées dans cette version : CVE-2026-92262 (CVSS 9.9), CVE-2026-85746 (9.9), CVE-2026-97380 (8.8), CVE-2026-107815 (8.5), CVE-2026-107818 (8.4). Conditions d'exploitation, privilèges, interaction et fonctionnalités exactes à confirmer dans les avis individuels ; ne pas conclure à une RCE accessible depuis le réseau sans analyse des prérequis.

Vérifier la version effective du serveur, l'origine des RPM et les correctifs rétroportés. Pas de nouvelle CVE datée du 22 octobre confirmée.

Source : https://mariadb.com/kb/en/security/

## Actions prioritaires
1. Patcher GitLab Self-Managed contre CVE-2026-110264 ; surveiller aussi CI/CD et fuite de secrets.
2. Appliquer le kernel corrigé Rocky/RHEL 9 aux hôtes exposés et vérifier le redémarrage.
3. Contrôler qemu-server >= 9.2.11 et kernel Proxmox sur chaque nœud.
4. Vérifier les versions et errata Nginx, OpenSSH et MariaDB, avec attention aux backports.

Rapport défensif : aucune procédure d'exploitation.
