# Notes d'entretien — _ton cas_ (À COMPLÉTER)

> Mini-cours `01`. Ce fichier sert d'abord à **toi** ; il est aussi lu pour
> évaluer ta préparation. Renomme en `notes_entretien.md`.

## 1. Avant le rendez-vous — 12 questions + 3 de réserve

> Le client accorde **12 réponses**. **Une question à la fois**. Classe par
> priorité : si tu n'en poses que 8, ce doivent être les 8 plus utiles.
> Catégories à couvrir : besoin · processus actuel · données (existence, volume,
> qualité, **extrait**) · données personnelles / confidentialité · critère de
> succès chiffré · coût d'une erreur · utilisateurs · SI / hébergement · budget / délai.

| #  | Priorité (1-3) | Catégorie | Question |
|----|-|---|--|
| 1  | 1 | Besoin | Quel problème concret souhaitez-vous résoudre avec la détection d'anomalies sur le bain, et quelle décision doit-elle aider à prendre ? |
| 2  | 1 | Processus actuel | Comment les dérives ou anomalies du bain sont-elles détectées aujourd'hui, et que se passe-t-il lorsqu'une anomalie est suspectée ? |
| 3  | 1 | Données | De combien d'historique disposez-vous pour la température, le pH, le niveau et les autres paramètres du procédé ? |
| 4  | 1 | Données / qualité | Disposez-vous d'un historique des incidents, défauts qualité ou interventions permettant de savoir quand une anomalie réelle s'est produite ? |
| 5  | 1 | Données / extrait | L'extrait de 48 h transmis est-il représentatif du fonctionnement habituel, et la dérive visible en fin de période correspond-elle à un incident connu ? |
| 6  | 2 | Utilisateurs | Qui consultera les alertes en pratique et qui aura la responsabilité de confirmer, ignorer ou contredire une alerte ? |
| 7  | 2 | Budget / délai | Quel budget et quel délai cible avez-vous pour un premier prototype puis, éventuellement, pour une mise en production ? |
| 8  | | | |
| 9  | | | |
| 10 | | | |
| 11 | | | |
| 12 | | | |
| R1 | réserve | | |
| R2 | réserve | | |
| R3 | réserve | | |

## 2. Pendant le rendez-vous — dit / interprété

| Question posée (telle quelle) | Ce que le client a **dit** (citation) | Ce que j'en **interprète** |
|---|---|---|
| Bonjour Hervé, on m'a parlé de vos problèmes de bains de zinc. Pourriez vous me transmettre un exemple des informations relevées par les capteurs, afin que je puisse voir à quelles informations on a accès et à quelle fréquence ? | « Je vous envoie 48 heures de relevés d'un bain, exportées de la supervision. Vous me direz si vous y voyez quelque chose. » | Un premier extrait de 48h est disponible. Il permet d'observer d'éventuelles tendances ou dérives, mais reste trop court pour définir à lui seul le fonctionnement normal du procédé sur la durée. |
| Qu'attendez vous d'un modèle IA déployé chez Galvaplus ? | « Nos bains tombent en panne deux à trois fois par mois : résistance qui lâche, dérive de température, problème de niveau. Chaque arrêt non prévu nous coûte environ 30 000 euros : production perdue, zinc à refondre, clients livrés en retard. On voudrait être prévenus 48 heures avant. » | Le besoin principal est d'anticiper les pannes environ 48h à l'avance afin de pouvoir organiser une intervention avant l'arrêt de production. Le gain potentiel est important au vu du coût d'une panne non planifiée. |
| Auriez vous peut être les plages standard de valeurs pour la température celsius, le ph et le niveau pct ? | « Il y a des seuils d'alarme dans la supervision : si la température passe 470, ça sonne. Mais quand ça sonne, c'est déjà trop tard. Un stagiaire a fait des graphiques Excel une fois, c'était joli mais ça n'a servi à rien. » | Des seuils d'alerte existent déjà, mais ils interviennent trop tard. L'objectif est donc plutôt de détecter une dérive avant le dépassement de ces seuils, en s'appuyant sur l'évolution des mesures dans le temps. |
| Quel type de maintenance / surveillance avez vous aujourd'hui ? | « Oui, quand un technicien sent que ça va lâcher, il change la pièce avant. Ces interventions-là sont dans un autre fichier, celui de la maintenance préventive. Du coup, certaines pannes n'ont jamais eu lieu parce qu'on les a évitées. » | Les techniciens anticipent déjà certaines pannes grâce à leur expérience. Le fichier de maintenance préventive sera donc important, car il peut contenir des exemples de dérives ayant conduit à une intervention avant qu'une panne ne se produise. |
| A quelle fréquence sont effectués les relevés ? | « Chaque bain a des capteurs : température, pH du bain de préparation, et niveau de zinc. Ils remontent dans notre supervision toutes les heures. On garde tout depuis deux ans, mais personne ne regarde vraiment, sauf quand il y a une alarme. » | Deux années de données horaires sont disponibles sur plusieurs bains, ce qui représente a priori un historique suffisant pour commencer l'analyse. Il faudra cependant vérifier la qualité des données, notamment les valeurs manquantes, les éventuelles ruptures de mesure et les changements de capteurs. |
| 48h c'est une limite stricte pour vous ? si la dérive se fait sur une période plus courte, comment voulez vous procéder ? | « En 48 heures, je peux planifier un arrêt propre : vider le planning, prévenir la maintenance, commander la pièce. Un arrêt planifié coûte trois à quatre fois moins cher qu'une panne en pleine production. » | Le délai de 48h correspond à un besoin opérationnel concret : il permet de préparer l'arrêt et de limiter fortement son coût. Une détection plus tardive peut rester utile, mais sa valeur métier sera plus faible. |
| Quel est votre budget ? | « Si ça évite ne serait-ce que dix pannes par an, ça fait 300 000 euros. La direction est prête à mettre 60 à 80 000 euros la première année, si on lui montre que ça marche avant. » | Le budget prévu est de 60 à 80 k€ la première année, avec une attente forte de démonstration de valeur avant un déploiement plus large. Une phase de POC ou de pilote paraît donc adaptée. |

