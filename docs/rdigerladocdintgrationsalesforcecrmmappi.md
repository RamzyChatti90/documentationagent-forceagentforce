# Documentation d'Intégration CRM (Salesforce) pour ForceAgentForce

Ce document décrit en détail le processus d'intégration de l'agent Force Commercial (SalesCoach) avec les systèmes de gestion de la relation client (CRM), en se concentrant spécifiquement sur Salesforce. Il couvre l'architecture, les flux de données, le mapping des champs et les spécifications des API endpoints nécessaires pour une intégration réussie.

---

**Table des Matières**

1.  Introduction
2.  Prérequis à l'Intégration
    2.1. Accès API CRM
    2.2. Permissions Utilisateur
    2.3. Informations d'Identification
3.  Architecture d'Intégration
    3.1. Vue d'ensemble
    3.2. Composants Clés
4.  Flux de Données et Scénarios
    4.1. Création/Mise à jour d'un Lead/Contact
    4.2. Création d'une Opportunité
    4.3. Mise à jour d'une Opportunité
    4.4. Création d'une Tâche/Activité
5.  Mapping des Champs
    5.1. Mapping Lead/Contact
    5.2. Mapping Opportunité
    5.3. Mapping Tâche/Activité
6.  API Endpoints et Méthodes
    6.1. API Salesforce Spécifique (REST API)
        6.1.1. Authentification (OAuth 2.0)
        6.1.2. Opérations sur les SObjects
7.  Authentification et Autorisation
    7.1. Flux OAuth 2.0 (pour Salesforce)
    7.2. Gestion des Jetons
8.  Configuration Spécifique Salesforce
    8.1. Création d'une Application Connectée
    8.2. Gestion des Permissions (Profils/Ensembles d'autorisations)
    8.3. Champs Personnalisés (si nécessaire)
9.  Gestion des Erreurs et Journalisation
    9.1. Codes d'Erreur
    9.2. Journalisation des Événements
10. Considérations de Sécurité
    10.1. Chiffrement des Données
    10.2. Accès Minimaliste
    10.3. Surveillance

---

## 1. Introduction

L'agent Force Commercial (SalesCoach) est conçu pour identifier et qualifier des opportunités commerciales à partir de conversations textuelles. Pour maximiser son efficacité, une intégration transparente avec le CRM de l'entreprise est essentielle. Ce document fournit les directives techniques pour connecter SalesCoach à Salesforce, permettant la création automatique de leads, contacts, opportunités et tâches, réduisant ainsi la saisie manuelle et assurant une synchronisation des données en temps réel.

## 2. Prérequis à l'Intégration

Avant de procéder à l'intégration, les éléments suivants doivent être mis en place :

### 2.1. Accès API CRM

*   **Salesforce :** L'édition Salesforce doit prendre en charge l'accès à l'API (ex: Enterprise Edition, Unlimited Edition, Developer Edition).
*   **Autres CRM :** Une licence ou un plan d'abonnement permettant l'accès aux API du CRM cible.

### 2.2. Permissions Utilisateur

Un utilisateur dédié à l'intégration dans le CRM avec les permissions suivantes (minimum requis) :

*   **Salesforce :**
    *   Accès à l'API (via le profil ou un ensemble d'autorisations).
    *   Permissions de "Créer", "Lire", "Mettre à jour" (CRUD) sur les objets `Lead`, `Contact`, `Account`, `Opportunity`, `Task`.
    *   Permissions de "Lire" sur les objets `User` (pour attribuer des propriétaires).
    *   Accès aux champs spécifiques utilisés pour le mapping (voir Section 5).

### 2.3. Informations d'Identification

*   **Salesforce :**
    *   `Consumer Key` et `Consumer Secret` de l'application connectée (voir Section 8.1).
    *   `Username` et `Password` de l'utilisateur d'intégration (avec jeton de sécurité si nécessaire, ou via un flux OAuth 2.0 plus robuste).
    *   `URL d'instance` Salesforce (ex: `https://mycompany.my.salesforce.com`).

## 3. Architecture d'Intégration

### 3.1. Vue d'ensemble

