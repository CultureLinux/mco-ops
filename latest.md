---
type: mco-daily
date: 2026-10-26
generated_at: 2026-10-26T08:00:51+01:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 16
rocky_kernel_latest: "5.14.0-687.60.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.21"
---

# Brief MCO défensif — lundi 26 octobre 2026

## Résumé exécutif

- **NOUVEAU — Rocky Linux 9** : le dépôt officiel BaseOS x86_64 contient le kernel **5.14.0-687.60.1.el9_8**, daté du **23 octobre 2026 à 18:54 UTC**. Il remplace le précédent dernier paquet suivi (687.59.1). L'erratum et le détail des corrections propres à 687.60.1 n'ont pas pu être associés avec certitude à cette version : ne pas lui attribuer de nouvelles CVE sans preuve.
- **CRITICAL — GitLab Self-Managed** : deux RCE CVSS 10.0 sans authentification, dont une touchant CI/CD ; correctifs des **12 et 21 octobre**. Mise à jour prioritaire des instances encore vulnérables.
- **CRITICAL — Hyperviseurs** : PwnEscape, CVE-2026-99999, permet une évasion KVM sous conditions ; correctifs Rocky/RHEL et Proxmox disponibles. Vérifier le noyau *démarré*.
- **WARNING — Proxmox** : six avis du 19 octobre corrigés dans **qemu-server >= 9.2.11** ; vigilance Secure Boot sur les noyaux signés.
- **WARNING — Nginx/MariaDB/OpenSSH** : versions correctives connues, contrôler les branches installées et les backports.

**Fenêtre de collecte :** sources officielles consultées le 26 octobre au matin (Europe/Paris). Aucun nouvel avis officiel daté du 26 octobre n'a été confirmé avant la publication ; cette absence de confirmation n'exclut pas des publications ultérieures.

## Comptage CVE — méthode et périmètre

Les champs YAML représentent les **CVE distinctes nommées et scorées dans le tableau ci-dessous**, issues des deux Critical Patch GitLab d'octobre, de l'avis Red Hat PwnEscape et des correctifs MariaDB 10.11.19. Ce sont des vulnérabilités **à surveiller**, pas des CVE apparues ce matin. Seuils **CVSS v3.1 : critical >= 9.0 ; important >= 7.0 et < 9.0**. Une CVE affectant plusieurs produits est comptée **une seule fois**. Pour CVE-2026-99999, le score **Red Hat 8.8** prime sur le score CNA 9.3 pour cette synthèse. Les CVE sans score vérifié sont décrites mais exclues des compteurs.

| Gravité | CVE | CVSS | Produit |
| --- | --- | ---: | --- |
| Critical | CVE-2026-110264 | 10.0 | GitLab, correctif 21/10 |
| Critical | CVE-2026-97229 | 10.0 | GitLab CI/CD, correctif 12/10 |
| Critical | CVE-2026-92262 | 9.9 | MariaDB 10.11.19 |
| Critical | CVE-2026-85746 | 9.9 | MariaDB 10.11.19 |
| Important | CVE-2026-15723 | 8.2 | GitLab |
| Important | CVE-2026-112212 | 7.5 | GitLab |
| Important | CVE-2026-92883 | 8.7 | GitLab |
| Important | CVE-2026-16301 | 8.5 | GitLab CI/CD |
| Important | CVE-2026-14814 | 8.2 | GitLab CI/CD/secrets |
| Important | CVE-2026-81839 | 7.7 | GitLab EE/CI/CD |
| Important | CVE-2026-96893 | 7.3 | GitLab EE/Duo |
| Important | CVE-2026-99999 | 8.8 | Linux kernel, RHEL/Proxmox (une fois) |
| Important | CVE-2026-107823 | 7.2 | MariaDB |
| Important | CVE-2026-107821 | 8.0 | MariaDB |
| Important | CVE-2026-107818 | 8.4 | MariaDB |
| Important | CVE-2026-107815 | 8.5 | MariaDB |
| Important | CVE-2026-107814 | 8.4 | MariaDB |
| Important | CVE-2026-102523 | 8.0 | MariaDB |
| Important | CVE-2026-97380 | 8.8 | MariaDB |
| Important | CVE-2026-86047 | 7.7 | MariaDB |