_Relance non prévue ? Note-la aussi, avec la raison (« réponse surprenante sur… »)._

### Boussole — ce que j'ai déjà obtenu

> Mets-la à jour **après chaque réponse**. Elle suit des **informations**, pas
> tes questions : une réponse peut en remplir plusieurs, une autre aucune.
> Quand il te reste 3-4 questions, regarde les 🔴 : lequel manquera le plus à
> ton cadrage ? C'est à toi de formuler la question.
>
> 🟢 obtenu · 🟠 partiel / à vérifier · 🔴 à obtenir · ⬜ pas demandé (→ §3)

| Information | Statut | Réponse n° |
|---|---|---|
| Besoin réel (≠ demande exprimée) | 🟢 obtenu | 2, 6 |
| Processus actuel | 🟢 obtenu | 3, 4 |
| Données : existence | 🟢 obtenu | 1, 5 |
| Données : volume | 🟢 obtenu | 5 |
| Données : qualité | 🟠 partiel / à vérifier | 1, 5 |
| Données : extrait obtenu | 🟢 obtenu | 1 |
| Données personnelles / confidentialité | 🔴 à obtenir | |
| Critère de succès chiffré | 🟠 partiel / à vérifier | 2, 6 |
| Coût d'une erreur | 🟢 obtenu | 2, 6 |
| Erreurs tolérées (chiffre : fausses alertes, mauvais routage…) | 🔴 à obtenir | |
| Utilisateurs | 🟠 partiel / à vérifier | 4, 6 |
| Validation humaine / qui décide | 🟠 partiel / à vérifier | 4 |
| SI / hébergement | 🔴 à obtenir | |
| Budget | 🟢 obtenu | 7 |
| Délai | 🟠 partiel / à vérifier | 6, 7 |
| Ce qui a déjà été essayé | 🟢 obtenu | 3 |

## 3. Après — ce que je n'ai pas pu demander → questions ouvertes

| Je n'ai pas pu demander / pas eu de réponse claire | Pourquoi c'est important                                      | → §6 du cadrage |
|----------------------------------------------------|---------------------------------------------------------------|---|
| SI / Hébergement                                   | Pour savoir comment on pourrait mettre en place notre système | |
| Confidentialité                                    | Pour être surs du cadre juridique dans lequel on se trouve    | |