L'intégration est basée sur un modèle push/pull où SalesCoach interagit avec le CRM via ses API. SalesCoach identifie une intention commerciale dans une conversation, extrait les informations pertinentes, puis utilise les API du CRM pour créer ou mettre à jour des enregistrements.

mermaid
graph TD
    A[Utilisateur / Canal de Conversation] --> B(Agent SalesCoach)
    B -- Détection d'Opportunité / Extraction de Données --> C{Module d'Intégration CRM}
    C -- Requêtes API (Création/MàJ) --> D[Salesforce / CRM]
    D -- Réponses API (Succès/Échec) --> C
    C -- Notifications / Logs --> B
    B -- Retour d'information à l'utilisateur --> A


### 3.2. Composants Clés

*   **Agent SalesCoach :** Le cœur de l'IA qui analyse les conversations.
*   **Module d'Intégration CRM :** Composant de SalesCoach responsable de la communication avec le CRM. Il gère l'authentification, le mapping des données, la construction des requêtes API et le traitement des réponses.
*   **API CRM :** L'interface exposée par le CRM (ex: Salesforce REST API) pour permettre des opérations programmatiques.
*   **Base de Données CRM :** Le système de stockage des données du CRM.

## 4. Flux de Données et Scénarios

SalesCoach déclenche des actions dans le CRM en fonction des intentions détectées dans les conversations.

### 4.1. Création/Mise à jour d'un Lead/Contact

*   **Déclencheur :** Détection d'un nouvel intérêt pour un produit/service, ou identification d'informations de contact complètes.
*   **Flux :**
    1.  SalesCoach extrait le nom, prénom, email, téléphone, entreprise, etc.
    2.  Le module d'intégration vérifie l'existence du contact/lead dans le CRM via l'API (recherche par email/téléphone).
    3.  Si le contact/lead existe, les informations sont mises à jour.
    4.  Si le contact/lead n'existe pas, un nouvel enregistrement `Lead` est créé.
    5.  Attribution du Lead/Contact à un propriétaire (souvent l'utilisateur d'intégration ou un commercial désigné).
*   **API Salesforce :** `POST /services/data/vXX.0/sobjects/Lead/`, `PATCH /services/data/vXX.0/sobjects/Lead/{id}`.

### 4.2. Création d'une Opportunité

*   **Déclencheur :** Détection d'une intention d'achat claire, d'une demande de devis, ou d'une expression de besoin qualifiée.
*   **Flux :**
    1.  SalesCoach identifie les détails de l'opportunité (produit/service, montant estimé, date de clôture, stade, etc.).
    2.  Vérification de l'existence du `Contact` ou `Lead` associé. Si un `Lead` est détecté, il est converti en `Contact` et `Account`.
    3.  Création d'un nouvel enregistrement `Opportunity` lié au `Contact`/`Account` existant.
    4.  Attribution de l'opportunité à un propriétaire.
*   **API Salesforce :** `POST /services/data/vXX.0/sobjects/Opportunity/`.

### 4.3. Mise à jour d'une Opportunité

*   **Déclencheur :** Le prospect fournit de nouvelles informations sur une opportunité existante (changement de budget, de délai, de besoin).
*   **Flux :**
    1.  SalesCoach identifie l'opportunité concernée et les informations à mettre à jour.
    2.  Le module d'intégration récupère l'ID de l'opportunité.
    3.  Mise à jour des champs pertinents de l'opportunité dans le CRM.
*   **API Salesforce :** `PATCH /services/data/vXX.0/sobjects/Opportunity/{id}`.

### 4.4. Création d'une Tâche/Activité

*   **Déclencheur :** Nécessité d'une action de suivi (rappel, envoi de documentation, prise de rendez-vous) identifiée par SalesCoach.
*   **Flux :**
    1.  SalesCoach identifie le type de tâche, la date d'échéance et la description.
    2.  Création d'une `Task` (Tâche) ou `Event` (Événement) liée au `Lead`, `Contact` ou `Opportunity` pertinent.
    3.  Attribution de la tâche à un commercial.
*   **API Salesforce :** `POST /services/data/vXX.0/sobjects/Task/`.

## 5. Mapping des Champs

Ce tableau détaille le mapping des champs entre les données extraites par SalesCoach et les champs standards de Salesforce. Des champs personnalisés peuvent être ajoutés si nécessaire (voir Section 8.3).

