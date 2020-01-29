# Request Lifecycle

In order to enable WP Emerge you first have to boot it. This is usually done in your theme's functions.php file or your plugin's main file as it is early enough to allow WP Emerge to hook itself into required WordPress actions. Once WP Emerge is booted, it will hook into the `template_include` action to process the request. This means that there are 2 major parts to the request lifecycle:

## Bootstrapping

1. Configuration options passed to `\App::make()->bootstrap( $config )` are loaded.
2. All service providers listed in the configuration are registered.
3. All service providers listed in the configuration are booted.

!> Step 2 and 3 are a critical part of bootstrapping which is why you should make sure your own service container registrations or overrides are done in your own Service Provider instead of after WP Emerge has been booted.


## `template_include` action

1. All defined routes are evaluated in the order they are defined until a satisified route is found.
    - If no route is satisfied, normal WordPress execution takes place and no further action is taken by WP Emerge.
2. If a route is satisfied, normal WordPress template output will be halted and WP Emerge will take over.
3. All suitable arguments depending on the route condition are prepared and passed to the route handler.
4. All middleware is sorted according to the middleware priority array and is executed. At the end of the middleware chain, the route handler (a controller method, for example) is executed. The middleware and route handler chain will be referred to as the `pipeline`.
5. If an exception is thrown from the pipeline the `ErrorHandler` defined in the service container will be invoked with that exception as its argument and the pipeline will be halted. The error handler must return a corresponding response object.
6. The returned response object from the pipeline or the error handler will be used to set the headers and output the body, ending the response.

## WP Emerge and The Loop

Since WP Emerge hooks right before the first template is loaded, the main WordPress query is not interrupted and you can use The Loop as you normally would.

## WordPress AJAX

AJAX routes are handled in the same way but use the appropriate `wp_ajax_*` and `wp_ajax_nopriv_*` hooks instead of `template_include`.

## WordPress Admin

Admin routes are handled in the same way but use the appropriate page `{$admin_page_hook}` and `load-{$admin_page_hook}` hooks instead of `template_include`. In addition, the response is split with the headers being sent during `load-{$admin_page_hook}` while the body of the response is sent during `{$admin_page_hook}`.
