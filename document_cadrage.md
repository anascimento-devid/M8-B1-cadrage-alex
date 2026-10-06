# Document de cadrage — _ton cas_ (À COMPLÉTER — 3 pages max)

> **Livrable principal.** Lisible par le persona client (pas un dev). Renomme en
> `document_cadrage.md`. Les analyses (besoin, données, risques, KPI) se font
> **directement ici** : pas de fichiers séparés. Le schéma vit dans
> `schema_archi_cible.md`, tes notes dans `notes_entretien.md`.
> **3 pages est un plafond** : phrases courtes, tableaux, pas de remplissage.

## 1. Synthèse exécutive (5-6 lignes — rédigée EN DERNIER)
_Besoin réel + solution proposée (famille, pas la stack) + 2-3 indicateurs clés._

> **Imprévu client (14h30) — ce que ça change** : _1-2 lignes : quelle contrainte
> a bougé, quelles sections tu as mises à jour (données ? risques ? archi ? KPI ?)._

## 2. Besoin métier et contexte (1 paragraphe)
_Demande exprimée (citation) vs **besoin réel reformulé**. Contraintes révélées
en entretien (budget, équipe, confidentialité…)._

Une entreprise industrielle garde des pièces en acier dans des cuves de zinc pour les protéger de la rouille.

Ils ont apparamment des dérives de température, résistances qui lachent et problèmes de niveau au fil du temps qui aboutissent a des pannes des bains très couteuses.

Ils aimeraient etre prévenus 48h avant des pannes pour faire de la maintenance prédictive et réduire ce cout. 

Leur budget pour la première année est de 60.000 à 80.000 euros.


## 3. Données — mini-cours `02`
| Donnée | Existante / à acquérir | Volume, qualité estimée | Personnelle ? |
|---|---|---|---|
| Horodatage des mesures | Existante | 48 mesures horaires sur 48 h, sans valeur manquante observée | Non |
| Identifiant du bain (`bain_id`) | Existante | Présent sur les 48 lignes ; un seul bain observé dans l’extrait (`BAIN-02`) | Non |
| Température du bain (`temperature_c`) | Existante | 48 valeurs numériques, complètes ; plage observée : 446,9 à 455,3 °C | Non |
| pH (`ph`) | Existante | 48 valeurs numériques, complètes ; plage observée : 4,58 à 5,42 | Non |
| Niveau du bain (`niveau_pct`) | Existante | 48 valeurs numériques, complètes ; plage observée : 81,8 à 87,4 % | Non |
| Historique plus long des capteurs | À acquérir | Nécessaire pour caractériser les variations normales, la saisonnalité et établir des seuils fiables | Non |
| Historique des incidents / anomalies réelles | À acquérir | Non présent dans l’extrait ; nécessaire pour valider une détection supervisée ou mesurer les performances | Potentiellement, selon les informations associées |
| Événements de maintenance / interventions | À acquérir | Non présent dans l’extrait ; permettrait de relier les dérives capteurs aux causes réelles | Potentiellement, si un opérateur est identifié |

_Si le client t'a transmis un extrait : 1-2 constats de qualité observés dessus.
Ce que tu n'as pas demandé n'existe pas : écris-le en question ouverte (§6)._

L’extrait contient 48 observations horaires consécutives, sans valeur manquante ni doublon apparent sur les variables fournies.

Une dérive progressive et corrélée est visible en fin de période : hausse de la température et du pH accompagnée d'une baisse du niveau. 

L’extrait est donc pertinent pour tester une détection de dérive, mais il est trop court pour définir à lui seul le comportement normal du procédé.

## 4. Risques et conformité — mini-cours `04` et `07`

**Usage réel** (2-3 lignes) : _qui utilise la sortie, ce qu'elle déclenche, qui peut la contredire._

La sortie est utilisée par les opérateurs ou responsables de maintenance pour signaler une dérive anormale du bain (température, pH, niveau). 

Elle déclenche une alerte ou une vérification, mais ne pilote pas directement l'installation. 

L'opérateur reste décisionnaire et peut ignorer ou contredire l'alerte après contrôle terrain.

**Qualification AI Act** : _niveau + cas du texte (ou pourquoi aucun) + condition de bascule._

xA priori **pas un système à haut risque au sens de l'annexe III** : le modèle surveille un procédé industriel et ne prend pas de décision concernant des personnes physiques.

Il reste un système d'IA soumis aux obligations générales applicables.  

Bascule possible vers le **haut risque** s'il devient un composant de sécurité d'un produit réglementé relevant de l'annexe I, ou s'il est intégré à un usage expressément visé par l'annexe III et remplit les conditions de l'article 6. 

Le maintien d'une validation humaine avant action limite ici l'autonomie du système.  

**RGPD** : _base légale proposée et pourquoi ; profilage ? art. 22 (2 conditions) ?_

Les données actuellement utilisées (température, pH, niveau, identifiant de bain et horodatage) ne sont pas des données personnelles : **le RGPD n'est donc pas applicable à ce périmètre et aucune base légale n'est nécessaire**.  

Il n'y a pas de profilage de personnes physiques. L'article 22 n'est pas applicable : il n'existe ni décision individuelle fondée exclusivement sur un traitement automatisé, ni effet juridique ou effet significatif similaire sur une personne. Si des identifiants d'opérateurs ou des données de performance individuelle étaient ajoutés, il faudrait réévaluer ce point.


