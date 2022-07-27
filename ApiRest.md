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
## Configuration d'accès au requêtes de l'API
- Modifie le  fichier Entity et rajouter :
    ```php
    #[ApiRessource(
        collectionOperations: ['GET'];
        itemOperations: ['GET'];
    )]
    ```
## Validation des données
- Rajouter dans le fichier Entity :
    ```php
     #[ORM\Column(type: Types::DATETIME_MUTABLE)]
        Assert\NotBlank([
            message: "The date cannot be blank.",
        ]),
        Assert\LessThan([
            value: new \DateTime('now'),
            message: "The date cannot be in the past.",
        ]),
        Asser\GreaterThan([
            value: '2008-01-01',
            message: 'The date must be in the future',
        ]),
    ```
---

## KWT Authetication 
### Installer le package KWT-Authentication  
- Ajouter dans le fichier Entity :
    ```
    symfony composer require jwt-auth
    ```
- Création d'un fichier JWT et ajouter dedans les fichiers : 
    - public.pem
    - private.pem

- Déporter dans du fichier .env dans le fichier .env.local :
    ```
    JWT_PASSPHRASE=123456
    ```
### Configuration de Symfony pour utiliser le package KWT-Authentication  
- Configurer le fichier security.yaml :
    ```yaml
     main:
            stateless: true
            provider: app_user_provider
            json_login:
                check_path: /authentication_token
                username_path: email
                password_path: password
                success_handler: lexik_jwt_authentication.handler.authentication_success
                failure_handler: lexik_jwt_authentication.handler.authentication_failure
            jwt: ~
    ```  
    &  
    ```yaml
    access_control:
        - { path: ^/api/docs, roles: PUBLIC_ACCESS } # Allows accessing the Swagger UI
        - { path: ^/authentication_token, roles: PUBLIC_ACCESS }
        - { path: ^/, roles: IS_AUTHENTICATED_FULLY }
    ```
- Modifier le fichier api/config/routes.yaml :
    ```yaml
    authentication_token:
        path: /authentication_token
        methods: ['POST']
    ```
---
Références & Ressources :   
[HTTP - Wikipédia](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)  
[Status codes HTTP - Wikipédia](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)  
[Headers des requêtes HTTP - Wikipédia](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)  
[Headers de requêtes HTTP - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)     
[Validation Symfony](https://symfony.com/doc/current/validation.html)  
[URIs - Wikipédia](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)  
[JWT Authentication](https://api-platform.com/docs/core/jwt/#jwt-authentication)
[API Platform](https://api-platform.com/)  
[OpenAPI](https://www.openapis.org/)
