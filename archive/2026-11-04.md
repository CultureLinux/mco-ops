---
type: mco-daily
date: 2026-11-04
generated_at: 2026-11-04T07:58:12+01:00
timezone: Europe/Paris
status: critical
critical_cve: 5
important_cve: 18
rocky_kernel_latest: "5.14.0-687.64.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — mercredi 4 novembre 2026

## Résumé exécutif
- **P0 — NOUVEAU Rocky Linux 9 :** le kernel `5.14.0-687.64.1.el9_8` est maintenant présent dans le dépôt officiel BaseOS x86_64 (horodatage 2 novembre 2026, 20:00 UTC). Il remplace la référence `687.61.1` du rapport précédent. L'erratum RHEL RHSA-2026:93412 associe la même version au correctif CVE-2026-98432 (CVSS 9.8). Vérifier l'avis Rocky et le RPM réellement installé avant de conclure à une remédiation effective.
- **P0 — GitLab Self-Managed :** les RCE sans authentification CVE-2026-110264 et CVE-2026-97229 (CVSS 10.0 dans les rapports précédents) restent prioritaires sur les instances non corrigées ; contrôler aussi les certificats eMudhra (CVE-2026-111943), les runners et les images CI/CD.
- **P1 — Proxmox VE :** contrôler le kernel réellement démarré, distinct de `pve-manager`, et les correctifs des avis PSA-2026-00075 à 00080.
- **P1 — OpenSSH, Nginx, MariaDB :** vérifier les correctifs upstream et les backports de distribution ; aucune nouvelle publication datée du 4 novembre confirmée lors de cette collecte.

## Méthode de comptage et limites
Périmètre : **23 CVE distinctes explicitement suivies dans le rapport du 3 novembre**, reprises à titre de continuité et **non réévaluées individuellement aujourd'hui**. CVSS v3.1 : critique si score >= 9.0, importante si 7.0 <= score < 9.0 ; une CVE affectant plusieurs produits n'est comptée qu'une fois. CVE sans score individuel confirmé exclues. Ces compteurs ne représentent ni les nouvelles CVE du jour ni un inventaire exhaustif ; la liste et les scores historiques demandent une revalidation fournisseur.

CVE critiques (5, suivi antérieur) : CVE-2026-98432 (9.8), CVE-2026-110264 (10.0), CVE-2026-97229 (10.0), CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).

CVE importantes (18, suivi antérieur) : CVE-2026-15723, CVE-2026-112212, CVE-2026-92883, CVE-2026-16301, CVE-2026-14814, CVE-2026-81839, CVE-2026-96893, CVE-2026-99999, CVE-2026-107823, CVE-2026-107821, CVE-2026-107818, CVE-2026-107815, CVE-2026-107814, CVE-2026-102523, CVE-2026-97380, CVE-2026-86047, CVE-2026-89603, CVE-2026-98466.

## Rocky Linux / RHEL 9
**NOUVEAU, vérifié dans le dépôt officiel Rocky BaseOS x86_64 :** `kernel-5.14.0-687.64.1.el9_8.x86_64.rpm` et `kernel-core-5.14.0-687.64.1.el9_8.x86_64.rpm`, datés du **2 novembre 2026 à 20:00 UTC**. Le précédent rapport indiquait `5.14.0-687.61.1.el9_8`.

**RHEL :** RHSA-2026:93412 (1er novembre) annonce le kernel `5.14.0-687.64.1.el9_8` en réponse notamment à CVE-2026-98432. Vulnérabilité du traitement TCP IPv4 : exposition réseau sans authentification (AV:N/AC:L/PR:N/UI:N), corruption mémoire / déni de service et risque potentiel de RCE ; ne pas confondre potentiel et exploitation confirmée. Le contenu détaillé de l'erratum Rocky correspondant n'a pas été validé directement : la présence du RPM ne prouve pas que chaque serveur a appliqué le correctif.

**Action P0 :** `dnf updateinfo list security`, `dnf repoquery --latest-limit 1 kernel-core`, `uname -r` ; vérifier l'espace `/boot`, qualifier la mise à jour puis redémarrer et revérifier le noyau démarré. Vérifier les éventuelles régressions applicatives et modules tiers.

Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D ; https://access.redhat.com/errata/RHSA-2026:93412

