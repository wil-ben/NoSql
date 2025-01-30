# Redis - Guide Pratique

## Introduction
Redis (Remote Dictionary Server) est un système de stockage de données open-source, in-memory, utilisable comme base de données, cache et message broker.

## Installation et Lancement de Redis
### Installation sur Linux (via WSL pour Windows)
Pour utiliser Redis sur un système Windows, il est nécessaire d'installer **WSL (Windows Subsystem for Linux)** afin de pouvoir exécuter un environnement Linux. Ensuite, Redis peut être installé avec les commandes suivantes :

```bash
sudo apt-get update
sudo apt-get install redis
```

Après l'installation, Redis peut rencontrer un problème de port en cours d'exécution. Il peut être nécessaire de modifier la configuration du port avant de pouvoir le lancer correctement.

### Démarrer Redis
- **Lancer le serveur Redis**
  ```bash
  redis-server
  ```
- **Ouvrir l'interface de commande Redis**
  ```bash
  redis-cli
  ```

---

## 1. Clés et Valeurs
Redis fonctionne principalement avec des paires clé-valeur.

### Définir et Récupérer une Valeur
```bash
SET utilisateur "Alice"
GET utilisateur   # Retourne "Alice"
```

### Incrémentation d'une Valeur Numérique
```bash
SET compteur 5
INCR compteur   # Incrémente la valeur, devient 6
```

### Expiration et Suppression d'une Clé
```bash
EXPIRE utilisateur 60   # Définit une expiration de 60 secondes
TTL utilisateur         # Vérifie le temps restant avant expiration
DEL utilisateur         # Supprime la clé immédiatement
```

---

## 2. Listes (List)
Une liste Redis est une collection ordonnée d'éléments.

### Manipulation des Listes
```bash
RPUSH fruits "pomme"   # Ajoute à la fin
RPUSH fruits "banane"
LPUSH fruits "orange"  # Ajoute au début

LRANGE fruits 0 -1      # Affiche tous les éléments (orange, pomme, banane)
LPOP fruits             # Supprime le premier de la liste et le retourne (ici c'est orange)
RPOP fruits             # Supprime le dernier de la liste et le retourne (ici c'est banane)
```

---

## 3. Ensembles (Sets)
Un ensemble dans Redis est une collection de valeurs uniques et non ordonnées.

### Caractéristiques des Sets :
- **Unicité** : Un élément ne peut exister qu'une seule fois.
- **Non ordonné** : Pas de tri spécifique.
- **Opérations rapides** : Ajout, suppression et recherche efficaces.

### Manipulation des Ensembles
```bash
SADD langages "Python"
SADD langages "Java"
SADD langages "Python"   # Pas de doublon

SMEMBERS langages        # Affiche Python, Java (Le doublon Python n'a donc pas été pris en compte)
SREM langages "Java"     # Supprime "Java"
```

### Opérations sur les Ensembles
```bash
SADD setA "a" "b" "c"
SADD setB "b" "c" "d"
SUNION setA setB         # Retourne "a", "b", "c", "d"
```
On souligne que **`SUNION`** ne prend pas en compte les doublons lors de la fusion

---

## 4. Sets Triés (Sorted Sets)
Les sets triés sont similaires aux sets, mais chaque élément est associé à un score permettant un tri automatique.

### Caractéristiques des Sets Triés :
- **Éléments uniques** comme un set normal.
- **Tri automatique** basé sur le score.
- **Accès rapide** aux éléments les mieux classés.

### Manipulation des Sets Triés
```bash
ZADD scores 100 "Alice"
ZADD scores 85 "Bob"
ZADD scores 95 "Charlie"

ZRANGE scores 0 -1      # Affiche en ordre croissant : Bob, Charlie, Alice
ZREVRANGE scores 0 -1   # Affiche en ordre décroissant : Alice, Charlie, Bob
ZRANK scores "Bob"      # Position de Bob : 1
```

---

## 5. Hachages (Hashes)
Les hachages sont des collections clé-valeur similaires aux objets ou dictionnaires.

### Manipulation des Hachages
```bash
HSET utilisateur:1 nom "Jean"
HSET utilisateur:1 age 30
HSET utilisateur:1 ville "Paris"

HGET utilisateur:1 nom        # Retourne "Jean"
HGETALL utilisateur:1         # Retourne tous les champs et valeurs
HINCRBY utilisateur:1 age 5   # Incrémente l'âge, devient 35
HVALS utilisateur:1           # Retourne la liste des valeurs
```

---

### Publication/Abonnement (Pub/Sub)
Redis permet la communication en temps réel via des canaux de publication et d'abonnement.

#### 1. `SUBSCRIBE` et `PSUBSCRIBE`
- **`SUBSCRIBE` :** S'abonne à un canal spécifique.
- **`PSUBSCRIBE` :** S'abonne avec un motif.

#### 2. `PUBLISH`
**Description :** Publie un message sur un canal.

**Exemple :**
```bash
PUBLISH mychannel "Hello, subscribers!"
```

---

## 6. Autres Fonctionnalités Utiles

### 1. Transactions (`MULTI` / `EXEC`)
Redis permet d'exécuter plusieurs commandes en une seule transaction atomique.
```bash
MULTI
SET compte 100
INCRBY compte 50
EXEC  # Applique les changements
```

### 2. Persistance des Données (`SAVE` / `BGSAVE`)
Redis peut sauvegarder les données sur disque.
```bash
SAVE    # Sauvegarde synchrone
BGSAVE  # Sauvegarde asynchrone en arrière-plan
```

### 3. Verrouillage avec `SETNX`
Empêche la modification simultanée d'une clé.
```bash
SETNX verrou "actif"  # Définit la clé uniquement si elle n'existe pas
```

### 4. Expiration Avancée avec `PEXPIRE`
Définit un temps d'expiration en millisecondes.
```bash
PEXPIRE session 5000  # Expiration dans 5000 ms (5 sec)
```

---

## Conclusion
Redis offre différentes structures de données adaptées à divers besoins, de la simple clé-valeur aux ensembles triés. Ce guide couvre les bases pour bien démarrer avec Redis !

