# Introduction à CouchDB et MapReduce

## Introduction

**Apache CouchDB** est une base de données NoSQL orientée documents qui utilise JSON, une API REST et MapReduce pour interroger les données. Sa capacité de réplication distribuée en fait un choix idéal pour les applications web et mobiles.

Ce rapport présente l'installation de CouchDB, la manipulation de documents et l'utilisation de MapReduce pour des traitements avancés.

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
- `COUCHDB_USER` et `COUCHDB_PASSWORD` définissent un administrateur.
- `-p 5984:5984` expose CouchDB sur le port 5984.

### 1.2 Validation de l'installation

```bash
curl -X GET http://admin:secret@localhost:5984/
```
Une réponse contenant `{"couchdb":"Welcome", "version":"X.X.X"}` confirme le bon fonctionnement.

---

## 2. Manipulation des Données

### 2.1 Création d'une base de données

```bash
curl -X PUT http://admin:secret@localhost:5984/films
```

### 2.2 Ajout de documents

#### Document unique

```bash
curl -X POST http://admin:secret@localhost:5984/films \
  -H "Content-Type: application/json" \
  -d '{"title":"Inception", "year":2010}'
```

#### Ajout en masse

```bash
curl -X POST http://admin:secret@localhost:5984/films/_bulk_docs \
  -H "Content-Type: application/json" \
  -d @films.json
```

### 2.3 Consultation et mise à jour

#### Lecture

```bash
curl -X GET http://admin:secret@localhost:5984/films/doc_id
```

#### Modification

```bash
curl -X PUT http://admin:secret@localhost:5984/films/doc_id \
  -H "Content-Type: application/json" \
  -d '{"_rev":"1-xxx", "title":"Interstellar", "year":2014}'
```

---

## 3. Utilisation de MapReduce

### 3.1 Nombre de films par année

#### Map

```javascript
function (doc) {
  if (doc.year && doc.title) {
    emit(doc.year, 1);
  }
}
```

#### Reduce

```javascript
function (keys, values, rereduce) {
  return sum(values);
}
```

### 3.2 Films par acteur

#### Map

```javascript
function (doc) {
  if (doc.actors) {
    doc.actors.forEach(actor => {
      emit(actor.name, doc.title);
    });
  }
}
```

#### Reduce

```javascript
function (keys, values) {
  return { count: values.length, films: values };
}
```

### 3.3 Films par réalisateur

#### Map

```javascript
function (doc) {
  if (doc.director) {
    emit(doc.director, doc.title);
  }
}
```

#### Reduce

```javascript
function (keys, values) {
  return values.length;
}
```

---

## 4. Calcul Matriciel avec MapReduce

### 4.1 Modélisation des documents

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

#### Map

```javascript
function (doc) {
  var somme = 0;
  doc.liens.forEach(function (lien) {
    somme += Math.pow(lien.poids, 2);
  });
  emit(doc.page, Math.sqrt(somme));
}
```

#### Reduce

```javascript
function (keys, values, rereduce) {
  return values;
}
```

### 4.3 Produit Matrice-Vecteur

#### Map

```javascript
function (doc) {
  doc.liens.forEach(function (lien) {
    emit(lien.vers, lien.poids * W[lien.vers]);
  });
}
```

#### Reduce

```javascript
function (keys, values, rereduce) {
  return values.reduce(function (a, b) { return a + b; }, 0);
}
```

---

## 5. Conclusion

CouchDB est une base NoSQL performante pour la gestion de données JSON avec une API REST et MapReduce. Son architecture réplicable et distribuée en fait un excellent choix pour les applications modernes.

