---
type: mco-daily
date: 2026-11-21
generated_at: 2026-11-21T07:56:49+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 1
rocky_kernel_latest: "5.14.0-741.22.1.el9_9.0.1"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — 21 novembre 2026

## Résumé exécutif
**P0 : Nginx**, dix CVE récentes (18 novembre), correctifs upstream 1.30.7 / 1.31.9. **GitLab Self-Managed** : appliquer les correctifs de sécurité du 16 novembre et qualifier 19.6. **Alerte transverse nouvellement qualifiée : Java4Shell CVE-2026-114938, CVSS 8.1**, exécution de code à distance sans authentification dans certaines configurations Java x86-64 ; les serveurs Java 17 du parc méritent une vérification urgente. Aucune nouvelle CVE critique CVSS >= 9 vérifiée.

## Rocky Linux / RHEL 9
- Kernel Rocky 9 security x86_64 vérifié : `5.14.0-741.22.1.el9_9.0.1` (18 novembre, 20:48 UTC). BaseOS : `5.14.0-741.22.1.el9_9` (12 novembre). Le dépôt security doit être activé pour proposer le premier ; ne pas présumer qu'il est actif.
- CVE et errata RHSA/RLSA spécifiques à cette révision : **non vérifiés** ; aucune attribution arbitraire.
- Vérifications défensives : `dnf repolist --enabled`, `dnf --enablerepo=security check-update 'kernel*'`, `uname -r`, `rpm -q kernel-core`, espace /boot, kdump et planification des redémarrages.
- **Alerte Java transverse :** le billet GitLab du 20 novembre indique que RHEL 9 n'avait pas encore de correctif Java4Shell au 19 novembre ; état au 21 novembre **non reconfirmé**. Suivre l'erratum du fournisseur et la disponibilité réelle des paquets.

Sources : https://download.rockylinux.org/pub/rocky/9/security/x86_64/os/Packages/k/ ; https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://about.gitlab.com/blog/java4shell-critical-java-flaw-how-gitlab-helps/

## Proxmox VE
- Branche suivie 9.2 (référence officielle précédemment établie). **Dernière version pve-manager non vérifiée aujourd'hui**, champ YAML vide.
- **Dernier kernel Proxmox non vérifié** ; ne pas le confondre avec pve-manager ni avec le kernel Rocky. Aucune régression récente confirmée.
- Contrôler `pveversion -v`, `uname -r`, `apt list --upgradable`, stockage /boot, RAID et sauvegardes avant reboot.

## GitLab Self-Managed CE/EE
- Annonce GitLab 19.6 du 19 novembre : nouvelles fonctionnalités d'automatisation et gouvernance MCP, **pas un bulletin de sécurité à elle seule**.
- Correctifs du 16 novembre à maintenir : 19.5.6 / 19.4.8 / 19.3.9 selon la branche. CVE déjà recensées et non recomptées : CVE-2026-18241 (CVSS 8.2, variables CI/CD protégées), CVE-2026-97187 (7.6, secrets Kubernetes), CVE-2026-105739 (8.7, XSS Wiki), CVE-2026-108153 (8.7, XSS Error Tracking).
- Vecteur réseau ; XSS avec authentification et interaction ; variables CI/CD avec authentification et sans interaction ; secrets Kubernetes avec privilèges élevés. Plages exactes des versions affectées et métriques CVSS complètes **non revalidées** : vérifier le bulletin par branche.
- Suivre également l'exposition des snippets privés sans authentification, les tokens/secrets, les runners et les images CI/CD. Aucune nouvelle RCE GitLab ni auth bypass GitLab confirmée au 21 novembre.
- Actions : sauvegarde, vérification version Omnibus/Runner, maintenance et validation des pipelines.

Sources : https://about.gitlab.com/press/releases/2026-11-19-new-gitlab-agentic-automations/ ; https://about.gitlab.com/releases/

