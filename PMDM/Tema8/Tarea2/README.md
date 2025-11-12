# Tema 8. Servicios en red I

**Tarea 2: "API REST de Gestión de Productos"**

## 1. Introducción

En esta práctica vas a diseñar, programar y documentar una **API REST funcional** para gestionar un catálogo de productos utilizando **PHP** y **MySQL**.

El objetivo final es que domines la construcción completa de una API REST funcional, desde el diseño de la base de datos hasta la implementación de los diferentes endpoints.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica, deberás ser capaz de:

* Diseñar una base de datos MySQL normalizada para un dominio de negocio.
* Crear tablas y relaciones en MySQL con tipos de datos apropiados.
* Insertar datos de prueba en una base de datos.
* Implementar el patrón MVC en PHP para APIs REST.
* Crear clases modelo que representen entidades de negocio.
* Desarrollar controladores que gestionen la lógica de negocio.
* Implementar vistas JSON para formatear respuestas.
* Utilizar PDO para realizar consultas preparadas seguras.
* Gestionar diferentes tipos de peticiones GET con parámetros variables.
* Implementar peticiones POST para inserción de datos.
* Manejar excepciones personalizadas y códigos de estado HTTP.
* Configurar rutas con `.htaccess` para URLs amigables.
* Documentar y probar una API REST con herramientas apropiadas.


## 3. Descripción general del proyecto

La **API REST de Gestión de Productos** es un servicio web que permite realizar operaciones de consulta e inserción sobre un catálogo de productos almacenado en MySQL.

La API proporcionará los siguientes **endpoints**:

* `GET /productosapi/` - Obtiene todos los productos del catálogo.
* `GET /productosapi/{codigo}` - Obtiene un producto específico por su código.
* `GET /productosapi/categoria/{nombre_categoria}` - Obtiene todos los productos de una categoría.
* `GET /productosapi/precio/{min}/{max}` - Obtiene productos en un rango de precio.
* `POST /productosapi/` - Inserta un nuevo producto en el catálogo.

La estructura del proyecto seguirá el mismo patrón MVC que hemos visto en el ejemplo de personas, pero adaptado al dominio de productos.

## 4. Estructura de la tarea

La tarea se divide en **seis ejercicios secuenciales**, más una **fase final de documentación y pruebas**.

Sigue el orden indicado y guarda capturas de pantalla de cada paso.

***

### EJERCICIO 1. Diseño y creación de la base de datos

#### 1.1. Análisis del dominio

El catálogo de productos contendrá la siguiente información:

* **Código**: identificador único del producto (alfanumérico, ej: "PROD001").
* **Nombre**: nombre descriptivo del producto.
* **Descripción**: texto con información detallada del producto.
* **Precio**: precio unitario en euros (decimal con 2 decimales).
* **Categoría**: tipo de producto (Electrónica, Hogar, Deportes, etc.).
* **Stock**: cantidad disponible en almacén (número entero).
* **Activo**: indica si el producto está disponible para la venta (booleano).


#### 1.2. Crear la base de datos en MySQL

Abre **phpMyAdmin** o tu herramienta de gestión MySQL preferida y ejecuta el siguiente script SQL:

```sql
-- Crear la base de datos
CREATE DATABASE IF NOT EXISTS catalogo_productos 
CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Usar la base de datos
USE catalogo_productos;

-- Crear la tabla de productos
CREATE TABLE producto (
    codigo VARCHAR(20) PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT,
    precio DECIMAL(10,2) NOT NULL,
    categoria VARCHAR(50) NOT NULL,
    stock INT NOT NULL DEFAULT 0,
    activo BOOLEAN NOT NULL DEFAULT TRUE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```


#### 1.3. Insertar datos de prueba

Inserta los siguientes productos de ejemplo para poder probar la API:

```sql
INSERT INTO producto (codigo, nombre, descripcion, precio, categoria, stock, activo) VALUES
('PROD001', 'Portátil ASUS VivoBook', 'Portátil 15.6 pulgadas, Intel i5, 8GB RAM, 256GB SSD', 599.99, 'Electrónica', 15, TRUE),
('PROD002', 'Ratón Logitech MX Master 3', 'Ratón inalámbrico ergonómico con precisión avanzada', 89.99, 'Electrónica', 42, TRUE),
('PROD003', 'Teclado Mecánico Corsair K95', 'Teclado gaming RGB con switches Cherry MX', 179.99, 'Electrónica', 28, TRUE),
('PROD004', 'Monitor LG UltraWide 34"', 'Monitor curvo 34 pulgadas, resolución 3440x1440', 449.99, 'Electrónica', 12, TRUE),
('PROD005', 'Lámpara de escritorio LED', 'Lámpara ajustable con 3 niveles de intensidad', 34.99, 'Hogar', 67, TRUE),
('PROD006', 'Silla ergonómica Herman Miller', 'Silla de oficina con soporte lumbar ajustable', 899.99, 'Hogar', 8, TRUE),
('PROD007', 'Cafetera Nespresso Vertuo', 'Cafetera de cápsulas con sistema Centrifusion', 149.99, 'Hogar', 23, TRUE),
('PROD008', 'Zapatillas Nike Air Zoom', 'Zapatillas running con amortiguación reactiva', 129.99, 'Deportes', 35, TRUE),
('PROD009', 'Balón de baloncesto Spalding', 'Balón oficial NBA tamaño 7', 79.99, 'Deportes', 50, TRUE),
('PROD010', 'Esterilla de yoga premium', 'Esterilla antideslizante 6mm grosor', 29.99, 'Deportes', 88, TRUE),
('PROD011', 'Auriculares Sony WH-1000XM5', 'Auriculares Bluetooth con cancelación de ruido', 349.99, 'Electrónica', 19, TRUE),
('PROD012', 'Smartwatch Apple Watch Series 9', 'Reloj inteligente con GPS y monitor cardíaco', 449.99, 'Electrónica', 0, FALSE),
('PROD013', 'Mochila para portátil', 'Mochila acolchada para portátiles hasta 17 pulgadas', 49.99, 'Accesorios', 120, TRUE),
('PROD014', 'Botella térmica 1L', 'Botella de acero inoxidable mantiene temperatura 24h', 24.99, 'Accesorios', 200, TRUE),
('PROD015', 'Batidora de vaso Ninja', 'Batidora de 1200W con jarra de 2L', 119.99, 'Hogar', 14, TRUE);
```


#### 1.4. Verificar la estructura

Ejecuta las siguientes consultas para verificar que todo está correcto:

```sql
-- Ver la estructura de la tabla
DESCRIBE producto;

-- Contar el número de productos insertados
SELECT COUNT(*) AS total_productos FROM producto;

-- Ver todos los productos
SELECT * FROM producto;

-- Ver productos por categoría
SELECT categoria, COUNT(*) AS cantidad 
FROM producto 
GROUP BY categoria;
```

💡 **Importante:** Anota el nombre de la base de datos (`catalogo_productos`), ya que lo necesitarás en el archivo de configuración PHP.

**En el documento a entregar:**

* Incluye una captura de pantalla de phpMyAdmin mostrando la estructura de la tabla `producto`.
* Incluye una captura mostrando los 15 productos insertados.

***

### EJERCICIO 2. Estructura de carpetas y archivos de configuración

#### 2.1. Crear la estructura de directorios

Crea la siguiente estructura de carpetas en tu servidor web (dentro de `htdocs` o `www`):

```
productosapi/
├── .htaccess
├── index.php
├── controladores/
│   └── ControladorProductos.php
├── datos/
│   ├── ConexionBD.php
│   ├── login_mysql.php
│   └── mensajes.php
├── modelos/
│   └── Producto.php
├── utilidades/
│   └── ExcepcionApi.php
└── vistas/
    ├── VistaApi.php
    └── VistaJson.php
```


#### 2.2. Configurar el archivo `.htaccess`

Este archivo permite crear URLs amigables y redirigir todas las peticiones al archivo `index.php`.

Crea el archivo `.htaccess` en la raíz del proyecto con el siguiente contenido:

```apache
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php [QSA,L]
```


#### 2.3. Crear el archivo de configuración de base de datos

Crea el archivo `datos/login_mysql.php` con las credenciales de tu base de datos:

```php
<?php

/**
 * Este fichero provee las constantes para conectarse
 * a la base de datos MySQL.
 */

define("NOMBRE_HOST", "localhost");        // Nombre del host
define("BASE_DE_DATOS", "catalogo_productos");  // Nombre de la base de datos
define("USUARIO", "root");                 // Nombre del usuario
define("CONTRASENA", "");                  // Contraseña (vacía en XAMPP por defecto)
```

