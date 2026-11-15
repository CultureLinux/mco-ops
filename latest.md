---
type: mco-daily
date: 2026-11-15
generated_at: 2026-11-15T08:04:44+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.26"
---

# Brief MCO défensif — dimanche 15 novembre 2026

## Résumé exécutif

- **P0 GitLab :** nouvelle information importante par rapport au brief du 14 novembre : GitLab CE/EE **19.5.5 et 19.4.7**, publiés le **12 novembre**, corrigent des bugs et régressions, **sans nouveau correctif de sécurité**. Ils succèdent aux correctifs critiques 19.5.4/19.4.6 du 10 novembre. La branche 19.3 reste suivie à 19.3.8 dans les bulletins consultés.
- **P0 OpenSSH :** CVE-2026-123158, contournement d'authentification dans certaines configurations Linux **musl sans PAM**, corrigé par OpenSSH 10.6p2 ; contrôler les systèmes concernés et les backports. L'avis CERT-FR date des 5–6 novembre.
- **P1 Rocky Linux 9 :** dernier kernel BaseOS x86_64 vérifié **5.14.0-687.66.1.el9_8** (5 novembre).
- **P1 Proxmox VE 9.2 :** dernière version de pve-manager vérifiée dans le suivi des dépôts **9.2.26** ; le dernier kernel Proxmox publié n'est pas établi, et doit être contrôlé séparément.
- **P1 Nginx :** six CVE de gravité moyenne et correctifs officiels connus ; **MariaDB :** pas de nouvelle alerte critique confirmée.

## GitLab Self-Managed CE/EE

**Publication du 12 novembre 2026 : 19.5.5 et 19.4.7.** L'éditeur indique explicitement qu'il s'agit de corrections de régressions et de bugs **sans correctifs de sécurité**. Ces versions incluent GitLab Orbit (désactivé par défaut) et des migrations de base de données. Sur instance unique, l'éditeur prévient d'une interruption de service pendant les migrations. En cas d'activation d'Orbit sur 19.4.7, ne pas migrer vers une 19.5 antérieure à 19.5.5.

Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-5-released/

**Rappel sécurité P0 du 10 novembre : CVE-2026-105437.** Débordement de tampon dans Git embarqué pouvant entraîner l'exécution de commandes dans Gitaly sous certaines conditions ; score CVSS v3.1 **7.5**, vecteur AV:N/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:H selon la référence de sécurité déjà validée : réseau, complexité élevée, sans privilèges, **interaction utilisateur requise**. Versions corrigées : 19.3.8, 19.4.6, 19.5.4, ou versions ultérieures de la même branche. Les 19.5.5 et 19.4.7 intègrent donc les corrections de leur branche. Ne pas décrire cette vulnérabilité comme une RCE sans interaction. Vérifier la version installée et les installations compilées depuis les sources.

Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-4-released/

**Chaîne de confiance CI/CD : CVE-2026-111943** (certificats intermédiaires eMudhra compromis). Les correctifs GitLab du 29 octobre incluent 19.5.3/19.4.5/19.3.7 ; mettre à jour séparément GitLab Runner vers **19.5.1** ou version corrigée ultérieure et reconstruire les images CI/CD dotées de leur propre magasin de CA. AI Gateway possède aussi ses propres versions correctives. Aucun nouveau contournement d'authentification ou vol de tokens GitLab n'est confirmé aujourd'hui.

Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## OpenSSH

**CVE-2026-123158 : contournement d'authentification.** L'avis CERT-FR du 5 novembre, actualisé le 6 novembre, indique les versions OpenSSH antérieures à **10.6p2**. Le scénario particulièrement concerné est sshd sous Linux musl, sans PAM, avec authentification par mot de passe et configuration de comptes spécifique (notamment Alpine Linux) ; il ne faut pas considérer toutes les installations Rocky/RHEL utilisant PAM comme vulnérables sans analyse de configuration et de paquet. Priorité élevée aux hôtes exposés concernés. Score CVSS non retenu dans les compteurs de ce jour faute de validation univoque dans les sources primaires consultées. Vérifier les correctifs rétroportés des distributions.

Sources : https://cert.cyber.gouv.fr/avis/CERTFR-2026-AVI-1468/ ; https://www.openssh.com/txt/release-10.6p2

## Rocky Linux / RHEL 9

Le dépôt officiel Rocky Linux 9 BaseOS x86_64 affiche **kernel et kernel-core 5.14.0-687.66.1.el9_8**, datés du **5 novembre 2026**, sans version ultérieure identifiée dans l'index vérifié. L'erratum Red Hat spécifique et les CVE de cette révision ne sont pas suffisamment établis ici. Contrôler `dnf updateinfo list security`, les correctifs réellement installés, l'espace /boot et le kernel démarré après redémarrage. Ne pas assimiler le numéro upstream Linux aux backports RHEL.

Source : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D

## Proxmox VE

Branche majeure suivie **9.2** ; dernière version de **pve-manager 9.2.26** vérifiée dans les dépôts lors du suivi précédent. La disponibilité stable de 9.2.27 n'est pas confirmée. **Kernel Proxmox : dernière version disponible non vérifiée** ; ne pas confondre avec pve-manager. Contrôler séparément `pveversion -v`, `uname -r`, les paquets kernel installés et les avis PSA, puis qualifier les régressions stockage/réseau/QEMU et sauvegardes avant déploiement. Aucun nouvel avis PSA ou correctif de régression n'a été confirmé ce dimanche.

Sources : https://pve.proxmox.com/wiki/Package_Repositories ; https://forum.proxmox.com/forums/announcements.7/

## Nginx et MariaDB

**Nginx :** l'éditeur liste six CVE de gravité **medium** : CVE-2026-15176, CVE-2026-93479, CVE-2026-95101, CVE-2026-97629, CVE-2026-97205, CVE-2026-90439. Les cinq premières sont corrigées dans les branches 1.30.6+/1.31.7+ et la dernière dans 1.30.5+/1.31.6+. Les scores CVSS individuels n'ont pas été établis ici ; vérifier les backports RPM et les fonctionnalités activées (HTTP/3, AIO, tickets TLS, scripts).

Source : https://nginx.org/en/security_advisories.html

**MariaDB :** la référence de suivi antérieure est **10.11.19 LTS** ; aucune nouvelle version ou CVE importante vérifiée dans cette collecte. Contrôler les notes de sécurité de chaque branche maintenue, les errata de distribution et l'état de support de la version réellement installée.

Source : https://mariadb.org/ ; https://mariadb.com/kb/en/security/

## Méthode, gravité et limites

Compteurs du jour : **CVE distinctes nouvellement confirmées et scorées dans cette collecte**, et non l'inventaire des vulnérabilités historiques. Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0 ; dédoublonnage par identifiant CVE entre produits. **0 critique / 0 importante nouvelle** ce dimanche : les CVE-2026-105437 et CVE-2026-123158 étaient déjà connues des briefs précédents ; le score de la seconde n'a pas été retenu faute de validation primaire univoque. Les six CVE Nginx ne sont pas comptées sans score CVSS individuel vérifié. Un zéro ne signifie pas absence de risque. Sources officielles prioritaires ; aucune instruction offensive.
