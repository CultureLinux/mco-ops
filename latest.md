---
type: mco-daily
date: 2026-10-23
timezone: Europe/Paris
status: action-required
---

# Brief MCO — vendredi 23 octobre 2026

## Résumé exécutif
- **P0 GitLab** : CVE-2026-110264, CVSS 10.0, RCE sans authentification ni interaction. Critical Patch du 21 octobre ; mettre à jour immédiatement.
- **P0 Proxmox VE** : PwnEscape (CVE-2026-99999), sortie de VM KVM ; kernel corrigé depuis 7.0.14-24-pve.
- **P1 Proxmox VE** : six avis du 19 octobre, corrigés dans qemu-server >= 9.2.11.
- **P1 Rocky/RHEL 9** : kernel 5.14.0-687.59.1.el9_8 et erratum RHSA-2026:86033.
- **P1 Nginx / MariaDB** : correctifs disponibles dans Nginx 1.30.6 / 1.31.7 et MariaDB 10.11.19.

Aucun nouvel avis officiel daté du 23 octobre n'a été confirmé dans la collecte de ce matin. Cette absence de confirmation ne prouve pas l'absence de nouvelles vulnérabilités.

## 1. GitLab Self-Managed CE/EE — P0
Critical Patch du 21 octobre 2026 : douze vulnérabilités.

| CVE | CVSS | Impact et conditions |
| --- | ---: | --- |
| CVE-2026-110264 | 10.0 | RCE sans authentification ni interaction ; AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H |
| CVE-2026-15723 | 8.2 | Prise de contrôle de compte LDAP, sous configuration OmniAuth SAML et liaison LDAP |
| CVE-2026-112212 | 7.5 | Empoisonnement de cache OAuth/OIDC sans authentification ; interaction ultérieure |
| CVE-2026-12664 | 6.8 | Contournement des restrictions d'un token read_api |
| CVE-2026-16255 | 5.9 | Contournement de restrictions d'appartenance SAML/LDAP |
| CVE-2026-105777 | 4.3 | Exposition de credentials internes dans des en-têtes HTTP |
| CVE-2026-13319 | 4.3 | Lecture de métadonnées de projets privés |
| CVE-2026-112378 | 4.3 | Contournement des restrictions d'accès AI Gateway |
| CVE-2026-105711 | 3.5 | Divulgation d'informations via cache |
| CVE-2026-14288 | 3.1 | Contournement des politiques d'approbation |
| CVE-2026-112326 | 3.1 | Suppression non autorisée de constats de vulnérabilité |
| CVE-2026-103515 | 2.7 | Contournement du verrou administratif IA |

Versions corrigées selon branche : **18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3, 19.5.1**. Les versions affectées et prérequis précis diffèrent selon chaque CVE ; ne pas extrapoler les conditions de la RCE aux autres vulnérabilités.

**Rappel CI/CD :** CVE-2026-97229 (CVSS 10.0), RCE sans authentification, a été corrigée dans la Critical Patch du 12 octobre. Contrôler la version installée, les runners, les pipelines, les tokens et les secrets. Pour une instance encore en 19.3.2, mettre à jour au minimum vers 19.3.6 après sauvegarde et vérification de compatibilité.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 2. Rocky Linux / RHEL 9 — P1
**Kernel Rocky 9 identifié : 5.14.0-687.59.1.el9_8.** Erratum **RHSA-2026:86033** du 20 octobre.

| CVE | Composant | Risque |
| --- | --- | --- |
| CVE-2026-99999 | IPv4/checksum | PwnEscape, corruption mémoire et sortie de VM possible |
| CVE-2026-31678 | Open vSwitch | Gestion incorrecte de références |
| CVE-2026-64024 | TCP | Prédiction possible de numéros de séquence |
| CVE-2026-64032 | Bridge | Use-after-free |
| CVE-2026-80590 | Réseau/GSO | Déni de service |
| CVE-2026-89633 | SMB | Lecture/écriture hors limites |
| CVE-2026-93817 | perf | Gestion incorrecte de durée de vie mémoire |

Pour PwnEscape, CVSS Red Hat 8.8, CNA 9.3 ; vecteur local, privilèges faibles, aucune interaction. Les vecteurs, authentifications et privilèges des autres CVE sont propres à chaque avis ; ne pas les assimiler à des attaques réseau sans authentification.

