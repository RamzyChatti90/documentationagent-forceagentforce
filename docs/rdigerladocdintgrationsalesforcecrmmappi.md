
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
=======
# Documentation d'Intégration Salesforce/CRM de l'Agent ForceAgentForce (SalesCoach)

## 1. Introduction

Ce document détaille les spécifications techniques et fonctionnelles pour l'intégration de l'agent ForceAgentForce (SalesCoach) avec les systèmes de gestion de la relation client (CRM), en se concentrant sur Salesforce. L'objectif de cette intégration est de permettre à SalesCoach de créer, mettre à jour et enrichir des enregistrements (Leads, Opportunités, Contacts, Tâches) directement dans le CRM, à partir des informations détectées et qualifiées lors des conversations textuelles.

L'intégration vise à automatiser le transfert d'informations clés, à réduire la saisie manuelle et à garantir la cohérence des données entre SalesCoach et le CRM, améliorant ainsi l'efficacité des équipes commerciales et la qualité du pipeline.

## 2. Prérequis à l'Intégration

Avant de procéder à l'intégration, les éléments suivants doivent être mis en place ou vérifiés :

*   **Accès à l'API Salesforce/CRM :** Un compte Salesforce/CRM avec un accès API activé est indispensable.
*   **Permissions utilisateur :** Un utilisateur dédié ou un profil avec les permissions nécessaires pour créer, lire, mettre à jour (CRUD) les objets `Lead`, `Opportunity`, `Contact`, `Account` et `Task` dans Salesforce/CRM.
*   **Clés d'API / Jetons d'accès :** Configuration des clés client (Consumer Key) et des secrets client (Consumer Secret) pour l'authentification OAuth 2.0.
*   **Environnement Salesforce :** Spécification de l'environnement (Sandbox ou Production) pour la connexion.
*   **URL de Domaine Personnalisé (si applicable) :** Si un domaine My Domain est utilisé dans Salesforce, son URL doit être fournie.
*   **Objet et Champs Personnalisés :** Identification et création préalable de tout objet ou champ personnalisé requis dans Salesforce pour stocker des données spécifiques à SalesCoach (par exemple, un champ "Source SalesCoach", "Score de Qualification SalesCoach").

## 3. Architecture d'Intégration

L'intégration entre SalesCoach et Salesforce/CRM s'effectuera via des appels d'API REST. SalesCoach agira comme le client, initiant des requêtes vers l'API Salesforce/CRM pour créer ou mettre à jour des enregistrements.

