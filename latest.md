---
type: mco-daily
date: 2026-10-19
timezone: Europe/Paris
status: action-required
---

# Brief MCO — lundi 19 octobre 2026

## Résumé exécutif

- **P0 GitLab** : **CVE-2026-97229, CVSS 10.0**, exécution de code sur le serveur GitLab via CI/CD **sans authentification ni interaction**. Versions correctives 19.2.8, 19.3.5, 19.4.2.
- **P0 Proxmox** : **PwnEscape / CVE-2026-99999** est **corrigée dans 7.0.14-24-pve**, selon l'avis PSA-2026-00068-1 du 16 octobre. Vérifier que ce noyau est effectivement démarré.
- **P1 Rocky/RHEL 9** : Red Hat classe toujours PwnEscape comme **Affected** sans erratum correctif pour RHEL 9. Dernier kernel Rocky observé : **5.14.0-687.58.1.el9_8** (15 octobre).

## Proxmox VE 9

### PwnEscape — CVE-2026-99999

- **Correction officielle** : `proxmox-kernel-7.0.14-24-pve(-signed)`, avis **PSA-2026-00068-1** du 16 octobre.
- **Impact** : une VM KVM malveillante peut déclencher une corruption mémoire dans le traitement des métadonnées de checksum IPv4/virtio, avec fuite de mémoire hôte, déni de service ou exécution de code sur l'hôte.
- **Vecteur et prérequis** : accès à une VM invitée ou à une charge contrôlée ; pas de RCE Internet sans prérequis. Pas d'interaction utilisateur.
- **MCO P0** : déployer le noyau corrigé, redémarrer les hôtes, contrôler `uname -r`, conserver un noyau de secours bootable et surveiller PCIe/AER, NIC, NVMe, KVM et kernel panics.
- Dernières versions identifiées dans la veille no-subscription : `7.0.14-24-pve` et `pve-manager 9.2.21`. Aucun successeur confirmé aujourd'hui.

Sources :
- https://forum.proxmox.com/threads/proxmox-virtual-environment-security-advisories.149331/
- https://forum.proxmox.com/threads/kvm-escape-cve-2026-99999.187102/
- https://github.com/proxmox/pve-kernel/blob/master/debian/changelog

## Rocky Linux / RHEL 9

### Kernel

Dernier paquet observé dans le dépôt officiel Rocky : `5.14.0-687.58.1.el9_8`, daté du **15 octobre 2026**. Pas de nouveau build confirmé depuis. L'erratum de sécurité exact de ce build et ses nouvelles CVE corrigées ne sont pas confirmés ; ne pas extrapoler.

### PwnEscape — CVE-2026-99999

| Critère | Donnée Red Hat |
|---|---|
| Gravité | Critical |
| CVSS Red Hat | **8.8** |
| Vecteur | Local, notamment VM non fiable |
| Privilèges | Faibles |
| Interaction utilisateur | Aucune |
| Impacts | Fuite mémoire, DoS, exécution de code hôte |
| RHEL 9 kernel | **Affected** |
| Erratum correctif RHEL 9 | **Aucun indiqué** |

**MCO P1/P0** : prioriser les hôtes exécutant des VM non fiables ou mutualisées ; suivre l'avis Red Hat et le backport Rocky avant de considérer une mise à jour comme correctrice.

Pour mémoire, `5.14.0-687.57.1.el9_8` correspond à **RHSA-2026:81552** (Important) et couvre notamment CVE-2026-68123 (Open vSwitch/GSO, 7.0), CVE-2026-72318 (CIFS/DFS, 7.0), CVE-2026-63925 (MACsec XPN, 7.0), CVE-2026-64507 (Spectre-v2/BPF JIT, 6.0). Ne pas les présenter comme nouvelles aujourd'hui.

Sources :
- https://access.redhat.com/security/cve/cve-2026-99999
- https://access.redhat.com/errata/RHSA-2026:81552
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

## GitLab Self-Managed CE/EE

Dernière **Critical Patch Release** confirmée : **12 octobre 2026**. Versions correctives : **19.4.2 / 19.3.5 / 19.2.8**. GitLab 19.5.0 est une version fonctionnelle publiée le 15 octobre ; aucune nouvelle Critical Patch ultérieure confirmée aujourd'hui.

| CVE | CVSS | Impact, vecteur et prérequis |
|---|---:|---|
| **CVE-2026-97229** | **10.0** | **RCE serveur via CI/CD**, réseau, **sans authentification ni interaction** ; versions affectées : 19.2 <19.2.8, 19.3 <19.3.5, 19.4 <19.4.2 |
| CVE-2026-92883 | 8.7 | XSS sur dépôt/syntax highlighting ; compte à faibles privilèges, interaction de la victime |
| **CVE-2026-16301** | 8.5 | **Auth bypass CI/CD** sur branches protégées ; Developer, sans interaction |
| **CVE-2026-14814** | 8.2 | Cache poisoning, exposition possible de variables CI/CD protégées ; authentifié, complexité élevée |
| CVE-2026-81839 | 7.7 | Pipelines exécutés avec privilèges inappropriés après transfert de projet ; EE, authentification et interaction |
| CVE-2026-96893 | 7.3 | Injection d'instructions Duo Agent Platform ; EE, authentification et interaction |
| **CVE-2026-101168** | 6.2 | **Fuite de secrets/tokens** dans les logs GitLab Pages ; accès privilégié aux logs |
| CVE-2026-7767 | 5.8 | Fuite de token via proxy Kubernetes Agent ; contrôle de l'agent et authentification |
| **CVE-2026-16275 / 84284 / 85529** | 5.3 chacune | Lecture de données confidentielles GraphQL **sans authentification** |

**MCO P0** : vérifier la version réellement installée ; si encore `19.3.2`, migrer au minimum vers `19.3.5` après sauvegarde, levée éventuelle du versionlock DNF, puis tests runners, pipelines, branches protégées, merge requests et secrets. Les plages affectées des CVE autres que 97229 doivent être vérifiées individuellement dans le bulletin officiel.

Sources :
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- https://docs.gitlab.com/releases/19/gitlab-19-5-released/

---
Rapport de veille défensive ; les versions sans confirmation explicite ne doivent pas être considérées comme corrigées.