Les scores proviennent des bulletins officiels GitLab, Red Hat et MariaDB. Les CVE Nginx sont qualifiées « medium » par l'éditeur, sans score numérique repris ici ; elles ne sont pas comptées.

## 1. Rocky Linux / RHEL 9 — NOUVEAU KERNEL

**Dernier kernel Rocky Linux 9 x86_64 vérifié dans le dépôt officiel : `5.14.0-687.60.1.el9_8`** (23/10/2026, 18:54:33 UTC). Le précédent `5.14.0-687.59.1.el9_8` date du 21/10. Vérification effectuée sur l'index **BaseOS**, pas seulement sur un dépôt de staging.

**CVE-2026-99999 — PwnEscape** : CVSS Red Hat **8.8**, `AV:L/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:H`. Un invité KVM non fiable ou un attaquant local capable de fournir des métadonnées checksum malformées peut provoquer une corruption mémoire du kernel hôte, avec risque d'évasion, divulgation et DoS. **Pas une RCE directement exploitable via un port réseau exposé sans contexte d'invité/local.** Red Hat signale un cas d'exploitation active.

**Correctif confirmé pour RHEL 9** : **RHSA-2026:86033** du 20 octobre, kernel de la série **687.59.1** ; le kernel Rocky **687.60.1** est plus récent. L'erratum spécifique, les CVE additionnelles et les régressions propres à **687.60.1** restent **non confirmés** lors de cette collecte. Ne pas attribuer à cette version des correctifs non documentés.

Autres CVE signalées dans l'erratum précédent : CVE-2026-31678 (Open vSwitch), CVE-2026-64024 (TCP), CVE-2026-64032 (bridge), CVE-2026-80590 (GSO), CVE-2026-89633 (SMB), CVE-2026-93817 (perf). Leurs scores individuels et prérequis ne sont pas revérifiés ici et sont exclus des compteurs.

**Actions** : `dnf updateinfo list --security`, `dnf check-update kernel-core`, `rpm -q kernel-core`, puis planifier le redémarrage ; vérifier `uname -r`, les pilotes tiers, le réseau et les journaux kernel. Contrôler séparément les flux EUS/SAP et les paquets kernel-rt.

Sources :
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://access.redhat.com/security/cve/CVE-2026-99999
- https://access.redhat.com/errata/RHSA-2026:86033

## 2. Proxmox VE 9 — kernel, manager et régressions

**Branche vérifiée : Proxmox VE 9.2**. Dans l'index officiel **pve-no-subscription**, dernier changelog `pve-manager` identifié : **9.2.21** (29/09/2026). **Kernel séparé** : dernier changelog `proxmox-kernel-7.0` identifié **7.0.14-25** (19/10/2026), y compris variante **signed**. Les numéros de version `pve-manager`, `qemu-server` et du noyau ne sont pas interchangeables.

**PSA-2026-00068-1 du 16/10 : PwnEscape / CVE-2026-99999** : évasion d'un invité KVM via virtio et métadonnées checksum ; **corrigé dans `proxmox-kernel-7.0.14-24-pve(-signed)`**. Pour Secure Boot, vérifier l'installation du paquet signé, le chargeur d'amorçage et la présence d'un noyau de secours. Un signalement communautaire isolé concernant le démarrage du `-25` n'établit pas une régression générale ; aucune régression généralisée confirmée.

**Six avis du 19/10**, correctif commun **`qemu-server >= 9.2.11`** :

