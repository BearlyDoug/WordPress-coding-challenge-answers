### Consider the following scenario:

#### For all players in the NBA so far:

* A `player` custom post type exists with a meta field called `player_external_id`
  * Assume all players have a unique value in this field (different than the WordPress post ID)
  
#### For the upcoming season, all players will have a page on a 3rd party site with the following URL structure:

`http://www.nba-player-tv.com/channel/{player_external_id}`

* A new meta field `player_tv_url` was added to the `player` custom post
* All new players drafted for the upcoming season have the proper value set

#### Summary

| Type | Name |
| ---- | ---- |
| Custom Post Type | player |
| Custom Fields | player_external_id, player_tv_url |

### We need to update any missing `player_tv_url` post metadata

```php
1. Write the code that would accomplish this.
DLH: There's two answers to this, with a one unknown element (Post ID) not being clearly defined.

Scenario 1: If the Post ID is defined within the code, and have a default fallback URL for player_tv_url

> $default_tv_url = 'http://www.nba-player-tv.com/channel/' . $player_external_id;
> $new_player_tv_url = (!empty($player_tv_url)) ? $player_tv_url : $default_tv_url;
> update_post_meta($post_id, 'player_tv_url', $new_player_tv_url);

Scenario 2: If we don't have the Post ID, however, we can get it via the unique "player_external_id" meta value, and then update the "player_tv_url" accordingly:
(If this is inside a loop, I would utilize the WP_Query function, instead)

> global $wpdb;
> $results = $wpdb->get_results("
	SELECT post_id 
	FROM $wpdb->postmeta 
	WHERE meta_key = 'player_external_id" && meta_value = '$player_external_id'
", ARRAY_A);

> $default_tv_url = 'http://www.nba-player-tv.com/channel/' . $player_external_id;
> $new_player_tv_url = (!empty($player_tv_url)) ? $player_tv_url : $default_tv_url;
> update_post_meta($results->post_id, 'player_tv_url', $new_player_tv_url);

Note: I prefer to use "update_post_meta()" over "add_post_meta()", because the update version will create the meta key/value entry if it doesn't exist, or update it if it does.

2. How would you trigger the execution of this code?
DLH: There are a few ways, depending on how the data is received. The code below is a mass update code, which should be done as a one time thing, or placed inside a function for a weekly (or daily) maintenance cycle:

Scenario 1 - Bulk update, done as a maintenance item, outside of a WordPress loop:
> global $wpdb;
> $results = $wpdb->get_results("
	SELECT * 
	FROM $wpdb->posts as posts
	WHERE posts.post_type = 'player'
	AND NOT EXISTS (
		SELECT * 
		FROM $wpdb->postmeta as postmeta
		WHERE postmeta.meta_key = 'player_tv_url' 
                AND postmeta.post_id = posts.ID
	)
");

> foreach($results as $thePlayer) {
> 	if(metadata_exists('player', $thePlayer->ID, 'player_external_id')) {
> 		$external_player_id = get_post_meta($thePlayer->ID, 'external_player_id', true);
>		$default_tv_url = 'http://www.nba-player-tv.com/channel/' . $player_external_id;
>		$new_player_tv_url = (!empty($player_tv_url)) ? $player_tv_url : $default_tv_url;
>		update_post_meta($results->post_id, 'player_tv_url', $new_player_tv_url);
> 	}
> }

Scenario 2 - Done as a single item update, manually:
DLH: If the custom post type has been configured correctly (per WordPress Codex / Development standards), you can enable the "Custom Fields" option via the "Screen Options" tab, so that individual player bio updates can be done, manually.

Scenario 3 - Inside a WordPress loop:
> 	if(metadata_exists('player', get_the_id(), 'player_external_id') && !metadata_exists('player', get_the_id(), 'player_tv_url')) {
>		$default_tv_url = 'http://www.nba-player-tv.com/channel/' . $player_external_id;
>		$new_player_tv_url = (!empty($player_tv_url)) ? $player_tv_url : $default_tv_url;
>		update_post_meta(get_the_id(), 'player_tv_url', $new_player_tv_url);
> 	}
```