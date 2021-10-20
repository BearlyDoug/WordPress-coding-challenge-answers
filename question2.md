### Consider the following code snippet

```php
<?php

add_action('admin_menu', 'custom_menu');

$custom_menu_string = __( 'Custom Menu' , 'nba');

function custom_menu(){
  add_menu_page($custom_menu_string, $custom_menu_string, 'manage_options', 'custom-menu', 'custom_menu_page_display');
}

function custom_menu_page_display(){
  ?>
    <h1>Hello World</h1>
    <p>This is a custom page</p>
  <?php
}
```

### Briefly explain

1. What does this code do?
DLH: It creates a menu link inside WP Admin, on the side-bar menu, called "Custom Menu".

2. Who can/cannot view its effects?
DLH: On a standard WordPress installation, only Administrators (or Super Admins on a MultiSite instance) can access this. If utilizing a third party plugin ("User Role Editor", "Multisite User Role Manager" or something similar), access can be customized a bit more.

3. What URL exposes the new functionality?
DLH: https://my-site.com/wp-admin/admin.php?page=nba