| Avis | Impact / prérequis |
| --- | --- |
| PSA-2026-00069-1 | Mot de passe cloud-init `cipassword` non masqué dans la configuration de snapshot ; accès à la configuration |
| PSA-2026-00070-1 | Lecture de snippets sur stockage non autorisé ; `VM.Config.Cloudinit`, autorisation de démarrer la VM et accès invité |
| PSA-2026-00071-1 | Modification de paramètres d'authentification cloud-init avec `VM.Config.Network`, sans `VM.Config.Cloudinit` |
| PSA-2026-00072-1 | Réponses QEMU Guest Agent non bornées, consommation mémoire hôte ; contrôle d'une VM avec agent |
| PSA-2026-00073-1 | DoS de `pvedaemon` via agent invité malveillant et allocation mémoire |
| PSA-2026-00074-1 | Contrôles insuffisants des commandes QEMU HMP, exposition potentielle de la disposition mémoire ; privilège `Sys.Modify` |

Scores CVSS individuels non publiés dans ces avis : **pas de score inventé**, ni de comptage supplémentaire. Vérifier `pveversion -v`, `uname -r`, `apt policy qemu-server pve-manager` et les permissions API déléguées. Après patch, surveiller les démarrages de VM, QGA, cloud-init et sauvegardes.

Sources :
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-signed-7.0/

## 3. GitLab Self-Managed CE/EE — CRITICAL

**Dernière Critical Patch vérifiée : 21/10/2026**. Versions corrigées par branche : **18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3, 19.5.1**. GitLab recommande une mise à jour immédiate des instances affectées, quel que soit le mode d'installation (Omnibus, source, Helm).

| CVE | CVSS | Versions affectées / vecteur / prérequis / impact |
| --- | ---: | --- |
| **CVE-2026-110264** | **10.0** | Depuis 7.0 avant correctif de branche ; **RCE réseau, PR:N/UI:N**, via compilateur regex ; `AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H`. |
| CVE-2026-15723 | 8.2 | Depuis 10.1 avant 19.3.6, 19.4.3, 19.5.1 ; compte LDAP pris en charge par OmniAuth SAML et auto-link LDAP ; attaquant authentifié, PR:L, UI:N. |
| CVE-2026-112212 | 7.5 | Depuis 18.3 avant 19.3.6, 19.4.3, 19.5.1 ; empoisonnement du cache OAuth/OIDC ; **PR:N**, **UI:R**. |
| CVE-2026-12664 | 6.8 | GraphQL : token `read_api` authentifié pouvant dépasser son périmètre ; PR:L, UI:N. |
| CVE-2026-16255 | 5.9 | EE : contournement de verrou d'appartenance SAML/LDAP ; authentification requise. |
| CVE-2026-105777 | 4.3 | Exposition de credentials internes dans des en-têtes sous conditions ; bulletin décrivant une fuite potentiellement sans authentification, vecteur CVSS publié PR:L : **divergence à clarifier**, ne pas conclure à PR:N sans validation. |
| CVE-2026-13319 | 4.3 | Métadonnées de projets privés lisibles par utilisateur authentifié sans appartenance. |

**Critical Patch du 12/10** (versions initiales corrigées 19.2.8 / 19.3.5 / 19.4.2 ; privilégier celles du 21/10) :

| CVE | CVSS | Impact et prérequis |
| --- | ---: | --- |
| **CVE-2026-97229** | **10.0** | RCE CI/CD via regex ; versions 19.2 < 19.2.8, 19.3 < 19.3.5, 19.4 < 19.4.2 ; **PR:N/UI:N**. |
| CVE-2026-92883 | 8.7 | XSS dans la coloration syntaxique ; utilisateur authentifié, **UI:R**. |
| CVE-2026-16301 | 8.5 | Contournement d'autorisation des pipelines de branches protégées ; rôle Developer requis, UI:N. |
| CVE-2026-14814 | 8.2 | Empoisonnement de cache de fichiers, déclenchement possible de CI/CD et **fuite de variables protégées** ; PR:L. |
| CVE-2026-81839 | 7.7 | EE : pipelines planifiés avec privilèges hérités d'un nouveau groupe ; PR:L, UI:R. |
| CVE-2026-96893 | 7.3 | EE Duo Agent : injection d'instructions, commandes non voulues ; PR:L, UI:R. |
| CVE-2026-101168 | 6.2 | GitLab Pages : fuite de tokens/secrets dans les journaux ; accès privilégié aux logs. |

