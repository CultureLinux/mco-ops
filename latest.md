---
type: mco-daily
date: 2026-11-11
generated_at: 2026-11-11T07:59:46+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.26"
---

# Brief MCO défensif — 11 novembre 2026

## Résumé exécutif
- **Rocky Linux 9** : dernier kernel identifié dans BaseOS x86_64 : `5.14.0-687.66.1.el9_8` (paquet daté du 5 novembre). Aucune révision ultérieure observée dans l'index consulté ; vérifier les errata spécifiques et le kernel actif.
- **Proxmox VE 9.2** : `pve-manager 9.2.26` (référence vérifiée lors de la collecte précédente) ; `qemu-server 9.2.15` (référence précédente). Dernier kernel Proxmox disponible **non confirmé** aujourd'hui. Ne pas confondre `pve-manager` et kernel démarré.
- **GitLab CE/EE** : publication du 29 octobre : `19.5.3`, `19.4.5`, `19.3.7` et autres branches maintenues. Le bulletin officiel précise **aucun correctif de sécurité dans cette patch release**, mais elle retire la confiance dans des certificats eMudhra compromis. Vérifier également le magasin de confiance des runners et images CI/CD. Aucune nouvelle Security/Critical Patch Release confirmée aujourd'hui.
- **Nginx** : six CVE de gravité *medium* dans les avis officiels, versions corrigées indiquées ci-dessous.
- **OpenSSH et MariaDB** : pas de nouvel avis critique vérifié durant cette collecte ; maintenir la veille des correctifs de distribution.

## Règles de comptage et limites
Périmètre du compteur : **nouvelles CVE identifiées et vérifiées pendant la collecte du 11 novembre**, sans double comptage interproduits. Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0. Résultat : **0 critique, 0 importante** dans ce périmètre. Cela ne constitue **pas** un inventaire des vulnérabilités non corrigées ni une attestation d'absence de CVE. Les alertes historiques de RCE/auth bypass, secrets/tokens et CI/CD doivent être revalidées individuellement (score, vecteur AV/AC/PR/UI, versions affectées/corrigées) avant toute attribution chiffrée ; les scores historiques non corroborés ne sont pas reconduits.

## Rocky Linux / RHEL 9
L'index BaseOS officiel montre `kernel-5.14.0-687.66.1.el9_8` et le paquet correspondant `kernel-core` datés du 5 novembre 2026. La liste détaillée des CVE corrigées et les éventuelles régressions de cette révision ne sont pas confirmées par un erratum vérifié ici. Contrôler `dnf updateinfo list --security`, `dnf check-update kernel\*`, `uname -r`, la capacité de `/boot`, puis qualifier et redémarrer. Les backports RHEL/Rocky doivent être appréciés à partir des errata RPM, non de la seule version upstream.

Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://access.redhat.com/security/security-updates/

## Proxmox VE
Version majeure suivie : 9.2. Dernier `pve-manager` vérifié lors du rapport précédent : 9.2.26 ; dernière référence `qemu-server` : 9.2.15. **Le dernier kernel Proxmox publié n'a pas été revalidé** ; le kernel 7.0.14-28-pve a été observé sur le parc mais ne prouve pas qu'il soit le plus récent du dépôt. Contrôler `pveversion -v`, `uname -r`, `proxmox-boot-tool kernel list` (si applicable), les avis PSA et les changelogs avant déploiement. Qualifier stockage, réseau, sauvegardes et migrations ; aucune nouvelle régression n'est confirmée aujourd'hui.

Sources : https://www.proxmox.com/en/ ; https://forum.proxmox.com/

## GitLab Self-Managed CE/EE
Le bulletin officiel du 29 octobre 2026 annonce 19.5.3, 19.4.5, 19.3.7, 19.2.10, 19.1.10, 19.0.11 et 18.11.14. Il indique explicitement « This patch release does not include any security fixes », malgré le retrait de confiance des certificats eMudhra. Les versions affectées et corrigées pour d'autres CVE, les scores CVSS, vecteurs, prérequis d'authentification, privilèges, interaction et fonctions touchées **ne sont pas vérifiables pour ce cycle** : ne pas reprendre les anciennes affirmations de RCE sans authentification comme faits établis. Vérifier la version GitLab en service, les runners, les images CI/CD, les certificats et les bulletins Security/Critical Patch Releases avant toute décision. Les migrations DB de 19.5.3 et 19.4.5 peuvent occasionner une interruption sur une instance mononœud.

Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## OpenSSH
Aucun nouvel avis critique daté du 11 novembre n'a été vérifié. Version upstream la plus récente et liste exhaustive des CVE : **non vérifiées** dans cette collecte. Pour Rocky/RHEL, contrôler les errata et les backports de `openssh-server`, l'authentification par clés, l'interdiction du mot de passe root et les journaux SSH.
Source : https://www.openssh.com/security.html

## Nginx
Avis officiels de gravité *medium* : CVE-2026-15176 (alias/rewrite), CVE-2026-93479 (évaluation de script), CVE-2026-95101 (HTTP/3), CVE-2026-97629 (TLS session tickets), CVE-2026-97205 (AIO), CVE-2026-90439 (module HTTP/3). Les cinq premières sont corrigées à partir de 1.30.6 / 1.31.7 ; la dernière à partir de 1.30.5 / 1.31.6. La présence de modules et options conditionne l'exposition. Contrôler la configuration compilée, la version du paquet et les backports.
Source : https://nginx.org/en/security_advisories.html

## MariaDB
La branche 10.11 LTS reste pertinente pour le parc. La version 10.11.19 a été suivie précédemment mais n'a **pas été revalidée** aujourd'hui comme dernière publication. Aucun nouveau CVE critique n'a été confirmé lors de cette collecte. Vérifier les release notes et les errata du paquet installé, ainsi que les impacts sur réplication, InnoDB et sauvegardes.
Source : https://mariadb.com/docs/release-notes/community-server

## Priorités opérationnelles
1. Qualifier le kernel Rocky disponible et vérifier le kernel démarré après mise à jour.
2. Comparer `pve-manager`, `qemu-server` et kernel Proxmox actifs aux dépôts configurés ; ne pas inférer une correction kernel de la version manager.
3. Contrôler les bulletins GitLab réellement applicables et les magasins de certificats des runners/images.
4. Vérifier Nginx, OpenSSH et MariaDB à l'aide des errata de distribution.

**Fiabilité :** absence de nouveau bulletin confirmé ne signifie pas absence de vulnérabilité ; toutes les valeurs non vérifiables sont signalées explicitement.