### 5.1. Mapping Lead/Contact

| Champ SalesCoach | Champ Salesforce (Lead) | Champ Salesforce (Contact) | Type de Donnée | Description |
| :--------------- | :---------------------- | :------------------------- | :------------- | :------------------------------------------------------------------- |
| `firstName`      | `FirstName`             | `FirstName`                | Texte          | Prénom du prospect/contact. |
| `lastName`       | `LastName`              | `LastName`                 | Texte          | Nom de famille du prospect/contact. |
| `email`          | `Email`                 | `Email`                    | Email          | Adresse email du prospect/contact. |
| `phone`          | `Phone`                 | `Phone`                    | Téléphone      | Numéro de téléphone principal. |
| `company`        | `Company`               | `Account.Name`             | Texte          | Nom de l'entreprise. Pour Contact, crée ou lie un compte. |
| `title`          | `Title`                 | `Title`                    | Texte          | Titre/fonction du prospect/contact. |
| `industry`       | `Industry`              | `Account.Industry`         | Liste de sélection | Secteur d'activité de l'entreprise. |
| `leadSource`     | `LeadSource`            | `LeadSource__c` (custom)   | Liste de sélection | Source du lead (ex: "Chatbot", "SalesCoach"). |
| `description`    | `Description`           | `Description`              | Texte long     | Résumé de la conversation ou informations supplémentaires. |
| `status`         | `Status`                | N/A                        | Liste de sélection | Statut du Lead (ex: "New", "Working"). |
| `ownerId`        | `OwnerId`               | `OwnerId`                  | ID             | ID de l'utilisateur Salesforce propriétaire. |

### 5.2. Mapping Opportunité

| Champ SalesCoach | Champ Salesforce (Opportunity) | Type de Donnée | Description |
| :--------------- | :----------------------------- | :------------- | :------------------------------------------------------------------- |
| `opportunityName`| `Name`                         | Texte          | Nom de l'opportunité (ex: "Vente [Produit] à [Entreprise]"). |
| `accountId`      | `AccountId`                    | ID             | ID du compte Salesforce associé. |
| `contactId`      | `ContactId` (via `PrimaryContact`) | ID             | ID du contact principal lié à l'opportunité. |
| `amount`         | `Amount`                       | Devise         | Montant estimé de l'opportunité. |
| `closeDate`      | `CloseDate`                    | Date           | Date de clôture estimée de l'opportunité. |
| `stageName`      | `StageName`                    | Liste de sélection | Stade de l'opportunité (ex: "Qualification", "Proposition", "Closed Won"). |
| `probability`    | `Probability`                  | Pourcentage    | Probabilité de succès de l'opportunité (dérivée du stade). |
| `description`    | `Description`                  | Texte long     | Détails supplémentaires de l'opportunité issus de la conversation. |
| `ownerId`        | `OwnerId`                      | ID             | ID de l'utilisateur Salesforce propriétaire. |
| `productInterest`| `Product__c` (custom)          | Texte          | Intérêt produit spécifique (si champ personnalisé existe). |

### 5.3. Mapping Tâche/Activité

| Champ SalesCoach | Champ Salesforce (Task) | Type de Donnée | Description |
| :--------------- | :---------------------- | :------------- | :------------------------------------------------------------------- |
| `subject`        | `Subject`               | Texte          | Objet de la tâche (ex: "Rappeler prospect", "Envoyer documentation"). |
| `description`    | `Description`           | Texte long     | Détails de la tâche à effectuer. |
| `dueDate`        | `ActivityDate`          | Date           | Date d'échéance de la tâche. |
| `status`         | `Status`                | Liste de sélection | Statut de la tâche (ex: "Not Started", "Completed"). |
| `priority`       | `Priority`              | Liste de sélection | Priorité de la tâche (ex: "High", "Normal"). |
| `relatedToId`    | `WhatId`                | ID             | ID de l'objet lié (Lead, Contact, Opportunity, Account). |
| `assignedToId`   | `OwnerId`               | ID             | ID de l'utilisateur Salesforce à qui la tâche est attribuée. |

## 6. API Endpoints et Méthodes

L'intégration avec Salesforce s'effectue principalement via la REST API.

