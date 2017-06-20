---
title: ¡No sin Composer!
theme: league
revealOptions:
    transition: 'slide'
---

# ¡No sin Composer!

<img data-src="resources/composer-logo.png" style="width: 200px;">

<p>[@carlosgoce](https://twitter.com/carlosgoce)</p>
<small>Developer @ Initios</small>


---


## Mapa

1. Autocarga ficheros PHP (no más require/include)
    * Estrategias
    * Namespaces y [PSR-4](http://www.php-fig.org/psr/psr-4/)
2. Gestionar dependencias
    * Composer.lock
    * Packagist
3. Crear nuestras propias librerías
    * [Versionamiento Semántico](http://semver.org/lang/es/)
    * Publicación

Bonus: Install + CLI + composer.json + Ejercicios

---

## Autocarga

Crear composer.json con tu estrategia/s

```json
{
    "autoloading": {
        "classmap": ["models/", "mislibrerias/"],
        "files": ["helpers/consultas.php"]
    }
}
```

Lanzar <i>composer dump-autoload</i> para que composer genere su mapa de ficheros/clases

Por último incluye el fichero "vendor/autoload.php" en tu fichero de entrada del proyecto (habitualmente index.php).


---

### Estrategias

- Classmap
- Files
- PSR-4

Para refrescar el autoloading podemos lanzar

```
composer dump-autoload
composer dump-autoload --optimize  # para producción
```

---

### Estrategias
##### Classmap

```json
{
    "autoload": {
        "classmap": ["src/", "lib/", "Something.php"]
    }
}
```

Busca clases escaneando los ficheros de las carpetas y/o ficheros indicados
El resultado se guarda en vendor/composer/autoload_classmap.php

---

### Estrategias
##### Files

```json
{
    "autoload": {
        "files": ["src/MyLibrary/functions.php"]
    }
}
```

Útil para ficheros de funciones.
No abusar porque estos ficheros se cargan siempre.

Equivalente a

```php
require 'src/MyLibrary/functions.php';
```

---

### Estrategias
##### PSR-4

El estandard de toda librería actual

```json
{
    "autoload": {
        "psr-4": { "MyLibrary\\": "src/" }
    }
}
```

Se indica que una carpeta está utilizando PSR-4, indicándole su Namespace.

<small>El fichero está guardado en "src/Service/EmailService.php"</small>

```
<?php
class MyLibrary\Service\EmailService {
    ...
}
```

<small>Especificación en [php-fig](http://www.php-fig.org/psr/psr-4/)</small>

---

### PSR-4

Ver Ejemplos de PSR-4

### Namespaces?

```
<?php
class Message {}
class Message {}

new Message();
```

<small>Fatal error: Cannot redeclare class Message...</small>

---

```php
<?php  # fichero A
namespace PHPVigo\Service;
class Message {}
```

```php
<?php  # fichero B
namespace PHPVigo\Queue;

class Message {}

# Está en el namespace actual
new Message();

# Está en otro namespace
new \PHPVigo\Service\Message();

```

```php
<?php  # fichero C
namespace PHPVigo\Controllers\HomeController;

use PHPVigo\Queue\Message as QueueMessage;
use PHPVigo\Service\Message as Message;

new Message();
new QueueMessage();
```

---

- ¿En que ruta deberían estar los ficheros?
- Como lanzaríamos una excepción en fichero C?

---

### Gestión de dependencias

<small>Las definimos en composer.json</small>

```json
{
    "php": ">=5.6.4",
    "require": {
        "illuminate/support": "@stable",
        "jstayton/google-maps-geocoder": "^2.4",
        "league/csv": "^8.0",
    },
    "require-dev": {
        "laravel-tinker": "dev-master"
    }
}
```

Sólo nos queda

```bash
composer install
composer install --no-dev  # para producción
# Paquetes y dependencias en la carpeta vendor
```

---

### Listo para Utilizar

```php
<?php
# Incluímos composer
require 'vendor/autoload.php';

# Composer encuentra las clases por nosotros
$collection = new Illuminate\Support\Collection([[1, 2, 3]]);
$writer = new League\Csv\Writer::createFromPath('fichero.csv');
$writer->insertAll($collection);
```

---

¿Donde encuentra composer las dependencias?

- Por defecto, en el repositorio oficial, **packagist.org**

---

### Composer.lock

Tras hacer una instalación inicial composer nos generará un fichero composer.lock

```
{
    "_readme": [
        "This file locks the dependencies of your project to a known state",
        "Read more about it at https://getcomposer.org/doc/01-basic-usage.md#composer-lock-the-lock-file",
        "This file is @generated automatically"
    ],
    "hash": "dd33e133dd4b85a5bb022156df0dc3a0",
    "content-hash": "f56d04d420c56813a824b3f7b68ff352",
    "packages": [
        {
            "name": "davejamesmiller/laravel-breadcrumbs",
            "version": "3.0.2",
            "source": {
                "type": "git",
                "url": "https://github.com/davejamesmiller/laravel-breadcrumbs.git",
                "reference": "6ca5a600003ecb52a5b5af14dad82033058604e1"
            },
...
```

---

Cuando hagamos *composer install* con un composer.lock presente, siempre nos instalará lo que indique
el composer.lock, no hará caso a composer.json.

<hr />

Si hemos modificado nuestro composer.json, añadido nuevas dependencias, o simplemente queremos actualizarlo
lanzaremos el comando

```composer update```
