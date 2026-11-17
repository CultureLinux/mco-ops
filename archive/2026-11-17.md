---
type: mco-daily
date: 2026-11-17
generated_at: 2026-11-17T08:00:00+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 4
rocky_kernel_latest: "5.14.0-741.22.1.el9_9"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — mardi 17 novembre 2026

## Résumé exécutif

**P0 — Nouvelle Security Patch Release GitLab du 16 novembre : 19.5.6 / 19.4.8 / 19.3.9, à appliquer rapidement.** Quatre nouvelles CVE importantes confirmées (CVSS 8.7, 8.7, 8.2 et 7.6), dont un accès aux variables CI/CD protégées et une exposition de secrets Kubernetes. Une fuite de fichiers privés sans authentification est également corrigée (CVSS 4.3). **P1 — Rocky Linux 9.9 : nouveau kernel 5.14.0-741.22.1.el9_9 dans BaseOS depuis le 12 novembre**, absent des briefs précédents. Proxmox, OpenSSH, Nginx et MariaDB restent sous surveillance.

## GitLab CE/EE — Security Patch Release du 16 novembre (urgence P0)

Versions corrigées **19.3.9, 19.4.8, 19.5.6** et versions ultérieures correspondantes ; tous modes de déploiement sauf indication contraire. GitLab recommande une mise à jour immédiate. Les quatre CVE importantes confirmées sont distinctes :

| CVE | CVSS | Impact / fonctionnalité | Vecteur et prérequis | Versions affectées |
|---|---:|---|---|---|
| CVE-2026-105739 | 8.7 | XSS dans les pages wiki, JavaScript dans le navigateur d'un lecteur | AV:N / AC:L / PR:L / UI:R ; utilisateur authentifié, interaction requise | >=17.2 avant 19.3.9 ; 19.4 <19.4.8 ; 19.5 <19.5.6 |
| CVE-2026-108153 | 8.7 | XSS dans Error Tracking | AV:N / AC:L / PR:L / UI:R ; utilisateur authentifié, interaction requise | >=16.1 avant 19.3.9 ; 19.4 <19.4.8 ; 19.5 <19.5.6 |
| CVE-2026-18241 | 8.2 | **Contournement d'autorisation de jobs CI/CD : accès à variables et ressources protégées** | AV:N / AC:H / PR:L / UI:N ; authentification requise, aucune interaction | >=9.3 avant 19.3.9 ; 19.4 <19.4.8 ; 19.5 <19.5.6 |
| CVE-2026-97187 | 7.6 | Lecture de **Kubernetes Secrets** d'autres namespaces via workspaces/agent | AV:N / AC:L / PR:H / UI:N ; privilèges élevés, aucune interaction | >=17.6 avant 19.3.9 ; 19.4 <19.4.8 ; 19.5 <19.5.6 |

**Autres alertes notables corrigées dans la même publication :** CVE-2026-104934 (CVSS 4.3), lecture **sans authentification** de fichiers d'un dépôt privé via snippets ; CVE-2026-115736 (CVSS 4.3), accès à des valeurs de secrets non masquées dans Duo Agent Platform EE ; CVE-2026-85778 (CVSS 6.2), exécution de code dans des pipelines protégés via politiques planifiées EE après désaffectation ; CVE-2026-110990 (CVSS 2.3), identifiants GraphQL en clair dans les journaux. Plusieurs autres corrections d'autorisation (Kubernetes agent, LFS, CI/CD templates, Duo) figurent dans l'avis officiel. **Ne pas confondre une fuite de fichiers sans authentification avec une RCE sans authentification.** Aucune nouvelle RCE sans authentification n'est confirmée par cet avis.

**Maintenance :** cette version n'introduit pas de nouvelle migration ; les paquets Omnibus arrêtent/reconfigurent/redémarrent normalement les services pendant la mise à jour. Sauvegarder et contrôler les runners, les variables protégées et les intégrations Kubernetes. Les correctifs Gitaly du 10 novembre (CVE-2026-105437) et eMudhra antérieurs restent inclus dans les branches corrigées correspondantes ; vérifier séparément les versions Runner et les magasins CA des images CI/CD.

