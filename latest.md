---
type: mco-daily
date: 2026-10-27
generated_at: 2026-10-27T07:57:15+01:00
timezone: Europe/Paris
status: critical
critical_cve: 4
important_cve: 16
rocky_kernel_latest: "5.14.0-687.60.1.el9_8"
proxmox_major: "9.2"
proxmox_pve_manager_latest: "9.2.21"
---

# Brief MCO défensif — mardi 27 octobre 2026

## Résumé exécutif
- **P0 GitLab CE/EE** : deux RCE critiques sans authentification, CVSS 10.0, corrigées par les Critical Patch Releases des **12 et 21 octobre**. Contrôler en priorité les installations Self-Managed et leurs pipelines CI/CD.
- **P0 Hyperviseurs** : **PwnEscape CVE-2026-99999** permet une sortie de VM KVM. Proxmox corrigé à partir de `7.0.14-24-pve`; correctif RHEL 9 dans RHSA-2026:86033.
- **P1 Proxmox** : six avis du 19 octobre corrigés dans `qemu-server >= 9.2.11` (mots de passe cloud-init, droits, QEMU Guest Agent et DoS).
- **P1 Rocky** : kernel `5.14.0-687.60.1.el9_8` identifié dans BaseOS x86_64, daté du 23 octobre ; l'erratum propre à ce build n'est pas établi.
- **P1 Nginx / MariaDB** : correctifs de sécurité disponibles ; comparer les RPM installés aux correctifs du distributeur.

**Nouveautés du 27 octobre :** aucune nouvelle publication datée du jour n'a pu être confirmée sur les sources officielles consultées. Le risque non corrigé des alertes antérieures reste élevé ; cela ne signifie pas que les serveurs sont exposés sans vérification de leur état.

## Méthode, limites et comptage des CVE
Périmètre de comptage : CVE **distinctes, explicitement identifiées avec CVSS numérique vérifié** dans les deux Critical Patch GitLab des 12 et 21 octobre, l'avis RHEL/Proxmox PwnEscape et les notes MariaDB 10.11.19. Une CVE commune à plusieurs produits n'est comptée qu'une fois. Seuils : **critical : CVSS >= 9.0** ; **important : 7.0 <= CVSS < 9.0**. Les autres CVE, notamment Nginx sans score numérique confirmé, ne sont pas incluses. Les nombres ne désignent **ni les seules nouvelles CVE du jour ni toutes les CVE existantes**.

**4 critiques :**

| CVE | CVSS | Source |
| --- | ---: | --- |
| CVE-2026-110264 | 10.0 | GitLab 21/10 |
| CVE-2026-97229 | 10.0 | GitLab 12/10 |
| CVE-2026-92262 | 9.9 | MariaDB 10.11.19 |
| CVE-2026-85746 | 9.9 | MariaDB 10.11.19 |

**16 importantes :**

| CVE | CVSS | Source |
| --- | ---: | --- |
| CVE-2026-15723 | 8.2 | GitLab 21/10 |
| CVE-2026-112212 | 7.5 | GitLab 21/10 |
| CVE-2026-92883 | 8.7 | GitLab 12/10 |
| CVE-2026-16301 | 8.5 | GitLab 12/10 |
| CVE-2026-14814 | 8.2 | GitLab 12/10 |
| CVE-2026-81839 | 7.7 | GitLab 12/10 |
| CVE-2026-96893 | 7.3 | GitLab 12/10 |
| CVE-2026-99999 | 8.8 | RHEL 9 / Proxmox |
| CVE-2026-107823 | 7.2 | MariaDB 10.11.19 |
| CVE-2026-107821 | 8.0 | MariaDB 10.11.19 |
| CVE-2026-107818 | 8.4 | MariaDB 10.11.19 |
| CVE-2026-107815 | 8.5 | MariaDB 10.11.19 |
| CVE-2026-107814 | 8.4 | MariaDB 10.11.19 |
| CVE-2026-102523 | 8.0 | MariaDB 10.11.19 |
| CVE-2026-97380 | 8.8 | MariaDB 10.11.19 |
| CVE-2026-86047 | 7.7 | MariaDB 10.11.19 |

Pour PwnEscape, le score **Red Hat 8.8** est retenu (la notation CNA peut être différente). Les six avis Proxmox sur qemu-server n'ont pas de CVSS numérique confirmé : ils ne sont pas comptés. Ne pas déduire les prérequis d'une CVE de son seul score.

