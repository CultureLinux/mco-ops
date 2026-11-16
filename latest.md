---
type: mco-daily
date: 2026-11-16
generated_at: 2026-11-16T08:03:40+01:00
timezone: Europe/Paris
status: warning
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-687.66.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.26"
---

# Brief MCO défensif — lundi 16 novembre 2026

## Résumé exécutif

- **P0 GitLab Self-Managed CE/EE :** appliquer la Critical Patch Release du 10 novembre (19.5.4 / 19.4.6 / 19.3.8 au minimum selon la branche), notamment pour CVE-2026-105437, risque d'exécution de commandes dans Gitaly. Les versions 19.5.5 et 19.4.7 du 12 novembre corrigent des régressions et héritent des correctifs de leur branche ; cette dernière publication n'ajoute pas de correctif de sécurité.
- **P1 Rocky Linux/RHEL 9 :** dernier kernel Rocky BaseOS x86_64 vérifié 5.14.0-687.66.1.el9_8 (paquet daté du 5 novembre). Erratum RHSA et CVE spécifiques non établis dans cette collecte.
- **P1 Proxmox VE :** version majeure suivie 9.2 ; pve-manager 9.2.26 dernière version précédemment vérifiée, non reconfirmée aujourd'hui ; version du dernier kernel Proxmox non vérifiable et **distincte de pve-manager**.
- **P1 OpenSSH/Nginx :** suivre CVE-2026-123158 (OpenSSH, conditions musl sans PAM selon le suivi CERT-FR) et les six CVE moyennes de Nginx. Vérifier les backports des distributions.
- **P2 MariaDB :** 10.11.19 LTS est la version 10.11 publiée référencée par la Fondation ; aucune nouvelle alerte CVE importante confirmée dans la collecte.

## GitLab CE/EE, Gitaly et CI/CD

**CVE-2026-105437 — urgence P0 opérationnelle.** Le bulletin officiel GitLab du 10 novembre décrit un débordement de tampon heap dans Git embarqué, pouvant sous conditions permettre l'exécution de commandes dans l'environnement Gitaly. Versions affectées : CE/EE à partir de 16.2 avant 19.3.8, branche 19.4 avant 19.4.6, branche 19.5 avant 19.5.4. Correctifs : 19.3.8 / 19.4.6 / 19.5.4 et versions ultérieures correspondantes. Le suivi antérieur donne CVSS 7.5, AV:N/AC:H/PR:N/UI:R : réseau, complexité élevée, aucun privilège, interaction utilisateur requise ; **ne pas assimiler à une RCE sans interaction**. Le bulletin GitLab qualifie néanmoins la correction de « Critical ». Les installations compilées depuis les sources doivent également reconstruire Git/Gitaly, ou utiliser un Git corrigé. Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-4-released/

**Publication du 12 novembre :** GitLab 19.5.5 / 19.4.7, correctifs de bugs et régressions, aucun nouveau correctif de sécurité. Migrations de base de données ; prévoir une interruption sur instance mononœud. GitLab Orbit est désactivé par défaut. Source : https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-5-released/

**Chaîne CI/CD et certificats :** suivre séparément la correction antérieure CVE-2026-111943 relative aux certificats eMudhra compromis, les versions GitLab Runner et les magasins CA propres aux images CI/CD. Le niveau de risque précis et les versions Runner les plus récentes n'ont pas été revalidés ce matin. Aucune nouvelle fuite de tokens/secrets, auth bypass ou RCE sans authentification n'est confirmée dans cette collecte ; cela ne constitue pas une attestation d'absence.

## Rocky Linux / RHEL 9

Le dépôt Rocky officiel BaseOS x86_64 présente kernel et kernel-core **5.14.0-687.66.1.el9_8**, datés du 5 novembre 2026. Aucun kernel plus récent n'a été trouvé dans l'index consulté. La correspondance exacte avec les errata Red Hat, les CVE corrigées, leur CVSS et les éventuelles régressions n'a pas été établie ; ne pas attribuer des CVE à cette révision sans erratum. Contrôler `dnf updateinfo list --security`, `dnf check-update`, `uname -r`, `rpm -q kernel-core` et l'espace `/boot` avant redémarrage. Source : https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## Proxmox VE

Proxmox VE 9.2 est la famille suivie. **pve-manager 9.2.26** est une référence du suivi officiel antérieur, non revalidée aujourd'hui comme dernière version. Le dernier **kernel pve** et les correctifs PSA/régressions ne sont pas confirmés : ne pas déduire la version du kernel de celle de pve-manager. Sur chaque nœud, comparer `pveversion -v`, `uname -r`, `apt policy pve-manager` et les versions des paquets kernel ; qualifier sauvegardes, stockage et réseau avant mise à jour. Source : https://pve.proxmox.com/wiki/Package_Repositories

## OpenSSH, Nginx, MariaDB

**OpenSSH :** CVE-2026-123158 suivie dans les avis CERT-FR précédents, contournement d'authentification sous configurations Linux musl sans PAM ; version corrective upstream rapportée 10.6p2. L'avis primaire et le CVSS n'ont pas été revalidés aujourd'hui. Sur Rocky/RHEL, examiner les errata et backports plutôt que le seul numéro upstream. Source : https://www.openssh.com/security.html

**Nginx :** l'éditeur documente CVE-2026-15176, CVE-2026-93479, CVE-2026-95101, CVE-2026-97629 et deux autres CVE suivies dans les précédents rapports, toutes classées « medium » par l'éditeur. Pour les quatre CVE nommées, branches non vulnérables 1.30.6+ / 1.31.7+ selon le bulletin ; vérifier les backports des paquets Rocky. Aucun score CVSS individuel n'a été vérifié. Source : https://nginx.org/en/security_advisories.html

**MariaDB :** Fondation MariaDB : 10.11.19 LTS (21 août 2026), 11.4.13 LTS, 11.8.9 LTS et 12.3.3 LTS. Aucun nouvel avis de sécurité critique ou important confirmé aujourd'hui. Contrôler les correctifs de la branche réellement installée et les sauvegardes avant montée de version. Source : https://mariadb.org/

## Méthode, compteurs et limites

Périmètre des compteurs : **CVE distinctes nouvellement vérifiées et scorées dans la collecte du 16 novembre**, pas le stock historique des CVE ouvertes. Critique : CVSS >= 9.0 ; importante : 7.0 <= CVSS < 9.0 ; dédoublonnage global par identifiant CVE entre produits. **0 critique / 0 importante nouvelle** aujourd'hui : CVE-2026-105437 était déjà recensée et les autres CVE n'ont pas de score primaire nouveau validé. Les zéros n'impliquent aucune absence de vulnérabilités. Statut warning en raison des correctifs antérieurs encore à vérifier sur le parc et des données non reconfirmées. Les versions non établies sont signalées explicitement ; aucune instruction offensive.
