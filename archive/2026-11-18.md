---
type: mco-daily
date: 2026-11-18
generated_at: 2026-11-18T07:58:14+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-741.22.1.el9_9"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — mercredi 18 novembre 2026

## Résumé exécutif

**P0 GitLab :** maintenir la priorité sur le patch de sécurité du **16 novembre 2026** (CE/EE 19.5.6, 19.4.8, 19.3.9), notamment le contournement d'autorisation CI/CD et l'exposition de secrets Kubernetes. **P1 Rocky Linux 9.9 :** le dépôt BaseOS x86_64 affiche le kernel **5.14.0-741.22.1.el9_9** (paquet daté du 12 novembre). **P1 Nginx :** correctifs de plusieurs vulnérabilités dans les branches maintenues. Aucun nouvel avis du 18 novembre individuellement scoré n'a été confirmé lors de cette collecte.

## GitLab Self-Managed CE/EE — urgence P0

La publication officielle GitLab du **16 novembre 2026** recommande la mise à jour immédiate vers **19.5.6, 19.4.8 ou 19.3.9**, selon la branche maintenue. Les vulnérabilités suivantes restent à traiter si l'instance n'est pas corrigée (elles ont été comptées dans le rapport du 17 novembre et ne sont **pas recomptées** aujourd'hui).

| CVE | CVSS | Fonctionnalité / impact | Vecteur et conditions | Versions corrigées |
|---|---:|---|---|---|
| CVE-2026-105739 | 8.7 | XSS Wiki | Réseau ; authentifié ; interaction requise | 19.3.9 / 19.4.8 / 19.5.6 |
| CVE-2026-108153 | 8.7 | XSS Error Tracking | Réseau ; authentifié ; interaction requise | idem |
| **CVE-2026-18241** | **8.2** | **Accès indu aux variables et ressources CI/CD protégées** | Réseau ; authentifié ; faible privilège ; aucune interaction ; complexité élevée | idem |
| **CVE-2026-97187** | **7.6** | **Lecture de secrets Kubernetes d'autres namespaces** | Réseau ; privilèges élevés ; aucune interaction | idem |

Les plages affectées commencent respectivement à 17.2, 16.1, 9.3 et 17.6, puis concernent les branches 19.3, 19.4 et 19.5 jusqu'aux correctifs ci-dessus. D'autres vulnérabilités de gravité inférieure sont également corrigées, dont la lecture non authentifiée de fichiers privés via snippets (CVE-2026-104934). Vérifier séparément GitLab Runner **19.5.1** et les images CI/CD avec magasins CA embarqués pour le retrait des certificats eMudhra (avis du 29 octobre).

Sources : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-6-released/ ; https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/

## Rocky Linux / RHEL 9

Le dépôt officiel Rocky Linux 9 BaseOS x86_64 liste **kernel / kernel-core 5.14.0-741.22.1.el9_9**, datés du **12 novembre 2026 18:20 UTC**. C'est une version de paquet, pas la preuve du kernel actuellement démarré sur les machines. Les errata RLSA/RHSA, CVE exactes corrigées et régressions de ce kernel ne sont pas individuellement vérifiés pendant cette collecte : ne pas les inventer. Contrôler `dnf updateinfo`, `dnf check-update`, `uname -r`, l'espace `/boot` et le redémarrage après qualification.

Source : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## Proxmox VE

Branche majeure suivie : **9.2** (référence des rapports précédents). Le dernier paquet **pve-manager** n'a pas été confirmé dans un index officiel lors de cette collecte : champ YAML volontairement vide. Référence antérieure non reconfirmée : **pve-manager 9.2.26**. La version la plus récente du **kernel Proxmox** n'a pas été établie et ne doit pas être confondue avec `pve-manager`. Contrôler `pveversion -v`, `proxmox-boot-tool kernel list` si applicable, `uname -r`, les dépôts activés et les notes de régression avant déploiement.

## OpenSSH

Le suivi antérieur mentionne **CVE-2026-123158**, avec des conditions d'exposition particulières. Son score, ses versions affectées et les correctifs propres à Rocky/RHEL n'ont pas été revérifiés aujourd'hui ; aucune attribution nouvelle de gravité n'est faite. Privilégier les errata de distribution et les rétroportages.

## Nginx

L'avis officiel liste six CVE récentes de gravité éditeur **medium** : CVE-2026-15176 (alias/rewrite), CVE-2026-93479 (script), CVE-2026-95101 (HTTP/3), CVE-2026-97629 (tickets TLS), CVE-2026-97205 (AIO) et CVE-2026-90439 (HTTP/3). Les cinq premières sont corrigées dans **1.30.6+ / 1.31.7+**, la dernière dans **1.30.5+ / 1.31.6+**. Vérifier la présence effective des modules et les correctifs rétroportés de la distribution ; aucune CVSS primaire nouvelle n'est attribuée ici.

Source : https://nginx.org/en/security_advisories.html

## MariaDB

La branche 10.11 LTS reste suivie ; **10.11.19** est une référence des précédents rapports, non revérifiée aujourd'hui comme dernière version. Aucun nouvel avis de sécurité ou CVSS n'a pu être établi avec certitude pendant cette collecte. Contrôler les publications MariaDB et les errata des paquets réellement installés.

## Méthode, compteurs et limites

Compter **une seule fois par identifiant CVE** les nouvelles vulnérabilités **individuellement vérifiées pendant cette collecte**, toutes familles confondues. **Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0.** Les CVE déjà comptées les jours précédents ne sont pas recomptées ; les scores inconnus restent exclus. **0 nouvelle CVE critique et 0 nouvelle CVE importante confirmées le 18 novembre** : ce n'est ni un inventaire exhaustif ni une affirmation d'absence de risque. Le statut **critical** reflète la priorité opérationnelle persistante du correctif GitLab touchant les secrets CI/CD, non une nouvelle CVE de score >= 9.0. Sources officielles consultées ; données non vérifiées explicitement signalées. Aucune instruction offensive.
