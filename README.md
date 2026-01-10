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
'/scripts/t_effectuer.tsv'
INTO TABLE t_effectuer
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

Ajout du champs "last_modified" a toute les tables qui enregistrera la date et heure du dernier changement / ajout 


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

### Savegarde complete (execution dans le cmd a ouvrir depuis docker)
```
mysqldump -u root -p db_pizzeria > /scripts/backups/full/full_db_pizzeria_$(date +%F).sql
```

### Savegarde différentiel (execution dans le cmd a ouvrir depuis docker)

- La date dans la commande correspond a la date de la derniere backup effectué (elle est donc a changer chaque jour)
- commande a faire pour chaque table (modifier le nom de la table dans la commande pour chaque table)

```
mysqldump -u root -p db_pizzeria t_client --where="last_modified >= '2026-01-07 00:00:00'" > /scripts/backups/différentiel/diff_t_client_diff_$(date +%F).sql
```
Scénarios
restauration 

---
## Requêtes SQL

Requête n°1 : 

- Afficher les dix pizzas les plus vendues (sans les toppings), triés par quantités totales décroissantes. 
- Vous devez afficher le nom et les quantités.
```sql
SELECT 
    p.nom AS "Nom de la pizza",
    SUM(lc.quantite) AS "Quantité totale"
FROM t_ligne_commande lc
JOIN t_produit p ON lc.produit_fk = p.produit_id
WHERE p.type = 'Pizza'
GROUP BY p.nom
ORDER BY SUM(lc.quantite) DESC
LIMIT 10;
```


Requête n°2 : 

- Afficher les toppings les plus ajoutés. Le résultat doit être ordonné par le nombre de toppings de manière décroissante.
- Vous devez afficher le nom et le nombre.

```sql
SELECT 
    p.nom AS "Nom du topping",
    SUM(lc.quantite) AS "Nombre ajouté"
FROM t_ligne_commande lc
JOIN t_produit p ON lc.produit_fk = p.produit_id
WHERE p.type = 'Topping'
GROUP BY p.nom
ORDER BY SUM(lc.quantite) DESC;
```
Requête n°3 : 

- Afficher le chiffre d’affaires par jour (commandes livrées).
- Vous ne devez afficher que la date et le chiffres d’affaires (arrondi à 2 chiffres après la virgule).
```sql
SELECT 
    DATE(c.date_heure) AS "Date",
    ROUND(SUM(lc.quantite * lc.prix_unitaire), 2) AS "Chiffre d'affaires"
FROM t_commande c
JOIN t_ligne_commande lc ON lc.commande_contenir_fk = c.commande_id
JOIN t_livraison l ON l.commande_affecter_fk = c.commande_id
GROUP BY DATE(c.date_heure)
ORDER BY DATE(c.date_heure);
```

Requête n°4 : 

- Afficher le chiffre d’affaires par NPA (adresse de livraison). 

- 1ère colonne : npa
- 2ème colonne : localité
- 3ème colonne : chiffre d’affaires (arrondi à 2 chiffres après la virgule)

```sql
SELECT 
    a.npa AS "NPA",
    a.localite AS "Localité",
    ROUND(SUM(lc.quantite * lc.prix_unitaire), 2) AS "Chiffre d'affaires"
FROM t_commande c
JOIN t_ligne_commande lc ON lc.commande_contenir_fk = c.commande_id
JOIN t_livraison l ON l.commande_affecter_fk = c.commande_id
JOIN t_adresse a ON c.adresse_relier_fk = a.adresse_id
GROUP BY a.npa, a.localite
ORDER BY "Chiffre d'affaires" DESC;
```

Requête n°5 : 

- Affiche le nombre de commandes par heure. Il s’agit par cette requête de savoir quelles sont les heures « chaudes ».

```sql
SELECT 
    HOUR(date_heure) AS "Heure",
    COUNT(*) AS "Nombre de commandes"
FROM t_commande
GROUP BY HOUR(date_heure)
ORDER BY COUNT(*) DESC;
```

Requête n°6: 

- Afficher le nombre de commandes des clients les plus fidèles. Un client est fidèle si son nombre de commandes est ≥ 5 . Afficher le résultat par ordre décroissant du nombre de commandes, puis par ordre alphabétique du nom.

