
# Documentation Complète de l'Agent Force Commercial (SalesCoach)

**Version :** 1.0.0
**Date :** 24 mai 2024
**Auteur :** Équipe Documentation Agent Force

markdown
# Documentation Agent Force Commercial (SalesCoach)

**Version:** 1.0
**Date:** 25 juillet 2024
**Auteur:** [Votre Nom/Équipe de Documentation]

---

## Table des Matières


*   [Introduction](#introduction)
*   [1. Cahier des Charges Fonctionnel de l'Agent SalesCoach](#1-cahier-des-charges-fonctionnel-de-lagent-salescoach)
    *   [1.1. Objectifs](#11-objectifs)
    *   [1.2. Périmètre Fonctionnel](#1.2-périmètre-fonctionnel)
    *   [1.3. Exigences Non Fonctionnelles](#13-exigences-non-fonctionnelles)
*   [2. Architecture Technique](#2-architecture-technique)
    *   [2.1. Flux de Conversation](#21-flux-de-conversation)
    *   [2.2. API et Services](#22-api-et-services)
    *   [2.3. Intégrations CRM](#23-intégrations-crm)
*   [3. Guide Utilisateur](#3-guide-utilisateur)
    *   [3.1. Prise en Main et Configuration Initiale](#31-prise-en-main-et-configuration-initiale)
    *   [3.2. Exemples de Conversations et Interaction](#32-exemples-de-conversations-et-interaction)
    *   [3.3. Gestion et Suivi des Opportunités](#33-gestion-et-suivi-des-opportunités)
*   [4. Spécifications IA et Règles de Détection d'Opportunité](#4-spécifications-ia-et-règles-de-détection-dopportunité)
    *   [4.1. Prompts IA Principaux](#41-prompts-ia-principaux)
    *   [4.2. Règles de Détection d'Opportunité](#42-règles-de-détection-dopportunité)
    *   [4.3. Gestion des Intentions et Entités](#43-gestion-des-intentions-et-entités)
*   [5. Cas d'Usage Métier](#5-cas-dusage-métier)
    *   [5.1. Qualification de Lead Automatisée](#51-qualification-de-lead-automatisée)
    *   [5.2. Création d'Opportunité Proactive](#52-création-dopportunité-proactive)
    *   [5.3. Suivi et Relance Optimisés](#53-suivi-et-relance-optimisés)
*   [6. Diagramme de Flux Conversationnel](#6-diagramme-de-flux-conversationnel)
*   [7. Documentation d'Intégration Salesforce/CRM](#7-documentation-dintégration-salesforcecrm)
    *   [7.1. Mapping des Champs](#71-mapping-des-champs)
    *   [7.2. Endpoints API et Authentification](#72-endpoints-api-et-authentification)
    *   [7.3. Scénarios d'Intégration et Personnalisation](#73-scénarios-dintégration-et-personnalisation)
*   [8. Synthèse de la Présentation aux Stakeholders](#8-synthèse-de-la-présentation-aux-stakeholders)
    *   [8.1. Démonstration des Fonctionnalités Clés](#81-démonstration-des-fonctionnalités-clés)
    *   [8.2. Métriques Attendues et Retour sur Investissement (ROI)](#82-métriques-attendues-et-retour-sur-investissement-roi)
*   [Conclusion](#conclusion)
*   [Annexes](#annexes)

---

## Introduction

Ce document constitue la documentation complète de l'Agent Force Commercial, alias SalesCoach. SalesCoach est un agent d'Intelligence Artificielle conçu pour assister les développeurs commerciaux (SDR/BDR) et les commerciaux dans la détection, la qualification et la création d'opportunités à partir de conversations textuelles (e-mails, chats, messageries professionnelles).

L'objectif de cette documentation est de fournir une vue d'ensemble détaillée de l'agent, couvrant son architecture technique, ses fonctionnalités, son guide d'utilisation, ses spécifications IA, ses cas d'usage métier et ses intégrations. Ce document est destiné à un public varié, incluant les équipes techniques, les commerciaux, les chefs de produit et les parties prenantes.

---

## 1. Cahier des Charges Fonctionnel de l'Agent SalesCoach

### 1.1. Objectifs

*   **Accélérer la détection d'opportunités :** Identifier automatiquement les signaux d'achat et les intentions commerciales dans les conversations textuelles.
*   **Améliorer la qualification des leads :** Extraire les informations clés (besoins, budget, autorité, échéance - BANT) pour une qualification rapide et précise.
*   **Optimiser la création d'opportunités :** Proposer la création d'opportunités dans le CRM avec des champs pré-remplis.
*   **Soutenir le suivi commercial :** Suggérer des actions de relance et de suivi basées sur l'état des conversations.
*   **Réduire la charge administrative :** Automatiser la saisie des données dans le CRM et la préparation des briefs.

### 1.2. Périmètre Fonctionnel

*   **Analyse de conversations textuelles :** Traitement des messages entrants et sortants.
*   **Détection d'intentions commerciales :** Identification de demandes de démo, de devis, de réunions, de problèmes, etc.
*   **Extraction d'informations clés :** Récupération des informations BANT, des interlocuteurs, des entreprises.
*   **Proposition d'actions :** Suggérer des actions au commercial (créer opportunité, qualifier, planifier rdv, relancer).
*   **Intégration CRM :** Connexion bidirectionnelle avec les systèmes CRM (Salesforce, HubSpot, etc.) pour la création/mise à jour de leads, contacts, comptes et opportunités.
*   **Interface utilisateur :** Affichage des analyses et suggestions de l'agent dans une interface conviviale.
*   **Personnalisation :** Possibilité de configurer les règles de détection et les prompts.

### 1.3. Exigences Non Fonctionnelles

*   **Performance :** Temps de réponse de l'agent inférieur à 2 secondes pour l'analyse d'une conversation standard.
*   **Fiabilité :** Taux de disponibilité de 99,9%.
*   **Sécurité :** Conformité aux normes de sécurité des données (RGPD, ISO 27001). Chiffrement des données en transit et au repos.
*   **Évolutivité :** Capacité à gérer un volume croissant de conversations et d'utilisateurs.
*   **Maintenabilité :** Code documenté, architecture modulaire.
*   **Ergonomie :** Interface utilisateur intuitive et facile à prendre en main.

---

## 2. Architecture Technique

### 2.1. Flux de Conversation

Le flux de conversation de l'agent SalesCoach se déroule comme suit :

1.  **Ingestion de données :** Les conversations textuelles (e-mails via connecteur IMAP/API, messages chat via webhook/API) sont ingérées dans le système.
2.  **Pré-traitement :** Nettoyage du texte, tokenisation, normalisation.
3.  **Analyse IA (NLU/NLP) :**
    *   **Détection d'intention :** Identification des intentions de l'interlocuteur (demande d'information, objection, intérêt pour un produit, etc.).
    *   **Extraction d'entités :** Reconnaissance des entités nommées (dates, lieux, noms d'entreprises, rôles, montants, produits).
    *   **Analyse de sentiment :** Évaluation du ton de la conversation.
4.  **Application des règles métiers :** Sur la base des intentions et entités détectées, et des règles configurées, l'agent évalue la pertinence commerciale.
5.  **Génération de suggestions :** L'agent génère des suggestions d'actions pour le commercial (ex: "Créer une opportunité pour X", "Planifier un rdv avec Y", "Qualifier Z").
6.  **Intégration CRM :** Les informations extraites et les suggestions sont poussées vers le CRM via API.
7.  **Affichage UI :** Les suggestions sont présentées au commercial via l'interface utilisateur de SalesCoach ou directement dans le CRM (via widget).

mermaid
graph TD
    A[Source de Conversation (Email, Chat)] --> B(Connecteur / Webhook)
    B --> C(Service d'Ingestion)
    C --> D{File d'Attente de Traitement}
    D --> E[Service de Pré-traitement]
    E --> F[Moteur IA (NLU/NLP)]
    F --> G[Moteur de Règles Métiers]
    G --> H[Service de Génération de Suggestions]
    H --> I[Base de Données (Historique, Configuration)]
    H --> J[API d'Intégration CRM]
    J --> K[CRM (Salesforce, HubSpot)]
    H --> L[Interface Utilisateur SalesCoach]
    K --> L


### 2.2. API et Services

*   **API d'Ingestion :** Permet d'envoyer des conversations textuelles à l'agent.
*   **API de Configuration :** Gère la personnalisation des règles, des prompts et des mappings CRM.
*   **API de Suggestion :** Expose les suggestions générées par l'agent pour intégration dans d'autres systèmes.
*   **API de Callback/Webhook :** Notifie les systèmes externes des actions effectuées ou des mises à jour.
*   **Microservices :** L'architecture est basée sur des microservices pour la scalabilité (e.g., service d'ingestion, service NLU, service de règles, service d'intégration CRM).

### 2.3. Intégrations CRM

SalesCoach est conçu pour s'intégrer de manière transparente avec les principaux CRM du marché, notamment Salesforce et HubSpot.

*   **Mécanisme :** Utilisation des API REST des CRM pour la création, la lecture, la mise à jour et la suppression (CRUD) des objets (Leads, Contacts, Comptes, Opportunités, Tâches).
*   **Authentification :** Supporte OAuth 2.0 pour une connexion sécurisée.
*   **Mapping :** Configuration flexible du mapping des champs entre les entités détectées par SalesCoach et les champs du CRM.

---

## 3. Guide Utilisateur

### 3.1. Prise en Main et Configuration Initiale

*   **Accès à l'application :** Se connecter via l'URL fournie avec les identifiants d'entreprise.
*   **Connexion CRM :**
    1.  Naviguer vers "Paramètres" > "Intégrations CRM".
    2.  Sélectionner votre CRM (ex: Salesforce).
    3.  Cliquer sur "Connecter" et suivre les étapes d'authentification OAuth.
    4.  Vérifier l'état de la connexion.
*   **Configuration des préférences :**
    1.  Dans "Paramètres" > "Préférences", définir les seuils de détection d'opportunité.
    2.  Choisir les notifications souhaitées (e-mail, notification in-app).

*Capture d'écran : Tableau de bord SalesCoach avec les principales métriques et un aperçu des conversations analysées.*
(Image: `dashboard_salescoach.png`)

### 3.2. Exemples de Conversations et Interaction

SalesCoach analyse vos conversations (e-mails, chats) et affiche des suggestions pertinentes.

**Exemple 1 : Détection d'une demande de démo**

*   **Conversation :**
    *   `Client : "Bonjour, je suis très intéressé par votre solution et j'aimerais en savoir plus. Serait-il possible d'organiser une démonstration la semaine prochaine ?" `
*   **Suggestion SalesCoach :**
    *   **Action :** Créer une tâche "Planifier démo" pour [Nom du Client].
    *   **Résumé :** Le client exprime un vif intérêt et demande une démo.
    *   **Info extraite :** Intention = `Demande de Démo`, Contact = `Nom du Client`, Échéance = `Semaine prochaine`.
    *   [Bouton : "Planifier Démo via CRM"] [Bouton : "Ignorer"]

*Capture d'écran : Interface de conversation SalesCoach montrant une suggestion de démo.*
(Image: `conversation_demo_suggestion.png`)

**Exemple 2 : Qualification d'une opportunité**

*   **Conversation :**
    *   `Commercial : "Quel est le budget alloué à ce projet ?" `
    *   `Client : "Nous avons un budget d'environ 50 000€ pour la première phase." `
    *   `Commercial : "Qui est le décideur principal pour cette initiative ?" `
    *   `Client : "Mme Dupont, notre Directrice des Opérations, est la décisionnaire finale." `
*   **Suggestion SalesCoach :**
    *   **Action :** Mettre à jour l'opportunité existante pour [Nom du Client].
    *   **Résumé :** Informations BANT clés détectées.
    *   **Info extraite :** Budget = `50 000€`, Décideur = `Mme Dupont (Directrice des Opérations)`.
    *   [Bouton : "Mettre à jour Opportunité dans CRM"] [Bouton : "Marquer comme qualifié"]

### 3.3. Gestion et Suivi des Opportunités

*   **Vue des opportunités :** Le tableau de bord de SalesCoach affiche les opportunités détectées et leur statut.
*   **Création rapide :** En un clic, créez une opportunité dans votre CRM avec les informations pré-remplies par SalesCoach.
*   **Mise à jour :** Mettez à jour les champs d'une opportunité existante directement depuis les suggestions de SalesCoach.
*   **Suivi des actions :** SalesCoach peut suggérer des relances si une conversation stagne ou si une date clé approche.

*Capture d'écran : Liste des opportunités gérées par SalesCoach avec leur statut et les actions recommandées.*
(Image: `opportunities_list.png`)

---

## 4. Spécifications IA et Règles de Détection d'Opportunité

### 4.1. Prompts IA Principaux

Les prompts sont les instructions données au modèle de langage pour guider son analyse et sa génération de texte.

*   **Prompt de Détection d'Intention :**
    
    "Tu es un expert en analyse de conversations commerciales. Analyse le texte suivant et identifie l'intention principale du client ou prospect. Les intentions possibles sont : 'Demande d'informations', 'Demande de démo', 'Demande de devis', 'Objection', 'Intérêt pour une fonctionnalité', 'Problème technique', 'Demande de réunion', 'Pas intéressé', 'Autre'. Retourne l'intention la plus pertinente."
    
*   **Prompt d'Extraction d'Entités (BANT) :**
    
    "À partir de la conversation donnée, extrais les informations suivantes si elles sont présentes :
    - Besoin (problème que le client cherche à résoudre)
    - Budget (montant ou fourchette de budget mentionné)
    - Autorité (nom et rôle du décideur)
    - Échéance (délai ou date pour le projet)
    Retourne ces informations sous forme JSON."
    
*   **Prompt de Génération de Suggestion d'Action :**
    
    "Compte tenu de l'intention détectée ([INTENTION]) et des entités extraites ([ENTITIES]), propose l'action commerciale la plus pertinente pour le commercial. L'action doit être concise et orientée vers le CRM (ex: 'Créer Opportunité', 'Mettre à jour Lead', 'Planifier Appel')."
    

### 4.2. Règles de Détection d'Opportunité

Les règles métiers sont appliquées après l'analyse IA pour affiner la détection et la qualification.

| Règle ID | Déclencheur (Intention/Entité)                                   | Condition Supplémentaire          | Action Suggérée                                        | Priorité |
| :------- | :-------------------------------------------------------------- | :-------------------------------- | :----------------------------------------------------- | :------- |
| RUL-001  | `Intention: Demande de Démo`                                    | N/A                               | `Créer Tâche: Planifier Démo`                          | Élevée   |
| RUL-002  | `Intention: Demande de Devis`                                   | `Entité: Budget` détecté          | `Créer Opportunité`                                    | Élevée   |
| RUL-003  | `Intention: Intérêt pour une fonctionnalité`                    | `Entité: Entreprise` connue       | `Mettre à jour Lead: Ajouter intérêt [Fonctionnalité]` | Moyenne  |
| RUL-004  | `Intention: Problème technique`                                 | N/A                               | `Créer Ticket Support`                                 | Moyenne  |
| RUL-005  | `Entité: Budget` détecté                                        | `Intention: Demande de Devis`     | `Mettre à jour Opportunité: Ajouter budget`            | Élevée   |
| RUL-006  | `Intention: Demande de réunion`                                 | N/A                               | `Créer Tâche: Planifier Réunion`                       | Élevée   |
| RUL-007  | `Intention: Objection` ou `Sentiment: Négatif`                  | N/A                               | `Suggérer Réponse: Lever l'objection/Relancer`         | Moyenne  |

### 4.3. Gestion des Intentions et Entités

*   **Intentions supportées :** Demande d'information, Demande de démo, Demande de devis, Objection, Intérêt pour une fonctionnalité, Problème technique, Demande de réunion, Pas intéressé, Remerciement, Acceptation.
*   **Entités supportées :** Personne, Organisation, Produit/Service, Date, Heure, Durée, Montant, Rôle, Localisation, URL.
*   **Mécanisme d'apprentissage :** Utilisation de modèles de Machine Learning entraînés sur des datasets de conversations commerciales. Possibilité d'affiner les modèles via des exemples fournis par l'utilisateur (fine-tuning).

---

## 5. Cas d'Usage Métier

### 5.1. Qualification de Lead Automatisée

*   **Scénario :** Un nouveau lead s'inscrit sur le site web et envoie un e-mail avec quelques questions sur la solution.
*   **Rôle de SalesCoach :**
    1.  Analyse l'e-mail, détecte l'intention `Demande d'informations` et extrait les entités (ex: nom de l'entreprise, questions spécifiques).
    2.  Suggère au commercial de `Qualifier le Lead` et de lui poser des questions complémentaires pour identifier le besoin et le budget.
    3.  Au fur et à mesure de l'échange, SalesCoach extrait les réponses du lead et met à jour automatiquement les champs de qualification dans le CRM.

### 5.2. Création d'Opportunité Proactive

*   **Scénario :** Lors d'un échange par chat, un prospect exprime clairement un besoin et demande un devis détaillé pour un projet à court terme.
*   **Rôle de SalesCoach :**
    1.  Détecte l'intention `Demande de Devis` et extrait des informations clés : `Produit/Service`, `Budget`, `Échéance`, `Autorité` (si mentionnées).
    2.  Propose au commercial de `Créer une Opportunité` dans le CRM, avec tous les champs pré-remplis (Nom de l'opportunité, Montant estimé, Date de clôture, Contact).
    3.  Le commercial valide la suggestion en un clic, et l'opportunité est créée instantanément dans Salesforce.

### 5.3. Suivi et Relance Optimisés

*   **Scénario :** Une opportunité est au stade "Proposition envoyée" depuis 7 jours sans réponse du prospect.
*   **Rôle de SalesCoach :**
    1.  Surveille l'activité des opportunités dans le CRM.
    2.  Détecte l'inactivité et l'approche d'une date de suivi configurée.
    3.  Suggère au commercial d'envoyer un e-mail de relance personnalisé, éventuellement en proposant un contenu adapté à la dernière interaction.
    4.  Peut même proposer des phrases de relance basées sur le contexte de la conversation.

---

## 6. Diagramme de Flux Conversationnel

Le diagramme ci-dessous illustre le processus décisionnel de l'agent SalesCoach face à une nouvelle conversation.

mermaid
graph TD
    A[Nouvelle Conversation Textuelle] --> B{Analyse IA (NLU/NLP)}
    B --> C{Intention Détectée ?}
    C -- Oui --> D{Extraction d'Entités (BANT, etc.)}
    C -- Non --> E[Considérer comme "Autre" / Log]

    D --> F{Application des Règles Métiers}
    F --> G{Règle de Création d'Opportunité Déclenchée ?}

    G -- Oui --> H[Suggérer "Créer Opportunité" avec pré-remplissage]
    G -- Non --> I{Règle de Mise à jour d'Opportunité / Lead Déclenchée ?}

    I -- Oui --> J[Suggérer "Mettre à jour Lead/Opportunité"]
    I -- Non --> K{Règle de Tâche / Suivi Déclenchée ?}

    K -- Oui --> L[Suggérer "Créer Tâche" (Démo, Appel, Relance)]
    K -- Non --> M[Suggérer "Analyser Manuellement" / Pas d'action spécifique]

    H --> N[Affichage UI / PUSH CRM]
    J --> N
    L --> N
    M --> N

    N --> O[Commercial Valide/Ignore]
    O --> P{Action Validée ?}
    P -- Oui --> Q[Exécuter Action dans CRM]
    P -- Non --> R[Ignorer / Log]


---

## 7. Documentation d'Intégration Salesforce/CRM

### 7.1. Mapping des Champs

Ce tableau décrit le mapping recommandé entre les entités détectées par SalesCoach et les champs standards de Salesforce (peut être adapté pour d'autres CRM).

| Entité SalesCoach | Type de Donnée | Objet Salesforce | Champ Salesforce Standard | Notes                                     |
| :---------------- | :------------- | :--------------- | :------------------------ | :---------------------------------------- |
| Nom du Contact    | Texte          | Lead / Contact   | `FirstName`, `LastName`   | Scindé si possible                        |
| Nom de l'Entreprise | Texte          | Lead / Account   | `Company`, `AccountName`  |                                           |
| Email Contact     | Email          | Lead / Contact   | `Email`                   |                                           |
| Téléphone Contact | Téléphone      | Lead / Contact   | `Phone`                   |                                           |
| Intention Clé     | Texte          | Lead / Opportunity | `Description`, `LeadSource` | Peut être utilisé pour le champ `LeadSource` |
| Besoin Client     | Texte Long     | Opportunity      | `Description`             | Détail du problème ou du besoin           |
| Budget Estimé     | Numérique      | Opportunity      | `Amount`                  | Converti en Devise du CRM                 |
| Décideur          | Texte          | Opportunity      | `PrimaryContact`          | Peut être lié à un Contact existant       |
| Échéance Projet   | Date           | Opportunity      | `CloseDate`               | Date de clôture estimée                   |
| Produit/Service Intéressé | Texte          | Opportunity      | `Product`                 | Peut être lié à un produit Salesforce     |
| Statut Qualification | Liste de choix | Lead / Opportunity | `Status`                  | Ex: "Qualifié par SalesCoach"             |

### 7.2. Endpoints API et Authentification

*   **Endpoint Principal Salesforce :** `https://[votre_instance].salesforce.com/services/data/vXX.X/`
*   **Méthode d'Authentification :** OAuth 2.0 (flux Web Server ou JWT Bearer).
    *   **Client ID :** Fourni lors de la création de l'application connectée dans Salesforce.
    *   **Client Secret :** Fourni lors de la création de l'application connectée dans Salesforce.
    *   **Callback URL :** `https://[votre_domaine_salescoach]/api/crm/callback/salesforce`
*   **Endpoints Clés Utilisés :**
    *   `POST /sobjects/Lead/` : Création d'un nouveau Lead.
    *   `PATCH /sobjects/Lead/[Id]` : Mise à jour d'un Lead existant.
    *   `POST /sobjects/Opportunity/` : Création d'une nouvelle Opportunité.
    *   `PATCH /sobjects/Opportunity/[Id]` : Mise à jour d'une Opportunité existante.
    *   `POST /sobjects/Task/` : Création d'une Tâche.
    *   `GET /query?q=[SOQL Query]` : Requêtes SOQL pour récupérer des données (ex: vérifier l'existence d'un Lead/Contact).

### 7.3. Scénarios d'Intégration et Personnalisation

*   **Installation d'un Package Managé (si applicable) :** Pour une intégration plus profonde, un package Salesforce peut être fourni pour installer des composants Lightning, des champs personnalisés ou des flows.
*   **Webhooks Salesforce :** Possibilité de configurer des webhooks dans Salesforce pour notifier SalesCoach de certains événements (ex: mise à jour d'un statut d'opportunité), permettant une synchronisation bidirectionnelle.
*   **Personnalisation des mappings :** L'interface de configuration de SalesCoach permet aux administrateurs de personnaliser le mapping des champs et d'ajouter des champs personnalisés spécifiques à leur CRM.
*   **Gestion des doublons :** Stratégies de détection et de gestion des doublons lors de la création de Leads/Contacts (ex: recherche par email avant création).

---

## 8. Synthèse de la Présentation aux Stakeholders

### 8.1. Démonstration des Fonctionnalités Clés

La présentation a mis en lumière les capacités de SalesCoach à travers une démonstration interactive, couvrant les points suivants :

*   **Analyse en temps réel :** Illustration de la détection d'opportunités et de la qualification de leads à partir de conversations e-mail et chat simulées.
*   **Interface utilisateur intuitive :** Présentation du tableau de bord de SalesCoach et de la facilité d'interaction avec les suggestions de l'agent.
*   **Intégration CRM fluide :** Démonstration de la création et de la mise à jour automatique d'opportunités et de tâches dans Salesforce en un clic.
*   **Personnalisation :** Aperçu de la configuration des règles et des prompts pour adapter l'agent aux besoins spécifiques de l'entreprise.

### 8.2. Métriques Attendues et Retour sur Investissement (ROI)

Les métriques clés et le ROI attendu ont été présentés pour justifier l'investissement dans SalesCoach.

| Indicateur Clé (KPI)              | Situation Actuelle | Cible avec SalesCoach | Impact Attendu                                         |
| :-------------------------------- | :----------------- | :-------------------- | :----------------------------------------------------- |
| **Temps de qualification de lead**| 48 heures          | 12 heures             | Réduction de 75%                                       |
| **Taux de conversion Lead -> Opp**| 15%                | 20%                   | Augmentation de 33%                                    |
| **Nombre d'opportunités créées**  | X par mois         | X + 25% par mois      | Identification plus rapide et proactive                |
| **Temps passé sur tâches admin**  | 2 heures/jour/SDR  | 0.5 heure/jour/SDR    | Gain de productivité significatif                      |
| **Revenus supplémentaires**      | -                  | +Y€ par an            | Grâce à l'augmentation des opportunités et de la conversion |
| **Satisfaction des commerciaux**  | Moyenne            | Élevée                | Moins de tâches répétitives, plus de temps pour vendre |

**Retour sur Investissement (ROI) :**
Le ROI est estimé à plus de 200% sur 12 mois, principalement grâce à :
*   L'augmentation du volume et de la qualité des opportunités.
*   La réduction des coûts opérationnels liés aux tâches administratives.
*   L'amélioration de la productivité et de la motivation des équipes commerciales.

---

## Conclusion

L'agent Force Commercial (SalesCoach) représente une avancée significative pour les équipes de vente, en automatisant et en optimisant des processus clés de détection et de qualification d'opportunités. Cette documentation fournit une base solide pour comprendre, implémenter et utiliser SalesCoach, garantissant une intégration réussie et un impact maximal sur la performance commerciale.

Les prochaines étapes incluront la mise en œuvre des retours des stakeholders, l'affinage continu des modèles IA, et l'extension des intégrations CRM pour couvrir un éventail encore plus large de plateformes.

---

## Annexes

*   **Annexe A : Glossaire des Termes Techniques**
    *   **NLU (Natural Language Understanding) :** Compréhension du langage naturel.
    *   **NLP (Natural Language Processing) :** Traitement du langage naturel.
    *   **BANT :** Budget, Authority, Need, Timeline (Critères de qualification de lead).
    *   **CRM (Customer Relationship Management) :** Gestion de la relation client.
    *   **Prompt Engineering :** Art de concevoir des requêtes efficaces pour les modèles d'IA.
*   **Annexe B : Références API Salesforce (Liens Externes)**
    *   [Salesforce Developer Documentation](https://developer.salesforce.com/docs/)
    *   [Salesforce REST API Guide](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/)
*   **Annexe C : Captures d'Écran Complémentaires**
    *   (Image: `crm_integration_settings.png`) - Page de configuration de l'intégration CRM.
    *   (Image: `rule_configuration_example.png`) - Exemple de configuration d'une règle de détection.
=======
1.  **Introduction**
    1.1. Contexte du projet
    1.2. Objectifs de la documentation
    1.3. Public Cible
    1.4. Portée du document
2.  **Cahier des Charges Fonctionnel de l'Agent SalesCoach**
    2.1. Vision et Objectifs de SalesCoach
    2.2. Fonctionnalités Clés
        2.2.1. Détection et Qualification d'Opportunités
        2.2.2. Génération de Contenu de Suivi
        2.2.3. Intégration CRM
        2.2.4. Reporting et Analyse
    2.3. Exigences Non Fonctionnelles
        2.3.1. Performance et Scalabilité
        2.3.2. Sécurité et Confidentialité
        2.3.3. Ergonomie et Expérience Utilisateur
        2.3.4. Maintenabilité
3.  **Architecture Technique de SalesCoach**
    3.1. Vue d'Ensemble de l'Architecture
    3.2. Flux de Conversation
        3.2.1. Ingestion des Conversations
        3.2.2. Traitement et Analyse IA
        3.2.3. Génération de Recommandations
        3.2.4. Interaction avec l'Utilisateur
    3.3. API et Services
        3.3.1. API d'Intégration des Plateformes de Communication
        3.3.2. API de Gestion des Opportunités
        3.3.3. API d'Administration
    3.4. Intégrations CRM et Autres Systèmes
        3.4.1. Mécanismes d'Intégration
        3.4.2. Sécurité des Intégrations
4.  **Guide Utilisateur SalesCoach**
    4.1. Premiers Pas avec SalesCoach
        4.1.1. Création de Compte et Configuration Initiale
        4.1.2. Connexion aux Plateformes de Communication
    4.2. Interface Utilisateur
        4.2.1. Tableau de Bord des Opportunités
        4.2.2. Vue Détail de Conversation
        4.2.3. Paramètres et Préférences
    4.3. Exemples de Conversations et Interactions
        4.3.1. Détection d'une Nouvelle Opportunité
        4.3.2. Qualification d'un Lead Existant
        4.3.3. Génération d'un Email de Suivi
    4.4. Gestion des Opportunités via SalesCoach
        4.4.1. Création Manuelle et Automatisée
        4.4.2. Mise à Jour et Suivi
        4.4.3. Clôture d'Opportunité
    4.5. FAQ et Dépannage
5.  **Spécifications des Prompts IA et Règles de Détection d'Opportunité**
    5.1. Principes de Conception des Prompts IA
    5.2. Prompts Clés pour l'Analyse de Conversation
        5.2.1. Prompt de Détection d'Intention
        5.2.2. Prompt de Qualification de Besoin
        5.2.3. Prompt de Génération de Proposition de Valeur
    5.3. Règles de Détection d'Opportunité
        5.3.1. Mots-clés et Expressions Déclencheurs
        5.3.2. Analyse Contextuelle et Sémantique
        5.3.3. Score de Qualification des Leads
    5.4. Mécanismes d'Apprentissage et d'Amélioration Continue
6.  **Cas d'Usage Métier**
    6.1. Qualification de Lead Automatisée
    6.2. Création et Enrichissement d'Opportunité
    6.3. Suivi et Relance Préventive
    6.4. Coaching en Temps Réel pour les Commerciaux
    6.5. Analyse des Tendances et Amélioration des Stratégies
7.  **Diagramme de Flux Conversationnel de l'Agent**
    7.1. Vue d'Ensemble du Flux
    7.2. Scénarios Clés et Arbre Décisionnel
        7.2.1. Scénario : Nouvelle Conversation / Détection de Problème
        7.2.2. Scénario : Identification d'un Besoin / Proposition de Solution
        7.2.3. Scénario : Tentative de Clôture / Suivi Post-Conversation
8.  **Documentation d'Intégration Salesforce/CRM**
    8.1. Prérequis et Configuration Initiale
    8.2. Mapping des Champs Salesforce
        8.2.1. Objets Salesforce (Lead, Contact, Opportunity, Account)
        8.2.2. Champs SalesCoach vers Champs Salesforce
        8.2.3. Personnalisation du Mapping
    8.3. Endpoints API et Authentification
        8.3.1. OAuth 2.0 pour Salesforce
        8.3.2. Endpoints pour la Création/Mise à Jour (Leads, Contacts, Opportunités)
        8.3.3. Gestion des Erreurs et Logs
    8.4. Synchronisation des Données
        8.4.1. Fréquence et Types de Synchronisation
        8.4.2. Gestion des Conflits
9.  **Présentation aux Parties Prenantes**
    9.1. Synthèse Exécutive (Executive Summary)
    9.2. Démonstration de SalesCoach (Aperçu)
    9.3. Métriques Attendues et ROI Potentiel
        9.3.1. Augmentation du Taux de Conversion
        9.3.2. Réduction du Cycle de Vente
        9.3.3. Amélioration de la Productivité Commerciale
    9.4. Feuille de Route et Prochaines Étapes
        9.4.1. Phases de Déploiement
        9.4.2. Évolutions Futures
10. **Conclusion**
11. **Annexes**
    11.1. Glossaire
    11.2. Références
    11.3. Historique des Révisions

---

## 1. Introduction

### 1.1. Contexte du projet

Le développement commercial moderne est caractérisé par un volume croissant d'interactions textuelles (emails, chats, messageries instantanées). Les équipes commerciales peinent souvent à identifier rapidement les opportunités, qualifier les leads et assurer un suivi efficace dans ce flux constant. Le projet "Agent Force Commercial" (nom de code : SalesCoach) vise à adresser ces défis en tirant parti de l'intelligence artificielle.

SalesCoach est un agent IA conçu pour analyser les conversations textuelles des développeurs commerciaux, détecter les signaux d'opportunité, qualifier les leads, et proposer des actions pertinentes pour maximiser les chances de conversion.

### 1.2. Objectifs de la documentation

Ce document a pour objectif de fournir une description complète et détaillée de l'agent SalesCoach, couvrant l'ensemble de ses aspects techniques, fonctionnels, opérationnels et stratégiques. Il servira de référence pour les développeurs, les utilisateurs finaux, les équipes de support, et les parties prenantes.

### 1.3. Public Cible

*   **Équipes de Développement :** Pour la compréhension de l'architecture, des API et des spécifications IA.
*   **Managers Commerciaux :** Pour la compréhension des cas d'usage métier et des bénéfices.
*   **Développeurs Commerciaux :** Pour le guide utilisateur et l'intégration dans leur flux de travail.
*   **Équipes Produit :** Pour la vision fonctionnelle et les évolutions futures.
*   **Parties Prenantes/Investisseurs :** Pour la présentation stratégique et le ROI.

### 1.4. Portée du document

Ce document consolide l'ensemble des livrables produits au cours du projet de documentation, incluant le cahier des charges fonctionnel, l'architecture technique, le guide utilisateur, les spécifications IA, les cas d'usage, le diagramme de flux conversationnel, la documentation d'intégration CRM, et un résumé de la présentation aux parties prenantes.

---

## 2. Cahier des Charges Fonctionnel de l'Agent SalesCoach

### 2.1. Vision et Objectifs de SalesCoach

**Vision :** Devenir l'assistant IA incontournable des développeurs commerciaux, transformant chaque conversation textuelle en une opportunité de vente qualifiée et optimisée.

**Objectifs :**
*   Augmenter le taux de détection et de qualification des opportunités.
*   Réduire le temps passé par les commerciaux sur des tâches administratives répétitives.
*   Améliorer la qualité et la pertinence des relances et des propositions.
*   Fournir des insights actionnables pour optimiser les stratégies commerciales.

### 2.2. Fonctionnalités Clés

#### 2.2.1. Détection et Qualification d'Opportunités
*   **Analyse contextuelle :** Analyser le contenu, le ton et l'historique des conversations pour identifier les signaux d'achat, les besoins exprimés, et les points de douleur.
*   **Qualification automatique :** Attribuer un score de qualification aux leads et aux opportunités basé sur des critères prédéfinis (BANT, MEDDIC, etc.).
*   **Alertes intelligentes :** Notifier le commercial en temps réel d'une opportunité détectée ou d'une action requise.

#### 2.2.2. Génération de Contenu de Suivi
*   **Rédaction d'emails/messages :** Proposer des brouillons d'emails de suivi, de propositions, ou de réponses personnalisées basés sur le contexte de la conversation.
*   **Recommandations d'actions :** Suggérer la prochaine meilleure action à entreprendre (planifier un appel, envoyer une documentation, etc.).

#### 2.2.3. Intégration CRM
*   **Création automatique d'opportunités :** Créer des fiches d'opportunités et de leads dans le CRM (ex: Salesforce) avec les informations pertinentes extraites de la conversation.
*   **Mise à jour des fiches :** Enrichir et mettre à jour les fiches existantes avec de nouvelles informations.
*   **Synchronisation bidirectionnelle :** Maintenir la cohérence des données entre SalesCoach et le CRM.

#### 2.2.4. Reporting et Analyse
*   **Tableau de bord :** Visualiser les opportunités détectées, leur statut et leur progression.
*   **Statistiques :** Fournir des métriques sur la performance de l'agent et l'efficacité des commerciaux.

### 2.3. Exigences Non Fonctionnelles

#### 2.3.1. Performance et Scalabilité
*   **Temps de réponse :** Analyse et recommandation en moins de 5 secondes pour la plupart des interactions.
*   **Volume :** Capacité à traiter des milliers de conversations simultanément.

#### 2.3.2. Sécurité et Confidentialité
*   **Conformité RGPD :** Traitement des données personnelles en conformité avec les réglementations.
*   **Cryptage :** Cryptage des données en transit et au repos.
*   **Authentification :** Mécanismes d'authentification robustes (OAuth 2.0, SSO).

#### 2.3.3. Ergonomie et Expérience Utilisateur
*   **Interface intuitive :** Facilité d'utilisation pour les commerciaux, même non technophiles.
*   **Intégration fluide :** S'intégrer naturellement dans le flux de travail existant du commercial.

#### 2.3.4. Maintenabilité
*   **Architecture modulaire :** Faciliter les mises à jour et l'ajout de nouvelles fonctionnalités.
*   **Journalisation :** Mise en place d'un système de log détaillé pour le débogage et le monitoring.

---

## 3. Architecture Technique de SalesCoach

### 3.1. Vue d'Ensemble de l'Architecture

L'architecture de SalesCoach est basée sur une approche microservices, garantissant scalabilité, résilience et maintenabilité. Elle s'articule autour de plusieurs composants clés : un module d'ingestion de données, un moteur d'IA, un module de gestion des opportunités, et des connecteurs CRM.

mermaid
graph TD
    A[Plateformes de Communication] --> B(Module d'Ingestion)
    B --> C{File d'attente de messages}
    C --> D[Service d'Analyse IA]
    D -- Prompts IA, Règles --> E[Base de Connaissances / Modèles IA]
    D --> F[Service de Recommandation]
    F --> G[Module de Gestion des Opportunités]
    G --> H[API SalesCoach]
    H -- Accès Utilisateur --> I[Interface Utilisateur SalesCoach]
    G -- Création/Mise à jour --> J[Connecteur CRM (ex: Salesforce)]
    J -- API CRM --> K[Système CRM (ex: Salesforce)]
    K -- Synchronisation --> G
    H -- Reporting --> L[Service de Reporting & Analytics]
    L --> I


### 3.2. Flux de Conversation

#### 3.2.1. Ingestion des Conversations
*   **Connecteurs :** Des adaptateurs spécifiques pour chaque plateforme (Gmail, Slack, Teams, etc.) écoutent les nouvelles conversations ou messages.
*   **Normalisation :** Les données brutes sont transformées en un format unifié pour le traitement.
*   **File d'attente :** Les messages sont placés dans une file d'attente (ex: Kafka, RabbitMQ) pour un traitement asynchrone et résilient.

#### 3.2.2. Traitement et Analyse IA
*   **Traitement du Langage Naturel (NLP) :** Identification des entités nommées, analyse de sentiment, détection d'intention.
*   **Application des prompts IA :** Le moteur d'IA utilise des Large Language Models (LLM) avec des prompts spécifiques pour évaluer le contenu, identifier les besoins, les douleurs, les budgets, les autorités et les délais (BANT).
*   **Scoring :** Attribution d'un score de qualification basé sur les règles métier et l'analyse IA.

#### 3.2.3. Génération de Recommandations
*   **Proposition d'actions :** Sur la base de l'analyse, l'agent suggère des actions (créer une opportunité, envoyer un email de suivi, planifier un meeting).
*   **Rédaction assistée :** Génération de brouillons de réponses ou d'emails personnalisés.

#### 3.2.4. Interaction avec l'Utilisateur
*   **Notifications :** Les recommandations sont poussées à l'utilisateur via l'interface SalesCoach ou directement dans la plateforme de communication (si l'intégration le permet).
*   **Feedback :** L'utilisateur peut valider, modifier ou rejeter les suggestions, ce qui alimente le système pour une amélioration continue.

### 3.3. API et Services

*   **API d'Ingestion (`/api/v1/conversations`) :** Endpoint pour recevoir les données de conversation des différentes plateformes.
*   **API d'Opportunités (`/api/v1/opportunities`) :** CRUD pour la gestion des opportunités et des leads détectés par l'IA.
*   **API de Recommandations (`/api/v1/recommendations`) :** Fournit les suggestions d'actions et de contenus.
*   **API d'Intégration CRM (`/api/v1/crm-sync`) :** Interface pour la synchronisation des données avec les systèmes CRM externes.

### 3.4. Intégrations Techniques (CRM, Plateformes de Communication)

*   **Connecteurs CRM :** Modules dédiés pour interagir avec des CRM spécifiques (Salesforce, HubSpot, Dynamics 365) via leurs APIs respectives (REST, SOAP).
*   **Authentification :** Utilisation d'OAuth 2.0 pour une connexion sécurisée aux services tiers.
*   **Gestion des Erreurs :** Mécanismes de retry et de logging pour gérer les défaillances d'intégration.

---

## 4. Guide Utilisateur SalesCoach

### 4.1. Premiers Pas avec SalesCoach

#### 4.1.1. Création de Compte et Configuration Initiale
*   Accédez à [URL de l'application SalesCoach].
*   Cliquez sur "S'inscrire" et suivez les étapes pour créer votre compte.
*   Une fois connecté, le tableau de bord s'affiche.

#### 4.1.2. Connexion aux Plateformes de Communication
*   Naviguez vers "Paramètres" > "Intégrations".
*   Cliquez sur "Connecter" à côté de la plateforme souhaitée (ex: Gmail, Slack).
*   Suivez les instructions d'authentification (généralement via OAuth 2.0). SalesCoach demandera les permissions nécessaires pour lire vos conversations.

### 4.2. Interface Utilisateur

#### 4.2.1. Tableau de Bord des Opportunités
*   **Vue d'ensemble :** Affiche un résumé des opportunités détectées, leur statut (Nouveau, Qualifié, En attente, Fermé), et les actions suggérées.
*   **Filtres et recherche :** Permet de filtrer les opportunités par commercial, par statut, par date ou par mots-clés.
*   **[Capture d'écran : Tableau de bord principal de SalesCoach]**

#### 4.2.2. Vue Détail de Conversation
*   Lorsque vous cliquez sur une opportunité, vous accédez à la conversation source.
*   **Analyse IA :** SalesCoach met en évidence les passages clés, les besoins exprimés, et les informations pertinentes.
*   **Suggestions :** Une barre latérale affiche les actions recommandées et les brouillons de messages.
*   **[Capture d'écran : Vue détail d'une conversation avec l'analyse IA et les suggestions]**

#### 4.2.3. Paramètres et Préférences
*   **Profil utilisateur :** Gestion des informations personnelles, mot de passe.
*   **Intégrations :** Gérer les connexions aux plateformes de communication et CRM.
*   **Règles de détection :** Personnaliser certains critères de détection d'opportunité (pour les administrateurs).

### 4.3. Exemples de Conversations et Interactions

#### 4.3.1. Détection d'une Nouvelle Opportunité
*   **Scénario :** Un client potentiel exprime un problème et un besoin clair dans un email.
*   **Action SalesCoach :** Détecte l'intention, crée une nouvelle opportunité et notifie le commercial.
*   **[Exemple de conversation : Email client + notification SalesCoach]**

#### 4.3.2. Qualification d'un Lead Existant
*   **Scénario :** Lors d'un échange sur Slack, un lead mentionne son budget et un délai d'implémentation.
*   **Action SalesCoach :** Met à jour le score de qualification du lead et propose de créer une tâche de suivi dans le CRM.
*   **[Exemple de conversation : Chat Slack + mise à jour du lead par SalesCoach]**

#### 4.3.3. Génération d'un Email de Suivi
*   **Scénario :** Après une conversation, le commercial souhaite envoyer un récapitulatif et une proposition.
*   **Action SalesCoach :** Propose un brouillon d'email pré-rempli avec les points clés de la conversation et un appel à l'action.
*   **[Exemple de conversation : Résumé de conversation + brouillon d'email généré par SalesCoach]**

### 4.4. Gestion des Opportunités via SalesCoach

#### 4.4.1. Création Manuelle et Automatisée
*   **Automatisée :** L'agent crée automatiquement une opportunité lorsqu'il détecte des signaux forts.
*   **Manuelle :** L'utilisateur peut créer manuellement une opportunité à partir de n'importe quelle conversation en cliquant sur le bouton "Créer Opportunité".

#### 4.4.2. Mise à Jour et Suivi
*   Les informations extraites des conversations sont utilisées pour enrichir la fiche d'opportunité.
*   Le statut de l'opportunité peut être mis à jour manuellement ou automatiquement par l'agent (ex: "Qualifié BANT", "Proposition envoyée").

#### 4.4.3. Clôture d'Opportunité
*   L'utilisateur peut marquer une opportunité comme "Gagnée" ou "Perdue". L'agent peut suggérer cette action en fonction de la conversation finale.

### 4.5. FAQ et Dépannage
*   `Q: SalesCoach ne détecte pas une opportunité évidente. Que faire ?`
    `R: Vérifiez les mots-clés configurés et assurez-vous que la conversation est bien connectée. Vous pouvez aussi signaler l'erreur pour améliorer l'IA.`
*   `Q: Comment déconnecter une intégration CRM ?`
    `R: Allez dans "Paramètres" > "Intégrations" et cliquez sur "Déconnecter" pour le CRM concerné.`

---

## 5. Spécifications des Prompts IA et Règles de Détection d'Opportunité

### 5.1. Principes de Conception des Prompts IA

Les prompts sont conçus pour guider les Large Language Models (LLM) dans l'analyse de conversations textuelles, l'extraction d'informations pertinentes et la génération de réponses ciblées. Ils sont structurés pour être clairs, précis et contextualisés, minimisant les hallucinations et maximisant la pertinence.

*   **Rôle et Contexte :** Définir clairement le rôle de l'IA (ex: "Tu es un expert en vente B2B...") et le contexte de la tâche (ex: "Analyse cette conversation client...").
*   **Instructions Spécifiques :** Énumérer les tâches précises à accomplir (ex: "Identifie le besoin, le budget, l'autorité, le délai.").
*   **Format de Sortie :** Spécifier le format attendu (JSON, liste à puces, texte libre structuré).
*   **Exemples (Few-shot learning) :** Fournir quelques exemples de conversations et les sorties attendues pour affiner la compréhension du modèle.

### 5.2. Prompts Clés pour l'Analyse de Conversation

#### 5.2.1. Prompt de Détection d'Intention
*   **Objectif :** Détecter si la conversation contient une intention d'achat, un problème non résolu, ou une demande d'information approfondie.
*   **Exemple de Prompt (pseudo-code) :**
    
    "Tu es un analyste commercial. Analyse la conversation suivante entre un prospect et un commercial.
    Détecte si le prospect exprime un besoin, un problème ou une intention d'achat claire.
    Retourne un JSON avec 'intention_detectee' (oui/non), 'type_intention' (besoin, problème, achat, info), 'resume_intention'."
    

#### 5.2.2. Prompt de Qualification de Besoin
*   **Objectif :** Extraire les informations clés pour la qualification BANT (Budget, Authority, Need, Timeline).
*   **Exemple de Prompt (pseudo-code) :**
    
    "En te basant sur la conversation fournie, extrais les éléments de qualification BANT.
    Si une information manque, indique 'Non spécifié'.
    Retourne un JSON avec 'besoin', 'budget', 'autorité', 'délai'."
    

#### 5.2.3. Prompt de Génération de Proposition de Valeur
*   **Objectif :** Générer un paragraphe de proposition de valeur personnalisé basé sur le besoin identifié du prospect.
*   **Exemple de Prompt (pseudo-code) :**
    
    "Le prospect [Nom du Prospect] a exprimé le besoin suivant : [Besoin du Prospect].
    Notre produit/service [Nom du Produit] offre [Bénéfice 1], [Bénéfice 2].
    Rédige un court paragraphe de proposition de valeur qui résonne avec son besoin spécifique."
    

### 5.3. Règles de Détection d'Opportunité

Les règles de détection combinent l'analyse NLP/IA avec des critères métier prédéfinis pour attribuer un score de qualification et déclencher la création d'une opportunité.

#### 5.3.1. Mots-clés et Expressions Déclencheurs
*   **Catégories :** "Problème" (ex: "difficulté", "challenge", "manque de"), "Besoin" (ex: "recherche", "nécessite", "solution pour"), "Budget" (ex: "prix", "coût", "investissement"), "Délai" (ex: "avant fin du mois", "urgent", "prochain trimestre").
*   **Pondération :** Chaque mot-clé ou expression a un poids qui contribue au score global.

#### 5.3.2. Analyse Contextuelle et Sémantique
*   **Combinaisons :** Détection de combinaisons de mots-clés dans une même phrase ou paragraphe.
*   **Sentiment :** Un sentiment positif ou neutre est généralement requis pour valider une opportunité (évite de créer des opportunités sur des plaintes).
*   **Historique :** L'historique des conversations avec le contact est pris en compte pour éviter les doublons ou les fausses positives.

#### 5.3.3. Score de Qualification des Leads
*   **Seuil :** Une opportunité est créée si le score de qualification dépasse un seuil configurable (ex: 70/100).
*   **Critères :**
    *   **B**udget : +20 points si mentionné et positif.
    *   **A**uthority : +20 points si le contact est un décideur ou a un pouvoir d'influence.
    *   **N**eed : +30 points si un besoin clair et avéré est exprimé.
    *   **T**imeline : +15 points si un délai est mentionné.
    *   **Engagement :** +15 points si le prospect pose des questions détaillées ou exprime un intérêt soutenu.

### 5.4. Mécanismes d'Apprentissage et d'Amélioration Continue

*   **Feedback Utilisateur :** Les validations ou rejets des suggestions par les commerciaux sont enregistrés et utilisés pour affiner les modèles.
*   **Ré-entraînement périodique :** Les modèles IA sont ré-entraînés régulièrement avec les nouvelles données et le feedback pour améliorer la précision.
*   **A/B Testing :** Expérimentation de différents prompts ou règles pour identifier les plus performants.

---

## 6. Cas d'Usage Métier

SalesCoach transforme la façon dont les commerciaux interagissent avec leurs prospects et gèrent leurs opportunités.

### 6.1. Qualification de Lead Automatisée

*   **Description :** SalesCoach analyse toutes les conversations entrantes (emails, chats) pour identifier les leads potentiels et les qualifier automatiquement selon des critères prédéfinis (ex: BANT).
*   **Bénéfice :** Gain de temps considérable pour les commerciaux qui n'ont plus à lire chaque message en détail pour identifier les signaux faibles. Les leads les plus prometteurs sont priorisés.
*   **Exemple :** Un email d'un nouveau contact mentionne un "projet de refonte du système CRM avec un budget alloué pour le prochain trimestre". SalesCoach détecte un besoin, un budget et un délai, qualifie le lead comme "chaud" et crée une opportunité dans Salesforce.

### 6.2. Création et Enrichissement d'Opportunité

*   **Description :** Dès qu'une opportunité est détectée, SalesCoach peut créer une fiche d'opportunité détaillée dans le CRM, pré-remplie avec les informations extraites de la conversation (nom de l'entreprise, contact, besoin, budget, délai). L'agent continue d'enrichir cette fiche au fur et à mesure des échanges.
*   **Bénéfice :** Réduction des tâches administratives, amélioration de la qualité des données CRM, vision 360° de l'opportunité toujours à jour.
*   **Exemple :** Après plusieurs échanges, SalesCoach extrait le nom du décideur et son rôle, la date prévue de décision, et les concurrents mentionnés, et met à jour l'opportunité correspondante.

### 6.3. Suivi et Relance Préventive

*   **Description :** SalesCoach surveille les conversations en cours et l'historique des échanges pour identifier les moments opportuns pour une relance, ou pour alerter le commercial en cas de silence prolongé ou de signaux négatifs. Il peut même proposer des brouillons de messages de relance personnalisés.
*   **Bénéfice :** Diminution des opportunités "oubliées", amélioration du taux de conversion grâce à des relances ciblées et au bon moment.
*   **Exemple :** Une opportunité est restée inactive pendant 7 jours. SalesCoach envoie une alerte au commercial et propose un email de relance axé sur la valeur ajoutée ou un cas d'étude pertinent.

### 6.4. Coaching en Temps Réel pour les Commerciaux

*   **Description :** Pendant une conversation, SalesCoach peut fournir des suggestions en temps réel au commercial, comme des objections à traiter, des informations complémentaires à demander, ou des points à valider avec le prospect.
*   **Bénéfice :** Aide à la décision, amélioration des compétences de vente, standardisation des bonnes pratiques.
*   **Exemple :** Un prospect exprime une hésitation sur le prix. SalesCoach suggère au commercial de rappeler la proposition de valeur ou de proposer une démo personnalisée pour justifier l'investissement.

---

## 7. Diagramme de Flux Conversationnel de l'Agent

### 7.1. Vue d'Ensemble du Flux

Le diagramme de flux conversationnel représente le parcours logique de l'agent SalesCoach, de l'ingestion d'une nouvelle conversation à la proposition d'actions et à l'intégration CRM. Il illustre l'arbre décisionnel de l'agent face à différentes situations.

mermaid
graph TD
    A[Nouvelle Conversation Textuelle (Email, Chat)] --> B{Ingestion & Normalisation}
    B --> C{Analyse NLP & IA: Détection d'Intention}
    C -- Intention Non Claire/Info --> D[Enregistrement de l'Interaction]
    C -- Intention Claire (Problème, Besoin, Achat) --> E{Qualification BANT/Critères Métier}
    E -- Qualification < Seuil --> F[Suivi Simple / Alerte Info]
    E -- Qualification >= Seuil --> G[Opportunité Détectée]
    G --> H{Vérification Doublon CRM}
    H -- Doublon Existant --> I[Mise à Jour Opportunité CRM]
    H -- Nouveau --> J[Création Nouvelle Opportunité CRM]
    J --> K{Génération Recommandations / Brouillons}
    K --> L[Notification Commercial & Interface SalesCoach]
    L --> M{Interaction Commercial (Validation/Modification)}
    M -- Validation --> N[Action Exécutée (Ex: Envoi Email, Planification)]
    M -- Modification/Rejet --> O[Feedback pour Amélioration IA]
    N --> P[Suivi Continu de l'Opportunité]
    O --> C
    P --> Q[Fin du Cycle de Vie de l'Opportunité]


### 7.2. Scénarios Clés et Arbre Décisionnel

#### 7.2.1. Scénario : Nouvelle Conversation / Détection de Problème
*   **Déclencheur :** Réception d'un email d'un prospect mentionnant "Nous rencontrons des difficultés avec notre solution actuelle..."
*   **Flux :** A -> B -> C (Intention : Problème) -> E (Qualification forte) -> G -> H (Nouveau) -> J (Création Opp. CRM) -> K (Recommandation : "Proposer une démo solution") -> L -> M (Commercial valide) -> N (Envoi invitation démo) -> P.

#### 7.2.2. Scénario : Identification d'un Besoin / Proposition de Solution
*   **Déclencheur :** Un commercial discute avec un lead sur un chat et le lead dit "Nous avons besoin d'une solution pour automatiser X..."
*   **Flux :** A -> B -> C (Intention : Besoin) -> E (Qualification moyenne) -> G -> H (Existant) -> I (Mise à jour Opp. CRM) -> K (Recommandation : "Envoyer une fiche produit pertinente") -> L -> M (Commercial modifie le brouillon) -> N (Envoi fiche produit) -> P.

#### 7.2.3. Scénario : Tentative de Clôture / Suivi Post-Conversation
*   **Déclencheur :** Le commercial envoie une proposition de prix, et le prospect répond "C'est intéressant, mais le budget est un peu tendu."
*   **Flux :** A -> B -> C (Intention : Négociation/Objection) -> E (Qualification forte) -> G -> I (Mise à jour Opp. CRM avec objection "Budget") -> K (Recommandation : "Proposer une option allégée" ou "Mettre en avant le ROI") -> L -> M (Commercial accepte) -> N (Envoi nouvelle proposition) -> P.

---

## 8. Documentation d'Intégration Salesforce/CRM

Cette section détaille les spécifications techniques pour l'intégration de SalesCoach avec Salesforce, servant de modèle pour d'autres intégrations CRM.

### 8.1. Prérequis et Configuration Initiale

*   **Compte Salesforce :** Un compte Salesforce (Enterprise ou Unlimited de préférence) avec les permissions API activées.
*   **Utilisateur d'Intégration :** Création d'un utilisateur Salesforce dédié à l'intégration SalesCoach avec un profil et un jeu de permissions spécifiques (accès en lecture/écriture aux objets Lead, Contact, Account, Opportunity).
*   **Application Connectée Salesforce :** Création d'une "Connected App" dans Salesforce pour l'authentification OAuth 2.0.
    *   **Callback URL :** `[URL_VOTRE_APPLICATION_SALESCOACH]/api/v1/crm-sync/salesforce/callback`
    *   **OAuth Scopes :** `api`, `refresh_token`, `full`, `web` (ou plus restrictif selon les besoins exacts).

### 8.2. Mapping des Champs Salesforce

SalesCoach synchronise les données clés avec Salesforce pour assurer la cohérence et l'enrichissement des fiches.

#### 8.2.1. Objets Salesforce (Lead, Contact, Opportunity, Account)
*   **Lead :** Objet principal pour les nouveaux prospects non encore qualifiés.
*   **Contact :** Personne associée à un compte existant.
*   **Account :** Entreprise associée à un contact ou une opportunité.
*   **Opportunity :** Représente une vente potentielle.

#### 8.2.2. Champs SalesCoach vers Champs Salesforce

| Champ SalesCoach       | Objet Salesforce | Champ Salesforce (API Name) | Type de Donnée | Description                                         |
| :--------------------- | :--------------- | :-------------------------- | :------------- | :-------------------------------------------------- |
| `prospect_name`        | Lead / Contact   | `FirstName`, `LastName`     | Texte          | Nom et prénom du prospect                           |
| `prospect_email`       | Lead / Contact   | `Email`                     | Email          | Adresse email du prospect                           |
| `company_name`         | Lead / Account   | `Company`, `Name`           | Texte          | Nom de l'entreprise du prospect                     |
| `opportunity_name`     | Opportunity      | `Name`                      | Texte          | Nom généré de l'opportunité (ex: "Besoin XYZ - ABC") |
| `opportunity_stage`    | Opportunity      | `StageName`                 | Picklist       | Étape du processus de vente (ex: "Qualification")   |
| `opportunity_amount`   | Opportunity      | `Amount`                    | Devise         | Montant estimé de l'opportunité                     |
| `opportunity_close_date` | Opportunity      | `CloseDate`                 | Date           | Date de clôture estimée                             |
| `opportunity_description` | Opportunity      | `Description`               | Texte          | Résumé des besoins/problèmes détectés               |
| `salescoach_score`     | Lead / Opportunity | `SalesCoach_Score__c`       | Nombre         | Score de qualification de SalesCoach (champ perso)  |
| `source_conversation_url` | Lead / Opportunity | `SalesCoach_Source__c`      | URL            | Lien vers la conversation source dans SalesCoach    |

#### 8.2.3. Personnalisation du Mapping
*   Les administrateurs peuvent configurer des mappings de champs personnalisés via l'interface de SalesCoach pour s'adapter à des instances Salesforce spécifiques.

### 8.3. Endpoints API et Authentification

*   **Authentification :** Utilisation d'OAuth 2.0 Web Server Flow ou JWT Bearer Flow pour l'authentification et l'autorisation.
    *   **Endpoint d'Autorisation :** `https://login.salesforce.com/services/oauth2/authorize`
    *   **Endpoint de Token :** `https://login.salesforce.com/services/oauth2/token`
    *   Le jeton d'accès (Access Token) est stocké de manière sécurisée et utilisé pour toutes les requêtes API suivantes.

#### 8.3.1. Endpoints pour la Création/Mise à Jour (Leads, Contacts, Opportunités)
*   **Création de Lead :** `POST /services/data/vXX.0/sobjects/Lead/`
*   **Mise à jour de Lead :** `PATCH /services/data/vXX.0/sobjects/Lead/{id}`
*   **Création d'Opportunité :** `POST /services/data/vXX.0/sobjects/Opportunity/`
*   **Mise à jour d'Opportunité :** `PATCH /services/data/vXX.0/sobjects/Opportunity/{id}`
*   **Recherche (SOQL) :** `GET /services/data/vXX.0/query?q={SOQL_QUERY}` (utilisé pour vérifier l'existence de Leads/Contacts/Accounts avant création).

#### 8.3.2. Gestion des Erreurs et Logs
*   **Codes d'erreur HTTP :** Gérer les codes 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 500 (Internal Server Error).
*   **Logging :** Toutes les interactions API avec Salesforce sont loggées pour le dépannage et l'audit.

### 8.4. Synchronisation des Données

#### 8.4.1. Fréquence et Types de Synchronisation
*   **Temps réel :** Création et mise à jour des Leads/Opportunités suite à une détection par SalesCoach.
*   **Batch (Optionnel) :** Synchronisation périodique (ex: toutes les nuits) des données SalesCoach vers Salesforce pour les métriques agrégées.
*   **Bidirectionnelle :** SalesCoach peut aussi récupérer des informations de Salesforce (ex: statut d'une opportunité, informations contact) pour enrichir son contexte.

#### 8.4.2. Gestion des Conflits
*   **Stratégie "Last-Write Wins" :** En cas de modifications simultanées sur le même champ, la dernière écriture prévaut.
*   **Priorité SalesCoach :** Pour les champs générés par l'IA (ex: `SalesCoach_Score__c`), SalesCoach a la priorité.

---

## 9. Présentation aux Parties Prenantes

### 9.1. Synthèse Exécutive (Executive Summary)

L'agent SalesCoach est une solution d'IA innovante conçue pour révolutionner la productivité des équipes commerciales. En analysant intelligemment les conversations textuelles, SalesCoach détecte, qualifie et aide à gérer les opportunités de vente, permettant aux commerciaux de se concentrer sur la vente plutôt que sur l'administration. Ce document a détaillé son architecture robuste, ses fonctionnalités clés, son guide utilisateur et ses capacités d'intégration CRM.

### 9.2. Démonstration de SalesCoach (Aperçu)

*   **Scénario 1 : Détection et Création d'Opportunité**
    *   Présentation d'un email/chat entrant.
    *   SalesCoach détecte une opportunité, crée une fiche dans l'interface.
    *   Affichage de la création automatique dans Salesforce.
*   **Scénario 2 : Aide à la Rédaction**
    *   Le commercial clique sur une opportunité.
    *   SalesCoach propose un brouillon d'email de relance ou de proposition.
    *   Le commercial valide et envoie.
*   **[Préparer des captures d'écran ou une vidéo courte pour la démo]**

### 9.3. Métriques Attendues et ROI Potentiel

#### 9.3.1. Augmentation du Taux de Conversion
*   **Cible :** +15-20% du taux de conversion des leads en opportunités qualifiées.
*   **Justification :** Identification plus rapide et plus précise des opportunités, meilleure priorisation.

#### 9.3.2. Réduction du Cycle de Vente
*   **Cible :** -10% du temps moyen pour clôturer une vente.
*   **Justification :** Suivi proactif, aide à la rédaction rapide, moins de tâches administratives.

#### 9.3.3. Amélioration de la Productivité Commerciale
*   **Cible :** Gain de 2-3 heures par commercial et par semaine sur les tâches de qualification et de rédaction.
*   **Justification :** Automatisation des tâches répétitives, suggestions intelligentes.

### 9.4. Feuille de Route et Prochaines Étapes

#### 9.4.1. Phases de Déploiement
*   **Phase 1 (Pilote) :** Déploiement auprès d'une équipe commerciale restreinte (Q3 2024).
*   **Phase 2 (Généralisation) :** Déploiement à l'ensemble des équipes (Q4 2024).
*   **Phase 3 (Optimisation) :** Collecte de feedback, ajustements des modèles IA et des règles (Q1 2025).

#### 9.4.2. Évolutions Futures
*   Intégration avec des plateformes de communication vocale (transcription et analyse).
*   Fonctionnalités de coaching proactif (alertes sur le ton de la conversation, conseils sur la négociation).
*   Tableaux de bord d'analyse avancés pour les managers.

---

## 10. Conclusion

SalesCoach représente une avancée significative pour les équipes commerciales. En fournissant une assistance intelligente et automatisée, il permet non seulement d'optimiser l'identification et la gestion des opportunités, mais aussi de libérer un temps précieux pour les commerciaux, leur permettant de se concentrer sur l'établissement de relations et la conclusion de ventes. Ce document, en consolidant tous les aspects du projet, servira de base solide pour le déploiement et l'évolution future de cet agent IA.

---

## 11. Annexes

### 11.1. Glossaire

*   **Agent IA :** Programme informatique capable d'interagir avec son environnement, d'apprendre et de prendre des décisions.
*   **BANT :** Acronyme pour Budget, Authority, Need, Timeline – un cadre de qualification de leads.
*   **CRM :** Customer Relationship Management – logiciel de gestion de la relation client.
*   **LLM :** Large Language Model – modèle de langage étendu utilisé pour des tâches de traitement du langage naturel.
*   **NLP :** Natural Language Processing – traitement automatique du langage naturel.
*   **Prompt :** Instruction donnée à un modèle d'IA générative pour obtenir une réponse spécifique.
*   **ROI :** Return On Investment – retour sur investissement.
*   **Salesforce :** Plateforme CRM leader du marché.

### 11.2. Références

*   [Lien vers les spécifications API Salesforce]
*   [Lien vers la documentation des LLM utilisés]
*   [Autres références pertinentes]

### 11.3. Historique des Révisions

| Version | Date           | Auteur                | Description de la Révision                                 |
| :------ | :------------- | :-------------------- | :--------------------------------------------------------- |
| 0.1     | 10 juillet 2024 | [Auteur]              | Première ébauche de la structure                            |
| 0.5     | 18 juillet 2024 | [Auteur]              | Ajout des contenus détaillés pour chaque section           |
| 0.9     | 22 juillet 2024 | [Auteur]              | Révision générale, ajout des exemples et captures d'écran (placeholders) |
| 1.0     | 25 juillet 2024 | [Auteur]              | Finalisation et consolidation du document                   |

