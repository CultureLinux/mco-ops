---
type: mco-daily
date: 2026-11-03
generated_at: 2026-11-03T08:03:50+01:00
timezone: Europe/Paris
status: critical
critical_cve: 5
important_cve: 18
rocky_kernel_latest: "5.14.0-687.61.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — mardi 3 novembre 2026

## Résumé exécutif
- **P0 — Kernel Linux :** CVE-2026-98432 (CVSS 9.8), défaut IPv4 TCP accessible sans authentification ; le correctif RHEL 9 est annoncé dans RHSA-2026:93412, tandis que la disponibilité du paquet Rocky équivalent n'a pas été confirmée aujourd'hui.
- **P0 — GitLab CE/EE :** maintenir la remédiation des RCE CVE-2026-110264 et CVE-2026-97229 (CVSS 10.0) et la rotation/reconstruction de confiance des certificats eMudhra (CVE-2026-111943), y compris les runners et images CI.
- **P1 — Proxmox VE :** vérifier le kernel démarré (correctif CVE-2026-98432 depuis 7.0.14-25-pve), pve-manager >= 9.2.24 et qemu-server >= 9.2.11.
- **P1 — OpenSSH, Nginx, MariaDB :** aucune nouvelle release ou alerte datée du 3 novembre confirmée dans cette collecte ; contrôler les versions réellement installées et les correctifs rétroportés.

## Méthode de comptage
Comptage des **CVE distinctes explicitement répertoriées** dans le rapport précédent du 2 novembre, et non de toutes les vulnérabilités des fournisseurs ni des nouveautés du jour. Seuils CVSS v3.1 : **critique >= 9.0**, **importante >= 7.0 et < 9.0**. Une CVE touchant plusieurs produits ne compte qu'une fois. Les CVE sans score individuel vérifié sont exclues. **Aucune nouvelle CVE n'a été ajoutée au décompte aujourd'hui** ; les 5 critiques et 18 importantes sont reportées du rapport précédent et doivent être réévaluées lors de toute nouvelle publication.

CVE critiques suivies (5) : CVE-2026-98432 (9.8), CVE-2026-110264 (10.0), CVE-2026-97229 (10.0), CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).

CVE importantes suivies (18) : CVE-2026-15723, CVE-2026-112212, CVE-2026-92883, CVE-2026-16301, CVE-2026-14814, CVE-2026-81839, CVE-2026-96893, CVE-2026-99999, CVE-2026-107823, CVE-2026-107821, CVE-2026-107818, CVE-2026-107815, CVE-2026-107814, CVE-2026-102523, CVE-2026-97380, CVE-2026-86047, CVE-2026-89603, CVE-2026-98466.

## Rocky Linux / RHEL 9
Dernier kernel Rocky 9 x86_64 **précédemment vérifié** : `5.14.0-687.61.1.el9_8`. Ce n'est **pas une confirmation de fraîcheur du dépôt au 3 novembre**. RHEL 9 a publié RHSA-2026:93412 le 1er novembre, avec le kernel `5.14.0-687.64.1.el9_8` et le correctif CVE-2026-98432. Il faut contrôler les métadonnées de dépôt Rocky avant d'affirmer que la correction y est disponible. Vérifier `dnf updateinfo list security`, `dnf repoquery --latest-limit 1 kernel-core`, `uname -r` et l'espace /boot avant mise à jour et redémarrage.

Sources : https://access.redhat.com/errata/RHSA-2026:93412 ; https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## Proxmox VE
Dernier état vérifié dans les rapports précédents : Proxmox VE 9.2 ; `pve-manager 9.2.24` ; kernel `7.0.14-26-pve` ; `qemu-server 9.2.13`. **Version pve-manager et version kernel sont distinctes.** Le kernel `7.0.14-25-pve` corrige CVE-2026-98432 ; contrôler `uname -r` après redémarrage. Les avis PSA-2026-00075 à PSA-2026-00080 impliquent notamment des chemins conditionnels d'exécution root ; vérifier les privilèges Sys.Modify et les interfaces d'administration. Aucune régression nouvelle datée du 3 novembre n'a été confirmée.

Source : https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/

## GitLab Self-Managed CE/EE
Priorité P0 aux CVE-2026-110264 et CVE-2026-97229 : RCE, CVSS 10.0, vecteur réseau, privilèges non requis, interaction non requise ; exposition conditionnée aux fonctionnalités concernées, notamment les expressions régulières et la configuration CI/CD. Les branches corrigées suivies sont 19.3.7, 19.4.5 et 19.5.3 ; les notes du 29 octobre mentionnent également CVE-2026-111943 liée aux certificats eMudhra compromis. Mettre à jour GitLab Runner **séparément** (version corrective précédemment vérifiée : 19.5.1), ainsi que les images CI/CD et leurs magasins de certificats. Pour toute instance 19.3.2, migration de sécurité prioritaire. Les versions affectées exactes et les conditions propres à chaque fonctionnalité doivent être validées dans les avis GitLab avant de conclure à une exposition.

Sources : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/ ; https://about.gitlab.com/releases/

## OpenSSH, Nginx et MariaDB
**OpenSSH :** référence amont précédemment relevée 10.6p1 ; aucune nouvelle version du jour vérifiée. Sur Rocky 9, comparer les errata et les correctifs rétroportés plutôt que la seule version majeure. Source : https://www.openssh.com/releasenotes.html

**Nginx :** références précédentes 1.30.6 / 1.31.7 ; aucune nouvelle version du jour vérifiée. Contrôler les modules compilés, les paquets et les avis du fournisseur. Source : https://nginx.org/en/security_advisories.html

**MariaDB :** référence LTS précédemment suivie 10.11.19, incluant les CVE critiques CVE-2026-92262 et CVE-2026-85746 ; aucune nouvelle release du jour confirmée. Contrôler la version serveur, les errata et l'exposition réseau. Source : https://mariadb.org/

## Limites de vérification
Collecte ciblée du 3 novembre : aucune nouvelle publication officielle suffisamment étayée pour modifier les chiffres ou versions de référence. Les versions et compteurs marqués « précédemment vérifiés » proviennent du rapport du 2 novembre ; ils ne doivent pas être présentés comme des vérifications fraîches du dépôt au 3 novembre. L'état des correctifs Rocky pour CVE-2026-98432 reste **non confirmé**. Ne pas assimiler absence d'avis trouvé à absence de vulnérabilité.