```sql
SELECT 
    c.nom AS "Nom du client",
    c.prenom AS "Prénom",
    COUNT(*) AS "Nombre de commandes"
FROM t_commande cmd
JOIN t_client c ON cmd.client_passer_fk = c.client_id
GROUP BY c.client_id, c.nom, c.prenom
HAVING COUNT(*) >= 5
ORDER BY COUNT(*) DESC, c.nom ASC;
```

Requête n°7: 

- Afficher le total dû par commande. Afficher l’id de la commande et le montant dû (arrondi à 2 chiffres après la virgule). Ordonnez le résultat par ordre croissant des ids de commandes.

```sql
SELECT 
    cmd.commande_id AS "ID Commande",
    ROUND(SUM(lc.quantite * lc.prix_unitaire), 2) AS "Montant dû"
FROM t_commande cmd
JOIN t_ligne_commande lc ON lc.commande_contenir_fk = cmd.commande_id
GROUP BY cmd.commande_id
ORDER BY cmd.commande_id ASC;
```

Requête n°8:

- Afficher le total payé par commande (commande ayant au moins un paiement). Afficher l’id de la commande et le total payé (arrondi à 2 chiffres après la virgule). Ordonnez le résultat par ordre croissant des ids de commandes.

```sql
SELECT 
    cmd.commande_id AS "ID Commande",
    ROUND(SUM(pai.montant), 2) AS "Total payé"
FROM t_commande cmd
JOIN t_paiement pai ON pai.commande_associer_a_fk = cmd.commande_id
GROUP BY cmd.commande_id
ORDER BY cmd.commande_id ASC;
```

Requête n°9: 

- Quelle est la répartition des types de commandes.
- Ordonner le résultat par le nombre de commande de chaque type, du plus grand au plus petit.

1ère colonne : type
2ème colonne : nombre de commandes de ce type

```sql
SELECT 
    type_commande AS "Type de commande",
    COUNT(*) AS "Nombre de commandes"
FROM t_commande
GROUP BY type_commande
ORDER BY COUNT(*) DESC;
```

Requête n°10:

- Quel est le délai moyen de livraison par livreur (en minutes).
- Ordonner le résultat par délai moyen en minutes du plus petit au plus grand.

```sql
SELECT 
    lvr.livreur_id AS "ID Livreur",
    lvr.nom AS "Nom Livreur",
    ROUND(AVG(TIMESTAMPDIFF(MINUTE, lv.date_depart, lv.date_arrivee)), 2) AS "Délai moyen (minutes)"
FROM t_livraison lv
JOIN t_effectuer ef ON ef.livraison_effectuer_fk = lv.livraison_id
JOIN t_livreur lvr ON lvr.livreur_id = ef.livreur_effectuer_fk
GROUP BY lvr.livreur_id, lvr.nom
ORDER BY "Délai moyen (minutes)" ASC;
```
---
## Index

Soit les 2 requêtes suivantes :

Requête n°1:

```
SELECT 
    c.commande_id AS "ID commande",
    c.date_heure AS "Date",
    c.statut AS "Statut",
    cl.nom AS "Client"
FROM t_commande c
JOIN t_client cl ON c.client_passer_fk = cl.client_id
WHERE c.statut LIKE 'livr%'
  AND c.date_heure > '2025-02-01'
ORDER BY c.date_heure DESC;
```
Indexe Créé:
```
CREATE INDEX idx_commande_statut_date
ON t_commande (statut, date_heure);
```
Cet index permet d’accélérer la recherche des commandes selon leur statut et leur date, 
tout en optimisant le tri par date de commande.


Requête n°2 :

```
SELECT 
    a.npa AS "Zone NPA",
    COUNT(c.commande_id) AS "Nombre de commandes"
FROM t_commande AS c
JOIN t_adresse AS a 
    ON c.adresse_relier_fk = a.adresse_id
WHERE c.type_commande = 'livraison'
  AND HOUR(c.date_heure) BETWEEN 18 AND 21
GROUP BY a.npa
ORDER BY COUNT(c.commande_id) DESC;
```
Indexe Créé:
```
CREATE INDEX idx_commande_type_date_adresse
ON t_commande (type_commande, date_heure, adresse_relier_fk);
```
Cet index améliore les performances des requêtes filtrant les commandes par type, 
par tranche horaire et par adresse de livraison.
---
## Utilisateurs et rôles

### Creation des rôles 

