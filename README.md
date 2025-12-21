# P-DB_106  
## Rapport de projet – Thanos Pizza  

### Création du MCD / MLD

---

## Première version (avant obtention des données)

### MCD  
<img width="1102" height="595" alt="MCD_V1" src="https://github.com/user-attachments/assets/b34978ae-49c5-4a10-9c02-3eb4a94ddedb" />

### MLD  
<img width="1038" height="613" alt="MLD_V1" src="https://github.com/user-attachments/assets/320514e1-923d-4c7b-afde-42dfebf48d9a" />

---

## Version finale

### MCD  
<img width="1105" height="713" alt="MCD_V2png" src="https://github.com/user-attachments/assets/95136f6b-f0cb-4ec9-9a6e-d102dcca0b57" />

### MLD  
<img width="1111" height="698" alt="MLD_V2" src="https://github.com/user-attachments/assets/38d02521-f3cb-4084-bee0-5d4ef906683e" />

---

## Changements notables

- **Paiement enrichi** : ajout des attributs `date_paiement` et `montant`, et renommage de `mode` en `mode_paiement`.
- **Commande simplifiée** : suppression de l’attribut `montant`, désormais géré dans la table `paiement`, et renommage des clés étrangères pour une meilleure cohérence (`nom_table_fk`).
- **Relations clarifiées** : une commande contient une ou plusieurs lignes de commande, et un produit est toujours associé via une ligne de commande.
- **Refonte de l’association `composer`** : déplacement de l’association récursive de `produit` vers `ligne_commande`.
- **Livreur amélioré** : ajout de l’attribut `actif` pour indiquer la disponibilité des livreurs.

---

## Mise en place d'un environement docker pour mysql

- Telechrger le zip et dezipper
- Ouvrir le docker compose et ajouter "command: --secure-file-priv=/scripts" apres la balise "volumes:"
- ouvrir le cmd dans de le dossier Docker_MySQL
- Faire docker-compose up -d
- Sur un navigateur tapez "http://localhost:8080" puis connecter vous en root root


---sql
CREATE TABLE t_client(
   client_id INT AUTO_INCREMENT,
   nom VARCHAR(50)  NOT NULL,
   prenom VARCHAR(50)  NOT NULL,
   couriel VARCHAR(100)  NOT NULL,
   telephone VARCHAR(50)  NOT NULL,
   PRIMARY KEY(client_id),
   UNIQUE(couriel),
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
   date_depart DATE NOT NULL,
   date_arrivee DATE NOT NULL,
   distance_estimee VARCHAR(50) ,
   commande_id_commande_affecter_fk INT NOT NULL,
   PRIMARY KEY(livraison_id),
   UNIQUE(commande_id_commande_affecter_fk),
   FOREIGN KEY(commande_id_commande_affecter_fk) REFERENCES t_commande(commande_id)
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

---


