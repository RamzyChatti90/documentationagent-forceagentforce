# Documentation Agent Force Commercial - Diagramme de Flux Conversationnel

## Ticket: YC-DOCUME-S1-006 — Créer le diagramme de flux conversationnel (arbre décisionnel de l'agent)

### 1. Introduction

Ce document présente le diagramme de flux conversationnel de l'agent SalesCoach. Il décrit l'arbre décisionnel et les interactions clés de l'agent IA, depuis la détection d'un signal d'opportunité jusqu'à l'intégration avec le CRM. L'objectif est de visualiser la logique sous-jacente qui permet à SalesCoach d'assister les développeurs commerciaux dans la création et la qualification d'opportunités.

### 2. Légende des Éléments

*   **[DÉBUT]** : Point de départ du processus.
*   **[ÉTAPE]** : Action ou processus réalisé par l'agent ou le système.
*   **[DÉCISION]** : Point où une condition est évaluée, menant à différentes branches.
*   **[INPUT UTILISATEUR]** : Attente d'une action ou d'une réponse de l'utilisateur (développeur commercial).
*   **[OUTPUT AGENT]** : Message ou suggestion de l'agent à l'utilisateur.
*   **[INTÉGRATION CRM]** : Interaction directe avec le système CRM (ex: Salesforce).
*   **[FIN]** : Point de terminaison d'une branche ou du processus.

### 3. Diagramme de Flux Conversationnel de l'Agent SalesCoach


[DÉBUT] Surveillance Continue des Conversations Textuelles
  |
  V
[ÉTAPE] Analyse de la Conversation par l'IA (NLP, Détection d'Intention, Mots-clés)
  |
  V
[DÉCISION] Signal d'Opportunité Potentielle Détecté ?
  ├───OUI───────────────────────────────────────────────────────────┐
  │                                                                │
  V                                                                V
[ÉTAPE] Qualification Préliminaire de l'Opportunité (Extraction Besoins, Budget, Échéance, Interlocuteur)
  |
  V
[OUTPUT AGENT] "Opportunité potentielle détectée ! Que souhaitez-vous faire ?"
    - "Créer une nouvelle opportunité" (Option A)
    - "Qualifier davantage le lead" (Option B)
    - "Assigner une tâche de suivi" (Option C)
    - "Ignorer pour le moment" (Option D)
  |
  V
[INPUT UTILISATEUR] Sélection d'une option

────────────────────────────────────────────────────────────────────────────────────────────────────
Branche A: Créer une nouvelle opportunité
────────────────────────────────────────────────────────────────────────────────────────────────────
  |
  V
[ÉTAPE] Collecte des Données pour la Création d'Opportunité (Nom, Compte, Contact, Montant, Étape, Date de clôture)
  |
  V
[DÉCISION] Toutes les informations nécessaires sont-elles disponibles ?
  ├───OUI───────────────────────────────────────────────────────────┐
  │                                                                │
  V                                                                V
[INTÉGRATION CRM] Appel API Salesforce/CRM pour créer l'Opportunité
  |                                                                [OUTPUT AGENT] "Il me manque des informations (ex: Montant estimé). Pouvez-vous les fournir ?"
  V                                                                  |
[DÉCISION] Création de l'opportunité réussie dans le CRM ?          V
  ├───OUI───────────────────────────────────────────────────────────[INPUT UTILISATEUR] Fourniture des informations manquantes
  │                                                                  |
  V                                                                  V
[OUTPUT AGENT] "Opportunité '[Nom]' créée avec succès dans Salesforce !"
  |                                                                [RETOUR] vers [ÉTAPE] Collecte des Données (pour re-vérification)
  V
[ÉTAPE] Suggestions d'actions complémentaires (ex: Créer une tâche, Envoyer un email)
  |
  V
[FIN] Interaction Opportunité Créée

────────────────────────────────────────────────────────────────────────────────────────────────────
Branche B: Qualifier davantage le lead
────────────────────────────────────────────────────────────────────────────────────────────────────
  |
  V
[OUTPUT AGENT] "Pour mieux qualifier, avez-vous des informations sur [BANT/MEDDIC/SPIN] ?"
  |
  V
[INPUT UTILISATEUR] Fourniture d'informations de qualification
  |
  V
[INTÉGRATION CRM] Mise à jour du Lead/Contact dans Salesforce/CRM avec les nouvelles informations
  |
  V
[OUTPUT AGENT] "Lead mis à jour avec les informations de qualification. Souhaitez-vous maintenant créer une opportunité ou une tâche de suivi ?"
  |
  V
[INPUT UTILISATEUR] Sélection (retour aux options initiales ou fin)
  ├───OUI (Créer opp. / Tâche)───────────────────────────────────────┐
  │                                                                │
  V                                                                V
[RETOUR] vers [OUTPUT AGENT] "Opportunité potentielle détectée..."  [FIN] Interaction Qualification

────────────────────────────────────────────────────────────────────────────────────────────────────
Branche C: Assigner une tâche de suivi
────────────────────────────────────────────────────────────────────────────────────────────────────
  |
  V
[OUTPUT AGENT] "Décrivez la tâche de suivi (ex: 'Appeler le client X le 15/03', 'Envoyer doc produit')."
  |
  V
[INPUT UTILISATEUR] Détails de la tâche (description, date d'échéance, priorité, assigné à)
  |
  V
[INTÉGRATION CRM] Appel API Salesforce/CRM pour créer une Tâche
  |
  V
[OUTPUT AGENT] "Tâche de suivi créée avec succès dans Salesforce !"
  |
  V
[FIN] Interaction Tâche Créée

────────────────────────────────────────────────────────────────────────────────────────────────────
Branche D: Ignorer pour le moment
────────────────────────────────────────────────────────────────────────────────────────────────────
  |
  V
[OUTPUT AGENT] "Opportunité ignorée. Je continuerai de surveiller la conversation."
  |
  V
[FIN] Interaction Ignorée

────────────────────────────────────────────────────────────────────────────────────────────────────
Branche "NON" (Pas de signal d'opportunité détecté)
────────────────────────────────────────────────────────────────────────────────────────────────────
  |
  V
[ÉTAPE] Suivi de Conversation Standard (pas d'action proactive d'opportunité)
  |
  V
[FIN] Aucun signal détecté


### 4. Scénarios Clés Représentés

*   **Détection proactive:** L'agent SalesCoach analyse en permanence les conversations pour identifier les signaux d'opportunité.
*   **Proposition d'actions:** Une fois un signal détecté, l'agent propose un éventail d'actions pertinentes au commercial.
*   **Création d'opportunité guidée:** L'agent assiste le commercial dans la collecte des informations nécessaires et la création de l'opportunité dans le CRM, y compris la gestion des informations manquantes.
*   **Qualification avancée:** L'agent peut guider le commercial à travers des questions de qualification pour enrichir les données du lead/contact.
*   **Automatisation du suivi:** La création de tâches de suivi est facilitée et intégrée directement au CRM.
*   **Gestion des cas d'absence d'opportunité:** L'agent reste en veille sans surcharger le commercial si aucun signal clair n'est détecté.

### 5. Conclusion

Ce diagramme de flux conversationnel met en évidence la logique structurée de l'agent SalesCoach, garantissant une interaction fluide et efficace avec le développeur commercial. Il sert de base pour le développement des prompts IA, les règles de détection et les intégrations CRM, assurant que l'agent maximise sa valeur en guidant les utilisateurs vers la création d'opportunités de manière intuitive.