---
type: mco-daily
date: 2026-11-23
generated_at: 2026-11-23T08:01:55+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-741.24.1.el9_9"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — 23 novembre 2026

## Résumé exécutif
**Priorité aux correctifs Nginx et GitLab Self-Managed**, ainsi qu'à la validation du nouveau kernel Rocky Linux 9.9. La collecte ne confirme aucune **nouvelle** CVE de score CVSS ≥ 7, ce qui ne prouve pas l'absence de nouvelles vulnérabilités. Les dernières versions Proxmox kernel et pve-manager restent non vérifiées.

## Rocky Linux / RHEL 9
- **Dernier kernel BaseOS x86_64 vérifié : `5.14.0-741.24.1.el9_9`**, paquet daté du 20 novembre 2026 à 18:27 UTC. Le dépôt `security` contient séparément `5.14.0-741.22.1.el9_9.0.1` daté du 18 novembre ; ne pas supposer l'équivalence des correctifs entre dépôts.
- **Errata RHSA/RLSA, CVE corrigées et régressions spécifiques : non vérifiés** ; aucune attribution de CVE à cette révision sans bulletin officiel.
- Action : `dnf repolist --enabled`, `dnf check-update 'kernel*'`, `rpm -q kernel-core`, `uname -r` ; vérifier /boot, initramfs, kdump et sauvegardes avant redémarrage.
- Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://download.rockylinux.org/pub/rocky/9/security/x86_64/os/Packages/k/

## Proxmox VE
- Branche majeure suivie **9.2** ; version **pve-manager** la plus récente non confirmée, donc champ YAML vide.
- **Kernel Proxmox : dernière révision non confirmée**, à distinguer explicitement de `pve-manager` et du kernel RHEL/Rocky. Aucune nouvelle régression confirmée.
- Action : `pveversion -v`, `uname -r`, `apt list --upgradable` ; contrôler RAID, kdump et sauvegardes avant redémarrage.
- Source officielle : https://pve.proxmox.com/wiki/Package_Repositories

## GitLab Self-Managed CE/EE
- Annonce GitLab **19.6 le 19 novembre** : nouveautés produit, **pas** un bulletin de sécurité à elle seule. Vérifier la disponibilité effective des correctifs selon les branches CE/EE.
- Dernière Security Patch Release précédemment suivie (16 novembre) : **19.5.6 / 19.4.8 / 19.3.9** ; aucun correctif ultérieur confirmé pendant cette collecte.
- CVE antérieurement recensées, non recomptées : **CVE-2026-18241 (CVSS 8.2)**, accès aux variables CI/CD protégées ; **CVE-2026-97187 (7.6)**, lecture de secrets Kubernetes ; **CVE-2026-105739 (8.7)**, XSS Wiki ; **CVE-2026-108153 (8.7)**, XSS Error Tracking. Scores, conditions et versions affectées **non revalidés aujourd'hui** : ne pas extrapoler les bornes d'exposition.
- Suivre spécifiquement RCE/Gitaly, auth bypass, fuites de tokens et secrets, accès sans authentification, Runner, certificats des images CI/CD et fonctionnalités activées. Mettre à jour les installations non corrigées après vérification des notes officielles.
- Sources : https://about.gitlab.com/releases/ ; https://about.gitlab.com/press/releases/2026-11-19-new-gitlab-agentic-automations/

## Nginx
- **Dix CVE signalées le 18 novembre**, dont trois « major » selon l'éditeur : **CVE-2026-123007** (corruption mémoire HTTP/2), **CVE-2026-118899** (boucle TLS 1.3), **CVE-2026-113425** (use-after-free proxy mail). Autres vulnérabilités précédemment recensées : resolver, HTTP/2, HTTP/3, SNI, PROXY protocol v2, alias/try_files et ssl_preread.
- Versions correctives upstream précédemment identifiées : **1.30.7 / 1.31.9** ; contrôler les correctifs rétroportés des distributions et les modules effectivement activés. Scores CVSS individuels, authentification, privilèges et interaction **non revalidés**.
- Aucune de ces CVE antérieurement suivies n'est recomptée.
- Source : https://nginx.org/en/security_advisories.html

## OpenSSH
- CVE-2026-123158 suivie précédemment : conditions spécifiques d'exposition et correctifs rétroportés **non revalidés**. Aucune nouvelle CVE importante confirmée. Vérifier les avis du fournisseur plutôt que la seule version upstream.
- Source : https://www.openssh.com/security.html

## MariaDB
- Branche 10.11 LTS surveillée ; référence antérieure 10.11.19 **non reconfirmée comme dernière version**. Aucune nouvelle CVE importante individuellement confirmée ; contrôler les bulletins de sécurité et les paquets de distribution.
- Sources : https://mariadb.org/ ; https://mariadb.com/docs/server/security

## Alerte transverse Java
- CVE-2026-114938 (« Java4Shell », CVSS 8.1 selon suivi antérieur) : risque RCE selon les applications et JVM exposées ; **pas de nouvelle qualification indépendante aujourd'hui**. Vérifier les builds OpenJDK et les correctifs du fournisseur. CVE déjà recensée, non recomptée.

## Comptage, gravité et limites
- Compter **une seule fois chaque CVE distincte nouvellement confirmée pendant la collecte**, sans doublon interproduits et sans recompter les CVE des rapports précédents. **Critique : CVSS ≥ 9.0 ; importante : 7.0 ≤ CVSS < 9.0**. Un score absent ou non vérifiable n'est pas inventé ni compté.
- **critical_cve: 0 ; important_cve: 0** signifie aucune nouvelle CVE scorée confirmée, **pas** zéro vulnérabilité dans le parc.
- `status: critical` reflète l'urgence persistante des correctifs Nginx et GitLab, et non une nouvelle CVE critique.
- Limites : dernières révisions Proxmox, détail des errata Rocky, nouvelles versions OpenSSH/MariaDB, scores Nginx et nouvelles Security Patch Releases GitLab non établis exhaustivement.
