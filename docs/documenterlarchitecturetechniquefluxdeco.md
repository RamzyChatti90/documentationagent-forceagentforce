# Architecture Technique de l'Agent SalesCoach

## Table des Matières
1.  Introduction
2.  Vue d'ensemble Architecturale
3.  Flux de Conversation Détaillé
4.  API de l'Agent SalesCoach
    4.1. Principes Généraux
    4.2. Endpoints Clés
    4.3. Authentification et Autorisation
5.  Intégrations CRM
    5.1. Principes d'Intégration
    5.2. Intégration Salesforce (Exemple Détaillé)
    5.3. Intégrations CRM Génériques
6.  Considérations Techniques
    6.1. Sécurité
    6.2. Scalabilité et Performance

---

## 1. Introduction

Ce document décrit l'architecture technique de l'agent SalesCoach, un système basé sur l'IA conçu pour analyser des conversations textuelles et aider les développeurs commerciaux à identifier et créer des opportunités. Il couvre les composants clés, le flux de traitement des conversations, les interfaces de programmation (API) et les mécanismes d'intégration avec les systèmes de gestion de la relation client (CRM), notamment Salesforce.

## 2. Vue d'ensemble Architecturale

L'architecture de l'agent SalesCoach est modulaire, permettant une évolutivité et une maintenance aisées. Elle se compose des éléments principaux suivants :