| Risque (éthique, métier, conformité) | 🔴/🟠/🟡 | Obligation ou raison                                                                               | Traitement dans l'archi |
|---|---|----------------------------------------------------------------------------------------------------|---|
| Faux négatif : une dérive réelle n'est pas détectée | 🔴 | Dégradation possible du procédé, des produits ou un risque opérationnel                            | Seuils conservateurs, suivi de plusieurs variables |
| Faux positif : alerte sans anomalie réelle | 🟠 | Risque d'interventions inutiles et de perte de confiance des utilisateurs                          | Score de confiance, persistance de l'anomalie sur plusieurs mesures et validation humaine |
| Dérive du procédé ou du comportement des capteurs | 🟠 | Les données futures peuvent différer des données ayant servi au calibrage                          | Surveillance des distributions, recalibrage périodique et suivi des performances |
| Données d'apprentissage insuffisantes | 🟠 | L'extrait disponible ne couvre que 48 h et ne représente probablement pas tous les régimes normaux | Acquérir un historique plus long et des incidents réels avant mise en production |
| Surconfiance dans la recommandation du modèle | 🟠 | Un opérateur pourrait considérer toute alerte comme certaine                                       | Présenter le système comme une aide à la décision, afficher les mesures responsables de l'alerte et conserver une validation humaine |
| Traçabilité insuffisante des alertes | 🟡 | Difficile d'expliquer après coup pourquoi une alerte a été produite                                | Journaliser les entrées, le score, les seuils, la version du modèle et la décision de l'opérateur |
| Introduction future de données personnelles | 🟡 | Des données de maintenance pourraient contenir des noms ou identifiants d'opérateurs               | Minimisation / pseudonymisation et réévaluation RGPD avant leur intégration |
| Arrêt automatique injustifié du bain | 🔴 | Un faux positif pourrait provoquer un arrêt de production et des pertes importantes | Pas d'arrêt automatique à ce stade ; alerte + validation humaine obligatoire |

**Sécurité du modèle** — selon l'**exposition** de ton archi : 2 menaces
plausibles minimum, les autres écartées en 1 ligne. Mitiger ≠ supprimer.

| Menace | Plausibilité sur CE cas | Mitigation proposée | Risque résiduel |
|---|---|---|---|
| Indisponibilité ou perte du modèle / flux de données | Élevée dans un environnement industriel | Monitoring du service, alertes en cas de données absentes, mode dégradé et conservation des alarmes industrielles classiques | 🟡 Une panne peut temporairement supprimer l'aide apportée par le modèle |

## 5. Architecture cible et sobriété — mini-cours `05`

Voir `schema_archi_cible.md`.

L'architecture retenue reste volontairement simple : récupération des données issues de la supervision, stockage de l'historique, modèle de détection de dérive, validation humaine, puis journalisation des alertes et des décisions.

**LLM retenu ou refusé :**  
Le recours à un LLM est écarté. Le besoin porte sur l'analyse de séries temporelles numériques issues de capteurs, et non sur du texte ou de la génération de contenu. Un modèle de machine learning classique ou de détection d'anomalies est donc plus adapté, plus simple à maintenir et moins coûteux.

**Ce qu'on écarte :**  
Pas de RAG, de base vectorielle ou de système multi-agents, car ces briques n'apportent pas de valeur ici. La décision de maintenance n'est pas automatisée : le technicien reste responsable de la validation finale.

**Évolution envisagée mais non retenue à ce stade : arrêt automatique du bain.**  
Cette option augmente fortement le risque métier car la sortie du modèle déclencherait directement une action sur le procédé industriel. 

Tant que les performances du modèle n'ont pas été validées sur un historique représentatif et que les règles de sécurité n'ont pas été définies avec le client, la validation humaine reste obligatoire.

## 6. Indicateurs, seuils, questions ouvertes — mini-cours `03`

| Indicateur | Cible | Seuil d'acceptabilité | Comment on le mesure |
|---|---|---|---|
| Taux de pannes détectées à l'avance | ≥ 90 % | ≥ 80 % | Nombre de pannes ayant généré une alerte avant l'arrêt / nombre total de pannes |
| Part des pannes détectées au moins 48h avant | ≥ 80 % | ≥ 60 % | Comparaison entre l'heure de l'alerte et l'heure réelle de la panne ou de l'intervention |
| Délai moyen d'anticipation | ≥ 48 h | ≥ 24 h | Temps moyen entre la première alerte pertinente et la panne / intervention |
| Faux positifs | À définir avec le client, idéalement très faibles | À définir | Nombre d'alertes non suivies d'une panne ou d'une intervention pertinente |
| Faux négatifs | Le plus proche possible de 0 | À définir, mais faible | Nombre de pannes n'ayant donné lieu à aucune alerte préalable |
| Disponibilité du système de détection | ≥ 99 % | ≥ 95 % | Temps pendant lequel le système reçoit les données et produit correctement ses analyses |
| Qualité des données reçues | ≥ 99 % de mesures exploitables | ≥ 95 % | Taux de mesures reçues sans valeur manquante, incohérente ou hors format |
| Gain économique | Éviter au moins 10 pannes/an | Projet rentable par rapport au budget engagé | Nombre de pannes évitées × coût moyen d'une panne, comparé au coût de la solution |

Prochaines étapes (3) + **questions ouvertes** au client (reprises de `notes_entretien.md` §3)._
### Questions ouvertes

- Combien de fausses alertes par mois les équipes considèrent-elles comme acceptables ?
- Quel taux minimal de pannes détectées serait jugé suffisant pour valider le POC ?
- Quel niveau d'anticipation reste utile si les 48h ne sont pas atteintes ?
- Qui reçoit l'alerte et qui décide réellement de lancer une intervention ?
- Où la solution devra-t-elle être hébergée : sur site, sur le SI existant ou dans le cloud ?
- Les historiques de maintenance contiennent-ils des données personnelles ou des informations confidentielles ?
- Les données des deux dernières années sont-elles complètes et homogènes sur tous les bains ?