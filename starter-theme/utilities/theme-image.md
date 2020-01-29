# App::theme()->image()

This utility provides tools to work with images.

## App::theme()->image()->thumbnail( $attachment_id, $width, $height, $crop = true )

Generate and cache a thumbnail for the passed attachment id. Uses WordPress' built-in image editor so no extra libraries are needed.
Useful if you need a high number of image sizes and you'd rather not define each and every one of them with `add_image_size()`.
