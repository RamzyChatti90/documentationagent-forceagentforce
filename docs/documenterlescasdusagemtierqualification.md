
⚠ Gemini error: 200 OK from POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent
=======
# Documentation Agent Force Commercial - Cas d'Usage Métier

**Ticket:** YC-DOCUME-S1-018
**Tâche:** Documenter les cas d'usage métier (qualification lead, création opportunité, suivi relance)

---

## 1. Introduction

Ce document détaille les principaux cas d'usage métier de l'agent IA SalesCoach, conçu pour assister les développeurs commerciaux (Sales Development Representatives - SDRs) et les commerciaux dans leurs interactions textuelles avec les prospects. L'objectif est d'optimiser le processus de vente, de la qualification initiale à la création d'opportunités et au suivi, en tirant parti de l'intelligence artificielle pour identifier les signaux clés et automatiser les tâches répétitives.

Les cas d'usage couverts sont :
1.  **Qualification de Lead**
2.  **Création d'Opportunité**
3.  **Suivi et Relance**

---

## 2. Cas d'Usage 1 : Qualification de Lead

### 2.1. Objectif
Évaluer l'intérêt, la pertinence et la maturité d'un lead à partir de ses interactions textuelles (e-mails, chats, messages LinkedIn, etc.) afin de déterminer s'il mérite un engagement commercial plus poussé.

### 2.2. Acteurs
*   **Agent SalesCoach (IA)**
*   **Développeur Commercial / Commercial**

### 2.3. Prérequis
*   Accès à une conversation textuelle entre le commercial et un prospect.
*   Configuration des règles de qualification et des critères de détection (par exemple, modèle BANT - Budget, Authority, Need, Timeline, ou MEDDIC).

### 2.4. Déclencheur
*   Le commercial soumet manuellement une conversation (ou un extrait) à l'agent SalesCoach.
*   L'agent SalesCoach est configuré pour écouter et analyser automatiquement des flux de conversations (par exemple, boîte de réception e-mail, chats CRM) dès leur réception.

### 2.5. Flux d'Exécution

| Étape | Acteur | Description |
| :---- | :----- | :---------- |
| **1.** | Commercial | Le commercial interagit avec un prospect par texte. |
| **2.** | Commercial / Système | Le commercial soumet la conversation à SalesCoach, ou SalesCoach intercepte la conversation. |
| **3.** | SalesCoach | Analyse le contenu de la conversation (mots-clés, questions posées, réponses du prospect, signaux d'achat, objections). |
| **4.** | SalesCoach | Identifie les éléments clés de qualification (besoins exprimés, budget potentiel, autorité de décision, délai envisagé). |
| **5.** | SalesCoach | Évalue le niveau de qualification du lead (par exemple, "chaud", "tiède", "froid") et attribue un score de qualification. |
| **6.** | SalesCoach | Génère un résumé des points clés de la conversation et des raisons de la qualification. |
| **7.** | SalesCoach | Suggère des questions complémentaires à poser ou des informations à rechercher si la qualification est incomplète. |
| **8.** | SalesCoach | Propose les prochaines étapes recommandées (par exemple, "Planifier un appel", "Envoyer une étude de cas", "Qualifier davantage"). |
| **9.** | Commercial | Le commercial consulte l'analyse de SalesCoach et décide des actions à entreprendre. |

### 2.6. Résultat Attendu
*   Un lead évalué avec un score ou un statut de qualification clair.
*   Des insights actionnables sur les besoins et l'intérêt du prospect.
*   Des recommandations pour la prochaine étape de la conversation.

### 2.7. Exemple d'Interaction

**Commercial (copie-colle le message du prospect) :**
"Merci pour votre e-mail. Nous sommes effectivement à la recherche d'une solution pour optimiser la gestion de nos stocks, car notre système actuel est obsolète et nous coûte cher en erreurs. Nous avons un budget d'environ 15 000€ pour un projet de ce type, mais nous ne sommes pas pressés, probablement pour le prochain trimestre."

**SalesCoach (Réponse) :**
"**Analyse de Qualification de Lead :**
*   **Statut :** Tiède (potentiel élevé, mais délai à confirmer)
*   **Besoin (Need) :** Optimisation de la gestion des stocks, remplacement d'un système obsolète, réduction des erreurs.
*   **Budget (Budget) :** Environ 15 000€ (signal positif).
*   **Autorité (Authority) :** Non précisée (à vérifier).
*   **Délai (Timeline) :** Prochain trimestre (à confirmer, mais non urgent).

**Recommandations :**
1.  **Prochaine étape :** Proposer un court appel pour comprendre plus en détail les défis spécifiques liés aux erreurs et à l'obsolescence du système actuel.
2.  **Questions suggérées :**
    *   `Qui sont les parties prenantes principales impliquées dans la décision d'achat d'une nouvelle solution de gestion des stocks ?`
    *   `Quels sont les impacts les plus critiques que votre système actuel a sur vos opérations quotidiennes ?`"

### 2.8. Bénéfices
*   **Gain de temps :** Automatise l'analyse des conversations.
*   **Meilleure priorisation :** Permet aux commerciaux de se concentrer sur les leads les plus prometteurs.
*   **Objectivité :** Réduit les biais humains dans la qualification.
*   **Uniformité :** Assure une qualification cohérente des leads à travers l'équipe.

---

## 3. Cas d'Usage 2 : Création d'Opportunité

### 3.1. Objectif
Convertir un lead qualifié ou une conversation prometteuse en une opportunité formelle au sein du CRM (ex: Salesforce), en pré-remplissant les informations pertinentes pour accélérer le processus de vente.

### 3.2. Acteurs
*   **Agent SalesCoach (IA)**
*   **Développeur Commercial / Commercial**
*   **Système CRM (ex: Salesforce)**

### 3.3. Prérequis
*   Intégration configurée entre SalesCoach et le CRM.
*   Mapping des champs entre SalesCoach et le CRM défini.
*   Règles de détection d'opportunité configurées (par exemple, détection de signaux d'achat forts, demande de devis, accord sur une démo).

