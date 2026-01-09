# P-DB_106  
## Rapport de projet – Thanos Pizza  

### Création du MCD / MLD

## Première version (avant obtention des données)

### MCD  
<img width="1102" height="595" alt="MCD_V1" src="https://github.com/user-attachments/assets/b34978ae-49c5-4a10-9c02-3eb4a94ddedb" />

### MLD  
<img width="1038" height="613" alt="MLD_V1" src="https://github.com/user-attachments/assets/320514e1-923d-4c7b-afde-42dfebf48d9a" />

## Version finale

### MCD  
<img width="1105" height="713" alt="MCD_V2png" src="https://github.com/user-attachments/assets/95136f6b-f0cb-4ec9-9a6e-d102dcca0b57" />

### MLD  
<img width="1111" height="698" alt="MLD_V2" src="https://github.com/user-attachments/assets/38d02521-f3cb-4084-bee0-5d4ef906683e" />


## Changements notables

- **Paiement enrichi** : ajout des attributs `date_paiement` et `montant`, et renommage de `mode` en `mode_paiement`.
- **Commande simplifiée** : suppression de l’attribut `montant`, désormais géré dans la table `paiement`, et renommage des clés étrangères pour une meilleure cohérence (`nom_table_fk`).
- **Relations clarifiées** : une commande contient une ou plusieurs lignes de commande, et un produit est toujours associé via une ligne de commande.
- **Refonte de l’association `composer`** : déplacement de l’association récursive de `produit` vers `ligne_commande`.
- **Livreur amélioré** : ajout de l’attribut `actif` pour indiquer la disponibilité des livreurs.

---

## Mise en place d'un environement docker pour mysql