## 1. GitLab Self-Managed CE/EE — CRITICAL
**Critical Patch du 21/10/2026 :** versions correctives `18.11.13`, `19.0.10`, `19.1.9`, `19.2.9`, `19.3.6`, `19.4.3`, `19.5.1`.

| CVE | CVSS | Vecteur / prérequis | Impact et plage affectée |
| --- | ---: | --- | --- |
| CVE-2026-110264 | 10.0 | AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H | **RCE serveur sans authentification ni interaction** dans le compilateur d'expressions régulières. GitLab CE/EE depuis 7.0, jusqu'au correctif propre à chaque branche. |
| CVE-2026-15723 | 8.2 | AV:N/AC:H/PR:L/UI:N | **Prise de contrôle de compte LDAP** par un utilisateur authentifié si OmniAuth SAML et liaison LDAP automatique sont activés ; depuis 10.1 avant les correctifs des branches 19.3/19.4/19.5. |
| CVE-2026-112212 | 7.5 | AV:N/AC:H/PR:N/UI:R | **Empoisonnement de cache OAuth/OIDC** sans authentification initiale, avec interaction ultérieure ; depuis 18.3 avant les correctifs. |
| CVE-2026-12664 | 6.8 | Authentifié, token read_api | Contournement du périmètre du token via GraphQL ; depuis 18.2 avant correctif. |
| CVE-2026-16255 | 5.9 | Authentifié, EE avec verrou SAML/LDAP | Contournement de restrictions d'appartenance ; depuis 12.0 avant correctif. |
| CVE-2026-105777 | 4.3 | **Ambiguïté dans l'avis** : description « unauthenticated » mais vecteur CVSS PR:L | Exposition possible de credentials internes via en-têtes ; depuis 17.6.5 avant correctif. **Ne pas conclure à PR:N sans clarification de l'éditeur.** |
| CVE-2026-13319 | 4.3 | Authentifié | Métadonnées de projets privés accessibles sans appartenance. |

