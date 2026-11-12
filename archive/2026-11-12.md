---
type: mco-daily
date: 2026-11-12
generated_at: 2026-11-12T08:00:29+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.26"
---

# Brief MCO défensif — 12 novembre 2026

## Résumé exécutif
- **Rocky Linux 9** : dernier kernel visible dans BaseOS x86_64 : `5.14.0-687.66.1.el9_8`, paquet du 5 novembre. Aucune révision supérieure identifiée dans l'index officiel consulté.
- **Proxmox VE** : dernière référence `pve-manager 9.2.26` (vérification du 10 novembre), `qemu-server 9.2.15`. Dernier kernel publié et nouvelles régressions : non vérifiés pour cette collecte. Le kernel et `pve-manager` sont deux composants distincts.
- **GitLab CE/EE — sécurité CI/CD** : le bulletin du 29 octobre corrige **CVE-2026-111943**, retrait de confiance de huit certificats intermédiaires eMudhra compromis. Il faut mettre à jour GitLab, GitLab Runner et les magasins de certificats des images CI/CD. Les affirmations antérieures selon lesquelles cette publication ne contenait aucun correctif de sécurité étaient erronées.
- **Nginx** : six CVE de gravité moyenne documentées officiellement ; correctifs disponibles.
- **OpenSSH et MariaDB** : aucune nouvelle alerte critique vérifiée pour ce cycle ; continuer les contrôles des errata de distribution.

## Méthode et compteurs
Les compteurs représentent les **nouvelles CVE avec score CVSS confirmé pendant cette collecte du 12 novembre**, dédoublonnées par identifiant CVE entre produits. Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0. **0 critique et 0 importante nouvellement vérifiées** : cela ne signifie ni zéro vulnérabilité connue ni zéro risque. CVE-2026-111943 est documentée mais son score CVSS n'a pas été confirmé ici ; elle n'est donc pas incluse dans ces deux compteurs. L'exhaustivité des bulletins du jour n'est pas garantie.

## Rocky Linux / RHEL 9
L'index BaseOS officiel contient `kernel-5.14.0-687.66.1.el9_8` et `kernel-core`, datés du 5 novembre 2026. Les errata détaillés, les CVE propres à cette révision et les régressions ne sont pas vérifiés dans cette collecte. Contrôler `dnf updateinfo list --security`, `dnf check-update 'kernel*'`, `uname -r`, l'espace /boot et les modules tiers avant redémarrage. Ne pas déduire les correctifs des seuls numéros de version : prendre en compte les backports RPM.
Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://access.redhat.com/security/security-updates/

## Proxmox VE
Branche suivie : Proxmox VE 9.2. `pve-manager 9.2.26` et `qemu-server 9.2.15` sont les dernières références précédemment vérifiées ; leur statut de dernières versions disponibles le 12 novembre n'a pas été reconfirmé. Dernier kernel Proxmox publié : **non vérifié** ; `7.0.14-28-pve` a été observé sur certains nœuds du parc mais ne prouve pas qu'il s'agisse de la dernière version disponible. Contrôler `pveversion -v`, `uname -r`, les dépôts configurés, changelogs et avis PSA. Qualifier stockage, réseau, sauvegardes, migrations et éventuelles régressions.
Sources : https://enterprise.proxmox.com/debian/pve/ ; https://forum.proxmox.com/

## GitLab Self-Managed CE/EE
**CVE-2026-111943 — certificats eMudhra compromis** : GitLab a retiré la confiance dans huit certificats intermédiaires dont les clés privées ont été compromises. Cela concerne la validation TLS et les chaînes de confiance, y compris l'écosystème CI/CD. Les versions affectées sont toutes les versions CE/EE antérieures à 18.11.14, les branches 19.0 avant 19.0.11, 19.1 avant 19.1.10, 19.2 avant 19.2.10, 19.3 avant 19.3.7, 19.4 avant 19.4.5 et 19.5 avant 19.5.3. Versions corrigées : 18.11.14, 19.0.11, 19.1.10, 19.2.10, 19.3.7, 19.4.5, 19.5.3. GitLab Runner **19.5.1** est à installer séparément ; reconstruire les images CI/CD possédant leur propre paquet `ca-certificates`. AI Gateway : versions correctives 18.11.8, 19.0.13, 19.1.9, 19.2.5, 19.3.3, 19.4.3, 19.5.3. Score CVSS, vecteur AV/AC/PR/UI et exploitabilité RCE : non confirmés pour cette CVE ; ne pas les inventer. Aucune nouvelle RCE, auth bypass, fuite de secrets/tokens ou attaque sans authentification n'a été individuellement vérifiée aujourd'hui ; ce n'est pas une garantie d'absence. Les versions correctives mentionnées sont celles du bulletin du 29 octobre, pas une preuve qu'elles soient les dernières versions publiées.
Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## OpenSSH
Aucun nouvel avis critique du 12 novembre vérifié. Dernière version upstream et inventaire complet des CVE : non vérifiés. Sur Rocky/RHEL, vérifier `rpm -q openssh-server`, `dnf updateinfo`, les backports, la configuration effective `sshd -T` et les journaux d'authentification.
Source : https://www.openssh.com/security.html

## Nginx
Six avis officiels de gravité **medium** : CVE-2026-15176 (alias/rewrite), CVE-2026-93479 (évaluation de scripts), CVE-2026-95101 (HTTP/3), CVE-2026-97629 (tickets de session TLS), CVE-2026-97205 (AIO), CVE-2026-90439 (module HTTP/3). Les cinq premiers sont corrigés à partir de 1.30.6 / 1.31.7 ; le dernier à partir de 1.30.5 / 1.31.6. Les conditions d'exposition dépendent des modules et fonctionnalités activés ; contrôler la version du paquet, `nginx -V`, les backports et la configuration.
Source : https://nginx.org/en/security_advisories.html

## MariaDB
La branche 10.11 LTS est suivie ; `10.11.19` est la dernière référence historique, **non revalidée** aujourd'hui. Aucune nouvelle CVE critique vérifiée dans ce cycle. Vérifier les notes de publication, errata des paquets, réplication, InnoDB, sauvegardes et restaurations.
Source : https://mariadb.com/docs/release-notes/community-server

## Priorités opérationnelles
1. Vérifier les versions et avis de sécurité GitLab, Runner, AI Gateway et magasins de certificats CI/CD ; prioriser les chaînes TLS affectées.
2. Qualifier le kernel Rocky et contrôler le kernel réellement démarré après mise à jour.
3. Vérifier séparément `pve-manager`, kernel Proxmox, QEMU et avis PSA.
4. Contrôler les correctifs Nginx et les errata OpenSSH/MariaDB de la distribution.

**Limites :** les versions Proxmox, OpenSSH et MariaDB n'ont pas toutes été revalidées en direct. Les compteurs 0/0 portent exclusivement sur les nouvelles CVE scorées confirmées aujourd'hui et non sur le stock de vulnérabilités.
