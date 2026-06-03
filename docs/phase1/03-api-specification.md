# Phase 1 — Spécification des API REST — UniCore AI

## Introduction

Ce document définit le **contrat d'interface** entre le frontend et le backend d'UniCore AI. Il décrit l'ensemble des endpoints REST, les formats de requête et de réponse, les règles d'autorisation et les codes HTTP attendus.

> Ce document est le **contrat technique** : le frontend et le backend peuvent être développés en parallèle en s'appuyant sur cette spécification.

---

## 1. Conventions générales

### 1.1 Structure des URL

```
https://api.unicore.ai/api/v1/{ressource}/{id?}/{action?}
```

| Élément | Règle | Exemple |
|---|---|---|
| Préfixe | Toujours `/api/v1/` | `/api/v1/memoires` |
| Ressources | Pluriel, minuscules | `memoires`, `users` |
| Identifiants | UUID dans le chemin | `/memoires/uuid-ici` |
| Actions | Verbe HTTP suffit | `PATCH /validations/{id}` |

### 1.2 Méthodes HTTP

| Méthode | Usage | Idempotent ? |
|---|---|---|
| `GET` | Lire une ou plusieurs ressources | ✅ Oui |
| `POST` | Créer une ressource | ❌ Non |
| `PUT` | Remplacer entièrement | ✅ Oui |
| `PATCH` | Modifier partiellement | ❌ Non |
| `DELETE` | Supprimer | ✅ Oui |

### 1.3 Codes HTTP standards

| Code | Signification | Quand l'utiliser |
|---|---|---|
| `200 OK` | Succès | GET, PATCH, DELETE réussis |
| `201 Created` | Création réussie | POST réussi |
| `204 No Content` | Suppression réussie | DELETE sans retour |
| `400 Bad Request` | Données invalides | Validation échouée |
| `401 Unauthorized` | Non authentifié | Token absent ou expiré |
| `403 Forbidden` | Non autorisé | Rôle insuffisant |
| `404 Not Found` | Ressource introuvable | ID inexistant |
| `409 Conflict` | Conflit de données | Email déjà utilisé |
| `422 Unprocessable` | Erreur de validation Pydantic | Champ manquant ou mauvais type |
| `500 Internal Error` | Erreur serveur | Exception non gérée |

### 1.4 Format de réponse standard

**Succès :**
```json
{
  "success": true,
  "data": { ... },
  "message": "Opération réussie"
}
```

**Erreur :**
```json
{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Le mémoire demandé n'existe pas",
    "details": []
  }
}
```

### 1.5 Authentification

Toutes les routes protégées nécessitent un token JWT dans le header :

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### 1.6 Matrice des autorisations

| Rôle | Lecture | Soumission | Validation | Administration |
|---|---|---|---|---|
| **ETUDIANT** | Ses mémoires | ✅ | ❌ | ❌ |
| **ENCADREUR** | Ses mémoires assignés | ❌ | ✅ | ❌ |
| **ADMINISTRATEUR** | Tout | ❌ | ❌ | ✅ Partiel |
| **SUPER_ADMIN** | Tout | ❌ | ✅ | ✅ Total |

---

## 2. Module Authentification — `/api/v1/auth`

### POST `/auth/register` — Inscription

**Accès :** Public

**Corps de la requête :**
```json
{
  "nom": "GUISSOU",
  "prenom": "Ali",
  "email": "ali.guissou@universite.bf",
  "password": "MotDePasseSecure123!",
  "role": "ETUDIANT"
}
```

**Réponse 201 :**
```json
{
  "success": true,
  "data": {
    "id": "uuid-user",
    "nom": "GUISSOU",
    "prenom": "Ali",
    "email": "ali.guissou@universite.bf",
    "role": "ETUDIANT",
    "created_at": "2025-03-01T10:00:00Z"
  },
  "message": "Compte créé avec succès"
}
```

**Erreurs :**
- `409` → Email déjà utilisé
- `422` → Champ manquant ou format invalide

---

### POST `/auth/login` — Connexion

**Accès :** Public

