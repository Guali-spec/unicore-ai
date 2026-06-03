# Phase 1 — Conception UML — UniCore AI

## Introduction

Ce document présente la conception fonctionnelle et technique de la plateforme UniCore AI à travers les diagrammes UML fondamentaux. Ces artefacts constituent la référence de conception pour toutes les phases de développement suivantes.

---

## 1. Diagramme de Cas d'Utilisation

### 1.1 Acteurs du système

| Acteur | Type | Description |
|---|---|---|
| **Étudiant** | Primaire | Soumet ses mémoires et consulte les résultats d'analyse |
| **Encadreur** | Primaire | Examine, commente et valide les mémoires |
| **Administrateur** | Primaire | Gère les utilisateurs et supervise la plateforme |
| **Super Administrateur** | Primaire | Accès total, configuration système |
| **Système IA** | Secondaire | Déclenche l'analyse automatique des documents |

### 1.2 Diagramme — Vue PlantUML

```plantuml
@startuml UniCore_AI_Use_Cases

skinparam actorStyle awesome
skinparam packageStyle rectangle
skinparam backgroundColor #FAFAFA
skinparam ArrowColor #333333
skinparam ActorBorderColor #01696f
skinparam UsecaseBorderColor #01696f
skinparam UsecaseBackgroundColor #e8f4f5

title Diagramme de Cas d'Utilisation — UniCore AI

' ==================== ACTEURS ====================
actor "Étudiant" as ETU #01696f
actor "Encadreur" as ENC #437a22
actor "Administrateur" as ADM #da7101
actor "Super Admin" as SPA #a12c7b
actor "Système IA" as SIA #006494

' ==================== SYSTÈME ====================
rectangle "UniCore AI — Plateforme Académique" {

  ' --- Authentification (tous) ---
  package "Authentification" {
    usecase "S'inscrire" as UC01
    usecase "Se connecter" as UC02
    usecase "Réinitialiser le mot de passe" as UC03
    usecase "Gérer son profil" as UC04
  }

  ' --- Espace Étudiant ---
  package "Gestion des Mémoires" {
    usecase "Soumettre un mémoire (PDF)" as UC05
    usecase "Consulter ses soumissions" as UC06
    usecase "Consulter le rapport d'analyse" as UC07
    usecase "Consulter le score d'originalité" as UC08
    usecase "Lire les recommandations" as UC09
    usecase "Suivre le statut de validation" as UC10
  }

  ' --- Module IA ---
  package "Analyse Intelligente" {
    usecase "Extraire le texte du PDF" as UC11
    usecase "Générer les embeddings" as UC12
    usecase "Calculer la similarité cosinus" as UC13
    usecase "Calculer le score d'originalité" as UC14
    usecase "Générer les recommandations" as UC15
  }

  ' --- Espace Encadreur ---
  package "Validation Académique" {
    usecase "Consulter les mémoires à valider" as UC16
    usecase "Lire le rapport d'analyse IA" as UC17
    usecase "Ajouter un commentaire" as UC18
    usecase "Valider un mémoire" as UC19
    usecase "Rejeter un mémoire" as UC20
    usecase "Archiver un mémoire validé" as UC21
  }

  ' --- Espace Admin ---
  package "Administration" {
    usecase "Gérer les utilisateurs" as UC22
    usecase "Consulter les statistiques globales" as UC23
    usecase "Gérer les établissements" as UC24
    usecase "Configurer le système" as UC25
    usecase "Superviser toute la plateforme" as UC26
  }

  ' --- Notifications ---
  package "Notifications" {
    usecase "Recevoir une notification" as UC27
    usecase "Envoyer une notification" as UC28
  }
}

' ==================== RELATIONS ACTEURS ====================

' Étudiant
ETU --> UC01
ETU --> UC02
ETU --> UC03
ETU --> UC04
ETU --> UC05
ETU --> UC06
ETU --> UC07
ETU --> UC08
ETU --> UC09
ETU --> UC10
ETU --> UC27

' Encadreur
ENC --> UC02
ENC --> UC04
ENC --> UC16
ENC --> UC17
ENC --> UC18
ENC --> UC19
ENC --> UC20
ENC --> UC21
ENC --> UC27

' Administrateur
ADM --> UC02
ADM --> UC04
ADM --> UC22
ADM --> UC23
ADM --> UC24
ADM --> UC27

' Super Admin
SPA --> UC25
SPA --> UC26
SPA --> UC22
SPA --> UC02

' Système IA (déclenché automatiquement)
SIA --> UC11
SIA --> UC12
SIA --> UC13
SIA --> UC14
SIA --> UC15
SIA --> UC28

' ==================== RELATIONS INCLUDE / EXTEND ====================

' La soumission inclut toujours le déclenchement de l'analyse IA
UC05 .> UC11 : <<include>>
UC11 .> UC12 : <<include>>
UC12 .> UC13 : <<include>>
UC13 .> UC14 : <<include>>
UC14 .> UC15 : <<include>>

' Consulter le rapport inclut le score et les recommandations
UC07 .> UC08 : <<include>>
UC07 .> UC09 : <<include>>

' La validation peut déclencher l'archivage
UC19 .> UC21 : <<extend>>

' Toute action importante génère une notification
UC05 .> UC28 : <<extend>>
UC19 .> UC28 : <<extend>>
UC20 .> UC28 : <<extend>>

@enduml
```

