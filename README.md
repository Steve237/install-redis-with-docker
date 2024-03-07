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
    container_name:redis
    image: redis:latest
    ports:
      - 6379:6379
```

Cela définit deux services : `php` pour votre application Symfony et `redis` pour le serveur Redis. Assurez-vous que le service PHP dépend du service Redis.
Pour accéder au terminal redis utiliser la commande: docker exec -it redis sh

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

### Commandes predis de base;

### Manipulation des clés et des valeurs :

- `set($key, $value)` : Définit la valeur d'une clé.
- `get($key)` : Récupère la valeur d'une clé.
- `del($key)` : Supprime une clé et sa valeur.
- `exists($key)` : Vérifie si une clé existe.
- `expire($key, $seconds)` : Définit une expiration (durée de vie) pour une clé en secondes.
- `ttl($key)` : Récupère le temps en secondes avant que la clé expire.

### Manipulation des listes :

- `lpush($key, $value)` : Ajoute une valeur à la tête d'une liste.
- `rpush($key, $value)` : Ajoute une valeur à la fin d'une liste.
- `lpop($key)` : Retire et renvoie la première valeur d'une liste.
- `rpop($key)` : Retire et renvoie la dernière valeur d'une liste.
- `llen($key)` : Récupère la longueur d'une liste.

### Manipulation des ensembles :

- `sadd($key, $value)` : Ajoute un élément à un ensemble.
- `srem($key, $value)` : Supprime un élément d'un ensemble.
- `smembers($key)` : Récupère tous les membres d'un ensemble.
- `sismember($key, $value)` : Vérifie si un élément est un membre d'un ensemble.

### Manipulation des hachages (hashes) :

- `hset($key, $field, $value)` : Définit le champ d'un hachage à une valeur.
- `hget($key, $field)` : Récupère la valeur d'un champ d'un hachage.
- `hdel($key, $field)` : Supprime un champ d'un hachage.
- `hgetall($key)` : Récupère tous les champs et valeurs d'un hachage.

### Autres opérations :

- `incr($key)` : Incrémente la valeur d'une clé numérique.
- `decr($key)` : Décrémente la valeur d'une clé numérique.
- `flushall()` : Supprime toutes les clés de la base de données.

Ces méthodes couvrent les opérations de base que vous pouvez effectuer avec Predis. Vous pouvez consulter la documentation officielle de Predis pour découvrir d'autres méthodes et fonctionnalités avancées.
       

