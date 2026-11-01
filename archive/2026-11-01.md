---
type: mco-daily
date: 2026-11-01
generated_at: 2026-11-01T08:01:23+01:00
timezone: Europe/Paris
status: critical
critical_cve: 5
important_cve: 16
rocky_kernel_latest: "5.14.0-687.61.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.24"
---

# Brief MCO défensif — dimanche 1er novembre 2026

## Résumé exécutif
- **P0 NOUVEAU — Kernel Linux IPv4 TCP : CVE-2026-98432, CVSS 9.8**. Red Hat décrit un use-after-free exploitable **à distance sans authentification ni interaction**, pouvant provoquer un DoS et potentiellement une RCE. **Proxmox** a publié l'avis PSA-2026-00080 le 31 octobre : correctif depuis **7.0.14-25-pve**. Pour **RHEL/Rocky 9**, aucun RPM explicitement attesté comme correctif de cette CVE n'a été confirmé pendant cette collecte : ne pas assimiler « kernel récent » à « CVE corrigée ».
- **P0 NOUVEAU — Proxmox : six avis PSA-2026-00075 à 00080 publiés les 30–31 octobre**. Notamment **élévation de privilèges root** via API réseau (PSA-00077) et exécution root via lien piégé ouvert par **root@pam** (PSA-00079). Contrôler **pve-manager >= 9.2.24**, **pve-xtermjs >= 6.0.0-3** et le kernel.
- **P0 GitLab :** deux RCE sans authentification CVSS 10.0 ; mise à jour du 29 octobre contre **CVE-2026-111943** (certificats eMudhra compromis) : corriger serveur, Runner, AI Gateway si utilisé et images CI/CD.
- **P1 Rocky/RHEL 9 :** kernel BaseOS **5.14.0-687.61.1.el9_8**, erratum **RHSA-2026:90365** (15 CVE). Un paquet **5.14.0-687.61.1.el9_8.0.1** est apparu le 30 octobre dans le dépôt **Rocky 9.8 security**, distinct de BaseOS ; la correspondance exacte avec CVE-2026-98432 n'est pas confirmée.
- **P1 OpenSSH, Nginx, MariaDB :** maintenir les correctifs des branches utilisées ; vérifier les backports RPM et les fonctionnalités réellement exposées.

## Méthode, sévérité et dédoublonnage
Les compteurs du front matter représentent les **CVE distinctes explicitement présentes dans les tableaux de comptage ci-dessous**, pas seulement les nouveautés du jour ni un inventaire exhaustif. Une CVE affectant plusieurs produits est comptée **une seule fois**. Seuils **CVSS v3.1 : critical >= 9.0 ; important >= 7.0 et < 9.0**. Pour PwnEscape, retenir **8.8 selon Red Hat** (score CNA différent : 9.3). Les CVE sans score numérique vérifié ne sont pas comptées, mais restent décrites et priorisées. Les scores de GitLab sont ceux des bulletins de sécurité des 12/21 octobre et ceux de MariaDB proviennent des notes 10.11.19.

### CVE critiques comptées (5)
| CVE | CVSS v3.1 | Produit | Risque / composant |
| --- | ---: | --- | --- |
| CVE-2026-98432 | 9.8 | Kernel Linux, RHEL/Proxmox | UAF IPv4 TCP, réseau sans authentification, RCE possible / DoS; PSA-2026-00080 |
| CVE-2026-110264 | 10.0 | GitLab CE/EE | RCE sans authentification, regexp |
| CVE-2026-97229 | 10.0 | GitLab CE/EE CI/CD | RCE sans authentification, regexp CI/CD |
| CVE-2026-92262 | 9.9 | MariaDB 10.11 | Correctif 10.11.19 |
| CVE-2026-85746 | 9.9 | MariaDB 10.11 | Correctif 10.11.19 |