💡 **Nota:** Si estás usando un servidor remoto, deberás cambiar estos valores por las credenciales proporcionadas por tu proveedor de hosting.

#### 2.4. Crear el archivo de mensajes y estados

Crea el archivo `datos/mensajes.php` con las constantes de estado:

```php
<?php

if (!defined('ESTADO_CREACION_EXITOSA'))
    define('ESTADO_CREACION_EXITOSA', 1);
if (!defined('ESTADO_CREACION_FALLIDA'))
    define('ESTADO_CREACION_FALLIDA', 2);
if (!defined('ESTADO_ERROR_BD'))
    define('ESTADO_ERROR_BD', 3);
if (!defined('ESTADO_AUSENCIA_CLAVE_API'))
    define('ESTADO_AUSENCIA_CLAVE_API', 4);
if (!defined('ESTADO_CLAVE_NO_AUTORIZADA'))
    define('ESTADO_CLAVE_NO_AUTORIZADA', 5);
if (!defined('ESTADO_URL_INCORRECTA'))
    define('ESTADO_URL_INCORRECTA', 6);
if (!defined('ESTADO_FALLA_DESCONOCIDA'))
    define('ESTADO_FALLA_DESCONOCIDA', 7);
if (!defined('ESTADO_PARAMETROS_INCORRECTOS'))
    define('ESTADO_PARAMETROS_INCORRECTOS', 8);
```

**En el documento a entregar:**

* ¿Por qué es importante separar las credenciales de la base de datos en un archivo aparte?
* ¿Qué ventaja tiene usar constantes para los estados en lugar de números directamente en el código?

***

### EJERCICIO 3. Clases base y de soporte

#### 3.1. Crear la clase de excepción personalizada

Crea el archivo `utilidades/ExcepcionApi.php`:

```php
<?php

/**
 * Clase para manejar excepciones específicas de la API
 * Permite definir un estado personalizado y un código HTTP
 */
class ExcepcionApi extends Exception
{
    public $estado;

    /**
     * Constructor de la excepción
     * @param string $estado Estado personalizado de la excepción
     * @param string $mensaje Mensaje descriptivo del error
     * @param int $codigo Código HTTP (por defecto 400)
     */
    public function __construct($estado, $mensaje, $codigo = 400)
    {
        $this->estado = $estado;
        $this->message = $mensaje;
        $this->code = $codigo;
    }
}
```


#### 3.2. Crear las clases de vista

**Archivo `vistas/VistaApi.php`** (clase abstracta base):

```php
<?php

/**
 * Clase abstracta base para las vistas de la API
 */
abstract class VistaApi
{    
    // Código HTTP de respuesta
    public $estado;

    /**
     * Método abstracto que deben implementar las clases hijas
     * @param mixed $cuerpo Contenido a imprimir
     */
    public abstract function imprimir($cuerpo);
}
```

**Archivo `vistas/VistaJson.php`**:

```php
<?php

require_once "VistaApi.php";

/**
 * Clase para imprimir respuestas en formato JSON
 */
class VistaJson extends VistaApi
{
    /**
     * Constructor
     * @param int $estado Código HTTP de respuesta (por defecto 200)
     */
    public function __construct($estado = 200)
    {
        $this->estado = $estado;
    }

    /**
     * Imprime el cuerpo de la respuesta en formato JSON
     * y establece el código de respuesta HTTP
     * @param mixed $cuerpo Contenido a convertir a JSON
     */
    public function imprimir($cuerpo)
    {
        if ($this->estado) 
        {
            http_response_code($this->estado);
        }
        
        header('Content-Type: application/json;charset=utf-8');
        echo json_encode($cuerpo, JSON_UNESCAPED_UNICODE|JSON_PRETTY_PRINT);
        exit;
    }
}
```


#### 3.3. Crear la clase de conexión a base de datos

Crea el archivo `datos/ConexionBD.php`:

```php
<?php

/**
 * Clase Singleton para gestionar la conexión a la base de datos MySQL
 * Utiliza PDO para una conexión segura y eficiente
 */

require_once('login_mysql.php');

class ConexionBD
{
    const ESTADO_ERROR_BD = 3;

    /**
     * Instancia única de la clase (patrón Singleton)
     */
    private static $db = null;

    /**
     * Objeto PDO de conexión
     */
    private static $pdo;

    /**
     * Constructor privado para evitar instanciación directa
     */ 
    final private function __construct()
    {
        try 
        {
            // Crear nueva conexión PDO
            self::obtenerBD();
        } 
        catch (PDOException $e) 
        {
            // Manejo de excepciones
            throw new ExcepcionApi(ESTADO_ERROR_BD, $e->getMessage());
        }
    }

    /**
     * Retorna la única instancia de la clase
     * @return ConexionBD Instancia única
     */
    public static function obtenerInstancia()
    {
        if (self::$db === null) 
        {
            self::$db = new self();
        }
        return self::$db;
    }

    /**
     * Crea y retorna el objeto PDO de conexión
     * @return PDO Objeto PDO configurado
     */
    public function obtenerBD()
    {
        if (self::$pdo == null) 
        {
            self::$pdo = new PDO(
                'mysql:dbname=' . BASE_DE_DATOS .
                ';host=' . NOMBRE_HOST . ";",
                USUARIO,
                CONTRASENA,
                array(PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES utf8")
            );

            // Habilitar excepciones para errores PDO
            self::$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        }

        return self::$pdo;
    }

    /**
     * Evita la clonación del objeto
     */
    final protected function __clone() {}

    /**
     * Destructor: cierra la conexión
     */
    function _destructor()
    {
        self::$pdo = null;
    }
}
```

***

### EJERCICIO 4. Modelo y Controlador de Productos

#### 4.1. Crear la clase modelo Producto

Crea el archivo `modelos/Producto.php`:

```php
<?php

/**
 * Clase que representa un producto del catálogo
 */
class Producto
{
    // Atributos públicos del producto
    public $codigo;
    public $nombre;
    public $descripcion;
    public $precio;
    public $categoria;
    public $stock;
    public $activo;

    /**
     * Constructor de la clase
     * @param string $codigo Código único del producto
     * @param string $nombre Nombre del producto
     * @param string $descripcion Descripción detallada
     * @param float $precio Precio en euros
     * @param string $categoria Categoría del producto
     * @param int $stock Cantidad en almacén
     * @param bool $activo Si está disponible para venta
     */
    public function __construct($codigo, $nombre, $descripcion, $precio, $categoria, $stock, $activo)
    {
        $this->codigo = $codigo;
        $this->nombre = $nombre;
        $this->descripcion = $descripcion;
        $this->precio = $precio;
        $this->categoria = $categoria;
        $this->stock = $stock;
        $this->activo = $activo;
    }

    /**
     * Convierte el objeto a un array asociativo
     * @return array Representación del producto
     */
    public function toArray()
    {
        return [
            "codigo" => $this->codigo,
            "nombre" => $this->nombre,
            "descripcion" => $this->descripcion,
            "precio" => floatval($this->precio),
            "categoria" => $this->categoria,
            "stock" => intval($this->stock),
            "activo" => (bool)$this->activo
        ];
    }
}
```


#### 4.2. Crear el controlador de productos

Crea el archivo `controladores/ControladorProductos.php` con los siguientes métodos:

```php
<?php

require_once('datos/ConexionBD.php');
require_once('utilidades/ExcepcionApi.php');
require_once('datos/mensajes.php');

/**
 * Controlador para gestionar operaciones sobre productos
 */
class ControladorProductos
{
    // Nombres de la tabla y columnas
    const NOMBRE_TABLA = "producto";
    const CODIGO = "codigo";
    const NOMBRE = "nombre";
    const DESCRIPCION = "descripcion";
    const PRECIO = "precio";
    const CATEGORIA = "categoria";
    const STOCK = "stock";
    const ACTIVO = "activo";

    /**
     * Obtiene todos los productos del catálogo
     * @return array Respuesta con todos los productos
     */
    public function obtenerTodosProductos()
    {
        try 
        {
            $pdo = ConexionBD::obtenerInstancia()->obtenerBD();

            // Sentencia SELECT para obtener todos los productos
            $comando = "SELECT * FROM " . self::NOMBRE_TABLA . " ORDER BY " . self::NOMBRE;

            $sentencia = $pdo->prepare($comando);
            $sentencia->execute();

            $productos = array();

            while ($row = $sentencia->fetch(PDO::FETCH_ASSOC)) 
            { 
                array_push($productos, $row);
            }

            return [
                [
                    "estado" => "exito",
                    "mensaje" => "Productos obtenidos correctamente",
                    "datos" => $productos,
                    "total" => count($productos)
                ]
            ];
        } 
        catch (PDOException $e) 
        {
            throw new ExcepcionApi(ESTADO_ERROR_BD, $e->getMessage());
        }
    }

    /**
     * Obtiene un producto específico por su código
     * @param string $codigo Código del producto
     * @return array Respuesta con el producto encontrado
     */
    public function obtenerProductoPorCodigo($codigo)
    {
        try 
        {
            $pdo = ConexionBD::obtenerInstancia()->obtenerBD();

            // Sentencia SELECT con filtro por código
            $comando = "SELECT * FROM " . self::NOMBRE_TABLA . " " .
                       "WHERE " . self::CODIGO . " = ?";

            $sentencia = $pdo->prepare($comando);
            $sentencia->bindParam(1, $codigo);
            $sentencia->execute();

            $producto = $sentencia->fetch(PDO::FETCH_ASSOC);

            if (!$producto) 
            {
                throw new ExcepcionApi(
                    'producto_no_encontrado', 
                    "No se encontró el producto con código '$codigo'", 
                    404
                );
            }

            return [
                [
                    "estado" => "exito",
                    "mensaje" => "Producto encontrado",
                    "datos" => $producto
                ]
            ];
        } 
        catch (PDOException $e) 
        {
            throw new ExcepcionApi(ESTADO_ERROR_BD, $e->getMessage());
        }
    }

    /**
     * Obtiene todos los productos de una categoría específica
     * @param string $categoria Nombre de la categoría
     * @return array Respuesta con los productos de la categoría
     */
    public function obtenerProductosPorCategoria($categoria)
    {
        try 
        {
            $pdo = ConexionBD::obtenerInstancia()->obtenerBD();

            // Sentencia SELECT filtrando por categoría
            $comando = "SELECT * FROM " . self::NOMBRE_TABLA . " " .
                       "WHERE " . self::CATEGORIA . " = ? " .
                       "ORDER BY " . self::NOMBRE;

            $sentencia = $pdo->prepare($comando);
            $sentencia->bindParam(1, $categoria);
            $sentencia->execute();

            $productos = array();

            while ($row = $sentencia->fetch(PDO::FETCH_ASSOC)) 
            { 
                array_push($productos, $row);
            }

            if (empty($productos)) 
            {
                throw new ExcepcionApi(
                    'sin_resultados', 
                    "No se encontraron productos en la categoría '$categoria'", 
                    404
                );
            }

            return [
                [
                    "estado" => "exito",
                    "mensaje" => "Productos de la categoría '$categoria'",
                    "datos" => $productos,
                    "total" => count($productos)
                ]
            ];
        } 
        catch (PDOException $e) 
        {
            throw new ExcepcionApi(ESTADO_ERROR_BD, $e->getMessage());
        }
    }

    /**
     * Obtiene productos dentro de un rango de precio
     * @param float $precioMin Precio mínimo
     * @param float $precioMax Precio máximo
     * @return array Respuesta con los productos en el rango
     */
    public function obtenerProductosPorRangoPrecio($precioMin, $precioMax)
    {
        try 
        {
            // Validar que los precios sean números válidos
            if (!is_numeric($precioMin) || !is_numeric($precioMax)) 
            {
                throw new ExcepcionApi(
                    'parametros_invalidos', 
                    'Los precios deben ser valores numéricos', 
                    400
                );
            }

            if ($precioMin > $precioMax) 
            {
                throw new ExcepcionApi(
                    'rango_invalido', 
                    'El precio mínimo no puede ser mayor que el máximo', 
                    400
                );
            }

            $pdo = ConexionBD::obtenerInstancia()->obtenerBD();

            // Sentencia SELECT con filtro por rango de precio
            $comando = "SELECT * FROM " . self::NOMBRE_TABLA . " " .
                       "WHERE " . self::PRECIO . " BETWEEN ? AND ? " .
                       "ORDER BY " . self::PRECIO;

            $sentencia = $pdo->prepare($comando);
            $sentencia->bindParam(1, $precioMin);
            $sentencia->bindParam(2, $precioMax);
            $sentencia->execute();

            $productos = array();

            while ($row = $sentencia->fetch(PDO::FETCH_ASSOC)) 
            { 
                array_push($productos, $row);
            }

            if (empty($productos)) 
            {
                throw new ExcepcionApi(
                    'sin_resultados', 
                    "No se encontraron productos entre $precioMin€ y $precioMax€", 
                    404
                );
            }

            return [
                [
                    "estado" => "exito",
                    "mensaje" => "Productos entre $precioMin€ y $precioMax€",
                    "datos" => $productos,
                    "total" => count($productos),
                    "rango" => [
                        "minimo" => floatval($precioMin),
                        "maximo" => floatval($precioMax)
                    ]
                ]
            ];
        } 
        catch (PDOException $e) 
        {
            throw new ExcepcionApi(ESTADO_ERROR_BD, $e->getMessage());
        }
    }

    /**
     * Inserta un nuevo producto en la base de datos
     * @param Producto $producto Objeto producto a insertar
     * @return array Respuesta indicando el resultado
     */
    public function insertarProducto($producto)
    {
        try 
        {
            $pdo = ConexionBD::obtenerInstancia()->obtenerBD();

            // Verificar si ya existe un producto con ese código
            $comandoVerificar = "SELECT COUNT(*) as total FROM " . self::NOMBRE_TABLA . 
                               " WHERE " . self::CODIGO . " = ?";
            $sentenciaVerificar = $pdo->prepare($comandoVerificar);
            $sentenciaVerificar->bindParam(1, $producto->codigo);
            $sentenciaVerificar->execute();
            $resultado = $sentenciaVerificar->fetch(PDO::FETCH_ASSOC);

            if ($resultado['total'] > 0) 
            {
                throw new ExcepcionApi(
                    'codigo_duplicado', 
                    "Ya existe un producto con el código '{$producto->codigo}'", 
                    409
                );
            }

            // Sentencia INSERT
            $comando = "INSERT INTO " . self::NOMBRE_TABLA . " (" .
                self::CODIGO . "," .
                self::NOMBRE . "," .
                self::DESCRIPCION . "," .
                self::PRECIO . "," .
                self::CATEGORIA . "," .
                self::STOCK . "," .
                self::ACTIVO . ") VALUES (?, ?, ?, ?, ?, ?, ?)";

            $sentencia = $pdo->prepare($comando);
            $sentencia->bindParam(1, $producto->codigo);
            $sentencia->bindParam(2, $producto->nombre);
            $sentencia->bindParam(3, $producto->descripcion);
            $sentencia->bindParam(4, $producto->precio);
            $sentencia->bindParam(5, $producto->categoria);
            $sentencia->bindParam(6, $producto->stock);
            $activo = $producto->activo ? 1 : 0;
            $sentencia->bindParam(7, $activo);

            if ($sentencia->execute()) 
            {
                return [
                    [
                        "estado" => "exito",
                        "mensaje" => "Producto insertado correctamente",
                        "codigo" => $producto->codigo,
                        "nombre" => $producto->nombre
                    ]
                ];
            } 
            else 
            {
                throw new ExcepcionApi(
                    'error_insercion', 
                    'No se pudo insertar el producto', 
                    500
                );
            }
        } 
        catch (PDOException $e) 
        {
            throw new ExcepcionApi(ESTADO_ERROR_BD, $e->getMessage(), 500);
        }
    }
}
```

