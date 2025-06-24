# TERMES DE RÉFÉRENCE (TDR)
## Développement d'une API REST FastAPI et d'un Chatbot IA pour la consultation des résultats d'examens

**Université de Mahajanga - Faculté de Médecine**  
**Stage de développement informatique**  
**Durée : 3 mois**  
**Date de rédaction : Juin 2025**

---

## 📋 TABLE DES MATIÈRES

1. [Contexte et Justification](#1-contexte-et-justification)
2. [Objectifs du Stage](#2-objectifs-du-stage)
3. [Architecture Technique](#3-architecture-technique)
4. [Spécifications Techniques Détaillées](#4-spécifications-techniques-détaillées)
5. [Système d'Identification des Étudiants](#5-système-didentification-des-étudiants)
6. [Livrables et Fonctionnalités](#6-livrables-et-fonctionnalités)
7. [Planning Détaillé](#7-planning-détaillé)
8. [Stack Technologique](#8-stack-technologique)
9. [Tests et Validation](#9-tests-et-validation)
10. [Déploiement et Production](#10-déploiement-et-production)
11. [Documentation Requise](#11-documentation-requise)
12. [Critères d'Évaluation](#12-critères-dévaluation)

---

## 1. CONTEXTE ET JUSTIFICATION

### 1.1 Situation Actuelle

L'Université de Mahajanga dispose actuellement d'une **application Laravel robuste** pour la gestion des examens comprenant :
- Interface d'administration complète pour les professeurs et administrateurs
- Système de gestion des étudiants, examens, et corrections
- Base de données structurée avec la table `resultats_finaux`
- Workflow complet de validation et publication des résultats

### 1.2 Besoins Identifiés

**Modernisation de l'accès aux données :**
- Développement d'une API REST moderne pour l'accès programmatique aux données
- Création d'une interface conversationnelle intelligente (Chatbot IA)
- Assurer une disponibilité 24h/24 pour la consultation par les étudiants
- Réduire la charge administrative du personnel

### 1.3 Architecture Hybride Proposée

```mermaid
graph TB
    A[Application Laravel<br/>🚀 Gestion Admin] --> D[Base de Données<br/>📊 resultats_finaux]
    B[API REST FastAPI<br/>⚡ Endpoints Modernes] --> D
    C[Chatbot IA Python<br/>🤖 Assistant Intelligent] --> B
    E[Interface Web] --> B
    F[Applications Mobiles] --> B
    G[Étudiants] --> C
    G --> E
    
    style A fill:#ff6b6b
    style B fill:#4ecdc4
    style C fill:#45b7d1
    style D fill:#96ceb4
```

### 1.4 Justification de l'Architecture

| Aspect | Bénéfice |
|--------|----------|
| **Séparation des responsabilités** | Laravel = Administration, FastAPI = Consultation, Chatbot = Interaction |
| **Performance** | FastAPI plus rapide que Laravel pour les APIs |
| **Évolutivité** | Chaque service peut évoluer indépendamment |
| **Maintenance** | Aucun risque sur l'application Laravel existante |
| **Technologies modernes** | Stack contemporaine pour le développement |

---

## 2. OBJECTIFS DU STAGE

### 2.1 Objectif Principal

Développer une **couche d'accès moderne et intelligente** aux données d'examens existantes, en créant :
1. Une API REST performante avec FastAPI
2. Un chatbot IA conversationnel en Python
3. Une intégration transparente avec le système Laravel existant

### 2.2 Objectifs Pédagogiques

- **Maîtriser FastAPI** : Framework Python moderne pour le développement d'APIs
- **Développer en Intelligence Artificielle** : NLP, classification d'intentions, génération de réponses
- **Comprendre les microservices** : Architecture distribuée et communication inter-services
- **Intégration système** : Connexion avec une application existante
- **Sécurité API** : Authentification, autorisation, protection des données

### 2.3 Objectifs Techniques Mesurables

| Objectif | Indicateur de Réussite |
|----------|----------------------|
| **API REST** | 15+ endpoints fonctionnels avec documentation complète |
| **Performance** | Réponses API < 500ms pour 95% des requêtes |
| **Chatbot** | Compréhension de 10+ types de questions différentes |
| **Sécurité** | Authentification JWT + autorisation par rôle |
| **Tests** | Couverture de code ≥ 80% |
| **Documentation** | Guide utilisateur et technique complets |

---

## 3. ARCHITECTURE TECHNIQUE

### 3.1 Vue d'Ensemble du Système

```yaml
Services Architecture:
  laravel-app:
    purpose: "Gestion administrative et interface professeurs"
    technology: "PHP Laravel"
    port: 8000
    status: "Existant - Ne pas modifier"
    
  fastapi-rest:
    purpose: "API REST moderne pour consultation"
    technology: "Python FastAPI"
    port: 8001
    status: "À développer"
    
  chatbot-ia:
    purpose: "Assistant intelligent conversationnel"
    technology: "Python + spaCy/Rasa"
    port: 8002
    status: "À développer"
    
  database:
    purpose: "Base de données partagée"
    technology: "MySQL 8.0"
    tables: "resultats_finaux + relations"
    status: "Existant - Lecture seule pour les nouveaux services"
```

### 3.2 Flux de Données

```mermaid
sequenceDiagram
    participant E as Étudiant
    participant C as Chatbot IA
    participant A as API FastAPI
    participant D as Base MySQL
    participant L as Laravel App

    E->>C: "Quelle est ma note en anatomie ?"
    C->>C: Analyse NLP (intention + entité)
    C->>A: GET /api/v1/students/123/results?ec=anatomie
    A->>D: SELECT * FROM resultats_finaux WHERE...
    D->>A: Données résultats
    A->>C: Réponse JSON structurée
    C->>C: Génération réponse naturelle
    C->>E: "Votre note en Anatomie est 15/20 (Admis)"
    
    Note over L,D: Laravel continue à gérer<br/>la création/modification des résultats
```

---

## 4. SPÉCIFICATIONS TECHNIQUES DÉTAILLÉES

### 4.1 Structure de la Base de Données

#### 4.1.1 Table Principale : `resultats_finaux`

```sql
-- Structure existante à exploiter (LECTURE SEULE)
CREATE TABLE resultats_finaux (
    id BIGINT UNSIGNED PRIMARY KEY,
    etudiant_id BIGINT UNSIGNED NOT NULL,        -- 🎯 Identification étudiant
    examen_id BIGINT UNSIGNED NOT NULL,          -- 📝 Référence examen
    session_exam_id BIGINT UNSIGNED NULL,        -- 🔄 Session (normale/rattrapage)
    code_anonymat_id BIGINT UNSIGNED NOT NULL,   -- 🎭 Code anonymisation
    ec_id BIGINT UNSIGNED NOT NULL,              -- 📚 Matière/module
    note DECIMAL(5,2) NOT NULL,                  -- 📊 Note finale (/20)
    statut ENUM('en_attente', 'publie', 'annule') DEFAULT 'en_attente',
    decision ENUM('admis', 'rattrapage', 'redoublant', 'exclus') NULL,
    date_publication TIMESTAMP NULL,             -- 📅 Date mise à disposition
    jury_validated BOOLEAN DEFAULT FALSE,       -- ✅ Validation jury
    genere_par BIGINT UNSIGNED NOT NULL,        -- 👤 Traçabilité création
    modifie_par BIGINT UNSIGNED NULL,           -- 👤 Traçabilité modification
    hash_verification VARCHAR(64) NULL,         -- 🔐 Intégrité (NE PAS EXPOSER)
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

#### 4.1.2 Tables Liées (Relations)

```sql
-- Tables existantes nécessaires pour l'API
etudiants:        -- Informations étudiants
  - id, nom, prenom, email, numero_matricule, numero_etudiant

examens:          -- Détails des examens  
  - id, intitule, date_examen, coefficient

ecs:              -- Éléments constitutifs (matières)
  - id, nom, code, coefficient, semestre

session_exams:    -- Sessions d'examen
  - id, type (normale/rattrapage), date_debut, date_fin

users:            -- Utilisateurs système
  - id, name, email, role (student/teacher/admin)
```

### 4.2 API REST FastAPI - Structure du Projet

```
exam-api/
├── 📁 app/
│   ├── 📄 main.py                    # Point d'entrée FastAPI
│   ├── 📄 database.py                # Configuration SQLAlchemy
│   ├── 📄 dependencies.py            # Dépendances globales
│   ├── 📁 models/                    # Modèles SQLAlchemy
│   │   ├── 📄 __init__.py
│   │   ├── 📄 resultats.py           # Modèle resultats_finaux
│   │   ├── 📄 etudiants.py           # Modèle étudiants
│   │   ├── 📄 examens.py             # Modèle examens
│   │   └── 📄 users.py               # Modèle authentification
│   ├── 📁 schemas/                   # Schémas Pydantic
│   │   ├── 📄 __init__.py
│   │   ├── 📄 resultats.py           # Schemas réponses
│   │   ├── 📄 auth.py                # Schemas authentification
│   │   └── 📄 common.py              # Schemas communs
│   ├── 📁 routers/                   # Endpoints groupés
│   │   ├── 📄 __init__.py
│   │   ├── 📄 auth.py                # Authentification
│   │   ├── 📄 students.py            # Endpoints étudiants
│   │   ├── 📄 admin.py               # Endpoints administration
│   │   └── 📄 chatbot.py             # Endpoints pour chatbot
│   ├── 📁 core/                      # Configuration & sécurité
│   │   ├── 📄 __init__.py
│   │   ├── 📄 config.py              # Variables d'environnement
│   │   ├── 📄 security.py            # JWT, hashing, permissions
│   │   └── 📄 exceptions.py          # Gestion erreurs custom
│   └── 📁 utils/                     # Utilitaires
│       ├── 📄 __init__.py
│       ├── 📄 helpers.py             # Fonctions helper
│       └── 📄 validators.py          # Validations métier
├── 📁 tests/                         # Tests automatisés
├── 📁 docs/                          # Documentation
├── 📄 requirements.txt               # Dépendances Python
├── 📄 .env.example                   # Variables d'environnement
├── 📄 Dockerfile                     # Conteneurisation
└── 📄 README.md                      # Documentation projet
```

### 4.3 Endpoints API Détaillés

#### 4.3.1 Authentification

```python
# POST /api/v1/auth/login-matricule
{
  "numero_matricule": "2024-MED-001",
  "password": "securepassword"
}

# POST /api/v1/auth/login-nom
{
  "nom": "CROLAS",
  "prenom": "Paul", 
  "password": "securepassword"
}

# POST /api/v1/auth/login-email
{
  "email": "paul.crolas@univ-mahajanga.mg",
  "password": "securepassword"
}

# Response commune
{
  "access_token": "eyJ...", 
  "token_type": "bearer", 
  "expires_in": 3600,
  "student_info": {
    "id": 123,
    "numero_matricule": "2024-MED-001",
    "nom": "CROLAS",
    "prenom": "Paul",
    "email": "paul.crolas@univ-mahajanga.mg"
  }
}
```

#### 4.3.2 Endpoints Étudiants

```python
# GET /api/v1/students/{etudiant_id}/results
# Tous les résultats publiés d'un étudiant
# Query params: ?session_id=1&ec_id=15&limit=10&offset=0

# GET /api/v1/students/{etudiant_id}/results/{examen_id}
# Résultats d'un examen spécifique

# GET /api/v1/students/{etudiant_id}/statistics
# Statistiques personnalisées : moyennes, évolution, classement

# GET /api/v1/students/{etudiant_id}/decisions
# Décisions d'admission par matière/semestre

# GET /api/v1/students/{etudiant_id}/ranking
# Classement relatif (si autorisé)

# GET /api/v1/students/{etudiant_id}/transcript
# Relevé de notes complet
```

#### 4.3.3 Endpoints Chatbot

```python
# GET /api/v1/chatbot/student-search
# Query params pour identification flexible :
# ?matricule=2024-MED-001
# ?nom=CROLAS&prenom=Paul
# ?email=paul.crolas@univ-mahajanga.mg

# POST /api/v1/chatbot/identify-student
# Body: {"text": "Je suis Paul Crolas, matricule 2024-MED-001"}
# Extraction automatique d'identité depuis texte libre

# GET /api/v1/chatbot/quick-stats/{etudiant_id}
# Statistiques rapides pour réponses chatbot

# POST /api/v1/chatbot/natural-query
{
  "student_id": 123,
  "query": "ma moyenne en cardiologie",
  "context": {...}
}
```

---

## 5. SYSTÈME D'IDENTIFICATION DES ÉTUDIANTS

### 5.1 Méthodes d'Authentification Multiples

**Priorité 1 : Numéro Matricule (Recommandé)**
```python
# Format matricule standard
MATRICULE_PATTERN = r"^20\d{2}-[A-Z]{2,4}-\d{3}$"
# Exemples : 2024-MED-001, 2023-CHIR-156, 2025-DENT-089
```

**Priorité 2 : Nom + Prénom**
```python
# Recherche avec normalisation (majuscules, accents)
# Gestion des homonymes avec suggestions
```

**Priorité 3 : Email Universitaire**
```python
# Format email standard : prenom.nom@univ-mahajanga.mg
EMAIL_PATTERN = r"^[a-z]+\.[a-z]+@univ-mahajanga\.mg$"
```

### 5.2 Extraction NLP d'Identité pour le Chatbot

```python
# Patterns d'extraction automatique
examples = [
    "Je suis Paul Crolas" → {prenom: "Paul", nom: "Crolas"},
    "Mon matricule est 2024-MED-001" → {matricule: "2024-MED-001"},
    "Paul CROLAS 2024-MED-001" → {prenom: "Paul", nom: "CROLAS", matricule: "2024-MED-001"}
]
```

### 5.3 Entités d'Identification

```json
{
    "nom": {
        "type": "nom de famille",
        "extraction": "NER + regex patterns",
        "examples": ["CROLAS", "RAKOTO", "ANDRIAMAHEFA"]
    },
    
    "prenom": {
        "type": "prénom",
        "extraction": "NER + base prénoms malgaches/français", 
        "examples": ["Paul", "Marie", "Jean", "Hery"]
    },
    
    "numero_matricule": {
        "type": "numéro matricule étudiant",
        "pattern": "YYYY-MED-XXX ou similaire",
        "regex": "20\\d{2}-[A-Z]{3}-\\d{3}",
        "examples": ["2024-MED-001", "2023-MED-156", "2025-CHI-089"]
    },
    
    "numero_etudiant": {
        "type": "numéro étudiant alternatif",
        "pattern": "numérique ou alphanumerique",
        "examples": ["ET001234", "123456789", "MED2024001"]
    }
}
```

### 5.4 Exemples d'Identification Automatique

```json
{
    "student_identification": {
        "description": "Identification de l'étudiant",
        "examples": [
            "Je suis Paul Crolas",
            "Mon matricule est 2024-MED-001", 
            "Je m'appelle Marie Rakoto, numéro 2024-MED-045",
            "Mon nom c'est ANDRY Jean"
        ],
        "entities": ["nom", "prenom", "numero_matricule", "numero_etudiant"]
    }
}
```

---

## 6. LIVRABLES ET FONCTIONNALITÉS

### 6.1 API REST FastAPI (70% du travail)

#### 6.1.1 Fonctionnalités Core

| Fonctionnalité | Description | Endpoints | Complexité |
|---------------|-------------|-----------|------------|
| **Authentification JWT** | Login/logout sécurisé | `/auth/*` | 🟢 Moyenne |
| **Consultation résultats** | Accès notes étudiants | `/students/{id}/results` | 🟡 Moyenne |
| **Statistiques personnelles** | Moyennes, évolution | `/students/{id}/statistics` | 🔴 Complexe |
| **Filtrage avancé** | Par matière, session, date | Query parameters | 🟡 Moyenne |
| **Permissions granulaires** | Étudiant vs Admin vs Prof | Middleware | 🔴 Complexe |
| **Documentation auto** | Swagger/OpenAPI | FastAPI natif | 🟢 Simple |

#### 6.1.2 Modèles de Données Pydantic

```python
from pydantic import BaseModel, Field
from datetime import datetime
from typing import Optional, List
from enum import Enum

class StatutEnum(str, Enum):
    EN_ATTENTE = "en_attente"
    PUBLIE = "publie" 
    ANNULE = "annule"

class DecisionEnum(str, Enum):
    ADMIS = "admis"
    RATTRAPAGE = "rattrapage"
    REDOUBLANT = "redoublant"
    EXCLUS = "exclus"

class ResultatResponse(BaseModel):
    id: int
    note: float = Field(..., ge=0, le=20)
    examen_titre: str
    ec_nom: str
    session_type: Optional[str] = None
    statut: StatutEnum
    decision: Optional[DecisionEnum] = None
    date_publication: Optional[datetime] = None
    jury_validated: bool
    
    class Config:
        from_attributes = True

class StatistiquesEtudiant(BaseModel):
    moyenne_generale: float
    total_examens: int
    total_admis: int
    total_rattrapage: int
    evolution_notes: List[float]
    matieres_forte: List[str]
    matieres_faible: List[str]
```

### 6.2 Chatbot IA (30% du travail)

#### 6.2.1 Structure du Chatbot

```
chatbot-ia/
├── 📁 app/
│   ├── 📄 main.py                    # Serveur FastAPI chatbot
│   ├── 📁 nlp/                       # Moteur NLP
│   │   ├── 📄 intent_classifier.py   # Classification intentions
│   │   ├── 📄 entity_extractor.py    # Extraction entités
│   │   ├── 📄 context_manager.py     # Gestion contexte
│   │   └── 📄 response_generator.py  # Génération réponses
│   ├── 📁 api_client/                # Client pour API REST
│   │   ├── 📄 exam_api_client.py     # Interface avec API FastAPI
│   │   └── 📄 cache_manager.py       # Cache Redis
│   ├── 📁 knowledge/                 # Base de connaissances
│   │   ├── 📄 faq_database.py        # FAQ automatisées
│   │   ├── 📄 ec_mapping.py          # Mapping matières
│   │   └── 📄 conversation_flows.py  # Flux conversation
├── 📁 data/                          # Données d'entraînement
├── 📁 tests/                         # Tests chatbot
└── 📁 docs/                          # Documentation
```

#### 6.2.2 Intentions et Entités

```python
# Classification des intentions utilisateur
INTENTS = {
    "get_grades": {
        "description": "Consultation des notes",
        "examples": [
            "Quelle est ma note en anatomie ?",
            "Mes résultats du dernier examen",
            "Combien j'ai eu en cardiologie ?",
            "Montre-moi mes notes"
        ],
        "entities": ["subject", "exam_type", "time_period"]
    },
    
    "get_average": {
        "description": "Calcul de moyennes",
        "examples": [
            "Quelle est ma moyenne générale ?",
            "Ma moyenne en anatomie ce semestre",
            "Moyenne de la promo en cardiologie"
        ],
        "entities": ["subject", "scope", "time_period"]
    },
    
    "get_admission_status": {
        "description": "Statut d'admission",
        "examples": [
            "Suis-je admis ?",
            "Ai-je validé mon semestre ?",
            "Est-ce que je passe en rattrapage ?",
            "Mes décisions d'admission"
        ],
        "entities": ["scope", "time_period"]
    }
}

# Entités nommées
ENTITIES = {
    "subject": {
        "type": "matière académique",
        "values": {
            "anatomie": ["anatomie", "anatomy", "anat"],
            "physiologie": ["physiologie", "physio"],
            "cardiologie": ["cardiologie", "cardio", "coeur"],
            "neurologie": ["neurologie", "neuro"],
            "pharmacologie": ["pharmacologie", "pharma"],
            "histologie": ["histologie", "histo"],
            "biochimie": ["biochimie", "biochem"]
        }
    },
    
    "time_period": {
        "type": "période temporelle",
        "values": {
            "ce_semestre": ["ce semestre", "semestre actuel"],
            "dernier_examen": ["dernier examen", "récent"],
            "cette_annee": ["cette année", "2025"],
            "tous": ["tous", "tout", "global"]
        }
    }
}
```

#### 6.2.3 Exemple de Conversation Complète

```
👤 User: Bonjour, je suis Paul Crolas, matricule 2024-MED-001

🤖 Bot: Bonjour Paul ! 👋 
        Je vous ai identifié dans le système.
        
        📋 **Vos informations :**
        • Nom : CROLAS Paul
        • Matricule : 2024-MED-001
        • Année : 1ère année Médecine
        
        Comment puis-je vous aider ?

👤 User: Ma note en anatomie

🤖 Bot: 📊 **Anatomie - Semestre 1**
        • Note : **15/20** (Admis)
        • Examen du 15 mars 2025
        • Rang : 23/156 étudiants
        • Moyenne promo : 12.4/20
        
        Félicitations Paul ! 🎉

👤 User: Ma moyenne générale ?

🤖 Bot: 📈 **Vos statistiques générales :**
        
        • Moyenne générale : **13.8/20**
        • Total examens : 12
        • Matières validées : 10/12 ✅
        • En rattrapage : 2
        
        🎯 **Points forts :** Anatomie (15/20), Cardiologie (14/20)
        📚 **À améliorer :** Pharmacologie (8/20)
```

---

## 7. PLANNING DÉTAILLÉ

### 7.1 Phase 1 : Configuration et Foundation (3 semaines)

**Semaine 1 : Setup et Architecture**
- [ ] Installation et configuration de l'environnement de développement
- [ ] Analyse approfondie de la base de données existante
- [ ] Configuration SQLAlchemy pour connexion à la base MySQL
- [ ] Structure de base du projet FastAPI
- [ ] Premiers modèles SQLAlchemy (Etudiant, Resultat)

**Semaine 2 : Authentification et Sécurité**
- [ ] Implémentation JWT avec FastAPI-Users
- [ ] Système d'authentification par matricule
- [ ] Authentification par nom/prénom avec gestion des homonymes
- [ ] Middleware de sécurité et permissions
- [ ] Tests unitaires d'authentification

**Semaine 3 : Endpoints de Base**
- [ ] Endpoints consultation résultats étudiants
- [ ] Filtrage par matière, session, date
- [ ] Endpoints statistiques de base
- [ ] Documentation automatique Swagger
- [ ] Tests d'intégration API

### 7.2 Phase 2 : API Avancée (3 semaines)

**Semaine 4 : Fonctionnalités Avancées**
- [ ] Calcul statistiques complexes (moyennes pondérées, évolution)
- [ ] Système de classement et ranking
- [ ] Génération de relevés de notes
- [ ] Endpoints administration (pour profs/admin)
- [ ] Optimisation des requêtes et performance

**Semaine 5 : Chatbot Foundation**
- [ ] Architecture du service chatbot
- [ ] Classification d'intentions avec spaCy
- [ ] Extraction d'entités nommées (matières, dates)
- [ ] Client API pour communication avec FastAPI
- [ ] Base de connaissances FAQ

**Semaine 6 : NLP et Conversations**
- [ ] Amélioration de la compréhension des questions
- [ ] Gestion du contexte conversationnel
- [ ] Génération de réponses naturelles
- [ ] Système de fallback et aide
- [ ] Intégration complète API ↔ Chatbot

### 7.3 Phase 3 : Finalisation et Tests (6 semaines)

**Semaines 7-8 : Tests et Débogage**
- [ ] Tests unitaires complets (objectif 80% couverture)
- [ ] Tests d'intégration API + Chatbot
- [ ] Tests de charge et performance
- [ ] Gestion d'erreurs et cas limites
- [ ] Validation sécurité

**Semaines 9-10 : Documentation et Déploiement**
- [ ] Documentation technique complète
- [ ] Guide utilisateur pour le chatbot
- [ ] Documentation API (en plus de Swagger)
- [ ] Configuration Docker et déploiement
- [ ] Formation aux utilisateurs finaux

**Semaines 11-12 : Optimisation et Livraison**
- [ ] Optimisations performance basées sur les tests
- [ ] Corrections bugs et améliorations UX
- [ ] Préparation de la présentation finale
- [ ] Transfert de connaissances à l'équipe
- [ ] Rédaction du rapport de stage

---

## 8. STACK TECHNOLOGIQUE

### 8.1 Technologies Principales

**Backend API :**
- **Python 3.9+** : Langage principal
- **FastAPI** : Framework web moderne et performant
- **SQLAlchemy 2.0** : ORM pour interaction base de données
- **Pydantic** : Validation et sérialisation des données
- **JWT (python-jose)** : Authentification stateless

**Intelligence Artificielle :**
- **spaCy** : Traitement du langage naturel
- **scikit-learn** : Classification et machine learning
- **NLTK** : Preprocessing et tokenisation
- **Rasa** (optionnel) : Framework conversationnel avancé

**Base de Données :**
- **MySQL 8.0** : Base existante (lecture seule)
- **Redis** : Cache et sessions (optionnel)

**DevOps et Testing :**
- **pytest** : Framework de test
- **Docker** : Conteneurisation
- **GitHub Actions** : CI/CD
- **Postman/Insomnia** : Tests API

### 8.2 Dépendances Python Principales

```txt
# requirements.txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
sqlalchemy==2.0.23
pymysql==1.1.0
pydantic==2.5.0
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6

# NLP et IA
spacy==3.7.2
scikit-learn==1.3.2
nltk==3.8.1
numpy==1.25.2
pandas==2.1.4

# Testing et développement
pytest==7.4.3
pytest-asyncio==0.21.1
httpx==0.25.2
pytest-cov==4.1.0

# Production
gunicorn==21.2.0
redis==5.0.1
python-dotenv==1.0.0
```

### 8.3 Architecture des Services

```yaml
# docker-compose.yml structure
version: '3.8'
services:
  fastapi-api:
    build: ./exam-api
    ports:
      - "8001:8001"
    environment:
      - DATABASE_URL=mysql://user:pass@mysql:3306/exam_db
      - JWT_SECRET_KEY=your-secret-key
    depends_on:
      - mysql
      
  chatbot-service:
    build: ./chatbot-ia
    ports:
      - "8002:8002"
    environment:
      - API_BASE_URL=http://fastapi-api:8001
    depends_on:
      - fastapi-api
      - redis
      
  mysql:
    image: mysql:8.0
    # Configuration existante - ne pas modifier
    
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

---

## 9. TESTS ET VALIDATION

### 9.1 Stratégie de Tests

#### 9.1.1 Tests Unitaires (Objectif : 80% couverture)

```python
# tests/test_auth.py
import pytest
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

class TestAuthentication:
    def test_login_matricule_valid(self):
        """Test authentification avec matricule valide"""
        response = client.post("/api/v1/auth/login-matricule", json={
            "numero_matricule": "2024-MED-001",
            "password": "testpassword"
        })
        assert response.status_code == 200
        data = response.json()
        assert "access_token" in data
        assert data["token_type"] == "bearer"
    
    def test_login_matricule_invalid_format(self):
        """Test matricule avec format invalide"""
        response = client.post("/api/v1/auth/login-matricule", json={
            "numero_matricule": "invalid-format",
            "password": "testpassword"
        })
        assert response.status_code == 422
    
    def test_login_nom_prenom(self):
        """Test authentification par nom/prénom"""
        response = client.post("/api/v1/auth/login-nom", json={
            "nom": "CROLAS",
            "prenom": "Paul",
            "password": "testpassword"
        })
        assert response.status_code == 200

# tests/test_students.py
class TestStudentEndpoints:
    def test_get_student_results_authenticated(self):
        """Test récupération résultats avec authentification"""
        # Obtenir token d'auth
        auth_response = client.post("/api/v1/auth/login-matricule", json={
            "numero_matricule": "2024-MED-001",
            "password": "testpassword"
        })
        token = auth_response.json()["access_token"]
        
        # Tester l'endpoint
        response = client.get("/api/v1/students/123/results", 
                            headers={"Authorization": f"Bearer {token}"})
        assert response.status_code == 200
        data = response.json()
        assert "status" in data
        assert "data" in data
    
    def test_get_student_results_unauthorized(self):
        """Test accès sans authentification"""
        response = client.get("/api/v1/students/123/results")
        assert response.status_code == 401
    
    def test_student_statistics(self):
        """Test calcul statistiques étudiant"""
        # Implementation avec mock data
        pass

# tests/test_chatbot.py
class TestChatbotNLP:
    def test_intent_classification(self):
        """Test classification d'intentions"""
        from app.nlp.intent_classifier import IntentClassifier
        
        classifier = IntentClassifier()
        
        # Test intention get_grades
        intent = classifier.predict("Quelle est ma note en anatomie ?")
        assert intent == "get_grades"
        
        # Test intention get_average
        intent = classifier.predict("Ma moyenne générale ?")
        assert intent == "get_average"
    
    def test_entity_extraction(self):
        """Test extraction d'entités"""
        from app.nlp.entity_extractor import EntityExtractor
        
        extractor = EntityExtractor()
        entities = extractor.extract("Ma note en anatomie ce semestre")
        
        assert "anatomie" in entities.get("subject", [])
        assert "ce semestre" in entities.get("time_period", [])
    
    def test_student_identification(self):
        """Test identification automatique étudiant"""
        from app.nlp.identity_extractor import IdentityExtractor
        
        extractor = IdentityExtractor()
        
        # Test extraction matricule
        identity = extractor.extract_identity("Mon matricule est 2024-MED-001")
        assert identity["matricule"] == "2024-MED-001"
        
        # Test extraction nom/prénom
        identity = extractor.extract_identity("Je suis Paul CROLAS")
        assert identity["prenom"] == "Paul"
        assert identity["nom"] == "CROLAS"
```

#### 9.1.2 Tests d'Intégration

```python
# tests/test_integration.py
class TestAPIIntegration:
    def test_full_student_journey(self):
        """Test complet : auth + consultation résultats"""
        # 1. Authentification
        auth_response = client.post("/api/v1/auth/login-matricule", json={
            "numero_matricule": "2024-MED-001",
            "password": "testpassword"
        })
        assert auth_response.status_code == 200
        token = auth_response.json()["access_token"]
        
        # 2. Consultation résultats
        results_response = client.get("/api/v1/students/123/results",
                                    headers={"Authorization": f"Bearer {token}"})
        assert results_response.status_code == 200
        
        # 3. Statistiques
        stats_response = client.get("/api/v1/students/123/statistics",
                                  headers={"Authorization": f"Bearer {token}"})
        assert stats_response.status_code == 200
    
    def test_chatbot_to_api_flow(self):
        """Test intégration Chatbot → API"""
        # Test conversation complète avec appels API
        pass

class TestChatbotIntegration:
    def test_conversation_flow(self):
        """Test flux de conversation complet"""
        chatbot_client = TestClient(chatbot_app)
        
        # 1. Identification
        response = chatbot_client.post("/chat/message", json={
            "message": "Je suis Paul CROLAS, matricule 2024-MED-001",
            "session_id": "test-session"
        })
        assert response.status_code == 200
        assert "identifié" in response.json()["response"]
        
        # 2. Question sur les notes
        response = chatbot_client.post("/chat/message", json={
            "message": "Ma note en anatomie ?",
            "session_id": "test-session"
        })
        assert response.status_code == 200
        assert "anatomie" in response.json()["response"].lower()
```

#### 9.1.3 Tests de Performance

```python
# tests/test_performance.py
import time
import asyncio
from concurrent.futures import ThreadPoolExecutor

class TestPerformance:
    def test_api_response_time(self):
        """Test temps de réponse API < 500ms"""
        start_time = time.time()
        
        response = client.get("/api/v1/students/123/results",
                            headers={"Authorization": f"Bearer {valid_token}"})
        
        response_time = (time.time() - start_time) * 1000  # en ms
        assert response_time < 500, f"Response time {response_time}ms > 500ms"
    
    def test_concurrent_requests(self):
        """Test charge avec requêtes concurrentes"""
        def make_request():
            return client.get("/api/v1/students/123/results",
                            headers={"Authorization": f"Bearer {valid_token}"})
        
        # Simuler 50 requêtes concurrentes
        with ThreadPoolExecutor(max_workers=50) as executor:
            futures = [executor.submit(make_request) for _ in range(50)]
            results = [future.result() for future in futures]
        
        # Vérifier que toutes les requêtes ont réussi
        success_count = sum(1 for r in results if r.status_code == 200)
        assert success_count >= 47, "Moins de 95% de requêtes réussies"
    
    def test_chatbot_nlp_performance(self):
        """Test performance traitement NLP < 100ms"""
        from app.nlp.intent_classifier import IntentClassifier
        
        classifier = IntentClassifier()
        
        start_time = time.time()
        intent = classifier.predict("Quelle est ma note en anatomie ?")
        processing_time = (time.time() - start_time) * 1000
        
        assert processing_time < 100, f"NLP processing {processing_time}ms > 100ms"
```

### 9.2 Validation et Critères d'Acceptation

#### 9.2.1 Critères Techniques

| Critère | Objectif | Méthode de Validation |
|---------|----------|----------------------|
| **Couverture de code** | ≥ 80% | pytest-cov |
| **Temps de réponse API** | < 500ms (95% des requêtes) | Tests de performance |
| **Disponibilité** | > 99% uptime | Tests de charge |
| **Sécurité** | 0 vulnérabilité critique | Audit sécurité |
| **Documentation** | 100% endpoints documentés | Swagger/OpenAPI |

#### 9.2.2 Critères Fonctionnels

| Fonctionnalité | Critère d'Acceptation | Test de Validation |
|----------------|----------------------|-------------------|
| **Authentification** | Login par matricule, nom/prénom, email | Tests unitaires auth |
| **Consultation résultats** | Accès à tous les résultats publiés | Tests intégration |
| **Chatbot NLP** | Compréhension 10+ types de questions | Tests conversation |
| **Statistiques** | Calculs moyennes, classements corrects | Tests calculs |
| **Sécurité données** | Accès restreint aux données personnelles | Tests autorisation |

---

## 10. DÉPLOIEMENT ET PRODUCTION

### 10.1 Architecture de Déploiement

```yaml
# Environnement de Production
Production Environment:
  reverse-proxy:
    technology: "Nginx"
    purpose: "Load balancing, SSL termination"
    config: |
      server {
          listen 443 ssl;
          server_name api.univ-mahajanga.mg;
          
          location /api/v1/ {
              proxy_pass http://fastapi-cluster;
          }
          
          location /chatbot/ {
              proxy_pass http://chatbot-cluster;
          }
      }
  
  fastapi-cluster:
    instances: 3
    technology: "Gunicorn + FastAPI"
    resources: "2 CPU, 4GB RAM par instance"
    
  chatbot-cluster:
    instances: 2
    technology: "Python + spaCy"
    resources: "1 CPU, 2GB RAM par instance"
    
  database:
    technology: "MySQL 8.0 (existant)"
    access: "Read-only pour nouveaux services"
    
  cache:
    technology: "Redis Cluster"
    purpose: "Sessions, cache API, NLP models"
```

### 10.2 Configuration Docker

```dockerfile
# Dockerfile.fastapi
FROM python:3.11-slim

WORKDIR /app

# Installation dépendances système
RUN apt-get update && apt-get install -y \
    gcc \
    default-libmysqlclient-dev \
    pkg-config \
    && rm -rf /var/lib/apt/lists/*

# Installation dépendances Python
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copie du code
COPY ./app ./app

# Variables d'environnement
ENV PYTHONPATH=/app
ENV WORKERS=4

# Exposition du port
EXPOSE 8001

# Commande de démarrage
CMD ["gunicorn", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", \
     "--bind", "0.0.0.0:8001", "--timeout", "120", "app.main:app"]
```

```dockerfile
# Dockerfile.chatbot
FROM python:3.11-slim

WORKDIR /app

# Installation spaCy et modèles
RUN pip install spacy==3.7.2
RUN python -m spacy download fr_core_news_sm

# Installation autres dépendances
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copie du code et modèles
COPY ./app ./app
COPY ./data ./data

# Pré-chargement des modèles NLP
RUN python -c "from app.nlp.intent_classifier import IntentClassifier; IntentClassifier()"

EXPOSE 8002

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8002"]
```

### 10.3 Configuration de Production

```python
# app/core/config.py
from pydantic_settings import BaseSettings
from typing import Optional

class Settings(BaseSettings):
    # Base de données
    DATABASE_URL: str = "mysql://user:password@localhost:3306/exam_db"
    DATABASE_POOL_SIZE: int = 20
    DATABASE_MAX_OVERFLOW: int = 30
    
    # Sécurité
    JWT_SECRET_KEY: str
    JWT_ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 60
    REFRESH_TOKEN_EXPIRE_DAYS: int = 7
    
    # API Configuration
    API_V1_STR: str = "/api/v1"
    PROJECT_NAME: str = "Exam Results API"
    VERSION: str = "1.0.0"
    
    # CORS
    BACKEND_CORS_ORIGINS: list = ["https://univ-mahajanga.mg"]
    
    # Chatbot
    CHATBOT_API_URL: str = "http://chatbot-service:8002"
    
    # Cache Redis
    REDIS_URL: Optional[str] = None
    CACHE_EXPIRE_SECONDS: int = 300
    
    # Monitoring
    LOG_LEVEL: str = "INFO"
    SENTRY_DSN: Optional[str] = None
    
    # Rate Limiting
    RATE_LIMIT_PER_MINUTE: int = 60
    
    class Config:
        env_file = ".env"
        case_sensitive = True

settings = Settings()
```

### 10.4 Monitoring et Logs

```python
# app/core/logging.py
import logging
import sys
from pathlib import Path

def setup_logging():
    """Configuration du système de logs"""
    
    # Format des logs
    log_format = (
        "%(asctime)s | %(levelname)s | %(name)s | "
        "%(funcName)s:%(lineno)d | %(message)s"
    )
    
    # Configuration
    logging.basicConfig(
        level=logging.INFO,
        format=log_format,
        handlers=[
            logging.StreamHandler(sys.stdout),
            logging.FileHandler("/var/log/exam-api/app.log"),
            logging.FileHandler("/var/log/exam-api/errors.log", level=logging.ERROR)
        ]
    )
    
    # Logger spécifique pour l'API
    api_logger = logging.getLogger("exam_api")
    
    # Logger pour le chatbot
    chatbot_logger = logging.getLogger("chatbot")
    
    return api_logger

# Middleware de logging des requêtes
from fastapi import Request
import time

async def log_requests(request: Request, call_next):
    """Middleware pour logger toutes les requêtes"""
    start_time = time.time()
    
    # Log de la requête entrante
    logger.info(f"Request: {request.method} {request.url}")
    
    # Traitement de la requête
    response = await call_next(request)
    
    # Log de la réponse
    process_time = time.time() - start_time
    logger.info(
        f"Response: {response.status_code} | "
        f"Time: {process_time:.3f}s | "
        f"Path: {request.url.path}"
    )
    
    return response
```

### 10.5 Sauvegarde et Récupération

```bash
#!/bin/bash
# scripts/backup.sh

# Sauvegarde des modèles NLP entraînés
backup_nlp_models() {
    DATE=$(date +%Y%m%d_%H%M%S)
    tar -czf "/backups/nlp_models_$DATE.tar.gz" /app/data/models/
    echo "NLP models backed up: nlp_models_$DATE.tar.gz"
}

# Sauvegarde des configurations
backup_configs() {
    DATE=$(date +%Y%m%d_%H%M%S)
    cp /app/.env "/backups/config_$DATE.env"
    echo "Configuration backed up: config_$DATE.env"
}

# Sauvegarde des logs applicatifs
backup_logs() {
    DATE=$(date +%Y%m%d_%H%M%S)
    tar -czf "/backups/logs_$DATE.tar.gz" /var/log/exam-api/
    echo "Logs backed up: logs_$DATE.tar.gz"
}

# Exécution des sauvegardes
backup_nlp_models
backup_configs
backup_logs

# Nettoyage des anciens backups (garder 30 jours)
find /backups -name "*.tar.gz" -mtime +30 -delete
find /backups -name "*.env" -mtime +30 -delete
```

---

## 11. DOCUMENTATION REQUISE

### 11.1 Documentation Technique

#### 11.1.1 Guide d'Installation et Configuration

```markdown
# Guide d'Installation - API Résultats d'Examens

## Prérequis
- Python 3.9+
- MySQL 8.0
- Redis (optionnel)
- Docker (pour déploiement)

## Installation Locale

### 1. Clone du Projet
```bash
git clone https://github.com/univ-mahajanga/exam-api.git
cd exam-api
```

### 2. Configuration Environnement
```bash
# Création environnement virtuel
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ou
venv\Scripts\activate  # Windows

# Installation dépendances
pip install -r requirements.txt
```

### 3. Configuration Base de Données
```bash
# Copier le fichier de configuration
cp .env.example .env

# Éditer .env avec vos paramètres
DATABASE_URL=mysql://user:password@localhost:3306/exam_db
JWT_SECRET_KEY=your-secret-key-here
```

### 4. Démarrage de l'API
```bash
uvicorn app.main:app --reload --port 8001
```

## Installation avec Docker

### 1. Construction des Images
```bash
docker-compose build
```

### 2. Démarrage des Services
```bash
docker-compose up -d
```

### 3. Vérification
```bash
curl http://localhost:8001/api/v1/health
```
```

#### 11.1.2 Documentation de l'API

```markdown
# Documentation API - Résultats d'Examens

## Authentification

Toutes les requêtes (sauf `/health` et `/docs`) nécessitent une authentification JWT.

### Obtenir un Token

**POST** `/api/v1/auth/login-matricule`

```json
{
  "numero_matricule": "2024-MED-001",
  "password": "mot_de_passe"
}
```

**Réponse :**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 3600,
  "student_info": {
    "id": 123,
    "numero_matricule": "2024-MED-001",
    "nom": "CROLAS",
    "prenom": "Paul"
  }
}
```

### Utilisation du Token

Inclure le token dans l'en-tête de chaque requête :
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Endpoints Principaux

### Consultation des Résultats

**GET** `/api/v1/students/{student_id}/results`

Paramètres de requête optionnels :
- `ec_id` : ID de la matière
- `session_id` : ID de la session d'examen
- `limit` : Nombre de résultats (défaut: 50)
- `offset` : Décalage pour pagination

**Exemple :**
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
     "http://localhost:8001/api/v1/students/123/results?ec_id=15&limit=10"
```

**Réponse :**
```json
{
  "status": "success",
  "data": {
    "results": [
      {
        "id": 1,
        "note": 15.0,
        "examen_titre": "Anatomie - Examen Final",
        "ec_nom": "Anatomie",
        "session_type": "normale",
        "statut": "publie",
        "decision": "admis",
        "date_publication": "2025-03-15T10:00:00Z",
        "jury_validated": true
      }
    ],
    "pagination": {
      "total": 1,
      "page": 1,
      "per_page": 10,
      "pages": 1
    }
  },
  "timestamp": "2025-06-23T14:30:00Z"
}
```

### Statistiques Étudiants

**GET** `/api/v1/students/{student_id}/statistics`

**Réponse :**
```json
{
  "status": "success",
  "data": {
    "moyenne_generale": 13.8,
    "total_examens": 12,
    "total_admis": 10,
    "total_rattrapage": 2,
    "evolution_notes": [12.0, 13.5, 14.0, 13.8],
    "matieres_forte": ["Anatomie", "Cardiologie"],
    "matieres_faible": ["Pharmacologie"]
  }
}
```

## Gestion des Erreurs

L'API utilise les codes de statut HTTP standards :

| Code | Signification | Description |
|------|---------------|-------------|
| 200 | OK | Requête réussie |
| 401 | Unauthorized | Token manquant ou invalide |
| 403 | Forbidden | Accès refusé (tentative d'accès aux données d'un autre étudiant) |
| 404 | Not Found | Ressource non trouvée |
| 422 | Validation Error | Données de requête invalides |
| 500 | Internal Server Error | Erreur serveur |

**Format des erreurs :**
```json
{
  "status": "error",
  "message": "Description de l'erreur",
  "details": {
    "code": "INVALID_MATRICULE",
    "field": "numero_matricule"
  },
  "timestamp": "2025-06-23T14:30:00Z"
}
```
```

### 11.2 Guide Utilisateur Chatbot

#### 11.2.1 Manuel d'Utilisation

```markdown
# Guide d'Utilisation - Chatbot Assistant Examens

## Démarrage d'une Conversation

### Identification

Avant de pouvoir consulter vos résultats, vous devez vous identifier. Le chatbot accepte plusieurs formats :

**Option 1 : Matricule**
```
"Mon matricule est 2024-MED-001"
"2024-MED-001"
"Je suis l'étudiant 2024-MED-001"
```

**Option 2 : Nom et Prénom**
```
"Je suis Paul CROLAS"
"Mon nom est CROLAS Paul"
"Paul CROLAS"
```

**Option 3 : Combiné**
```
"Je suis Paul CROLAS, matricule 2024-MED-001"
"Paul CROLAS 2024-MED-001"
```

### Consultation des Notes

Une fois identifié, vous pouvez poser différents types de questions :

**Notes par Matière**
```
"Ma note en anatomie"
"Quelle est ma note en cardiologie ?"
"Mes résultats en pharmacologie"
"Note anatomie"
```

**Notes d'un Examen Spécifique**
```
"Ma note du dernier examen"
"Mes résultats de l'examen de mars"
"L'examen d'anatomie du 15 mars"
```

**Moyennes et Statistiques**
```
"Ma moyenne générale"
"Quelle est ma moyenne ?"
"Mes statistiques"
"Ma moyenne en anatomie"
"Ma moyenne ce semestre"
```

**Statut d'Admission**
```
"Suis-je admis ?"
"Est-ce que je passe ?"
"Ai-je validé mon semestre ?"
"Mes décisions d'admission"
```

**Classement (si disponible)**
```
"Mon classement"
"Où je me situe dans la promo ?"
"Ma position"
```

## Types de Réponses

### Réponse Simple
```
Utilisateur: "Ma note en anatomie"

Chatbot: 📊 Anatomie - Semestre 1
• Note : 15/20 (Admis)
• Examen du 15 mars 2025
• Rang : 23/156 étudiants
• Moyenne promo : 12.4/20

Félicitations, vous êtes au-dessus de la moyenne ! 🎉
```

### Réponse avec Contexte
```
Utilisateur: "Ma moyenne générale"

Chatbot: 📈 Vos statistiques générales :

• Moyenne générale : 13.8/20
• Total examens passés : 12
• Matières validées : 10/12 ✅
• En rattrapage : 2

🎯 Points forts : Anatomie (15/20), Cardiologie (14/20)
📚 À améliorer : Pharmacologie (8/20)

Voulez-vous voir le détail d'une matière ?
```

### Aide et Support
```
"Aide"
"Comment ça marche ?"
"Je ne comprends pas"
"Que puis-je demander ?"
```

## Synonymes Reconnus

Le chatbot comprend de nombreux synonymes pour les matières :

| Matière Officielle | Synonymes Acceptés |
|-------------------|-------------------|
| Anatomie | anatomy, anat, anatomie humaine |
| Physiologie | physio, physiologie humaine |
| Cardiologie | cardio, coeur, système cardiovasculaire |
| Neurologie | neuro, système nerveux, neuroanatomie |
| Pharmacologie | pharma, médicaments, pharmaco |
| Histologie | histo, tissus, histologie générale |
| Biochimie | biochem, bioch, biochimie médicale |

## Limitations et Confidentialité

### Accès aux Données
- Vous ne pouvez consulter que **VOS** résultats personnels
- Aucun accès aux données d'autres étudiants
- Seuls les résultats **publiés** sont accessibles

### Sécurité
- Chaque session est sécurisée et temporaire
- Vos données ne sont jamais stockées par le chatbot
- Connexion chiffrée (HTTPS)

### Que Faire en Cas de Problème

**Problèmes d'Identification**
```
"Je ne trouve pas mon profil"
"Erreur d'identification"
```

**Problèmes Techniques**
```
"Erreur système"
"Le chatbot ne répond pas"
```

**Questions sur les Résultats**
```
"Contester une note"
"Erreur dans mes résultats"
"Problème avec ma moyenne"
```

Le chatbot vous guidera vers les bonnes ressources ou contacts.
```

### 11.3 Documentation Architecture

```markdown
# Architecture Système - API et Chatbot Examens

## Vue d'Ensemble

Le système est composé de trois services principaux :

1. **Application Laravel** (existante) : Gestion administrative
2. **API FastAPI** (nouveau) : Interface de consultation moderne
3. **Chatbot IA** (nouveau) : Assistant conversationnel

## Flux de Communication

```mermaid
graph LR
    A[Étudiant] --> B[Chatbot]
    B --> C[API FastAPI]
    C --> D[Base MySQL]
    
    E[Laravel Admin] --> D
    
    B -.-> F[Cache Redis]
    C -.-> F
```

## Composants Détaillés

### API FastAPI

**Responsabilités :**
- Authentification JWT des étudiants
- Consultation des résultats (lecture seule)
- Calculs statistiques et moyennes
- Validation des permissions d'accès

**Technologies :**
- FastAPI + SQLAlchemy
- JWT pour l'authentification
- Pydantic pour la validation
- MySQL pour les données

**Sécurité :**
- Accès lecture seule à la base de données
- Validation stricte des permissions (étudiant ne peut voir que ses données)
- Rate limiting (60 requêtes/minute par utilisateur)
- Logs complets des accès
- Chiffrement des communications (HTTPS)

### Chatbot IA

**Responsabilités :**
- Compréhension du langage naturel (français)
- Classification des intentions utilisateur
- Extraction d'entités (matières, dates, etc.)
- Génération de réponses conversationnelles
- Gestion du contexte de conversation

**Technologies :**
- spaCy pour le traitement du langage naturel
- scikit-learn pour la classification
- FastAPI pour l'interface web
- Redis pour le cache des sessions

**Pipeline NLP :**
1. Préprocessing du texte (normalisation, tokenisation)
2. Classification d'intention
3. Extraction d'entités nommées
4. Appel API pour récupération des données
5. Génération de réponse naturelle

### Base de Données

**Structure existante préservée :**
- Table `resultats_finaux` : source principale des données
- Relations avec `etudiants`, `examens`, `ecs`
- Nouveaux services en lecture seule uniquement
- Aucune modification de la structure existante

## Patterns Architecturaux

### Microservices
- Séparation claire des responsabilités
- Communication via APIs REST
- Déploiement indépendant possible
- Scalabilité horizontale

### API-First Design
- Documentation automatique (OpenAPI/Swagger)
- Versioning des APIs (/api/v1/)
- Réponses JSON standardisées
- Gestion d'erreurs cohérente

### Event-Driven (futur)
- Notifications en temps réel des nouveaux résultats
- Webhooks pour intégrations tierces
- Audit trail complet

## Monitoring et Observabilité

### Métriques Clés
- Temps de réponse API (< 500ms)
- Taux d'erreur (< 1%)
- Précision du chatbot (> 90%)
- Utilisation des ressources

### Logs Structurés
- Format JSON pour faciliter l'analyse
- Corrélation des requêtes avec trace-id
- Séparation par niveau (INFO, WARNING, ERROR)
- Rotation automatique des fichiers

### Alertes
- Temps de réponse dégradé
- Taux d'erreur élevé
- Utilisation mémoire/CPU critique
- Erreurs de base de données
```

---

## 12. CRITÈRES D'ÉVALUATION

### 12.1 Évaluation Technique (60% de la note)

#### 12.1.1 Qualité du Code (20%)

| Critère | Excellent (18-20) | Bien (15-17) | Satisfaisant (12-14) | Insuffisant (<12) |
|---------|------------------|---------------|---------------------|-------------------|
| **Architecture** | Architecture claire, respect des principes SOLID | Bonne structure, quelques améliorations possibles | Structure acceptable | Architecture confuse |
| **Lisibilité** | Code auto-documenté, nommage excellent | Code clair, bien commenté | Code compréhensible | Code difficile à lire |
| **Conventions** | Respect strict PEP8, conventions FastAPI | Respect global des conventions | Quelques écarts mineurs | Conventions non respectées |
| **Réutilisabilité** | Code modulaire, composants réutilisables | Bonne modularité | Modularité acceptable | Code monolithique |

#### 12.1.2 Fonctionnalités API (25%)

| Fonctionnalité | Points | Critères d'Évaluation |
|----------------|--------|----------------------|
| **Authentification JWT** | 5 pts | Login par matricule/nom/email, sécurité, gestion erreurs |
| **Endpoints étudiants** | 8 pts | Consultation résultats, filtres, pagination, performance |
| **Calculs statistiques** | 6 pts | Moyennes pondérées, évolution, classements |
| **Gestion des permissions** | 4 pts | Autorisation granulaire, sécurité des données |
| **Documentation API** | 2 pts | Swagger complet, exemples, guides |

#### 12.1.3 Fonctionnalités Chatbot (15%)

| Composant | Points | Critères d'Évaluation |
|-----------|--------|----------------------|
| **Classification NLP** | 6 pts | Précision intentions, gestion synonymes |
| **Extraction entités** | 4 pts | Reconnaissance matières, dates, noms |
| **Génération réponses** | 3 pts | Qualité conversationnelle, formatage |
| **Intégration API** | 2 pts | Communication fluide avec FastAPI |

### 12.2 Évaluation Méthodologique (25% de la note)

#### 12.2.1 Tests et Qualité (15%)

| Aspect | Critères | Points |
|--------|----------|--------|
| **Tests unitaires** | Couverture ≥ 80%, tests pertinents | 6 pts |
| **Tests d'intégration** | Scénarios complets, API + Chatbot | 4 pts |
| **Tests de performance** | Benchmarks, temps de réponse | 3 pts |
| **Gestion d'erreurs** | Robustesse, messages clairs | 2 pts |

#### 12.2.2 Documentation (10%)

| Type | Critères | Points |
|------|----------|--------|
| **Documentation technique** | Architecture, installation, déploiement | 4 pts |
| **Guide utilisateur** | Manuel chatbot, exemples d'usage | 3 pts |
| **Code documenté** | Docstrings, commentaires pertinents | 3 pts |

### 12.3 Évaluation Professionnelle (15% de la note)

#### 12.3.1 Gestion de Projet (10%)

- **Respect du planning** : Livraison dans les délais (3 pts)
- **Communication** : Reporting régulier, demande d'aide appropriée (3 pts)
- **Autonomie** : Capacité à résoudre les problèmes (2 pts)
- **Initiative** : Propositions d'améliorations (2 pts)

#### 12.3.2 Présentation Finale (5%)

- **Qualité de la présentation** : Structure, clarté (2 pts)
- **Démonstration technique** : Fonctionnalités opérationnelles (2 pts)
- **Réponses aux questions** : Maîtrise technique (1 pt)

### 12.4 Critères d'Excellence (Bonus +5%)

Pour obtenir des points bonus, le stagiaire peut implémenter des fonctionnalités avancées :

| Fonctionnalité Avancée | Points Bonus |
|------------------------|--------------|
| **Cache Redis optimisé** | +1 pt |
| **Notifications temps réel** | +1 pt |
| **Interface web simple** | +1 pt |
| **Monitoring/Métriques** | +1 pt |
| **Containerisation Docker** | +1 pt |

### 12.5 Grille de Notation Finale

```python
# Calcul de la note finale
def calculate_final_grade(scores):
    technique = scores['code_quality'] + scores['api_features'] + scores['chatbot_features']
    methodologique = scores['tests_quality'] + scores['documentation']
    professionnel = scores['project_management'] + scores['presentation']
    bonus = scores.get('advanced_features', 0)
    
    total = technique + methodologique + professionnel + bonus
    return min(total, 20)  # Note maximale 20/20

# Barème
GRADE_SCALE = {
    18-20: "Excellent - Toutes les fonctionnalités implémentées avec qualité exceptionnelle",
    16-17: "Très bien - Objectifs atteints avec quelques améliorations mineures",
    14-15: "Bien - Fonctionnalités principales implémentées correctement",
    12-13: "Satisfaisant - Objectifs de base atteints",
    10-11: "Passable - Fonctionnalités minimales avec des lacunes",
    0-9:   "Insuffisant - Objectifs non atteints"
}
```

---

## 13. RESSOURCES ET SUPPORT

### 13.1 Documentation Technique

#### 13.1.1 Ressources FastAPI

**Documentation Officielle :**
- [FastAPI Documentation](https://fastapi.tiangolo.com/) - Guide complet et tutoriels
- [SQLAlchemy 2.0](https://docs.sqlalchemy.org/en/20/) - ORM et gestion base de données
- [Pydantic](https://docs.pydantic.dev/) - Validation et sérialisation des données

**Tutoriels Recommandés :**
- [FastAPI Tutorial par Corey Schafer](https://www.youtube.com/playlist?list=PL-osiE80TeTtoQCKZ03TU5fNfx2UY6U4p)
- [Building APIs with FastAPI](https://testdriven.io/courses/tdd-fastapi/)
- [JWT Authentication with FastAPI](https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/)

#### 13.1.2 Ressources Intelligence Artificielle

**Documentation spaCy :**
- [spaCy Documentation](https://spacy.io/usage) - Traitement du langage naturel
- [French Language Model](https://spacy.io/models/fr) - Modèle français pré-entraîné
- [Custom NER Training](https://spacy.io/usage/training#ner) - Entraînement personnalisé

**NLP et Chatbots :**
- [Natural Language Processing with Python](https://www.nltk.org/book/) - Livre NLTK
- [Rasa Documentation](https://rasa.com/docs/) - Framework conversationnel avancé
- [Hugging Face Transformers](https://huggingface.co/docs/transformers/) - Modèles pré-entraînés

### 13.2 Outils de Développement

#### 13.2.1 Environnement de Développement

**IDE Recommandés :**
- **VS Code** avec extensions :
  - Python
  - Pylance
  - REST Client
  - Docker
  - MySQL

**Configuration VS Code :**
```json
{
    "python.defaultInterpreterPath": "./venv/bin/python",
    "python.linting.enabled": true,
    "python.linting.pylintEnabled": true,
    "python.formatting.provider": "black",
    "python.testing.pytestEnabled": true
}
```

#### 13.2.2 Outils de Test et Debug

**Tests API :**
- **Postman** : Collection d'endpoints pour tests manuels
- **httpx** : Client HTTP async pour tests automatisés
- **pytest-asyncio** : Tests asynchrones avec FastAPI

**Debug et Monitoring :**
- **FastAPI Debug Toolbar** : Debug en développement
- **Rich** : Logging coloré et formaté
- **py-spy** : Profiling performance Python

### 13.3 Données de Test

#### 13.3.1 Dataset d'Exemple

```sql
-- Script de génération de données de test
-- /scripts/generate_test_data.sql

-- Étudiants de test
INSERT INTO etudiants (nom, prenom, numero_matricule, email) VALUES
('CROLAS', 'Paul', '2024-MED-001', 'paul.crolas@univ-mahajanga.mg'),
('RAKOTO', 'Marie', '2024-MED-002', 'marie.rakoto@univ-mahajanga.mg'),
('ANDRIAMAHEFA', 'Jean', '2024-MED-003', 'jean.andriamahefa@univ-mahajanga.mg'),
('RASOA', 'Sarah', '2024-MED-004', 'sarah.rasoa@univ-mahajanga.mg'),
('RAHARIJAONA', 'David', '2024-MED-005', 'david.raharijaona@univ-mahajanga.mg');

-- Matières de test
INSERT INTO ecs (nom, code, coefficient, semestre) VALUES
('Anatomie', 'ANAT1', 3, 1),
('Physiologie', 'PHYS1', 3, 1),
('Cardiologie', 'CARD1', 2, 1),
('Neurologie', 'NEUR1', 2, 1),
('Pharmacologie', 'PHAR1', 2, 1),
('Histologie', 'HIST1', 2, 1),
('Biochimie', 'BIOC1', 3, 1);

-- Résultats de test (notes variées pour Paul CROLAS)
INSERT INTO resultats_finaux (etudiant_id, examen_id, ec_id, note, statut, decision, date_publication, jury_validated) VALUES
(1, 1, 1, 15.0, 'publie', 'admis', '2025-03-15 10:00:00', true),    -- Anatomie
(1, 2, 2, 13.5, 'publie', 'admis', '2025-03-20 10:00:00', true),    -- Physiologie
(1, 3, 3, 14.0, 'publie', 'admis', '2025-03-25 10:00:00', true),    -- Cardiologie
(1, 4, 4, 12.0, 'publie', 'admis', '2025-04-01 10:00:00', true),    -- Neurologie
(1, 5, 5, 8.0, 'publie', 'rattrapage', '2025-04-05 10:00:00', true), -- Pharmacologie
(1, 6, 6, 11.5, 'publie', 'admis', '2025-04-10 10:00:00', true),    -- Histologie
(1, 7, 7, 16.0, 'publie', 'admis', '2025-04-15 10:00:00', true);    -- Biochimie
```

#### 13.3.2 Données de Test Chatbot

```python
# /data/test_conversations.json
{
  "test_conversations": [
    {
      "conversation_id": "test_001",
      "messages": [
        {
          "user": "Bonjour, je suis Paul CROLAS matricule 2024-MED-001",
          "expected_intent": "student_identification",
          "expected_entities": {
            "nom": "CROLAS",
            "prenom": "Paul",
            "matricule": "2024-MED-001"
          }
        },
        {
          "user": "Ma note en anatomie",
          "expected_intent": "get_grades",
          "expected_entities": {
            "subject": "anatomie"
          },
          "expected_response_contains": ["15/20", "anatomie", "admis"]
        },
        {
          "user": "Et en cardiologie ?",
          "expected_intent": "get_grades",
          "expected_entities": {
            "subject": "cardiologie"
          },
          "context_dependent": true
        }
      ]
    },
    {
      "conversation_id": "test_002",
      "messages": [
        {
          "user": "2024-MED-002",
          "expected_intent": "student_identification",
          "expected_entities": {
            "matricule": "2024-MED-002"
          }
        },
        {
          "user": "Ma moyenne générale",
          "expected_intent": "get_average",
          "expected_entities": {
            "scope": "general"
          }
        }
      ]
    }
  ],
  
  "test_intents": {
    "get_grades": [
      "Ma note en anatomie",
      "Quelle est ma note en cardiologie ?",
      "Mes résultats en pharmacologie",
      "Note biochimie",
      "Combien j'ai eu en histologie ?"
    ],
    "get_average": [
      "Ma moyenne générale",
      "Quelle est ma moyenne ?",
      "Ma moyenne en anatomie",
      "Moyenne ce semestre"
    ],
    "get_admission_status": [
      "Suis-je admis ?",
      "Est-ce que je passe ?",
      "Ai-je validé ?",
      "Mes décisions d'admission"
    ]
  },
  
  "test_entities": {
    "subjects": {
      "anatomie": ["anatomie", "anat", "anatomy"],
      "cardiologie": ["cardiologie", "cardio", "coeur"],
      "pharmacologie": ["pharmacologie", "pharma", "médicaments"]
    },
    "time_periods": {
      "ce_semestre": ["ce semestre", "semestre actuel"],
      "cette_annee": ["cette année", "année actuelle"]
    }
  }
}
```

### 13.4 Support et Encadrement

#### 13.4.1 Structure d'Encadrement

**Maître de Stage (Responsable Technique) :**
- Validation de l'architecture technique
- Review du code et des fonctionnalités
- Support sur les problèmes complexes
- Évaluation technique finale

**Tuteur Pédagogique :**
- Suivi de la progression pédagogique
- Aide sur la méthodologie de développement
- Préparation de la soutenance
- Évaluation du rapport de stage

**Référent Base de Données :**
- Accès et permissions sur la base MySQL
- Explication de la structure existante
- Support sur les requêtes complexes

#### 13.4.2 Planning des Points de Suivi

| Semaine | Type de Suivi | Participants | Objectifs |
|---------|---------------|--------------|-----------|
| 1 | Kick-off | Maître de stage + Stagiaire | Architecture, setup, objectifs |
| 2 | Point technique | Maître de stage | Review setup, premiers développements |
| 4 | Point d'étape | Tous | Validation API de base |
| 6 | Review mi-parcours | Tous | Démonstration API + Chatbot basic |
| 8 | Point technique | Maître de stage | Review tests et intégration |
| 10 | Préparation soutenance | Tuteur pédagogique | Structure rapport et présentation |
| 12 | Soutenance finale | Jury complet | Présentation et évaluation |

#### 13.4.3 Canaux de Communication

**Communication Quotidienne :**
- **Slack/Teams** : Channel dédié projet
- **Email** : paul.crolas@univ-mahajanga.mg
- **Téléphone** : Urgences uniquement

**Meetings Réguliers :**
- **Daily standup** : 15min chaque matin (optionnel)
- **Weekly review** : 1h chaque vendredi
- **Monthly demo** : Présentation des avancées

**Documentation Partagée :**
- **GitHub** : Code source et issues
- **Confluence/Notion** : Documentation collaborative
- **Google Drive** : Documents de suivi

### 13.5 Critères de Succès du Stage

#### 13.5.1 Livrables Minimums (Pour validation du stage)

✅ **API FastAPI fonctionnelle** :
- 10+ endpoints opérationnels
- Authentification JWT
- Documentation Swagger complète
- Tests unitaires (>50% couverture)

✅ **Chatbot de base** :
- Identification automatique étudiants
- Compréhension 5+ types de questions
- Intégration avec l'API
- Interface conversationnelle

✅ **Documentation** :
- Guide installation/déploiement
- Guide utilisateur chatbot
- Rapport de stage complet

#### 13.5.2 Objectifs d'Excellence

🎯 **API Avancée** :
- 15+ endpoints avec fonctionnalités complexes
- Performance optimisée (<500ms)
- Tests complets (>80% couverture)
- Sécurité renforcée

🎯 **Chatbot Intelligent** :
- NLP avancé avec spaCy
- Gestion contexte conversationnel
- 10+ types de questions
- Réponses naturelles et personnalisées

🎯 **Production Ready** :
- Déploiement Docker
- Monitoring et logs
- Cache Redis
- Interface web bonus

#### 13.5.3 Impact Attendu

**Pour l'Université :**
- Modernisation de l'accès aux données d'examens
- Réduction de la charge administrative
- Amélioration de l'expérience étudiante
- Base technique pour futurs développements

**Pour le Stagiaire :**
- Maîtrise des technologies modernes (FastAPI, IA)
- Expérience en architecture microservices
- Compétences en NLP et chatbots
- Portfolio technique solide

---

## CONCLUSION

Ce Terme de Référence définit un projet ambitieux mais réalisable qui permettra au stagiaire de développer des compétences techniques solides tout en apportant une valeur concrète à l'Université de Mahajanga.

Le projet combine des aspects modernes du développement logiciel (API REST, Intelligence Artificielle, microservices) avec un besoin réel d'amélioration de l'accès aux données académiques.

La réussite de ce stage sera mesurée non seulement par la qualité technique des livrables, mais aussi par leur utilité pratique et leur intégration harmonieuse avec le système existant.

**Prochaines étapes :**
1. Validation de ce TDR par l'équipe pédagogique
2. Configuration de l'environnement de développement
3. Accès à la base de données de test
4. Démarrage du développement selon le planning établi

---

**Document préparé par :** [BEZARA Florent]  
**Le stagiaire** : [Paul Crolas]
**Date :** Juin 2025  
**Version :** 1.0  
**Statut :** En attente de validation
