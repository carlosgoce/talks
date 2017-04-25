---
title: PHP Traits
---

# Traits
### En 5 minutos
###### phpvigo

<p>[@carlosgoce](https://twitter.com/carlosgoce)</p>
<small>Developer @ Initios</small>


---

## ¿Trait?

* Mecanismo de reutilización de código (phpnet)
* Disponible desde PHP 5.4


---

### Ejemplo básico

```php
trait MiPrimerTrait {
    public $mensaje = 'Hola';

    public function saludar() {
        echo $this->mensaje;
    }
}
```

```php
class Persona {
    use MiPrimerTrait;
}

$persona = new Persona();
$persona->saludar();
# Hola
```


---

##### Código duplicado sin traits

```php
class ProductController extends BaseController
{
    public function index()
    {
        return view('product.index', \Product:all());
    }
}
```

```php
class UserController extends BaseController
{
    public function index()
    {
        return view('user.index', \User:all());
    }
}
```

---

### Refactor con traits

```php
trait Indexable {
    public function index() {
        $all = $this->model::all();
        return view($this->model . '.index', $all);
    }
}

class ProductController extends BaseController
{
    use App\Traits\Indexable;
    protected $model = 'product';
}

class UserController extends BaseController
{
    use App\Traits\Indexable;
    protected $model = 'user';
}
```

---

#### CRUD Controller con Traits


```php

class ProductController extends BaseController
{
    use App\Traits\Indexable;
    use App\Traits\Editable;
    use App\Traits\Storable;
    use App\Traits\Deletable;

    $model = 'product';
}

```

---

### Traits que usan traits

```php
namespace \App\Traits;

trait CRUDable {
    use Indexable;
    use Editable;
    use Storable;
    use Deletable;
}
```

```php
use App\Traits\CRUDable;

class ProductController extends BaseController {
    use CRUDable;
    $model = \Product::class;
}
```

---

#### Ejemplo avanzado

```php
trait Cacheable
{
    public function __call($name, $arguments)
    {
        $cacheKey = get_class($this) . $name;

        if ( array_key_exists($name, $this->cacheableMethods) ) {
            return Cache::getOrSet($cacheKey, $this->minutes, function() { ... });
        }

        return call_user_func_array(array($this, $name), $arguments);
    }
}
```

```php
<?php
class User extends SomeORM {
    use Cacheable;

    public $cacheableMethods = ['comments';

    public function comments() { # consulta muy lenta }
}
```

---

### Ejemplos Laravel

- Authorizable
- CanResetPassword
- Confirmable
- HasNotifications
- Macroable
- Notifiable
- RedirectsUsers
- SoftDeletes

Etc etc etc.

---

### Sobreescritura

```php
class RestrictedUser
{
    use Authorizable {
        can as parentCan;
    };

    public function can($ability, $arguments = array()) {
        if ($ability === 'delete post') {
            return false;
        }

        return $this->parentCan($ability, $arguments);
    }
}
```

---

## Gracias

##### https://github.com/carlosgoce/talks

[Carlos González Goce](https://twitter.com/carlosgoce)
@ PHPVigo, Abril 2017
