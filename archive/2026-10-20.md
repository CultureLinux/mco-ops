---
type: mco-daily
date: 2026-10-20
timezone: Europe/Paris
status: action-required
---

# Brief MCO — mardi 20 octobre 2026

## Résumé exécutif
- GitLab 19.5 est disponible depuis le 15 octobre. Vérifier les Security Patch Releases et corriger en priorité les instances encore en 19.3.x.
- RHEL 9 : errata kernel RHSA-2026:83156 (14 octobre), RHSA-2026:81552 (13 octobre), RHSA-2026:76739 (8 octobre). Confirmer les correspondances Rocky avant déploiement.
- Proxmox VE : vérifier le kernel PVE réellement installé et les bulletins officiels avant toute annonce de correctif ou régression.
- OpenSSH, Nginx, MariaDB : nouveaux périmètres MCO ; aucune nouvelle CVE datée du 20 octobre n'a pu être confirmée dans les sources consultées.

## Rocky Linux / RHEL 9
| CVE | Erratum RHEL 9 | Date |
| --- | --- | --- |
| CVE-2026-80985 | RHSA-2026:83156 | 2026-10-14 |
| CVE-2026-64508 | RHSA-2026:81552 | 2026-10-13 |
| CVE-2026-74669 | RHSA-2026:76739 | 2026-10-08 |
| CVE-2026-97417 | RHSA-2026:76739 | 2026-10-08 |

Les vecteurs, CVSS, prérequis, versions Rocky corrigées et risques de régression n'ont pas été vérifiés : ne pas les inférer.

Sources :
- https://access.redhat.com/security/cve/cve-2026-80985
- https://access.redhat.com/security/cve/cve-2026-64508
- https://access.redhat.com/security/cve/cve-2026-74669
- https://access.redhat.com/security/cve/cve-2026-97417

## Proxmox VE
Aucune nouvelle version ou régression datée du 20 octobre confirmée lors de cette collecte. Contrôler les avis de sécurité, la version du noyau PVE, les changelogs et le redémarrage effectif avant validation MCO.

Source : https://pve.proxmox.com/wiki/Main_Page

## GitLab Self-Managed CE/EE
GitLab 19.5, publié le 15 octobre 2026, est la dernière version mineure identifiée. Le détail des Security/Critical Patch Releases, les CVE/CVSS, leurs vecteurs, privilèges, interactions, versions affectées et versions corrigées n'a pas pu être vérifié de manière exhaustive aujourd'hui. En particulier, vérifier en priorité les risques RCE, contournement d'authentification, fuites de tokens et secrets, pipelines CI/CD et accès sans authentification. Ne pas confondre version mineure et version corrigée.

Source : https://about.gitlab.com/whats-new/

## OpenSSH
Surveiller les avis de sécurité OpenSSH, les versions amont et les correctifs backportés dans RHEL/Rocky 9. Aucune nouvelle CVE confirmée pour le 20 octobre dans la collecte. Vérifier l'exposition SSH, les méthodes d'authentification et les politiques de clés.

Source : https://www.openssh.com/security.html

## Nginx
Surveiller les avis officiels, les branches stables/mainline et les correctifs de distribution. Aucune nouvelle CVE confirmée pour le 20 octobre dans la collecte. Priorité aux failles exploitables à distance sans authentification et à l'exposition TLS/reverse proxy.

Source : https://nginx.org/en/security_advisories.html

## MariaDB
Surveiller les publications de sécurité MariaDB, particulièrement la branche 10.11 LTS, et les correctifs des distributions. Aucune nouvelle CVE confirmée pour le 20 octobre dans la collecte. Priorité aux accès non authentifiés, à l'élévation de privilèges, aux fuites de données et aux risques de corruption.

Source : https://mariadb.com/kb/en/security/

## Décisions MCO
1. Vérifier les correctifs GitLab effectivement applicables aux versions déployées.
2. Recouper les errata RHEL avec les RPM Rocky 9.
3. Vérifier les versions et les bulletins de sécurité Proxmox, OpenSSH, Nginx et MariaDB.
4. Ne pas annoncer de CVSS, de correctif ni de régression sans source officielle.