### 6.1. API Salesforce Spécifique (REST API)

Toutes les requêtes doivent être authentifiées via OAuth 2.0. La version de l'API (`vXX.0`) doit être spécifiée (ex: `v58.0` pour Spring '23).

#### 6.1.1. Authentification (OAuth 2.0)

*   **Endpoint :** `https://[instance_url]/services/oauth2/token`
*   **Méthode :** `POST`
*   **Body (x-www-form-urlencoded) :**
    *   `grant_type=password` (pour le flux mot de passe, recommandé pour les intégrations de service à service où la sécurité est gérée)
    *   `client_id=[Consumer Key]`
    *   `client_secret=[Consumer Secret]`
    *   `username=[Salesforce Username]`
    *   `password=[Salesforce Password + Security Token]`
*   **Réponse :** Un jeton d'accès (`access_token`) est retourné, à utiliser dans l'en-tête `Authorization` de toutes les requêtes subséquentes.

#### 6.1.2. Opérations sur les SObjects

Les requêtes sont faites à l'URL de l'instance Salesforce.

*   **En-tête commun :**
    *   `Authorization: Bearer [access_token]`
    *   `Content-Type: application/json`

**Création d'un SObject (Lead, Opportunity, Task, etc.)**

*   **Endpoint :** `https://[instance_url]/services/data/vXX.0/sobjects/[SObjectApiName]/`
*   **Méthode :** `POST`
*   **Body :** Objet JSON représentant les champs de l'enregistrement à créer.
    *   **Exemple (Création de Lead) :**
        json
        {
            "FirstName": "Jean",
            "LastName": "Dupont",
            "Company": "ACME Corp",
            "Email": "jean.dupont@acmecorp.com",
            "LeadSource": "SalesCoach"
        }
        
*   **Réponse :** `{ "id": "00Q...", "success": true, "errors": [] }`

**Mise à jour d'un SObject**