*   **Interface de Communication (IC)**: Canal par lequel les conversations textuelles sont ingérées (ex: messagerie d'entreprise, email, plateforme de chat).
*   **Moteur de Traitement du Langage Naturel (NLU/NLP)**: Responsable de l'analyse sémantique, de la détection d'intention et de l'extraction d'entités clés des messages.
*   **Moteur de Règles et IA (MRI)**: Cœur décisionnel de l'agent, appliquant des modèles d'IA et des règles métier pour détecter les opportunités, qualifier les leads et suggérer des actions.
*   **Base de Connaissances et Modèles (BCM)**: Stocke les modèles d'IA, les règles métier, les définitions d'opportunités, les profils de leads et les données d'apprentissage.
*   **Moteur d'Intégration CRM (MIC)**: Gère les interactions bidirectionnelles avec les systèmes CRM (ex: Salesforce) pour créer, mettre à jour et récupérer des informations.
*   **Service d'API (API Service)**: Expose les fonctionnalités de l'agent à d'autres applications et sert d'interface pour le MIC.
*   **Base de Données Opérationnelle (BDO)**: Stocke les données temporaires des conversations, les logs et les résultats d'analyse avant leur éventuelle persistance dans le CRM.

mermaid
graph TD
    IC[Interface de Communication] -->|Conversations Textuelles| NLU[Moteur NLU/NLP]
    NLU -->|Intentions & Entités| MRI[Moteur de Règles et IA]
    MRI -->|Requêtes/Mises à jour| BCM[Base de Connaissances & Modèles]
    MRI -->|Actions/Suggestions| API[Service d'API]
    API -->|Création/Maj CRM| MIC[Moteur d'Intégration CRM]
    MIC -->|API CRM| CRM[Système CRM (ex: Salesforce)]
    MRI -->|Données de session/logs| BDO[Base de Données Opérationnelle]
    API -->|Réponses/Notifications| IC


## 3. Flux de Conversation Détaillé

Le traitement d'une conversation par l'agent SalesCoach suit un flux structuré pour garantir une analyse précise et des actions pertinentes :

1.  **Réception du Message**:
    *   Les messages sont ingérés via l'Interface de Communication (IC) depuis diverses sources (chat, email, etc.).
    *   Chaque message est horodaté et associé à un identifiant de conversation unique.

2.  **Prétraitement et Analyse NLU**:
    *   Le Moteur NLU/NLP nettoie le texte (normalisation, suppression de stopwords).
    *   Il effectue une analyse linguistique pour détecter les intentions (ex: "demander un devis", "exprimer un besoin", "demander une démo") et extraire les entités (ex: "nom de l'entreprise", "produit mentionné", "budget", "délai").

3.  **Évaluation par le Moteur de Règles et IA (MRI)**:
    *   Les intentions et entités extraites sont transmises au MRI.
    *   Le MRI applique des modèles d'IA (apprentissage supervisé, réseaux de neurones) pour évaluer la probabilité qu'une opportunité commerciale existe.
    *   Des règles métier définies dans la BCM sont appliquées pour affiner la qualification (ex: présence de mots-clés spécifiques, montant de budget minimum, délai court).
    *   Le MRI identifie si le message correspond à un cas d'usage métier prédéfini (qualification de lead, demande de devis, suivi de relance, etc.).

4.  **Interaction avec le CRM (via MIC)**:
    *   Si une opportunité ou un lead est détecté, le MRI déclenche une requête via le Service d'API vers le Moteur d'Intégration CRM (MIC).
    *   Le MIC interroge le CRM pour vérifier l'existence d'un lead/compte/opportunité correspondant afin d'éviter les doublons.
    *   Si nécessaire, le MIC crée ou met à jour des enregistrements dans le CRM (Lead, Opportunity, Task, Event) en mappant les entités extraites aux champs CRM pertinents.
    *   Les résultats des opérations CRM (succès, échec, ID d'enregistrement) sont renvoyés au MRI.

5.  **Génération de la Réponse/Action**:
    *   En fonction des résultats de l'analyse et des interactions CRM, le MRI formule une suggestion ou une action pour l'utilisateur (le développeur commercial).
    *   Exemples d'actions : "Une opportunité a été créée dans Salesforce (ID: OP-12345). Le client semble intéressé par X avec un budget de Y.", "Ce lead nécessite une qualification approfondie, suggérez une démo.", "Relancez le client concernant la proposition Z."
    *   Ces suggestions sont transmises via le Service d'API à l'Interface de Communication (IC) ou à un tableau de bord dédié.

6.  **Journalisation et Audit**:
    *   Toutes les étapes du flux, les données d'entrée, les résultats d'analyse et les actions CRM sont journalisées dans la Base de Données Opérationnelle (BDO) à des fins d'audit, de débogage et d'amélioration continue des modèles.

## 4. API de l'Agent SalesCoach

Le Service d'API de l'agent SalesCoach est une interface RESTful permettant l'interaction programmatique avec les fonctionnalités de l'agent.

### 4.1. Principes Généraux

*   **Standard RESTful**: Utilisation des méthodes HTTP standards (GET, POST, PUT, DELETE).
*   **Format de Données**: Toutes les requêtes et réponses sont au format JSON.
*   **Versionning**: L'API utilise un versionning pour assurer la compatibilité ascendante (ex: `/api/v1/`).
*   **Documentation**: Une documentation OpenAPI (Swagger) sera disponible pour faciliter l'intégration.

### 4.2. Endpoints Clés

| Endpoint                       | Méthode | Description                                                                   | Corps de Requête (Exemple)                           | Réponse (Exemple)                                                                 |
| :----------------------------- | :------ | :---------------------------------------------------------------------------- | :--------------------------------------------------- | :-------------------------------------------------------------------------------- |
| `/api/v1/analyze-conversation` | `POST`  | Analyse une conversation textuelle et retourne les opportunités/actions.      | `{ "conversation_id": "c123", "text": "Le client a dit..." }` | `{ "status": "success", "opportunities": [...], "suggestions": [...] }`         |
| `/api/v1/crm/opportunity`      | `POST`  | Crée ou met à jour une opportunité dans le CRM.                               | `{ "crm_type": "salesforce", "data": { "name": "...", "amount": "..." } }` | `{ "status": "success", "crm_id": "006..." }`                                   |
| `/api/v1/crm/lead`             | `POST`  | Crée ou met à jour un lead dans le CRM.                                       | `{ "crm_type": "salesforce", "data": { "first_name": "...", "email": "..." } }` | `{ "status": "success", "crm_id": "00Q..." }`                                   |
| `/api/v1/crm/activity`         | `POST`  | Enregistre une activité (tâche, événement) liée à un lead/opportunité.        | `{ "crm_id": "006...", "type": "Task", "subject": "..." }` | `{ "status": "success", "activity_id": "00T..." }`                              |
| `/api/v1/feedback`             | `POST`  | Permet aux utilisateurs de fournir un feedback sur les suggestions de l'agent. | `{ "conversation_id": "c123", "rating": 5, "comment": "..." }` | `{ "status": "success", "message": "Feedback enregistré." }`                   |

### 4.3. Authentification et Autorisation

*   **Authentification**: Utilisation de jetons JWT (JSON Web Tokens) ou de clés API pour sécuriser l'accès aux endpoints. Les jetons sont générés après une authentification réussie via un service d'identité centralisé.
*   **Autorisation**: Des rôles et permissions sont associés aux jetons pour contrôler l'accès aux différentes fonctionnalités de l'API.

## 5. Intégrations CRM

Le Moteur d'Intégration CRM (MIC) est un composant crucial de l'agent SalesCoach, permettant une interaction fluide avec les systèmes CRM pour la gestion des données commerciales.

### 5.1. Principes d'Intégration

*   **Bidirectionnelle (si nécessaire)**: Bien que l'agent se concentre sur la création/mise à jour, la capacité de récupérer des informations existantes du CRM est essentielle pour éviter les doublons et enrichir le contexte.
*   **Temps Réel**: Les créations/mises à jour d'opportunités et de leads sont effectuées en temps quasi-réel pour garantir l'actualité des données.
*   **Sécurisée**: Toutes les communications avec le CRM sont chiffrées (HTTPS) et utilisent des mécanismes d'authentification robustes (OAuth 2.0).
*   **Configurable**: Le mapping des champs entre l'agent et le CRM est configurable pour s'adapter aux spécificités de chaque implémentation CRM.

### 5.2. Intégration Salesforce (Exemple Détaillé)

L'intégration avec Salesforce, en tant que CRM de référence, est implémentée via l'API REST de Salesforce.

*   **Mécanisme d'Intégration**:
    *   Utilisation de l'API REST Salesforce (version 58.0+ recommandée) pour toutes les opérations.
    *   Les requêtes sont initiées par le MIC suite aux instructions du MRI.
    *   Les Webhooks Salesforce peuvent être configurés pour informer l'agent de certains événements CRM si une intégration bidirectionnelle plus poussée est requise (ex: mise à jour d'un statut d'opportunité par un commercial).

*   **Objets Salesforce Cibles**:
    *   **Lead**: Création et mise à jour de leads qualifiés.
    *   **Opportunity**: Création et mise à jour d'opportunités, y compris les champs standard (Nom, Montant, Date de clôture, Étape) et potentiellement des champs personnalisés.
    *   **Account**: Association des opportunités/leads à des comptes existants ou création de nouveaux comptes si non trouvés.
    *   **Contact**: Création ou association de contacts liés aux leads/opportunités.
    *   **Task/Event**: Création de tâches ou d'événements pour les commerciaux (ex: "Relancer le client", "Planifier une démo") suite à la détection d'une opportunité.

*   **Flux de Données (Exemple : Création d'Opportunité)**:
    1.  Le MRI détecte une opportunité et envoie les données pertinentes (nom client, produit, budget, délai, etc.) au MIC via l'API interne.
    2.  Le MIC vérifie si un `Account` ou `Lead` existe déjà dans Salesforce pour ce client.
        *   Si oui, l'opportunité est liée à cet enregistrement.
        *   Si non, un nouveau `Lead` ou `Account` peut être créé d'abord.
    3.  Le MIC construit la requête `POST` vers l'endpoint `/services/data/vXX.0/sobjects/Opportunity` de Salesforce.
    4.  **Mapping des Champs (Exemple)**:
        *   `Name` (Opportunité) <= `Nom_Opportunite_Generé` (Agent)
        *   `AccountId` (Opportunité) <= `ID_Compte_Salesforce` (Agent)
        *   `Amount` (Opportunité) <= `Budget_Détecté` (Agent)
        *   `CloseDate` (Opportunité) <= `Date_Cloture_Estimée` (Agent)
        *   `StageName` (Opportunité) <= `Qualification` (Agent, valeur par défaut: "Qualification")
        *   `Description` (Opportunité) <= `Résumé_Conversation` (Agent)
        *   `LeadSource` (Opportunité) <= `Agent_SalesCoach` (Valeur fixe)
    5.  Le MIC gère la réponse de Salesforce, y compris les ID des enregistrements créés/mis à jour et les éventuelles erreurs.
    6.  Les résultats sont renvoyés au MRI pour la génération de la suggestion finale à l'utilisateur.

*   **Authentification**:
    *   Utilisation du flux OAuth 2.0 (Web Server Flow ou JWT Bearer Flow) pour obtenir un jeton d'accès (access token) sécurisé.
    *   Le jeton est stocké de manière sécurisée et rafraîchi avant son expiration.

*   **Gestion des Erreurs et Logs**:
    *   Les erreurs d'API Salesforce (ex: champs obligatoires manquants, restrictions d'accès) sont capturées et journalisées dans la BDO.
    *   Des mécanismes de retry avec backoff exponentiel sont mis en place pour les erreurs transitoires.
    *   Des alertes peuvent être configurées pour les échecs persistants d'intégration.

### 5.3. Intégrations CRM Génériques

L'architecture du MIC est conçue pour être extensible. Pour d'autres CRM (Dynamics 365, HubSpot, Pipedrive, etc.):

*   Un nouveau connecteur spécifique au CRM sera développé au sein du MIC.
*   Ce connecteur implémentera l'API spécifique du CRM et le mapping des champs.
*   Les principes d'authentification, de gestion des erreurs et de journalisation seront adaptés au CRM cible.

## 6. Considérations Techniques

### 6.1. Sécurité

*   **Chiffrement des Données**: Toutes les données en transit (HTTPS/TLS) et au repos (chiffrement de base de données) sont chiffrées.
*   **Gestion des Accès**: Principe du moindre privilège appliqué à tous les composants et intégrations (ex: les identifiants CRM ont les permissions minimales requises).
*   **Audit et Journalisation**: Journalisation complète des activités pour la traçabilité et la détection d'anomalies.
*   **Conformité**: Conception visant la conformité aux réglementations sur la protection des données (ex: RGPD).

### 6.2. Scalabilité et Performance

*   **Microservices**: L'architecture modulaire permet de déployer et de scaler indépendamment les différents services (NLU, MRI, MIC, API Service).
*   **Traitement Asynchrone**: Le traitement des conversations peut être mis en file d'attente et traité de manière asynchrone pour gérer les pics de charge.
*   **Mise en Cache**: Utilisation de caches pour les données fréquemment consultées (ex: modèles NLU, règles métier) afin de réduire la latence.
*   **Monitoring**: Des outils de monitoring et d'alerting sont mis en place pour surveiller la performance et la santé des services.