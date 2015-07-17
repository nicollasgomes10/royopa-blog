{
"title" : "Doctrine Event Listeners e Subscribers no Symfony 2",
"author":"Royopa",
"date":"25-09-2014",
"tag":"doctrine, php, symfony, listener, subscriber",
"slug" : "events-listeners-e-subscribers-no-symfony-2",
"category":"Symfony"
}

Cópia com algumas alterações na tradução da página [Como Registrar listeners e Assinantes de Eventos](http://andreiabohner.org/symfony2docs/cookbook/doctrine/event_listeners_subscribers.html)
da tradução em português da documentação do Symfony, coordenada pela
[Andréia Bohner](https://github.com/andreia).

O Doctrine contém um valioso sistema de eventos que são disparados quando quase
tudo acontece dentro do sistema. Para você, isso significa que poderá criar
[serviços](http://andreiabohner.org/symfony2docs/book/service_container.html)
arbitrários e dizer ao Doctrine para notificar os objetos sempre que uma
determinada ação (ex. `prePersist`) acontecer.
Isto pode ser útil, por exemplo, para criar um índice de pesquisa independente
sempre que um objeto em seu banco de dados for salvo.

O Doctrine define dois tipos de objetos que podem ouvir eventos do
Doctrine: listeners and subscribers. Ambos são muito semelhantes, mas os
listeners são um pouco mais simples. Para saber mais, consulte
[O Sistema de Eventos](http://docs.doctrine-project.org/projects/doctrine-orm/en/2.1/reference/events.html)
no site do Doctrine.

O site do Doctrine também explica todos os eventos existentes que podem ser
escutados.

Configurando o Listener/Subscriber
==================================

Para registrar um serviço para agir como um listener ou subscriber de
eventos você só tem que usar a [tag](http://andreiabohner.org/symfony2docs/book/service_container.html#book-service-container-tags) com o nome apropriado. Dependendo de seu caso de uso, você pode
ligar um listener em cada conexão DBAL e entity manager ORM da entidade ou apenas
em uma conexão específica DBAL e todos os entity managers que usam esta conexão.

```yaml
doctrine:
    dbal:
        default_connection: default
        connections:
            default:
                driver: pdo_sqlite
                memory: true

services:
    my.listener:
        class: Acme\SearchBundle\EventListener\SearchIndexer
        tags:
            - { name: doctrine.event_listener, event: postPersist }
    my.listener2:
        class: Acme\SearchBundle\EventListener\SearchIndexer2
        tags:
            - { name: doctrine.event_listener, event: postPersist, connection: default }
    my.subscriber:
        class: Acme\SearchBundle\EventListener\SearchIndexerSubscriber
        tags:
            - { name: doctrine.event_subscriber, connection: default }
```

```xml
<?xml version="1.0" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:doctrine="http://symfony.com/schema/dic/doctrine">

    <doctrine:config>
        <doctrine:dbal default-connection="default">
            <doctrine:connection driver="pdo_sqlite" memory="true" />
        </doctrine:dbal>
    </doctrine:config>

    <services>
        <service id="my.listener" class="Acme\SearchBundle\EventListener\SearchIndexer">
            <tag name="doctrine.event_listener" event="postPersist" />
        </service>
        <service id="my.listener2" class="Acme\SearchBundle\EventListener\SearchIndexer2">
            <tag name="doctrine.event_listener" event="postPersist" connection="default" />
        </service>
        <service id="my.subscriber" class="Acme\SearchBundle\EventListener\SearchIndexerSubscriber">
            <tag name="doctrine.event_subscriber" connection="default" />
        </service>
    </services>
</container>
```

```php
use Symfony\Component\DependencyInjection\Definition;

$container->loadFromExtension('doctrine', array(
    'dbal' => array(
        'default_connection' => 'default',
        'connections' => array(
            'default' => array(
                'driver' => 'pdo_sqlite',
                'memory' => true,
            ),
        ),
    ),
));

$container
    ->setDefinition(
        'my.listener',
        new Definition('Acme\SearchBundle\EventListener\SearchIndexer')
    )
    ->addTag('doctrine.event_listener', array('event' => 'postPersist'))
;
$container
    ->setDefinition(
        'my.listener2',
        new Definition('Acme\SearchBundle\EventListener\SearchIndexer2')
    )
    ->addTag('doctrine.event_listener', array('event' => 'postPersist', 'connection' => 'default'))
;
$container
    ->setDefinition(
        'my.subscriber',
        new Definition('Acme\SearchBundle\EventListener\SearchIndexerSubscriber')
    )
    ->addTag('doctrine.event_subscriber', array('connection' => 'default'))
;
```

Criando a Classe Listener
=========================

No exemplo anterior, um serviço `my.listener` foi configurado como um listener
Doctrine no evento `postPersist`. A classe atrás desse serviço deve ter um
método `postPersist`, que será chamado quando o evento é lançado:

```php
// src/Acme/SearchBundle/EventListener/SearchIndexer.php
namespace Acme\SearchBundle\EventListener;

use Doctrine\ORM\Event\LifecycleEventArgs;
use Acme\StoreBundle\Entity\Product;

class SearchIndexer
{
    public function postPersist(LifecycleEventArgs $args)
    {
        $entity = $args->getEntity();
        $entityManager = $args->getEntityManager();

        // perhaps you only want to act on some "Product" entity
        if ($entity instanceof Product) {
            // ... do something with the Product
        }
    }
}
```

Em cada evento, você tem acesso a um objeto `LifecycleEventArgs`, que dá
acesso tanto ao objeto entidade do evento quanto ao gerenciador de
entidade em si.

Algo importante a notar é que um listener estará ouvindo *todas* as
entidades em sua aplicação. Então, se você está interessado apenas em
lidar com um tipo específico de entidade (por exemplo, uma entidade
` Product`, mas não uma entidade `BlogPost`, você deve verificar o
nome da classe da entidade em seu método (como mostrado acima).

Criando a classe Subscriber
===========================

Um evento subscriber do Doctrine deve implementar a interface
`Doctrine\Common\EventSubscriber` e ter um método de evento para cada um dos
eventos que ela assina:

```php
// src/Acme/SearchBundle/EventListener/SearchIndexerSubscriber.php
namespace Acme\SearchBundle\EventListener;

use Doctrine\Common\EventSubscriber;
use Doctrine\ORM\Event\LifecycleEventArgs;
// for Doctrine 2.4: Doctrine\Common\Persistence\Event\LifecycleEventArgs;
use Acme\StoreBundle\Entity\Product;

class SearchIndexerSubscriber implements EventSubscriber
{
    public function getSubscribedEvents()
    {
        return array(
            'postPersist',
            'postUpdate',
        );
    }

    public function postUpdate(LifecycleEventArgs $args)
    {
        $this->index($args);
    }

    public function postPersist(LifecycleEventArgs $args)
    {
        $this->index($args);
    }

    public function index(LifecycleEventArgs $args)
    {
        $entity = $args->getEntity();
        $entityManager = $args->getEntityManager();

        // perhaps you only want to act on some "Product" entity
        if ($entity instanceof Product) {
        // ... do something with the Product
        }
    }
}
```

Doctrine event subscribers can not return a flexible array of methods to
call for the events like the :ref:`Symfony event subscriber <event_dispatcher-using-event-subscribers>`
can. Doctrine event subscribers must return a simple array of the event
names they subscribe to. Doctrine will then expect methods on the subscriber
with the same name as each subscribed event, just as when using an event listener.

Para a referência completa, veja o capítulo [The Event System](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/events.html)
na documentação do Doctrine.