### 3.4. Déclencheur
*   SalesCoach détecte automatiquement un signal d'opportunité fort dans une conversation analysée.
*   Le commercial demande explicitement à SalesCoach de créer une opportunité suite à une qualification réussie.

### 3.5. Flux d'Exécution

| Étape | Acteur | Description |
| :---- | :----- | :---------- |
| **1.** | SalesCoach | Analyse la conversation et identifie les signaux forts indiquant une opportunité (par exemple, "Envoyez-moi une proposition", "Nous sommes prêts à avancer", "Quel est le prix ?"). |
| **2.** | SalesCoach | Extrait les informations clés de la conversation nécessaires à la création d'une opportunité : nom du compte, contact, produit/service intéressé, montant estimé, délai, étape de vente initiale. |
| **3.** | SalesCoach | Propose au commercial de créer une nouvelle opportunité dans le CRM, en affichant les champs pré-remplis. |
| **4.** | Commercial | Examine les informations proposées par SalesCoach. |
| **5.** | Commercial | Valide, ajuste ou complète les champs si nécessaire. |
| **6.** | Commercial / SalesCoach | Le commercial confirme la création, et SalesCoach interagit avec le CRM via API pour créer l'opportunité. |
| **7.** | SalesCoach | Confirme la création de l'opportunité et fournit un lien direct vers l'enregistrement CRM. |
| **8.** | SalesCoach | Suggère les prochaines étapes pour l'opportunité nouvellement créée (par exemple, "Planifier la démo produit", "Envoyer la proposition formelle"). |

### 3.6. Résultat Attendu
*   Une nouvelle opportunité créée et pré-remplie dans le CRM.
*   Gain de temps administratif pour le commercial.
*   Standardisation des informations d'opportunité.

### 3.7. Exemple d'Interaction

**Conversation récente (extraits) :**
*   Prospect : "...votre solution de gestion de projet semble correspondre parfaitement à nos besoins. Pourriez-vous nous envoyer un devis pour 50 utilisateurs avec les modules A et B ?"
*   Commercial : "Absolument ! Je vous prépare cela. Quand seriez-vous en mesure de prendre une décision ?"
*   Prospect : "Idéalement, nous aimerions avoir ça en place d'ici la fin du mois prochain."

**SalesCoach (Proposition) :**
"**Détection d'Opportunité :** Un signal d'achat clair a été détecté.
**Proposition de Création d'Opportunité :**
*   **Nom de l'Opportunité :** [Nom du Compte] - Solution Gestion Projet (50 Users)
*   **Compte :** [Nom du Compte]
*   **Contact Principal :** [Nom du Contact]
*   **Produits/Services :** Gestion de Projet - Module A, Gestion de Projet - Module B
*   **Montant Estimé :** (À estimer par le commercial, basé sur la grille tarifaire)
*   **Date de Clôture Estimée :** Fin du mois prochain
*   **Étape de Vente :** Proposition Envoyée

**Actions suggérées :**
1.  Vérifier et compléter le montant estimé.
2.  Confirmer la création de l'opportunité dans Salesforce.
3.  Préparer et envoyer la proposition formelle."