**Corps de la requête :**
```json
{
  "email": "ali.guissou@universite.bf",
  "password": "MotDePasseSecure123!"
}
```

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "access_token": "eyJhbGci...",
    "refresh_token": "eyJhbGci...",
    "token_type": "bearer",
    "expires_in": 1800,
    "user": {
      "id": "uuid-user",
      "nom": "GUISSOU",
      "prenom": "Ali",
      "email": "ali.guissou@universite.bf",
      "role": "ETUDIANT"
    }
  }
}
```

**Erreurs :**
- `401` → Credentials invalides

---

### POST `/auth/refresh` — Renouvellement du token

**Corps :**
```json
{ "refresh_token": "eyJhbGci..." }
```

**Réponse 200 :**
```json
{
  "success": true,
  "data": { "access_token": "nouveau_token...", "expires_in": 1800 }
}
```

### POST `/auth/logout` — Déconnexion

**Accès :** Authentifié

**Réponse 200 :** Révocation du refresh token.

---

## 3. Module Utilisateurs — `/api/v1/users`

### GET `/users/me` — Profil courant

**Accès :** Tout utilisateur authentifié

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "id": "uuid-user",
    "nom": "GUISSOU",
    "prenom": "Ali",
    "email": "ali.guissou@universite.bf",
    "role": "ETUDIANT",
    "is_active": true,
    "last_login_at": "2025-03-01T10:00:00Z",
    "created_at": "2025-01-15T08:00:00Z"
  }
}
```

### PATCH `/users/me` — Modifier son profil

**Corps :**
```json
{
  "nom": "GUISSOU",
  "prenom": "Ali Nouveau",
  "avatar_url": "https://..."
}
```

### GET `/users` — Lister tous les utilisateurs

**Accès :** ADMINISTRATEUR, SUPER_ADMIN

**Paramètres de requête :**
```
?role=ETUDIANT&is_active=true&page=1&limit=20&search=guissou
```

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "items": [ { ...user }, { ...user } ],
    "total": 150,
    "page": 1,
    "limit": 20,
    "pages": 8
  }
}
```

### PATCH `/users/{id}/toggle-status` — Activer/Désactiver

**Accès :** ADMINISTRATEUR, SUPER_ADMIN

---

## 4. Module Mémoires — `/api/v1/memoires`

### POST `/memoires` — Soumettre un mémoire

**Accès :** ETUDIANT

**Corps :** `multipart/form-data`
```
titre          : "Analyse des réseaux de neurones pour la détection d'anomalies"
resume         : "Ce mémoire présente..."
mots_cles      : ["IA", "deep learning", "anomalies"]
annee_academique : "2024-2025"
domaine        : "Informatique"
encadreur_id   : "uuid-encadreur"
fichier        : [fichier PDF ≤ 20Mo]
```

**Réponse 201 :**
```json
{
  "success": true,
  "data": {
    "id": "uuid-memoire",
    "titre": "Analyse des réseaux...",
    "statut": "EN_ATTENTE",
    "submitted_at": "2025-03-01T10:00:00Z",
    "document": {
      "id": "uuid-doc",
      "nom_fichier": "memoire_guissou_2025.pdf",
      "taille_octets": 2500000
    }
  },
  "message": "Mémoire soumis. L'analyse débutera dans quelques instants."
}
```

**Erreurs :**
- `400` → Fichier non PDF / Taille dépassée
- `409` → Document identique déjà soumis (même hash SHA-256)

---

### GET `/memoires` — Lister les mémoires

**Accès :** Selon le rôle (étudiant = ses mémoires, encadreur = ses assignés, admin = tous)

**Paramètres :**
```
?statut=ANALYSE_TERMINEE&annee=2024-2025&page=1&limit=20&search=réseaux
```

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": "uuid-memoire",
        "titre": "Analyse des réseaux...",
        "statut": "ANALYSE_TERMINEE",
        "submitted_at": "2025-03-01T10:00:00Z",
        "etudiant": { "id": "...", "nom": "GUISSOU", "prenom": "Ali" },
        "encadreur": { "id": "...", "nom": "NIKIEMA", "prenom": "Serge" },
        "score_originalite": 87.45
      }
    ],
    "total": 45,
    "page": 1,
    "limit": 20
  }
}
```

---

### GET `/memoires/{id}` — Détails complets d'un mémoire