**En el documento a entregar:**

* Explica la diferencia entre usar `bindParam()` y concatenar directamente las variables en la consulta SQL. ¿Qué tipo de ataque previene el uso de consultas preparadas (prepared statements)?
* Explica por qué es importante verificar si un código ya existe antes de insertar un nuevo producto.

***

### EJERCICIO 5. Enrutador principal (index.php)

Este archivo es el punto de entrada de la API. Recibe todas las peticiones, las analiza y las dirige al método del controlador correspondiente.

Crea el archivo `index.php`:

```php
<?php

// Mostrar errores en desarrollo (desactivar en producción)
ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);
error_reporting(E_ALL);

// Incluir archivos necesarios
require_once 'controladores/ControladorProductos.php';
require_once 'modelos/Producto.php';
require_once 'vistas/VistaJson.php';
require_once 'utilidades/ExcepcionApi.php';

// Crear la vista JSON con código 200 por defecto
$vista = new VistaJson(200); 

// Configurar manejador global de excepciones
set_exception_handler(function ($e) use ($vista) {
    $estado = $e instanceof ExcepcionApi ? $e->estado : 'error';
    $codigo = $e->getCode() ?: 500;
    $vista->estado = $codigo;
    $vista->imprimir([
        [
            "estado" => $estado, 
            "mensaje" => $e->getMessage()
        ]
    ]);
});

// Obtener el método HTTP de la petición
$metodo = $_SERVER['REQUEST_METHOD'];

// Obtener y procesar la ruta de la petición
$peticion = explode('/', trim($_SERVER['REQUEST_URI'], '/'));
$recurso = array_shift($peticion); // Primer segmento de la URL

// Normalizar el nombre del recurso (permitir variaciones)
if ($recurso === 'productosapi' || $recurso === 'productos') {
    $recurso = 'productos';
} else {
    throw new ExcepcionApi(
        'recurso_no_encontrado', 
        "Recurso no encontrado: $recurso. Use '/productosapi/'", 
        404
    );
}

// Crear instancia del controlador
$controlador = new ControladorProductos();

// Procesar la petición según el método HTTP
switch ($metodo) {
    case 'GET':
        // Analizar los segmentos de la URL para determinar la acción
        if (empty($peticion)) {
            // GET /productosapi/ --> Obtener todos los productos
            $vista->estado = 200;
            $vista->imprimir($controlador->obtenerTodosProductos());
            
        } elseif ($peticion[0] === 'categoria' && isset($peticion[1])) {
            // GET /productosapi/categoria/{nombre} --> Filtrar por categoría
            $categoria = urldecode($peticion[1]);
            $vista->estado = 200;
            $vista->imprimir($controlador->obtenerProductosPorCategoria($categoria));
            
        } elseif ($peticion[0] === 'precio' && isset($peticion[1]) && isset($peticion[2])) {
            // GET /productosapi/precio/{min}/{max} --> Filtrar por rango de precio
            $precioMin = $peticion[1];
            $precioMax = $peticion[2];
            $vista->estado = 200;
            $vista->imprimir($controlador->obtenerProductosPorRangoPrecio($precioMin, $precioMax));
            
        } else {
            // GET /productosapi/{codigo} --> Obtener producto por código
            $codigo = urldecode($peticion[0]);
            $vista->estado = 200;
            $vista->imprimir($controlador->obtenerProductoPorCodigo($codigo));
        }
        break;

    case 'POST':
        // POST /productosapi/ --> Insertar nuevo producto
        
        // Leer el cuerpo de la petición (JSON)
        $data = json_decode(file_get_contents('php://input'), true);
        
        if (!$data) {
            throw new ExcepcionApi(
                'json_invalido', 
                'El JSON enviado es inválido o está vacío', 
                400
            );
        }

        // Validar campos obligatorios
        $camposRequeridos = ['codigo', 'nombre', 'precio', 'categoria'];
        foreach ($camposRequeridos as $campo) {
            if (!isset($data[$campo]) || empty($data[$campo])) {
                throw new ExcepcionApi(
                    'campo_requerido', 
                    "El campo '$campo' es obligatorio", 
                    400
                );
            }
        }

        // Crear objeto Producto desde el JSON recibido
        $producto = new Producto(
            $data['codigo'],
            $data['nombre'],
            $data['descripcion'] ?? '',
            $data['precio'],
            $data['categoria'],
            $data['stock'] ?? 0,
            $data['activo'] ?? true
        );

        // Insertar el producto
        $vista->estado = 201; // 201 Created
        $vista->imprimir($controlador->insertarProducto($producto));
        break;

    case 'PUT':
    case 'DELETE':
        throw new ExcepcionApi(
            'metodo_no_implementado', 
            "El método $metodo no está implementado en esta versión", 
            501
        );
        break;

    default:
        throw new ExcepcionApi(
            'metodo_no_permitido', 
            'Método HTTP no permitido', 
            405
        );
}
```

