---
type: mco-daily
date: 2026-11-13
generated_at: 2026-11-13T08:05:08+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.26"
---

# Brief MCO défensif — 13 novembre 2026

## Résumé exécutif
- **Rocky Linux 9** : dernier kernel vérifié dans BaseOS x86_64 : `5.14.0-687.66.1.el9_8` (paquet daté du 5 novembre). Aucun nouveau kernel confirmé.
- **Proxmox VE** : références précédemment confirmées `pve-manager 9.2.26` et `qemu-server 9.2.15`. Le dernier kernel Proxmox disponible n'a pas été vérifié ce jour.
- **GitLab Self-Managed CE/EE** : maintenir le suivi du bulletin du 29 octobre relatif aux certificats intermédiaires eMudhra compromis (CVE-2026-111943), avec contrôle des runners et images CI/CD. Aucune nouvelle Security Patch Release confirmée pendant cette collecte.
- **Nginx** : six CVE de gravité moyenne documentées officiellement, correctifs publiés.
- **OpenSSH et MariaDB** : aucune nouvelle alerte critique confirmée pendant cette collecte ; vérifier les errata RPM et les branches maintenues.

## Périmètre, méthode et compteurs
Les compteurs portent sur les **nouvelles CVE individuellement vérifiées et scorées pendant la collecte du 13 novembre**, et non sur le stock de CVE non corrigées. Une CVE est comptée une seule fois, même si plusieurs produits sont touchés. Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0. Résultat : **0 critique, 0 importante nouvellement confirmées**. Les CVE dont le score n'est pas vérifié sont exclues des compteurs ; ce zéro ne démontre pas l'absence de vulnérabilités. La collecte n'est pas exhaustive.

## Rocky Linux / RHEL 9 — P1
L'index officiel BaseOS x86_64 montre `kernel-5.14.0-687.66.1.el9_8` et `kernel-core-5.14.0-687.66.1.el9_8`, datés du 5 novembre 2026. Aucun numéro supérieur n'a été identifié dans cet index. Les errata RHEL/Rocky, CVE précises corrigées et régressions propres à cette révision n'ont pas été confirmés individuellement. Vérifier `dnf updateinfo list --security`, `dnf check-update 'kernel*'`, `uname -r`, l'espace /boot et les modules tiers avant de planifier un redémarrage. Les backports RPM priment sur une comparaison naïve de versions.
Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D ; https://access.redhat.com/security/security-updates/

## Proxmox VE — P1
Branche suivie : Proxmox VE 9.2. Dernières références vérifiées lors des collectes précédentes : **pve-manager 9.2.26** et **qemu-server 9.2.15** ; leur statut de dernières versions publiées au 13 novembre n'a pas été reconfirmé. **Kernel Proxmox : dernière version publiée non vérifiée** ; `7.0.14-28-pve` a été observé sur des nœuds mais ne constitue pas une preuve de dernière version. Distinguer impérativement `pve-manager` de `proxmox-kernel` et du noyau actif (`uname -r`). Contrôler `pveversion -v`, les avis PSA, le changelog et les dépôts ; qualifier stockage, migrations, réseau, QEMU, sauvegardes et kdump avant déploiement. Aucune nouvelle régression vérifiée.
Sources : https://pve.proxmox.com/wiki/Roadmap ; https://enterprise.proxmox.com/debian/pve/dists/trixie/

## GitLab Self-Managed CE/EE — P1 sécurité et CI/CD
Le bulletin du **29 octobre 2026** signale **CVE-2026-111943** : retrait de confiance de huit certificats intermédiaires eMudhra compromis. Risque : chaîne de confiance TLS, y compris GitLab, GitLab Runner et images CI/CD possédant leur propre magasin de certificats. Versions de référence mentionnées par le bulletin : GitLab **19.5.3, 19.4.5, 19.3.7** et GitLab Runner **19.5.1**. Confirmer les branches maintenues et versions réellement installées avant intervention. Le score CVSS, vecteur CVSS, exigences d'authentification, privilèges et interaction pour cette CVE ne sont pas individuellement vérifiés dans cette collecte : ne pas les inventer. Les anciennes alertes RCE/auth bypass/non authentifiées et fuites de secrets ou tokens ne sont pas requalifiées sans bulletin officiel ; aucune nouvelle alerte de ce type n'a été confirmée ici. Examiner les Security/Critical Patch Releases et les fonctionnalités CI/CD exposées.
Sources : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/ ; https://about.gitlab.com/releases/

## Nginx — P1
L'éditeur documente six CVE de gravité **medium** : CVE-2026-15176 (alias/rewrite), CVE-2026-93479 (évaluation de scripts), CVE-2026-95101 (corps de requête HTTP/3), CVE-2026-97629 (tickets de session TLS), CVE-2026-97205 (AIO) et CVE-2026-90439 (module HTTP/3). Versions non vulnérables annoncées : **1.30.6+ et 1.31.7+** pour les cinq premières ; **1.30.5+ et 1.31.6+** pour la dernière. Scores CVSS individuels non vérifiés : non comptés comme importants ou critiques. Vérifier les modules réellement activés et les backports des paquets de distribution.
Source : https://nginx.org/en/security_advisories.html

## OpenSSH — P2
Aucun nouvel avis critique du 13 novembre confirmé. Vérifier `rpm -q openssh openssh-server`, `dnf updateinfo list --security` et la configuration effective `sshd -T`. Les versions upstream ne reflètent pas nécessairement les correctifs rétroportés par Rocky/RHEL.
Source : https://www.openssh.com/security.html

## MariaDB — P2
Branche 10.11 LTS précédemment suivie : **10.11.19**, non revalidée comme dernière publication ce matin. Aucune nouvelle CVE importante ou critique individuellement confirmée dans cette collecte. Vérifier `SELECT VERSION();`, `dnf updateinfo list --security`, les notes de maintenance et le support de la branche.
Source : https://mariadb.org/download/

## Actions recommandées
1. Qualifier le kernel Rocky et confirmer la version réellement démarrée sur chaque serveur.
2. Comparer les paquets Proxmox et le kernel actif aux dépôts et avis PSA.
3. Vérifier GitLab, Runner et les magasins de certificats CI/CD à la lumière du bulletin eMudhra.
4. Vérifier les six corrections Nginx et les errata OpenSSH/MariaDB.
5. Ne pas considérer l'absence de nouvelles CVE scorées comme une preuve d'absence de risque.

**Limites :** le dernier kernel Proxmox, les errata détaillés RHEL et les dernières versions OpenSSH/MariaDB n'ont pas été intégralement vérifiés. Aucun CVSS, statut d'exploitation ou numéro de version supplémentaire n'est déduit sans source.