### 3.8. Bénéfices
*   **Accélération du cycle de vente :** Réduit le temps entre la détection d'une opportunité et sa formalisation.
*   **Réduction des erreurs :** Minimise les erreurs de saisie manuelle.
*   **Meilleure traçabilité :** Assure que toutes les opportunités sont correctement enregistrées.
*   **Concentration :** Permet aux commerciaux de se concentrer sur la vente plutôt que sur l'administration.

---

## 4. Cas d'Usage 3 : Suivi et Relance

### 4.1. Objectif
Aider le développeur commercial à identifier les prospects nécessitant une relance et à générer des messages de relance personnalisés et pertinents, basés sur l'historique des interactions et les informations disponibles.

### 4.2. Acteurs
*   **Agent SalesCoach (IA)**
*   **Développeur Commercial / Commercial**

### 4.3. Prérequis
*   Accès à l'historique des conversations et activités du prospect.
*   Règles de relance configurées (par exemple, délai d'inactivité, événements spécifiques comme l'ouverture d'un e-mail, la visite d'une page).
*   Intégration avec le CRM pour récupérer l'historique des interactions.

### 4.4. Déclencheur
*   SalesCoach détecte qu'un prospect n'a pas été contacté depuis une période définie (par exemple, 7 jours après une proposition).
*   SalesCoach identifie un événement pertinent (par exemple, le prospect a visité la page "Tarifs", a ouvert un e-mail).
*   Le commercial demande des suggestions de relance pour un prospect spécifique.

### 4.5. Flux d'Exécution

| Étape | Acteur | Description |
| :---- | :----- | :---------- |
| **1.** | SalesCoach | Analyse en continu l'historique des interactions (e-mails, appels, messages) et les activités des prospects. |
| **2.** | SalesCoach | Identifie les prospects "en sommeil" ou ceux qui ont montré une nouvelle activité nécessitant une relance. |
| **3.** | SalesCoach | Notifie le commercial des prospects à relancer, en indiquant la raison de la relance (par exemple, "Pas de réponse depuis X jours", "A visité la page X"). |
| **4.** | SalesCoach | Génère plusieurs propositions de messages de relance personnalisés, en se basant sur :<br> - Le dernier échange<br> - Les besoins exprimés précédemment<br> - Des informations externes (actualités de l'entreprise, nouveaux produits)<br> - L'objectif de la relance (obtenir une démo, valider un budget, etc.). |
| **5.** | SalesCoach | Suggère le canal de relance le plus approprié (e-mail, LinkedIn, appel). |
| **6.** | Commercial | Examine les suggestions de SalesCoach. |
| **7.** | Commercial | Sélectionne un message, l'adapte si nécessaire, et l'envoie au prospect. |
| **8.** | SalesCoach | Met à jour le statut du prospect et enregistre l'action de relance dans le CRM. |

### 4.6. Résultat Attendu
*   Des relances plus opportunes et ciblées.
*   Une meilleure gestion du pipeline de vente.
*   Des messages de relance personnalisés et efficaces.

### 4.7. Exemple d'Interaction

**Notification SalesCoach :**
"**Prospect à Relancer :** [Nom du Prospect] ([Nom du Compte])
**Raison :** Aucune réponse depuis 5 jours après l'envoi de la proposition de démo.
**Dernière interaction :** Envoyé un e-mail proposant une démo le [Date]."

**SalesCoach (Suggestions de Relance) :**
**Option 1 (Rappel doux) :**
`Objet : Petite relance concernant notre démo`
`Bonjour [Nom du Prospect],`
`Je voulais juste faire un suivi rapide concernant ma proposition de démo pour [Nom du Produit] que je vous ai envoyée la semaine dernière.`
`Avez-vous eu le temps d'y jeter un œil ? Je reste à votre disposition si vous avez des questions ou si vous souhaitez planifier un créneau.`
`Cordialement,`

**Option 2 (Valeur ajoutée) :**
`Objet : Une ressource pour [Problème du Prospect] + notre démo`
`Bonjour [Nom du Prospect],`
`J'espère que vous allez bien. En pensant à notre discussion sur [Problème spécifique mentionné], j'ai trouvé cet article/cette étude de cas qui pourrait vous intéresser : [Lien vers ressource].`
`Je me demandais si vous aviez pu examiner ma proposition de démo pour voir comment [Nom du Produit] pourrait vous aider concrètement avec ce défi.`
`Quand seriez-vous disponible pour un court échange ?`
`Cordialement,`

### 4.8. Bénéfices
*   **Amélioration du taux de conversion :** Réduit le nombre d'opportunités perdues par manque de suivi.
*   **Proactivité :** Assure que les prospects sont relancés au bon moment.
*   **Personnalisation :** Les messages générés sont plus pertinents et augmentent les chances de réponse.
*   **Efficacité :** Gain de temps considérable dans la rédaction des e-mails de relance.

