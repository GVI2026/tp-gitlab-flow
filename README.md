# cours-03-gitlab-flow-api

API NestJS minimaliste — support du TP cours-03 (GitLab Flow).

---

## Branches du projet

| Branche | Rôle |
|---|---|
| `main` | Ligne courante du produit — nouvelles fonctionnalités |
| `production/v1` | Ligne de maintenance pour une version plus ancienne encore supportée |
| `feature/...` | Développement d'une fonctionnalité → fusionne dans `main` |
| `hotfix/...` | Correctif urgent → fusionne d'abord dans `production/v1`, puis doit être reporté sur `main` |

> Détail des règles et cycles de vie → [BRANCHES.md](BRANCHES.md)

---

## Démarrage recommandé — DevContainer (Windows / Linux / macOS)

**Prérequis** : [VS Code](https://code.visualstudio.com/) + [Docker Desktop](https://www.docker.com/products/docker-desktop/) + extension [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

1. **Forker** le dépôt sur GitHub, puis le cloner :
   ```bash
   git clone <url-de-ton-fork>
   cd cours-03-gitlab-flow-api
   ```

2. Ouvrir dans VS Code et accepter "Reopen in Container" — ou via la palette de commandes : `Dev Containers: Reopen in Container`

3. Attendre l'initialisation (première fois ~2 min). Les dépendances npm et les migrations Prisma s'exécutent automatiquement.

4. Lancer l'application :
   ```bash
   npm run start:dev
   ```

5. Swagger disponible sur [http://localhost:3000/api](http://localhost:3000/api)

---

## Démarrage manuel (sans DevContainer)

**Prérequis** : Node.js 24, PostgreSQL 18

1. Copier et renseigner les variables d'environnement :
   ```bash
   cp .env.example .env
   # Éditer .env : renseigner DATABASE_URL
   ```

2. Installer les dépendances :
   ```bash
   npm install
   ```

3. Appliquer les migrations :
   ```bash
   npx prisma migrate dev --name init
   ```

4. Lancer l'application :
   ```bash
   npm run start:dev
   ```

---

## Lancer les tests

```bash
npm test
```

---

## Routes disponibles

| Méthode | Route | Description |
|---|---|---|
| `POST` | `/tasks` | Créer une tâche |
| `GET` | `/tasks` | Lister toutes les tâches |
| `GET` | `/tasks/:id` | Récupérer une tâche |
| `PATCH` | `/tasks/:id` | Mettre à jour une tâche |
| `DELETE` | `/tasks/:id` | Supprimer une tâche |
| `GET` | `/tasks/stats` | ⚠️ **À implémenter sur la ligne courante** — `{ total, done, pending }` |

---

## Scénario GitLab Flow

### Étape 0 — Mise en place

1. **Forker** le dépôt sur GitHub, puis le cloner.
2. Vérifier que les deux branches permanentes existent :
   ```bash
   git branch -a
   ```
   Tu dois voir `main` et `origin/production/v1`.
3. Se placer sur `main` :
   ```bash
   git checkout main
   ```

### Étape 1 — Ajouter une fonctionnalité sur la ligne courante

4. Créer la branche de fonctionnalité depuis `main` :
   ```bash
   git checkout -b feature/add-task-stats
   ```
5. Implémenter `GET /tasks/stats` dans `TasksService` et `TasksController`.
6. Décommenter le bloc `TODO` dans `tasks.service.spec.ts` et l'adapter.
7. Vérifier que tous les tests passent :
   ```bash
   npm test
   ```
8. Committer proprement (conventional commits) :
   ```bash
   git add .
   git commit -m "feat: add task stats endpoint"
   ```
9. Pousser la branche et ouvrir une Pull Request vers `main`.

### Étape 2 — Corriger un bug urgent sur la version maintenue

La validation du champ `title` est volontairement incomplète : un `POST /tasks` avec `{ "title": "" }` accepte une chaîne vide.

10. Basculer sur la branche de maintenance :
   ```bash
   git checkout production/v1
   ```
11. Créer une branche de correctif depuis cette ligne de maintenance :
    ```bash
   git checkout -b hotfix/fix-title-validation
    ```
12. Corriger le problème dans `CreateTaskDto`.
13. Ajouter ou adapter un test qui vérifie que le titre vide est rejeté.
14. Committer proprement :
    ```bash
    git add .
    git commit -m "fix: reject empty task title"
    ```
15. Pousser la branche et ouvrir une Pull Request vers `production/v1`.

### Étape 3 — Reporter le correctif critique sur la ligne courante

16. Une fois le hotfix intégré sur `production/v1`, reporter ce correctif sur `main`.

Le dépôt est justement là pour montrer qu'un correctif critique ne reste pas sur une seule ligne de maintenance.

Tu peux utiliser l'une des deux approches suivantes :

- `cherry-pick` du commit de hotfix,
- ou correction équivalente sur une nouvelle branche issue de `main`.

17. Vérifier que `main` et `production/v1` contiennent bien chacune le correctif là où il est nécessaire.

---

## Pour les plus rapides — Exercice optionnel validé

Après avoir terminé le scénario principal, traite un **second correctif mineur** sur la ligne de maintenance puis reporte-le également sur `main`.

L'objectif du bonus n'est pas d'ajouter une nouvelle notion, mais de renforcer l'idée centrale du GitLab Flow : plusieurs lignes actives impliquent parfois plusieurs corrections coordonnées.

Tu peux par exemple :

- corriger un second bug simple sur `production/v1`,
- le commit en `fix: ...`,
- puis le reporter proprement sur `main`.

---

## Note de setup — enseignant

Avant la séance, s'assurer que :

- La branche `production/v1` existe et est poussée
- `main` représente la ligne courante du produit
- `production/v1` représente une version plus ancienne encore maintenue
- `main` et `production/v1` sont protégées : PR obligatoire, CI doit passer avant merge
- `npm test` est vert sur un dépôt fraîchement cloné

Pour que la démonstration soit parlante, il est conseillé de préparer un historique simple montrant que `production/v1` n'est pas la même ligne de travail que `main`.