### 1.3 Description textuelle des cas d'utilisation principaux

#### UC05 — Soumettre un mémoire

| Élément | Détail |
|---|---|
| **Acteur principal** | Étudiant |
| **Précondition** | L'étudiant est authentifié |
| **Déclencheur** | L'étudiant clique sur "Déposer mon mémoire" |
| **Scénario nominal** | 1. L'étudiant remplit le formulaire (titre, résumé, fichier PDF) → 2. Le système valide le fichier → 3. Le document est enregistré → 4. L'analyse IA est déclenchée automatiquement → 5. Une notification est envoyée |
| **Scénarios alternatifs** | Fichier non PDF → message d'erreur / Taille dépassée → rejet |
| **Postcondition** | Le mémoire est enregistré avec le statut `EN_ATTENTE` |

#### UC19 — Valider un mémoire

| Élément | Détail |
|---|---|
| **Acteur principal** | Encadreur |
| **Précondition** | L'encadreur est authentifié / le rapport d'analyse est disponible |
| **Déclencheur** | L'encadreur ouvre un mémoire à valider |
| **Scénario nominal** | 1. L'encadreur consulte le rapport IA → 2. Il lit le document → 3. Il ajoute un commentaire (optionnel) → 4. Il clique sur "Valider" → 5. Le mémoire passe au statut `VALIDÉ` → 6. Le mémoire est archivé automatiquement → 7. L'étudiant est notifié |
| **Scénarios alternatifs** | Rejet → statut `REJETÉ`, retour à l'étudiant avec commentaires |
| **Postcondition** | Le mémoire est archivé ou retourné à l'étudiant |

---

## 2. Diagramme de Classes

### 2.1 Concept

Le diagramme de classes modélise les entités métier du système, leurs attributs et leurs relations. Chaque classe correspond à une table de base de données et à un modèle SQLAlchemy.

### 2.2 Diagramme — Vue PlantUML

