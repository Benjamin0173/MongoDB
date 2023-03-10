Créez une base de données sample nommée "sample_db" et une collection appelée "employees".
Insérez les documents suivants dans la collection "employees":
```JSON
{
   name: "John Doe",
   age: 35,
   job: "Manager",
   salary: 80000
}
{
   name: "Jane Doe",
   age: 32,
   job: "Developer",
   salary: 75000
}
{
   name: "Jim Smith",
   age: 40,
   job: "Manager",
   salary: 85000
}
```
---
##### Écrivez une requête MongoDB pour trouver tous les documents dans la collection "employees".

```JSON
db.employees.find({})
```

![[Pasted image 20230131122019.png]]


---
##### Écrivez une requête pour trouver tous les documents où l'âge est supérieur à 33.

```JSON
db.employees.find({"age": {$gt: 33}})
```

![[Pasted image 20230131122316.png]]

---
##### Écrivez une requête pour trier les documents dans la collection "employees" par salaire décroissant.

```JSON
db.employees.find().sort({"salary": -1})
```

![[Pasted image 20230131122448.png]]

---
##### Écrivez une requête pour sélectionner uniquement le nom et le job de chaque document.

```JSON
db.employees.find({}, {"name": 1, "job": 1, "_id": 0})
```

![[Pasted image 20230131122526.png]]

---
##### Écrivez une requête pour compter le nombre d'employés par poste.

```JSON
db.employees.aggregate([{$group: {_id: "$job", count: {$sum: 1}}}])
```


![[Pasted image 20230131122753.png]]

---
##### Écrivez une requête pour mettre à jour le salaire de tous les développeurs à 80000.

![[Pasted image 20230131122825.png]]

```JSON
db.employees.updateMany({"job" : "Developper"}, {$set: {"salary": 80000}})
```


![[Pasted image 20230131122859.png]]
![[Pasted image 20230131122915.png]]

