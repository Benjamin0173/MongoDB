----


MongoDB met a disposition un puissant outil d'analyse et de traitement de l'information : 
le pipeline d'agregation (ou framework)

Metaphore du tapis roulant d'usine

Methode utilisee:

```JSON
db.collection.aggregate(pipeline, options)
```

- pipeline: designe un tableau d'étapes
- options: désigne un document

Parmi les options, nous retiendrons:
- collation, permet d'affecter une collation a l'operation d'aggregation
- bybassDocumentValidation: fonctionne avec un operateur appele $out et permet de passer au travers de la validation des Documents
- allowDiskUse: donne la possibilite de faire déborder les operations d'écriture sur le disque

Vous pouvez aussi aggregate sans argument :

```JSON
	db.personnes.aggregate()
```

Au sein de shell, nous allons creer une variable pipeline : 

```shell
var pipeline = []
db.personnes.aggregate(piepline)
db.personnes.aggregate(
	pipeline,
	{
		"collation": {
			"locale": "fr"
		}
	}
)
```

-----

Le filtrage avec $match

Cela permet d'obtenir des pipelines performants avec des temps d'execution courts

Normalement $match dois intervenir le plus en amont possible dans le pipeline car $match agit comme un filtre en réduisant le nombre de document a traiter plus en aval dans le pipeline. (Dans l'idéal on devrait le trouver comme premier operateur).

la syntaxe est le suivante :

```JSON
	{ $match : {<requete>} }
```

Commencons par la 1er etape

```JSON
var pipeline = [{
	$match : {
		"interets": "jardinage"
	}
}]

db.personnes.aggregate(pipeline)
```

Cela correspond a la requete : 

```JSON
db.personnes.find({"interets": "jardinage"})
```



```JSON
var pipeline = [{
	$match : {
		"interets": "jardinage"
	},
	$match : {
		"nom": /^L/,
		"age": {$gt: 70}
	}
}]

db.personnes.aggregate(pipeline)
```


#### Selection/Modification de champs : $project

syntaxe:

```javascript
{ $project: { <spec> } }
```

```javascript
var pipeline = [{
	$match : {
		"interets": "jardinage"
	}
}, {
	$project : {
		"_id": 0,
		"nom": 1,
		"prenom": 1,
		"super_senior": { $gte: ["$age", 70]},
	}
}, {
	$match:{
		"super_senior": true
	}
}]

db.personnes.aggregate(pipeline)
```

```javascript
var pipeline = [{
	$match : {
		"interets": "jardinage"
	}
}, {
	$project : {
		"_id": 0,
		"nom": 1,
		"prenom": 1,
		"ville": "$adresse.ville",
	}
}, {
	$match:{
		"ville": {$exists: true}
	}
}]

db.personnes.aggregate(pipeline)
```