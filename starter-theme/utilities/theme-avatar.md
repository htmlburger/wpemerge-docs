# App::theme()->avatar()

This utility provides tools to work with custom user avatars.

## App::theme()->avatar()->setDefault( $attachment_id )

Register an attachment as the default avatar for users on the site.

## App::theme()->avatar()->addUserMetaKey( $user_meta_key )

Register a user meta key as a possible avatar for users.
When an avatar is fetched, it will check all registered user meta keys for a valid attachment id value and will use that as the avatar for the user - this way you can provide users with the ability to upload a custom avatar instead of relying on Gravatars or custom plugins for this functionality.

## App::theme()->avatar()->removeUserMetaKey( $user_meta_key )

Unregister a user meta key as a possible avatar for users.