```plantuml
@startuml UniCore_AI_Classes

skinparam classAttributeIconSize 0
skinparam backgroundColor #FAFAFA
skinparam classBorderColor #01696f
skinparam classBackgroundColor #f0fafa
skinparam ArrowColor #333333
skinparam packageBackgroundColor #f9f9f9

title Diagramme de Classes Métier — UniCore AI

' ==================== ÉNUMÉRATIONS ====================

enum RoleUtilisateur {
  ETUDIANT
  ENCADREUR
  ADMINISTRATEUR
  SUPER_ADMIN
}

enum StatutMemoire {
  BROUILLON
  EN_ATTENTE
  EN_ANALYSE
  ANALYSE_TERMINEE
  EN_VALIDATION
  VALIDE
  REJETE
  ARCHIVE
}

enum TypeNotification {
  SOUMISSION_RECUE
  ANALYSE_TERMINEE
  VALIDATION_DEMANDEE
  MEMOIRE_VALIDE
  MEMOIRE_REJETE
  SYSTEME
}

' ==================== CLASSES MÉTIER ====================

class User {
  + id : UUID
  + nom : String
  + prenom : String
  + email : String
  + mot_de_passe_hash : String
  + role : RoleUtilisateur
  + est_actif : Boolean
  + date_creation : DateTime
  + date_modification : DateTime
  --
  + verifier_mot_de_passe(mdp: String) : Boolean
  + generer_token() : String
  + desactiver() : void
}

class Memoire {
  + id : UUID
  + titre : String
  + resume : String
  + mots_cles : List<String>
  + annee_academique : String
  + domaine : String
  + statut : StatutMemoire
  + date_soumission : DateTime
  + date_modification : DateTime
  --
  + soumettre() : void
  + changer_statut(statut: StatutMemoire) : void
  + est_analysable() : Boolean
}

class Document {
  + id : UUID
  + nom_fichier : String
  + chemin_stockage : String
  + taille_octets : Integer
  + type_mime : String
  + hash_sha256 : String
  + date_upload : DateTime
  --
  + valider_format() : Boolean
  + obtenir_url() : String
}

class AnalysisReport {
  + id : UUID
  + score_originalite : Float
  + score_similarite_max : Float
  + nombre_comparaisons : Integer
  + resume_analyse : String
  + statut_analyse : String
  + duree_traitement_ms : Integer
  + date_analyse : DateTime
  --
  + est_complet() : Boolean
  + obtenir_niveau_originalite() : String
}

class Embedding {
  + id : UUID
  + vecteur : Vector(384)
  + modele_utilise : String
  + version_modele : String
  + date_generation : DateTime
  --
  + calculer_similarite(autre: Embedding) : Float
}

class SimilarityResult {
  + id : UUID
  + score_similarite : Float
  + passages_similaires : JSON
  + date_calcul : DateTime
  --
  + est_similaire(seuil: Float) : Boolean
}

class ValidationReview {
  + id : UUID
  + decision : String
  + commentaire : String
  + points_forts : String
  + points_amelioration : String
  + date_decision : DateTime
  --
  + valider() : void
  + rejeter(raison: String) : void
}

class Recommendation {
  + id : UUID
  + type_recommandation : String
  + contenu : String
  + priorite : Integer
  + date_generation : DateTime
}

class Notification {
  + id : UUID
  + titre : String
  + message : String
  + type : TypeNotification
  + est_lue : Boolean
  + date_creation : DateTime
  + date_lecture : DateTime
  --
  + marquer_lue() : void
}

class AuditLog {
  + id : UUID
  + action : String
  + entite : String
  + entite_id : UUID
  + details : JSON
  + adresse_ip : String
  + date_action : DateTime
}

' ==================== RELATIONS ====================

' Un User peut soumettre plusieurs Mémoires
User "1" o-- "0..*" Memoire : soumet >

' Un Memoire a exactement un Document (fichier PDF)
Memoire "1" *-- "1" Document : contient >

' Un Memoire a un rapport d'analyse (après traitement)
Memoire "1" o-- "0..1" AnalysisReport : génère >

' Un Memoire a un Embedding vectoriel
Memoire "1" *-- "0..1" Embedding : vectorisé en >

' Un AnalysisReport contient plusieurs résultats de similarité
AnalysisReport "1" *-- "0..*" SimilarityResult : contient >

' Une SimilarityResult compare deux mémoires
SimilarityResult "0..*" --> "1" Memoire : référence (source) >
SimilarityResult "0..*" --> "1" Memoire : référence (cible) >

' Un AnalysisReport génère des recommandations
AnalysisReport "1" *-- "0..*" Recommendation : produit >

' Un Encadreur peut émettre des ValidationReview
User "1" o-- "0..*" ValidationReview : émet >

' Un Memoire reçoit une ValidationReview
Memoire "1" o-- "0..1" ValidationReview : reçoit >

' Un Memoire a un encadreur assigné
Memoire "0..*" --> "1" User : encadré par >

' Notifications liées à un User
User "1" o-- "0..*" Notification : reçoit >

' AuditLog lié à un User
User "1" o-- "0..*" AuditLog : génère >

' Relations avec les énumérations
User ..> RoleUtilisateur : utilise
Memoire ..> StatutMemoire : utilise
Notification ..> TypeNotification : utilise

@enduml
```

