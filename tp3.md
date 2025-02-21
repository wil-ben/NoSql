# Introduction à CouchDB et MapReduce

## Introduction

**Apache CouchDB** est une base de données NoSQL orientée documents, conçue pour le stockage en JSON, l'accès via HTTP et l'utilisation de **MapReduce** pour interroger les données. Doté d'une réplication distribuée et d'une architecture flexible, il est idéal pour les applications web et mobiles.

Ce document présente les étapes essentielles pour installer CouchDB, manipuler des documents et exploiter MapReduce pour réaliser des requêtes avancées.

---

## 1. Installation et Configuration

### 1.1 Installation avec Docker (Recommandée)

```bash
docker run -d --name couchdb \
  -e COUCHDB_USER=admin \
  -e COUCHDB_PASSWORD=secret \
  -p 5984:5984 \
  couchdb:latest
```
- `COUCHDB_USER` et `COUCHDB_PASSWORD` permettent de définir un compte administrateur.
- `-p 5984:5984` expose CouchDB sur le port 5984.

### 1.2 Validation de l'installation

```bash
curl -X GET http://admin:secret@localhost:5984/
```
Une réponse contenant `{"couchdb":"Welcome", "version":"X.X.X"}` confirme que l'installation est réussie.

---

## 2. Manipulation des Données

### 2.1 Création d'une base de données

```bash
curl -X PUT http://admin:secret@localhost:5984/films
```

### 2.2 Ajout de documents

#### Insertion d'un document unique

```bash
curl -X POST http://admin:secret@localhost:5984/films \
  -H "Content-Type: application/json" \
  -d '{"title":"Inception", "year":2010}'
```

#### Insertion en lot

```bash
curl -X POST http://admin:secret@localhost:5984/films/_bulk_docs \
  -H "Content-Type: application/json" \
  -d @films.json
```

### 2.3 Consultation et mise à jour de documents

#### Lecture d'un document

```bash
curl -X GET http://admin:secret@localhost:5984/films/doc_id
```

#### Modification d'un document existant

```bash
curl -X PUT http://admin:secret@localhost:5984/films/doc_id \
  -H "Content-Type: application/json" \
  -d '{"_rev":"1-xxx", "title":"Interstellar", "year":2014}'
```

---

## 3. Utilisation de MapReduce

### 3.1 Nombre de films par année

#### Fonction Map

```javascript
function (doc) {
  if (doc.year && doc.title) {
    emit(doc.year, 1);
  }
}
```

#### Fonction Reduce

```javascript
function (keys, values, rereduce) {
  return sum(values);
}
```

### 3.2 Films par acteur

#### Fonction Map

```javascript
function (doc) {
  if (doc.actors) {
    doc.actors.forEach(actor => {
      emit(actor.name, doc.title);
    });
  }
}
```

#### Fonction Reduce

```javascript
function (keys, values) {
  return { count: values.length, films: values };
}
```

### 3.3 Films par réalisateur

#### Fonction Map

```javascript
function (doc) {
  if (doc.director) {
    emit(doc.director, doc.title);
  }
}
```

#### Fonction Reduce

```javascript
function (keys, values) {
  return values.length;
}
```

---

## 4. Calcul Matriciel avec MapReduce

### 4.1 Modélisation des documents

Chaque ligne de la matrice est enregistrée sous forme de document JSON :

```json
{
  "page": "P1",
  "liens": [
    {"vers": "P2", "poids": 0.3},
    {"vers": "P3", "poids": 0.7}
  ]
}
```

### 4.2 Calcul de la norme des vecteurs

#### Fonction Map

```javascript
function (doc) {
  var somme = 0;
  doc.liens.forEach(function (lien) {
    somme += Math.pow(lien.poids, 2);
  });
  emit(doc.page, Math.sqrt(somme));
}
```

#### Fonction Reduce

```javascript
function (keys, values, rereduce) {
  return values;
}
```

### 4.3 Produit Matrice-Vecteur

#### Fonction Map

```javascript
function (doc) {
  doc.liens.forEach(function (lien) {
    emit(lien.vers, lien.poids * W[lien.vers]);
  });
}
```

#### Fonction Reduce

```javascript
function (keys, values, rereduce) {
  return values.reduce(function (a, b) { return a + b; }, 0);
}
```

---

## 5. Conclusion

CouchDB se distingue par :
- Son stockage **JSON natif**
- Son interface **RESTful**
- Son mécanisme de requêtes **MapReduce**
- Sa **réplication distribuée**

L'utilisation de **MapReduce** permet des agrégations puissantes et efficaces, en particulier pour les applications web et mobiles nécessitant une synchronisation répartie des données.


