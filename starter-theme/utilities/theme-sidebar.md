# \MyApp::theme()->sidebar()

This utility provides tools to work with sidebars.

`\MyApp::theme()->sidebar()->getCurrentSidebarId( $default = 'default-sidebar', $meta_key = '_custom_sidebar' )`

Return the specified default sidebar id unless a sidebar id is stored in the specified meta key for the current post/page.
Useful if you wish to have per-page sidebars that you store in post meta.
