# install-redis-with-docker
Pour installer Redis avec Docker Compose et l'utiliser avec Predis dans un projet Symfony 5, en configurant Redis comme cache par défaut, suivez les étapes ci-dessous :

### Étape 1 : Configuration de Docker Compose

Créez un fichier `docker-compose.yaml` à la racine de votre projet Symfony avec le contenu suivant :

```yaml
version: '3'
services:
  php:
    build:
      context: .
    volumes:
      - .:/var/www/html
    ports:
      - 9000:9000
    depends_on:
      - redis
  redis:
    image: redis:latest
    ports:
      - 6379:6379
```

Cela définit deux services : `php` pour votre application Symfony et `redis` pour le serveur Redis. Assurez-vous que le service PHP dépend du service Redis.

### Étape 2 : Configuration de Symfony pour utiliser Predis

1. Installez Predis via Composer :

```bash
composer require predis/predis
```

2. Dans Symfony 5, vous pouvez configurer Predis dans le fichier `config/services.yaml`. Voici un exemple de configuration de Predis en tant que service :

```yaml
services:
    Predis\Client:
        arguments:
            - '%env(REDIS_URL)%'
```

### Étape 3 : Configuration de Symfony pour utiliser Redis comme cache par défaut

1. Dans le fichier `config/packages/framework.yaml`, configurez Symfony pour utiliser Redis comme cache par défaut :

```yaml
framework:
    cache:
        app: cache.adapter.redis
        default_redis_provider: 'redis://redis:6379'
```

Assurez-vous d'ajuster `%env(REDIS_HOST)%` et `%env(REDIS_PORT)%` selon votre configuration Docker Compose.

### Étape 4 : Exécution de Docker Compose

Dans le répertoire de votre projet, exécutez la commande suivante pour démarrer vos conteneurs Docker :

```bash
docker-compose up -d
```

Cela démarrera les conteneurs Docker pour PHP et Redis.

Ces étapes devraient vous permettre d'installer Redis avec Docker Compose, de l'utiliser avec Predis dans un projet Symfony 5, et de le configurer comme cache par défaut.

### Étape 5 : Utilisation de predis dans Symfony pour interagir avec redis dans un controller Symfony

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Predis\Client;

class ExampleController extends AbstractController
{
    private $redis;

    public function __construct(Client $redis)
    {
        $this->redis = $redis;
    }

    public function exampleAction()
    {
        // Définir une valeur dans Redis
        $this->redis->set('mykey', 'Hello Redis');

        // Récupérer la valeur depuis Redis
        $value = $this->redis->get('mykey');

        return $this->render('example.html.twig', [
            'value' => $value
        ]);
    }
}

### Etape 6: Utiliser redis pour stocker la session Symfony;
Dans framework.yaml le paramètre handler_id doit être défini comme suit:

handler_id: '%env(REDIS_URL)%'
       

