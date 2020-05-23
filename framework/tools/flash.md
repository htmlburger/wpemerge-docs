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
// inside your controller method
if ( $email_is_invalid ) {
    // flash an error message
    Flash::add( 'errors', 'Please enter a valid email address.' );
    // redirect the user back to the form
    return \App::redirect()->back();
}

// inside your form view
$errors = Flash::get( 'errors' );
foreach ( $errors as $error ) {
    echo '<p>' . $error . '</p>';
}
```
