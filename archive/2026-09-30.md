---
type: mco-daily
date: 2026-09-30
generated_at: 2026-09-30T08:08:00+02:00
status: critical
rocky_kernel_latest: 5.14.0-687.52.1.el9_8
rocky_security_advisory: RHSA-2026:71700
proxmox_major: 9.2
proxmox_pve_manager_latest: 9.2.21
proxmox_kernel_production: 7.0.14-19-pve
proxmox_kernel_test_seen: 7.0.14-20-pve
gitlab_latest_security_release: 19.4.1
gitlab_supported_security_fixes: [19.4.1, 19.3.3, 19.2.7]
---

# MCO Daily — 30 septembre 2026

## Résumé exécutif

🔴 **GitLab reste prioritaire** : aucune nouvelle Critical Patch Release n'a remplacé `19.4.1`, `19.3.3` et `19.2.7`. Les deux RCE CI/CD CVSS 9.9 restent le risque principal et justifient l'upgrade immédiat des instances affectées.

🟠 **Rocky/RHEL 9** : la cible reste `5.14.0-687.52.1.el9_8`. Aucun kernel Rocky 9 x86_64 plus récent n'est publié dans BaseOS ce matin. **DiagSpill / CVE-2026-74469 (CVSS 8.3)** reste un point de vigilance du bulletin RHSB-2026-011.

🟡 **Proxmox VE : nouveauté** : `pve-manager 9.2.21` est désormais présent dans le dépôt no-subscription. Cette version apporte surtout des corrections Ceph/Cephx importantes pour les clusters utilisant Ceph, notamment la migration des clés et le nettoyage des clés d'OSD chiffrés. Le kernel production suivi reste `7.0.14-19-pve`; `7.0.14-20-pve` est observé dans pve-test et ne doit pas être forcé en production.

---

# GitLab Self-Managed

## Critical Patch Release toujours en vigueur

Versions corrigées publiées le 23 septembre 2026 :

```text
19.4.x -> 19.4.1
19.3.x -> 19.3.3
19.2.x -> 19.2.7
```

GitLab recommande l'upgrade immédiat de toutes les installations affectées.

### Vulnérabilités prioritaires

| Criticité | CVE | Impact / vecteur | Prérequis | Versions affectées | Corrigé |
|---|---|---|---|---|---|
| 🔴 Critical 9.9 | CVE-2026-89078 | **RCE serveur** par double-free lors du parsing d'une regex CI/CD spécialement forgée | AV:N, auth requise, faibles privilèges, UI:N | 19.2 <19.2.7 ; 19.3 <19.3.3 ; 19.4 <19.4.1 | 19.2.7 / 19.3.3 / 19.4.1 |
| 🔴 Critical 9.9 | CVE-2026-93577 | **RCE serveur** par integer overflow du compilateur regex via configuration CI/CD | AV:N, auth requise, faibles privilèges, UI:N | mêmes branches | mêmes versions |
| 🟠 High 8.7 | CVE-2026-84739 | XSS dans le diff viewer de Merge Request | AV:N, auth faible privilège, interaction victime requise | >=13.11 jusqu'aux versions corrigées | mêmes versions |
| 🟠 High 7.7 | CVE-2026-92470 | **fuite de variables CI/CD** depuis des traces debug via Duo AI | GitLab EE, AV:N, auth faible privilège, UI:N | >=18.7 jusqu'aux versions corrigées | mêmes versions |
| 🟡 Medium 5.4 | CVE-2026-92874 | dépassement du scope prévu d'un token MCP | auth + token MCP, UI:N | >=18.3 jusqu'aux versions corrigées | mêmes versions |
| 🟢 Low 3.7 | CVE-2026-4523 | **lecture sans authentification de traces CI/CD** pouvant contenir des valeurs sensibles | AV:N, AC:H, **PR:N**, UI:N | >=15.11 jusqu'aux versions corrigées | mêmes versions |

### Signaux prioritaires

- **RCE :** CVE-2026-89078, CVE-2026-93577.
- **CI/CD :** les deux RCE et les fuites de variables/traces.
- **Secrets/tokens :** CVE-2026-92470 et CVE-2026-92874.
- **Sans authentification :** CVE-2026-4523.
- **Auth bypass critique :** aucun nouveau cas critique identifié dans cette release.

**Action MCO :** toute instance encore en `19.3.2` doit passer au minimum en `19.3.3`.

---

# Rocky Linux / RHEL 9

## Kernel actuel

```text
5.14.0-687.52.1.el9_8
RHSA-2026:71700 — Important
```

Le dépôt Rocky BaseOS x86_64 ne publie pas de kernel plus récent au 30 septembre au matin.