Source officielle : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-6-released/
Avis Gitaly précédent : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-4-released/

## Rocky Linux / RHEL 9 — changement de branche 9.9

Le dépôt officiel Rocky Linux 9 BaseOS x86_64 contient **kernel et kernel-core 5.14.0-741.22.1.el9_9**, datés du **12 novembre 2026 à 18:20 UTC**. C'est une évolution importante par rapport au précédent suivi 5.14.0-687.66.1.el9_8 (9.8). Le numéro de paquet confirme sa disponibilité dans ce dépôt, mais ne suffit pas à attribuer des CVE particulières : errata RHSA/RLSA, scores CVSS et régressions propres à cette version non vérifiés dans cette collecte. Sur les hôtes Rocky 9, comparer `cat /etc/rocky-release`, `dnf updateinfo list --security`, `dnf check-update`, `rpm -q kernel-core`, `uname -r`, `df -h /boot` et prévoir redémarrage et validation applicative. Attention aux noyaux installés mais non démarrés.

Source officielle : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## Proxmox VE et kernel PVE

La famille suivie est **Proxmox VE 9.2**. La dernière référence antérieure pour **pve-manager est 9.2.26**, mais sa qualité de dernière version du dépôt n'a pas été reconfirmée ce matin : champ YAML volontairement vide. Le **kernel Proxmox** est un paquet distinct de pve-manager ; la dernière version publiée et d'éventuelles nouvelles régressions/PSA n'ont pas été établies aujourd'hui. Sur les nœuds : `pveversion -v`, `apt policy pve-manager`, `uname -r`, `dpkg -l 'proxmox-kernel*'`, contrôle kdump et sauvegardes avant redémarrage. Ne pas inférer le kernel de la version pve-manager.

Référence officielle : https://pve.proxmox.com/wiki/Package_Repositories

## OpenSSH, Nginx, MariaDB

**OpenSSH :** poursuivre l'examen de CVE-2026-123158 et des configurations effectivement affectées ; pas de nouvelle CVE scorée individuellement confirmée dans cette collecte. Sur Rocky/RHEL, examiner les correctifs rétroportés et les errata plutôt que le seul numéro upstream. Source : https://www.openssh.com/security.html

**Nginx :** six CVE de gravité moyenne documentées officiellement : CVE-2026-15176, CVE-2026-93479, CVE-2026-95101, CVE-2026-97629, CVE-2026-97205 (corrigées en 1.30.6+/1.31.7+) et CVE-2026-90439 (corrigée en 1.30.5+/1.31.6+). Vérifier les fonctionnalités réellement activées (HTTP/3, AIO, TLS tickets, rewrite) et les backports. Source : https://nginx.org/en/security_advisories.html

**MariaDB :** branche 10.11 LTS, référence précédemment suivie 10.11.19 ; aucune nouvelle version ou CVE de cette branche vérifiée aujourd'hui. Examiner les annonces officielles et les correctifs distribués. Source : https://mariadb.org/

## Méthodologie, gravité et limites

Périmètre des compteurs : **CVE distinctes nouvellement identifiées et individuellement scorées dans la collecte du 17 novembre**, pas l'inventaire historique. **Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0**. Dédoublonnage global par identifiant CVE entre produits. **0 critique, 4 importantes** : CVE-2026-105739, CVE-2026-108153, CVE-2026-18241, CVE-2026-97187, toutes documentées par l'avis GitLab du 16 novembre. Les CVE plus faibles et les scores non vérifiés ne sont pas inclus. Le statut `critical` reflète l'urgence opérationnelle du correctif GitLab et l'exposition possible de secrets CI/CD, pas une affirmation de CVSS >=9.0. Les données non vérifiées sont signalées, et aucune instruction offensive n'est fournie.