## Nginx — priorité P0
- Avis officiels récents : dix CVE corrigées en 1.30.7 et 1.31.9, avec une exception : CVE-2026-120993 concerne la branche 1.31.4–1.31.8 et est corrigée à partir de 1.31.9.
- Trois gravités **major** selon l'éditeur : CVE-2026-123007 (corruption mémoire HTTP/2), CVE-2026-118899 (boucle infinie TLS 1.3), CVE-2026-113425 (use-after-free proxy mail).
- Autres : CVE-2026-123634 (resolver), CVE-2026-121810 (HTTP/3 ; coquille « CVE-CVE » sur la page éditeur), CVE-2026-123633 (HTTP/2), CVE-2026-120967 (validation SNI/certificat client), CVE-2026-120993 (PROXY v2), CVE-2026-125283 (alias/try_files), CVE-2026-123640 (ssl_preread).
- Scores CVSS et vecteurs détaillés individuels **non vérifiés** ; « major » de l'éditeur n'est pas assimilé à CVSS >= 9. Aucun nouveau comptage de ces CVE déjà signalées.
- Actions : `nginx -V`, `nginx -T`, vérifier modules HTTP/2, HTTP/3, mail, stream et TLS, puis appliquer les paquets corrigés ou les backports de la distribution.

Source : https://nginx.org/en/security_advisories.html

## OpenSSH et MariaDB
- OpenSSH : CVE-2026-123158 déjà suivie ; aucune nouvelle CVE ou nouvelle version corrigée vérifiée au 21 novembre. Vérifier le correctif rétroporté du fournisseur et les conditions d'exposition.
- MariaDB : 10.11 LTS suivie ; dernière révision et nouvelles CVE **non vérifiées** aujourd'hui. Contrôler `mariadb --version`, les errata, sauvegardes et réplication avant maintenance.

## Alerte transverse : Java4Shell (nouvelle qualification)
- **CVE-2026-114938, CVSS 8.1**, corruption mémoire dans l'optimisation Base64 d'OpenJDK sur certaines machines x86-64 ; **RCE sans authentification** démontrée dans plusieurs produits Java sous conditions. Publiée/corrigée par OpenJDK le 17 novembre ; analyse GitLab publiée le 20 novembre.
- Builds corrigés indiqués par GitLab : OpenJDK 17.0.21.1, 21.0.13.1, 25.0.5.1 et 27.0.1.1 (ou correctif rétroporté du fournisseur). Les versions 17.0.6 et ultérieures concernées doivent être évaluées, sans présumer que tous les déploiements sont exploitables.
- Actions défensives : inventorier `java -version` sur les services Java 17, vérifier les builds réels, planifier la mise à jour et le redémarrage ; en attente de correctif, GitLab mentionne l'option JVM `-XX:-UseBASE64Intrinsics` comme mesure temporaire. L'impact sur GitLab lui-même est déclaré non exploitable dans son infrastructure gérée, ce qui ne vaut pas garantie pour les services tiers.
Source : https://about.gitlab.com/blog/java4shell-critical-java-flaw-how-gitlab-helps/

## Méthode de comptage et limites
- CVE distinctes **nouvellement qualifiées lors de cette collecte**, sans doublon interproduits, avec score CVSS confirmé : critique >= 9.0 ; importante >= 7.0 et < 9.0. Une CVE déjà comptée les jours précédents n'est pas recomptée. Java4Shell CVE-2026-114938 (8.1) est la seule nouvelle CVE importante vérifiée dans cette collecte, signalée en complément transverse pertinent au parc.
- Les CVE sans score vérifié ne sont pas incluses ; les compteurs ne représentent **pas** le stock de vulnérabilités.
- `status: critical` exprime l'urgence opérationnelle Nginx, GitLab et Java, pas une nouvelle CVE CVSS >= 9.
- Limites : dernière version pve-manager et kernel Proxmox, détail des errata Rocky, scores Nginx, nouveautés OpenSSH/MariaDB non établis.