**Actions P0** : si instance encore en `19.3.2`, cibler au minimum **`19.3.6`** après sauvegarde, contrôle des migrations, runners et intégrations ; auditer les tokens, variables CI/CD, accès aux logs Pages et indicateurs de compromission. Les bulletins GitLab proposent des détections défensives pour les deux RCE.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 4. OpenSSH

**Dernière version amont vérifiée : OpenSSH 10.6p1 du 06/10/2026.** La publication traite plusieurs correctifs de sécurité (notamment SFTP/GSSAPI/compression) et une dépréciation progressive de `scp -R`. Aucun nouvel avis daté du 26/10 confirmé. Sur Rocky/RHEL, **vérifier les errata rétroportés** : une version RPM inférieure à 10.6p1 ne prouve pas une vulnérabilité.

Contrôles : `ssh -V`, `rpm -q openssh-server`, `dnf updateinfo list --security`, configuration SSH effective, exposition réseau et méthodes d'authentification.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx

**Versions corrigées vérifiées : 1.30.6+ et 1.31.7+**. Cinq avis de gravité éditeur **medium** :

| CVE | Fonctionnalité / risque |
| --- | --- |
| CVE-2026-15176 | Buffer overflow avec `alias` et `rewrite` |
| CVE-2026-93479 | Buffer overflow dans l'évaluation de scripts |
| CVE-2026-95101 | Injection de corps de requête HTTP/3 |
| CVE-2026-97629 | Falsification de session via tickets TLS |
| CVE-2026-97205 | Use-after-free avec AIO |

Exposition dépendante des modules, protocoles et directives activés. CVSS numériques individuels et conditions complètes non confirmés ici ; ne pas qualifier arbitrairement de RCE sans authentification. Vérifier `nginx -V`, `nginx -T`, les paquets et backports, puis tester la configuration avant redémarrage.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB

**Branche 10.11 LTS : 10.11.19** (sortie 24/08/2026), dernière maintenance de cette branche vérifiée dans les notes officielles consultées. Correctifs CVE publiés a posteriori :

| CVE | CVSS | Correctif |
| --- | ---: | --- |
| CVE-2026-92262 | 9.9 | 10.11.19 |
| CVE-2026-85746 | 9.9 | 10.11.19 |
| CVE-2026-97380 | 8.8 | 10.11.19 |
| CVE-2026-107815 | 8.5 | 10.11.19 |
| CVE-2026-107818 | 8.4 | 10.11.19 |
| CVE-2026-107814 | 8.4 | 10.11.19 |
| CVE-2026-107821 | 8.0 | 10.11.19 |
| CVE-2026-102523 | 8.0 | 10.11.19 |
| CVE-2026-86047 | 7.7 | 10.11.19 |
| CVE-2026-107823 | 7.2 | 10.11.19 |

**Vecteur, authentification, privilèges, interaction :** les notes globales donnent CVE/CVSS/version corrigée mais pas les vecteurs complets de chaque CVE ; **non vérifiés**. Un CVSS élevé ne prouve pas une RCE réseau sans authentification. Examiner séparément les moteurs de stockage, CONNECT, Galera, TLS, réplication et la configuration des comptes.

Actions : vérifier `SELECT VERSION();`, provenance des RPM, `dnf updateinfo`, droits des comptes et exposition du port SQL.

Sources :
- https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19
- https://mariadb.com/docs/server/security/cve/community-server

## Priorités opérationnelles

1. **P0** : mettre à jour GitLab et contrôler les deux RCE, pipelines, variables protégées et secrets.
2. **P0** : corriger les hyperviseurs Proxmox/RHEL exposés à PwnEscape ; confirmer le noyau actif après redémarrage.
3. **P1** : intégrer le **nouveau kernel Rocky 687.60.1** dans la qualification, sans lui attribuer de CVE non vérifiées ; contrôler les régressions.
4. **P1** : vérifier `qemu-server >= 9.2.11`, Secure Boot et les privilèges Proxmox.
5. **P1** : maintenir Nginx, MariaDB et OpenSSH sur leurs correctifs de sécurité, y compris backports.

**Rapport exclusivement défensif, sans instruction offensive.**
