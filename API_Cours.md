# API
## Installation de la dépendance api-platform
    symfony composer require api
---
## Ajouter des opérations sur les entités  

- Ajouter un attribut #[ApiResource] au dessus de la class :
    ```php
    use ApiPlatform\Core\Annotation\ApiResource
    ```

- Pour modifier les opérations possible sur l'API il faut rajouter des options à  #[ApiResource]  

- En enlevant ou ajoutant des methodes on peut choisir lesquels seront utilisables sur l'entité :
    ```php
    #[ApiResource(
        collectionOperations: ['get', 'post'],
        itemOperations: ['get', 'put', 'delete'],
    )]
    ```
---
## Méthodes de validations

- Il faut d'abord installer la dépendance avec cette ligne de commandes
    ```php
    composer require symfony/validator doctrine/annotations
    ```
- [En cliquant ici vous pouvez voir la Doc de la validation de Symfony][1]

---
## Installation de JWT token
- Executer cette ligne de commande:
    ```
    symfony composer require jwt-auth
    ```

- Créer les clées publics/privées
    - Pour environnement de dev:
        - Créer dossier config/jwt
        - Rajouter les deux clés envoyé par Benjamin puis ajouter le mdp 123456 dans .env.local   
        *Les fichiers .key sont à transformer en .pem* 

    <!-- - Pour la prod :  
        rechercher comment générer les clés   -->


- Coller dans le fichier config/package/security.yaml et remplacer le contenu de main par :
    ```yaml
    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false
        # Inserer le code en dessous
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

- Remplacer le access_control dans le même fichier :
    ```yaml
    # Inserer au même niveau d'indentation que firewall
    access_control:
        - { path: ^/api/docs, roles: PUBLIC_ACCESS } # Allows accessing the Swagger UI
        - { path: ^/authentication_token, roles: PUBLIC_ACCESS }
        - { path: ^/api, roles: IS_AUTHENTICATED_FULLY }
    ```

- Ajouter dans le fichier config/route.yaml :
    ```yaml
    # Inserer au niveau de indentation 0
    authentication_token:
        path: /authentication_token
        methods: ['POST']
    ```

- Modifier le fichier config/package/api_platform.yaml et fusionner les swagger :
    ```yaml
    # Faites attention au niveau d'indentation
    swagger:
        versions: [3]
        api_keys:
            JWT:
                name: Authorization
                type: header
    ```

*Pour s'authentifier, il faura maintenant aller sur url/authentication_token*

- Pour créer une opération Post sur authentication token dans la doc :
    - Créer le fichier src/OpenApi/JwtDecorator.php et mettre ce code à l'intérieur :
    ```php
    <?php
    declare(strict_types=1);

    namespace App\OpenApi;

    use ApiPlatform\Core\OpenApi\Factory\OpenApiFactoryInterface;
    use ApiPlatform\Core\OpenApi\OpenApi;
    use ApiPlatform\Core\OpenApi\Model;

    final class JwtDecorator implements OpenApiFactoryInterface
    {
        public function __construct(
            private OpenApiFactoryInterface $decorated
        ) {}

        public function __invoke(array $context = []): OpenApi
        {
            $openApi = ($this->decorated)($context);
            $schemas = $openApi->getComponents()->getSchemas();

            $schemas['JwtToken'] = new \ArrayObject([
                'type' => 'object',
                'properties' => [
                    'token' => [
                        'type' => 'string',
                        'readOnly' => true,
                    ],
                ],
            ]);
            $schemas['Credentials'] = new \ArrayObject([
                'type' => 'object',
                'properties' => [
                    'email' => [
                        'type' => 'string',
                        'example' => 'admin@test.com',
                    ],
                    'password' => [
                        'type' => 'string',
                        'example' => '1234',
                    ],
                ],
            ]);

            $schemas = $openApi->getComponents()->getSecuritySchemes() ?? [];
            $schemas['JWT'] = new \ArrayObject([
                'type' => 'http',
                'scheme' => 'bearer',
                'bearerFormat' => 'JWT',
            ]);

            $pathItem = new Model\PathItem(
                ref: 'JWT Token',
                post: new Model\Operation(
                    operationId: 'postCredentialsItem',
                    tags: ['Login'],
                    responses: [
                        '200' => [
                            'description' => 'Get JWT token',
                            'content' => [
                                'application/json' => [
                                    'schema' => [
                                        '$ref' => '#/components/schemas/JwtToken',
                                    ],
                                ],
                            ],
                        ],
                    ],
                    summary: 'Get JWT token to login.',
                    requestBody: new Model\RequestBody(
                        description: 'Generate new JWT Token',
                        content: new \ArrayObject([
                            'application/json' => [
                                'schema' => [
                                    '$ref' => '#/components/schemas/Credentials',
                                ],
                            ],
                        ]),
                    ),
                    security: [],
                ),
            );
            $openApi->getPaths()->addPath('/authentication_token', $pathItem);

            return $openApi;
        }
    }
    ```

- Ajouter dans le fichier config/services.yaml : 
    ```yaml
    # Inserer au même niveau d'indentation que App\ juste au dessus
        App\OpenApi\JwtDecorator:
            decorates: 'api_platform.openapi.factory'
            arguments: ['@.inner']
    ```

    - Création des roles  
        - Ajouter dans le fichier config/package/security.yaml avant firewall :
        ```yaml 
        # Inserer au même niveau d'indentation que provider et firewall
           role_hierarchy:
            ROLE_ADMIN: ROLE_USER
            ROLE_SUPER_ADMIN: ROLE_USER
            ROLE_STATS: ROLE_USER
        ```
---
## Bonus 
- Vider le cache 
    - Dans le terminal :
    ```
    symfony console cache:clear
    ```
    - Directement dans les fichiers :
        - Supprimer le dossier var\cache

 
<!-- ## Références -->
[1]: https://symfony.com/doc/current/validation.html