- Dans le docker-commpose j'ai ajouté cette ligne au préalable : command: --secure-file-priv=/scripts .
- Telecharger Docker_MySQL.zip et dezipper
- ouvrir le cmd dans de le dossier Docker_MySQL
- Faire docker-compose up -d
- Sur un navigateur tapez "http://localhost:8081" puis connecter vous en root root
- (Si il y a un probleme d'insertion apres cela, redemarrer docker)

---

## Création de la base de données 
```sql
CREATE DATABASE IF NOT EXISTS db_pizzeria
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

```

## Création des tables 
```sql
USE db_pizzeria;
```
```sql
CREATE TABLE t_client(
   client_id INT AUTO_INCREMENT,
   nom VARCHAR(50)  NOT NULL,
   prenom VARCHAR(50)  NOT NULL,
   couriel VARCHAR(100)  NOT NULL,
   telephone VARCHAR(50)  NOT NULL,
   PRIMARY KEY(client_id),
   UNIQUE(telephone)
);

CREATE TABLE t_adresse(
   adresse_id INT AUTO_INCREMENT,
   rue VARCHAR(100)  NOT NULL,
   npa VARCHAR(10)  NOT NULL,
   localite VARCHAR(50)  NOT NULL,
   latitude VARCHAR(50) ,
   longitude VARCHAR(50) ,
   PRIMARY KEY(adresse_id)
);

CREATE TABLE t_produit(
   produit_id INT AUTO_INCREMENT,
   type VARCHAR(50)  NOT NULL,
   nom VARCHAR(50)  NOT NULL,
   prix_ttc DECIMAL(10,2)   NOT NULL,
   tva DECIMAL(5,2)   NOT NULL,
   etat BOOLEAN NOT NULL,
   PRIMARY KEY(produit_id)
);

CREATE TABLE t_commande(
   commande_id INT AUTO_INCREMENT,
   date_heure DATETIME NOT NULL,
   type_commande VARCHAR(50)  NOT NULL,
   statut VARCHAR(50)  NOT NULL,
   adresse_relier_fk INT,
   client_passer_fk INT NOT NULL,
   PRIMARY KEY(commande_id),
   FOREIGN KEY(adresse_relier_fk) REFERENCES t_adresse(adresse_id),
   FOREIGN KEY(client_passer_fk) REFERENCES t_client(client_id)
);

CREATE TABLE t_paiement(
   paiement_id INT AUTO_INCREMENT,
   mode_paiement VARCHAR(50)  NOT NULL,
   date_paiement DATETIME NOT NULL,
   montant VARCHAR(50)  NOT NULL,
   commande_associer_a_fk INT NOT NULL,
   PRIMARY KEY(paiement_id),
   FOREIGN KEY(commande_associer_a_fk) REFERENCES t_commande(commande_id)
);

CREATE TABLE t_livreur(
   livreur_id INT AUTO_INCREMENT,
   nom VARCHAR(50)  NOT NULL,
   actif BOOLEAN NOT NULL,
   PRIMARY KEY(livreur_id)
);

CREATE TABLE t_ligne_commande(
   ligne_commande_id VARCHAR(50) ,
   quantite VARCHAR(50)  NOT NULL,
   prix_unitaire VARCHAR(50)  NOT NULL,
   commande_composer_ligne_fk VARCHAR(50) ,
   produit_fk INT NOT NULL,
   commande_contenir_fk INT NOT NULL,
   PRIMARY KEY(ligne_commande_id),
   FOREIGN KEY(commande_composer_ligne_fk) REFERENCES t_ligne_commande(ligne_commande_id),
   FOREIGN KEY(produit_fk) REFERENCES t_produit(produit_id),
   FOREIGN KEY(commande_contenir_fk) REFERENCES t_commande(commande_id)
);

CREATE TABLE t_livraison(
   livraison_id INT AUTO_INCREMENT,
   date_depart DATETIME NOT NULL,
   date_arrivee DATETIME NOT NULL,
   distance_estimee VARCHAR(50) ,
   commande_affecter_fk INT NOT NULL,
   PRIMARY KEY(livraison_id),
   UNIQUE(commande_affecter_fk),
   FOREIGN KEY(commande_affecter_fk) REFERENCES t_commande(commande_id)
);

CREATE TABLE t_disposer(
   client_fk INT,
   addresse_disposer_fk INT,
   PRIMARY KEY(client_fk, addresse_disposer_fk),
   FOREIGN KEY(client_fk) REFERENCES t_client(client_id),
   FOREIGN KEY(addresse_disposer_fk) REFERENCES t_adresse(adresse_id)
);

CREATE TABLE t_effectuer(
   livreur_effectuer_fk INT,
   livraison_effectuer_fk INT,
   PRIMARY KEY(livreur_effectuer_fk, livraison_effectuer_fk),
   FOREIGN KEY(livreur_effectuer_fk) REFERENCES t_livreur(livreur_id),
   FOREIGN KEY(livraison_effectuer_fk) REFERENCES t_livraison(livraison_id)
);

```
---

## Ajout de donnée 

- Mettre tout ses tsv dans le dossier scripts au préalable


Ajout table t_adresse
```sql
LOAD DATA INFILE
'/scripts/t_adresse.tsv'
INTO TABLE t_adresse
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

Ajout table t_client
```sql
LOAD DATA INFILE
'/scripts/t_client.tsv'
INTO TABLE t_client
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

Ajout table t_disposer
```sql
LOAD DATA INFILE '/scripts/t_disposer.tsv'
INTO TABLE t_disposer
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(addresse_disposer_fk, client_fk);
```

Ajout table t_livreur
```sql
LOAD DATA INFILE
'/scripts/t_livreur.tsv'
INTO TABLE t_livreur
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

Ajout table t_commande
```sql
LOAD DATA INFILE
'/scripts/t_commande.tsv'
INTO TABLE t_commande
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(
  commande_id,
  client_passer_fk,
  type_commande,
  @adresse,
  @date_txt,
  statut
)
SET
  adresse_relier_fk = NULLIF(@adresse, ''),
  date_heure = STR_TO_DATE(@date_txt, '%d.%m.%Y %H:%i');
```

Ajout table t_livraison
```sql
LOAD DATA INFILE '/scripts/t_livraison.tsv'
INTO TABLE t_livraison
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(
  livraison_id,
  @date_depart,
  @date_arrivee,
  @distance_estimee,
  commande_affecter_fk
)
SET
  date_depart = STR_TO_DATE(@date_depart, '%d.%m.%Y %H:%i'),
  date_arrivee = STR_TO_DATE(@date_arrivee, '%d.%m.%Y %H:%i'),
  distance_estimee = NULLIF(@distance_estimee, '');
```

Ajout table t_effectuer
```sql
LOAD DATA INFILE
'/scripts/t_produit.tsv'
INTO TABLE t_produit
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

Ajout table t_produit 
```sql
LOAD DATA INFILE
'/scripts/t_produit.tsv'
INTO TABLE t_produit
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

Ajout table t_paiement
```sql
LOAD DATA INFILE '/scripts/t_paiement.tsv'
INTO TABLE t_paiement
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(
  paiement_id,
  commande_associer_a_fk,
  mode_paiement,
  montant,
  @date_paiement)
SET
  date_paiement = STR_TO_DATE(@date_paiement, '%d.%m.%Y %H:%i');

```

Ajout table t_ligne_commande
```sql
LOAD DATA INFILE '/scripts/t_ligne_commande.tsv'
INTO TABLE t_ligne_commande
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(
  ligne_commande_id,
  quantite,
  prix_unitaire,
  @commande_passer,
  produit_fk,
  commande_contenir_fk
)
SET
  commande_composer_ligne_fk = NULLIF(@commande_passer, '');
```
---

## Mettre en place une stratégie de sauvegardes et de restauration

Ajout de du champs "last_modified" qui enregistrera la date et heure du dernier changement / ajout 


```sql
ALTER TABLE t_client
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_adresse
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_produit
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_commande
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_paiement
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_livreur
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_ligne_commande
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_livraison
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_disposer
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;

ALTER TABLE t_effectuer
ADD COLUMN last_modified DATETIME
DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP;
```

Savegarde complete (execution dans le cmd a ouvrir depuis docker)
```
mysqldump -u root -p db_pizzeria > /scripts/backups/full/full_db_pizzeria_$(date +%F).sql
```

Savegarde différentiel (execution dans le cmd a ouvrir depuis docker)

La date dans la commande correspond a la date de la derniere backup effectué (elle est donc a changer chaque jour)
commande a faire pour chaque table (modifier le nom de la table dans la commande pour chaque table)

```
mysqldump -u root -p db_pizzeria t_client --where="last_modified >= '2026-01-07 00:00:00'" > /scripts/backups/différentiel/diff_t_client_diff_$(date +%F).sql
```
Scénarios
restauration 
## Requêtes SQL

Requête n°1 : 

Afficher les dix pizzas les plus vendues (sans les toppings), triés par quantités totales décroissantes. 
Vous devez afficher le nom et les quantités.



Requête n°2 : 

Afficher les toppings les plus ajoutés. Le résultat doit être ordonné par le nombre de toppings de manière décroissante.
Vous devez afficher le nom et le nombre.


Requête n°3 : 

Afficher le chiffre d’affaires par jour (commandes livrées). 
Vous ne devez afficher que la date et le chiffres d’affaires (arrondi à 2 chiffres après la virgule).


Requête n°4 : 

Afficher le chiffre d’affaires par NPA (adresse de livraison). 

1ère colonne : npa
2ème colonne : localité
3ème colonne : chiffre d’affaires (arrondi à 2 chiffres après la virgule)


Requête n°5 : 

Affiche le nombre de commandes par heure. Il s’agit par cette requête de savoir quelles sont les heures « chaudes ».
NB : les heures « chaudes » sont des heures pendant lesquelles le nombre de commandes sont les plus élevées.


Requête n°6: 

Afficher le nombre de commandes des clients les plus fidèles. Un client est fidèle si son nombre de commandes est ≥ 5 . Afficher le résultat par ordre décroissant du nombre de commandes, puis par ordre alphabétique du nom.


Requête n°7: 

Afficher le total dû par commande. Afficher l’id de la commande et le montant dû (arrondi à 2 chiffres après la virgule). Ordonnez le résultat par ordre croissant des ids de commandes.


Requête n°8:

Afficher le total payé par commande (commande ayant au moins un paiement). Afficher l’id de la commande et le total payé (arrondi à 2 chiffres après la virgule). Ordonnez le résultat par ordre croissant des ids de commandes.


Requête n°9: 

Quelle est la répartition des types de commandes.
Ordonner le résultat par le nombre de commande de chaque type, du plus grand au plus petit.

1ère colonne : type
2ème colonne : nombre de commandes de ce type


Requête n°10:

Quel est le délai moyen de livraison par livreur (en minutes).
Ordonner le résultat par délai moyen en minutes du plus petit au plus grand.
Aide : l’id du livreur, son nom et le délai dans le SELECT.








