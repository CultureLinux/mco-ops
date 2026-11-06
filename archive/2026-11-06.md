---
type: mco-daily
date: 2026-11-06
generated_at: 2026-11-06T08:02:11+01:00
timezone: Europe/Paris
status: critical
critical_cve: 5
important_cve: 18
rocky_kernel_latest: "5.14.0-687.65.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — vendredi 6 novembre 2026

## Résumé exécutif
- **P0 — Nouveau Rocky Linux 9 :** le dépôt officiel BaseOS x86_64 propose le kernel **5.14.0-687.65.1.el9_8**, daté du **3 novembre 2026 à 17:13 UTC**. Version précédente suivie : 5.14.0-687.64.1.el9_8. Qualifier la mise à jour et redémarrer pour l'activer.
- **P0 — GitLab :** maintenir les remédiations des RCE sans authentification précédemment suivies et des certificats eMudhra compromis ; contrôler serveur, Runner et images CI/CD séparément.
- **P1 — Proxmox :** vérifier kernel actif et paquets pve-manager/qemu-server indépendamment, ainsi que les avis PSA.
- **P1 — OpenSSH, Nginx, MariaDB :** comparer versions installées, bulletins officiels et backports de distribution.

## Méthode et limites des compteurs
Règle : CVSS v3.1 **critique >= 9.0**, **importante >= 7.0 et < 9.0**. Une CVE est comptée une seule fois par identifiant, même si plusieurs produits sont affectés. Les valeurs **5 critiques / 18 importantes** correspondent au registre historique du rapport du 5 novembre, **non réévalué individuellement ce matin** ; ce ne sont pas 23 nouvelles CVE. Aucune nouvelle CVE n'est ajoutée sans score confirmé. Les scores historiques et la liste ne constituent pas un inventaire exhaustif.

Critiques historiques : CVE-2026-98432 (9.8), CVE-2026-110264 (10.0), CVE-2026-97229 (10.0), CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).
Importantes historiques : CVE-2026-15723, CVE-2026-112212, CVE-2026-92883, CVE-2026-16301, CVE-2026-14814, CVE-2026-81839, CVE-2026-96893, CVE-2026-99999, CVE-2026-107823, CVE-2026-107821, CVE-2026-107818, CVE-2026-107815, CVE-2026-107814, CVE-2026-102523, CVE-2026-97380, CVE-2026-86047, CVE-2026-89603, CVE-2026-98466.

## Rocky Linux / RHEL 9
**Version vérifiée fraîchement :** `kernel-5.14.0-687.65.1.el9_8.x86_64.rpm` et `kernel-core` correspondants, présents dans le dépôt officiel BaseOS x86_64, datés du **03/11/2026 17:13 UTC**. Cette présence ne garantit pas que le correctif soit installé ou démarré sur les hôtes.

**Risque prioritaire hérité :** CVE-2026-98432 (CVSS 9.8), vulnérabilité réseau TCP IPv4 sans authentification, sans privilèges ni interaction ; corruption mémoire/DoS et possibilité de RCE sous conditions selon le suivi Red Hat précédent. RHSA-2026:93412 associait le correctif RHEL au kernel 5.14.0-687.64.1.el9_8 ; l'erratum propre à la version .65.1 et ses CVE spécifiques n'ont pas été confirmés ce matin. Ne pas attribuer automatiquement de CVE supplémentaires à cette version.

**Actions :** `dnf updateinfo list security`, `dnf repoquery --latest-limit 1 kernel-core`, `uname -r` ; contrôler espace /boot, DKMS/modules tiers et régression applicative, puis redémarrer en fenêtre MCO et revérifier le kernel actif.

Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://access.redhat.com/errata/RHSA-2026:93412

## Proxmox VE
Références **historiques non reconfirmées comme dernières versions aujourd'hui** : Proxmox VE 9.2, **pve-manager 9.2.24**, kernel **7.0.14-26-pve**, qemu-server 9.2.13. Le kernel et pve-manager sont des paquets distincts. Le suivi antérieur situe la correction de CVE-2026-98432 à partir de 7.0.14-25-pve ; vérifier les avis PSA-2026-00075 à PSA-2026-00080 et les correctifs associés.

**Actions :** `pveversion -v`, `uname -r`, `apt policy pve-manager proxmox-kernel-7.0 qemu-server` ; qualifier régressions KVM, ZFS, réplication, réseau et démarrage avant reboot. Source : https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/

## GitLab Self-Managed CE/EE et Runner
Alertes historiques à haute priorité : **CVE-2026-110264** et **CVE-2026-97229** (CVSS 10.0, RCE sans authentification selon bulletins précédemment suivis ; exposition réseau, sans privilèges ni interaction, dont chaîne CI/CD). Pour les installations affectées, traiter comme **P0**. Les détails techniques, périmètres de versions et conditions exactes doivent être reconfirmés sur les avis officiels avant toute qualification de non-exposition.

**CVE-2026-111943** : suivi de certificats intermédiaires eMudhra compromis ; risque de confiance TLS et de chaîne CI/CD. Score individuel non confirmé et donc exclu du compteur. Versions correctives de référence du suivi précédent : GitLab **19.3.7, 19.4.5, 19.5.3** ; Runner **19.5.1**. Leur statut de versions les plus récentes n'a pas été reconfirmé aujourd'hui. Mettre à jour GitLab et Runner indépendamment, examiner les images CI/CD contenant leur propre magasin de certificats et les éventuels secrets/tokens exposés.

Sources : https://docs.gitlab.com/releases/patches/ ; https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## OpenSSH
Aucun nouvel avis du 6 novembre confirmé dans la collecte ciblée. Version upstream de référence historique **10.6p1** (non reconfirmée). Vérifier `sshd -V` si disponible, `rpm -q openssh-server`, `dnf updateinfo`, l'authentification par clé, les accès root et les backports. Source : https://www.openssh.com/security.html

## Nginx
Aucun nouvel avis du 6 novembre confirmé. Référence historique mainline **1.31.8** (non reconfirmée). Vérifier `nginx -v`, paquets, modules tiers, correctifs de sécurité et compatibilité TLS/reverse proxy. Source : https://nginx.org/en/security_advisories.html

## MariaDB
Aucun nouvel avis du 6 novembre confirmé. Référence historique de la branche 10.11 LTS **10.11.19** (non reconfirmée). Vérifier `mariadb --version`, `rpm -q MariaDB-server mariadb-server`, réplication, sauvegardes, correctifs rétroportés et compatibilité applicative. Source : https://mariadb.com/kb/en/security/

## Priorités opérationnelles
1. **P0 :** qualifier le kernel Rocky .65.1 et vérifier la version réellement démarrée après redémarrage.
2. **P0 :** auditer GitLab, Runner, certificats de confiance et images CI/CD.
3. **P1 :** contrôler Proxmox (kernel séparé de pve-manager), avis PSA et risques de régression.
4. **P1 :** rapprocher OpenSSH, Nginx et MariaDB des bulletins officiels et backports RPM.

**Fiabilité :** vérification fraîche du dépôt Rocky ; les autres versions et les compteurs sont explicitement historiques et ne sont pas présentés comme nouvellement vérifiés. Aucune instruction offensive.
