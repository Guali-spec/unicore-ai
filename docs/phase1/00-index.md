# Phase 1 — Conception — UniCore AI

## Documents de cette phase

| Fichier | Contenu |
|---|---|
| `01-uml-conception.md` | Diagrammes UML : Cas d'utilisation, Classes, Séquences |
| `02-database-schema.md` | Schéma ERD, scripts SQL, index pgvector |
| `03-api-specification.md` | Spécification complète des endpoints REST |

## Comment utiliser ces documents

1. **Commencer par le diagramme de cas d'utilisation** pour comprendre le périmètre fonctionnel
2. **Consulter le diagramme de classes** avant d'écrire les modèles SQLAlchemy
3. **Utiliser le schéma SQL** pour créer les migrations Alembic
4. **Se référer à la spécification API** pour coder les routes FastAPI et les appels frontend

## Outils recommandés

- Visualiser les diagrammes PlantUML : https://www.plantuml.com/plantuml/uml/
- Extension VS Code : **PlantUML** (jebbs.plantuml)
- Tester les API : **Swagger UI** sur http://localhost:8000/docs

## Décisions techniques majeures prises en Phase 1

| Décision | Justification |
|---|---|
| UUID comme clé primaire | Évite les collisions, facilite les futures distributions |
| pgvector avec index HNSW | Meilleure performance que IVFFlat pour la similarité cosinus |
| Enum PostgreSQL natifs | Intégrité des données garantie par la base |
| JSONB pour les passages similaires | Flexibilité du stockage des données semi-structurées |
| Soft delete via `is_active` | Préserve l'historique et l'audit |
| Hash SHA-256 sur les documents | Détection des soumissions en double |
