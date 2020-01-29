# Extending

WP Emerge uses a service container for all of its dependencies which means that you can easily replace or add dependencies.
For a real-world example, we will be adding our own custom routing condition.

## Scenario

- Our site has events with a post type of `event`.
- Events have an `event_date` post meta which holds the date the event is scheduled for. The date format stored is 
`Y-m-d` e.g. 2018-12-31.
- Our goal is to match all past events and display a custom page.

## Implementation

1. First, we will define our custom condition class:
    ```php
    use WPEmerge\Requests\RequestInterface;
    use WPEmerge\Routing\Conditions\ConditionInterface;

    class PastEvent implements ConditionInterface {
        public function isSatisfied( RequestInterface $request ) {
            // Makre sure we are on a singular event page.
            if ( is_singular( 'event' ) ) {
                // Get the event date.
                $event_date = get_post_meta( get_the_ID(), 'event_date', true );
                // Get today's date in the same format.
                $today = date( 'Y-m-d' );

                // The Y-m-d format is very easy to compare.
                if ( $today > $event_date ) {
                    // Our condition is satisfied - we must return true.
                    return true;
                }
            }

            // In all other cases return false.
            return false;
        }

        public function getArguments( RequestInterface $request ) {
            // We can return an array of arguments we wish to pass on to the
            // route handler for convenience.
            // We do not really need to pass anything so we return an empty
            // array but we could pass the event date, for example.
            return [];
        }
    }
    ```

2. Next, we will define a service provider class which will register our new condition:
    ```php
    use WPEmerge\ServiceProviders\ServiceProviderInterface;

    class PastEventConditionServiceProvider implements ServiceProviderInterface {
        public function register( $container ) {
            // Conditions are registered by appending them to the
            // array of condition types which is registered with
            // the WPEMERGE_ROUTING_CONDITION_TYPES_KEY key
            // in the container.
            $condition_name = 'past_event';
            $condition_class = PastEvent::class;

            $container[ WPEMERGE_ROUTING_CONDITION_TYPES_KEY ] = array_merge(
                $container[ WPEMERGE_ROUTING_CONDITION_TYPES_KEY ],
                [
                    $condition_name => $condition_class,
                ]
            );
        }

        public function bootstrap( $container ) {
            // We have nothing to bootstrap.
        }
    }
    ```

3. Finally, we pass our brand new service provider while booting WP Emerge:
    ```php
    App::make()->bootstrap( [
        'providers' => [
            // ... other providers go here
            PastEventConditionServiceProvider::class,
            // ... or here
        ],
        // ... other options go here
    ] );
    ```

We can now use our custom route condition like so:
```php
App::route()->get()
    ->where( 'past_event' )
    ->handle( function( $request ) {
        return 'This event has already passed :(';
    } );
```
