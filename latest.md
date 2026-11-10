---
type: mco-daily
date: 2026-11-10
generated_at: 2026-11-10T07:59:53+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.26"
---

# Brief MCO défensif — mardi 10 novembre 2026

## Résumé exécutif
- **Nouveau constat vérifié : Proxmox VE `pve-manager 9.2.26`** est référencé dans les changelogs officiels des dépôts enterprise et no-subscription depuis le 5 novembre ; la référence historique 9.2.24 était dépassée. Le kernel Proxmox est un paquet distinct, et sa dernière version publiée n'est pas vérifiée.
- **Rocky Linux 9** : dernier kernel trouvé dans BaseOS x86_64 : `5.14.0-687.66.1.el9_8` (5 novembre) ; pas de révision ultérieure visible dans l'index vérifié.
- **GitLab CE/EE et CI/CD** : mise à jour de sécurité du 29 octobre contre la confiance accordée à huit certificats eMudhra compromis. Mettre à jour séparément Runner et images CI/CD avec magasins CA embarqués.
- **Nginx** : six CVE de gravité *medium* dans les avis officiels, correctifs publiés ; pas de nouvel avis du 10 novembre identifié.
- **OpenSSH et MariaDB** : pas de nouvel avis daté du jour confirmé ; MariaDB 10.11.19 reste la maintenance LTS 10.11 identifiée.

## Méthode de comptage et limites
Les compteurs YAML comptent **uniquement les CVE nouvellement confirmées et datées du 10 novembre 2026** dans les sources consultées, avec score CVSS vérifié : critique **CVSS >= 9.0**, importante **7.0 <= CVSS < 9.0**. Une CVE touchant plusieurs produits n'est comptée qu'une fois. **0/0 ne signifie ni absence de CVE historiques ni absence de vulnérabilités sur le parc.** Les CVE sans score vérifié sont exclues du décompte. Le statut *warning* signale des mises à jour de sécurité et des écarts de version nécessitant qualification ; il ne garantit pas l'absence de risque critique.

## Rocky Linux / RHEL 9
Le dépôt officiel Rocky Linux 9 BaseOS x86_64 présente les RPM `kernel` et `kernel-core` `5.14.0-687.66.1.el9_8`, horodatés du 5 novembre 2026. Aucun RPM plus récent identifié dans l'index vérifié. Erratum RHEL/Rocky associé à cette révision, CVE propres à la révision et régressions : **non confirmés**. Ne pas extrapoler le statut d'une CVE à partir du seul numéro de version. La CVE-2026-98432 mentionnée dans le suivi précédent nécessite une nouvelle validation officielle de son score, du vecteur, des versions affectées et de sa correction.
Actions défensives : `dnf updateinfo list security`, `dnf check-update 'kernel*'`, `df -h /boot`, puis `uname -r` après redémarrage ; qualifier modules tiers et sauvegardes.
Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D ; https://access.redhat.com/errata/

## Proxmox VE
**Proxmox VE 9.2** : les index officiels des changelogs `pve-manager` enterprise et no-subscription contiennent **9.2.26**, datée du 5 novembre 2026 (index no-subscription). C'est la version de `pve-manager` la plus récente identifiée dans ces index, et non une preuve que chaque miroir est synchronisé.
**Kernel Proxmox :** `7.0.14-28-pve` a été observé sur certains nœuds du parc ; **la version la plus récente disponible en dépôt n'est pas confirmée**. Ne jamais confondre kernel actif, paquet kernel installé et version `pve-manager`.
**qemu-server :** changelog `9.2.15` présent dans le dépôt no-subscription (6 novembre), à qualifier avec les autres paquets.
Régressions précises et nouveaux avis PSA datés du 10 novembre : non confirmés. Contrôles : `pveversion -v`, `uname -r`, `apt policy pve-manager qemu-server`, tests de migration, stockage, ZFS, réseau et sauvegardes avant déploiement.
Sources : https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/ ; https://metadata.cdn.proxmox.com/enterprise/changelogs/pve/dists/trixie/pve-enterprise/p/pve-manager/ ; https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/q/qemu-server/

## GitLab Self-Managed CE/EE, Runner et CI/CD
Le bulletin officiel du **29 octobre 2026**, intitulé *Patch Release and Security Update*, corrige **CVE-2026-111943** en retirant la confiance accordée à **huit certificats intermédiaires eMudhra dont les clés privées sont compromises**. Versions correctives CE/EE : **18.11.14, 19.0.11, 19.1.10, 19.2.10, 19.3.7, 19.4.5, 19.5.3** ; Runner auto-hébergé **19.5.1** ; AI Gateway **18.11.8, 19.0.13, 19.1.9, 19.2.5, 19.3.3, 19.4.3, 19.5.3**. Reconstituer les images CI/CD contenant un magasin CA autonome.
Impact : chaîne de confiance TLS, pas de preuve de RCE directe GitLab dans ce bulletin. CVSS, vecteur AV/AC, authentification, privilèges, interaction et branches vulnérables détaillées : **non vérifiés pour cette CVE**. Les alertes historiques CVE-2026-110264 et CVE-2026-97229 présentées comme RCE sans authentification dans d'anciens rapports ne sont pas reconfirmées ici : ne pas traiter leurs scores ni versions comme vérifiés. Aucun nouveau Security/Critical Patch Release du 10 novembre identifié dans la collecte.
Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## Nginx
Six avis de sévérité **medium** selon l'éditeur : CVE-2026-15176 (alias/rewrite), CVE-2026-93479 (script), CVE-2026-95101 (HTTP/3), CVE-2026-97629 (TLS session tickets), CVE-2026-97205 (AIO), CVE-2026-90439 (HTTP/3). Les cinq premières sont corrigées en **1.30.6+ / 1.31.7+** et la dernière en **1.30.5+ / 1.31.6+**. CVSS numériques, vecteurs, privilèges et interaction : non vérifiés, donc non comptés. Vérifier modules activés et backports Rocky/RHEL.
Source : https://nginx.org/en/security_advisories.html

## OpenSSH
La page officielle de sécurité consultée n'indique pas de nouvel avis du 10 novembre. Pour Rocky/RHEL, comparer les errata et backports, pas seulement le numéro de version upstream. Vérifier `sshd -T` (authentification, accès root, forwarding) et exposition des services.
Sources : https://www.openssh.com/security.html ; https://access.redhat.com/errata/

## MariaDB
Le catalogue officiel liste **10.11.19** comme maintenance de la série 10.11 LTS ; autres maintenances vérifiées : 11.4.13, 11.8.9, 12.3.3. Aucun nouvel avis CVE du 10 novembre confirmé. La branche communautaire 10.6 est en fin de vie depuis juillet 2026. Vérifier sauvegardes/restaurations, réplication, compatibilité et version du paquet avant mise à jour.
Source : https://mariadb.org/mariadb/all-releases/

## Priorités opérationnelles
1. **Proxmox :** comparer `pve-manager` installé à **9.2.26** et `qemu-server` à **9.2.15**, qualifier les mises à jour et vérifier séparément le kernel démarré.
2. **GitLab :** vérifier serveur, Runner, AI Gateway et magasins CA des images CI/CD ; prioriser tout avis officiel RCE, auth bypass, fuite de secrets/tokens et accès sans authentification.
3. **Rocky :** qualifier `5.14.0-687.66.1.el9_8` et vérifier les errata du parc, l'espace /boot et les redémarrages.
4. **Nginx/OpenSSH/MariaDB :** contrôler correctifs de distribution, configuration exposée et versions maintenues.
