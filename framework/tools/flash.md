# Flash

Flash is a built-in global middleware which allows you to "flash" data to the session for one-time use (this will be familiar if you've used [Symfony's FlashBag](https://symfony.com/doc/current/components/http_foundation/sessions.html#flash-messages) ).

Since Flash uses PHP's `$_SESSION` superglobal by default, you have to ensure it is available:
```php
add_action( 'init', function() {
    session_start();
} );
```

You can also replace the usage of `$_SESSION` with a different solution, as long as it implements `ArrayAccess`:
```php
// Run this inside the register() method of a service provider.
$container[ WPEMERGE_SESSION_KEY ] = function() {
    return new OtherSesssionImplementation();
};
```

## Usage

A typical use case is to flash error messages inside a controller method in response to a user submitting a form:
```php
\App::route()->post()->url( '/contact-form' )
    ->middleware( 'flash' )
    ->handle( function ($request, $view) {
        $is_valid_email = filter_var($request->body('email'), FILTER_VALIDATE_EMAIL);
        if (!$is_valid_email) {
            \App::flash()->add( 'errors', 'Please enter a valid email address.' );
        }
        
        /* ... */ 
        
        // redirect the user back to the form
        return \App::redirect()->back();
    } );

// inside your form view
$errors = \App::flash()->get( 'errors' );
foreach ( $errors as $error ) {
    echo '<p>' . $error . '</p>';
}
```
