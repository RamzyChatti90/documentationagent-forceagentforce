# Spécifications des Prompts IA et Règles de Détection d'Opportunité

## Table des Matières

1.  [Introduction](#1-introduction)
2.  [Spécifications des Prompts IA](#2-spécifications-des-prompts-ia)
    *   [2.1 Principes Généraux](#21-principes-généraux)
    *   [2.2 Structure Générique des Prompts](#22-structure-générique-des-prompts)
    *   [2.3 Types de Prompts et Exemples](#23-types-de-prompts-et-exemples)
        *   [2.3.1 Prompt Système Global (Persona)](#231-prompt-système-global-persona)
        *   [2.3.2 Prompt de Détection d'Opportunité](#232-prompt-de-détection-dopportunité)
        *   [2.3.3 Prompt de Qualification de Lead](#233-prompt-de-qualification-de-lead)
        *   [2.3.4 Prompt de Suggestion d'Action Commerciale](#234-prompt-de-suggestion-daction-commerciale)
        *   [2.3.5 Prompt de Résumé/Extraction d'Informations](#235-prompt-de-résuméextraction-dinformations)
3.  [Règles de Détection d'Opportunité](#3-règles-de-détection-dopportunité)
    *   [3.1 Cadre de Référence](#31-cadre-de-référence)
    *   [3.2 Critères de Détection d'Opportunité](#32-critères-de-détection-dopportunité)
        *   [3.2.1 Besoin (Need)](#321-besoin-need)
        *   [3.2.2 Budget](#322-budget)
        *   [3.2.3 Autorité (Authority)](#323-autorité-authority)
        *   [3.2.4 Calendrier (Timeline)](#324-calendrier-timeline)
        *   [3.2.5 Intérêt Explicite](#325-intérêt-explicite)
        *   [3.2.6 Sentiment et Urgence](#326-sentiment-et-urgence)
    *   [3.3 Logique de Détection et Ponderation](#33-logique-de-détection-et-ponderation)
    *   [3.4 Exemples de Déclencheurs et Score](#34-exemples-de-déclencheurs-et-score)
4.  [Conclusion](#4-conclusion)

---

## 1. Introduction

Ce document détaille les spécifications techniques des prompts utilisés par l'agent IA SalesCoach et les règles sous-jacentes à la détection d'opportunités commerciales à partir de conversations textuelles. L'objectif est d'assurer une compréhension claire du fonctionnement interne de l'agent, garantissant ainsi précision, pertinence et cohérence dans son assistance aux développeurs commerciaux.

SalesCoach vise à transformer des échanges textuels bruts en informations actionnables, permettant aux commerciaux de se concentrer sur les relations et la conclusion de ventes. La qualité des prompts et la robustesse des règles de détection sont cruciales pour atteindre cet objectif.

## 2. Spécifications des Prompts IA

### 2.1 Principes Généraux

Les prompts de SalesCoach sont conçus pour :
*   **Précision :** Extraire des informations spécifiques et pertinentes.
*   **Pertinence :** Aligner les analyses et suggestions avec le contexte commercial.
*   **Cohérence :** Maintenir une persona d'expert commercial et des réponses uniformes.
*   **Robustesse :** Gérer diverses structures de conversation et niveaux de détail.
*   **Sécurité :** Éviter les biais et les informations non éthiques.

### 2.2 Structure Générique des Prompts

Chaque prompt suit une structure logique pour guider le Modèle de Langage (LLM) :


[Prompt Système Global/Persona]

[Contexte Spécifique à la Tâche]
  - Informations sur le client (si disponibles)
  - Historique de conversation pertinent

[Instruction Principale de la Tâche]
  - Qu'attend-on de l'IA ? (Ex: "Détecte les opportunités", "Qualifie le lead")

[Contraintes/Format de la Réponse]
  - Format attendu (JSON, liste, résumé)
  - Critères de succès


### 2.3 Types de Prompts et Exemples

#### 2.3.1 Prompt Système Global (Persona)

Ce prompt définit le rôle et le comportement général de SalesCoach. Il est pré-apposé à chaque interaction.

**Objectif :** Établir le rôle d'expert en développement commercial, analytique et orienté action.

**Exemple :**

Tu es "SalesCoach", un assistant IA expert en développement commercial. Ton rôle est d'analyser des conversations textuelles entre un commercial et un prospect/client pour identifier des opportunités, qualifier des leads, et suggérer des actions concrètes au commercial. Tu es précis, factuel, et orienté vers l'augmentation des ventes. Ne génère jamais de contenu non sollicité. Réponds toujours en français.


#### 2.3.2 Prompt de Détection d'Opportunité

**Objectif :** Identifier si une opportunité commerciale est présente dans la conversation actuelle, basée sur des signaux clés.

**Exemple :**

Contexte de la conversation : [HISTORIQUE_CONVERSATION_PRÉCÉDENT]
Nouvelle partie de la conversation : [DERNIER_ÉCHANGE_DE_CONVERSATION]

Instruction : Analyse cette conversation et détermine si elle contient des signaux clairs de détection d'opportunité commerciale.
Une opportunité est détectée si au moins deux des critères suivants (BANT : Besoin, Budget, Autorité, Calendrier) sont fortement suggérés, ou si un besoin explicite et un intérêt marqué sont présents.
Si une opportunité est détectée, fournis :
1.  Un score de confiance (0-100%).
2.  Une brève justification des signaux identifiés.
3.  Les critères BANT potentiellement remplis.
4.  Une proposition de titre pour l'opportunité.
Si aucune opportunité n'est détectée, indique "Aucune opportunité détectée".

Format de réponse attendu (JSON) :
{
  "opportunite_detectee": true/false,
  "score_confiance": [0-100],
  "justification": "...",
  "critères_bant_identifiés": ["Besoin", "Budget", "Autorité", "Calendrier"],
  "titre_opportunite": "..."
}


#### 2.3.3 Prompt de Qualification de Lead

**Objectif :** Extraire les informations clés d'un lead pour le qualifier selon des critères prédéfinis.

**Exemple :**

Contexte de la conversation : [CONVERSATION_COMPLÈTE]

Instruction : Qualifie le lead basé sur les informations disponibles dans la conversation. Extrais les éléments suivants :
- Nom de l'entreprise
- Nom et fonction du contact
- Problématique principale (besoin identifié)
- Budget potentiel (si mentionné)
- Calendrier de décision (si mentionné)
- Autorité de décision du contact (décideur, influenceur, utilisateur)
- Prochaines étapes suggérées par le prospect (ex: démo, documentation)

Format de réponse attendu (JSON) :
{
  "entreprise": "...",
  "contact": { "nom": "...", "fonction": "..." },
  "probleme_principal": "...",
  "budget_potentiel": "...",
  "calendrier_decision": "...",
  "autorite_decision": "...",
  "prochaines_etapes_prospect": "..."
}


#### 2.3.4 Prompt de Suggestion d'Action Commerciale

**Objectif :** Proposer la meilleure action à entreprendre par le commercial après un échange.

**Exemple :**

Contexte de la conversation : [CONVERSATION_COMPLÈTE]
Opportunité détectée (si applicable) : [DÉTAILS_OPPORTUNITÉ_PRÉCÉDENTE]
Informations sur le lead : [DÉTAILS_LEAD_PRÉCÉDENT]

Instruction : En tant que SalesCoach, suggère la meilleure prochaine action commerciale pour le commercial, en justifiant ton choix.
Les actions possibles incluent : "Envoyer documentation", "Planifier démo", "Proposer appel découverte", "Relancer (préciser sujet)", "Qualifier davantage", "Fermer le lead".

Format de réponse attendu (JSON) :
{
  "action_suggeree": "...",
  "justification": "...",
  "element_cle_suggere": "..." (ex: type de documentation, point à aborder lors de la démo)
}


#### 2.3.5 Prompt de Résumé/Extraction d'Informations

**Objectif :** Synthétiser des parties de conversation ou extraire des points spécifiques.

**Exemple :**

Contexte de la conversation : [PARTIE_DE_CONVERSATION_À_RÉSUMER]

Instruction : Résume les points clés de cette partie de conversation en 3 phrases maximum. Concentre-toi sur les informations pertinentes pour le suivi commercial.

Format de réponse attendu (texte libre, résumé concis)


## 3. Règles de Détection d'Opportunité

Les règles de détection d'opportunité sont le cœur de la valeur de SalesCoach. Elles définissent les critères et la logique permettant à l'IA d'identifier quand une conversation textuelle présente un potentiel commercial significatif.

### 3.1 Cadre de Référence

La détection des opportunités s'appuie principalement sur une adaptation du cadre **BANT (Budget, Authority, Need, Timeline)**, enrichie par l'analyse de l'intérêt explicite et du sentiment.

### 3.2 Critères de Détection d'Opportunité

Chaque critère est évalué par l'IA en analysant le contenu de la conversation.

#### 3.2.1 Besoin (Need) - Poids : Élevé

*   **Description :** Le prospect exprime clairement une problématique, un défi, un point de douleur, ou un objectif qui pourrait être résolu par la solution proposée.
*   **Déclencheurs (exemples) :**
    *   "Nous avons du mal à gérer X."
    *   "Il nous faudrait une solution pour améliorer Y."
    *   "Notre processus actuel est inefficace à cause de Z."
    *   "Nous cherchons à atteindre l'objectif A, mais nous manquons de B."
    *   "Existe-t-il un moyen de... ?"
*   **Indicateurs :** Verbes d'action (améliorer, résoudre, optimiser), mots-clés liés aux défis (problème, difficulté, manque, inefficacité), expressions de désir (nous voulons, il nous faut).

#### 3.2.2 Budget - Poids : Moyen

*   **Description :** Le prospect mentionne des considérations financières, des allocations budgétaires, ou des questions de coût.
*   **Déclencheurs (exemples) :**
    *   "Quel est l'ordre de grandeur des coûts ?"
    *   "Avons-nous un budget pour ce type de projet ?"
    *   "C'est un investissement que nous envisageons."
    *   "Le prix est un facteur clé pour nous."
*   **Indicateurs :** Mots-clés (budget, coût, prix, investissement, financement), questions sur la tarification, discussions sur le ROI.

#### 3.2.3 Autorité (Authority) - Poids : Moyen

*   **Description :** Le prospect indique son rôle dans le processus de décision ou mentionne d'autres parties prenantes impliquées.
*   **Déclencheurs (exemples) :**
    *   "Je suis le responsable des achats pour ce projet."
    *   "Je dois en discuter avec mon directeur."
    *   "Nous avons un comité de décision."
    *   "Je suis en charge de la sélection des fournisseurs."
*   **Indicateurs :** Titres professionnels (directeur, responsable), mentions de "mon équipe", "les décideurs", "le comité", expressions de pouvoir décisionnel.

#### 3.2.4 Calendrier (Timeline) - Poids : Moyen

*   **Description :** Le prospect exprime une échéance, une urgence ou un délai pour la mise en œuvre d'une solution.
*   **Déclencheurs (exemples) :**
    *   "Nous aimerions mettre cela en place avant la fin du trimestre."
    *   "Notre projet doit démarrer dans les 3 mois."
    *   "C'est assez urgent pour nous."
    *   "Quand pourriez-vous livrer une telle solution ?"
*   **Indicateurs :** Expressions temporelles (avant X, dans Y mois, rapidement, urgent, échéance), questions sur les délais de livraison ou de mise en œuvre.

#### 3.2.5 Intérêt Explicite - Poids : Élevé

*   **Description :** Le prospect manifeste un intérêt direct et proactif pour la solution.
*   **Déclencheurs (exemples) :**
    *   "Pourriez-vous m'envoyer une démo ?"
    *   "J'aimerais en savoir plus sur votre produit."
    *   "Votre solution semble correspondre à nos besoins."
    *   "Quand pouvons-nous discuter plus en détail ?"
*   **Indicateurs :** Demandes d'information (démo, documentation, appel), expressions positives directes sur la solution.

#### 3.2.6 Sentiment et Urgence - Poids : Faible à Moyen

*   **Description :** Analyse du ton général de la conversation et de l'urgence perçue.
*   **Déclencheurs (exemples) :**
    *   Un ton enthousiaste, des exclamations positives.
    *   Des mots-clés exprimant l'urgence (immédiatement, rapidement, critique).
*   **Indicateurs :** Analyse de sentiment (positif, neutre, négatif), détection de mots-clés d'urgence.

### 3.3 Logique de Détection et Ponderation

SalesCoach utilise une approche pondérée pour la détection d'opportunité :

1.  **Évaluation Individuelle :** Chaque critère est évalué indépendamment et reçoit un score de présence (ex: 0 = absent, 1 = faible, 2 = modéré, 3 = fort).
2.  **Pondération des Critères :** Les scores sont multipliés par un coefficient de pondération (Besoin x3, Intérêt Explicite x3, Budget x2, Autorité x2, Calendrier x2, Sentiment/Urgence x1).
3.  **Score Global d'Opportunité :** La somme des scores pondérés génère un score global.
4.  **Seuil de Détection :** Un seuil prédéfini est appliqué pour déclarer une opportunité.
    *   **Faible :** Score > X (suggère une opportunité potentielle, à qualifier davantage).
    *   **Modérée :** Score > Y (opportunité prometteuse, nécessite un suivi).
    *   **Forte :** Score > Z (opportunité claire, action immédiate recommandée).

    *Exemple de seuils (à ajuster) :*
    *   Score total > 10 : Opportunité Faible
    *   Score total > 18 : Opportunité Modérée
    *   Score total > 25 : Opportunité Forte

5.  **Détection Incrémentale :** L'IA prend en compte l'historique de conversation. Une opportunité peut se construire sur plusieurs échanges, accumulant des scores au fil du temps.

### 3.4 Exemples de Déclencheurs et Score

| Critère                | Déclencheur Explicite                               | Score de Présence | Pondération | Score Pondéré |
| :--------------------- | :-------------------------------------------------- | :---------------- | :---------- | :------------ |
| **Besoin**             | "Notre solution actuelle est obsolète et inefficace." | 3 (Fort)          | x3          | 9             |
| **Budget**             | "Quel est le coût d'une telle implémentation ?"     | 2 (Modéré)        | x2          | 4             |
| **Autorité**           | "Je suis le chef de projet et le décideur final."   | 3 (Fort)          | x2          | 6             |
| **Calendrier**         | "Nous devons trouver une solution d'ici Q3."       | 2 (Modéré)        | x2          | 4             |
| **Intérêt Explicite**  | "J'aimerais une démo de votre produit."            | 3 (Fort)          | x3          | 9             |
| **Sentiment/Urgence**  | "C'est une priorité absolue pour nous !"           | 2 (Modéré)        | x1          | 2             |
| **TOTAL**              |                                                     |                   |             | **34**        |

*Interprétation :* Un score de 34 indique une **Opportunité Forte** (selon l'exemple de seuils ci-dessus), justifiant une action commerciale immédiate.

## 4. Conclusion

La précision des prompts IA et la pertinence des règles de détection d'opportunité sont les piliers de la performance de SalesCoach. Ce document fournit une base solide pour le développement et l'optimisation continue de l'agent. Une attention particulière sera portée à l'affinement de ces spécifications via des boucles de feedback et l'analyse de données réelles pour maximiser la valeur apportée aux développeurs commerciaux.