---
type: mco-daily
date: 2026-11-14
generated_at: 2026-11-14T08:03:14+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 1
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.26"
---

# Brief MCO défensif — samedi 14 novembre 2026

## Résumé exécutif

- **P0 — GitLab Critical Patch Release du 10 novembre, absente du suivi précédent :** 19.5.4, 19.4.6 et 19.3.8 corrigent **CVE-2026-105437**, débordement de tampon dans Git pouvant mener à l'exécution de commandes dans Gitaly. Mise à jour urgente des instances Self-Managed.
- **P1 — OpenSSH :** nouvel avis CERT-FR du 5 novembre (mis à jour le 6), **CVE-2026-123158**, contournement de politique de sécurité ; versions antérieures à 10.6p2 concernées.
- **P1 — Rocky Linux 9 :** dernier paquet kernel BaseOS x86_64 vérifié : 5.14.0-687.66.1.el9_8.
- **P1 — Proxmox VE 9.2 :** pve-manager 9.2.26 disponible en no-subscription ; 9.2.27 visible en pve-test seulement, ne pas le présenter comme version stable. Dernier kernel Proxmox non vérifié.
- **P1 — Nginx :** six CVE de gravité moyenne documentées, correctifs dans les branches maintenues. **MariaDB :** 10.11.19 LTS, branche communautaire 10.6 en fin de vie.

## GitLab Self-Managed CE/EE — correctif critique du 10 novembre

Bulletin officiel : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-4-released/

**CVE-2026-105437** — vulnérabilité dans Git embarqué, heap-based buffer overflow ; possibilité d'écriture hors limites et d'exécution de commandes dans l'environnement Gitaly sous certaines conditions. **CVSS v3.1 7.5 (important)**, vecteur `CVSS:3.1/AV:N/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:H` selon SUSE/CNA : réseau, complexité élevée, aucun privilège requis, interaction utilisateur requise, confidentialité/intégrité/disponibilité élevées. Le qualificatif **Critical Patch Release** est celui de GitLab ; il ne signifie pas que le score CVSS de cette CVE est >= 9.

Versions affectées selon GitLab : à partir de 16.2 et avant 19.3.8 dans la branche correspondante ; 19.4 avant 19.4.6 ; 19.5 avant 19.5.4. Versions corrigées : **19.3.8, 19.4.6, 19.5.4**. Les paquets Linux et images officiels incluent le Git corrigé ; les installations compilées depuis les sources doivent reconstruire Git/Gitaly ou utiliser un Git corrigé (2.56.1+ ou correctif de distribution).

**Urgence P0 :** comparer immédiatement la version de GitLab Self-Managed aux versions corrigées, mettre à jour et consulter la détection officielle « Git merge-tree child process crash ». Le bulletin ne démontre pas une RCE sans interaction : ne pas confondre absence de privilèges et absence d'interaction.

Source du vecteur : https://www.suse.com/security/cve/CVE-2026-105437.html

## Rocky Linux / RHEL 9

Kernel `5.14.0-687.66.1.el9_8` et `kernel-core` présents dans BaseOS x86_64, datés du 5 novembre 2026. Aucun paquet plus récent identifié dans l'index vérifié. Les errata Red Hat spécifiques et les CVE corrigées par cette révision ne sont pas établis ici ; ne pas extrapoler la couverture des correctifs à partir du seul numéro de version.

Actions : `dnf updateinfo list --security`, `dnf check-update kernel\*`, contrôle de `/boot`, installation qualifiée, redémarrage planifié, puis `uname -r`.

Source : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D

## Proxmox VE

Version majeure **9.2**, `pve-manager 9.2.26` vérifié dans un miroir no-subscription. `9.2.27` apparaît dans le dépôt **pve-test** le 11 novembre ; ne pas déployer automatiquement en production. Le dernier paquet kernel Proxmox publié n'a pas été vérifié aujourd'hui ; les kernels 7.0.14-28-pve précédemment observés sur certains nœuds ne prouvent pas le dernier disponible.

Contrôles : `pveversion -v`, `uname -r`, `apt policy pve-manager proxmox-kernel-7.0`, vérifier la source APT et les PSA, tester sauvegarde, migration, réseau et stockage avant généralisation.

Sources : https://mirror.itp.uni-frankfurt.de/proxmox-ve/dists/trixie/pve-no-subscription/binary-amd64/ ; https://sg2.metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-test/p/pve-manager/ ; https://proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2

## OpenSSH

**CVE-2026-123158**, avis CERT-FR du 5 novembre mis à jour le 6 : contournement de politique de sécurité, versions antérieures à **10.6p2** affectées. Score CVSS, vecteur, authentification et conditions exactes non confirmés pendant cette collecte : aucune qualification spéculative en RCE ou sans authentification. Vérifier les correctifs rétroportés des distributions plutôt que comparer seulement le numéro upstream.

Sources : https://cert.cyber.gouv.fr/avis/CERTFR-2026-AVI-1468/ ; https://www.openssh.com/txt/release-10.6p2

## Nginx

Six CVE de gravité moyenne dans les avis upstream : CVE-2026-15176, CVE-2026-93479, CVE-2026-95101, CVE-2026-97629, CVE-2026-97205 et CVE-2026-90439. Les cinq premières sont corrigées dans 1.30.6+/1.31.7+ ; la dernière dans 1.30.5+/1.31.6+. Les scores CVSS individuels n'ont pas été confirmés dans cette collecte. Vérifier modules activés, correctifs de distribution et configuration.

Source : https://nginx.org/en/security_advisories.html

## MariaDB

MariaDB **10.11.19 LTS** reste la dernière maintenance 10.11 confirmée par la fondation (21 août 2026). La branche Community 10.6 est EOL depuis le 6 juillet 2026. Aucune nouvelle CVE importante/critique individuellement confirmée pendant cette collecte ; vérifier errata RPM et correctifs rétroportés.

Source : https://mariadb.org/mariadb-server-12-3-11-8-11-4-and-10-11-q3-2026-maintenance-releases-and-goodbye-10-6/

## Méthode, gravité et limites

Périmètre de comptage : **CVE distinctes nouvellement confirmées dans la collecte du rapport**, sans doublon entre produits. Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0. Résultat : **0 critique et 1 importante (CVE-2026-105437, 7.5)**. CVE-2026-123158 et les six CVE Nginx sont documentées mais non comptées faute de scores CVSS individuellement vérifiés ; 0 ne signifie pas absence de vulnérabilités historiques. Les versions ou conditions non vérifiables sont explicitement signalées. Sources officielles privilégiées ; aucune procédure offensive.
