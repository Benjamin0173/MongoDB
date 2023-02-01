
index en BDD : liste de mots récurant
->plus rapide
![[Pasted image 20230131141136.png]]

```javascript
db.personnes.insert(
{"nom": "Durand", "prenom": "René", "interets": ["jardinage", "bricolage"], "age": 77},  
{"nom": "Durand", "prenom": "Gisèle", "interets": ["bridge", "cuisine"], "age": 75},  
{"nom": "Dupont", "prenom": "Gaston", "interets": ["jardinage",   "pétanque"], "age": 79},  
{"nom": "Dupont", "prenom": "Catherine", "interets": ["cuisine"], "age": 66},  
{"nom": "Duport", "prenom": "Eric", "interets": ["cuisine", "pétanque"], "age": 57},  
{"nom": "Duport", "prenom": "Arlette","interets": ["jardinage"], "age": 80},  
{"nom": "Lejeune", "prenom": "Jean","interets": ["jardinage"], "age": 75},  
{"nom": "Lejeune", "prenom": "Mariette","interets": ["jardinage", "bridge"], "age": 66}  )
```

``` javascript
db.personnes.createIndex(<champ_et__type>, <option>)
db.personnes.createIndex({"age": -1})

db.personnes.dropIndex(age_-1)
db.personnes.createIndex({"age": -1}, {"name": "unSuperNom"})
db.personnes.createIndex({"prenom": 1}, {"background": true})


db.personnes.find({"interets": {$all: ["jardinage", "bridge"]}})
db.personnes.find({"interets.1": "jardinage"})

db.personnes.find({"interets.1": {$exists : 1}})

db.salles.find({$epx : {$gt: [ {$multiply : ["$_id",100]}, "$capacite"]}}, "_id":0,"nom":1,"capacite":1)

```


---
```javascript
db.hobbies.insertMany([   {"_id": 1, "nom": "Yves"},   {"_id": 2, "nom": "Sandra", "passions": []},   {"_id": 3, "nom": "Line", "passions": ["Théâtre"]}  ])
```



les operateur tableaux : 

```javascript
{$push : {<champ>: <valeur>, ...}}
```

l'operateur "push" permet d'ajouter une ou plusieurs valeurs au sain d'un tableau


```javascript
db.hobbies.updateOne({"_id": 1}, {$push : {"passion": "le roller!"}})




db.hobbies.updateOne({"_id": 2}, {$push : {"passions": {$each : ["Minecraft", "Rise of Kingdom"]}})

//Evite les doublons
db.hobbies.updateOne({"_id": 2}, {$addToSet : {"passions": {$each : ["Minecraft", "Rise of Kingdom"]}})
```

---
# les requetes geospatiales

```javascript
{type : <type dobject GEOJson>, coordinates : <coordonnees>} 
```

le type Point

```javascript
{type : "Point",
	"coodinates" : [13.0, 1.0]
} 
```

le type multiPoint

```javascript
{type : "Point",
	"coodinates" : [13.0, 1.0], [13.0,3.0]
} 
```

le type LineString

```javascript
{type : "LineString",
	"coodinates" : [13.0, 1.0], [13.0,3.0]
} 
```

le type Polygone

```javascript
{type : "Polygon",
	"coodinates" : [
		[
			[13.0, 1.0], [13.0,3.0]
		], 
		[
			[13.0, 1.0], [13.0,3.0]
		]
	]
} 
```



---

db.avignon.createIndex({"localisation" : "2dsphere"})

db.avignon2d.createIndex({"localisation" : "2d"})

l'operateur $nearSphere:

```javascript
{
$nearSphere: {
	$geometry: {
		type: "Point",
		"coodinates" : [<longitute>], [<latitude>]
		
		}
	}
	$minDistance: <distance en metre>
	$maxDistance: <distance en metre>
}

```


```javascript
var opera= {type: "Point", coordinates: [43.949749,4.805325]}
```

Effectuer une requete sur la collection avignon

```javascript
db.avignon.find(
	{
		$localisation: {
			$nearSphere: {
				$geometry: {opera}
			}
		}
	},{ "_id": 0, "nom" : 1}
).explain()
```

Cet operateur n'effectue aucun tri et ne necessite 


```javascript
{
	<champ des documents contenant les coordonnees> : {
		$geoWithin: {
			<operateur de forme>: <coordonnees>
		}
	}
}
```

Création d'un polygone pour notre exemple : 

```javascript
var polygone = [
	[43.9548, 4.80143],
	[43.95475, 4.80779],
	[43.95045, 4.81097],
	[43.9548, 4.80449],
]

```

la requete suivante utilise le polygone:

```javascript
db.avignon2d.find({
	"localisation": {
		$geoWithin: {
			$polygon: polygon
		}
	}
},
{"_id":0, "nom":-1} 
)

```

Signature pour le cas d'utilisation d'objects GeoJSON:
```javascript
{
	<champ des documents contenant les coordonnees> : {
		$geoWithin: {
			<operateur de forme>: <coordonnees>
		}
	}
}
```