### CVE importantes comptées (16)
| CVE | CVSS v3.1 | Produit | Risque / composant |
| --- | ---: | --- | --- |
| CVE-2026-15723 | 8.2 | GitLab | Compte LDAP / SAML auto-link |
| CVE-2026-112212 | 7.5 | GitLab | Empoisonnement OAuth/OIDC |
| CVE-2026-92883 | 8.7 | GitLab | XSS coloration syntaxique |
| CVE-2026-16301 | 8.5 | GitLab | Contournement pipelines protégés |
| CVE-2026-14814 | 8.2 | GitLab | Cache et secrets CI/CD |
| CVE-2026-81839 | 7.7 | GitLab EE | Pipelines exécutés avec privilèges d'un autre groupe |
| CVE-2026-96893 | 7.3 | GitLab EE | Injection d'instructions Duo Agent |
| CVE-2026-99999 | 8.8 | Kernel RHEL/Proxmox | PwnEscape, sortie de VM; score Red Hat |
| CVE-2026-107823 | 7.2 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107821 | 8.0 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107818 | 8.4 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107815 | 8.5 | MariaDB | Correctif 10.11.19 |
| CVE-2026-107814 | 8.4 | MariaDB | Correctif 10.11.19 |
| CVE-2026-102523 | 8.0 | MariaDB | Correctif 10.11.19 |
| CVE-2026-97380 | 8.8 | MariaDB | Correctif 10.11.19 |
| CVE-2026-86047 | 7.7 | MariaDB | Correctif 10.11.19 |

## 1. ALERTE CRITIQUE — CVE-2026-98432, kernel IPv4 TCP
**Source Red Hat : RHSB-2026-016 du 30 octobre**, CVSS **9.8**, vecteur **CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H**. Une condition de concurrence lors de connexions IPv4 TCP peut provoquer un use-after-free ; attaquant **réseau**, **sans authentification**, **sans privilèges**, **sans interaction**. Red Hat évoque **DoS, corruption mémoire et RCE potentielle** ; la fiabilité d'une RCE n'est pas établie. L'attaque concerne des chemins impliquant options IPv4 et migrations de requêtes ; IPv6 natif n'est pas affecté selon Proxmox.

**Proxmox : PSA-2026-00080-1**, avis du **31 octobre**, corrigé dans **proxmox-kernel-7.0.14-25-pve(-signed)**. Le noyau **7.0.14-26-pve** identifié en no-subscription est postérieur au correctif ; confirmer qu'il est effectivement démarré. Le kernel **7.0.14-24-pve** corrigeait PwnEscape mais **pas encore cette nouvelle CVE**.

**RHEL/Rocky :** Red Hat indique RHEL 9 affecté et une publication de correctifs accélérée ; aucun erratum attribuant explicitement un RPM Rocky/RHEL 9 à la correction de CVE-2026-98432 n'a été confirmé pendant cette collecte. Le dépôt Rocky BaseOS contient **5.14.0-687.61.1.el9_8** (27 octobre) et le dépôt séparé **9.8/security** contient **5.14.0-687.61.1.el9_8.0.1** (30 octobre). **Ne pas présumer que l'un de ces paquets corrige CVE-2026-98432 sans avis officiel ou changelog probant.** Vérifier les errata, évaluer l'exposition des services TCP IPv4 et suivre les mesures de réduction de surface recommandées par Red Hat, en tenant compte du risque opérationnel. Ne pas déployer de filtrage sans test.

Sources :
- https://access.redhat.com/security/vulnerabilities/RHSB-2026-016
- https://access.redhat.com/security/cve/cve-2026-98432
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://download.rockylinux.org/pub/rocky/9.8/security/x86_64/os/Packages/k/

## 2. Proxmox VE 9.2 — six avis publiés les 30 et 31 octobre
Versions vérifiées dans les index officiels **pve-no-subscription** : **pve-manager 9.2.24** (paquet de gestion), **proxmox-kernel-7.0 7.0.14-26** (noyau **7.0.14-26-pve**) et **qemu-server 9.2.13**. **Ce sont des paquets différents** ; un pve-manager récent ne garantit pas un kernel corrigé ou démarré.

