# Branches du projet

Ce tableau résume les règles de GitLab Flow retenues dans ce dépôt.

| Branche | Rôle | Contexte de diffusion | Part de | Fusionne dans |
|---|---|---|---|---|
| `main` | Ligne courante du produit | version actuelle / validation courante | — | — |
| `production/v1` | Ligne de maintenance d'une ancienne version supportée | production v1 | historique préparé avant la séance | — |
| `feature/...` | Nouvelle fonctionnalité pour la ligne courante | future version courante | `main` | `main` |
| `hotfix/...` | Correctif urgent pour une version maintenue | correctif de production | `production/v1` | `production/v1`, puis report sur `main` |

## Règles

- On ne développe **jamais directement** sur `main`.
- On ne développe **jamais directement** sur `production/v1`.
- Une nouvelle fonctionnalité part toujours de `main`.
- Un correctif urgent sur une ancienne version part de la branche de maintenance concernée.
- Lorsqu'un bug existe à la fois sur la version maintenue et sur la ligne courante, il doit être **reporté** aussi sur `main`.

## Cycle de vie d'une feature sur la ligne courante

```
main
  └─ feature/ma-fonctionnalite
       └─ (commits)
main ← merge feature
```

## Cycle de vie d'un hotfix sur une version maintenue

```
production/v1
  └─ hotfix/fix-mon-bug
       └─ (correctif)
production/v1 ← merge hotfix
main          ← cherry-pick ou correction équivalente
```

## Pourquoi ce flow est intéressant ici

Ce dépôt sert à montrer qu'un projet peut devoir :

- développer la version courante,
- continuer à corriger une ancienne version encore utilisée,
- coordonner les reports de correctifs,
- accepter un coût Git plus élevé que dans un flow plus simple.
