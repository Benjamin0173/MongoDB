Jeu de données: Téléchargez ou générez un jeu de données de stations météorologiques, qui incluent la date, la température, la pression atmosphérique, etc.

DataSet Utiliser:
https://www.kaggle.com/datasets/mnassrib/jena-weather-dataset
~894k Documents


#### Préparation des données:
##### a. Importez les données de stations météorologiques dans MongoDB en utilisant la commande mongoimport.

![[Pasted image 20230201120831.png]]
![[Pasted image 20230201123110.png]]





##### b. Assurez-vous que les données sont bien structurées et propres pour une utilisation ultérieure.
![[Pasted image 20230201133133.png]]




----
---
#### Indexation avec MongoDB:
##### a. Créez un index sur le champ de la date pour améliorer les performances de la recherche. Utilisez la méthode createIndex ().

```JSON
db.Weather.createIndex({"Date Time" : 1})
```

![[Pasted image 20230201133520.png]]

##### b. Vérifiez que l'index a été créé en utilisant la méthode listIndexes ().

```JSON
db.Weather.getIndexes
```

![[Pasted image 20230201133539.png]]

---
---
#### Requêtes MongoDB:
##### a. Recherchez les stations météorologiques qui ont enregistré une température supérieure à 25°C pendant les mois d'été (juin à août). Utilisez la méthode find () et les opérateurs de comparaison pour trouver les documents qui correspondent à vos critères.


 ```Javascript
db.weather_advanced.find({
     $and: [
        { "T (degC)": { $gt: 25 } },
        ({
           "Date Time": { 
           $gte: new Date("2004-06-01T00:00:00.000Z"),
            $lt: new Date("2004-09-31T23:59:59.999Z") 
            }
        })
    ]
})
```
![[Pasted image 20230201170358.png]]



##### b. Triez les stations météorologiques par pression atmosphérique, du plus élevé au plus bas. Utilisez la méthode sort () pour trier les résultats.
![[Pasted image 20230201143401.png]]
J'ai du utiliser limit(10) car il ne PEUT PAS afficher 894k Doc
![[Pasted image 20230201143455.png]]



#### Framework d'agrégation:
##### a. Calculez la température moyenne par station météorologique pour chaque mois de l'année. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et grouper les données par mois.

``` JSON
db.weather_advanced.aggregate([
	{ $group: {
		_id: {
			year: { $year: "$Date Time" },
			month: { $month: "$Date Time" },
			station: "$Station" },
			avgTemperature: { $avg: "$T (degC)" }
		}
	},
	{
	$sort: { "_id.year": 1, "_id.month": 1 }
	}
])
```
![[Pasted image 20230201170952.png]]


##### b. Trouvez la station météorologique qui a enregistré la plus haute température en été. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et trouver la valeur maximale.

```JSON
db.weather_advanced.aggregate([
	{ 
		$match: {
			"Date Time": {
			$gte: ISODate("2004-06-01T00:00:00.000Z"),
			$lt: ISODate("2004-09-01T00:00:00.000Z")
			}
		}
	},{
	$group: { _id: null, maxTemperature: { $max: "$T (degC)" }
	}
 }])
```
![[Pasted image 20230201171236.png]]


---
#### Export de la base de données:
##### a. Exportez les résultats des requêtes dans un fichier CSV pour un usage ultérieur. Utilisez la commande mongoexport pour exporter des données de MongoDB.

![[Pasted image 20230202113352.png]]
![[Pasted image 20230202113406.png]]
![[Pasted image 20230202113453.png]]


![[Pasted image 20230202115320.png]]