*   **Modèle :** Client-Serveur (SalesCoach -> Salesforce/CRM API)
*   **Protocole :** HTTPS
*   **Format des données :** JSON
*   **Authentification :** OAuth 2.0 (flux de jeton de mot de passe, flux d'authentification Web, ou JWT selon la configuration client et les exigences de sécurité).

![Diagramme d'Architecture Simplifié](https://www.plantuml.com/plantuml/svg/SyxCJYxCJbOGArLCSYfCqW10i59KqT5w30Hq0000)
plantuml
@startuml
participant "Agent SalesCoach" as SC
participant "API Salesforce/CRM" as SFAPI
database "Base de Données Salesforce/CRM" as SFDB

SC -> SFAPI: 1. Demande de jeton d'accès (OAuth 2.0)
SFAPI --> SC: 2. Jeton d'accès
SC -> SFAPI: 3. Appel API (Création/Mise à jour Lead/Opportunité/Contact/Tâche)
SFAPI --> SFDB: 4. Écriture/Lecture des données
SFDB --> SFAPI: 5. Confirmation
SFAPI --> SC: 6. Réponse API (Succès/Échec)
@enduml


## 4. Configuration des Connecteurs

La configuration de l'intégration se fera via une interface d'administration de SalesCoach ou des fichiers de configuration, nécessitant les informations suivantes :

*   **Type de CRM :** Salesforce (ou autre, si l'intégration est générique)
*   **URL de l'instance Salesforce :** `https://login.salesforce.com` (Production) ou `https://test.salesforce.com` (Sandbox)
*   **URL d'authentification :** `https://login.salesforce.com/services/oauth2/token`
*   **Consumer Key (ID Client) :** Clé d'application Salesforce.
*   **Consumer Secret (Secret Client) :** Secret d'application Salesforce.
*   **Nom d'utilisateur Salesforce (pour OAuth 2.0 Password Grant) :** Utilisateur dédié à l'API.
*   **Mot de passe Salesforce (pour OAuth 2.0 Password Grant) :** Mot de passe de l'utilisateur API + jeton de sécurité.
*   **Version de l'API Salesforce :** Ex: `v58.0`

## 5. Mapping des Champs

Cette section détaille le mappage des informations extraites par SalesCoach vers les objets et champs correspondants dans Salesforce.

### 5.1. Création / Mise à jour de Lead

| Champ SalesCoach (Détecté) | Objet Salesforce | Champ Salesforce (API Name) | Type de Champ | Description | Notes |
| :-------------------------- | :--------------- | :-------------------------- | :------------ | :---------- | :---- |
| Nom de l'entreprise         | Lead             | `Company`                   | Texte         | Nom de la société du prospect. | Obligatoire pour la création. |
| Prénom du contact           | Lead             | `FirstName`                 | Texte         | Prénom du prospect. | |
| Nom du contact              | Lead             | `LastName`                  | Texte         | Nom de famille du prospect. | Obligatoire pour la création. |
| Email du contact            | Lead             | `Email`                     | Email         | Adresse e-mail du prospect. | Utilisé pour la déduplication. |
| Numéro de téléphone         | Lead             | `Phone`                     | Téléphone     | Numéro de téléphone du prospect. | |
| Titre du poste              | Lead             | `Title`                     | Texte         | Fonction du prospect. | |
| Site Web de l'entreprise    | Lead             | `Website`                   | URL           | Site web de la société. | |
| Adresse (Ville)             | Lead             | `City`                      | Texte         | Ville du prospect. | |
| Adresse (Pays)              | Lead             | `Country`                   | Texte         | Pays du prospect. | |
| Description de l'opportunité| Lead             | `Description`               | Zone de texte | Résumé de l'opportunité détectée par SalesCoach. | Peut être utilisé pour des notes internes. |
| Source du Lead              | Lead             | `LeadSource`                | Liste de sélection | Valeur fixe : "SalesCoach AI" ou "IA Conversationnelle". | Nécessite que la valeur existe dans la liste de sélection `LeadSource`. |
| Statut du Lead              | Lead             | `Status`                    | Liste de sélection | Valeur fixe : "Qualifié par SalesCoach" ou "Nouveau". | Nécessite que la valeur existe dans la liste de sélection `Status`. |
| Score de qualification      | Lead             | `SalesCoach_Score__c`       | Nombre        | Score de qualification attribué par l'IA. | Champ personnalisé recommandé. |
| URL de la conversation      | Lead             | `SalesCoach_Chat_URL__c`    | URL           | Lien vers la conversation complète dans SalesCoach. | Champ personnalisé recommandé. |

### 5.2. Création / Mise à jour d'Opportunité (après conversion de Lead ou directement)

| Champ SalesCoach (Détecté) | Objet Salesforce | Champ Salesforce (API Name) | Type de Champ | Description | Notes |
| :-------------------------- | :--------------- | :-------------------------- | :------------ | :---------- | :---- |
| Nom de l'opportunité        | Opportunity      | `Name`                      | Texte         | Nom de l'opportunité (ex: "Projet X chez [Entreprise]"). | Obligatoire. |
| Nom de l'entreprise         | Opportunity      | `AccountId`                 | Lookup        | ID du compte associé. | Créé ou lié automatiquement si le Lead est converti en Compte. |
| Date de clôture prévue      | Opportunity      | `CloseDate`                 | Date          | Date estimée de clôture. | Peut être une estimation par défaut si non détectée. |
| Montant estimé              | Opportunity      | `Amount`                    | Devise        | Montant potentiel de l l'opportunité. | Peut être une estimation par défaut si non détectée. |
| Phase de l'opportunité      | Opportunity      | `StageName`                 | Liste de sélection | Ex: "Qualification", "Analyse des Besoins". | Obligatoire. Nécessite que la valeur existe. |
| Description de l'opportunité| Opportunity      | `Description`               | Zone de texte | Détails de l'opportunité extraits de la conversation. | |
| Source de l'opportunité     | Opportunity      | `LeadSource`                | Liste de sélection | Valeur fixe : "SalesCoach AI". | Nécessite que la valeur existe. |
| URL de la conversation      | Opportunity      | `SalesCoach_Chat_URL__c`    | URL           | Lien vers la conversation complète dans SalesCoach. | Champ personnalisé recommandé. |

### 5.3. Création de Tâche de Suivi

| Champ SalesCoach (Action)   | Objet Salesforce | Champ Salesforce (API Name) | Type de Champ | Description | Notes |
| :-------------------------- | :--------------- | :-------------------------- | :------------ | :---------- | :---- |
| Demande de rappel / Suivi   | Task             | `Subject`                   | Texte         | Sujet de la tâche (ex: "Relance suite conversation SalesCoach"). | Obligatoire. |
| Description du besoin       | Task             | `Description`               | Zone de texte | Résumé du contexte de la tâche. | |
| Date d'échéance             | Task             | `ActivityDate`              | Date          | Date à laquelle la tâche doit être effectuée. | Peut être J+1 ou J+X par défaut. |
| Priorité                    | Task             | `Priority`                  | Liste de sélection | Valeur fixe : "Normal" ou "Haute". | |
| Statut                      | Task             | `Status`                    | Liste de sélection | Valeur fixe : "Non commencée". | |
| Assigné à                   | Task             | `OwnerId`                   | Lookup        | ID de l'utilisateur Salesforce à qui la tâche est assignée. | L'utilisateur Salesforce qui a initié la conversation ou un utilisateur par défaut. |
| Lié à (Lead/Contact/Opp.)   | Task             | `WhatId`                    | Polymorphic   | ID du Lead, Contact ou Opportunité lié. | |
| Lié à (Contact)             | Task             | `WhoId`                     | Lookup        | ID du Contact lié (si applicable). | |

## 6. API Endpoints Utilisés (Salesforce REST API)

SalesCoach interagira avec les endpoints REST API de Salesforce pour effectuer les opérations CRUD.

### 6.1. Authentification

*   **Endpoint :** `/services/oauth2/token`
*   **Méthode :** `POST`
*   **Description :** Obtention d'un jeton d'accès OAuth 2.0.

json
// Exemple de requête (Password Grant)
POST /services/oauth2/token HTTP/1.1
Host: login.salesforce.com
Content-Type: application/x-www-form-urlencoded

grant_type=password
&client_id=[YOUR_CONSUMER_KEY]
&client_secret=[YOUR_CONSUMER_SECRET]
&username=[YOUR_SALESFORCE_USERNAME]
&password=[YOUR_SALESFORCE_PASSWORD_PLUS_SECURITY_TOKEN]


### 6.2. Opérations sur les Sobjects (Leads, Opportunités, Contacts, Tâches)

Toutes les requêtes suivantes nécessiteront le jeton d'accès obtenu lors de l'authentification dans l'en-tête `Authorization: Bearer [ACCESS_TOKEN]`.

*   **URL de base de l'API :** `https://[YOUR_INSTANCE_URL]/services/data/v[API_VERSION]`

#### 6.2.1. Création d'un enregistrement

*   **Endpoint :** `/sobjects/[ObjectName]`
*   **Méthode :** `POST`
*   **Description :** Crée un nouvel enregistrement pour l'objet spécifié.
*   **Exemple (Création de Lead) :**
    json
    POST /services/data/v58.0/sobjects/Lead HTTP/1.1
    Host: [YOUR_INSTANCE_URL]
    Authorization: Bearer [ACCESS_TOKEN]
    Content-Type: application/json

    {
        "LastName": "Dupont",
        "Company": "ACME Corp",
        "Email": "jean.dupont@acmecorp.com",
        "LeadSource": "SalesCoach AI",
        "SalesCoach_Score__c": 85
    }
    

#### 6.2.2. Mise à jour d'un enregistrement

*   **Endpoint :** `/sobjects/[ObjectName]/[RecordId]`
*   **Méthode :** `PATCH`
*   **Description :** Met à jour un enregistrement existant.
*   **Exemple (Mise à jour de Lead) :**
    json
    PATCH /services/data/v58.0/sobjects/Lead/00Qxxxxxxxxxxxxxxx HTTP/1.1
    Host: [YOUR_INSTANCE_URL]
    Authorization: Bearer [ACCESS_TOKEN]
    Content-Type: application/json

    {
        "Status": "Qualifié par SalesCoach",
        "Description": "Intérêt fort pour la solution X, besoin de démo."
    }
    

#### 6.2.3. Recherche d'enregistrements (SOQL Query)

*   **Endpoint :** `/query`
*   **Méthode :** `GET`
*   **Description :** Exécute une requête SOQL pour récupérer des enregistrements. Utilisé pour vérifier l'existence d'un Lead/Contact avant création, ou pour récupérer des IDs.
*   **Exemple (Recherche de Lead par Email) :**
    
    GET /services/data/v58.0/query?q=SELECT+Id,Status+FROM+Lead+WHERE+Email='jean.dupont@acmecorp.com' HTTP/1.1
    Host: [YOUR_INSTANCE_URL]
    Authorization: Bearer [ACCESS_TOKEN]
    

#### 6.2.4. Conversion de Lead (si géré par SalesCoach)

*   **Endpoint :** `/services/data/v58.0/sobjects/Lead/00Qxxxxxxxxxxxxxxx/converts`
*   **Méthode :** `POST`
*   **Description :** Convertit un Lead qualifié en Compte, Contact et Opportunité.
*   **Exemple :**
    json
    POST /services/data/v58.0/sobjects/Lead/00Qxxxxxxxxxxxxxxx/converts HTTP/1.1
    Host: [YOUR_INSTANCE_URL]
    Authorization: Bearer [ACCESS_TOKEN]
    Content-Type: application/json

    {
        "leadId": "00Qxxxxxxxxxxxxxxx",
        "convertedStatus": "Qualifié",
        "doNotCreateOpportunity": false,
        "opportunityName": "Opportunité pour ACME Corp",
        "sendEmailToOwner": false
    }
    

## 7. Flux de Données et Synchronisation

Le flux de données est principalement unidirectionnel de SalesCoach vers Salesforce/CRM.

1.  **Détection d'Opportunité / Qualification de Lead :**
    *   Lorsqu'une conversation textuelle atteint un seuil de qualification ou qu'une intention d'opportunité est détectée par SalesCoach.
    *   SalesCoach tente de rechercher un Lead ou Contact existant dans Salesforce via l'email ou le nom/prénom/entreprise.
    *   **Si existant :** Mise à jour du Lead/Contact avec les nouvelles informations (score, description).
    *   **Si non existant :** Création d'un nouveau Lead dans Salesforce avec les informations extraites et le statut "Nouveau" ou "Qualifié par SalesCoach".

2.  **Création d'Opportunité :**
    *   Si le Lead est qualifié et qu'une opportunité claire est identifiée (soit par conversion de Lead, soit directement si le Contact/Compte existe déjà).
    *   SalesCoach crée une nouvelle Opportunité dans Salesforce, liée au Compte et Contact appropriés.

3.  **Création de Tâche de Suivi :**
    *   Si SalesCoach détecte un besoin de relance ou une action spécifique à entreprendre par un commercial.
    *   SalesCoach crée une Tâche dans Salesforce, assignée au commercial concerné et liée au Lead/Contact/Opportunité approprié.

4.  **Synchronisation :**
    *   Les mises à jour sont effectuées en temps quasi réel dès que SalesCoach détecte une information pertinente ou une action à déclencher.
    *   Il n'y a pas de synchronisation bidirectionnelle prévue à ce stade (Salesforce -> SalesCoach).

## 8. Gestion des Erreurs et Journalisation

*   **Gestion des Erreurs API :** SalesCoach doit implémenter une gestion robuste des erreurs API (codes HTTP 4xx, 5xx) pour réagir aux échecs de création/mise à jour.
    *   **Exemples :** `400 Bad Request` (données invalides), `401 Unauthorized` (jeton expiré), `403 Forbidden` (permissions insuffisantes), `404 Not Found` (ID d'enregistrement incorrect).
    *   En cas d'erreur `401`, SalesCoach doit tenter de renouveler le jeton d'accès.
    *   En cas d'autres erreurs, les requêtes échouées doivent être journalisées.
*   **Journalisation :** Toutes les interactions avec l'API Salesforce (requêtes, réponses, succès, échecs) doivent être journalisées dans SalesCoach pour faciliter le débogage et l'audit. Les journaux doivent inclure :
    *   Timestamp de l'opération
    *   Type d'opération (Création Lead, Mise à jour Opportunité, etc.)
    *   Statut (Succès/Échec)
    *   ID de l'enregistrement Salesforce (si succès)
    *   Message d'erreur Salesforce (si échec)
    *   Payload de la requête (peut être anonymisé pour des raisons de conformité)

## 9. Sécurité

*   **Transmission sécurisée :** Toutes les communications entre SalesCoach et Salesforce/CRM doivent utiliser HTTPS (TLS 1.2 ou supérieur).
*   **Authentification :** Utilisation d'OAuth 2.0 pour l'accès API, évitant le stockage direct des identifiants utilisateur. Les jetons d'accès et de rafraîchissement doivent être stockés de manière sécurisée (chiffrés au repos).
*   **Permissions minimales :** Le compte utilisateur Salesforce utilisé pour l'intégration doit avoir le jeu de permissions le plus restreint possible, juste suffisant pour effectuer les opérations requises (CRUD sur Lead, Opportunity, Contact, Account, Task).
*   **Audit :** Les actions effectuées par SalesCoach dans Salesforce seront tracées dans les journaux d'audit de Salesforce.

## 10. Tests d'Intégration

Les tests d'intégration doivent couvrir les scénarios suivants :

*   **Authentification :** Vérifier la connexion initiale et le renouvellement de jeton.
*   **Création de Lead :** Création d'un nouveau Lead avec toutes les informations requises.
*   **Mise à jour de Lead :** Mise à jour d'un Lead existant avec de nouvelles données.
*   **Création d'Opportunité :** Création d'une Opportunité liée à un Compte/Contact existant ou suite à la conversion d'un Lead.
*   **Création de Tâche :** Création d'une Tâche de suivi liée à un enregistrement existant.
*   **Scénarios d'erreur :**
    *   Tentative de création avec des données manquantes ou invalides.
    *   Tentative de mise à jour d'un enregistrement inexistant.
    *   Erreurs d'authentification (jeton expiré, permissions insuffisantes).
*   **Déduplication :** Vérifier que SalesCoach gère correctement les cas où un Lead/Contact existe déjà.

## 11. Annexes

*   **Documentation Salesforce REST API :** [https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/sforce_rest_api.htm](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/sforce_rest_api.htm)
*   **Documentation Salesforce OAuth 2.0 :** [https://help.salesforce.com/s/articleView?id=sf.remoteaccess_oauth_overview.htm&type=5](https://help.salesforce.com/s/articleView?id=sf.remoteaccess_oauth_overview.htm&type=5)
*   **Exemples de requêtes SOQL :** [https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm)
