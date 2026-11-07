---
type: mco-daily
date: 2026-11-07
generated_at: 2026-11-07T07:58:26+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — samedi 7 novembre 2026

## Résumé exécutif
- **Nouveau Rocky Linux 9 :** le dépôt officiel BaseOS x86_64 propose le kernel **5.14.0-687.66.1.el9_8**, daté du **5 novembre 2026 à 21:26 UTC**, après 687.65.1. Qualifier et planifier le redémarrage.
- **GitLab Self-Managed :** vérifier sans délai les correctifs de sécurité serveur et Runner, ainsi que les certificats dans les images CI/CD. Aucun nouvel avis du 7 novembre confirmé pendant cette collecte.
- **Proxmox VE :** versions de référence historiques pve-manager 9.2.24 et kernel 7.0.14-26-pve ; **pas de revalidation du dernier paquet ce matin**. Vérifier indépendamment les versions installées et démarrées.
- **OpenSSH, Nginx, MariaDB :** contrôler les avis officiels, les versions déployées et les backports de distribution.

## Méthode de classement et limites
Les compteurs du front matter recensent **uniquement les nouvelles CVE distinctes dont le score a été confirmé lors de cette collecte du 7 novembre** : critique CVSS >= 9.0, importante 7.0 <= CVSS < 9.0 ; une CVE commune à plusieurs produits ne compte qu'une fois. **0/0 ne signifie pas absence de vulnérabilités dans le parc** : le registre historique précédent indiquait 5 critiques et 18 importantes, sans revalidation individuelle aujourd'hui. Les anciennes alertes restent ouvertes jusqu'à vérification des versions déployées. Ne pas interpréter les références historiques comme des dernières versions vérifiées.

## Rocky Linux / RHEL 9
**Version vérifiée :** kernel et kernel-core `5.14.0-687.66.1.el9_8.x86_64`, datés du 5 novembre 2026, 21:26:24 UTC, dans BaseOS x86_64. L'erratum spécifique à cette révision et les CVE corrigées n'ont pas été vérifiés ; ne pas leur attribuer de score ou d'impact sans bulletin officiel.
Source officielle : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
RHEL : vérifier les errata de Red Hat sur https://access.redhat.com/errata/ ; la correspondance exacte avec la révision Rocky 687.66.1 n'a pas été établie.
**Action :** `dnf updateinfo list security`, `dnf check-update kernel\*`, vérifier /boot, sauvegardes et compatibilité modules, puis contrôler `uname -r` après redémarrage.
**Alerte historique à confirmer :** CVE-2026-98432 (CVSS 9.8 dans le suivi antérieur), scénario réseau TCP IPv4 sans authentification, risque de DoS/corruption mémoire et RCE sous conditions. Vérifier son statut précis dans les bulletins officiels et la version installée avant de conclure à une exposition.

## Proxmox VE
Version majeure suivie : **9.2**. Dernier `pve-manager` historiquement vérifié : **9.2.24**. Kernel historiquement suivi : **7.0.14-26-pve**. Ces références ne sont **pas reconfirmées comme dernières disponibles** le 7 novembre. Le kernel est un paquet distinct de `pve-manager`; vérifier `pveversion -v`, `uname -r`, `apt policy pve-manager proxmox-kernel-7.0` et les avis PSA. Vérifier les risques de régression ZFS, KVM, réseau, LXC et sauvegarde avant reboot.
Source : https://www.proxmox.com/en/about/company-details/press-releases et https://forum.proxmox.com/

## GitLab Self-Managed CE/EE et CI/CD
Versions correctives **historiques** suivies : GitLab 19.5.3, 19.4.5, 19.3.7 ; GitLab Runner 19.5.1. **Non reconfirmées comme dernières versions aujourd'hui.** Vérifier les patch releases et les notes de sécurité officielles.
Alertes historiques nécessitant validation des bulletins : CVE-2026-110264 et CVE-2026-97229 (RCE sans authentification, scores 10.0 historiquement suivis), et CVE-2026-111943 (certificats eMudhra compromis, score non confirmé). Pour chaque CVE, les branches affectées/corrigées, vecteur, privilèges, interaction et fonctionnalité ne sont pas tous revalidés dans cette collecte : **ne pas extrapoler**. Priorité opérationnelle élevée aux risques RCE, auth bypass, secrets/tokens et pipelines CI/CD. Mettre à jour serveur, Runner et images de build séparément.
Sources : https://about.gitlab.com/releases/ et https://docs.gitlab.com/releases/patches/

## OpenSSH
Aucun nouveau bulletin daté du 7 novembre confirmé. Vérifier la version packagée et les backports plutôt que comparer seulement le numéro upstream ; surveiller auth bypass, RCE et élévation de privilèges. Source : https://www.openssh.com/security.html

## Nginx
L'avis officiel confirme quatre CVE de sévérité **medium** : CVE-2026-15176, CVE-2026-93479, CVE-2026-95101 et CVE-2026-97629, corrigées dans les branches **1.30.6+** et **1.31.7+** selon les configurations concernées. Ne pas assimiler « medium » à un CVSS >= 7 sans score individuel vérifié. Vérifier les versions et modules HTTP/3, AIO, TLS tickets, alias/rewrite.
Source : https://nginx.org/en/security_advisories.html

## MariaDB
Aucune nouvelle release ou CVE du 7 novembre confirmée. La référence historique 10.11.19 n'est pas revalidée comme dernière version. Vérifier les notes de sécurité et les paquets du dépôt actif, en particulier la branche 10.11 LTS.
Source : https://mariadb.com/kb/en/security/

## Actions MCO prioritaires
1. Qualifier le nouveau kernel Rocky 687.66.1, contrôler /boot et les modules tiers, puis redémarrer avec fenêtre de maintenance.
2. Auditer GitLab et chaque Runner séparément ; inventorier les images CI/CD et les CA embarquées.
3. Vérifier les versions effectives Proxmox, notamment kernel démarré vs pve-manager installé.
4. Comparer OpenSSH, Nginx et MariaDB aux avis officiels et aux errata RPM.

## Transparence
Collecte partielle : les dernières versions Proxmox/GitLab/MariaDB/OpenSSH et les scores individuels des CVE historiques ne sont pas revalidés ce matin. Les compteurs 0/0 sont ceux des nouvelles CVE **confirmées pendant cette collecte**, pas un bilan global de sécurité.
