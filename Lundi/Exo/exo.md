Voici la base de donnees qui permet d'effectuer la serie d'exercices : 

```
db.salles.insertMany([ 
   { 
       "_id": 1, 
       "nom": "AJMI Jazz Club", 
       "adresse": { 
           "numero": 4, 
           "voie": "Rue des Escaliers Sainte-Anne", 
           "codePostal": "84000", 
           "ville": "Avignon", 
           "localisation": { 
               "type": "Point", 
               "coordinates": [43.951616, 4.808657] 
           } 
       }, 
       "styles": ["jazz", "soul", "funk", "blues"], 
       "avis": [{ 
               "date": new Date('2019-11-01'), 
               "note": NumberInt(8) 
           }, 
           { 
               "date": new Date('2019-11-30'), 
               "note": NumberInt(9) 
           } 
       ], 
       "capacite": NumberInt(300), 
       "smac": true 
   }, { 
       "_id": 2, 
       "nom": "Paloma", 
       "adresse": { 
           "numero": 250, 
           "voie": "Chemin de l'Aérodrome", 
           "codePostal": "30000", 
           "ville": "Nîmes", 
           "localisation": { 
               "type": "Point", 
               "coordinates": [43.856430, 4.405415] 
           } 
       }, 
       "avis": [{ 
               "date": new Date('2019-07-06'), 
               "note": NumberInt(10) 
           } 
       ], 
       "capacite": NumberInt(4000), 
       "smac": true 
   }, 
    { 
       "_id": 3, 
       "nom": "Sonograf", 
       "adresse": { 
           "voie": "D901", 
           "codePostal": "84250", 
           "ville": "Le Thor", 
           "localisation": { 
               "type": "Point", 
               "coordinates": [43.923005, 5.020077] 
           } 
       }, 
       "capacite": NumberInt(200), 
       "styles": ["blues", "rock"] 
   } 
]) 
```
-----
##### Exercice 1

Affichez l’identifiant et le nom des salles qui sont des SMAC.

```JSON
db.salles.find({"smac" : true})
```

![[Pasted image 20230130154805.png]]

##### Exercice 2

Affichez le nom des salles qui possèdent une capacité d’accueil strictement supérieure à 1000 places.

```JSON
db.salles.find({"capacite" : {$gt : 1000}})
```

![[Pasted image 20230130155622.png]]

##### Exercice 3

Affichez l’identifiant des salles pour lesquelles le champ adresse ne comporte pas de numéro.

```JSON
db.salles.find({"adresse.numero" : null})
```

![[Pasted image 20230130161238.png]]

##### Exercice 4

Affichez l’identifiant puis le nom des salles qui ont exactement un avis.

```JSON
db.salles.find({"avis" : {$size: 1}})
```

![[Pasted image 20230130162456.png]]

##### Exercice 5

Affichez tous les styles musicaux des salles qui programment notamment du blues.

```JSON
db.salles.find({"styles" : "blues"})
```

![[Pasted image 20230130162153.png]]


##### Exercice 6

Affichez tous les styles musicaux des salles qui ont le style « blues » en première position dans leur tableau styles.

```JSON
db.salles.find({"styles.0" : "blues"})
```

![[Pasted image 20230130164333.png]]

##### Exercice 7

Affichez la ville des salles dont le code postal commence par 84 et qui ont une capacité strictement inférieure à 500 places (pensez à utiliser une expression régulière).

```JSON
db.salles.find({"capacite" : {$lt : 500}}, {"adresse.codepostal" : /^84.*/})
```

![[Pasted image 20230130164621.png]]

##### Exercice 8

Affichez l’identifiant pour les salles dont l’identifiant est pair ou le champ avis est absent.

```JSON
db.salles.find({$or : [{"_id" :{$mod : [2, 0]}}, {avis: {$exists: false}}]})
```

![[Pasted image 20230130164922.png]]

##### Exercice 9

Affichez le nom des salles dont au moins un des avis comporte une note comprise entre 8 et 10 (tous deux inclus).

```JSON
db.salles.find({"avis.note" : {$gte: 8, $lte: 10}})
```

![[Pasted image 20230130165239.png]]

##### Exercice 10

Affichez le nom des salles dont au moins un des avis comporte une date postérieure au 15/11/2019 (pensez à utiliser le type JavaScript Date).

```JSON
db.salles.find({"avis.date" : {$gt: new Date("2019-11-15")}})
```

![[Pasted image 20230130165407.png]]

##### Exercice 11

Affichez le nom ainsi que la capacité des salles dont le produit de la valeur de l’identifiant par 100 est strictement supérieur à la capacité.

```javascript
db.salles.find({$epx : {$gt: [ {$multiply : ["$_id",100]}, "$capacite"]}}, "_id":0,"nom":1,"capacite":1)
```


##### Exercice 12

Affichez le nom des salles de type SMAC programmant plus de deux styles de musiques différents en utilisant l’opérateur $where qui permet de faire usage de JavaScript.

bug avec $where

```javascript
db.salles.find({ $where: function() { return this.type === "SMAC" && this.styles.length > 2; } }, {nom: 1, _id: 0})
```

