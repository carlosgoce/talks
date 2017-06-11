---
title: ¡NO sin Composer!
---

# ¡No sin Composer!


<p>[@carlosgoce](https://twitter.com/carlosgoce)</p>
<small>Developer @ Initios</small>

---

## Mapa

1. Autocarga ficheros PHP (no más require/include)
    * Estrategias
    * Namespaces y [PSR-4](http://www.php-fig.org/psr/psr-4/)
2. Gestionar depenencias
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

---