```sql
CREATE ROLE 'Administrateur';
CREATE ROLE 'Manager';
CREATE ROLE 'Pizzaiolo';
CREATE ROLE 'Livreur';
CREATE ROLE 'Agent_de_caisse';
CREATE ROLE 'Analyste';
```

### Attribution des droits aux rôles

- Administrateur
```sql
GRANT ALL PRIVILEGES ON db_pizzeria.* TO 'Administrateur' WITH GRANT OPTION;

- Manager
```sql
GRANT SELECT, INSERT, UPDATE ON db_pizzeria.t_commande TO 'Manager';
GRANT SELECT, INSERT, UPDATE ON db_pizzeria.t_ligne_commande TO 'Manager';
GRANT SELECT, INSERT, UPDATE ON db_pizzeria.t_livraison TO 'Manager';
GRANT SELECT, INSERT, UPDATE ON db_pizzeria.t_livreur TO 'Manager';
GRANT SELECT ON db_pizzeria.t_paiement TO 'Manager';
GRANT SELECT, INSERT, UPDATE ON db_pizzeria.t_produit TO 'Manager';
```
- Pizzaiolo
```sql
GRANT SELECT ON db_pizzeria.t_commande TO 'Pizzaiolo';
GRANT SELECT ON db_pizzeria.t_ligne_commande TO 'Pizzaiolo';
GRANT UPDATE (status) ON db_pizzeria.t_commande TO 'Pizzaiolo';
```
- Livreur
```sql
GRANT UPDATE (status) ON db_pizzeria.t_commande TO 'Livreur';
GRANT UPDATE (date_depart, date_arrivee) ON db_pizzeria.t_livraison TO 'Livreur';
GRANT SELECT ON db_pizzeria.t_commande TO 'Livreur';
GRANT SELECT ON db_pizzeria.t_livraison TO 'Livreur';
```
- Agents de caisse
```sql
GRANT SELECT ON db_pizzeria.t_commande TO 'Agent_de_caisse';
GRANT SELECT, INSERT, UPDATE ON db_pizzeria.t_paiement TO 'Agent_de_caisse';
```

- Analyste
```sql
GRANT SELECT ON db_pizzeria.* TO 'Analyste';
```

### Création utilisateurs 

```
CREATE USER 'bob'@'localhost' IDENTIFIED BY 'bob2026';
CREATE USER 'matteo'@'localhost' IDENTIFIED BY 'matteo2026';
CREATE USER 'alice'@'localhost' IDENTIFIED BY 'alice2026';
CREATE USER 'julien'@'localhost' IDENTIFIED BY 'julien2026';
CREATE USER 'luis'@'localhost' IDENTIFIED BY 'luis2026';
CREATE USER 'david'@'localhost' IDENTIFIED BY 'david2026';
```

### Attribution des rôles

```
GRANT 'Administrateur' TO 'matteo'@'localhost';
GRANT 'Manager' TO 'bob'@'localhost';
GRANT 'Pizzaiolo' TO 'alice'@'localhost';
GRANT 'Livreur' TO 'julien'@'localhost';
GRANT 'Agent_de_caisse' TO 'luis'@'localhost';
GRANT 'Analyste' TO 'david'@'localhost';
```
---
## Transaction
### Scénarios : Paiement d'une commande 

- Un client paie sa commande.
- Le système doit alors :
- enregistrer le paiement,
- mettre à jour le statut de la commande (ex : payee).


Cas normal (transaction réussie)

```
START TRANSACTION;

-- 1. Enregistrement du paiement
INSERT INTO t_paiement (
    mode_paiement,
    date_paiement,
    montant,
    commande_associer_a_fk
)
VALUES (
    'Carte bancaire',
    NOW(),
    '39.90',
    1
);

UPDATE t_commande
SET statut = 'payee'
WHERE commande_id = 1;

COMMIT;
```

Résultat :
- paiement enregistré
- commande marquée comme payée

Cas d’erreur provoquée (ROLLBACK)
```
START TRANSACTION;

INSERT INTO t_paiement (
    mode_paiement,
    date_paiement,
    montant,
    commande_associer_a_fk
)
VALUES (
    'Carte bancaire',
    NOW(),
    '39.90',
    999
);

UPDATE t_commande
SET statut = 'payee'
WHERE commande_id = 9999;

ROLLBACK;
```

Résultat :
- Le commande_id empêche l’insertion (existe pas)
- La transaction est annulée
- Aucun paiement n’est enregisté


## Conclusion




