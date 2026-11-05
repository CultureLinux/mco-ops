---
type: mco-daily
date: 2026-11-05
generated_at: 2026-11-05T07:59:34+01:00
timezone: Europe/Paris
status: critical
critical_cve: 5
important_cve: 18
rocky_kernel_latest: "5.14.0-687.64.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — jeudi 5 novembre 2026

## Résumé exécutif
- **P0 — Rocky Linux/RHEL 9 :** kernel Rocky `5.14.0-687.64.1.el9_8` toujours présent dans BaseOS x86_64 ; correctif de la vulnérabilité réseau TCP IPv4 CVE-2026-98432 (CVSS 9.8) associé à la version RHEL équivalente. Vérifier les avis Rocky et les noyaux réellement démarrés.
- **P0 — GitLab Self-Managed :** maintenir en priorité les correctifs des RCE sans authentification CVE-2026-110264 et CVE-2026-97229, ainsi que la remédiation des certificats eMudhra, runners et images CI/CD.
- **P1 — Proxmox VE :** vérifier séparément kernel actif, `pve-manager`, `qemu-server` et avis PSA.
- **P1 — OpenSSH, Nginx, MariaDB :** surveiller correctifs upstream et backports RPM. Aucune nouvelle publication du 5 novembre suffisamment confirmée dans cette collecte ciblée.

## Comptage des CVE et fiabilité
Règle : CVSS v3.1 **critique >= 9.0**, **importante >= 7.0 et < 9.0** ; dédoublonnage par identifiant CVE, quel que soit le nombre de produits touchés. Les valeurs du front matter (**5 critiques, 18 importantes**) sont **les 23 identifiants distincts du suivi du 4 novembre**, repris sans nouvelle addition et **non revalidés individuellement aujourd'hui**. Elles ne représentent pas les nouvelles CVE du 5 novembre ni un inventaire exhaustif. Les CVE sans score individuel confirmé sont exclues ; il faut revalider les scores et les listes historiques auprès des fournisseurs.

CVE critiques suivies (historique) : CVE-2026-98432 (9.8), CVE-2026-110264 (10.0), CVE-2026-97229 (10.0), CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).

CVE importantes suivies (historique) : CVE-2026-15723, CVE-2026-112212, CVE-2026-92883, CVE-2026-16301, CVE-2026-14814, CVE-2026-81839, CVE-2026-96893, CVE-2026-99999, CVE-2026-107823, CVE-2026-107821, CVE-2026-107818, CVE-2026-107815, CVE-2026-107814, CVE-2026-102523, CVE-2026-97380, CVE-2026-86047, CVE-2026-89603, CVE-2026-98466.

## Rocky Linux / RHEL 9
**Vérification fraîche du dépôt officiel BaseOS x86_64 :** `kernel-5.14.0-687.64.1.el9_8.x86_64.rpm` figure toujours dans l'index, daté du 2 novembre 2026 à 20:00 UTC. Aucun paquet plus récent n'a été identifié dans l'index consulté. Le dépôt `9.8/security` est distinct : ne pas confondre ses versions suffixées `.0.1` avec BaseOS.

**RHEL :** RHSA-2026:93412 (référence du suivi précédent) associe la version `5.14.0-687.64.1.el9_8` à CVE-2026-98432. Risque réseau sans authentification, sans privilèges ni interaction ; corruption mémoire / DoS et possibilité de RCE sous conditions. Le détail de l'erratum Rocky n'a pas été vérifié séparément ce matin.

**Action P0 :** `dnf updateinfo list security`, `dnf repoquery --latest-limit 1 kernel-core`, `uname -r` ; vérifier l'espace `/boot`, les modules tiers, la fenêtre de redémarrage et la version du kernel après reboot. Ne pas déduire le correctif actif de la seule présence du RPM dans le dépôt.

Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://access.redhat.com/errata/RHSA-2026:93412

