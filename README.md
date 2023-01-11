# qualinka-microservices-docker
Configuration docker 🐳 pour déployer Qualinka-Microservices 

[![Docker Pulls](https://img.shields.io/docker/pulls/abesesr/qualinka-ms.svg)](https://hub.docker.com/r/abesesr/qualinka-ms/)

## Microservices Qualinka

Ce dépôt git contient la configuration docker pour son déploiement.

### Description des microservices

#### find-ra-idref 
A partir d'une appellation (dont la forme la plus courante est firstName / lastName), le service renvoie les notices d’autorité de la base IdRef.  
Avec le paramètre "file", il est possible d’appeler un fichier de requêtes en particulier, afin d’ajuster les requêtes passées à l’appellation en entrée (ex : réduite à un lastName) ou le degré de finesse des résultats (ex : recherche étroite).
  - Url : https://qualinka.idref.fr/find-ra-idref/api/v2/req?lastName=robert&firstName=val%C3%A9rie
  - Url avec fichier de requête : https://qualinka.idref.fr/find-ra-idref/api/v2/req?lastName=robert&firstName=val%C3%A9rie&file=findra_light
  - Debug : https://qualinka.idref.fr/find-ra-idref/api/v2/debug/req?lastName=robert&firstName=val%C3%A9rie

#### find-nonlinked-rc-sudoc 
A partir d'une appellation (dont la forme la plus courante est firstName / lastName), le service renvoie les points d’accès des notices bibliographiques Sudoc non liés à une autorité de la base IdRef (sous la forme ppn + "-" + sa position à partir des zones 70X ; ex. 123456789-1).  
Avec le paramètre "file", il est possible d’appeler un fichier de requêtes en particulier, afin d’ajuster les requêtes passées à l’appellation en entrée (ex : réduite à un lastName) ou le degré de finesse des résultats (ex : recherche étroite).  
Ce service est le complémentaire du service "linked_rc_idref_sudoc".
 - Url : https://qualinka.idref.fr/find-nonlinked-rc-sudoc/api/v2/req?lastName=robert&firstName=val%C3%A9rie
 - Url avec fichier de requête : https://qualinka.idref.fr/find-nonlinked-rc-sudoc/api/v2/req?lastName=robert&firstName=val%C3%A9rie&file=findrc_light
 - Debug : https://qualinka.idref.fr/find-nonlinked-rc-sudoc/api/v2/debug/req?lastName=robert&firstName=val%C3%A9rie

#### linked_rc_idref_sudoc 
A partir d'un identifiant IdRef de notice d’autorité (sous la forme ex. 123456879), le service renvoie les points d’accès des notices bibliographiques Sudoc liés à une autorité de la base IdRef (sous la forme ppn + "-" + sa position à partir des zones 70X ; ex. 123456789-1).  
Avec le paramètre "file", il est possible d’appeler un fichier de requêtes en particulier, afin d’ajuster les requêtes passées à l’appellation en entrée (ex : réduite à un lastName) ou le degré de finesse des résultats (ex : recherche étroite).  
Ce service est le complémentaire du service "find-nonlinked-rc-sudoc".
 - Url : https://qualinka.idref.fr/linked-rc-idref-sudoc/api/v2/req?ra_id=076642860

#### attrra
A partir d'un identifiant IdRef d'autorité (sous la forme ex. 123456879), le service renvoie des informations issues des champs de la notice d’autorité sous la forme d’attributs.
 - Url : https://qualinka.idref.fr/attrra/api/v2/req?ra_id=076642860 

#### attrrc 
A partir d’un identifiant Sudoc de point d’accès de notice bibliographique (sous la forme ppn + "-" + sa position à partir des zones 70X ; ex. 123456789-1), le service renvoie des informations issues des champs de la notice bibliographique sous la forme d’attributs.
 - Url : https://qualinka.idref.fr/attrrc/api/v2/req?rc_id=019057547-1

Pour ces 5 microservices : par défaut, le format de réponse est JSON, mais il est possible d'ajouter &format=xml pour obtenir la réponse en XML (sauf pour le mode debug).  
Les fichiers de requêtes qu'on peut préciser pour find-ra-idref et find-nonlinked-rc-sudoc doivent se trouver sur ce dépôt : https://github.com/abes-esr/qualinka-findws-requests

Documentation OpenAPI de ces 5 web services : https://qualinka.idref.fr/swagger-ui.html

#### api-gateway 
Sert de point unique d'accès aux autres services. Il s'agit d'une passerelle

#### eureka-naming-server 
Registre des microservices

#### spring-cloud-config-server 
Centralise la configuration des microservices. Les configurations se trouvent sur ce dépôt : https://github.com/abes-esr/spring-cloud-config-server

### Administration

Le docker-compose attend un .env contenant la clé de décryptage JASYPT (MASTER_PASSWORD) des variables contenues dans les fichiers de configuration.  

Les microservices s'administrent comme tout conteneur d'un docker-compose :  

```
cd qualinka-microservices/

# Démarrer les conteneurs : 
sudo docker-compose up -d

# Voir les conteneurs :
sudo docker-compose ps

# Voir les logs d'un conteneur : 
sudo docker logs NomConteneur -f --tail=100

# Arrêter les conteneurs : 
sudo docker-compose stop

# Redémarrage des conteneurs : 
sudo docker-compose restart
```

Il n'y a pas de volume monté : pas de données à sauvegarder.  
Les microservices utilisent un moteur de recherche Solr et une base de données Oracle internes à l'Abes.


