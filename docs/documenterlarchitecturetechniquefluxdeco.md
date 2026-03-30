# Architecture Technique de l'Agent SalesCoach

Ce document détaille l'architecture technique de l'agent IA SalesCoach, couvrant les flux de conversation, les interfaces de programmation (API) et les mécanismes d'intégration avec les systèmes CRM. L'objectif est de fournir une compréhension approfondie des composants internes et de leurs interactions.

---

## Table des Matières

1.  [Vue d'ensemble de l'Architecture](#1-vue-densemble-de-larchitecture)
2.  [Flux de Conversation](#2-flux-de-conversation)
    *   [2.1. Ingestion des Données](#21-ingestion-des-données)
    *   [2.2. Traitement du Langage Naturel (NLP)](#22-traitement-du-langage-naturel-nlp)
    *   [2.3. Moteur de Détection d'Opportunité](#23-moteur-de-détection-dopportunité)
    *   [2.4. Génération de Recommandations et d'Actions](#24-génération-de-recommandations-et-dactions)
    *   [2.5. Boucle de Rétroaction et Apprentissage](#25-boucle-de-rétroaction-et-apprentissage)
3.  [API de l'Agent SalesCoach](#3-api-de-lagent-salescoach)
    *   [3.1. API Exposées](#31-api-exposées)
    *   [3.2. API Internes](#32-api-internes)
4.  [Intégrations CRM (Exemple: Salesforce)](#4-intégrations-crm-exemple-salesforce)
    *   [4.1. Vue d'ensemble de l'intégration](#41-vue-densemble-de-lintégration)
    *   [4.2. Flux de Données](#42-flux-de-données)
    *   [4.3. Mécanismes d'Intégration](#43-mécanismes-dintégration)
    *   [4.4. Mapping des Champs CRM](#44-mapping-des-champs-crm)
    *   [4.5. Gestion des Erreurs et Journalisation](#45-gestion-des-erreurs-et-journalisation)
5.  [Considérations de Sécurité et de Performance](#5-considérations-de-sécurité-et-de-performance)

---

## 1. Vue d'ensemble de l'Architecture

L'architecture de l'agent SalesCoach est conçue pour être modulaire, scalable et résiliente. Elle se compose de plusieurs micro-services interconnectés, chacun responsable d'une fonction spécifique, facilitant ainsi le développement, le déploiement et la maintenance.

mermaid
graph TD
    A[Source Conversation (Chat, Email, SMS)] --> B(Service d'Ingestion)
    B --> C(Service NLP & Analyse Sémantique)
    C --> D(Moteur de Détection d'Opportunité IA)
    D --> E(Service de Génération de Recommandations)
    E --> F(Interface Utilisateur / Tableau de Bord Commercial)
    E --> G(Service d'Intégration CRM)
    G --> H(Système CRM - Salesforce, HubSpot, etc.)
    F -.-> I(Boucle de Rétroaction / Validation Utilisateur)
    I --> D
    Subgraph SalesCoach Core
        C
        D
        E
    End


**Composants Principaux:**

*   **Service d'Ingestion:** Collecte les conversations textuelles depuis diverses sources.
*   **Service NLP & Analyse Sémantique:** Prétraite le texte, extrait les entités, les intentions et le sentiment.
*   **Moteur de Détection d'Opportunité IA:** Cœur de l'agent, applique les modèles d'IA et les règles métier pour identifier les signaux d'opportunité.
*   **Service de Génération de Recommandations:** Formule des suggestions d'actions et de réponses pour le commercial.
*   **Service d'Intégration CRM:** Gère la synchronisation bidirectionnelle des données avec les systèmes CRM.
*   **Interface Utilisateur / Tableau de Bord:** Présente les recommandations et permet la validation des actions.
*   **Boucle de Rétroaction:** Permet l'amélioration continue des modèles IA via les retours utilisateurs.

## 2. Flux de Conversation

Le traitement d'une conversation par l'agent SalesCoach suit un parcours défini, de l'ingestion à la génération d'actions.

### 2.1. Ingestion des Données

Le service d'ingestion est responsable de la réception et de la normalisation des conversations textuelles.

*   **Sources:** Messageries instantanées (Slack, Teams), plateformes de chat web, emails, SMS, enregistrements de conversations transcrits.
*   **Mécanismes:**
    *   **Webhooks:** Les plateformes externes envoient des notifications en temps réel lors de nouveaux messages.
    *   **API Polling:** L'agent interroge périodiquement les sources pour de nouvelles données (moins recommandé pour le temps réel).
    *   **Connecteurs Directs:** Modules spécifiques pour des intégrations profondes (ex: Salesforce Chat).
*   **Format:** Les conversations sont converties en un format JSON standardisé, incluant l'ID de la conversation, l'historique des messages, les participants, et les métadonnées (horodatage, source).

### 2.2. Traitement du Langage Naturel (NLP)

Une fois ingérées, les conversations sont traitées par le module NLP.

*   **Nettoyage du Texte:** Suppression des bruits (emojis, liens non pertinents), normalisation (minuscules, lemmatisation).
*   **Détection de Langue:** Identification de la langue de la conversation.
*   **Segmentation:** Découpage de la conversation en tours de parole et phrases.
*   **Extraction d'Entités Nommées (NER):** Identification des informations clés (noms de personnes, organisations, produits, dates, montants).
*   **Classification d'Intention:** Détection des intentions derrière les messages (question, objection, intérêt, demande de démo).
*   **Analyse de Sentiment:** Évaluation du ton général de la conversation ou de messages spécifiques (positif, négatif, neutre).
*   **Vectorisation:** Conversion du texte en représentations numériques (embeddings) pour les modèles d'IA.

### 2.3. Moteur de Détection d'Opportunité

C'est le cœur intelligent de l'agent, où les données NLP sont analysées pour identifier les signaux d'opportunité.

*   **Modèles IA:** Utilisation de modèles de Machine Learning (ML) et de Deep Learning (DL) entraînés sur des données de conversations commerciales.
    *   **Classification Binaire/Multi-classes:** Pour détecter la présence d'une opportunité, le type d'opportunité (nouvelle, cross-sell, up-sell).
    *   **Modèles de Séquence:** Pour analyser l'évolution de l'intérêt au fil de la conversation.
*   **Règles Métier:** Application de règles prédéfinies basées sur des mots-clés, des phrases spécifiques, ou des combinaisons d'entités (ex: "besoin de [produit X]" + "budget de [montant]").
*   **Prompts IA:** Utilisation de Large Language Models (LLM) avec des prompts spécifiques pour :
    *   **Synthétiser** l'état actuel de la conversation.
    *   **Identifier** les "pain points" et les besoins implicites.
    *   **Qualifier** la maturité du lead (BANT, MEDDIC, etc.).
    *   **Détecter** les signaux d'achat ou les objections.
*   **Calcul de Score:** Attribution d'un score de probabilité ou de maturité à l'opportunité détectée.

### 2.4. Génération de Recommandations et d'Actions

Basé sur les opportunités détectées, l'agent génère des suggestions pour le commercial.

*   **Types de Recommandations:**
    *   **Prochaine Étape Suggérée:** "Proposer une démo", "Envoyer une étude de cas", "Qualifier le budget".
    *   **Réponses Pré-rédigées:** Suggestions de messages à envoyer, adaptées au contexte.
    *   **Ressources Pertinentes:** Liens vers des fiches produit, des témoignages clients, des FAQ.
*   **Actions Automatisées (avec validation):**
    *   **Création d'Opportunité CRM:** Pré-remplir les champs pour une nouvelle opportunité dans le CRM.
    *   **Mise à Jour de Lead/Contact CRM:** Ajouter des notes, mettre à jour le statut, attribuer des tâches.
    *   **Planification de Suivi:** Suggérer la création d'une tâche de relance.
*   **Personnalisation:** Les recommandations sont adaptées au profil du commercial et aux préférences de l'entreprise.

### 2.5. Boucle de Rétroaction et Apprentissage

L'agent apprend et s'améliore continuellement grâce aux interactions utilisateur.

*   **Validation Utilisateur:** Le commercial valide ou rejette les recommandations et actions proposées par l'agent.
*   **Notation:** Le commercial peut noter la pertinence des suggestions.
*   **Données d'Entraînement:** Ces retours sont collectés et utilisés pour réentraîner et affiner les modèles IA, améliorant ainsi la précision et la pertinence des détections et des recommandations futures.

## 3. API de l'Agent SalesCoach

L'agent SalesCoach expose des API pour permettre son intégration dans des environnements externes et utilise des API internes pour la communication entre ses propres services.

### 3.1. API Exposées

Ces API sont conçues pour permettre à des applications tierces (ex: plateformes de communication, CRM) d'interagir avec l'agent SalesCoach.

*   **Architecture:** RESTful API, utilisant JSON pour les formats de requête et de réponse.
*   **Authentification:** OAuth 2.0 ou Clés API (API Keys) pour sécuriser l'accès.
*   **Endpoints Clés:**

    *   `POST /api/v1/conversations/process`
        *   **Description:** Envoie une nouvelle conversation ou un segment de conversation pour analyse.
        *   **Request Body:**
            json
            {
              "conversation_id": "string",
              "messages": [
                {
                  "sender_id": "string",
                  "timestamp": "ISO 8601 datetime",
                  "text": "string"
                }
              ],
              "metadata": {
                "source": "string",
                "lead_id": "string",
                "contact_id": "string"
              }
            }
            
        *   **Response Body:**
            json
            {
              "status": "success",
              "analysis_id": "string",
              "recommendations": [
                {
                  "type": "opportunity_detected",
                  "score": 0.85,
                  "details": {
                    "opportunity_name": "Projet X - Renouvellement",
                    "value_estimate": 50000,
                    "stage": "Qualification",
                    "reason": "Client a exprimé un besoin de renouvellement avec des fonctionnalités Y."
                  }
                },
                {
                  "type": "suggested_action",
                  "action": "create_crm_opportunity",
                  "description": "Créer une opportunité dans Salesforce",
                  "payload": { /* CRM specific fields */ }
                },
                {
                  "type": "suggested_response",
                  "text": "Merci pour votre intérêt ! Seriez-vous disponible pour une courte démonstration la semaine prochaine ?",
                  "confidence": 0.92
                }
              ]
            }
            

    *   `GET /api/v1/analyses/{analysis_id}`
        *   **Description:** Récupère les résultats d'une analyse spécifique.

    *   `POST /api/v1/feedback`
        *   **Description:** Soumet le feedback utilisateur sur les recommandations.
        *   **Request Body:**
            json
            {
              "analysis_id": "string",
              "recommendation_id": "string",
              "feedback_type": "accepted" | "rejected" | "modified",
              "comment": "string (optional)"
            }
            

### 3.2. API Internes

Ces API facilitent la communication entre les différents micro-services au sein de l'architecture SalesCoach. Elles sont généralement exposées via un bus de messages ou un mécanisme RPC interne (ex: gRPC).

*   **Exemples:**
    *   `NLP_Service.analyze_text(text)`
    *   `Opportunity_Engine.detect_opportunity(nlp_output)`
    *   `Recommendation_Generator.generate_recommendations(opportunity_details)`
    *   `CRM_Integration_Service.create_opportunity(payload)`

## 4. Intégrations CRM (Exemple: Salesforce)

L'intégration avec les systèmes CRM est cruciale pour que SalesCoach puisse enrichir les données existantes et initier des actions concrètes. Salesforce est pris comme exemple représentatif.

### 4.1. Vue d'ensemble de l'intégration

L'intégration vise à établir un pont bidirectionnel entre SalesCoach et le CRM, permettant à l'agent d'accéder au contexte client et d'y enregistrer les opportunités et actions détectées.

### 4.2. Flux de Données

*   **De SalesCoach vers CRM:**
    *   **Création d'Opportunités:** Quand une nouvelle opportunité est détectée.
    *   **Mise à Jour de Leads/Contacts:** Ajout de notes, mise à jour du statut, enrichissement des informations.
    *   **Création de Tâches/Activités:** Planification de rappels ou d'actions pour le commercial.
    *   **Journalisation des Interactions:** Enregistrement des analyses et recommandations de SalesCoach dans l'historique de l'activité.
*   **De CRM vers SalesCoach:**
    *   **Récupération de Contexte:** Accès aux informations du lead/contact/compte (historique, produits achetés, statut actuel) pour affiner l'analyse de conversation.
    *   **Mise à Jour des Données de Référence:** Synchronisation des listes de produits, des étapes du pipeline, etc.

### 4.3. Mécanismes d'Intégration

*   **API REST (Salesforce REST API):** Méthode principale pour interagir avec Salesforce.
    *   Authentification via OAuth 2.0 (JWT Bearer Flow ou Web Server Flow).
    *   Utilisation des ressources standard (Opportunity, Lead, Contact, Task, Account) et potentiellement des objets personnalisés.
*   **Webhooks Salesforce (Outbound Messages):** Pour que Salesforce notifie SalesCoach de certains événements (ex: mise à jour d'un statut de lead, création d'une nouvelle tâche).
*   **Connecteurs Dédiés:** Utilisation de SDK ou de bibliothèques spécifiques au CRM pour simplifier l'intégration.

### 4.4. Mapping des Champs CRM

Un mapping précis des champs est essentiel pour assurer la cohérence des données. Voici un exemple pour la création d'une opportunité dans Salesforce.

| Champ SalesCoach (Source) | Champ Salesforce (Destination) | Type de Donnée | Description |
| :------------------------ | :---------------------------- | :------------- | :----------------------------------------------------------------------------------------------------- |
| `opportunity_name`        | `Name`                        | String         | Nom de l'opportunité généré par l'IA. |
| `account_id`              | `AccountId`                   | ID             | ID du compte Salesforce associé. |
| `contact_id`              | `ContactId`                   | ID             | ID du contact Salesforce principal. |
| `value_estimate`          | `Amount`                      | Currency       | Estimation de la valeur de l'opportunité. |
| `stage`                   | `StageName`                   | Picklist       | Étape du processus de vente (ex: Qualification, Proposition). |
| `close_date_estimate`     | `CloseDate`                   | Date           | Date de clôture estimée. |
| `description`             | `Description`                 | Long Text Area | Résumé de l'opportunité et des points clés de la conversation. |
| `source_conversation_id`  | `SalesCoach_Conversation_ID__c` | String         | ID de la conversation SalesCoach (champ personnalisé). |
| `salescoach_score`        | `SalesCoach_Score__c`         | Number         | Score de probabilité/maturité attribué par SalesCoach (champ personnalisé). |

### 4.5. Gestion des Erreurs et Journalisation

*   **Gestion des Erreurs:** Mise en place de mécanismes de retry avec backoff exponentiel pour les appels API échoués. Notification des erreurs critiques.
*   **Journalisation:** Enregistrement détaillé de toutes les interactions avec le CRM (requêtes, réponses, erreurs) pour audit et débogage.

## 5. Considérations de Sécurité et de Performance

*   **Sécurité des Données:**
    *   Chiffrement des données en transit (TLS/SSL) et au repos (AES-256).
    *   Contrôle d'accès basé sur les rôles (RBAC) pour les utilisateurs et les services.
    *   Conformité aux réglementations (RGPD, CCPA) concernant les données personnelles.
*   **Scalabilité:**
    *   Architecture micro-services permettant la mise à l'échelle horizontale des composants indépendamment.
    *   Utilisation de services cloud managés pour les bases de données, le stockage et le calcul.
*   **Latence:**
    *   Optimisation des modèles IA pour des temps de réponse rapides.
    *   Mise en cache des données fréquemment utilisées.
    *   Déploiement dans des régions géographiques proches des utilisateurs finaux.
⚠ Gemini error: 200 OK from POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent
