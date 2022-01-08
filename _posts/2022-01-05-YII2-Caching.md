---
title: Caché en Yii2
published: true
---


Para entender porque necesitamos implementar caché en nuestras web, entendamos primero que pasa cuando interactuamos con un servidor web. 

Cada vez que se realiza una *request* a una aplicación web suceden un conjunto de cosas en tu servidor, por ejemplo:

- Probablemente se conectará a una o varias bases de datos.
- También puede que se accedan a archivos locales o se realicen cálculos matemáticos, generando una carga en la CPU.

Es decir, cada *request* tiene una cierta cantidad de demanda de CPU y disco de red y, en algunas aplicaciones especializadas, incluso puede haber otras cosas involucradas.

Todas esas cosas son las que afectan el tiempo que pasa entre que las *requests* llegan al servidor web y éste pueda generar la respuesta y ser enviada al usuario. Por lo tanto cuanto más disco o CPU se use y cuanto más se acceda a una base de datos, más lenta será la respuesta de su servidor. 

Si se realiza otra *request* para ese mismo contenido, en la mayoría de los casos ocurrirá exactamente lo mismo y su servidor tendrá que manejar ese acceso al disco en esa red y esa sobrecarga de CPU requerida para la *request* por segunda o tercera vez o varias veces. ¿Qué ocurre cuando algunos de estos recursos comienza a acercarse a su límite? Se empezará a ralentizar o provocará un error. Por lo tanto, si la CPU alcanza los límites de su servidor, si el acceso al disco es tan lento que las cosas comienzan a agotarse o si la carga de su base de datos es tan pesada que nuevamente obtiene un error de *time out* o incluso el servidor web podría agotar el *time out* esperando la base de datos responder.


La forma más fácil de reducir el uso de estos recursos es almacenar en caché los datos cuando se generan en la memoria. De esta manera el objetivo es que se pueden atender la mayor cantidad de *requests* posibles directamente desde la memoria, minimizando el uso de CPU o disco de red y el acceso a la base de datos. 

Esto tendrá algunos costos:
 - Su código será un poco más complejo, no demasiado, pero tendrá que escribir un código adicional.
 - Requerirá memoria adicional y eso podría tener un efecto en el tipo de servidor que puede usar para su servidor. Dependiendo de qué es exactamente lo que vas a almacenar en caché, es posible que no requieras mucha memoria.


Si está diseñando un servicio web para que miles de usuarios seguramente la caché será uno de los mecanismo que utilice. Ya que cuantas más *requests* pueda manejar con los mismos recursos, menos servidores necesitará, lo cual es más barato y también podría significar que puede usar una sola base de datos para su aplicación en lugar de múltiples bases de datos. 


Ahora yendo particularmente a Yii2, el *caching* es una de las características que el *framework* provee. Veamos los diferentes mecanismos de almacenamiento en caché y como configurarlos.

## [](#header-2)Claves de Caché

Es importante tener en cuenta que cada elemento de datos almacenado en caché se identifica por una **clave**. Cuando se almacena un elemento de datos en la memoria caché, se debe especificar una clave. Más tarde, cuando se recupera el elemento de datos de la memoria caché, se debe proporcionar la clave correspondiente.

Puedes utilizar una cadena o un valor arbitrario como una clave de caché. Cuando una clave no es una cadena de texto, ésta será automáticamente serializada en una cadena.

Una estrategia común para definir una clave de caché es incluir en ella todos los factores determinantes en términos de un array. Por ejemplo, ```yii\db\Schema``` utiliza la siguiente clave para almacenar en caché la información del esquema de una tabla de base de datos:

```php
[
    __CLASS__,              // nombre de la clase del esquema
    $this->db->dsn,         // nombre del origen de datos de la conexión BD
    $this->db->username,    // usuario para la conexión BD
    $name,                  // nombre de la tabla
];
```
Como puedes ver, la clave incluye toda la información necesaria para especificar de una forma exclusiva una tabla de base de datos.

Cuando en un mismo almacenamiento en caché es utilizado por diferentes aplicaciones, se debería especificar un prefijo único para las claves de la caché por cada una de las aplicaciones para así evitar conflictos.Por ejemplo, en la configuración de la aplicación podrías escribir el siguiente código:

```php
'components' => [
    'cache' => [
        'class' => 'yii\caching\ApcCache',
        'keyPrefix' => 'myapp',       // un prefijo de clave de caché único
    ],
],
```

## [](#header-2)Formas de invalidar un elemento de caché 

Un elemento de datos almacenado en la memoria caché permanecerá en ella para siempre, a menos que sea removida de alguna manera debido a alguna directiva de caché (ej. el espacio de almacenamiento en caché está lleno y los datos más antiguos se eliminan). Para cambiar este comportamiento Yii provee diferentes mecanismos que se envían por parámetro al guardar el elemento de dato para invalidar un elemento de caché:

### [](#header-3)Caducidad de Caché

El parámetro nos indica por cuántos segundos el elemento se mantendrá válido en memoria caché. Cuando llames get() para recuperar el elemento, si el tiempo de caducidad ha pasado, el método devolverá ```false```, indicando que el elemento de datos no ha sido encontrado en la memoria caché.


### [](#header-3)Dependencias

Los datos almacenados en caché pueden también ser invalidados conforme a algunos cambios en la caché de dependencias, donde las disponibles son:

- yii\caching\ChainedDependency: la dependencia cambia si cualquiera de las dependencias en la cadena cambia.
- yii\caching\DbDependency: la dependencia cambia si el resultado de la consulta de la sentencia SQL especificada cambia.
- yii\caching\ExpressionDependency: la dependencia cambia si el resultado de la expresión de PHP especificada cambia.
- yii\caching\FileDependency: la dependencia cambia si se modifica la última fecha de modificación del archivo.
- yii\caching\TagDependency: marca un elemento de datos en caché con uno o más *tags*. Cada *tag* puede ser invalidado llamando a yii\caching\TagDependency::invalidate(), de manera que invalidaría a todos los elementos de datos que contienen dicho *tag*.

## [](#header-2)Mecanismos de caché

### [](#header-3)Caché de datos

Es el mecanismo más básico y suele ser la base de los demás mecanismos. Trata del almacenamiento de alguna variable PHP en caché y recuperarla más tarde.

El siguiente código muestra el patrón típico de uso para el almacenamiento en caché:

```php
// intenta recuperar $data de la caché
$data = $cache->get($key);

if ($data === false) {

    // $data no ha sido encontrada en la caché, calcularla desde cero
    $data = Products::findOne($id);
    // guardar $data en caché para así recuperarla la próxima vez
    $cache->set($key, $data);
}

// $data está disponible aquí
```

El siguiente código se puede escribir también como:

```php
$data = $cache->getOrSet($key, function () use ($id) {
        return Products::findOne($id);
    });
```

### [](#header-3)Caché de consultas

La caché de consultas es una característica especial de caché construido sobre el almacenamiento de caché de datos. Se proporciona para almacenar en caché el resultado de consultas a la base de datos.

Las consultas en caché requieren una ```DB connection``` y un componente de aplicación caché válido. Las implementaciones de caché de consultas podemos verlos en los siguientes códigos para los diferentes casos:

#### [](#header-4)Caché de *raw query*

```php
$db = Client::getDb();
$result = $db->cache(function ($db) use ($id) {
    $query = new Yii\db\Query;
    $query->select("client.*")
        ->from('client')
        ->where(['client.id' => $id])
        ->one();
    $command = $query->createCommand();
    $result  = $command->queryAll();
    return $result;
});
```

#### [](#header-4)Caché de ActiveRecord

```php
$result = 
$db = Yii::$app->db;// or Category::getDb()
$dep = new DbDependency();
$dep->sql = 'SELECT count(*) FROM category';
$result = $db->cache(function ($db) use ($id) {
    return Category::find()->where(['id' => $id])->all();
}, CACHE_TIMEOUT, $dep);
```

##### [](#header-5)Caché en ActiveQuery ->all() y ->one() 

Puede habilitar fácilmente el almacenamiento en caché de todas las consultas para una tabla determinada siguiendo el ejemplo siguiente. Digamos que tenemos una tabla de **clientes** y deseamos almacenar en caché los datos del cliente durante 10 minutos (600 segundos). Primero agregue un archivo **ClientQuery.php** en el mismo directorio que su modelo **Client.php** y configure ```$queryCacheDuration``` en segundos: 

```php
<?php

namespace app\models\application;

/**
 * This is the ActiveQuery class for [[Client]].
 *
 * @see Client
 */
class ClientQuery extends Yii\db\ActiveQuery
{
	public $queryCacheDuration = 600;
}
```

Luego agrega un método find() en tu modelo para que haga referencia a **ClientQuery**:

```php
/**
 * @inheritdoc
 * @return ClientQuery the active query used by this AR class.
 */
public static function find()
{
	return new ClientQuery(get_called_class());
}
```

Alternativamente, puede anular las rutinas all () y one () y configurar el uso de la caché allí (nuevamente, en su archivo **ClientQuery.php**): 

```php
<?php

namespace app\models\application;

/**
 * This is the ActiveQuery class for [[Client]].
 *
 * @see Client
 */
class ClientQuery extends Yii\db\ActiveQuery
{
    public function all($db = null)
    {
        $this->cache(60);
        return parent::all($db);
    }

    public function one($db = null)
    {
        $this->cache(60);
        return parent::one($db);
    }
}
```

##### [](#header-5)Caché de una consulta basada en relaciones

La acción en el controlador (sin caché) es:

```php
$model = $this->findModel($id);
$issues = $model->getCurrentIssues()
    ->orderBy('ID DESC')
    ->all();
$retVal = $this->renderAjax('issues/issues', [
    'model' => $model,
    'issues' => $issues
```

Donde $model->getCurrentIssues() es una relación en el modelo:

```php
public function getCurrentIssues()
{
    return $this->hasMany(Issue::class, ['SiteID' => 'ID'])
            ->onCondition(['Deleted' => 0])
            ->andOnCondition(['in', 'IssueStatusID', (new Yii\db\Query())->select('ID')->from('issue_status')->where(['Code' => ['REOPENED', 'CONFIRMED', 'NEW']])]);
}
```

Para optimizar la acción en el controlador con caché se debería cambiar de la siguiente forma:

```php
$db = Yii::$app->db;
$model = $this->findModel($id);
$issues = Issue::getDb()->cache(function ($db) use($model) {
    return $model->getPastIssues()
    ->orderBy('ID DESC')
    ->all();
}, 60, null);
```

Acá es importante resaltar que configurar la relación del modelo de la siguiente forma no funciona:

```php
public function getCurrentIssues() {
    return Yii::$app->db->cache(function($db) {
         return $this->hasMany(Issue::class, ['SiteID' => 'ID'])
            ->onCondition(['Deleted' => 0])
            ->andOnCondition(['in', 'IssueStatusID', (new Yii\db\Query())->select('ID')->from('issue_status')->where(['Code' => ['REOPENED', 'CONFIRMED', 'NEW']])]);
    });
}
```

Ya que el método cache () es activar y desactivar la caché de consultas cuando se llama el *callback*. Tenga en cuenta que hasMany() o hasOne() no realizan ninguna consulta a la base de datos, por lo que la caché de esta manera no tiene ningún efecto. 

#### [](#header-4)Caché de Search Model


```php
$db->cache(function () use ($dataProvider) {
    $dataProvider->prepare();  // lanzar consulta a base de datos
    // o si desea realizar consultas de base de datos AR, puede hacer lo siguiente:
    // $customers = Customer::find()->all();
});

return $dataProvider;
```

> Nota: Algunas bases de datos, como por ejemplo MySQL, soporta el almacenamiento en caché desde el mismo servidor de la base de datos. Puedes optar por utilizar cualquiera de los mecanismos de memoria caché. El almacenamiento en caché de consultas previamente descrito tiene la ventaja que de que se puede especificar dependencias de caché de una forma flexible y son potencialmente mucho más eficientes.

## [](#header-2)Caché de Fragmentos

La Caché de Fragmentos se refiere al almacenamiento en caché de una sección de una página Web.

Para usar la caché de fragmentos, utiliza el siguiente código en tu vista:

```php
if ($this->beginCache($id)) {

    // ... generar contenido aquí ...

    $this->endCache();
}
```

Es decir, encierra la lógica de la generación del contenido entre las llamadas beginCache() y endCache(). Si el contenido se encuentra en la memoria caché, beginCache() mostrará el contenido y devolverá ```false```, saltandose así la lógica de generación del contenido. De lo contrario, el código de generación se ejecutaría y al alcanzar la llamada endCache(), el contenido generado será capturado y almacenado en la memoria caché.

### [](#header-3)Opciones de Caché 

#### [](#header-4)Caducidad

```php
if ($this->beginCache($id, ['duration' => 3600])) {

    // ... generar contenido aquí ...

    $this->endCache();
}
```

#### [](#header-4)Dependencias

```php
$dependency = [
    'class' => 'yii\caching\DbDependency',
    'sql' => 'SELECT MAX(updated_at) FROM post',
];

if ($this->beginCache($id, ['dependency' => $dependency])) {

    // ... generar contenido aquí ...

    $this->endCache();
}
```

