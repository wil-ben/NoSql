# README - Commandes MongoDB pour la base de données `lesfilms`

Ce document regroupe les principales commandes MongoDB pour interagir avec la base de données `lesfilms`, qui contient une collection de films. Chaque commande est expliquée en détail.

---

## 1. Installer MongoDB sur WSL
Pour installer MongoDB sur WSL (Windows Subsystem for Linux), exécutez les commandes suivantes :
```bash
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt update
sudo apt install -y mongodb-org
```

---

## 2. Démarrer MongoDB sur WSL
Avant d'exécuter les commandes MongoDB, assurez-vous que le service MongoDB est démarré :
```bash
sudo systemctl start mongod
```
Si vous souhaitez que MongoDB démarre automatiquement avec votre session WSL :
```bash
sudo systemctl enable mongod
```

---

## 3. Importer une collection de films
Pour importer une collection de films à partir d'un fichier JSON :
```bash
mongoimport --db lesfilms --collection films --file films.json --jsonArray
```

---

## 4. Vérifier que les données ont été importées
Pour compter le nombre de documents dans la collection `films` :
```bash
db.films.countDocuments()
```

---

## 5. Afficher un seul document de la collection
Pour comprendre la structure des données, on peut afficher un document au hasard :
```bash
db.films.findOne()
```

---

## 6. Afficher tous les films d'action
Pour obtenir tous les films dont le genre est "Action" :
```bash
db.films.find({ genre: "Action" })
```

---

## 7. Compter le nombre de films d'action
```bash
db.films.countDocuments({ genre: "Action" })
```

---

## 8. Afficher les films d'action produits en France
```bash
db.films.find({ genre: "Action", country: "FR" })
```

---

## 9. Afficher les films d'action produits en France en 1963
```bash
db.films.find({ genre: "Action", country: "FR", year: 1963 })
```

---

## 10. Afficher les titres et les grades des films d'action réalisés en France sans leurs identifiants
```bash
db.films.find(
  { genre: "Action", country: "FR" },
  { _id: 0, title: 1, grades: 1 }
)
```

---

## 11. Afficher les films d'action réalisés en France ayant une note supérieure à 10
```bash
db.films.find(
  { genre: "Action", country: "FR", "grades.note": { $gt: 10 } },
  { _id: 0, title: 1, grades: 1 }
)
```
> Remarque : Cette requête retourne des films contenant **au moins** une note > 10, mais pas forcément que des notes supérieures à 10.

---

## 12. Afficher les films d'action réalisés en France ayant **uniquement** des notes supérieures à 10
```bash
db.films.find(
  { genre: "Action", country: "FR", grades: { $not: { $elemMatch: { note: { $lte: 10 } } } } },
  { _id: 0, title: 1, grades: 1 }
)
```

---

## 13. Afficher les différents genres de la base `lesfilms`
```bash
db.films.distinct("genre")
```

---

## 14. Afficher les différents grades attribués
```bash
db.films.distinct("grades.grade")
```

---

## 15. Afficher tous les films avec au moins un des artistes suivants : `artist:4`, `artist:18`, `artist:11`
```bash
db.films.find(
  { "actors": { $in: ["artist:4", "artist:18", "artist:11"] } },
  { _id: 0, title: 1, actors: 1 }
)
```

---

## 16. Afficher tous les films qui **n'ont pas** de résumé
```bash
db.films.find(
  { "resume": { $exists: false } },
  { _id: 0, title: 1 }
)
```

---

## 17. Afficher tous les films avec Leonardo DiCaprio en 1997
```bash
db.films.find(
  { "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo", year: 1997 },
  { _id: 0, title: 1, actors: 1, year: 1 }
)
```

---

## Conclusion
Ce fichier regroupe les principales requêtes pour manipuler la base `lesfilms` et effectuer des recherches précises. Vous pouvez les utiliser et les adapter selon vos besoins.