*   **Endpoint :** `https://[instance_url]/services/data/vXX.0/sobjects/[SObjectApiName]/{id}`
*   **Méthode :** `PATCH`
*   **Body :** Objet JSON avec les champs à mettre à jour.
    *   **Exemple (Mise à jour d'Opportunity) :**
        json
        {
            "StageName": "Proposition/Price Quote",
            "Amount": 15000.00
        }
        
*   **Réponse :** `204 No Content` en cas de succès.

**Récupération d'un SObject par ID**

*   **Endpoint :** `https://[instance_url]/services/data/vXX.0/sobjects/[SObjectApiName]/{id}`
*   **Méthode :** `GET`
*   **Réponse :** Objet JSON représentant l'enregistrement.

**Recherche d'un SObject (SOQL Query)**

*   **Endpoint :** `https://[instance_url]/services/data/vXX.0/query?q=[SOQL Query]`
*   **Méthode :** `GET`
*   **Exemple (Recherche de Lead par Email) :**
    *   `q=SELECT Id, FirstName, LastName FROM Lead WHERE Email='jean.dupont@acmecorp.com'`
*   **Réponse :** `{ "totalSize": 1, "done": true, "records": [...] }`

## 7. Authentification et Autorisation

### 7.1. Flux OAuth 2.0 (pour Salesforce)

Pour une intégration de service à service, le flux **OAuth 2.0 Username-Password** est souvent utilisé pour sa simplicité. Cependant, pour une sécurité accrue ou une conformité spécifique, d'autres flux comme le **JWT Bearer flow** peuvent être envisagés.

Le flux Username-Password nécessite le `Consumer Key`, `Consumer Secret`, `Username` et `Password` (avec jeton de sécurité) de l'utilisateur d'intégration. Le jeton d'accès obtenu a une durée de vie limitée et doit être rafraîchi ou de nouvelles requêtes d'authentification effectuées.

### 7.2. Gestion des Jetons

*   SalesCoach doit implémenter un mécanisme pour stocker le `access_token` de manière sécurisée et le réutiliser pour toutes les requêtes API jusqu'à son expiration.
*   En cas d'expiration ou d'invalidation du jeton, SalesCoach doit être capable de refaire une demande d'authentification pour obtenir un nouveau jeton.

## 8. Configuration Spécifique Salesforce

### 8.1. Création d'une Application Connectée

1.  **Dans Salesforce :** `Setup` > `Platform Tools` > `Apps` > `App Manager`.
2.  Cliquez sur `New Connected App`.
3.  Remplissez les informations de base (Nom de l'application connectée, Nom de l'API, Email de contact).
4.  Cochez `Enable OAuth Settings`.
5.  Pour `Callback URL`, entrez une URL valide (même si non utilisée directement pour le flux username-password, elle est requise). Par exemple, `https://localhost/callback`.
6.  Sélectionnez les `Selected OAuth Scopes` nécessaires :
    *   `Access and manage your data (api)`
    *   `Perform requests on your behalf at any time (refresh_token, offline_access)` (recommandé pour la gestion des jetons)
    *   `Provide access to your data via the Web (web)` (si l'interface utilisateur de SalesCoach doit accéder à Salesforce via le navigateur)
7.  Enregistrez l'application.
8.  Notez le `Consumer Key` et le `Consumer Secret` générés.

### 8.2. Gestion des Permissions (Profils/Ensembles d'autorisations)

Assurez-vous que le profil ou l'ensemble d'autorisations attribué à l'utilisateur d'intégration dispose des permissions CRUD sur les objets `Lead`, `Contact`, `Account`, `Opportunity`, `Task`, ainsi que l'accès aux champs nécessaires (voir Section 5).

1.  **Dans Salesforce :** `Setup` > `Administration` > `Users` > `Profiles` ou `Permission Sets`.
2.  Créez un nouvel ensemble d'autorisations ou modifiez un profil existant.
3.  Accordez les `Object Settings` et `Field-Level Security` appropriés.
4.  Assurez-vous que l'autorisation `API Enabled` est cochée.

### 8.3. Champs Personnalisés (si nécessaire)

Si SalesCoach extrait des informations qui ne correspondent pas aux champs Salesforce standards, des champs personnalisés peuvent être créés dans Salesforce.

1.  **Dans Salesforce :** `Setup` > `Object Manager` > Sélectionnez l'objet (ex: `Lead`).
2.  `Fields & Relationships` > `New`.
3.  Choisissez le type de données et configurez le champ.
4.  Mettez à jour le mapping des champs (Section 5) pour inclure ces nouveaux champs personnalisés (ex: `Product__c`).

## 9. Gestion des Erreurs et Journalisation

Une gestion robuste des erreurs et une journalisation détaillée sont cruciales pour le débogage et la maintenance de l'intégration.

### 9.1. Codes d'Erreur

SalesCoach doit intercepter et gérer les codes d'erreur HTTP et les messages d'erreur spécifiques de l'API Salesforce.

*   **HTTP 400 Bad Request :** Erreur de validation des données envoyées.
*   **HTTP 401 Unauthorized :** Jeton d'accès invalide ou expiré.
*   **HTTP 403 Forbidden :** Permissions insuffisantes pour effectuer l'opération.
*   **HTTP 404 Not Found :** Ressource non trouvée (ex: ID d'enregistrement incorrect).
*   **HTTP 500 Internal Server Error :** Erreur côté Salesforce.

### 9.2. Journalisation des Événements

SalesCoach doit enregistrer les informations suivantes :

*   **Requêtes API :** URL, méthode, corps de la requête (sans données sensibles).
*   **Réponses API :** Code de statut HTTP, corps de la réponse.
*   **Erreurs :** Détails de l'erreur, horodatage, contexte (quel objet, quelle opération).
*   **Événements clés :** Création/Mise à jour d'un Lead, Opportunité, Tâche.
*   **Identifiants :** ID Salesforce des enregistrements créés ou mis à jour.

## 10. Considérations de Sécurité

### 10.1. Chiffrement des Données

Toutes les communications entre SalesCoach et Salesforce doivent utiliser HTTPS pour garantir le chiffrement des données en transit.

### 10.2. Accès Minimaliste

L'utilisateur d'intégration Salesforce doit avoir le principe du moindre privilège : uniquement les permissions nécessaires pour effectuer les opérations requises par SalesCoach.

### 10.3. Surveillance

Mettre en place une surveillance de l'intégration pour détecter rapidement les échecs d'API, les problèmes d'authentification ou les anomalies de données.

---