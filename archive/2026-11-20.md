---
type: mco-daily
date: 2026-11-20
generated_at: 2026-11-20T08:00:55+01:00
timezone: Europe/Paris
status: critical
critical_cve: 0
important_cve: 0
rocky_kernel_latest: "5.14.0-741.22.1.el9_9.0.1"
proxmox_major: "9.2"
proxmox_pve_manager_latest: ""
---

# Brief MCO défensif — 20 novembre 2026

## Résumé exécutif
**P0 Nginx :** correctifs 1.30.7 / 1.31.9 contre dix vulnérabilités publiées le 18 novembre, notamment HTTP/2, TLS 1.3 et proxy mail. **Nouveau point Rocky :** le dépôt *security* contient un kernel `5.14.0-741.22.1.el9_9.0.1` du 18 novembre, plus récent que le paquet BaseOS. **GitLab 19.6** annoncé le 19 novembre ; conserver la priorité sur les correctifs de sécurité GitLab du 16 novembre.

## Rocky Linux / RHEL 9
- BaseOS x86_64 : `kernel-5.14.0-741.22.1.el9_9` (12 novembre 2026).
- Dépôt Rocky 9 **security** x86_64 : `kernel-5.14.0-741.22.1.el9_9.0.1` (18 novembre 2026 à 20:48 UTC), avec `kernel-core` et modules correspondants. La disponibilité effective dépend de l'activation de ce dépôt sur chaque serveur ; ne pas assimiler ce paquet au kernel disponible par défaut.
- CVE, vecteurs et errata spécifiques à la révision `.0.1` : **non établis** ; ne pas lui attribuer des corrections précises sans bulletin.
- Red Hat publie des errata kernel RHEL 9 (par exemple RHSA-2026:97873 du 11 novembre et RHSA-2026:99135 du 12 novembre), mais leur correspondance exacte avec la révision Rocky `.0.1` n'est pas établie.
- Actions : `dnf repolist --enabled`, `dnf updateinfo list security`, `dnf --enablerepo=security check-update kernel\*`, `uname -r`, `rpm -q kernel-core`, espace /boot ; valider kdump/RAID et planifier le reboot.

Sources : https://download.rockylinux.org/pub/rocky/9/security/x86_64/os/Packages/k/ ; https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/ ; https://access.redhat.com/security/cve/cve-2026-80586

## Proxmox VE
- Branche majeure suivie : **9.2**.
- Dernière version de `pve-manager` : **non reconfirmée**, champ YAML volontairement vide (référence historique 9.2.26, pas une validation du jour).
- Kernel Proxmox : dernière révision publiée **non vérifiée** ; distincte de `pve-manager` et du kernel Rocky. Aucune régression nouvelle vérifiée.
- Actions : `pveversion -v`, `uname -r`, `apt list --upgradable`, contrôle du kernel booté et des sauvegardes VM avant redémarrage.

## GitLab Self-Managed CE/EE
- **GitLab 19.6 annoncé officiellement le 19 novembre** : évolutions d'automatisation agentique, gouvernance MCP et remédiation ; ce communiqué ne constitue pas à lui seul une Security Patch Release et ne confirme pas une CVE nouvelle.
- Bulletin de sécurité du 16 novembre : correctifs suivis **19.5.6 / 19.4.8 / 19.3.9**. Risques déjà identifiés : CVE-2026-18241 (CVSS 8.2, accès aux variables CI/CD protégées), CVE-2026-97187 (7.6, secrets Kubernetes inter-namespace), CVE-2026-105739 (8.7, XSS Wiki), CVE-2026-108153 (8.7, XSS Error Tracking). Ces CVE ne sont **pas recomptées**.
- Pour ces quatre CVE, vecteur réseau ; les XSS exigent authentification et interaction utilisateur ; CI/CD exige authentification sans interaction ; Kubernetes exige des privilèges élevés. Les bornes exactes de versions affectées et les métriques CVSS complètes ne sont pas revalidées ce jour : consulter le bulletin officiel avant toute qualification par instance.
- Surveiller en outre les accès sans authentification aux snippets privés, l'exposition de secrets/tokens, les runners, les images CI/CD et les régressions post-upgrade. Aucune nouvelle RCE ou auth bypass datée du 20 novembre n'est individuellement confirmée.
- Actions : vérifier `gitlab-rake gitlab:env:info`, version des runners, sauvegarde, puis déployer la version corrigée adaptée à la branche avec validation des migrations et pipelines.

Sources : https://about.gitlab.com/press/releases/2026-11-19-new-gitlab-agentic-automations/ ; https://about.gitlab.com/releases/

## Nginx
- **Dix CVE récentes** dans l'avis officiel ; versions corrigées upstream **1.30.7+ / 1.31.9+**. Parmi elles : CVE-2026-123007 (corruption mémoire HTTP/2, *major*), CVE-2026-118899 (boucle TLS 1.3, *major*), CVE-2026-113425 (use-after-free mail proxy, *major*), CVE-2026-123634 (resolver, *medium*), CVE-2026-123633 (HTTP/2, *medium*), CVE-2026-121810 (HTTP/3, *medium*).
- Les autres vulnérabilités concernent notamment SNI/mTLS, PROXY protocol, alias/try_files et ssl_preread. Gravité éditeur ≠ score CVSS. **Scores CVSS et conditions d'exploitation détaillées non validés** ; ne pas en déduire une RCE ou exploitation sans authentification confirmée.
- Priorité aux Nginx exposés, y compris les reverse proxies frontaux ; vérifier les modules activés et les correctifs rétroportés des distributions. `nginx -v`, `nginx -T`, `rpm -q nginx` ou `dpkg -l nginx\*`, puis `nginx -t` et reload/restart maîtrisé.

Source : https://nginx.org/en/security_advisories.html

## OpenSSH
- CVE-2026-123158 précédemment suivie : contexte musl / absence de PAM signalé dans les précédents avis ; qualification exacte et score non reconfirmés ce jour. Ne pas extrapoler aux paquets OpenSSH de Rocky/RHEL sans erratum correspondant.
- Pas de nouvel avis critique individuellement vérifié au 20 novembre. Contrôler `rpm -q openssh-server` et `dnf updateinfo`.

## MariaDB
- Branche 10.11 LTS suivie ; référence historique 10.11.19, **non reconfirmée comme dernière version**.
- Aucune nouvelle CVE importante datée du jour vérifiée ; vérifier les avis éditeur, le dépôt installé et les versions corrigées plutôt que d'inférer une absence de risque.
- Contrôler `mariadb --version`, `dnf updateinfo`, sauvegardes et réplication avant maintenance.

## Méthode et limites
- Fenêtre : informations nouvelles ou encore opérationnellement pertinentes au 20 novembre 2026, sources officielles en priorité.
- **Comptage** : identifiants CVE distincts nouvellement confirmés **avec score CVSS vérifié pendant cette collecte**, critique CVSS >= 9.0, importante 7.0 <= CVSS < 9.0 ; une CVE touchant plusieurs produits compte une seule fois. Les CVE déjà recensées et les CVE sans score confirmé ne sont pas ajoutées. `critical_cve: 0` et `important_cve: 0` **ne signifient pas zéro vulnérabilité**.
- `status: critical` reflète l'urgence opérationnelle persistante des vulnérabilités Nginx et GitLab, non l'existence d'une nouvelle CVE CVSS >= 9.
- Absences de vérification : dernier pve-manager, dernier kernel Proxmox, correspondance RHSA/kernel Rocky security, scores CVSS des dix avis Nginx, nouvelles CVE OpenSSH/MariaDB.
