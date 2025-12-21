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


