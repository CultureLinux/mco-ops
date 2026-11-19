---
type: mco-daily
date: 2026-11-19
generated_at: 2026-11-19T07:58:10+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-741.22.1.el9_9"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — 19 novembre 2026

## Résumé exécutif

**P0 — Nouvelle publication de sécurité Nginx du 18 novembre : dix CVE corrigées dans 1.30.7 (stable) et 1.31.9 (mainline).** Risques importants de corruption mémoire HTTP/2, boucle infinie TLS 1.3 et contournement de vérification de certificat client SNI dans le module stream. Qualifier et corriger rapidement les reverse proxies exposés. **P0 — GitLab :** maintenir l'urgence des correctifs Self-Managed du 16 novembre, particulièrement les secrets CI/CD et Kubernetes. **Rocky Linux 9.9 :** kernel 5.14.0-741.22.1.el9_9 vérifié. Aucun nouveau score CVSS individuel suffisamment vérifié pour alimenter les compteurs aujourd'hui ; cela ne signifie pas absence de risque.

## Nginx — alerte du 18 novembre (P0)

L'éditeur a publié nginx **1.30.7 stable** et **1.31.9 mainline** le **18 novembre 2026**. Dix CVE distinctes sont documentées :

| CVE | Gravité éditeur | Impact / composant | Versions vulnérables | Correctif |
|---|---|---|---|---|
| CVE-2026-123634 | medium | Use-after-free, resolver | 0.6.18–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-123007 | major | Corruption mémoire, contrôle de flux HTTP/2 | 1.9.5–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-121810 | medium | Use-after-free HTTP/3 | 1.25.0–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-123633 | medium | Use-after-free HTTP/2 | 1.19.7–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-118899 | major | Boucle infinie, corps de requête TLS 1.3 | 1.13.0–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-113425 | major | Use-after-free, proxy mail | 0.5.33–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-120993 | minor | Déréférencement NULL, PROXY protocol v2 | 1.31.4–1.31.8 | 1.31.9 |
| CVE-2026-125283 | minor | Débordement mémoire, alias et try_files | 0.9.6–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-120967 | medium | Contournement SNI, validation certificat client stream | 1.25.5–1.31.8 | 1.30.7 / 1.31.9 |
| CVE-2026-123640 | minor | Validation insuffisante, stream ssl_preread | 1.15.2–1.31.8 | 1.30.7 / 1.31.9 |

**Attention :** l'avis Nginx affiche par erreur « CVE-CVE-2026-121810 » ; l'identifiant normalisé est CVE-2026-121810. Les scores CVSS, vecteurs CVSS complets, prérequis d'authentification et interactions utilisateur ne sont pas vérifiés pour ces dix CVE : ne pas déduire ces propriétés des seules descriptions. Les impacts dépendent des modules et de la configuration effectivement activés. Pour Rocky/RHEL, vérifier les backports des paquets avant de conclure qu'un numéro de version ancien est vulnérable.

**Actions défensives :** inventorier les instances Nginx publiques et internes, identifier HTTP/2, HTTP/3, TLS 1.3, stream/mTLS, proxy mail et PROXY protocol, appliquer les paquets corrigés du fournisseur, valider la configuration avec nginx -t, puis recharger après tests. Vérifier les logs et la supervision. Aucun mode opératoire offensif.

Sources : [actualité officielle du 18 novembre](https://nginx.org/2026.html), [avis de sécurité Nginx](https://nginx.org/en/security_advisories.html).

## GitLab Self-Managed CE/EE (P0 maintenu)

Le bulletin de sécurité du **16 novembre 2026**, déjà suivi, annonce les correctifs **19.5.6, 19.4.8 et 19.3.9**. Quatre CVE importantes précédemment recensées : CVE-2026-105739 (CVSS 8.7, XSS Wiki), CVE-2026-108153 (8.7, XSS Error Tracking), CVE-2026-18241 (8.2, accès aux variables et ressources CI/CD protégées) et CVE-2026-97187 (7.6, secrets Kubernetes inter-namespace). Ces CVE ne sont **pas recomptées** aujourd'hui. Le bulletin du 10 novembre concernant CVE-2026-105437 (7.5, Git/Gitaly, risque d'exécution de commandes, réseau, complexité élevée, sans privilège, interaction requise) reste également à traiter si une instance est antérieure aux correctifs.