#### [](#header-4)Variaciones

El contenido almacenado en caché puede variar de acuerdo a ciertos parámetros. Por ejemplo, para una aplicación Web que soporte multiples idiomas, la misma pieza del código de la vista puede generar el contenido almacenado en caché en diferentes idiomas. Por lo tanto, es posible que desees hacer variaciones del mismo contenido almacenado en caché de acuerdo con la actual selección del idioma en la aplicación.

```php
if ($this->beginCache($id, ['variations' => [Yii::$app->language]])) {

    // ... generar código aquí ...

    $this->endCache();
}
```

### [](#header-3)Alternando el Almacenamiento en Caché

Puede que a veces quieras habilitar la caché de fragmentos únicamente cuando ciertas condiciones se cumplan. Por ejemplo, para una página que muestra un formulario, tal vez quieras guardarlo en la caché cuando es inicialmente solicitado (a través de una petición ```GET```). Cualquier muestra posterior (a través de una petición ```POST```) del formulario no debería ser almacenada en caché ya que el formulario puede que contenga entradas del usuario. Para hacerlo, podrías configurar la opción de activado (```enabled```), de la siguiente manera:

```php
if ($this->beginCache($id, ['enabled' => Yii::$app->request->isGet])) {

    // ... generar contenido aquí ...

    $this->endCache();
}
```

## [](#header-2)Caché de páginas

La caché de páginas se refiere a guardar el contenido de toda una página en el almacenamiento de caché del servidor. Posteriormente, cuando la misma página sea requerida de nuevo, su contenido será devuelto desde la caché en vez de volver a generarlo desde cero. 

El almacenamiento en caché de páginas está soportado por ```yii\filters\PageCache```, un filtro de acción. Puede ser utilizado de la siguiente forma en un controlador:

```php
public function behaviors()
{
    return [
        [
            'class' => 'yii\filters\PageCache',
            'only' => ['index'],
            'duration' => 60,
            'variations' => [
                Yii::$app->language,
            ],
            'dependency' => [
                'class' => 'yii\caching\DbDependency',
                'sql' => 'SELECT COUNT(*) FROM post',
            ],
        ],
    ];
}
```

El código anterior establece que el almacenamiento de páginas en caché debe ser utilizado sólo en la acción index; el contenido de la página debería almacenarse durante un máximo de 60 segundos y ser variado por el idioma actual de la aplicación; además, el almacenamiento de la página en caché debería ser invalidado si el número total de artículos ha cambiado.

Como puedes ver, la caché de páginas es muy similar a la caché de fragmentos. Ambos soportan opciones tales como ```duration```, ```dependencies```, ```variations```, y ```enabled```. Su principal diferencia es que la caché de páginas está implementado como un filtro de acción mientras que la caché de fragmentos se hace en un *widget*.


## [](#header-2)Caché HTTP

 Las aplicaciones Web pueden hacer uso de la caché en el lado del cliente para así ahorrar tiempo y recursos para generar y transmitir el mismo contenido una y otra vez.

 Para usar la caché del lado del cliente, puedes configurar ```yii\filters\HttpCache``` como un filtro en el controlador para aquellas acciones cuyo resultado deba estar almacenado en la caché en el lado del cliente. ```HttpCache``` solo funciona en peticiones ```GET``` y ```HEAD```. Puede manejar tres tipos de cabeceras (headers) HTTP relacionadas en este tipo de consultas:

### [](#header-3)La Cabecera Last-Modified

La cabecera Last-Modified usa un sello de tiempo para indicar si la página ha sido modificada desde que el cliente la almacena en la caché.

El siguiente es un ejemplo haciendo uso de la cabecera Last-Modified:

```php
public function behaviors()
{
    return [
        [
            'class' => 'yii\filters\HttpCache',
            'only' => ['index'],
            'lastModified' => function ($action, $params) {
                $q = new Yii\db\Query();
                return $q->from('post')->max('updated_at');
            },
        ],
    ];
}
```

El código anterior establece que la memoria caché HTTP debe ser habilitada únicamente por la acción index. Se debe generar una cabecera HTTP Last-Modified basado en el último tiempo de actualización de los artículos. Cuando un navegador visita la página index la primera vez, la página será generada en el servidor y enviada al navegador; Si el navegador visita la misma página de nuevo y no ningún artículo modificado durante el período, el servidor no volverá a regenerar la página, y el navegador usará la versión caché del lado del cliente. Como resultado, la representación del lado del servidor y la transmisión del contenido de la página son ambos omitidos.

