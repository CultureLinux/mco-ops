---
type: mco-daily
date: 2026-11-22
generated_at: 2026-11-22T07:58:22+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-741.24.1.el9_9"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — 22 novembre 2026

## Résumé exécutif
**Nouveau kernel Rocky Linux 9.9 dans BaseOS : 5.14.0-741.24.1.el9_9**, daté du 20 novembre, plus récent que la référence du 21 novembre. **Nginx : dix CVE récentes**, correctifs upstream 1.30.7/1.31.9 ; **GitLab Self-Managed :** correctifs de sécurité du 16 novembre et mise à niveau 19.6 à qualifier. Aucun nouvel identifiant CVE avec score confirmé ≥ 7 n'a été établi pour cette collecte ; cela ne prouve pas l'absence de vulnérabilités.

## Rocky Linux / RHEL 9
- **BaseOS x86_64 : kernel 5.14.0-741.24.1.el9_9** publié dans l'index le 20 novembre à 18:27 UTC. Il remplace comme référence la révision 5.14.0-741.22.1.el9_9 du 12 novembre.
- Dépôt distinct **security** : 5.14.0-741.22.1.el9_9.0.1 observé au 18 novembre. Les deux dépôts ont des politiques de versions différentes ; ne pas déduire automatiquement les correctifs applicables de leur comparaison lexicale.
- CVE précises et errata RHSA/RLSA de la nouvelle révision : **non établis**. Contrôler les dépôts activés, `dnf check-update 'kernel*'`, `uname -r`, `rpm -q kernel-core`, /boot, kdump et fenêtre de redémarrage.
- Alerte transverse Java4Shell CVE-2026-114938, CVSS 8.1, précédemment recensée : vérifier les JVM et correctifs fournisseur, **non recomptée** aujourd'hui.
Sources : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://download.rockylinux.org/pub/rocky/9/security/x86_64/os/Packages/k/

## Proxmox VE
- Branche majeure suivie : **9.2** (référence précédente). **Dernière version pve-manager non reconfirmée**, champ YAML laissé vide.
- **Dernière révision du kernel Proxmox non reconfirmée** : distincte de `pve-manager` et du kernel Rocky. Aucune nouvelle régression vérifiée.
- Contrôler `pveversion -v`, `uname -r`, `apt list --upgradable`, état RAID, sauvegardes et kdump avant redémarrage.

## GitLab Self-Managed CE/EE
- Annonce officielle **GitLab 19.6 du 19 novembre 2026** : évolutions produit et gouvernance ; l'annonce n'est pas en elle-même un bulletin de sécurité.
- Correctifs de sécurité précédemment identifiés le 16 novembre : **19.5.6 / 19.4.8 / 19.3.9**, selon la branche. Les versions affectées exactes et la disponibilité d'éventuels correctifs ultérieurs n'ont pas été revalidées aujourd'hui.
- CVE déjà suivies : **CVE-2026-18241** (CVSS 8.2, accès aux variables CI/CD protégées), **CVE-2026-97187** (7.6, secrets Kubernetes), **CVE-2026-105739** (8.7, XSS Wiki) et **CVE-2026-108153** (8.7, XSS Error Tracking). Elles ne sont pas recomptées.
- Maintenir l'inventaire des risques RCE, auth bypass, accès sans authentification, fuite de tokens et secrets, ainsi que les versions de Runner et les magasins de certificats des images CI/CD. Conditions d'exploitation détaillées et versions affectées : non revalidées lors de cette collecte.
Sources : https://about.gitlab.com/press/releases/2026-11-19-new-gitlab-agentic-automations/ ; https://about.gitlab.com/releases/

## Nginx
- Avis officiels toujours actifs : dix CVE récentes, dont **CVE-2026-123007** (corruption mémoire HTTP/2, major), **CVE-2026-118899** (boucle TLS 1.3, major), **CVE-2026-113425** (use-after-free mail proxy, major), **CVE-2026-123634** (resolver, medium), **CVE-2026-121810** (HTTP/3, medium), **CVE-2026-123633** (HTTP/2, medium).
- Correctifs upstream : **1.30.7+ / 1.31.9+** pour les vulnérabilités listées. Contrôler les modules réellement activés, versions de paquets et backports des distributions ; éviter toute qualification CVSS inventée.
- Les CVE déjà recensées ne sont pas recomptées ; aucun nouveau score CVSS individuel vérifié aujourd'hui.
Source : https://nginx.org/en/security_advisories.html

## OpenSSH
- Pas de nouvelle CVE individuellement vérifiée aujourd'hui. Suivi de CVE-2026-123158 signalée précédemment ; conditions d'exposition et correctifs rétroportés à confirmer par les bulletins éditeur/distribution.

## MariaDB
- Branche 10.11 LTS surveillée ; dernière version exacte et nouveaux avis de sécurité **non reconfirmés**. Vérifier les paquets installés et les avis officiels avant toute conclusion d'absence de risque.

## Comptage et limites
- Règle : compter **uniquement les CVE distinctes nouvellement confirmées lors de la collecte**, avec score CVSS vérifié ; **critique ≥ 9.0**, **importante ≥ 7.0 et < 9.0**. Dédoublonnage interproduits et exclusion des CVE déjà recensées les jours précédents.
- **critical_cve: 0 ; important_cve: 0** : aucune nouvelle CVE qualifiée dans cette collecte, pas un bilan exhaustif des vulnérabilités.
- `status: critical` reflète l'urgence opérationnelle persistante des correctifs Nginx/GitLab/Java, et non la découverte d'une nouvelle CVE critique.
- Limites : derniers pve-manager/kernel Proxmox, errata précis du kernel Rocky, scores Nginx, nouvelles versions OpenSSH/MariaDB non vérifiés.