**En el documento a entregar:**

* Explica qué hace la función `urldecode()` y por qué es necesaria.
* ¿Por qué leemos el cuerpo de la petición POST con `file_get_contents('php://input')` en lugar de usar `$_POST`?

***

### EJERCICIO 6. Pruebas de la API

Ahora que la API está completa, es momento de probarla exhaustivamente. Utilizaremos **Postman** (o una alternativa como **Thunder Client** en VS Code, o **cURL** desde la línea de comandos).

#### 6.1. Instalar Postman (si no lo tienes)

1. Descarga Postman desde [https://www.postman.com/downloads/](https://www.postman.com/downloads/)
2. Instálalo y crea una cuenta gratuita (opcional pero recomendado).

#### 6.2. Configurar el entorno de pruebas

Asegúrate de que tu servidor local (XAMPP, WAMP, MAMP, etc.) esté ejecutándose.

La URL base de tu API será algo como:

- `http://localhost/productosapi/`
- `http://localhost:8080/productosapi/` (si usas un puerto diferente)


#### 6.3. Realizar las siguientes pruebas

Realiza cada una de estas peticiones y **guarda capturas de pantalla** de las respuestas:

##### Prueba 1: Obtener todos los productos

```
Método: GET
URL: http://localhost/productosapi/
```

**Resultado esperado:** JSON con los 15 productos y código HTTP 200.

##### Prueba 2: Obtener un producto específico por código

```
Método: GET
URL: http://localhost/productosapi/PROD005
```

**Resultado esperado:** JSON con los datos de la "Lámpara de escritorio LED" y código 200.

##### Prueba 3: Obtener producto inexistente

```
Método: GET
URL: http://localhost/productosapi/PROD999
```

**Resultado esperado:** Mensaje de error "producto_no_encontrado" y código 404.

##### Prueba 4: Obtener productos por categoría

```
Método: GET
URL: http://localhost/productosapi/categoria/Electrónica
```

**Resultado esperado:** JSON con los productos de la categoría "Electrónica" y código 200.

##### Prueba 5: Obtener productos por categoría inexistente

```
Método: GET
URL: http://localhost/productosapi/categoria/Juguetes
```

**Resultado esperado:** Mensaje "sin_resultados" y código 404.

##### Prueba 6: Obtener productos por rango de precio

```
Método: GET
URL: http://localhost/productosapi/precio/50/150
```

**Resultado esperado:** JSON con productos entre 50€ y 150€ (debería devolver 6 productos) y código 200.

##### Prueba 7: Rango de precio inválido

```
Método: GET
URL: http://localhost/productosapi/precio/200/100
```

**Resultado esperado:** Error "rango_invalido" y código 400.

##### Prueba 8: Insertar un nuevo producto que contenga tu nombre (POST)

```
Método: POST
URL: http://localhost/productosapi/
Headers: Content-Type: application/json
Body (raw JSON):
{
    "codigo": "PROD016",
    "nombre": "Cable HDMI 2.1 --Incluye aquí tu nombre--",
    "descripcion": "Cable HDMI de 2 metros compatible con 4K 120Hz",
    "precio": 19.99,
    "categoria": "Accesorios",
    "stock": 150,
    "activo": true
}
```

**Resultado esperado:** Mensaje de éxito "Producto insertado correctamente" y código 201.

##### Prueba 9: Verificar que el producto insertado existe

```
Método: GET
URL: http://localhost/productosapi/PROD016
```

**Resultado esperado:** JSON con los datos del cable HDMI recién insertado.

##### Prueba 10: Intentar insertar producto con código duplicado

```
Método: POST
URL: http://localhost/productosapi/
Body (raw JSON):
{
    "codigo": "PROD016",
    "nombre": "Otro producto",
    "precio": 99.99,
    "categoria": "Test"
}
```

**Resultado esperado:** Error "codigo_duplicado" y código 409 (Conflict).

##### Prueba 11: Insertar producto sin campos obligatorios

```
Método: POST
URL: http://localhost/productosapi/
Body (raw JSON):
{
    "codigo": "PROD017",
    "nombre": "Producto incompleto"
}
```

**Resultado esperado:** Error "campo_requerido" indicando que falta el campo "precio" y código 400.

##### Prueba 12: Enviar JSON mal formado

```
Método: POST
URL: http://localhost/productosapi/
Body (raw JSON):
{
    "codigo": "PROD018"
    "nombre": "Falta una coma"
}
```

**Resultado esperado:** Error "json_invalido" y código 400.

**En el documento a entregar:**

* Incluye **capturas de pantalla** de Postman para las 12 pruebas realizadas.
* En cada captura debe verse:
    - La URL completa
    - El método HTTP
    - El cuerpo de la petición (si aplica)
    - El código de estado HTTP de la respuesta
    - El JSON de respuesta completo

***

## 5. Documentación final

Además de las respuestas a las preguntas de cada ejercicio, tu documento debe incluir una conclusión final de lo que has aprendido en esta tarea.



## Entrega

Debes entregar **un archivo comprimido (.zip)** que contenga:

1. **Carpeta `productosapi/`** con todo el código PHP de la API.
2. **Documento PDF** con toda la documentación, capturas y respuestas (nombre del archivo: `TuApellido_TuNombre_API_Productos.pdf`).