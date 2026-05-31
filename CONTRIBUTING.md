# Guide de contribution — UniCore AI

Merci de votre intérêt pour UniCore AI ! Ce guide explique comment contribuer efficacement au projet.

## Code de conduite

En participant à ce projet, vous vous engagez à maintenir un environnement respectueux et professionnel pour tous les contributeurs.

## Comment contribuer ?

### Signaler un bug

1. Vérifiez que le bug n'est pas déjà signalé dans les [Issues](../../issues).
2. Ouvrez une nouvelle issue en utilisant le template **Bug Report**.
3. Décrivez précisément : le comportement observé, le comportement attendu, et les étapes pour reproduire.

### Proposer une fonctionnalité

1. Ouvrez une issue avec le template **Feature Request**.
2. Décrivez le besoin, le contexte et la valeur ajoutée.
3. Attendez une validation avant de commencer le développement.

### Soumettre du code

#### 1. Fork et clone

```bash
git clone https://github.com/votre-username/unicore-ai.git
cd unicore-ai
```

#### 2. Créer une branche

Utilisez la convention de nommage suivante :

| Type | Format | Exemple |
|---|---|---|
| Fonctionnalité | `feat/description` | `feat/auth-jwt` |
| Correction bug | `fix/description` | `fix/upload-pdf-error` |
| Documentation | `docs/description` | `docs/api-endpoints` |
| Refactoring | `refactor/description` | `refactor/ai-pipeline` |
| Tests | `test/description` | `test/similarity-service` |

```bash
git checkout -b feat/nom-de-la-feature
```

#### 3. Convention des commits

Ce projet utilise les **Conventional Commits** :

```
type(scope): description courte

Corps optionnel décrivant le contexte

Footer optionnel (ex: Closes #42)
```

**Types autorisés :**

| Type | Usage |
|---|---|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `docs` | Documentation |
| `style` | Formatage (pas de logique) |
| `refactor` | Refactoring sans changement fonctionnel |
| `test` | Ajout ou modification de tests |
| `chore` | Tâches de maintenance |
| `perf` | Amélioration des performances |

**Exemples :**
```bash
git commit -m "feat(auth): implement JWT authentication with refresh token"
git commit -m "fix(ai): correct cosine similarity calculation for empty vectors"
git commit -m "docs(api): add endpoint documentation for memoires routes"
```

#### 4. Standards de code

**Backend (Python)**
- Respecter [PEP 8](https://pep8.org/)
- Typage obligatoire (Python type hints)
- Docstrings sur toutes les fonctions publiques
- Tests unitaires pour chaque nouveau service

**Frontend (TypeScript)**
- Types explicites, pas de `any`
- Composants fonctionnels uniquement
- Props typées avec interfaces TypeScript

#### 5. Ouvrir une Pull Request

- Décrivez clairement les modifications apportées
- Référencez l'issue associée (`Closes #N`)
- Assurez-vous que tous les tests passent
- Attendez la revue d'un maintainer

## Structure des branches

```
main          ← Version stable (production)
  └── develop ← Intégration des fonctionnalités
        ├── feat/...
        ├── fix/...
        └── docs/...
```

## Questions

Pour toute question, ouvrez une issue de type **Question** ou contactez directement le mainteneur du projet.
