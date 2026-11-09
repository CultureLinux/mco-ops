---
type: mco-daily
date: 2026-11-09
generated_at: 2026-11-09T07:56:15+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — lundi 9 novembre 2026

## Résumé exécutif
- **Rocky Linux 9** : le dernier kernel identifié dans le dépôt officiel BaseOS x86_64 est `5.14.0-687.66.1.el9_8` (paquet du 5 novembre). Aucune révision ultérieure confirmée.
- **GitLab Self-Managed CE/EE** : la publication du **29 octobre** est bien une **mise à jour de sécurité** pour CVE-2026-111943 (certificats intermédiaires eMudhra compromis), contrairement à la conclusion erronée du rapport du 8 novembre. Mise à jour séparée de Runner et des images CI/CD nécessaire.
- **Proxmox VE** : famille 9.2 confirmée dans le suivi ; la dernière version publiée de `pve-manager` n'a pas été vérifiée aujourd'hui. Ne pas confondre `pve-manager` avec le kernel `7.0.14-28-pve` observé sur certains nœuds ; ce dernier n'est pas nécessairement le plus récent en dépôt.
- **Nginx** : six CVE listées dans l'avis officiel, toutes classées *medium* par l'éditeur, avec versions correctives publiées.
- **MariaDB 10.11** : version LTS `10.11.19` confirmée ; la branche communautaire 10.6 est en fin de vie depuis juillet 2026. OpenSSH : aucune nouvelle version ou CVE datée du jour confirmée.

## Périmètre, gravité et fiabilité
Compteurs du front matter : **nouvelles CVE distinctes du 9 novembre avec score CVSS confirmé pendant cette collecte** ; critique si CVSS >= 9.0, importante si 7.0 <= CVSS < 9.0 ; une CVE affectant plusieurs composants ne compte qu'une fois. **0/0 ne signifie pas absence de vulnérabilités**. Les CVE antérieures ne sont pas déclarées corrigées. Une CVE sans score CVSS vérifié est signalée mais non comptabilisée. Les données non vérifiées (versions, vecteur, privilèges, interaction, périmètre affecté) sont explicitement laissées indéterminées.

## Rocky Linux / RHEL 9
Le dépôt Rocky BaseOS x86_64 contient `kernel` et `kernel-core` `5.14.0-687.66.1.el9_8` datés du 5 novembre 2026. Erratum spécifique, CVE propres à cette révision et éventuelles régressions : **non vérifiés**. Le risque réseau sans authentification précédemment suivi sous CVE-2026-98432 doit être recoupé avec l'avis Red Hat et la correspondance RPM avant d'affirmer son statut. Pour le parc : `dnf updateinfo list security`, `dnf check-update 'kernel*'`, `df -h /boot`, `uname -r` ; qualifier et redémarrer avec contrôle du kernel actif.
Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://access.redhat.com/errata/

## Proxmox VE et kernel
Référence de famille : Proxmox VE **9.2**. Les précédents suivis mentionnent `pve-manager 9.2.24` puis une observation locale `9.2.25`, mais **la version la plus récente du paquet n'a pas été vérifiée dans un dépôt officiel aujourd'hui** : champ YAML vide. Kernel Proxmox **distinct** : `7.0.14-28-pve` a été observé sur des nœuds du parc ; version maximale disponible non vérifiée. Les avis PSA précédemment suivis (00075–00080) nécessitent une vérification officielle individuelle ; aucun nouveau correctif ni régression datée du jour confirmé. Contrôler `pveversion -v`, `uname -r`, `apt policy pve-manager`, `proxmox-boot-tool kernel list` selon configuration ; valider stockage, ZFS, réseau, KVM/LXC et sauvegardes.
Sources : https://www.proxmox.com/en/downloads ; https://forum.proxmox.com/

## GitLab CE/EE, Runner, CI/CD
**Rectification du rapport précédent** : le bulletin officiel GitLab du **29 octobre 2026** est intitulé « Patch Release and Security Update » et indique explicitement qu'il traite **CVE-2026-111943**, relative au retrait de confiance de **huit certificats intermédiaires eMudhra compromis**. Versions corrigées GitLab CE/EE : **18.11.14 ; 19.0.11 ; 19.1.10 ; 19.2.10 ; 19.3.7 ; 19.4.5 ; 19.5.3**. Versions affectées : toutes les versions antérieures à 18.11.14 et chaque branche 19.x avant le correctif de sa branche. Runner auto-hébergé : **19.5.1** (mise à jour indépendante) ; reconstruire les images CI/CD contenant leur propre magasin `ca-certificates`. AI Gateway : versions corrigées **18.11.8, 19.0.13, 19.1.9, 19.2.5, 19.3.3, 19.4.3, 19.5.3**. Vecteur CVSS, authentification, privilèges et interaction pour cette CVE : **non vérifiés** ; ne pas assimiler ce retrait de confiance à une RCE GitLab démontrée.
Les alertes RCE historiques (CVE-2026-110264 et CVE-2026-97229) ne sont **pas reconfirmées individuellement** aujourd'hui ; ne pas reprendre leurs scores, leur caractère sans authentification ou leurs plages de versions comme vérifiés. Examiner en priorité les bulletins officiels concernant RCE, auth bypass, secrets/tokens, pipelines, runners et accès sans authentification.
Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## Nginx
L'éditeur classe *medium* les six avis : CVE-2026-15176 (alias/rewrite), CVE-2026-93479 (évaluation de script), CVE-2026-95101 (HTTP/3), CVE-2026-97629 (tickets TLS), CVE-2026-97205 (AIO), CVE-2026-90439 (module HTTP/3). Les cinq premiers sont corrigés en **1.30.6+ / 1.31.7+** ; CVE-2026-90439 en **1.30.5+ / 1.31.6+**. Les scores CVSS, vecteurs AV/AC/PR/UI et exposition réelle ne sont pas confirmés ici : vérifier les modules activés et les backports de distribution avant conclusion.
Source : https://nginx.org/en/security_advisories.html

## OpenSSH
Aucun nouvel avis ou version daté du 9 novembre vérifié. Pour les paquets Rocky/RHEL, privilégier les errata et backports de sécurité du fournisseur plutôt qu'une simple comparaison de version upstream.
Sources : https://www.openssh.com/security.html ; https://access.redhat.com/errata/

## MariaDB
La fondation liste **10.11.19** comme dernière maintenance de la série 10.11 LTS, ainsi que 11.4.13, 11.8.9 et 12.3.3 pour les autres LTS. La branche **10.6 Community est EOL depuis le 6 juillet 2026** ; préparer la migration si elle est encore utilisée. Aucun nouveau correctif CVE de MariaDB Server daté du 9 novembre confirmé. Vérifier sauvegarde/restauration, compatibilité SQL et réplication avant mise à jour.
Sources : https://mariadb.org/ ; https://mariadb.org/mariadb/all-releases/ ; https://mariadb.org/mariadb-server-12-3-11-8-11-4-and-10-11-q3-2026-maintenance-releases-and-goodbye-10-6/

## Priorités opérationnelles
1. Vérifier immédiatement l'état des versions GitLab, Runner, images CI/CD et certificats de confiance ; ne pas confondre patch serveur et patch Runner.
2. Qualifier les mises à jour Rocky 9 et Proxmox sur les nœuds concernés, contrôler le kernel réellement démarré après redémarrage.
3. Vérifier les modules Nginx exposés, les backports OpenSSH et le maintien des branches MariaDB LTS.
4. Compléter les champs CVSS/AV/AC/PR/UI et versions affectées uniquement depuis les bulletins primaires avant d'augmenter les compteurs.
