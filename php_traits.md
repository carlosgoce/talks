<!-- $theme: gaia -->

Traits
======

En 5 minutos



- Ejemplos de código son pseudocódigo el objetivo principal es mostrar como utilizar los Trait


##### by [@carlosgoce](https://twitter.com/carlosgoce)

###### Initios


---

# ¿Que es un trait?


>  mecanismo de reutilización de código

- PHP >= 5.4
- Traducibles como `rasgos` (phpnet) / `tratos`

Pero mejor veámoslo con ejemplos

---

### Típico controlador

```php
class ProductController extends BaseController
{
    public function index()
    {
        return view('product.index', \Product:all());
    }
}

# Código duplicado

class UserController extends BaseController
{
    public function index()
    {
        return view('user.index', \User:all());
    }
}
```

---

### Con traits

```php
trait Indexable { # Sufijo `able`
    public function index() {
        $all = $this->model::all();
        return view(get_class($this->name) . '.index');
    }
}

class ProductController extends BaseController
{
    use App\Traits\Indexable;
    protected $name = \Product::class;
}

class UserController extends BaseController
{
    use App\Traits\Indexable;
    protected $name = \User::class;
}
```

---

### ¿Por qué no?


```php

class ProductController extends BaseController
{
    use App\Traits\Indexable;
    use App\Traits\Editable;
    use App\Traits\Storable;
    use App\Traits\Deletable;
    
    $model = \Product::class;
}

```

---

### Traits que usan traits

```php
trait CRUDable {
    use App\Traits\Indexable;
    use App\Traits\Editable;
    use App\Traits\Storable;
    use App\Traits\Deletable;
}
```


```php
class ProductController extends BaseController {
    use App\Traits\CRUDable;
    $model = \Product::class;
}
```

---

# Interfaces
##### Pequeño recordatorio de interfaces (más conocidas)

- Qué implementar, no cómo. Nos obliga a cumplir su contrato

```php
# Interfaz BuiltIn
Countable {
    abstract public int count ( void )
}

class Vehiculo implements Countable {
    $ruedas = 4;

    public function count() 
    {
        return $this->numeroRuedas;
    }
}
```

> *Notice: Ruler (`<hr>`) is not displayed in Marp.*

---

## Gracias! :smile:

### https://github.com/carlosgoce/talks

&copy; 2017 [Carlos González Goce](https://twitter.com/carlosgoce)
PHPVigo, Abril 2017