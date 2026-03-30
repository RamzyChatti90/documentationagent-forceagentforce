# Diagramme de Flux Conversationnel de l'Agent SalesCoach

## 1. Introduction

Ce document présente le diagramme de flux conversationnel de l'agent IA SalesCoach. Il décrit de manière structurée les étapes successives par lesquelles l'agent analyse une conversation textuelle, identifie les opportunités commerciales, interagit avec le développeur commercial et s'intègre aux systèmes CRM. L'objectif est de fournir une compréhension claire et détaillée du processus décisionnel et des interactions de l'agent.

## 2. Acteurs Principaux

*   **Développeur Commercial (Utilisateur)** : L'acteur humain qui fournit les conversations, interagit avec les propositions de l'agent, et valide ou modifie les actions suggérées.
*   **Agent SalesCoach** : L'entité IA qui effectue l'analyse des conversations, la détection d'opportunités, la qualification et la génération de propositions.
*   **Système CRM (ex: Salesforce)** : Le système d'enregistrement des opportunités, leads, contacts et activités commerciales, avec lequel l'agent s'intègre.

## 3. Déclencheur du Flux

Le processus de l'agent SalesCoach est initié par les événements suivants :

*   **Ingestion Automatique** : Une nouvelle conversation textuelle (e-mail, message de chat, transcription d'appel, etc.) est automatiquement détectée et soumise à l'agent pour analyse.
*   **Soumission Manuelle** : Le développeur commercial soumet explicitement une conversation ou un extrait de texte à l'agent via l'interface utilisateur.

## 4. Diagramme de Flux Conversationnel Détaillé

Le flux conversationnel de l'agent SalesCoach se déroule en plusieurs phases logiques :

### Phase 1: Ingestion et Analyse Préliminaire de la Conversation

1.  **Réception de la Conversation**
    *   L'agent SalesCoach reçoit une nouvelle conversation textuelle (déclencheur).
2.  **Analyse du Contenu (NLP/NLU)**
    *   L'agent applique des techniques de Traitement du Langage Naturel (TLN) et de Compréhension du Langage Naturel (CLN) pour analyser le texte.
    *   **Extraction des intentions**: Détection d'expressions d'intérêt, de questions, de besoins, de problèmes, etc.
    *   **Détection de mots-clés**: Identification de termes pertinents pour le domaine commercial (budget, délai, solution, concurrent, etc.).
3.  **Décision : Détection d'Intérêt Commercial ou d'Opportunité Potentielle ?**
    *   L'agent évalue si la conversation contient des signaux forts ou faibles d'un intérêt commercial ou d'une opportunité.
    *   **Si OUI (Intérêt commercial détecté)** : Passe à la Phase 2.
    *   **Si NON (Pas d'intérêt commercial direct)** : Passe à la Phase 3.

### Phase 2: Détection et Qualification d'Opportunité (si intérêt détecté)

1.  **Extraction des Entités Clés**
    *   L'agent identifie et extrait les informations structurées de la conversation :
        *   Nom du contact
        *   Nom de l'entreprise
        *   Besoins exprimés
        *   Budget potentiel (s'il est mentionné)
        *   Échéance envisagée
        *   Produits/services d'intérêt
        *   Concurrents mentionnés
2.  **Application des Règles de Qualification**
    *   L'agent applique les règles métier préconfigurées (basées sur des frameworks comme BANT, MEDDIC, ou des critères spécifiques à l'entreprise) pour évaluer la qualité et la maturité de l'opportunité.
    *   **Décision : Opportunité qualifiée selon les critères ?**
        *   **Si OUI (Opportunité qualifiée)** : Passe à la Phase 4.
        *   **Si NON (Opportunité non qualifiée ou informations insuffisantes)** : Passe à l'étape suivante (Suggestion de questions complémentaires).
3.  **Suggestion de Questions Complémentaires (si non qualifiée)**
    *   L'agent identifie les lacunes dans les informations nécessaires à la qualification.
    *   Il génère des suggestions de questions que le développeur commercial pourrait poser pour obtenir les données manquantes.
    *   Passe à la Phase 4 (Présentation au développeur commercial avec les données existantes et les suggestions).

### Phase 3: Identification d'Actions de Suivi/Relance (si pas d'intérêt direct)

1.  **Analyse pour Suivi/Relance**
    *   L'agent recherche des éléments indiquant un besoin de suivi ultérieur, même sans opportunité immédiate (ex: demande de documentation, contact futur).
2.  **Proposition d'Action de Suivi**
    *   L'agent suggère une action au développeur commercial (ex: "Programmer un rappel dans 1 semaine", "Envoyer un e-mail de remerciement", "Ajouter une tâche de suivi au contact").
3.  **Décision du Développeur Commercial**
    *   **Si VALIDE** : L'action est enregistrée dans le CRM (tâche, rappel). **Fin du flux pour cette branche.**
    *   **Si REJETTE** : L'action est ignorée. **Fin du flux pour cette branche.**

### Phase 4: Présentation et Interaction avec le Développeur Commercial

1.  **Génération du Résumé de l'Opportunité**
    *   L'agent compile un résumé clair et concis de l'opportunité ou de l'intérêt détecté, incluant toutes les informations extraites et le niveau de qualification.
2.  **Proposition d'Actions au Développeur Commercial**
    *   L'agent propose des actions concrètes basées sur son analyse :
        *   "Créer une nouvelle opportunité dans le CRM"
        *   "Mettre à jour un lead ou un contact existant"
        *   "Ajouter une tâche de suivi spécifique"
        *   "Envoyer un modèle d'e-mail pré-rédigé"
3.  **Présentation à l'Interface Utilisateur**
    *   Le résumé de l'opportunité, les informations extraites et les actions proposées sont affichés au développeur commercial via l'interface de SalesCoach.
4.  **Décision du Développeur Commercial**
    *   **Si VALIDE** : Le développeur commercial accepte les propositions de l'agent telles quelles. Passe à la Phase 5.
    *   **Si MODIFIE** : Le développeur commercial ajuste les informations extraites (ex: corrige un budget, affine une description) ou les actions proposées. Les données sont mises à jour dans l'agent. Passe à la Phase 5 avec les données modifiées.
    *   **Si REJETTE** : Le développeur commercial rejette l'opportunité ou les propositions. L'opportunité peut être archivée ou ignorée. **Fin du flux pour cette branche.**

### Phase 5: Intégration CRM et Confirmation

1.  **Interaction avec le Système CRM**
    *   L'agent SalesCoach se connecte au système CRM (ex: Salesforce) via ses API.
    *   Il exécute les actions validées par le développeur commercial :
        *   Création ou mise à jour d'un lead, contact ou compte.
        *   Création ou mise à jour d'une opportunité avec les champs mappés.
        *   Ajout de tâches, événements ou activités liées à l'opportunité.
2.  **Confirmation à l'Utilisateur**
    *   L'agent confirme au développeur commercial que les actions ont été exécutées avec succès dans le CRM.

### Phase 6: Boucle de Feedback et Amélioration Continue

1.  **Enregistrement de l'Historique**
    *   Toutes les interactions, les analyses de l'agent et les décisions du développeur commercial sont enregistrées.
2.  **Collecte de Feedback**
    *   Les validations, modifications et rejets de l'utilisateur servent de données d'apprentissage pour l'agent.
3.  **Ajustement des Modèles et Règles**
    *   Ces retours sont utilisés pour affiner les prompts IA, les règles de détection d'opportunité, les modèles de qualification et les suggestions d'actions, améliorant ainsi la précision de l'agent au fil du temps.
4.  **Fin du Flux**
    *   Le processus est terminé pour cette conversation.

## 5. Caractéristiques Clés du Flux

*   **Adaptabilité** : Le flux est conçu pour s'adapter à divers types de conversations et de niveaux de détail.
*   **Transparence** : Chaque étape est conçue pour être claire, et les propositions de l'agent sont toujours soumises à la validation humaine.
*   **Intégration** : L'intégration au CRM est une étape finale cruciale pour assurer que les informations sont correctement enregistrées dans les systèmes métier.
*   **Apprentissage** : Le flux intègre une boucle de feedback essentielle pour l'amélioration continue de la performance de l'agent.
*   **Gestion des Ambigüités** : En cas de doute ou d'informations insuffisantes, l'agent privilégie la demande de clarification à l'utilisateur plutôt que de prendre une décision potentiellement erronée.