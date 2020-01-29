# Route Groups

You can group routes which share attributes:

```php
App::route()
    ->middleware( 'mymiddleware' )
    ->url( '/foo/' )
    ->group( function() {
        App::route()
            ->url( '/bar/' )
            ->group( function() {
                // Match if '/foo/bar/' is the full path:
                App::route()->get()->url( '/' )->handle( $handler );
        
                // Match if '/foo/bar/baz/' is the full path:
                App::route()->get()->url( '/baz/' )->handle( $handler );
            } );
        
        // Match if '/foo/' is the full path
        // AND the query var 'my_query_var' is present:
        App::route()->get()->where( 'query_var', 'my_query_var' )->handle( $handler );
    } );
```

!> URL conditions will be concatenated as long as they are in a chain which starts from a root group. The 
following examples will **NOT** concatenate URL conditions:

```php
// Root condition is not a URL:
App::route()
    ->where( 'post_id', 1 )
    ->group( function() {
        // Match if the current query loads the single post with id of 1
        // AND '/foo/' is the full path
        // -> this usage is fine so far
        App::route()
            ->url( '/foo/' )
            ->group( function() {
                // Match if the current query loads the single post with id of 1
                // AND '/foo/' is the full path
                // AND '/bar/' is the full path
                // -> The above will always fail since both '/foo/' and '/bar/' are required
                // -> to be the full path as we've started with a non-URL root condition
                App::route()->get()->url( '/bar/' )->handle( $handler );
            } );
    } );

// Root condition is a URL:
App::route()
    ->url( '/foo/' )
    ->group( function() {
        // Match if '/foo/' is the full path
        // AND the current query loads the single post with id of 1
        // -> this usage is fine, but note that we are breaking the URL chain
        App::route()
            ->where( 'post_id', 1 )
            ->group( function() {
                // Match if '/foo/' is the full path
                // AND the current query loads the single post with id of 1
                // AND '/bar/' is the full path
                // -> the above will always fail since both '/foo/' and '/bar/' are required
                // -> to be the full path as we've broken the URL condition chain
                App::route()->get()->url( '/bar/' )->handle( $handler );
            } );
    } );
```
