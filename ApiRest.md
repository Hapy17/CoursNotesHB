# API REST 
## Les bases 
### Le protocole HTTP

Il sert pour l'échange de données entre le client et le serveur.

Il permet de faire des requêtes du client vers le serveur.
- URL: L'URL est le chemin vers lequel le serveur va répondre.
- methods (GET, POST, PUT, DELETE): Les méthodes HTTP sont les différentes méthodes de requête que le serveur peut répondre.
    - GET: Permet de récupérer des données du serveur.
    - POST: Permet d'envoyer des données au serveur.
    - PUT: Permet de modifier des données du serveur.
    - DELETE: Permet de supprimer des données du serveur.
    - options :
        - patch : permet de modifier un objet.
        - head : permet de récupérer les informations sur le contenu de la page.
        - connect : permet de se connecter au serveur.
        - trace : permet de suivre le chemin d'un objet.
- headers: Les headers sont des informations qui sont envoyées au serveur.
- body: Le body est le contenu de la requête. 

Il y a aussi des réponses du serveur vers le client.
- status code (200, 404, 500): Le status code est le code de réponse du serveur.
- headers : Les headers sont les informations envoyées par le serveur.
- body : Le body est le contenu de la réponse. 
---
## Installation de API Platform 
*Tout ceci est à faire apres crée des Entities*
- Tapez la commande suivante dans votre projet:
    ```
    symfony composer require api
    ```
- Lancez la commande :
    ```
    symfony serve
    ```
- Vous pouvez accéder à la doc de l'API en tapant l'URL suivante:
    ```
    http://127.0.0.1:8000/api
    ```
- Ajoutez dans votre fichier Entity: 
    ```php
    use ApiPlatform\Core\Annotation\ApiResource;
    #[ApiResource]
   ```



---
Références & Ressources :   
[HTTP - Wikipédia](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)  
[Status codes HTTP - Wikipédia](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)  
[Headers des requêtes HTTP - Wikipédia](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)  
[Headers de requêtes HTTP - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)  
[API Platform](https://api-platform.com/)  
[OpenAPI](https://www.openapis.org/)
