# afdbneo4jprojectsgraph
Funded projects by type, counties, institutions and financing sources in Africa

# starting model implmentation / Implémentation du modèle de départ

MERGE (c1:Pays {nom_pays: 'Côte d’Ivoire'})
MERGE (p1:Projet {titre:'PREMOPEF',date_approb:'10 nov. 2020',statut:'Approuvé',souverain:'Oui'})
MERGE (s1:Secteur {nom_secteur:'Agriculture et Développement rural'})
MERGE (f1:Financeur {nom_financeur: 'Fonds Africain de Développement '})
MERGE (f2:Financeur {nom_financeur: 'Programme Mondial pour l’Agriculture et la Sécurité Alimentaire'})
MERGE (p1)-[:EST_EXECUTE {date_achevement:'31 dec. 2025'}]->(c1)
MERGE (f1)-[:ENGAGEMENT {montant:'8451775,5'}]->(p1)
MERGE (f2)-[:ENGAGEMENT {montant:'2000000'}]->(p1)
MERGE (p1)-[:CONCERNE ]->(s1)

# csv complete data file loading / Chargement du jeu de données complet csv

LOAD CSV WITH HEADERS FROM  'file:///data_ok_3.csv' AS row FIELDTERMINATOR ';'
MERGE (c:Pays {nom_pays: row.Country})
MERGE (p:Projet {titre: row.Title, date_approb:row.StartingDate,statut:row.Status,souverain:row.Sovereign})
MERGE (s:Secteur {nom_secteur:row.Sector})
MERGE(f:Financeur {nom_financeur:row.SOF} )
MERGE (p)-[:CONCERNE]->(s)
MERGE (p)-[:EST_EXECUTE]->(c)
MERGE (f)-[financement:ENGAGEMENT {
  montant: toInteger(row.Commitment)
  }]->(p)

**** BASIC QUERIES ***

# All  the nodes / tous les noeuds du graphe
MATCH (n)
RETURN n

# All the sectors related to PRMOPEF project / Tous les secteurs concernés par le projet PREMOPEF
MATCH connection = (:Projet {titre: 'PREMOPEF'})-[:CONCERNE]->(:Secteur)
RETURN connection

# Counties with hight amount of projects / Pays ayant le plus de projets proposés
MATCH (c:Pays)<-[:EST_EXECUTE]-()
RETURN c.nom_pays, COUNT(*) AS Nbprojets
ORDER BY Nbprojets DESC
LIMIT 10

# Tabe of all project executed in a country (Gabon) / Tableau de tous les projets exécutés dans un pays
MATCH  (origin:Projet)-[connection:EST_EXECUTE]->(destination:Pays)
WHERE destination.nom_pays = 'Gabon'  
RETURN origin.titre, destination.nom_pays