---

## 3. Diagrammes de Séquence

### 3.1 Séquence 1 — Soumission et analyse d'un mémoire

Ce diagramme modélise le flux complet depuis le dépôt d'un mémoire jusqu'à la disponibilité du rapport d'analyse.

```plantuml
@startuml Sequence_Soumission_Analyse

skinparam sequenceArrowThickness 2
skinparam sequenceParticipantBorderColor #01696f
skinparam sequenceParticipantBackgroundColor #f0fafa
skinparam sequenceLifeLineBorderColor #cccccc
skinparam backgroundColor #FAFAFA

title Séquence — Soumission et Analyse d'un Mémoire

actor "Étudiant" as ETU
participant "Frontend\n(Next.js)" as FE
participant "API Backend\n(FastAPI)" as API
participant "Service\nMémoires" as SVC_MEM
participant "Module IA\n(Pipeline)" as SVC_IA
database "PostgreSQL\n+ pgvector" as DB
participant "Service\nNotifications" as SVC_NOTIF

== Authentification préalable ==
ETU -> FE : Connexion (email + mdp)
FE -> API : POST /auth/login
API -> DB : Vérifier credentials
DB --> API : User trouvé + hash valide
API --> FE : Token JWT
FE --> ETU : Accès accordé

== Soumission du mémoire ==
ETU -> FE : Remplit le formulaire\n(titre, résumé, PDF)
FE -> FE : Validation côté client\n(format, taille ≤ 20Mo)
FE -> API : POST /api/v1/memoires/\n[Bearer Token + FormData]

API -> API : Vérifier Token JWT
API -> API : Vérifier rôle ÉTUDIANT
API -> SVC_MEM : creer_memoire(donnees, fichier)

SVC_MEM -> SVC_MEM : Valider le fichier PDF
SVC_MEM -> SVC_MEM : Calculer hash SHA-256
SVC_MEM -> DB : INSERT INTO documents
SVC_MEM -> DB : INSERT INTO memoires\n(statut = EN_ATTENTE)
DB --> SVC_MEM : memoire_id généré

SVC_MEM --> API : Mémoire créé (id, statut)
API --> FE : 201 Created {memoire_id, statut}
FE --> ETU : "Mémoire soumis avec succès !"

== Déclenchement asynchrone de l'analyse IA ==
SVC_MEM -> SVC_IA : analyser_memoire(memoire_id)\n[tâche asynchrone]
SVC_MEM -> DB : UPDATE memoires\nSET statut = EN_ANALYSE

SVC_IA -> DB : Lire le fichier PDF
SVC_IA -> SVC_IA : Extraire le texte brut\n(PyMuPDF)
SVC_IA -> SVC_IA : Nettoyer et segmenter\nle texte
SVC_IA -> SVC_IA : Générer les embeddings\n(MiniLM — 384 dimensions)

SVC_IA -> DB : INSERT INTO embeddings\n(vecteur pgvector)

SVC_IA -> DB : SELECT embeddings\nFROM tous les autres mémoires

loop Pour chaque mémoire existant
  SVC_IA -> SVC_IA : Calculer similarité cosinus
  SVC_IA -> DB : INSERT INTO similarity_results\n(source_id, cible_id, score)
end

SVC_IA -> SVC_IA : Calculer score d'originalité\n(100% - max_similarité)
SVC_IA -> SVC_IA : Générer les recommandations

SVC_IA -> DB : INSERT INTO analysis_reports
SVC_IA -> DB : INSERT INTO recommendations
SVC_IA -> DB : UPDATE memoires\nSET statut = ANALYSE_TERMINEE

== Notification à l'étudiant ==
SVC_IA -> SVC_NOTIF : notifier(etudiant_id,\n"Analyse terminée")
SVC_NOTIF -> DB : INSERT INTO notifications
SVC_NOTIF --> ETU : Notification temps réel

ETU -> FE : Consulter le rapport
FE -> API : GET /api/v1/analyses/{memoire_id}
API -> DB : SELECT analysis_reports\n+ similarity_results\n+ recommendations
DB --> API : Rapport complet
API --> FE : 200 OK {rapport JSON}
FE --> ETU : Affichage du score\net des recommandations

@enduml
```

