---
type: mco-daily
date: 2026-10-17
timezone: Europe/Paris
status: action-required
---

# Brief MCO — samedi 17 octobre 2026

## Résumé exécutif

- **P0 GitLab** : vérifier sans délai les instances non corrigées face à **CVE-2026-97229 (CVSS 10.0)**, RCE CI/CD sans authentification annoncée dans la Critical Patch Release du 12 octobre.
- **Rocky Linux 9** : kernel `5.14.0-687.58.1.el9_8` signalé disponible le 15 octobre ; vérifier son erratum et ses correctifs exacts avant attribution de CVE.
- **Proxmox VE 9** : dernière version observée sur no-subscription : `7.0.14-24-pve`, `pve-manager 9.2.21` ; aucun successeur confirmé dans le contrôle du 17 octobre.

## GitLab Self-Managed CE/EE — priorités

Dernière Critical Patch identifiée : 12 octobre 2026. Versions correctives : **19.4.2 / 19.3.5 / 19.2.8**. Version fonctionnelle 19.5.0 publiée le 15 octobre. Les plages affectées exactes diffèrent selon les CVE ; consulter le bulletin officiel avant qualification.

| CVE | CVSS | Impact et prérequis |
|---|---:|---|
| **CVE-2026-97229** | **10.0** | **RCE serveur CI/CD**, vecteur réseau, **sans authentification ni interaction** ; branches 19.2 < 19.2.8, 19.3 < 19.3.5, 19.4 < 19.4.2. |
| CVE-2026-92883 | 8.7 | XSS dépôt ; compte à faibles privilèges et interaction victime. |
| CVE-2026-16301 | 8.5 | **Contournement d'autorisation CI/CD** sur branches protégées ; Developer, sans interaction. |
| CVE-2026-14814 | 8.2 | Cache poisoning, risque d'exposition de variables CI/CD protégées ; authentifié, complexité élevée. |
| CVE-2026-81839 | 7.7 | Pipelines privilégiés après transfert de projet ; EE, authentification et interaction. |
| CVE-2026-96893 | 7.3 | Injection d'instructions Duo Agent Platform ; EE, authentification et interaction. |
| CVE-2026-101168 | 6.2 | **Fuite de tokens/secrets** dans journaux Pages ; accès privilégié aux journaux. |
| CVE-2026-7767 | 5.8 | Fuite de token via proxy Kubernetes Agent ; contrôle d'agent, authentification et interaction. |
| CVE-2026-16275 / 84284 / 85529 | 5.3 | **Lecture sans authentification** de données privées/confidentielles via GraphQL. |

**Action P0** : relever la version effective GitLab ; si encore 19.3.2, migrer au minimum vers 19.3.5 après sauvegarde, contrôle des verrouillages DNF, tests runners, pipelines, branches protégées, MR et secrets. Ne pas attendre une montée de version fonctionnelle pour appliquer la correction de sécurité.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- https://docs.gitlab.com/releases/19/gitlab-19-5-released/

## Rocky Linux / RHEL 9 — kernel et errata

**Nouveau paquet observé :** `5.14.0-687.58.1.el9_8` (15 octobre). L'association de ce build à un erratum de sécurité précis et la liste des CVE nouvellement corrigées restent **à confirmer** ; ne pas lui attribuer automatiquement les correctifs du build précédent.

Le précédent kernel `5.14.0-687.57.1.el9_8` est associé à **RHSA-2026:81552** (13 octobre, Important), avec notamment :
- CVE-2026-68123 (CVSS Red Hat 7.0) : corruption mémoire Open vSwitch/GSO.
- CVE-2026-72318 (7.0) : lecture hors limites CIFS/DFS.
- CVE-2026-63925 (7.0) : protection anti-rejeu MACsec XPN.
- CVE-2026-64507 (6.0) : atténuation Spectre-v2/BPF JIT incomplète.

**Point de vigilance :** une alerte circulant sous **CVE-2026-99999** a été mentionnée dans la veille du jour, mais son existence, sa qualification et son état de correction **ne sont pas vérifiés par un bulletin éditeur dans ce rapport**. Ne pas la traiter comme une CVE confirmée ni attribuer un statut corrigé/non corrigé aux kernels Rocky ou Proxmox sur cette seule base.

**Action P1** : comparer `dnf updateinfo` et les errata Red Hat/Rocky, tester le nouveau kernel sur pilote, redémarrer en maintenance et vérifier `uname -r`.

Sources :
- https://access.redhat.com/errata/RHSA-2026:81552
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## Proxmox VE 9 — kernel spécifique et stabilité

Dernières versions identifiées sur canal no-subscription : **kernel 7.0.14-24-pve** et **pve-manager 9.2.21**. Le kernel -24 apporte des corrections de stabilité PCIe/NVMe, Intel ixgbe, réseau et SMB/CIFS ; aucune version -25 ni pve-manager 9.2.22 n'a été confirmée lors de la veille initiale.

**Action P1** : déployer sur un nœud pilote, conserver un kernel de repli bootable et surveiller PCIe/AER, NIC, stockage, KVM et kernel panics avant généralisation.

Sources :
- https://github.com/proxmox/pve-kernel/blob/master/debian/changelog
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/

---
Rapport exclusivement défensif. Les versions et CVE signalées sans confirmation officielle doivent être revalidées avant décision de patch management.
