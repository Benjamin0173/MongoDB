Les methodes find et findOne ont la meme signature et permettent d'effectuer des requetes mongo

```
db.collection.find(<requete>, <projection> )
```

Chacun de ces parametres sont tous deux des documents.

```
DBQuery.shellBatchSize = 40
```

mongorc.js est un fichier de config qui se trouve a la racine du repertoire utilisateur

/home/userName


```
    db.maCollection.find().limit(12)
    db.maCollection.find().limit(12).pretty()
```

On peut utiliser des operateur afin d'affiner la rechecher : 
```
db.maCollection.find({"age": {$eq: 76} })
db.maCollection.find({"age": 76 }, {"prenom": true})
```





On retrouve d'autres operateurs tels que : 
- $ne : different de
- $gt : superieur a , $gte: superieur ou egal
- $lt:   ... , $lte: ...
- $in et $nin : absence ou presence 


On peut combiner ces operateurs pour effectuer des recherches sur des intervalles : 
```
    db.maCollection.find({"age": { $lt: 50, $gt: 20 }})
    db.maCollection.find({"age": { $nin: [23, 45, 78] }})
    db.maCollection.find({"age": { $exists: true }})
```


Les operateurs logiques

```
    db.personnes.find(
        {
            $and: [
                {
                    "age": { 
                        $exists: 1 
                    }
                },
                {
                    "age": {
                        $nin: [23, 45, 234, 100]
                    }
                }
        ]},
        {
            "_id": 0,
            "nom": "1",
            "prenom": "1"
        }
    )

```




db.p.find({
    "age": {$gte: 70}
})

db.p.find({
    "age": {
        $not: {
            $lt: 70
        }
    }
})




L'operateur $where fait intervenir une syntaxe particuliere : 
```
db.personnes.find({ $where: function() {
    return obj.nom.length > 4
} })
```

db.personnes.find({$where: "obj.nom.length*11 == obj.age"})




L'indexation simple: 

```
db.collection.createIndex(<champ_et_type>, <options>)

db.personnes.createIndex({"age": -1})


```

Il existe un moyen de consulter la liste des index d'une collection : 

```
db.personnes.getIndexes()
```

Pour supprimer un index il suffit d'effectuer la commande suivante: 

```
    db.personnes.dropIndex("age_-1")

    db.personnes.createIndex({"age": -1}, {"name": "unSuperNom"})

    db.personnes.createIndex({"prenom": 1}, {"background": true})
```



Les operateurs de tableaux 


```
 { $push: {<champ>: <valeur>, ...} }
```

l'operateur `push` permet d'ajouter une ou plusieurs valeurs au sein d'un tableau.



```
    db.hobbies.updateOne({"_id": 1}, {$push : {"passions": "Le roller!"}})
```


```
    db.hobbies.updateOne({
        "_id": 2
    },
    $push : {
        "passions": {
            $each: ["Minecraft", "Rise of Kingdom"]
        }
    }
    )

    db.hobbies.updateOne({
    "_id": 2
    },
    $addToSet : {
        "passions": {
            $each: ["Minecraft", "Rise of Kingdom"]
        }
    }
    )



    db.personnes.find({"interets.1": "jardinage"})


  db.personnes.find({"interets": {$all: ["jardinage", "bridge"]})

  db.personnes.find({"interets": {$size: 2}})

  db.personnes.find({"interets.1": {$exists: 1}})

```




db.salles.find({
    $expr : { $gt: [ { $mutliply: ["$_id", 100] }, "$capacite" ]  }
},
"_id": 0,
"nom": 1,
"capacite": 1
)



Les requetes geospatiales

```
 { type: <type d'objet GeoJSON>, coordinates: <coordonnees>}
```

Le type Point:
```
{
    "type": "Point",
    "cooordinates": [13.0, 1.0]
}
```


Le type multipoint: 
```
    {
        "type": "MultiPoint",
        "coordinates": [
            [13.0, 1.0] , [13.0, 3.0]
        ]
    }
```

Le type LineString: 
```
    {
        "type": "LineString",
        "coordinates": [
            [13.0, 1.0] , [13.0, 3.0]
        ]
    }
```

Le type Polyon: 
```
    {
        "type": "Polygon",
        "coordinates": [
         [
            [13.0, 1.0] , [13.0, 3.0]
        ],
        [
            [13.0, 1.0] , [13.0, 3.0]
        ]   
        ]
    }
```


Creation d'index: 

```
db.avignon.createIndex({"localisation": "2dsphere"})

db.avignon2d.createIndex({"localisation": "2d"})
```

L'operateur $nearSphere:

```
{
    $nearSphere: {
        $geometry: {
            type: "Point",
            coordinates: [<longitude>, <latitude>]
        },
        $minDistance: <distance en metres>,
        $maxDistance: <distance en metres>
    }
}

{
    $nearSphere: [ <x>, <y> ],
    $minDistance: <distance en radians>,
    $maxDistance: <distance en radians>
}
```

var opera = { type: "Point", coordinates: [43.949749, 4.805325] }


Effectuer une requete sur la collection avignon 
```
db.avignon.find(
    {
        "localisation": {
            $nearSphere: {
                $geometry: opera
            }
        }
    }, {"_id": 0, "nom": 1}
).explain()
```


### L'operateur $geoWithin

Cet operateur n'effectue aucun tri et ne necessite pas la creation
d'un index geospatiale, on l'utilise de la maniere suivante: 

```
{
    <champ des documents contenant les coordonnees> : {
        $geoWithin: {
            <operateur de forme>: <coordonnees>
        }
    }
}
```

Creation d'un polygone pour notre exemple :
``` javascript
var polygone  = [
    [43.9548, 4.80143],
    [43.95475, 4.80779],
    [43.95045, 4.81097],
    [43.4657, 4.80449],
]
```
La requete suivante utilise ce polygone : 
```
db.avignon2d.find(
  {
    "localisation": {
        $geoWithin: {
            $polygon: polygone
        }
    }
  },
  {"_id":0, "nom":1}
)
```


Signature pour le cas d'utilisation d'objets GeoJSON:
```
{
    <champ des documents contenant les coordonnees> : {
        $geoWithin: {
            type: < "Polygon" ou bien ""MultiPolygon">,
            coordinates:  [<coordonnees>]
        }
    }
}
```
var polygone  = [
    [43.9548, 4.80143],
    [43.95475, 4.80779],
    [43.95045, 4.81097],
    [43.4657, 4.80449],
    [43.9548, 4.80143],
]


db.avignon.find({
    "localisation": {
        $geoWithin: {
            $geometry : {
                type: "Polygon",
                coordinates: [polygone]
            }
        }
    }
}, {"_id": 0, "nom":1})
Le framework d'agregation

MongoDB met a disposition un puissant outil d'analyse et de 
traitement de l'information: le pipeline d'agregation (ou framework)

Metaphore du tapis roulant d'usine

Methode utilisee: 
```
    db.collection.aggregate(pipeline, options)
```

- pipeline: designe un tableau d'etapes
- options: designe un document

Parmi les options, nous retiendrons:
 - collation, permet d'affecter une collation a l'operation d'aggregation
 - bypassDocumentValidation: fonctionne avec un operateur appele $out et permet de passer au travers de la validation des documents.
 - allowDiskUse: donne la possibilite de faire deborder les operations d'ecriture sur le disque
  
Vous pouvez appeler aggregate sans argument: 
```
    db.personnes.aggregate()
```

Au sein du shell, nous allons creer une variable pipeline : 
```
 var pipeline = []
 db.personnes.aggregate(pipeline)
 db.personnes.aggregate(
    pipeline,
     {
        "collation": {
            "locale": "fr"
        }
     }
     )
```


#### Le filtrage avec $match

Cela permet d'obtenir des pipelines performants avec des temps d'execution courts.
Normalement $match doit intervenir le plus en amont possible dans le pipeline car $match agit comme un filtre en reduisant le nombre de documents a traiter plus en aval dans le pipeline. (Dans l'ideal on devrait le trouver comme premier operateur).

La syntaxe est la suivante : 
```
    { $match : {<requete>} }
```

Commencons par la premiere etape 

``` javascript
var pipeline = [{
    $match : {
        "interets": "jardinage"
    }
}]

db.personnes.aggregate(pipeline)
```

Cela correspond a la requete: 
```
    db.personnes.find({ "interets": "jardinage" })
```

``` javascript
var pipeline = [{
    $match : {
        "interets": "jardinage"
    },
    $match: {
        "nom": /^L/,
        "age": {$gt: 70}
    }
}]

db.personnes.aggregate(pipeline)
```


#### Selection/modification de champs : $project

syntaxe: 
``` javascript
    { $project: { <spec> } }
```

``` javascript
var pipeline = [{
    $match : {
        "interets": "jardinage"
    }
}, {
}, {
    $project: {
        "_id": 0,
        "nom": 1,
        "prenom": 1,
        "super_senior": { $gte: ["$age", 70] }
    }
},
{
    $match: {
        "super_senior": true
    }
}]

db.personnes.aggregate(pipeline)
```


``` javascript
var pipeline = [{
    $match : {
        "interets": "jardinage"
    }},{
    $project: {
        "id": 0,
        "nom": 1,
        "prenom": 1,
        "ville": "$adresse.ville"
    }
},
{
    $match: {
        "ville": {$exists: true}
    }
}
]