### 3.2 Séquence 2 — Validation académique par l'encadreur

```plantuml
@startuml Sequence_Validation

skinparam sequenceArrowThickness 2
skinparam sequenceParticipantBorderColor #437a22
skinparam sequenceParticipantBackgroundColor #f0f8f0
skinparam backgroundColor #FAFAFA

title Séquence — Validation Académique

actor "Encadreur" as ENC
participant "Frontend\n(Next.js)" as FE
participant "API Backend\n(FastAPI)" as API
participant "Service\nValidation" as SVC_VAL
database "PostgreSQL" as DB
participant "Service\nNotifications" as SVC_NOTIF
actor "Étudiant" as ETU

== Consultation des mémoires à valider ==
ENC -> FE : Accès espace encadreur
FE -> API : GET /api/v1/memoires/\n?statut=ANALYSE_TERMINEE
API -> API : Vérifier rôle ENCADREUR
API -> DB : SELECT memoires WHERE\nencadreur_id = current_user\nAND statut = ANALYSE_TERMINEE
DB --> API : Liste des mémoires
API --> FE : 200 OK [liste]
FE --> ENC : Tableau de bord\ndes mémoires à examiner

== Examen d'un mémoire ==
ENC -> FE : Ouvre un mémoire
FE -> API : GET /api/v1/memoires/{id}
API -> DB : SELECT memoire + document\n+ rapport + similarités
DB --> API : Données complètes
API --> FE : 200 OK {mémoire complet}
FE --> ENC : Affichage du document PDF\net du rapport IA

API -> DB : UPDATE memoires\nSET statut = EN_VALIDATION

== Décision de validation ==
alt Validation acceptée
  ENC -> FE : Ajoute un commentaire\n+ clique "Valider"
  FE -> API : PATCH /api/v1/validations/{memoire_id}\n{decision: "VALIDE", commentaire}
  API -> SVC_VAL : enregistrer_validation(memoire_id,\n encadreur_id, decision, commentaire)
  SVC_VAL -> DB : INSERT INTO validation_reviews\n(decision = VALIDE)
  SVC_VAL -> DB : UPDATE memoires\nSET statut = VALIDE
  SVC_VAL -> DB : INSERT INTO archives\n(mémoire archivé automatiquement)
  SVC_VAL --> API : Validation enregistrée
  API --> FE : 200 OK
  FE --> ENC : "Mémoire validé et archivé"
  SVC_VAL -> SVC_NOTIF : notifier(etudiant_id,\n"Votre mémoire a été validé")
  SVC_NOTIF --> ETU : 🎉 Notification de validation

else Validation rejetée
  ENC -> FE : Ajoute raison du rejet\n+ clique "Rejeter"
  FE -> API : PATCH /api/v1/validations/{memoire_id}\n{decision: "REJETE", commentaire}
  API -> SVC_VAL : enregistrer_validation(memoire_id,\n encadreur_id, "REJETE", commentaire)
  SVC_VAL -> DB : INSERT INTO validation_reviews\n(decision = REJETE)
  SVC_VAL -> DB : UPDATE memoires\nSET statut = REJETE
  SVC_VAL --> API : Rejet enregistré
  API --> FE : 200 OK
  FE --> ENC : "Mémoire rejeté — étudiant notifié"
  SVC_VAL -> SVC_NOTIF : notifier(etudiant_id,\n"Votre mémoire a été rejeté")
  SVC_NOTIF --> ETU : ⚠️ Notification de rejet\navec commentaires
end

@enduml
```

