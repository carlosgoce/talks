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

# Mapa

---

1. Autocarga ficheros PHP
    * Estrategias
    * Namespaces y [PSR-4](http://www.php-fig.org/psr/psr-4/)
2. Gestionar dependencias
    * Composer.lock
    * [Packagist](https://packagist.org/)
3. Crear nuestras propias librerías
    * [Versionamiento Semántico](http://semver.org/lang/es/)
    * Publicación
4. Bonus

---

# ¿Composer?

Algunos gestores de dependencias

<table>
    <tr><th>NodeJS</th><td>npm</td><td>2010</td></tr>
    <tr><th>Ruby</th><td>rubygems</td><td>2004</td></tr>
    <tr><th>Python</th><td>pip</td><td>2011</td></tr>
    <tr><th>...</th><td>...</td><td>...</td></tr>
    <tr><th>PHP</th><td>pear</td><td>1999</td></tr>
    <tr><th>PHP</th><td>composer</td><td>2012</td></tr>
</table>

---

# Instalación

---

### Unix

```bash
curl -sS https://getcomposer.org/installer | php

# Lo movemos al $PATH
mv composer.phar /usr/local/bin/composer
```

```bash

➤ composer
   ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 1.4.2 2017-05-17 08:17:52
```

---

### Windows

[composer.exe](https://getcomposer.org/Composer-Setup.exe)

---

# Autocarga

---

## Método *Old School*

```bash
- project
  - index.php
  - helpers.php
  - queries.php
```

```php
<?php # index.php

# Primero cargamos las librerías

require_once 'helpers.php';
require_once 'queries.php';

# Código del proyecto
```

<aside class="notes">
    No hay forma de cargar únicamente una clase u otra en función de la request.
    Siempre se cargan todos los ficheros.
</aside>

---

## Método Composer

Define en composer.json la estrategia/s de autocarga

```json
{
    "autoloading": {
        "classmap": ["models/", "mislibrerias/"],
        "files": ["helpers/consultas.php"]
    }
}
```

---

## Inicializa Composer

```php
<?php  # index.php
require 'vendor/autoload.php';

# La clase Query es cargada automáticamente
Query::products->all();
```


---

# Estrategias

- Classmap
- Files
- PSR-4

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

Busca clases escaneando los ficheros de las carpetas y/o ficheros indicados.

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

<small>Usar con cautela, estos ficheros se cargan <u>siempre.</u></small>

---

### Estrategias
##### PSR-4

Método recomendado para cargar clases

```json
{
    "autoload": {
        "psr-4": { "MyLibrary\\": "src/" }
    }
}
```

```
<?php # src/Service/EmailService.php
class MyLibrary\Service\EmailService {
    ...
}
```

<small>Especificación en [php-fig](http://www.php-fig.org/psr/psr-4/)</small>

---

### Refrescar Autoloading

```
composer dump-autoload
composer dump-autoload --optimize  # para producción
```

---

### Namespaces

```
<?php
require 'libs/SwiftMailer/Message.php';
require 'entidades/Message.php';

# Podríamos estar cargando 2 clases con mismo nombre
class Message {}
class Message {}

new Message();
```

<small>
> Fatal error: Cannot redeclare class Message..
</small>

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

new Message();
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

<aside class="notes">
    <p>¿En qué ruta deberían estar los ficheros?</p>
    <p>¿Como lanzaríamos una excepción en fichero C?</p>
</aside>

---

# Gestión de dependencias

---

## Método *Old School*

> Nos bajamos un zip de "por ahí" y lo descomprimimos en nuestro proyecto

Problemas:
  - ¿Cómo actualizarlo?
  - ¿Cómo reutilizarlo en otros proyectos?
  - ¿Cómo instalar dependencias de dependencias?
  - ...

---

## Método Composer

<small>composer.json</small>

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

<small>Instalación</small>

```bash
composer install
composer install --no-dev  # para producción
# Paquetes y dependencias en la carpeta vendor
```

---

### Importamos el autoloader de Composer

y a trabajar


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

### [Limitando versiones](https://getcomposer.org/doc/articles/versions.md)

<table>
    <tr><td>Exacta</td><td>1.0.2</td></tr>
    <tr><td>Rango</td><td>>1.0.2 <2.0.0</td></tr>
    <tr><td>Rango (con -)</td><td>1.0 - 2.0</td></tr>
    <tr><td>Comodín</td><td>1.0.*</td></tr>
    <tr><td>Tilde</td><td>~1.2</td></tr>
    <tr><td>Caret (recomendado)</td><td>^1.2</td></tr>
</table>

---

### Composer.lock

<small>Generado tras una instalación o actualización</small>

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

<aside class="notes">
Cuando hagamos *composer install* con un composer.lock presente, siempre nos instalará lo que indique
el composer.lock, no hará caso a composer.json.

<hr />

Si hemos modificado nuestro composer.json, añadido nuevas dependencias, o simplemente queremos actualizarlo
lanzaremos el comando **composer update**
</aside>

---

# packagist.org

El repositorio oficial de composer.

<img data-src="resources/packagist-logo.png" style="width: 600px;">

---

# Crear tu propia librería

Y subirla a packagist.org

---

### composer.json para librerías

##### Ejemplo extraído de [league/csv](http://csv.thephpleague.com)

```json

{
    "name": "league/csv",
    "type": "library",
    "description" : "Csv data manipulation made easy in PHP",
    "keywords": ["csv", "import", "export", "read"],
    "license": "MIT",
    "homepage" : "http://csv.thephpleague.com",
    "require": {
        "php" : ">=7.0.10"
        ...
    },
    "require-dev": {
        "phpunit/phpunit" : "^6.0",
        ...
    },
    "autoload": {
        "psr-4": {
            "League\\Csv\\": "src"
        },
        "files": ["src/functions_include.php"]
    },
    "autoload-dev": {
        "psr-4": {
            "LeagueTest\\Csv\\": "tests"
        }
    },
    "scripts": {
        "test": "phpunit --coverage-text; php-cs-fixer fix -v --diff --dry-run --allow-risky=yes;",
        "phpunit": "phpunit --coverage-text",
        "phpcs": "php-cs-fixer fix -v --diff --dry-run --allow-risky=yes;"
    },
    "suggest": {
        "ext-iconv" : "Needed to ease transcoding CSV using iconv stream filters"
    }
}
```

---

### Ingredientes extra

- Imprescindible si quieres que otros lo utilicen
  - [Documentación](http://csv.thephpleague.com/8.0/)
  - [Tests](https://github.com/thephpleague/csv/tree/master/tests)
  - [Versionamiento Semántico](http://semver.org/lang/es/)

- Extra points
  - [Badges](https://github.com/thephpleague/csv)
  - [Changelog](http://csv.thephpleague.com/upgrading/changelog/)
  - [Licencia](https://choosealicense.com/)
  - [Web](http://csv.thephpleague.com/)

---

### Publicar en packagist.org

#### 3 Simples pasos:

1. Sube tu código a [github](github.com)
2. Revisa las [normas y requisitos](https://packagist.org/about#naming-your-package)
3. [Envía tu repositorio](https://packagist.org/packages/submit)

---

# Composer CLI

---

```bash
composer <comando>
```

<table style="font-size: 30px;">
    <thead>
        <tr><th>comando</th><th>descripción</th></tr>
    </thead>
    <tbody>
        <tr><td>install</td><td>instala a partir de un composer.lock o genera uno</td></tr>
        <tr><td>update</td><td>actualiza a las últimas versiones y actualiza el composer.lock</td></tr>
        <tr><td>init</td><td>asistente para generar un composer.json</td></tr>
        <tr><td>dump</td><td>regenera ficheros de autocarga</td></tr>
        <tr><td>require</td><td>añade un paquete como dependencia a tu composer.json</td></tr>
        <tr><td>validate</td><td>verifica si tu composer.json es válido</td></tr>
    </tbody>
</table>

<smal>[Composer Cheatsheet](http://composer.json.jolicode.com/)</small>

---

# Thank you!

- 20-06-2017 PHPVigo
