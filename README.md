# P-DB_106 

Rapport de projet P-DB_106 (Thanos pizza)

Creation MLD / MCD: 

Première version (avant obtention des données): 

MCD 

<img width="1102" height="595" alt="MCD_V1" src="https://github.com/user-attachments/assets/b34978ae-49c5-4a10-9c02-3eb4a94ddedb" /> 

MLD 

<img width="1038" height="613" alt="MLD_V1" src="https://github.com/user-attachments/assets/320514e1-923d-4c7b-afde-42dfebf48d9a" /> 


Version finale :
MCD 

<img width="1105" height="713" alt="MCD_V2png" src="https://github.com/user-attachments/assets/95136f6b-f0cb-4ec9-9a6e-d102dcca0b57" /> 

MLD 

<img width="1111" height="698" alt="MLD_V2" src="https://github.com/user-attachments/assets/38d02521-f3cb-4084-bee0-5d4ef906683e" /> 

Changement notable :
 <ul>
   <li>
     Ajout des attributs date_paiement et montant, et renommage de mode en mode_paiement afin de mieux représenter un paiement réel.
   </li>
   <li>
     Suppression de l’attribut montant, celui-ci étant désormais dans la table paiement, et renommage des fk de verbe_nomtable_fk en nom_table_fk .
   </li>
   <li>
     Les relations ont été précisées afin de garantir qu’une commande contient une ou plusieurs lignes de commande et qu’un produit est toujours associé via une ligne de commande.
   </li>
   <li>
     Déplacement de l'association récursive "composer" de produit à ligne commande
   </li>
   <li>
     L’attribut actif a été ajouté à l’entité Livreur pour indiquer la disponibilité des livreurs.
   </li>
   
  
 </ul>