Le build `687.52.1` corrige notamment **CVE-2026-68121 / PPPoEject** et les autres CVE de RHSA-2026:71700.

## DiagSpill reste à surveiller

### CVE-2026-74469 — DiagSpill

- **Sévérité Red Hat :** Important
- **CVSS :** 8.3
- **Sous-système :** SCTP / `sctp_diag`
- **Impact :** important out-of-bounds write, corruption mémoire, crash/DoS ; Red Hat indique également un impact potentiel sur l'intégrité
- **Prérequis local :** SCTP et `sctp_diag`; contrairement aux autres failles du bulletin, pas de user namespace non privilégié ni capacité spéciale nécessaire
- **Scénario réseau :** DoS distant possible dans certaines configurations SCTP lorsque ASCONF/ADD-IP est activé et qu'un processus local déclenche la requête de diagnostic
- **État bulletin :** RHSB-2026-011 reste Ongoing / Important

**Action MCO :** conserver `5.14.0-687.52.1.el9_8` comme minimum actuel, vérifier qu'il est actif après reboot et surveiller l'errata RHEL 9 corrigeant explicitement DiagSpill. Sur les serveurs sans besoin SCTP, réduire l'exposition après validation applicative.

---

# Proxmox VE

## Nouveau pve-manager 9.2.21

`pve-manager 9.2.21` est apparu dans le dépôt PVE no-subscription le 29 septembre.

Points MCO pertinents :

- **Ceph OSD destroy :** suppression désormais des clés lockbox et dm-crypt d'un OSD chiffré détruit ; auparavant elles pouvaient rester dans les monitors et apparaître comme clés non sûres pendant la migration Cephx.
- **Ceph + FQDN :** correction du rolling restart OSD sur les nœuds dont le hostname est un FQDN.
- **Migration Cephx :** plusieurs garde-fous supplémentaires pour les rotations de clés, les copies de clés de stockage, les élections monitor et les clusters utilisant des FQDN.
- **RBD/CephFS :** dépendance sur `libpve-storage-perl >= 9.1.9` afin de gérer les keyrings contenant une clé `aes256k`; une version antérieure peut laisser les storages RBD/CephFS utilisant une clé migrée inactifs.
- **VM CD/DVD :** changement de média via une tâche API afin de rendre visibles les warnings, notamment ceux concernant certaines ISO VirtIO problématiques.

### Kernel Proxmox

```text
production suivie : 7.0.14-19-pve
pve-test observé   : 7.0.14-20-pve
```

Le build `7.0.14-20-pve` est visible dans `pve-test`, daté du 24 septembre. Il n'est pas retenu comme cible production tant que sa promotion dans les dépôts habituels n'est pas confirmée.

### Action Proxmox

- [ ] Déployer `pve-manager 9.2.21` selon la fenêtre MCO habituelle, particulièrement sur les clusters Ceph.
- [ ] Vérifier `libpve-storage-perl >= 9.1.9` avant/pendant une migration Cephx.
- [ ] Conserver `7.0.14-19-pve` comme kernel de référence production.
- [ ] Ne pas forcer `7.0.14-20-pve` depuis pve-test.

---

# Actions du jour

## 🔴 Urgent

- [ ] **GitLab** : passer les instances vulnérables vers `19.4.1`, `19.3.3` ou `19.2.7`.

## 🟠 Haute priorité

- [ ] **Rocky** : vérifier `uname -r` et confirmer `5.14.0-687.52.1.el9_8` après reboot.
- [ ] Maintenir la surveillance de **DiagSpill / CVE-2026-74469**.

## 🟡 MCO Proxmox

- [ ] Intégrer **pve-manager 9.2.21** à la prochaine fenêtre.
- [ ] Prioriser la mise à jour sur les clusters Ceph/Cephx.
- [ ] Rester sur **7.0.14-19-pve** côté kernel production.

---

# Sources

- GitLab — Critical Patch Release 19.4.1, 19.3.3, 19.2.7  
  https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-4-1-released/

- Red Hat — RHSB-2026-011  
  https://access.redhat.com/security/vulnerabilities/RHSB-2026-011

- Red Hat — CVE-2026-74469  
  https://access.redhat.com/security/cve/cve-2026-74469

- Rocky Linux 9 — BaseOS x86_64 kernel packages  
  https://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/Packages/k/

- Proxmox — pve-manager no-subscription changelogs  
  https://metadata.cdn.proxmox.com/download/changelogs/pve/dists/trixie/pve-no-subscription/p/pve-manager/

- Proxmox pve-manager source changelog  
  https://github.com/proxmox/pve-manager/blob/master/debian/changelog