| Avis | Date | Risque et prérequis | Correctif |
| --- | --- | --- | --- |
| PSA-2026-00075 | 30/10 | Validation insuffisante de paramètres URL xterm.js ; **requêtes POST au nom d'une victime** qui ouvre un lien piégé (**UI requise**). | **pve-xtermjs >= 6.0.0-3** |
| PSA-2026-00076 | 30/10 | Lecture d'informations de pool sans privilège **Pool.Audit** ; fuite de métadonnées. | **pve-manager >= 9.2.23** |
| PSA-2026-00077 | 30/10 | Injection de lignes dans /etc/network/interfaces via API ; **élévation root / commandes root** pour utilisateur ou token ayant **Sys.Modify** sur /nodes/{node}. **PR:L, pas une RCE anonyme**. | **pve-manager >= 9.2.23** |
| PSA-2026-00078 | 30/10 | XSS dans **consent-text** datacenter ; attaquant initialement très privilégié. | **pve-manager >= 9.2.24** |
| PSA-2026-00079 | 30/10 | Mauvais échappement des options de commande Ceph/xtermjs ; **exécution root** si une victime connectée **root@pam** ouvre un lien piégé (**UI:R**). | **pve-manager >= 9.2.24** |
| PSA-2026-00080 / CVE-2026-98432 | 31/10 | UAF IPv4 TCP, **AV:N/PR:N/UI:N**, DoS / RCE potentielle, CVSS Red Hat **9.8**. | **kernel >= 7.0.14-25-pve(-signed)** |

Les cinq premiers avis ne comportent pas de CVSS individuel vérifié : **non comptés**. La CVE-2026-98432 est comptée une fois dans le tableau critique, même si elle touche aussi RHEL/Rocky.

**Autres correctifs Proxmox encore à contrôler :** PSA-2026-00068 (**PwnEscape, CVE-2026-99999**, sortie de VM, kernel corrigé depuis **7.0.14-24-pve**) ; PSA-2026-00069 à **00074** du 19 octobre (**cloud-init cipassword/snippets/ACL, QEMU Guest Agent DoS, HMP permissions**), corrigés par **qemu-server >= 9.2.11**. Les prérequis dépendent des avis (droits de VM, accès invité, agent activé). Sous Secure Boot, installer le kernel signé ; conserver un noyau de secours et qualifier les régressions avant reboot. Aucun défaut généralisé de démarrage du -26 n'est confirmé.

Contrôles défensifs : versions effectives de **pve-manager**, **pve-xtermjs**, **qemu-server** et du **kernel démarré** ; permissions **Sys.Modify**, **Pool.Audit**, **VM.Config.Cloudinit**, **VM.Config.Network** ; journaux d'administration et sauvegardes.

Sources :
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/q/qemu-server/

## 3. GitLab Self-Managed CE/EE — CRITICAL
**Dernière Security Update officielle vérifiée : 29 octobre 2026**, **CVE-2026-111943**, confiance indue envers **huit certificats intermédiaires eMudhra aux clés privées compromises**. **Versions CE/EE corrigées : 18.11.14, 19.0.11, 19.1.10, 19.2.10, 19.3.7, 19.4.5, 19.5.3** ; toutes les versions antérieures de ces branches sont affectées. Score CVSS individuel non confirmé dans le bulletin, donc exclu des compteurs. L'impact dépend de l'utilisation de certificats et de la chaîne de confiance TLS ; **ce n'est pas une nouvelle RCE GitLab**.

**Runner self-hosted >= 19.5.1** à mettre à jour **séparément** ; reconstruire les images CI/CD qui embarquent leur propre magasin **ca-certificates**. **AI Gateway** si auto-hébergé : correctifs **18.11.8, 19.0.13, 19.1.9, 19.2.5, 19.3.3, 19.4.3, 19.5.3** selon branche. Les versions **19.5.3 et 19.4.5** incluent des migrations susceptibles d'imposer un arrêt sur installation mono-nœud.

