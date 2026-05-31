<div align="center">

<img src="docs/assets/logo.png" alt="UniCore AI Logo" width="120" />

# UniCore AI

### Plateforme Intelligente d'Analyse et d'Innovation Académique

[![FastAPI](https://img.shields.io/badge/FastAPI-0.115-009688?style=flat-square&logo=fastapi)](https://fastapi.tiangolo.com/)
[![Next.js](https://img.shields.io/badge/Next.js-15-black?style=flat-square&logo=next.js)](https://nextjs.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=flat-square&logo=postgresql)](https://www.postgresql.org/)
[![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat-square&logo=python)](https://www.python.org/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/Licence-MIT-yellow?style=flat-square)](LICENSE)
[![Status](https://img.shields.io/badge/Statut-En%20développement-orange?style=flat-square)]()

<br/>

> **UniCore AI** est une plateforme SaaS destinée aux établissements d'enseignement supérieur.  
> Elle centralise les mémoires académiques, analyse leur originalité grâce à l'Intelligence Artificielle  
> et facilite le processus de validation par les encadreurs.

<br/>

[🚀 Démarrage rapide](#-démarrage-rapide) •
[📖 Documentation](#-documentation) •
[🏗️ Architecture](#️-architecture) •
[🤝 Contribuer](#-contribuer)

</div>

---

## 📌 Table des matières

- [Présentation du projet](#-présentation-du-projet)
- [Fonctionnalités principales](#-fonctionnalités-principales)
- [Architecture technique](#️-architecture)
- [Stack technologique](#-stack-technologique)
- [Structure du projet](#-structure-du-projet)
- [Démarrage rapide](#-démarrage-rapide)
- [Variables d'environnement](#-variables-denvironnement)
- [Documentation API](#-documentation-api)
- [Acteurs et rôles](#-acteurs-et-rôles)
- [Roadmap](#-roadmap)
- [Contribuer](#-contribuer)
- [Auteur](#-auteur)
- [Licence](#-licence)

---

## 🎯 Présentation du projet

Les établissements d'enseignement supérieur produisent chaque année un volume important de mémoires et de projets de fin d'études. La gestion de ces documents reste souvent fragmentée, exposant les institutions à plusieurs problèmes :

- **Répétition fréquente des sujets** sans détection automatique
- **Détection tardive du plagiat** basée sur des processus manuels
- **Absence d'analyse sémantique** pour mesurer l'originalité réelle
- **Archivage non centralisé** causant des pertes d'information

**UniCore AI** répond à ces défis en combinant une architecture logicielle moderne avec des techniques avancées de traitement automatique du langage naturel (NLP).

---

## ✨ Fonctionnalités principales

| Fonctionnalité | Description | Statut |
|---|---|---|
| 📤 **Dépôt en ligne** | Soumission sécurisée des mémoires (PDF) | 🔄 En cours |
| 🤖 **Analyse automatique** | Traitement IA des documents soumis | 🔄 En cours |
| 🔍 **Détection de similarités** | Comparaison sémantique entre travaux | 🔄 En cours |
| 📊 **Score d'originalité** | Calcul d'un indice d'originalité (0–100%) | 🔄 En cours |
| 💡 **Recommandations** | Suggestions d'amélioration personnalisées | 🔄 En cours |
| ✅ **Validation académique** | Workflow d'approbation par les encadreurs | 🔄 En cours |
| 🗄️ **Archivage intelligent** | Centralisation des travaux validés | 🔄 En cours |
| 🔐 **Authentification sécurisée** | JWT + gestion des rôles (RBAC) | 🔄 En cours |

---

## 🏗️ Architecture

UniCore AI repose sur une architecture **orientée services** qui sépare clairement les responsabilités entre les différents composants du système.

```
┌─────────────────────────────────────────────────────────┐
│                     CLIENTS                              │
│         Navigateur Web (Next.js 15 + TypeScript)         │
└────────────────────────┬────────────────────────────────┘
                         │ HTTPS
┌────────────────────────▼────────────────────────────────┐
│               BACKEND — API REST                         │
│              FastAPI + SQLAlchemy + JWT                  │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────┐  │
│  │  Auth/Users  │  │   Mémoires   │  │  Validations  │  │
│  └──────────────┘  └──────────────┘  └───────────────┘  │
└────────┬────────────────────┬────────────────────────────┘
         │                    │
┌────────▼──────┐    ┌────────▼──────────────────────────┐
│  PostgreSQL   │    │       MODULE IA                    │
│  + pgvector   │    │  Sentence Transformers MiniLM      │
│               │    │  Embeddings · Similarité cosinus   │
│  Base de      │    │  Score d'originalité               │
│  données      │◄───│  Recommandations                   │
└───────────────┘    └────────────────────────────────────┘
```

### Flux d'analyse d'un mémoire

```
Étudiant soumet PDF
      ↓
Extraction du texte brut
      ↓
Génération d'embeddings (MiniLM)
      ↓
Stockage vectoriel (pgvector)
      ↓
Comparaison cosinus avec la base existante
      ↓
Calcul du score de similarité
      ↓
Calcul du score d'originalité
      ↓
Génération du rapport + recommandations
      ↓
Notification à l'étudiant et à l'encadreur
```

---

## 🛠️ Stack technologique

### Frontend
| Technologie | Version | Usage |
|---|---|---|
| [Next.js](https://nextjs.org/) | 15 | Framework React (App Router) |
| [TypeScript](https://www.typescriptlang.org/) | 5 | Typage statique |
| [Tailwind CSS](https://tailwindcss.com/) | 4 | Styles utilitaires |
| [Shadcn UI](https://ui.shadcn.com/) | Latest | Composants UI accessibles |

### Backend
| Technologie | Version | Usage |
|---|---|---|
| [FastAPI](https://fastapi.tiangolo.com/) | 0.115 | Framework API REST Python |
| [SQLAlchemy](https://www.sqlalchemy.org/) | 2 | ORM base de données |
| [Pydantic](https://docs.pydantic.dev/) | 2 | Validation des données |
| [Alembic](https://alembic.sqlalchemy.org/) | Latest | Migrations de base de données |
| [PyJWT](https://pyjwt.readthedocs.io/) | Latest | Authentification JWT |

### Intelligence Artificielle
| Technologie | Usage |
|---|---|
| [Sentence Transformers](https://www.sbert.net/) | Génération d'embeddings sémantiques |
| `all-MiniLM-L6-v2` | Modèle de vectorisation |
| [pgvector](https://github.com/pgvector/pgvector) | Stockage et recherche vectorielle |
| [PyMuPDF](https://pymupdf.readthedocs.io/) | Extraction de texte PDF |

### Infrastructure
| Technologie | Usage |
|---|---|
| [Docker](https://www.docker.com/) | Conteneurisation |
| [Docker Compose](https://docs.docker.com/compose/) | Orchestration locale |
| [PostgreSQL 16](https://www.postgresql.org/) | Base de données relationnelle |
| [GitHub Actions](https://github.com/features/actions) | CI/CD automatisé |

---

## 📁 Structure du projet

```
unicore-ai/
│
├── 📁 backend/                    # API REST — FastAPI (Python)
│   ├── app/
│   │   ├── api/                   # Routes HTTP (endpoints)
│   │   │   ├── v1/
│   │   │   │   ├── auth.py
│   │   │   │   ├── users.py
│   │   │   │   ├── memoires.py
│   │   │   │   ├── analyses.py
│   │   │   │   └── validations.py
│   │   ├── core/                  # Configuration centrale
│   │   │   ├── config.py          # Variables d'environnement
│   │   │   ├── security.py        # JWT, hachage
│   │   │   └── database.py        # Connexion PostgreSQL
│   │   ├── models/                # Modèles SQLAlchemy (tables)
│   │   ├── schemas/               # Schémas Pydantic (validation)
│   │   ├── services/              # Logique métier
│   │   └── main.py                # Point d'entrée FastAPI
│   ├── tests/
│   ├── alembic/                   # Migrations base de données
│   ├── requirements.txt
│   └── Dockerfile
│
├── 📁 frontend/                   # Application Next.js
│   ├── src/
│   │   ├── app/                   # Pages (App Router)
│   │   │   ├── (auth)/            # Login, Register
│   │   │   ├── dashboard/         # Tableau de bord
│   │   │   ├── memoires/          # Gestion mémoires
│   │   │   └── admin/             # Espace administrateur
│   │   ├── components/            # Composants réutilisables
│   │   ├── lib/                   # Utilitaires, API client
│   │   └── types/                 # Types TypeScript
│   ├── public/
│   ├── package.json
│   └── Dockerfile
│
├── 📁 ai-service/                 # Module Intelligence Artificielle
│   ├── pipeline/
│   │   ├── extractor.py           # Extraction de texte PDF
│   │   ├── embedder.py            # Génération d'embeddings
│   │   ├── similarity.py          # Calcul de similarité cosinus
│   │   ├── scorer.py              # Score d'originalité
│   │   └── recommender.py        # Génération de recommandations
│   ├── tests/
│   └── Dockerfile
│
├── 📁 infra/                      # Infrastructure
│   ├── docker-compose.yml         # Environnement de développement
│   ├── docker-compose.prod.yml    # Environnement de production
│   └── nginx/
│       └── nginx.conf             # Configuration reverse proxy
│
├── 📁 docs/                       # Documentation
│   ├── architecture/              # Diagrammes UML et architecture
│   ├── api/                       # Spécification des endpoints
│   ├── database/                  # Schéma entité-relation
│   └── assets/                    # Images et ressources
│
├── 📁 .github/                    # Automatismes GitHub
│   ├── workflows/
│   │   ├── ci-backend.yml         # Tests automatiques backend
│   │   └── ci-frontend.yml        # Build automatique frontend
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   └── PULL_REQUEST_TEMPLATE.md
│
├── .gitignore
├── .editorconfig
├── README.md                      # Ce fichier
├── CONTRIBUTING.md
└── LICENSE
```

---

## 🚀 Démarrage rapide

### Prérequis

Avant de commencer, assurez-vous d'avoir installé :

- [Docker](https://www.docker.com/get-started) >= 24.0
- [Docker Compose](https://docs.docker.com/compose/install/) >= 2.0
- [Git](https://git-scm.com/)

### Installation

**1. Cloner le dépôt**

```bash
git clone https://github.com/votre-username/unicore-ai.git
cd unicore-ai
```

**2. Configurer les variables d'environnement**

```bash
cp .env.example .env
# Éditez .env avec vos valeurs
```

**3. Lancer tous les services**

```bash
docker compose -f infra/docker-compose.yml up --build
```

**4. Accéder à la plateforme**

| Service | URL |
|---|---|
| 🌐 Frontend | http://localhost:3000 |
| ⚡ API Backend | http://localhost:8000 |
| 📚 Documentation API | http://localhost:8000/docs |
| 🗄️ Base de données | localhost:5432 |

---

## 🔐 Variables d'environnement

Copiez `.env.example` vers `.env` et renseignez les valeurs :

```env
# === BASE DE DONNÉES ===
DATABASE_URL=postgresql://unicore:password@db:5432/unicore_db
POSTGRES_USER=unicore
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=unicore_db

# === SÉCURITÉ ===
SECRET_KEY=your_very_long_and_random_secret_key_here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

# === CONFIGURATION IA ===
AI_MODEL_NAME=all-MiniLM-L6-v2
SIMILARITY_THRESHOLD=0.80

# === STOCKAGE FICHIERS ===
UPLOAD_DIR=./uploads
MAX_FILE_SIZE_MB=20

# === FRONTEND ===
NEXT_PUBLIC_API_URL=http://localhost:8000/api/v1
```

> ⚠️ **Ne jamais committer le fichier `.env` dans Git.** Il est déjà listé dans `.gitignore`.

---

## 📖 Documentation API

La documentation interactive de l'API est automatiquement générée par FastAPI et accessible à :

- **Swagger UI** : http://localhost:8000/docs
- **ReDoc** : http://localhost:8000/redoc

### Principaux endpoints

```
POST   /api/v1/auth/register        Inscription d'un nouvel utilisateur
POST   /api/v1/auth/login           Connexion et obtention du token JWT
GET    /api/v1/users/me             Profil de l'utilisateur connecté

POST   /api/v1/memoires/            Soumettre un nouveau mémoire
GET    /api/v1/memoires/            Lister les mémoires (selon le rôle)
GET    /api/v1/memoires/{id}        Détails d'un mémoire

POST   /api/v1/analyses/{id}        Lancer l'analyse IA d'un mémoire
GET    /api/v1/analyses/{id}        Consulter le rapport d'analyse

PATCH  /api/v1/validations/{id}     Valider ou rejeter un mémoire
```

---

## 👥 Acteurs et rôles

| Acteur | Rôle | Permissions principales |
|---|---|---|
| 🎓 **Étudiant** | Utilisateur final | Soumettre, consulter ses mémoires et rapports |
| 👨‍🏫 **Encadreur** | Validateur académique | Examiner, commenter, valider ou rejeter |
| 🛠️ **Administrateur** | Gestionnaire | Gérer les utilisateurs et statistiques |
| 👑 **Super Admin** | Superviseur global | Contrôle total de la plateforme |

---

## 🗺️ Roadmap

- [x] Cadrage du projet et cahier des charges
- [ ] **Phase 0** — Initialisation du dépôt et architecture
- [ ] **Phase 1** — Conception UML et base de données
- [ ] **Phase 2** — Infrastructure Docker
- [ ] **Phase 3** — Backend FastAPI (Auth, Mémoires, Validations)
- [ ] **Phase 4** — Module IA (Embeddings, Similarité, Score)
- [ ] **Phase 5** — Frontend Next.js
- [ ] **Phase 6** — Tests et intégration
- [ ] **Phase 7** — Déploiement et documentation finale

---

## 🤝 Contribuer

Les contributions sont les bienvenues. Consultez le fichier [CONTRIBUTING.md](CONTRIBUTING.md) pour connaître le processus de contribution.

En résumé :

1. Forkez le projet
2. Créez une branche de fonctionnalité (`git checkout -b feat/nom-de-la-feature`)
3. Committez vos modifications (`git commit -m 'feat: description claire'`)
4. Poussez la branche (`git push origin feat/nom-de-la-feature`)
5. Ouvrez une Pull Request

---

## 👤 Auteur

**GUISSOU Ali**

Projet réalisé dans le cadre d'un mémoire de fin d'études en génie logiciel,  
sous l'encadrement du **Dr. NIKIEMA Serge Lebian**.

---

## 📄 Licence

Ce projet est distribué sous licence **MIT**.  
Consultez le fichier [LICENSE](LICENSE) pour plus d'informations.

---

<div align="center">

Conçu avec ❤️ pour moderniser la gestion académique

</div>
