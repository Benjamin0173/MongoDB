Exercice 1 
Modifiez la collection salle afin que soient dorénavant validés les documents destinés à y être insérés ; cette validation aura lieu en mode « strict » et portera sur les champs suivants :

nom sera obligatoire et devra être de type chaîne de caractères.

capacite sera obligatoire et devra être de type entier (int).

Dans le champ adresse, les champs codePostal et ville, tous deux de type chaîne de caractères, seront obligatoires.

![[Pasted image 20230131151703.png]]

Que constatez-vous lors de la tentative d’insertion suivante, et quelle en est la cause ?

```
db.salles.insertOne( 
{"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville"}} 
) 
```
![[Pasted image 20230131151820.png]]



Que proposez-vous pour régulariser la situation ?

![[Pasted image 20230131151703.png]]

Exercice 2

Rajoutez à vos critères de validation existants un critère supplémentaire : le champ _id devra dorénavant être de type entier (int) ou ObjectId.

![[Pasted image 20230201102619.png]]


Que se passe-t-il si vous tentez de mettre à jour l’ensemble des documents existants dans la collection à l’aide de la requête suivante :

```
db.salles.updateMany({}, {$set: {"verifie": true}}) 
```

Supprimez les critères rajoutés à l’aide de la méthode delete en JavaScript


Exercice 3

Rajoutez aux critères de validation existants le critère suivant :

Le champ smac doit être présent OU les styles musicaux doivent figurer parmi les suivants : "jazz", "soul", "funk" et "blues".

Que se passe-t-il lorsque nous exécutons la mise à jour suivante ?

```
db.salles.update({"_id": 3}, {$set: {"verifie": false}})
```
