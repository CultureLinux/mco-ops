# Brief MCO — 13 octobre 2026

## Résumé exécutif
**GitLab P0 :** la Critical Patch du 12 octobre corrige des vulnérabilités critiques. Cibles officielles : **19.4.2, 19.3.5, 19.2.8**. Correction importante du rapport du 12 octobre : **19.3.5** corrige le bug des pages de merge requests avec Ona présent dans 19.3.4. **CVE-2026-97229 (CVSS 10.0)** concerne le traitement des configurations CI/CD et permet une RCE sans authentification. Mise à jour immédiate.

**Rocky 9 P1 :** dernier kernel vérifié **5.14.0-687.56.1.el9_8**, publié le 8 octobre ; pas de nouveauté depuis hier. L'erratum RHSA-2026:76739 comprend notamment CVE-2026-46317 (KVM arm64, Red Hat 7.0, corrigée pour RHEL 9). CVE-2026-68432 (VXLAN, Red Hat 7.0) reste indiquée Affected pour RHEL 9 dans la fiche consultée : ne pas présumer de sa correction.

**Proxmox P1 :** dernier kernel **7.0.14-24-pve**, changelog daté du 10 octobre ; `pve-manager 9.2.21`. Correctifs PCIe (#7969, NIC/NVMe et Dell R610), Intel ixgbe (#7874, X550/E610), réseau SRv6 (CVE-2026-98096), MPLS/Open vSwitch (CVE-2026-97612), SMB/CIFS DFS (CVE-2026-97562), Btrfs (CVE-2026-98279, CVE-2026-93800, CVE-2026-93798) et clés chiffrées (CVE-2026-98222). CVSS individuels non fournis par Proxmox.

## GitLab : risques complémentaires
- CVE-2026-92883, CVSS 8.7 : XSS, authentifié, interaction requise.
- CVE-2026-16301, 8.5 : contournement d'autorisation sur pipelines de branches protégées, rôle Developer, sans interaction.
- CVE-2026-14814, 8.2 : cache dépôt et risque pour variables CI/CD, authentifié, complexité élevée.
- CVE-2026-81839, 7.7 : pipelines après transfert de projet, EE, authentifié et interaction.
- CVE-2026-96893, 7.3 : instructions Duo Agent Platform, EE, authentifié et interaction.
- CVE-2026-101168, 6.2 : exposition de tokens/secrets dans logs Pages, accès privilégié.
- CVE-2026-16275 / CVE-2026-84284 / CVE-2026-85529, 5.3 : lectures GraphQL sans authentification.
Versions corrigées : **19.2.8 / 19.3.5 / 19.4.2** ; plages affectées et prérequis détaillés dans la publication officielle GitLab. Pour CVE-2026-97229 : 19.2 avant 19.2.8, 19.3 avant 19.3.5 et 19.4 avant 19.4.2.

## Actions
1. GitLab : sauvegarde, contrôle versionlock et upgrade vers **19.3.5** si encore en 19.3.2 ; valider runners, CI/CD, MR, SSH et authentification.
2. Rocky : déployer `687.56.1` et vérifier le noyau après redémarrage.
3. Proxmox : pilote `7.0.14-24-pve`, vérifier PCIe/AER, réseau, stockage, VM et conserver un kernel de secours.

## Sources officielles
- https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-2-released/
- https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/
- https://access.redhat.com/security/cve/cve-2026-46317
- https://access.redhat.com/security/cve/cve-2026-68432
- https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/proxmox-kernel-7.0/
- https://github.com/proxmox/pve-kernel/blob/master/debian/changelog