### [](#header-3)La Cabecera ETag

La cabecera **Entity Tag** (o para abreviar ETag) usa un *hash* para representar el contenido de una página. Si la página ha sido cambiada, el *hash* también cambiará. Al comparar el *hash* guardado en el lado del cliente con el hash generado en el servidor, la caché puede determinar si la página ha cambiado y deber ser retransmitida.

El siguiente es un ejemplo de cómo usar la cabecera ETag:

```php
public function behaviors()
{
    return [
        [
            'class' => 'yii\filters\HttpCache',
            'only' => ['view'],
            'etagSeed' => function ($action, $params) {
                $post = $this->findModel(Yii::$app->request->get('id'));
                return serialize([$post->title, $post->content]);
            },
        ],
    ];
}
```

El código anterior establece que la caché HTTP debe ser activada únicamente para la acción *view*. Debería generar una cabecera HTTP ETag basándose en el título y contenido del artículo consultado. Cuando un navegador visita la página view por primera vez, la página se generará en el servidor y será enviada al navegador; Si el navegador visita la misma página de nuevo y no ha ocurrido un cambio en el título o contenido del artículo, el servidor no volverá a generar la página, y el navegador usará la versión guardada en la caché del lado del cliente. Como resultado, la representación del lado del servidor y la transmisión del contenido de la página son ambos omitidos.

ETags permiten estrategias de almacenamiento de caché más complejas y/o mucho más precisas que las cabeceras Last-Modified. Por ejemplo, un ETag puede ser invalidado si el sitio Web ha cambiado de tema (theme).

La generación de un ETag que requiera muchos recursos puede echar por tierra el propósito de estar usando HttpCache e introducir una sobrecarga innecesaria, ya que debe ser re-evaluada en cada solicitud (request). Trata de encontrar una expresión sencilla para invalidar la caché si la página ha sido modificada.

### [](#header-3)La Cabecera Cache-Control

La cabecera Cache-Control especifica la directiva general de la caché para páginas. Puedes enviarla configurando la propiedad ```yii\filters\HttpCache::$cacheControlHeader``` con el valor de la cabecera. Por defecto, la siguiente cabecera será enviada:

```
Cache-Control: public, max-age=3600
```

> Cuando una página utiliza la sesión, PHP enviará automáticamente cabeceras HTTP relacionadas con la caché tal y como se especifican en session.cache_limiter de la configuración INI de PHP. Estas cabeceras pueden interferir o deshabilitar el almacenamiento de caché que desees de ```HttpCache```. Para evitar este problema, por defecto ```HttpCache``` deshabilitará automáticamente el envío de estas cabeceras. Si deseas modificar este comportamiento, tienes que configurar la propiedad ```yii\filters\HttpCache::$sessionCacheLimiter```. La propiedad puede tomar un valor de cadena, incluyendo ```public```, ```private```, ```private_no_expire```, and ```nocache```.

> Los robots de motores de búsqueda tienden a respetar las cabeceras de caché. Dado que algunos *crawlers* tienen limitado el número de páginas que pueden rastrear por dominios dentro de un cierto período de tiempo, la introducción de cabeceras de caché pueden ayudar a la indexación del sitio Web y reducir el número de páginas que deben ser procesadas.

### [](#header-3)Limitaciones

El almacenamiento en caché de consultas no funciona con los resultados de consulta que contienen controladores de recursos. Por ejemplo, cuando se utiliza el tipo de columna BLOB en algunos DBMS, el resultado de la consulta devolverá un recurso para manejar los datos de la columna.

Algunos sistemas de almacenamiento caché tienen limitación de tamaño. Por ejemplo, memcache limita el tamaño máximo de cada entrada a 1MB. Por lo tanto, si el tamaño de un resultado de la consulta excede ese límite, el almacenamiento en caché fallará.

# [](#header-1)Fuentes

- [Almacenamiento de Datos en Caché?](https://www.yiiframework.com/doc/guide/2.0/es/caching-data)
- [Yii 2.0 Part 17 - Caching](https://www.youtube.com/watch?v=aRh2m_1nZF8/)
- [What are yii2’s cache dependencies? How do I use cache dependencies?](https://www.itworkman.com/169213.html/)
- [Yii2 Optimization & Caching and ActiveRecord](https://kb.zensoft.hu/yii2-caching-and-activerecord/#caching-in-a-search-model/)


habilitar log 
configurar base de datos 
