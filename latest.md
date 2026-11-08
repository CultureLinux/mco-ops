---
type: mco-daily
date: 2026-11-08
generated_at: 2026-11-08T08:03:12+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — dimanche 8 novembre 2026

## Résumé exécutif
- **Rocky Linux 9 :** dernier kernel observé dans BaseOS x86_64 : **5.14.0-687.66.1.el9_8** (5 novembre 2026, 21:26 UTC). Aucun kernel plus récent confirmé.
- **GitLab CE/EE :** correctifs 19.5.3/19.4.5/19.3.7 publiés le 29 octobre ; le bulletin précise **qu'il ne contient pas de correctifs de sécurité**, contrairement à une interprétation précédente. Il inclut néanmoins la défiance des certificats eMudhra compromis.
- **Proxmox VE :** référence suivie 9.2, pve-manager 9.2.24 et kernel 7.0.14-26-pve (références historiques, dernières versions non reconfirmées). Un patch Proxmox du 3 novembre mentionne 9.2.25 pour la compatibilité des notifications de clusters mixtes : ce n'est pas une preuve de disponibilité du paquet.
- **Nginx :** quatre avis CVE officiels affectant certaines versions, corrigés en 1.30.6+ ou 1.31.7+.
- **OpenSSH / MariaDB :** pas de nouvelle version ou CVE confirmée lors de cette collecte ciblée.

## Méthode et limites
Les compteurs **0 critique / 0 importante** désignent exclusivement les **nouvelles CVE distinctes avec score CVSS vérifié dans cette collecte du 8 novembre**, pas l'ensemble des risques connus. Seuils : critique CVSS >= 9.0 ; importante 7.0 <= CVSS < 9.0. Une CVE affectant plusieurs produits est comptée une seule fois. Les alertes antérieures ne sont pas déclarées résolues. Les vecteurs AV/AC/PR/UI, fonctionnalités, versions affectées et corrigées ne sont indiqués que lorsqu'ils sont confirmés dans un avis officiel ; sinon ils demeurent indéterminés.

## Rocky Linux / RHEL 9
La version `kernel-5.14.0-687.66.1.el9_8.x86_64` et `kernel-core` sont présentes dans le dépôt officiel Rocky BaseOS. Date du paquet : 5 novembre 2026. L'erratum propre à cette révision, les CVE corrigées et une éventuelle régression ne sont pas confirmés. Vérifier `dnf updateinfo list security`, `dnf check-update 'kernel*'`, l'espace /boot, les modules tiers et `uname -r` après reboot.
Source : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/?C=M&O=D
Pour RHEL : https://access.redhat.com/errata/
L'alerte historique CVE-2026-98432 (risque réseau TCP IPv4 sans authentification, score 9.8 précédemment rapporté) nécessite une vérification individuelle du bulletin et des paquets avant conclusion.

## Proxmox VE
Références historiques : Proxmox VE 9.2 ; `pve-manager 9.2.24` ; kernel `7.0.14-26-pve`. **Dernières versions non vérifiées ce jour.** Le kernel et pve-manager sont des composants distincts. Contrôler `pveversion -v`, `uname -r`, `apt policy pve-manager` et les paquets proxmox-kernel. Les PSA précédemment suivis 00075–00080 demandent une vérification individuelle. Tester ZFS, KVM, LXC, réseau et sauvegardes avant redémarrage. Un patch daté du 3 novembre évoque un garde-fou pour les matchers de notifications dans des clusters aux versions hétérogènes (seuil 9.2.25) ; publication du paquet non établie.
Source : https://lore.proxmox.com/pve-devel/20261103104859.172466-40-l.wagner%40proxmox.com/

## GitLab Self-Managed CE/EE, Runner, CI/CD
Bulletin officiel du **29 octobre 2026** : GitLab CE/EE **19.5.3, 19.4.5, 19.3.7, 19.2.10, 19.1.10, 19.0.11 et 18.11.14** ; le bulletin affirme explicitement **« does not include any security fixes »**. Il liste néanmoins une modification de confiance CA eMudhra, à vérifier dans GitLab, Runner et images CI/CD. Ne pas présenter ces versions comme les dernières aujourd'hui. Le Runner 19.5.1 est une référence historique non reconfirmée.
Les alertes historiques CVE-2026-110264 et CVE-2026-97229, précédemment qualifiées de RCE sans authentification CVSS 10.0, ne sont **pas individuellement revalidées aujourd'hui** ; ne pas en déduire les versions affectées, vecteurs ou remédiations sans avis officiel. Vérifier les patch releases de sécurité et l'exposition de l'instance, les tokens/secrets, pipelines et permissions des runners.
Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## Nginx, OpenSSH, MariaDB
Nginx : avis officiels **CVE-2026-15176** (alias/rewrite), **CVE-2026-93479** (évaluation de script), **CVE-2026-95101** (HTTP/3), **CVE-2026-97629** (tickets de session TLS), tous classés **medium par nginx** ; versions non vulnérables indiquées **1.30.6+ et 1.31.7+**. Les scores CVSS, vecteurs détaillés, prérequis et impacts exacts n'ont pas été vérifiés individuellement ; aucune de ces CVE n'est comptée comme nouvelle critique/importante. Vérifier les fonctionnalités activées et les backports.
Source : https://nginx.org/en/security_advisories.html
OpenSSH : aucune nouvelle version ou CVE confirmée dans cette collecte ; contrôler les bulletins et les backports de distribution. Source : https://www.openssh.com/security.html
MariaDB : aucune nouvelle version ou CVE confirmée dans cette collecte ; vérifier la branche 10.11 et les bulletins de sécurité, les paquets RPM et l'exposition réseau. Source : https://mariadb.org/about/security-policy/

## Priorités d'exploitation
Comparer les versions **installées et actives** aux versions corrigées officielles ; qualifier les mises à jour Rocky et Proxmox avant reboot ; contrôler les correctifs GitLab/Runner et les chaînes de confiance CI/CD. Aucun test offensif.