Pour les XSS, authentification et interaction utilisateur sont nécessaires selon les qualifications précédemment établies ; pour l'accès CI/CD, authentification requise et pas d'interaction ; pour Kubernetes, privilèges élevés. Les plages exactes d'introduction de chaque CVE et une éventuelle publication supplémentaire du 19 novembre n'ont pas été revalidées dans cette collecte : ne pas extrapoler. Vérifier les versions Omnibus, contrôler les variables CI/CD, les tokens, les intégrations Kubernetes et GitLab Runner ; appliquer les correctifs appropriés après sauvegarde et validation. Surveiller particulièrement les attaques sans authentification, fuites de secrets et contournements d'autorisation. Sources : [GitLab releases](https://about.gitlab.com/releases/), [GitLab security](https://about.gitlab.com/security/).

## Rocky Linux / RHEL 9 (P1)

Le dépôt officiel Rocky Linux 9 BaseOS x86_64 présente **kernel 5.14.0-741.22.1.el9_9**, daté du **12 novembre 2026**. Red Hat indique que RHEL 9.9 a été publié le **10 novembre 2026** avec kernel initial 5.14.0-741.14.1.el9_9 (RHBA-2026:97422). Ne pas confondre ce kernel de sortie avec le paquet Rocky plus récent. Les correspondances exhaustives RHSA/RLSA/CVE de la révision 741.22.1 ne sont pas vérifiées ici. Contrôler dnf updateinfo, les mises à jour proposées, /boot, les kernels installés et uname -r, puis qualifier les redémarrages. Sources : [dépôt Rocky BaseOS](https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/), [dates de publication RHEL](https://access.redhat.com/articles/red-hat-enterprise-linux-release-dates).

## Proxmox VE (P1)

**Proxmox VE 9.2** est confirmé officiellement ; la série utilise **Linux 7.0** comme kernel stable par défaut. La dernière version du paquet **pve-manager** n'a pas été vérifiée aujourd'hui : champ YAML vide. Les références antérieures 9.2.26 (pve-manager) et 7.0.14-28-pve (kernel observé sur le parc) ne doivent pas être présentées comme les dernières versions publiées. Aucune nouvelle régression spécifique n'est confirmée. Sur les nœuds, comparer pveversion -v, apt policy pve-manager, les paquets proxmox-kernel installés et uname -r ; contrôler les changements de kernel et les alertes kdump avant reboot. Source : [Proxmox VE 9.2 officiel](https://www.proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2).

## OpenSSH (P2)

Suivi de CVE-2026-123158 signalée dans les briefs antérieurs : conditions spécifiques d'exposition à confirmer selon les builds et les configurations. Aucun nouvel avis daté du 19 novembre ni score CVSS confirmé pendant cette collecte. Vérifier les correctifs rétroportés Rocky/RHEL plutôt que de conclure à partir du seul numéro OpenSSH. Sources de contrôle : [OpenSSH](https://www.openssh.com/), [Red Hat CVE](https://access.redhat.com/security/security-updates/).

## MariaDB (P2)

La référence précédente suivie est MariaDB **10.11.19 LTS**, non reconfirmée comme toute dernière version aujourd'hui. Aucun nouvel avis de sécurité individuellement confirmé. Vérifier les branches maintenues, les errata des distributions et la compatibilité des mises à jour. Source : [MariaDB Foundation](https://mariadb.org/).

## Méthodologie et limites

Périmètre : nouveautés publiées ou découvertes depuis le rapport du 18 novembre, plus vulnérabilités non résolues prioritaires. **critical_cve** = nombre de CVE distinctes nouvellement qualifiées dans cette collecte avec score CVSS vérifié >= 9.0 ; **important_cve** = nombre de CVE distinctes nouvellement qualifiées avec CVSS vérifié >= 7.0 et < 9.0. Une CVE présente sur plusieurs produits ne compte qu'une fois. Une CVE déjà comptabilisée précédemment n'est pas recomptée. Les dix nouvelles CVE Nginx ont une gravité *éditeur* vérifiée mais **pas de score CVSS individuel vérifié** dans cette collecte : elles ne sont donc pas artificiellement classées dans les compteurs (0/0). **0/0 ne signifie ni absence de vulnérabilité ni absence de CVE nouvelles.** Statut **critical** en raison des nouvelles vulnérabilités Nginx exposées et de l'urgence GitLab persistante, indépendamment des compteurs. Les données non vérifiées restent signalées comme telles.