**Accès :** Propriétaire / Encadreur assigné / Admin

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "id": "uuid-memoire",
    "titre": "...",
    "resume": "...",
    "mots_cles": ["IA", "deep learning"],
    "annee_academique": "2024-2025",
    "domaine": "Informatique",
    "statut": "ANALYSE_TERMINEE",
    "etudiant": { ... },
    "encadreur": { ... },
    "document": { "id": "...", "nom_fichier": "...", "url_telechargement": "..." },
    "rapport_analyse": { ... },
    "validation": null,
    "submitted_at": "2025-03-01T10:00:00Z"
  }
}
```

### DELETE `/memoires/{id}` — Supprimer un mémoire

**Accès :** Propriétaire (uniquement si statut = BROUILLON ou EN_ATTENTE)

---

## 5. Module Analyses — `/api/v1/analyses`

### POST `/analyses/{memoire_id}` — Déclencher l'analyse manuellement

**Accès :** ETUDIANT (son mémoire) / ADMINISTRATEUR

**Réponse 202 Accepted :**
```json
{
  "success": true,
  "data": {
    "memoire_id": "uuid-memoire",
    "statut": "EN_ANALYSE",
    "message": "Analyse lancée en arrière-plan"
  }
}
```

---

### GET `/analyses/{memoire_id}` — Obtenir le rapport d'analyse

**Accès :** Propriétaire / Encadreur assigné / Admin

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "id": "uuid-rapport",
    "memoire_id": "uuid-memoire",
    "score_originalite": 87.45,
    "score_similarite_max": 0.1255,
    "nombre_comparaisons": 234,
    "statut": "COMPLET",
    "duree_traitement_ms": 4230,
    "created_at": "2025-03-01T10:05:00Z",
    "similitudes": [
      {
        "memoire_cible": {
          "id": "uuid-autre",
          "titre": "Mémoire similaire",
          "auteur": "Autre Étudiant",
          "annee": "2023-2024"
        },
        "score_similarite": 0.1255,
        "passages_similaires": [
          { "extrait_source": "...", "extrait_cible": "...", "score": 0.82 }
        ]
      }
    ],
    "recommandations": [
      {
        "type": "ORIGINALITE",
        "contenu": "Renforcez l'originalité de votre approche méthodologique...",
        "priorite": 1
      }
    ]
  }
}
```

---

## 6. Module Validations — `/api/v1/validations`

### PATCH `/validations/{memoire_id}` — Valider ou rejeter un mémoire

**Accès :** ENCADREUR assigné au mémoire

**Corps :**
```json
{
  "decision": "VALIDE",
  "commentaire": "Excellent travail. Bonne maîtrise des concepts IA.",
  "points_forts": "Méthodologie rigoureuse, revue de littérature complète.",
  "points_amelioration": "Améliorer la conclusion."
}
```

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "id": "uuid-validation",
    "memoire_id": "uuid-memoire",
    "decision": "VALIDE",
    "encadreur": { "nom": "NIKIEMA", "prenom": "Serge" },
    "created_at": "2025-03-05T14:00:00Z"
  },
  "message": "Mémoire validé et archivé avec succès"
}
```

**Valeurs `decision` acceptées :** `VALIDE` | `REJETE` | `REVISION_DEMANDEE`

---

## 7. Module Notifications — `/api/v1/notifications`

### GET `/notifications` — Mes notifications

**Paramètres :** `?is_read=false&limit=10`

### PATCH `/notifications/{id}/read` — Marquer comme lue

### PATCH `/notifications/read-all` — Tout marquer comme lu

---

## 8. Module Administration — `/api/v1/admin`

### GET `/admin/stats` — Statistiques globales

**Accès :** ADMINISTRATEUR, SUPER_ADMIN

**Réponse 200 :**
```json
{
  "success": true,
  "data": {
    "total_utilisateurs": 520,
    "total_memoires": 1240,
    "memoires_par_statut": {
      "EN_ATTENTE": 12,
      "EN_ANALYSE": 3,
      "ANALYSE_TERMINEE": 45,
      "VALIDE": 980,
      "REJETE": 200
    },
    "score_originalite_moyen": 74.2,
    "memoires_ce_mois": 38
  }
}
```

---

## 9. Tableau récapitulatif des endpoints

| Méthode | Endpoint | Accès | Description |
|---|---|---|---|
| POST | `/auth/register` | Public | Inscription |
| POST | `/auth/login` | Public | Connexion |
| POST | `/auth/refresh` | Public | Renouveler token |
| POST | `/auth/logout` | Auth | Déconnexion |
| GET | `/users/me` | Auth | Profil courant |
| PATCH | `/users/me` | Auth | Modifier profil |
| GET | `/users` | Admin | Lister utilisateurs |
| PATCH | `/users/{id}/toggle-status` | Admin | Activer/désactiver |
| POST | `/memoires` | Étudiant | Soumettre mémoire |
| GET | `/memoires` | Rôle | Lister mémoires |
| GET | `/memoires/{id}` | Rôle | Détails mémoire |
| DELETE | `/memoires/{id}` | Propriétaire | Supprimer |
| POST | `/analyses/{memoire_id}` | Étudiant/Admin | Lancer analyse |
| GET | `/analyses/{memoire_id}` | Rôle | Rapport d'analyse |
| PATCH | `/validations/{memoire_id}` | Encadreur | Valider/Rejeter |
| GET | `/notifications` | Auth | Mes notifications |
| PATCH | `/notifications/{id}/read` | Auth | Marquer lue |
| GET | `/admin/stats` | Admin | Statistiques |
