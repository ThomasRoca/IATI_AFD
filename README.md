## Publier les données AFD sur le portail IATI

**Date de création**: 09/03/2017<br>
**Autheur:** Thomas Roca, Data officer, unité Transformation digitale<br>
**Mots Clés**: #IATI, #opendata, #transparence, #redevabilité, #accountability

<br>
![](http://www.aidtransparency.net/annualreport2015/images/furniture/logo.png)


<br>
--

### A propos de l'IATI

L'Initiative International sur la Transparence de l'Aide a pour ambition de créer un standard de publication de données sur l'aide au développement afin de faciliter le partage d'information, la transparence et la redevabilité des acteurs du développement. L'IATI est aussi une communauté de donneurs et d'organisation de la société civile.
Un [registre](http://iatistandard.org/) est maintenu et permet à tous d'acceder dans un format commun, aux actions, aux montant engagés dans le passé mais aussi  les montants prévus. En cela, l'ITAI se différencie du CAD de l'OCDE qui publie les données d'Aide Publique au Développement avec près de 2 ans de retard, ce qui pour IATI ne permet aux décideurs des pays partenaires de prendre des décisions d'investissement.

Des outils sont développés par le secretariat : 

- Une [tableau de bord](http://dashboard.iatistandard.org/publisher/afd.html) affiche les informations de chaque fournisseur de données<br><br>
![](http://www.stats4dev.com/doc/dashboard_IATI_AFD.png)

- [D-portal](http://www.d-portal.org/ctrack.html#view=search) permet de visualiser les données<br><br>
![](http://www.stats4dev.com/doc/dportal.png)

- [Validator](validator.iatistandard.org) permet de valider les fichiers d'open data produits. Par exemple: <br>[http://validator.iatistandard.org/?url=https://www.data.gouv.fr/s/resources/donnees-de-laide-au-developpement-de-lafd-1/20160930-101707/fr-AFD-CG.xml](http://validator.iatistandard.org/?url=https://www.data.gouv.fr/s/resources/donnees-de-laide-au-developpement-de-lafd-1/20160930-101707/fr-AFD-CG.xml).<br>
*NB. Il suffit d'ajouter l'URL du fichier à valider*

![](http://www.stats4dev.com/doc/validator.png)

<br>
### 1. Le fonctionnement
Le registre IATI n'heberge pas les fichiers de données produits par les donneurs. Il créer et maintient des metas data et fourni un service de validation et d'indexation de ces fichiers pour les rendre accessibles.
Au 09/03/2017, des fichiers au format XML sont produits par le système d'information de l'AFD - un fichier par pays (60 fichiers) - et sont envoyés manuellement sur le portail Open data du gouvernement: [http://data.gouv.fr/](http://data.gouv.fr/).
Il faut ensuite enregistrer un par un les liens dans le registre de l'IATI à partir de leur portail. 

**Recommandation:** concatener les 60 fichiers en un seul afin de réduire le nombre d'opération à faire manuellement, ou bien utiliser un script qui envoie les liens via l'API du portail. Il existe une fonctione qui permet de télécharger les liens (dans un CSV) de l'ensemble des fichiers afin de les mettre à jours sur le registre.

<br>

### 2. Les problèmes recontrés

**i. Les fichiers XML produits par le système d'information semblent corrompus**
Au 09/03/2017, les fichiers XML  produits par le système d'information de l'AFD sont corrompus. Comme le montre le dashboard AFD du registre IATI, 61 fichiers sur 64 n'ont pas passés la validation:
<br><br>
![](http://www.stats4dev.com/doc/dashboard_bilan.png)

L'outil validator donne une indication sur les problèmes rencontrés:
Par exemple pour le ficier fr-AFD-DO.xml (Rep. Dominicaine)
![](http://www.stats4dev.com/doc/validator_extra_info.png)

Le validator montre que le format XML est bien valide, mais le fichier ne correspond pas au standard de publication IATI. En effet il contient des champs obligatoires non renseignés tel que: ``<activity-date type="end-planned" iso-date=""/>`` ou encore ``<coordinates precision="3" longitude="" latitude=""/>``

Afin de réparer ces erreurs, j'ai écris un script python afin de:

- scrapper le dashboard de l'AFD sur le registre IATI pour récupérér les liens des fichier XML sources
- Telecharger les fichiers originaux et les sauvegarder
- Lire les fichiers et identifier les champs vides
- Supprimer les lignes contenants ces champs vides
- Reécrire les fichiers XML et les sauvegarder dans un dossier séparer afin de les uploader de nouvaux.

Ce script est disponible sur ce dossier [Github](https://github.com/ThomasRoca/IATI_AFD/blob/master/IATI%20files%20fixer%20Github.ipynb)

Un test avec l'outil validator a été effectué sur le fichier: [fr-AFD-AF.xml](
http://validator.iatistandard.org/?url=http://stats4dev.com/test/fr-AFD-AF.xml)
et semble valider le concept.

![](http://www.stats4dev.com/doc/succeed_test.png)

**ii. L'hébergement des fichiers XML sur data.gouv.fr ne semble pas être une bonne option**

L'hébergement des fichiers sur le portail http://data.gouv.fr/ pose un certain nombre de problèmes pratiques. Lorsque les fichiers sont uploaded ou mis à jours via l'interface (je dois encore tester l'API), la date est insérée dans l'url ainsi qu'un code. Ceci impose alors de mettre à jours les liens du côté du registre IATI..

**Solutions envisagées:**

- Heberger en interne les fichier XML sur un espace dédié sur les serveurs AFD (et mettre à jour le registre IATI une fois pour toute)
- Utiliser un seul fichier pour l'enemble des activités (et non plus un fichier par pays)
- Utiliser l'API de data.gouv.fr si il permet d'update les fichiers sans modifier l'url.