**Deux RCE antérieures sans authentification** :
- **CVE-2026-110264 (10.0)** : overflow dans compilateur d'expressions régulières ; **AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H**, RCE serveur. Affecte les versions 7.0 avant 18.11.13 et les branches **19.0 < 19.0.10**, **19.1 < 19.1.9**, **19.2 < 19.2.9**, **19.3 < 19.3.6**, **19.4 < 19.4.3**, **19.5 < 19.5.1**.
- **CVE-2026-97229 (10.0)** : regexp dans configuration **CI/CD**, **AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H**, RCE serveur sans interaction ; **19.2 < 19.2.8**, **19.3 < 19.3.5**, **19.4 < 19.4.2**.

**Autres vulnérabilités ciblées** : **CVE-2026-15723 (8.2)**, prise de compte LDAP si SAML et liaison automatique (authentification requise) ; **CVE-2026-112212 (7.5)**, empoisonnement OAuth/OIDC (**PR:N/UI:R**) ; **CVE-2026-16301 (8.5)**, contournement des pipelines protégés par Developer (**PR:L**) ; **CVE-2026-14814 (8.2)**, risque d'exposition de secrets CI/CD via cache (**PR:L**) ; **CVE-2026-81839 (7.7)**, pipelines EE avec privilèges d'un autre groupe (**PR:L/UI:R**) ; **CVE-2026-96893 (7.3)**, injection d'instructions Duo Agent (**PR:L/UI:R**) ; **CVE-2026-92883 (8.7)**, XSS coloration syntaxique (**PR:L/UI:R**) ; **CVE-2026-105777 (4.3)**, credentials internes dans les en-têtes ; **CVE-2026-101168 (6.2)**, secrets dans logs GitLab Pages (accès préalable aux logs) ; **CVE-2026-12664 (6.8)**, portée de token read_api contournée. Pour ces dernières, vérifier les versions et vecteurs par bulletin avant toute conclusion sur l'exploitabilité.

**Action** : toute instance 19.3.2 doit être mise à niveau vers **19.3.7 au minimum sur cette branche**, ou vers une branche maintenue plus récente après sauvegarde et vérification des chemins d'upgrade. Auditer runners, variables protégées, tokens, secrets, SAML/LDAP, pipelines et journaux.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-3-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/

## 4. Rocky Linux / RHEL 9 — autres errata kernel
**RHSA-2026:90365**, émis le **26 octobre 2026**, classé **Important** par Red Hat, pour **RHEL 9.8 EUS** ; paquet Rocky BaseOS x86_64 **5.14.0-687.61.1.el9_8** observé le **27 octobre**. Quinze CVE : **CVE-2024-57974, CVE-2026-53388, CVE-2026-63978, CVE-2026-63979, CVE-2026-64266, CVE-2026-64523, CVE-2026-68476, CVE-2026-72242, CVE-2026-74515, CVE-2026-80824, CVE-2026-89638, CVE-2026-89777, CVE-2026-93262, CVE-2026-98222, CVE-2026-98320**. Composants : UDP, FUSE, net/handshake, IPVS, SELinux, KVM s390, USB, SMB, VFIO, RAID5, KEYS, netfilter. Scores individuels non tous vérifiés : exclus des compteurs.

**PwnEscape CVE-2026-99999** : score Red Hat **8.8** (**AV:L/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:H**) contre **9.3 CNA** ; sortie de VM possible, pas une RCE réseau anonyme. L'erratum précédent concernait ce défaut ; contrôler le statut exact du correctif par branche et l'installation effective.

