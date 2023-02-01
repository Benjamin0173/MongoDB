
#### Le Format JSON: Pourquoi?
JSON format universel, trés répendue
Aucune stucture imposée (stocker en JSON) (seul condition syntaxe)
2 documents peut avoir les meme champs
LES BDD stockant des document permettent la gestion d'une quantité immense de données

##### Exemple : 
MYSQL Contrainte : table utilisateur column nom,adresse,num -> ajouter 2num -> modifier structure -> ajouter potentiel 3num/ ajouter des champ -> trés lourd ->création de table externe  --> table adresse

Pas c'est probleme avec Mongo/ il est plus souple
gere rapidement d'enorme quantité de données
schemaless = sans schema

##### Definition : 
document : deff
schema : deff
collection :


#### Qu'est-ce que MongoDB?

##### BDD:
BDD conteneur physique des collections
chaque bdd possede ses propre fichier

##### Collection :
Groupe de documents MongoDB
Les Doc au sein d'une même collection peuvent avoir des champs différents
Tous les Doc d'une même collection sont généralement similaires ou on un usage similaire

##### Doc
Un Docuument est un ensemble de données de valeur
le schema des doc est dit "dynamique"

Img: 
Database peut contenir des collection qui elle meme contien des Collections

Dessaventage TRES VITE COMPOSANT DUPLIQUER

##### MongoDB vs RDBMS
RDBMS  || MongoDB
BDD  || BDD
Table  || Collection
null  || Document
null  || Field
null  || Document imbriqués
null  || Clé primaire
null  || Mongod
null  || Mongo

##### AVANTAGE DE MONGO

pas de shema
structure claire et simple
pas de jointure complexe
plus besoin d'ORM
stockage sur disque
trés facile de "scale"
des requetes imbriquées et dynamique (presque aussi performant que SQL)

Stockage oriente Doc
MaJ simplifiées
excellent support et développement rapide
Indexage possible sur chaque attribut

##### Dans Quelle Domaine

Appli Web
Big Data
DataHub

##### notation JSON

``` json
//Object Vide
{}
```

Les propriété d'un Object JSON sont représentees pas des paires cle/valeur : 

``` json
{"cle" :"valeur", "autre_cle": ""}

{"nom" :"Nicolas", "prenom": "PEYACHON"}
//Erreur prenom -> Nom et inversement

{"nom" :"Nicolas", 
 "prenom": "PEYACHON",
 "parent": [
	 {"nom" :"Nicolas", "prenom": "PEYACHON"},
	 {"nom" :"Nicolas", "prenom": "PEYACHON"},	 
 ]
 }


```

Les types de Données

- booleen
- numeriques
- chaine de caracteres
- tableau
- object
- null (absence de valeur)

Mongo apporte ses propres types à ceux rencontrés au sein du format JSON standard :

- Date : Entier signé de 8 octets representrer le nombre de secondes depuis epoh (01/01/1970 00:00:00), le type DATE ne stocke pas les timezone
- ObjectID : stocke sur 12 octets, utilise en interne afin de garantir l'unicite des identifiants auto-generes par la BDD
- Numberlong et NumberInt : par defaut Mongo considere que toute valeur numerique est un nombre a virgule code sur 8 octets
- NumberDecimal : sur 16 octets 
- BinData : pour stocker des caracteres qu'on ne peut pas encoder en Utf-8


Creation de BDD
On peut utiliser 'use' pour se connecter a une bdd qui n'existe pas. l'insertion du premier document va entrainet la création de la bdd

``` bash
use tmp
db.useCollection.insert({"key" : "value"})
```

Vous pouvez effectuer la requete : 

``` javascript
use tmp
db.uneCollection.find()
db.uneCollection.find().pretty()
```


vous obtenez le résultat du type : 

``` javascript
{
	"id" : ObjectId("5d13Sq513vq"),
	"key" : "Value"
}
```

vous remarquez l'utilisation du mot cle 'db' il s'agit d'un mot cle qui renvoie vers la base de donnees en cours d'utilisation
la combinaison db.collection constitue ce qu'on appelle un namespace en MongoDB

Suppression d'une base de donnees


``` javascript
use maDb
db.dropDataase()
```

Pour vérifier utiliser la commande : 

``` javascript
show dbs
```

afin de lister les base de donnees.

MongoDB met a disposition des commande de type 'helper'
Ex

runCommand()
adminCommand()

collation
object avec plein d'attribue
définir un certain nombre de regle pour simplifier les chaine de caractere
ctrl + f = regle de comparaison des chaine de caractere

systeme de cle valeur dans un langage donnée

valeur par défault
valeur local
qui definit le standars? = ICU

pour creer une collection il suffit d'entrer : 

``` javascript
db.createCollection(
	"MaCollection",
	"collation" : {
		"locale": "fr",
		"caseLevel": "false",
		"strength": "3",
		"numericOrdering": "false",
		"alternate": "non-ignorable",
		"backward": "false",
		"normalization": "false",
	}
)
```

``` javascript

db.collection.updateOne(<filtre>, <Modification>)


db.personnes.updateOne(
	{
		"nom" : "PEYRACHON"
	}
	{
		$set : { "nom" : "BARRAY"}
	}
)
```

$set et un operateur


---
Les méthodes find et findOne ont la meme signature et permettent d'effectuer des requetes mongo

```javascript
db.collection.find(<filtre>, <Projection>)
```

chacun de ces parametres sont tous deux des documents


```javascript
DBQuery.shellBatchSize = 40
```

mongorc.js est un fichier config qui se trouve a la racine du répertoire utilisateur

```javascript
db.maCollection.find().limit(12)
db.maCollection.find().limit(12).pretty()
```


On retrouve d'autre operateur tels que :
- $ne : different de
- $gt : superieur a , $gte : superieur ou egal
- $lt : ... , $lte : ...
- $in et $nin : absence ou précence
- $divide : diviser
- 
on peut combiner ces operateurs pour effectuer des recherches sur des intervalles : 
```javascript
db.maCollection.find({"age" : { $lt: 50, $gt: 20}})
db.maCollection.find({"age" : { $nin: [23, 45, 78]}})
db.maCollection.find({"age" : { $exists: true}})
```


l'operateur $where fait intervenir une syntaxe particulier 
```javascript
db.personne.find({ $where: "this.nom.length > 4"})

db.personne.find({ $where: function() {
	return obj.nom.length > 4
}  })
```

