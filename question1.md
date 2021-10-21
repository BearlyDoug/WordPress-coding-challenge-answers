### Given a file `wp-content/plugins/hello-world.php` with the following content:

```php
<?php

add_filter('the_content', 'hello_world');

function hello_world ( $content ){
  return "<h1>Hello World</h1>" . $content;
}

### Answer the following questions:

1. Why can't I see this plugin when viewing https://my-site.com/wp-admin/plugins.php?
DLH: The function does a return of the quoted element, prepended to the content on the public side. Otherwise, there's no actual direct output.

1. If enabled, what would this plugin do?
DLH: Inserts the words "Hello World" in a Heading 1 tag before any content of that page/post is rendered.
```