## Proxmox VE
Références **du suivi précédent, non reconfirmées comme dernières versions ce matin** : Proxmox VE **9.2**, `pve-manager 9.2.24`, kernel **`7.0.14-26-pve`**, `qemu-server 9.2.13`. La version du kernel et celle de `pve-manager` sont des composants distincts. Le suivi antérieur indique que CVE-2026-98432 est corrigée depuis `7.0.14-25-pve`. Les avis PSA-2026-00075 à PSA-2026-00080 exigent toujours une vérification de conformité.

**Action P1 :** `pveversion -v`, `uname -r`, `apt policy pve-manager proxmox-kernel-7.0 qemu-server` ; contrôler les avis Proxmox et qualifier les régressions possibles ZFS, KVM, LXC, stockage et réseau avant redémarrage.

Source : https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/

## GitLab Self-Managed CE/EE et Runner
Alertes **historiques non réévaluées aujourd'hui** : CVE-2026-110264 (CVSS 10.0, RCE sans authentification) et CVE-2026-97229 (CVSS 10.0, RCE en lien avec CI/CD sans authentification). Vecteur réseau, privilèges et interaction à confirmer sur les bulletins individuels avant toute décision de qualification détaillée. Versions correctives de référence : **19.3.7 / 19.4.5 / 19.5.3** ; elles ne sont **pas confirmées comme dernières versions** ce matin.

CVE-2026-111943 : suivi du bulletin du 29 octobre sur les certificats intermédiaires eMudhra compromis ; score CVSS non confirmé, non comptabilisé. Vérifier séparément GitLab Runner (référence corrective historique **19.5.1**), AI Gateway et images CI/CD embarquant un magasin de certificats.

**Action P0 :** comparer versions installées aux notes officielles, prioriser les instances exposées à Internet et les fonctions CI/CD ; vérifier la chaîne de confiance et les secrets/tokens sans publier de valeurs sensibles.

Sources : https://docs.gitlab.com/releases/patches/ ; https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## OpenSSH
Aucun nouvel avis daté du 5 novembre confirmé. Référence upstream du suivi précédent : **10.6p1**, non reconfirmée aujourd'hui. Pour Rocky 9, les versions RPM peuvent différer par rétroportage. Contrôler `rpm -q openssh openssh-server` et `dnf updateinfo list security`. Source : https://www.openssh.com/releasenotes.html

## Nginx
Aucun nouvel avis daté du 5 novembre confirmé. Référence précédente : **1.31.8** en mainline, avec correctifs de sécurité dans les branches 1.30.6/1.31.7 ; ne pas déduire la sécurité d'une simple comparaison de numéros de version entre branches. Vérifier `nginx -v`, `rpm -q nginx` et les avis du fournisseur RPM. Source : https://nginx.org/en/security_advisories.html

## MariaDB
Aucun nouvel avis daté du 5 novembre confirmé. Référence précédente : **10.11.19** sur la branche 10.11 LTS, non reconfirmée aujourd'hui. Vérifier `mariadb --version`, les paquets installés, les notes de sécurité de la branche et les backports. Source : https://mariadb.com/kb/en/security/

## Priorités opérationnelles
1. **P0 :** valider et déployer le kernel Rocky corrigé, puis contrôler `uname -r` sur chaque machine redémarrée.
2. **P0 :** mettre à niveau GitLab et Runner séparément, auditer les images CI/CD et certificats de confiance.
3. **P1 :** contrôler kernel Proxmox actif, avis PSA, versions des paquets et régressions.
4. **P1 :** comparer OpenSSH/Nginx/MariaDB aux bulletins officiels et aux errata RPM.

**Limites :** recherche officielle ciblée, non exhaustive. Les versions Proxmox, GitLab, OpenSSH et MariaDB et les compteurs historiques n'ont pas été reconfirmés comme derniers états aujourd'hui ; aucune nouvelle CVE n'a été ajoutée sans preuve. Aucune instruction offensive.
