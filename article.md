# Comparer l'incomparable : la mécanique statistique derrière le score de l'UTMB Index

*Évaluer équitablement la performance d'un coureur de trail est un problème difficile à résoudre par des formules basées sur le parcours ou la vitesse seuls. Les parcours changent d'une édition à l'autre, la météo varie, les formats de course — du 20K au 100 miles — couvrent des réalités physiologiques très différentes. L'approche statistique offre une alternative : produire un score comparable malgré tout.*

· · ·

## Un problème d'une complexité trompeuse

À première vue, comparer deux performances en trail semble simple : qui finit en moins de temps a mieux couru. Mais cette évidence s'effondre dès qu'on compare des courses différentes. Terminer un ultra de montagne de 170 kilomètres en moins de 20 heures ou boucler un 50 km vallonné en 3 heures : ces deux performances ne parlent pas le même langage. Les distances varient, les dénivelés s'accumulent différemment, les altitudes changent les équilibres physiologiques, et la densité du peloton de tête transforme radicalement ce que signifie "arriver dans le top 10". Et même sur une course donnée, d'une édition à l'autre, une modification de parcours ou des conditions météo dégradées suffisent à rendre les chronos incomparables.

C'est précisément ce problème que cherche à résoudre le calcul du score de l'UTMB Index : attribuer à chaque finissant un score normalisé (sur une échelle de 0 à 1 000 points) qui reflète non seulement le chrono, mais le contexte dans lequel il a été réalisé. Comparer des performances entre courses différentes, entre années différentes, et maintenir cette comparaison cohérente dans le temps : tels sont les objectifs du système présenté ici.

## La CCC comme cas d'étude

Pour illustrer le fonctionnement du système, on va utiliser la CCC en comparant deux éditions :

| | CCC 2022 | CCC 2024 |
|---|---|---|
| Distance | 98.5 km | 100.4 km |
| D+ / D- | 5 807 m+ / 5 995 m- | 5 714 m+ / 5 895 m- |
| KM-effort | 156 | 157 |
| Finishers | 1 727 | 1 636 |
| DNF | 386 (18 %) | 634 (28 %) |
| Météo | Instable et frais : 6-14°C à 2000 m, orages l'après-midi | Ensoleillé et chaud : 12-20°C à 2000 m, jusqu'à 30°C en vallée |
| Vainqueur | Petter Engdahl — 9:53:02 | Hayden Hawks — 10:20:11 |
| Vitesse du vainqueur | 9.97 km/h | 9.71 km/h |
| Score du vainqueur | 968 | 961 |

Les parcours sont différents sur la fin de course : la CCC 2022 passait par la Tête aux Vents à 2100m, la CCC 2024 par le Béchard, moins haut, mais par un sentier plus technique. Mais ils sont équivalents sur une formule simpliste du km-effort (100 m D+ = 1 km) : 156 vs 157 KME.

### Le score est linéaire en vitesse

Calculer un score revient à chercher une fonction f(vitesse) = score. Le choix fondamental du modèle est que cette fonction est **linéaire et passe par l'origine** : f(v) = coefficient × v. Cela implique une proportionnalité stricte entre vitesse et score. Le rapport score/vitesse est constant pour tous les coureurs d'une même édition. Un coureur deux fois plus rapide qu'un autre obtient exactement deux fois plus de points — le score à 8.28 km/h (800 pts) est exactement le double du score à 4.14 km/h (400 pts).

Tout le problème du scoring se ramène donc à trouver le bon coefficient. Et ce coefficient est **différent pour chaque édition de chaque course**.

### Pourquoi un coefficient par édition

Hawks met 27 minutes de plus qu'Engdahl, mais obtient un score quasi identique (961 vs 968). Si on appliquait le coefficient de la CCC 2022 à la vitesse de Hawks (9.71 km/h), on obtiendrait 938 points — pas 961. La formule de 2022 sous-évalue sa performance.

La raison : les conditions de course ne sont pas les mêmes. Météo fraîche en 2022, chaleur et plein soleil en 2024. Le taux de DNF passe de 18 % à 28 %. Tout le peloton est ralenti, pas seulement Hawks. Le coefficient de la CCC 2024 doit être plus élevé que celui de 2022 pour compenser : à vitesse égale, le score doit être plus haut parce que courir à cette vitesse dans ces conditions est plus difficile.

C'est ce coefficient qui absorbe les différences de parcours, de météo, de compétitivité du plateau. La question devient : comment le trouver ?

Notre objectif : expliquer comment le système arrive à scorer la CCC 2024, étape par étape.

Le système résout ce problème en quatre étapes :

1. Recherche des courses similaires
2. Calcul des expected scores et de la stabilité
3. Régression pour trouver le coefficient
4. Calcul des scores

Cet article les décrit une par une, appliquées à la CCC 2024. Trois coureurs servent de fil conducteur :

- **Hayden Hawks** — 1er, 10h20, coureur régulier, a déjà couru l'OCC
- **Dakota Jones** — 9e, 11h12, coureur régulier, a déjà couru la CCC
- **Arnaud Bonin** — 10e, 11h12, peu de données en 100K, a déjà couru l'OCC 3 fois