## Proxmox VE
**Dernières références issues des rapports précédents, non reconfirmées comme dernières versions du dépôt ce matin :** Proxmox VE `9.2` ; `pve-manager 9.2.24` ; kernel `7.0.14-26-pve` ; `qemu-server 9.2.13`. Le kernel corrigé pour CVE-2026-98432 est indiqué depuis `7.0.14-25-pve` dans les avis précédents. **Ne jamais déduire le kernel démarré de la version pve-manager.**

**Action P1 :** `pveversion -v`, `uname -r`, `apt policy proxmox-kernel-7.0 pve-manager qemu-server pve-xtermjs` ; contrôler les avis PSA-2026-00075 à PSA-2026-00080, notamment les chemins conditionnels d'exécution root, les droits `Sys.Modify`, l'interface xtermjs et les risques de régression au redémarrage (Secure Boot, ZFS, KVM).

Source : https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/

## GitLab Self-Managed CE/EE et CI/CD
**RCE critiques historiques à traiter en P0 :** CVE-2026-110264 et CVE-2026-97229, CVSS 10.0 dans les rapports antérieurs ; vecteur réseau, sans authentification ni interaction selon les avis antérieurs. La seconde concerne des fonctionnalités CI/CD. Risques : compromission serveur et secrets/tokens/pipelines ; les conditions détaillées d'exposition et les plages de versions affectées doivent être confrontées au bulletin de chaque branche avant toute affirmation plus précise.

**Versions correctives de référence des bulletins d'octobre (non garanties dernières versions au 4 novembre) :** CE/EE `19.3.7`, `19.4.5`, `19.5.3` ; GitLab Runner `19.5.1` pour le bulletin certificats eMudhra (CVE-2026-111943). Vérifier séparément le serveur, les runners, les chaînes de confiance et les images CI/CD, reconstruire les images concernées. L'état de patch d'une instance `19.3.2` doit être considéré prioritaire.

Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/ ; https://docs.gitlab.com/releases/

## OpenSSH
Aucune nouvelle alerte datée du 4 novembre confirmée. Référence historique suivie : OpenSSH `10.6p1` (fraîcheur non reconfirmée). Contrôler les avis upstream et les errata de la distribution ; une version RPM ancienne peut intégrer des correctifs rétroportés. Examiner l'exposition SSH et les paramètres d'authentification.

Source : https://www.openssh.com/security.html

## Nginx
**Vérifié sur la page officielle :** dernière actualité affichée `1.31.8` mainline (21 octobre 2026) ; `1.30.6` stable et `1.31.7` mainline (14 octobre) corrigent notamment CVE-2026-15176, CVE-2026-93479, CVE-2026-95101, CVE-2026-97629, CVE-2026-97205. Modules et conditions : alias/rewrite, évaluation de scripts, HTTP/3, tickets de session TLS, AIO/SSI. Les scores CVSS individuels ne sont pas réévalués aujourd'hui et ces CVE ne sont pas ajoutées aux compteurs historiques.

**Action :** `nginx -v`, `nginx -V` ; vérifier modules activés, correctifs rétroportés et recharger après validation.

Sources : https://nginx.org/ ; https://nginx.org/en/security_advisories.html

## MariaDB
Aucune nouvelle publication datée du 4 novembre confirmée. Référence antérieure suivie pour la branche 10.11 LTS : `10.11.19` (non reconfirmée comme dernière version). Vérifier les avis MariaDB et les paquets de distribution, puis qualifier réplication, sauvegardes et compatibilité applicative avant mise à jour.

Sources : https://mariadb.org/ ; https://mariadb.com/kb/en/security/

## Priorités opérationnelles
1. **P0** : déployer après qualification le nouveau kernel Rocky, contrôler `uname -r` après reboot ; corriger les instances GitLab et runners non à jour.
2. **P1** : vérifier les versions effectives Proxmox (kernel séparé de pve-manager), les avis PSA et les configurations sensibles.
3. **P1** : auditer les versions et backports OpenSSH/Nginx/MariaDB ; surveiller les nouvelles publications officielles.

**Fiabilité :** données non vérifiées aujourd'hui explicitement signalées ; aucune instruction offensive.