Autres CVE de la même publication : CVE-2026-112378 (4.3, fonctions IA), CVE-2026-105711 (3.5, fuite via cache), CVE-2026-14288 (3.1, politique d'approbation), CVE-2026-112326 (3.1, suppression de constats), CVE-2026-103515 (2.7, verrou IA). Les conditions exactes et versions affectées sont détaillées dans le bulletin.

**Critical Patch du 12/10/2026 :** `19.2.8`, `19.3.5`, `19.4.2` (supplantées sur les branches suivies par les correctifs du 21 octobre).
- **CVE-2026-97229 (10.0)** : **RCE CI/CD sans authentification ni interaction**, `AV:N/AC:L/PR:N/UI:N` ; versions 19.2 avant 19.2.8, 19.3 avant 19.3.5, 19.4 avant 19.4.2.
- **CVE-2026-92883 (8.7)** : XSS dans la coloration syntaxique, authentification faible, interaction de la victime ; depuis 19.1.
- **CVE-2026-16301 (8.5)** : Developer authentifié pouvant déclencher des pipelines sur branches protégées ; depuis 18.7.
- **CVE-2026-14814 (8.2)** : cache de fichiers source empoisonné, **risque d'exécution dans CI/CD et d'exposition de variables protégées**, privilèges faibles, complexité élevée.
- **CVE-2026-81839 (7.7, EE)** : exécution de pipelines via politique de planification, utilisateur authentifié et interaction.
- **CVE-2026-96893 (7.3, EE)** : injection d'instructions dans GitLab Duo Agent Platform, authentification et interaction.
- **CVE-2026-101168 (6.2)** : tokens et secrets dans les journaux GitLab Pages, accès privilégié aux logs.
- Plusieurs autres défauts d'autorisation GraphQL permettent, sous conditions, la lecture de données privées sans authentification (notamment CVE-2026-16275, CVE-2026-84284, CVE-2026-85529, chacun 5.3).

**Action P0 :** vérifier la version installée. Si elle est toujours `19.3.2`, passer **au minimum à 19.3.6** en suivant le chemin de migration GitLab. Sauvegarder, prévoir l'indisponibilité d'une instance mono-nœud pendant les migrations, contrôler runners, tokens, variables protégées et journaux. Le bulletin du 21 octobre signale des migrations régulières pour 19.3.6/19.4.3 et des migrations post-déploiement pour 19.4.3.

Sources officielles :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-5-1-released/
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- https://docs.gitlab.com/policy/maintenance/

## 2. Rocky Linux / RHEL 9 — kernel et errata
Le dépôt officiel **Rocky 9 BaseOS x86_64** expose `kernel-core-5.14.0-687.60.1.el9_8`, daté du **23/10/2026 à 18:54 UTC**. Dernière version vérifiée sur ce dépôt : **`5.14.0-687.60.1.el9_8`**. Les corrections précises entre `687.59.1` et `687.60.1` ne sont pas attribuées ici à un nouvel erratum : **à vérifier**, sans inventer de CVE supplémentaires.

**CVE-2026-99999 (PwnEscape)** : corruption mémoire dans le traitement des métadonnées de checksum IPv4 ; un invité VM non fiable ou un attaquant local peut compromettre la mémoire hôte (exécution de code, DoS ou divulgation). Red Hat classe le problème **Important, CVSS 8.8** et indique RHEL 9 `kernel` **corrigé par RHSA-2026:86033 du 20/10/2026**. Vecteur local, privilèges faibles, pas d'interaction dans l'évaluation rapportée. Le score Red Hat est celui du compteur, pas une garantie d'absence d'exploitation.

Contrôles : `dnf updateinfo list --security`, `dnf list --showduplicates kernel-core`, `rpm -q kernel-core`, `uname -r`. Déployer après validation des modules tiers et du réseau, puis redémarrer pour activer le nouveau noyau. Les correctifs RPM backportés ne se déduisent pas du seul numéro de version amont.

Sources :
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://access.redhat.com/security/cve/cve-2026-99999
- https://access.redhat.com/errata/RHSA-2026:86033

## 3. Proxmox VE 9.2 — kernel ≠ pve-manager
- **Branche Proxmox VE vérifiée : 9.2**, annoncée officiellement le **21/05/2026**.
- **`pve-manager` : 9.2.21**, dernier changelog observé dans le répertoire officiel **pve-no-subscription** au moment de cette collecte. Un index de changelogs n'est pas une garantie de disponibilité sur tous les miroirs ou dans `pve-enterprise`.
- **Kernel Proxmox :** `7.0.14-25-pve` signalé disponible, mais **le caractère « dernier kernel de tous les dépôts » n'a pas été établi**. Le correctif de sécurité confirmé est **`proxmox-kernel-7.0.14-24-pve(-signed)`** pour CVE-2026-99999, PSA-2026-00068-1 du 16 octobre. Ne pas confondre `pve-manager 9.2.21` avec le noyau `7.0.14-25-pve`.

**Six avis du 19/10/2026, correctif commun `qemu-server >= 9.2.11` :**

| Avis | Impact | Conditions |
| --- | --- | --- |
| PSA-2026-00069-1 | Mot de passe cloud-init non masqué dans la configuration de snapshot | Accès à la configuration de snapshot |
| PSA-2026-00070-1 | Lecture de snippets d'un stockage non autorisé | `VM.Config.Cloudinit`, démarrage VM et accès invité |
| PSA-2026-00071-1 | Modification de paramètres cloud-init d'authentification | `VM.Config.Network` ; effet généralement au premier boot ou après changement d'instance-id |
| PSA-2026-00072-1 | Épuisement mémoire hôte via réponses Guest Agent | Contrôle d'une VM avec Guest Agent |
| PSA-2026-00073-1 | DoS `pvedaemon` par lecture de fichier Guest Agent | Agent invité malveillant |
| PSA-2026-00074-1 | Divulgation potentielle de mémoire via commandes HMP | Privilège `Sys.Modify` insuffisamment restreint |

Scores CVSS individuels non publiés dans ces avis ; **non comptés**. Contrôler aussi les autres avis d'octobre concernant l'import OVA, la restauration VMA et les accès aux sauvegardes, selon les fonctions activées.

**Régression / compatibilité :** un témoignage communautaire du **23 octobre** décrit un échec de démarrage avec `7.0.14-25-pve` et Secure Boot lorsque le paquet signé n'était pas installé. **Signalement isolé, pas de régression générale confirmée.** Avant redémarrage, vérifier `mokutil --sb-state`, `dpkg -l 'proxmox-kernel*'`, `proxmox-boot-tool status` et la présence du paquet `-signed`. Vérifier `pveversion -v` et `uname -r` après maintenance. Le noyau corrigé doit être effectivement démarré.

Sources :
- https://proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-4
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/page-5
- https://forum.proxmox.com/threads/shim-failure-7-0-14-25.187228/

## 4. OpenSSH
**Dernière version amont vérifiée : 10.6p1**, publiée le **06/10/2026**. Les notes signalent des corrections de sécurité et changements de comportement, notamment autour de SFTP, GSSAPI, compression et `scp -R` (dépréciation progressive). Aucun nouvel avis du 27 octobre confirmé. Les CVSS numériques et CVE spécifiques ne sont pas ajoutés sans bulletin vérifié. Sur Rocky/RHEL, rechercher les errata RPM rétroportés, vérifier l'authentification SSH et les changements de compatibilité avant déploiement.

Source : https://www.openssh.com/releasenotes.html

## 5. Nginx
Cinq vulnérabilités de gravité **medium selon l'éditeur**, corrigées dans **1.30.6+ / 1.31.7+** :

| CVE | Fonctionnalité / risque | Versions affectées selon l'éditeur |
| --- | --- | --- |
| CVE-2026-15176 | Débordement mémoire avec `alias` et `rewrite` | 0.1.29–1.31.6 |
| CVE-2026-93479 | Débordement mémoire dans l'évaluation de scripts | 1.30.0–1.30.5, 1.31.2–1.31.6 |
| CVE-2026-95101 | Injection de corps de requête HTTP/3 | 1.25.0–1.31.6 |
| CVE-2026-97629 | Falsification de session avec tickets TLS | 1.23.2–1.31.6 |
| CVE-2026-97205 | Use-after-free avec AIO | 0.8.11–1.31.6 |

CVSS numériques et vecteurs complets non vérifiés : non comptés. L'exposition réelle dépend de la configuration et des modules. Contrôler `nginx -V`, `nginx -T`, les paquets et backports, notamment si HTTP/3, AIO ou tickets TLS sont actifs.

Source : https://nginx.org/en/security_advisories.html

## 6. MariaDB 10.11 LTS
**Version de maintenance vérifiée : 10.11.19, publiée le 24/08/2026**. Les notes officielles recensent **21 CVE** corrigées, dont deux critiques (9.9) et huit importantes (7.0–8.9) selon le barème ci-dessus. Les dix autres ne sont pas comptées dans les deux catégories. Les vulnérabilités documentées touchent diverses fonctions ; les prérequis d'authentification, privilèges et interactions **ne sont pas tous précisés dans la liste de la release**. Ne pas conclure à une RCE réseau sans authentification à partir d'un CVSS élevé.

CVE importantes dans 10.11.19 : CVE-2026-107823 (7.2), CVE-2026-107821 (8.0), CVE-2026-107818 (8.4), CVE-2026-107815 (8.5), CVE-2026-107814 (8.4), CVE-2026-102523 (8.0), CVE-2026-97380 (8.8), CVE-2026-86047 (7.7). CVE critiques : CVE-2026-92262 (9.9), CVE-2026-85746 (9.9).

Contrôler `SELECT VERSION();`, l'origine des RPM, l'exposition réseau et les options CONNECT/Galera/chiffrement/réplication utilisées. Prévoir sauvegarde et test de restauration avant changement de version.

Source : https://mariadb.com/docs/release-notes/community-server/10.11/10.11.19

## Plan d'action
1. **P0** GitLab : vérifier les versions CE/EE et appliquer les Critical Patches, puis examiner les événements CI/CD et les éventuelles fuites de secrets.
2. **P0** Proxmox / Rocky : corriger PwnEscape, redémarrer les hôtes concernés et vérifier le kernel actif.
3. **P1** Proxmox : mettre `qemu-server` à jour, contrôler les permissions cloud-init et les accès Guest Agent.
4. **P1** Nginx / MariaDB / OpenSSH : vérifier les versions, les fonctionnalités exposées, les correctifs de distribution et les régressions.

Rapport défensif uniquement. Les versions publiées ne prouvent pas l'état des machines ; aucun inventaire des hôtes n'a été interrogé.