##### Exercice 13

Affichez les différents codes postaux présents dans les documents de la collection salles.

```JSON
db.salles.distinct({"adresse.codePostal"})
```

![[Pasted image 20230130165929.png]]

##### Exercice 14

Mettez à jour tous les documents de la collection salles en rajoutant 100 personnes à leur capacité actuelle.

```JSON
db.salles.updateMany({}, {$inc : {capacite: 100}})
```

![[Pasted image 20230130170734.png]]
![[Pasted image 20230130170802.png]]

##### Exercice 15

Ajoutez le style « jazz » à toutes les salles qui n’en programment pas.
![[Pasted image 20230130170948.png]]

```JSON
db.salles.updateMany({"styles" : {$not: {$in : ["jazz"]}}}, {$push: {styles: "jazz"}})
```

![[Pasted image 20230130171053.png]]
![[Pasted image 20230130171036.png]]


##### Exercice 16

Retirez le style «funk» à toutes les salles dont l’identifiant n’est égal ni à 2, ni à 3.

![[Pasted image 20230130171413.png]]

```JSON
db.salles.updateMany({_id: {$ne:2, $ne:3}, styles: "funk"}, {$pull: {styles: "funk"}})
```

![[Pasted image 20230130171458.png]]
![[Pasted image 20230130171520.png]]



##### Exercice 17

Ajoutez un tableau composé des styles «techno» et « reggae » à la salle dont l’identifiant est 3.

![[Pasted image 20230130171950.png]]

```JSON
db.salles.update({_id: 3}, {$set: {styles: ["techno", "reggae"]}})
```

![[Pasted image 20230130172014.png]]
![[Pasted image 20230130172033.png]]


##### Exercice 18

Pour les salles dont le nom commence par la lettre P (majuscule ou minuscule), augmentez la capacité de 150 places et rajoutez un champ de type tableau nommé contact dans lequel se trouvera un document comportant un champ nommé telephone dont la valeur sera « 04 11 94 00 10 ».
![[Pasted image 20230131091505.png]]





##### Exercice 19

Pour les salles dont le nom commence par une voyelle (peu importe la casse, là aussi), rajoutez dans le tableau avis un document composé du champ date valant la date courante et du champ note valant 10 (double ou entier). L’expression régulière pour chercher une chaîne de caractères débutant par une voyelle suivie de n’importe quoi d’autre est [^aeiou]+$.

![[Pasted image 20230131100005.png]]

```JSON
db.salles.updateMany({name: { $regex : /[^aeiou]+$/}}, {$push: {avis: {date : new Date(), note : 10}}})
```

![[Pasted image 20230131100031.png]]
![[Pasted image 20230131100100.png]]



##### Exercice 20

En mode upsert, vous mettrez à jour tous les documents dont le nom commence par un z ou un Z en leur affectant comme nom « Pub Z », comme valeur du champ capacite 50 personnes (type entier et non décimal) et en positionnant le champ booléen smac à la valeur « false ».

aucun document a un Nom commencant pas un Z ou z

```JSON
db.salles.updateOne({name: /^[zZ]/}, {$set: {name: "Pub Z", capacite: 50, smac: false }}, {upsert: true})
```

![[Pasted image 20230131095636.png]]

![[Pasted image 20230131095545.png]]

mais il a été crée avec la commande

##### Exercice 21

Affichez le décompte des documents pour lesquels le champ _id est de type « objectId ».

```JSON
db.salles.update({_id: {$type: "objectId"}})
```

![[Pasted image 20230131092052.png]]
Il sont tous des en int32
![[Pasted image 20230131092210.png]]


##### Exercice 22

Pour les documents dont le champ _id n’est pas de type « objectId », affichez le nom de la salle ayant la plus grande capacité. Pour y parvenir, vous effectuerez un tri dans l’ordre qui convient tout en limitant le nombre de documents affichés pour ne retourner que celui qui comporte la capacité maximale.

```JSON
db.salles.find({"_id": {$ne: ObjectId("")}}, {"nom": 1, "capacité": 1})
.sort({"capacité": -1}) .limit(1)
```


##### Exercice 23

Remplacez, sur la base de la valeur de son champ _id, le document créé à l’exercice 20 par un document contenant seulement le nom préexistant et la capacité, que vous monterez à 60 personnes.

![[Pasted image 20230131095545.png]]

```JSON
db.salles.replaceOne({_id: ObjectId("<UN OBJECTID>")}, {nom: "Pub Z", capacite: 60})
```

![[Pasted image 20230131102742.png]]
![[Pasted image 20230131102758.png]]

##### Exercice 24

Effectuez la suppression d’un seul document avec les critères suivants : le champ _id est de type « objectId » et la capacité de la salle est inférieure ou égale à 60 personnes.

```JSON
db.salles.deleteOne({_id: {$type: "objectId"}, "capacity" : {$lte: 60}})
```

![[Pasted image 20230131094304.png]]


##### Exercice 25

À l’aide de la méthode permettant de trouver un seul document et de le mettre à jour en même temps, réduisez de 15 personnes la capacité de la salle située à Nîmes.

![[Pasted image 20230131093206.png]]