**Action :** vérifier le RPM corrigé, redémarrer les hôtes concernés, confirmer uname -r, contrôler modules tiers, interfaces réseau et journaux.

Sources :
- https://access.redhat.com/errata/RHSA-2026:86033
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## 3. Proxmox VE 9 — P0/P1
**PwnEscape CVE-2026-99999** : sortie de VM KVM, corrigée depuis **7.0.14-24-pve**. Le kernel **7.0.14-25-pve** est identifié depuis le 20 octobre dans no-subscription. Aucune régression spécifique au -25 confirmée dans la collecte.

Six avis du 19 octobre, tous corrigés par **qemu-server >= 9.2.11** :

| Avis | Impact et prérequis |
| --- | --- |
| PSA-2026-00069 | Divulgation de mot de passe cloud-init dans la configuration de snapshot ; accès à cette configuration |
| PSA-2026-00070 | Lecture de snippets non autorisés ; privilèges VM.Config.Cloudinit, droit de démarrer la VM et accès invité |
| PSA-2026-00071 | Contournement de permissions cloud-init avec VM.Config.Network |
| PSA-2026-00072 | Réponses QEMU Guest Agent sans limite, épuisement mémoire hôte ; agent invité contrôlé |
| PSA-2026-00073 | Déni de service pvedaemon par épuisement mémoire via agent invité |
| PSA-2026-00074 | Contrôles insuffisants des commandes QEMU HMP, divulgation potentielle de mémoire |

Scores CVSS individuels non confirmés. Vérifier les versions installées, les permissions déléguées, l'état des services et les régressions avant et après mise à jour.

Source : https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/

## 4. OpenSSH
Version amont **10.6p1** publiée le 6 octobre : corrections de sécurité concernant les chemins SFTP, GSSAPI, la compression SSH et les restrictions authorized_keys. Aucune nouvelle CVE du 23 octobre confirmée. Sur Rocky/RHEL, vérifier les errata et correctifs rétroportés plutôt que de comparer seulement le numéro de version amont. Contrôler l'exposition réseau et l'authentification SSH.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx — P1
Versions corrigées : **1.30.6** et **1.31.7**.

| CVE | Impact | Gravité officielle |
| --- | --- | --- |
| CVE-2026-15176 | Débordement mémoire avec alias/rewrite | Medium |
| CVE-2026-93479 | Débordement mémoire lors de l'évaluation de scripts | Medium |
| CVE-2026-95101 | Injection de corps de requête HTTP/3 | Medium |
| CVE-2026-97629 | Falsification de session TLS | Medium |
| CVE-2026-97205 | Use-after-free avec AIO | Medium |

Exposition conditionnée par modules, protocoles et directives activés. Scores CVSS et prérequis détaillés non tous confirmés ; ne pas supposer une RCE sans authentification pour chaque CVE.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB — P1
Branche 10.11 LTS : **10.11.19**, publiée le 24 août.

| CVE | CVSS | Version corrective |
| --- | ---: | --- |
| CVE-2026-92262 | 9.9 | 10.11.19 |
| CVE-2026-85746 | 9.9 | 10.11.19 |
| CVE-2026-97380 | 8.8 | 10.11.19 |
| CVE-2026-107815 | 8.5 | 10.11.19 |
| CVE-2026-107818 | 8.4 | 10.11.19 |

Des corrections concernent notamment les privilèges élevés, CONNECT, Galera SST, les transferts chiffrés et la réplication. Les vecteurs, authentifications, privilèges et interactions doivent être vérifiés dans chaque avis ; un score élevé n'implique pas une RCE réseau sans authentification.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Priorités opérationnelles
1. **P0** Mettre à jour GitLab Self-Managed contre CVE-2026-110264 et vérifier l'exposition CI/CD.
2. **P0** Corriger les hyperviseurs Proxmox exposés à PwnEscape et vérifier qemu-server >= 9.2.11.
3. **P1** Appliquer et démarrer le kernel Rocky/RHEL 9 corrigé.
4. **P1** Vérifier Nginx et MariaDB, en tenant compte des backports et des modules activés.
5. Vérifier les errata OpenSSH et la configuration d'authentification.

Aucune procédure offensive.