### 3.3 Séquence 3 — Authentification JWT

```plantuml
@startuml Sequence_Auth

skinparam sequenceArrowThickness 2
skinparam backgroundColor #FAFAFA

title Séquence — Authentification JWT

actor "Utilisateur" as USR
participant "Frontend" as FE
participant "API /auth" as API
participant "AuthService" as AUTH
database "PostgreSQL" as DB

== Inscription ==
USR -> FE : Formulaire inscription
FE -> API : POST /api/v1/auth/register\n{nom, email, password, role}
API -> AUTH : creer_utilisateur(donnees)
AUTH -> AUTH : Valider email unique
AUTH -> AUTH : Hacher le mot de passe\n(bcrypt, cost=12)
AUTH -> DB : INSERT INTO users
DB --> AUTH : user_id
AUTH --> API : User créé
API --> FE : 201 Created
FE --> USR : "Compte créé — veuillez vous connecter"

== Connexion ==
USR -> FE : Email + mot de passe
FE -> API : POST /api/v1/auth/login\n{email, password}
API -> AUTH : authentifier(email, password)
AUTH -> DB : SELECT user WHERE email = ?
DB --> AUTH : User (hash inclus)
AUTH -> AUTH : Vérifier bcrypt(password, hash)

alt Credentials valides
  AUTH -> AUTH : Générer access_token (JWT, 30min)
  AUTH -> AUTH : Générer refresh_token (JWT, 7j)
  AUTH -> DB : Stocker refresh_token
  AUTH --> API : {access_token, refresh_token}
  API --> FE : 200 OK {tokens}
  FE -> FE : Stocker tokens en mémoire
  FE --> USR : Redirection dashboard

else Credentials invalides
  AUTH --> API : Erreur authentification
  API --> FE : 401 Unauthorized
  FE --> USR : "Email ou mot de passe incorrect"
end

== Requête authentifiée ==
USR -> FE : Action (ex: soumettre mémoire)
FE -> API : POST /api/v1/memoires/\nAuthorization: Bearer <access_token>
API -> AUTH : verifier_token(access_token)
AUTH -> AUTH : Décoder JWT\nVérifier signature + expiration
AUTH -> AUTH : Extraire user_id + role
AUTH --> API : Utilisateur authentifié {id, role}
API -> API : Vérifier autorisation\n(rôle suffisant ?)
API --> FE : Réponse métier

== Renouvellement du token ==
FE -> API : POST /api/v1/auth/refresh\n{refresh_token}
API -> AUTH : renouveler_token(refresh_token)
AUTH -> DB : Vérifier refresh_token valide
AUTH -> AUTH : Générer nouveau access_token
AUTH --> API : {nouveau access_token}
API --> FE : 200 OK {access_token}

@enduml
```

---

## Récapitulatif des artefacts UML

| Diagramme | Fichier | Usage |
|---|---|---|
| Cas d'utilisation | Ce document, section 1 | Définir le périmètre fonctionnel |
| Classes métier | Ce document, section 2 | Base pour SQLAlchemy et Pydantic |
| Séquence — Soumission/Analyse | Ce document, section 3.1 | Guide l'implémentation du pipeline IA |
| Séquence — Validation | Ce document, section 3.2 | Guide l'implémentation du workflow métier |
| Séquence — Auth JWT | Ce document, section 3.3 | Guide l'implémentation de la sécurité |

> Ces diagrammes utilisent la notation **PlantUML**. Ils peuvent être rendus avec [PlantUML Online](https://www.plantuml.com/plantuml/uml/) ou l'extension VS Code **PlantUML**.
