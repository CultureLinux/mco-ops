# mco-ops

Dépôt de publication des briefs MCO quotidiens pour le parc Rocky Linux et Proxmox.

## Contenu

- `latest.md` : dernier brief MCO disponible.
- `archive/` : historique des rapports quotidiens au format `YYYY-MM-DD.md`.

## Périmètre

Les rapports couvrent principalement :

- nouveautés et changements importants Proxmox VE ;
- nouveaux kernels Rocky Linux / RHEL ;
- vulnérabilités kernel récemment corrigées ou publiées ;
- classement des CVE par criticité ;
- impact, vecteur d'exploitation et prérequis ;
- disponibilité des correctifs ;
- actions MCO recommandées.

## Structure

```text
.
├── README.md
├── latest.md
└── archive/
    ├── 2026-09-21.md
    ├── 2026-09-22.md
    └── ...
```

## Récupération du dernier rapport

```bash
curl -fsSL \
  https://raw.githubusercontent.com/CultureLinux/mco-ops/main/latest.md
```

Ou :

```bash
wget -qO- \
  https://raw.githubusercontent.com/CultureLinux/mco-ops/main/latest.md
```

## Format

Les rapports sont écrits en Markdown et contiennent un front matter YAML pour faciliter leur exploitation automatique.

Exemple :

```yaml
---
date: 2026-09-21
status: warning
critical_cve: 0
important_cve: 4
---
```

Valeurs possibles pour `status` :

- `ok`
- `info`
- `warning`
- `critical`

## Sources

Les rapports privilégient les sources officielles, notamment :

- Rocky Linux
- Red Hat
- Proxmox
- NVD / CVE lorsque pertinent
- CISA KEV lorsque pertinent

## Objectif

Fournir un brief court, exploitable rapidement par une équipe MCO, avec priorité donnée aux changements nécessitant une action opérationnelle.