**NOUVEAU :** le paquet **5.14.0-687.61.1.el9_8.0.1** est visible dans le dépôt séparé **Rocky 9.8/security** depuis le 30 octobre. Ce dépôt et BaseOS sont distincts : **ne pas déclarer la version .0.1 comme disponible sur tous les hôtes**. Aucun lien explicite entre cette build et la correction de CVE-2026-98432 n'a été vérifié. Contrôler **dnf updateinfo**, **dnf repolist**, **rpm -q kernel-core**, **uname -r** et l'espace **/boot** avant toute mise à jour ; prévoir redémarrage.

Sources :
- https://access.redhat.com/errata/RHSA-2026:90365
- https://access.redhat.com/security/cve/cve-2026-99999
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://download.rockylinux.org/pub/rocky/9.8/security/x86_64/os/Packages/k/

## 5. OpenSSH
Version amont vérifiée **OpenSSH 10.6/10.6p1 du 6 octobre 2026**. Les notes décrivent des durcissements/corrections touchant SFTP, GSSAPI, compression, restrictions de noms d'utilisateur et compatibilité. Pas de nouveau CVSS individuel vérifié pour ce périmètre depuis la dernière collecte. Sur RHEL/Rocky, privilégier **errata et backports** : un RPM affichant une version amont plus ancienne peut être corrigé. Vérifier l'exposition SSH, authentification et règles d'accès.

Source : https://www.openssh.org/txt/release-10.6

## 6. Nginx
Cinq avis officiels **medium** : **CVE-2026-15176** (alias/rewrite, overflow), **CVE-2026-93479** (évaluation de scripts, overflow), **CVE-2026-95101** (injection corps HTTP/3), **CVE-2026-97629** (tickets TLS, falsification de session), **CVE-2026-97205** (AIO, use-after-free). Correctifs annoncés **1.30.6+ et 1.31.7+**. Les plages affectées et l'exploitabilité dépendent des directives/modules activés. Scores CVSS v3.1 individuels non vérifiés : exclus des compteurs. Contrôler le paquet et les correctifs rétroportés.

Source : https://nginx.org/en/security_advisories.html

## 7. MariaDB
**MariaDB Community Server 10.11.19 LTS**, publiée le **24 août 2026**, comprend **2 CVE critiques CVSS 9.9** (**CVE-2026-92262, CVE-2026-85746**) et **8 importantes CVSS 7.2–8.8** documentées dans les tableaux de comptage. Le bulletin contient aussi d'autres CVE de score inférieur à 7. Les prérequis, privilèges, vecteurs et interactions ne sont pas uniformes : **ne pas conclure à une RCE sans authentification à partir du score seul**. Vérifier versions, CONNECT, Galera, réplication, sauvegardes et paquets de distribution.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Plan d'action défensif
1. **P0 — CVE-2026-98432 :** auditer immédiatement les hôtes Linux acceptant des connexions IPv4 TCP ; sur Proxmox, installer et **démarrer** le kernel >= 7.0.14-25-pve signé si nécessaire ; sur Rocky/RHEL, suivre l'erratum officiel de correction avant d'affirmer une remédiation.
2. **P0 — Proxmox PSA-00075 à 00079 :** mettre à jour pve-manager et pve-xtermjs, revoir les droits **Sys.Modify** et les accès **root@pam**, qualifier la chaîne de démarrage et les éventuelles régressions.
3. **P0 — GitLab :** corriger serveur et Runner séparément, renouveler les magasins CA des images CI/CD, contrôler secrets, tokens et intégrations.
4. **P1 — Rocky/RHEL :** appliquer les correctifs kernel vérifiés et redémarrer après contrôle de **/boot**.
5. **P1 — Proxmox qemu-server :** maintenir >= 9.2.11 ; surveiller cloud-init, Guest Agent, HMP et délégations de privilèges.
6. **P1 — OpenSSH/Nginx/MariaDB :** maintenir les branches supportées et les backports fournisseur.

**Limites :** versions vérifiées dans des dépôts publics, pas sur les serveurs de production ; disponibilité et présence d'un correctif CVE précis doivent être confirmées par avis fournisseur. Aucune procédure offensive.
