HttpCacheServiceProvider
========================

The *HttpCacheProvider* provides support for the Symfony2 Reverse Proxy.

Parameters
----------

* **http_cache.cache_dir**: The cache directory to store the HTTP cache data.

* **http_cache.options** (optional): An array of options for the `HttpCache
  <http://api.symfony.com/master/Symfony/Component/HttpKernel/HttpCache/HttpCache.html>`_
  constructor.

Services
--------

* **http_cache**: An instance of `HttpCache
  <http://api.symfony.com/master/Symfony/Component/HttpKernel/HttpCache/HttpCache.html>`_,

Registering
-----------

::

    $app->register(new Silex\Provider\HttpCacheServiceProvider(), array(
        'http_cache.cache_dir' => __DIR__.'/cache/',
    ));

Usage
-----

Silex already supports any Reverse Proxy like Varnish out of the box by
setting Response HTTP cache headers::

    $app->get('/', function() {
        return new Response('Foo', 200, array(
            'Cache-Control' => 's-maxage=5',
        ));
    });

This provider allows you to use the Symfony2 reverse proxy natively with
Silex applications by using the `http_cache` service::

    $app['http_cache']->run();

The provider also provide ESI support::

    $app->get('/', function() {
        return new Response(<<<EOF
    <html>
        <body>
            Hello
            <esi:include src="/included" />
        </body>
    </html>

    EOF
        , 200, array(
            'Cache-Control' => 's-maxage=20',
            'Surrogate-Control' => 'content="ESI/1.0"',
        ));
    });

    $app->get('/included', function() {
        return new Response('Foo', 200, array(
            'Cache-Control' => 's-maxage=5',
        ));
    });

    $app['http_cache']->run();

For more information, consult the `Symfony2 HTTP Cache documentation
<http://symfony.com/doc/current/book/http_cache.html>`_.
