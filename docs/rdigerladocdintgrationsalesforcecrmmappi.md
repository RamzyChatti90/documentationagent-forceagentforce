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