#### Exercice 1

Écrivez le pipeline qui affichera dans un champ nommé ville le nom de celles abritant une salle de plus de 50 personnes ainsi qu’un booléen nommé grande qui sera positionné à la valeur « vrai » lorsque la salle dépasse une capacité de 1 000 personnes. Voici le squelette du code à utiliser dans le shell :
```
var pipeline = [ 
... 
] 
```

``` JSON
  var pipeline1 = [
    {
        $match: {
            capacite: {$gt: 50}
        }
    },
    {
        $addFields: {
            ville: "$adresse.ville",
            grande: {
                $cond: {
                    if: {$gt: ["$capacite", 1000]},
                    then: true,
                    else: false
                }
            }
        }
    },
    {
        $project: {
            _id: 0,
            nom: 1,
            ville: 1,
            grande: 1
        }
    }
]
```

db.salles.aggregate(pipeline) 

![[Pasted image 20230202122322.png]]

#### Exercice 2


Écrivez le pipeline qui affichera dans un champ nommé apres_extension la capacité d’une salle augmentée de 100 places, dans un champ nommé avant_extension sa capacité originelle, ainsi que son nom.


```JSON
db.salles.aggregate([
    {
       $project: {
          "avant_extension": "$capacite",
          "apres_extension": { $add: [ "$capacite", 100 ] },
          "nom": 1
       }
    }
 ])
```

![[Pasted image 20230202123539.png]]


#### Exercice 3

Écrivez le pipeline qui affichera, par numéro de département, la capacité totale des salles y résidant. Pour obtenir ce numéro, il vous faudra utiliser l’opérateur $substrBytes dont la syntaxe est la suivante :
```
{$substrBytes: [ < chaîne de caractères >, < indice de départ >, 
< longueur > ]} 
```

```JSON
 db.salles.aggregate([
    {
       $group: {
          _id: {$substrBytes: ["$adresse.codePostal", 0, 2]},
          totalCapacity: {$sum: "$capacite"}
       }
    }
 ])
```

![[Pasted image 20230202123653.png]]


#### Exercice 4

Écrivez le pipeline qui affichera, pour chaque style musical, le nombre de salles le programmant. Ces styles seront classés par ordre alphabétique.

```JSON
db.salles.aggregate([
    {$unwind: "$styles"},
    {$group: {_id: "$styles", nombreSalles: {$sum: 1}}},
    {$sort: {_id: 1}}
  ])
```

![[Pasted image 20230202124112.png]]

#### Exercice 5

À l’aide des buckets, comptez les salles en fonction de leur capacité :

```JSON
  db.salles.aggregate([
    {
       $bucket: {
          groupBy: "$capacite",
          boundaries: [100, 500], //   boundaries: [500, 5000],
          default: "autre",
          output: {
             nombre_salles: { $sum: 1 }
          }
       }
    }
 ])
```

##### celles de 100 à 500 places

![[Pasted image 20230202124219.png]]

##### celles de 500 à 5000 places

![[Pasted image 20230202124324.png]]

#### Exercice 6

Écrivez le pipeline qui affichera le nom des salles ainsi qu’un tableau nommé avis_excellents qui contiendra uniquement les avis dont la note est de 10.

```JSON
 db.salles.aggregate([
    {
       $project: {
          nom: 1,
          avis_excellents: {
             $filter: {
                input: "$avis",
                as: "avis",
                cond: { $eq: [ "$$avis.note", 10 ] }
             }
          }
       }
    }
 ])
```

![[Pasted image 20230202124453.png]]