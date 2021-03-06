# Nettrine / DBAL

## Content

- [Installation - how to install](#installation)
- [Configuration - basic setup](#configuration)
- [Usage](#usage)
- [Events](#events)
- [Bridges](#bridges)
    - [Symfony\Console](#symfony-console)

## Installation

At first you have to setup extension.

```yaml
extensions:
    dbal: Nettrine\DBAL\DI\DbalExtension
```

There are also some bridges as Symfony\Console. You'll known in next sections.

## Configuration

Minimal configuration could looks like this.

```yaml
dbal:
    debug: %debugMode%
    connection:
        host: localhost
        driver: mysqli
        dbname: nettrine
        user: root
        password: root
```

Full configuration options:

```yaml
dbal:
    debug: %debugMode%
    configuration:
        sqlLogger: NULL
        resultCacheImpl: NULL
        filterSchemaAssetsExpression: NULL
        autoCommit: TRUE

    connection:
        url: NULL
        pdo: NULL
        memory: NULL
        driver: pdo_mysql
        driverClass: NULL
        host: NULL
        dbname: NULL
        servicename: NULL
        user: NULL
        password: NULL
        charset: UTF8
        portability: PortabilityConnection::PORTABILITY_ALL
        fetchCase: PDO::CASE_LOWER
        persistent: TRUE
        types: []
        typesMapping: []
        wrapperClass: NULL
```

### Types

Here is a example how to custom type. For more information, follow the official documention.

- http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/types.html
- http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/cookbook/custom-mapping-types.html

```yaml
dbal:
    connection:
        types:
            uuid_binary_ordered_time:
                class: Ramsey\Uuid\Doctrine\UuidBinaryOrderedTimeType
                commented: falsee

        typesMapping:
            uuid_binary_ordered_time: binary
```

## Events

You can use native [Doctrine DBAL event system](https://www.doctrine-project.org/projects/doctrine-dbal/en/2.7/reference/events.html#events).

```yaml
services:
    subscriber1:
      class: App\PostConnectSubscriber
      tags: [nettrine.subscriber]
```

Register and create your own subscribers. There're services, so constructor injection will works. There're also
loaded lazily, don't worry about performance.

```php
namespace App;

use Doctrine\Common\EventSubscriber;
use Doctrine\DBAL\Event\ConnectionEventArgs;
use Doctrine\DBAL\Events;

final class PostConnectSubscriber implements EventSubscriber
{
	public function postConnect(ConnectionEventArgs $args): void
	{
		// Magic goes here...
	}

	public function getSubscribedEvents(): array
	{
		return [Events::postConnect];
	}

}
```

Don't waste time to tag single service and tag them all together using decorator.

```yaml
decorator:
    Doctrine\Common\EventSubscriber:
      tags: [nettrine.subscriber]
```

## Bridges

### Symfony\Console

This package works pretty well with [Symfony/Console](https://symfony.com/doc/current/components/console.html). Take a look at [Contributte/Console](https://github.com/contributte/console)
tiny integration for Nette Framework.

```yaml
extensions:
    # Console
    console: Contributte\Console\DI\ConsoleExtension

    # Dbal
    dbal: Nettrine\Dbal\DI\DbalExtension
    dbal.console: Nettrine\Dbal\DI\DbalConsoleExtension(%consoleMode%)
```

From this moment when you type `bin/console`, there'll be registered commands from Doctrine DBAL.

![Commands](assets/commands.png)
