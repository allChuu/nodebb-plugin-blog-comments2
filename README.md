This plugin is a folk from [NodeBB Blog Comments](https://github.com/psychobunny/nodebb-plugin-blog-comments), I just added some more features and fixed some bugs to it.

* Support multiple blogs, use `articleID` and `blogger` to distinguish posts from different blogs, so this is kinda break change to the original nodebb-plugin-blog-comments.
* Simple script for ghost, hugo, pelican etc, just add a `<script>` node at any place of the page, will support more blog platforms later.
* Use `siteTitle` instead of NodeBB in the `comments.tpl`.
* Fixed some bugs, like escaped title and content problems, profile image display, etc.

The multiple blogs feature is great, you can see at here: https://v2mm.tech/category/46/blog
There are already some blogs conntected with [V2MM](https://v2mm.tech).

# NodeBB Blog Comments

The original [NodeBB Blog Comments](https://github.com/psychobunny/nodebb-plugin-blog-comments) lets NodeBB act as a comments engine/widget for your blog. It supports both [Ghost](https://ghost.org/) and [WordPress](http://wordpress.org/).

The comments are exposed to any plugin you have built into the core, so it is completely possible to have emoticons, embedded video, and/or whatever else you want in the comments section of your blog.

Articles are published to a forum category of your choice, and will gain a tag that links it back to the original article.


## Screenshots

![blog comments](http://i.imgur.com/pPO42Hy.png)

## Installation

First install the plugin:

    npm install nodebb-plugin-blog-comments2

Activate the plugin in the ACP and reboot NodeBB. Head over to the Blog Comments section in the ACP and select the Category ID you'd like to publish your blog content to (default is Category 1). Make sure you put the correct URL to your blog.

### Ghost Installation

Paste this any where in `yourtheme/post.hbs`, somewhere between `{{#post}}` and `{{/post}}`. All you have to edit is line 3 (`nbb.url`) - put the URL to your NodeBB forum's home page here.

```html
<a id="nodebb-comments"></a>
<script type="text/javascript">
var nbb = {};
nbb.url = '//your.nodebb.com'; // EDIT THIS
nbb.cid = 1;	// OPTIONAL. Forces a Category ID in NodeBB.
nbb.blogger = 'blogger_name';	//  Omit it to fallback to 'default'.

(function() {
nbb.articleID = '{{../post.id}}';
nbb.tags = [{{#../post.tags}}"{{name}}",{{/../post.tags}}];
nbb.script = document.createElement('script'); nbb.script.type = 'text/javascript'; nbb.script.async = true;
nbb.script.src = nbb.url + '/plugins/nodebb-plugin-blog-comments2/lib/ghost.js';
(document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(nbb.script);
})();
</script>
<span id="nbb-title" style="display:none">{{../post.title}}</span>
<span id="nbb-markdown" style="display:none">{{../post.markdown}}</span>
<noscript>Please enable JavaScript to view comments</noscript>
```

If you wish, you can move `<a id="nodebb-comments"></a>` to where you want to place the actual comments widget.

See demo at [hgkat's blog](http://hgkat.com), her blog is using this script to connect with [V2MM](https://v2mm.tech).

### Simple Ghost Installation
Since some blogs might installed with docker, couldn't change the theme file, I create this simple script which can be added at any place of the html.

Just add this any where on your blog's page:

```
<script id='nodebb-comments-script' ourl="//your.nodebb.com" ocid="1" blogger='name' src="//your.nodebb.com/plugins/nodebb-plugin-blog-comments2/lib/simple-ghost.js"></script>
```
Note: ocid is the category id in your NodeBB, blogger is set for distinguish posts from different blogs.

See demo at [Wave's blog](http://blog.lovejog.com), his blog is using this script to connect with [V2MM](https://v2mm.tech).

### Simple Hugo Installation

```
<script id='nodebb-comments-script' ourl="//your.nodebb.com" ocid="1" blogger='name' src="//your.nodebb.com/plugins/nodebb-plugin-blog-comments2/lib/simple-hugo.js"></script>
```

See demo at [Leyafo's blog](http://www.leyafo.com/), his blog is using this script to connect with [V2MM](https://v2mm.tech).

### Simple Pelican Installation

```
<script id='nodebb-comments-script' ourl="//your.nodebb.com" ocid="1" blogger='name' src="//your.nodebb.com/plugins/nodebb-plugin-blog-comments2/lib/simple-pelican.js"></script>
```

See demo at [Cycleuser's blog](http://blog.cycleuser.org), his blog is using this script to connect with [V2MM](https://v2mm.tech).

**Wordpress plugin and any other plugins in PHP, I haven't test them yet, as I don't have those blogs.**
If your blog is wordpress, and would like to connect with [V2MM](https://v2mm.tech), I would be glad to set up this script for you.

### Wordpress Installation

First, install the [Wordpress JSON API](http://wordpress.org/plugins/json-api/) plugin.

Replace the contents of `/wp-content/themes/YOUR_THEME/comments.php` with the following (back-up the old comments.php, just in case):

```html
<?php
if ( post_password_required() )
	return;
?>

<a id="nodebb-comments"></a>
<script type="text/javascript">
var nodeBBURL = '//your.nodebb.com',
	wordpressURL = '<?php get_site_url(); ?>',
	articleID = '<?php echo the_ID(); ?>',
	categoryID = 1; // OPTIONAL. Forces a Category ID in NodeBB.
					 //  Omit it to fallback to specified IDs in the admin panel.

(function() {
var nbb = document.createElement('script'); nbb.type = 'text/javascript'; nbb.async = true;
nbb.src = nodeBBURL + '/plugins/nodebb-plugin-blog-comments2/lib/wordpress.js';
(document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(nbb);
})();
</script>
<noscript>Please enable JavaScript to view comments</noscript>
```

### General - PHP example
Paste this any where that you want load commenting system. All you have to edit is line 3 (`nodeBBURL`) - put the URL to your NodeBB forum's home page here. You can also use any template engine (hbs, eco...) instead of PHP.

```html
	<a id="nodebb-comments"></a>
	<script type="text/javascript">
	var nodeBBURL = '//your.nodebb.com',

	<?php
		echo "articleID = " .getId().";";
		$obj = new stdClass();
		$obj->title_plain = "";
		$obj->url="";
		$obj->tags = [];
		$obj->markDownContent= "";
		$obj->cid = 1; // OPTIONAL. Forces a Category ID in NodeBB.
						// Omit it to fallback to specified IDs in the admin panel.
		echo "var articleData =" .json_encode($obj).";";
	?>

	(function() {
	var nbb = document.createElement('script'); nbb.type = 'text/javascript'; nbb.async = true;
	nbb.src = nodeBBURL + '/plugins/nodebb-plugin-blog-comments2/lib/generalphp.js';
	(document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(nbb);
	})();
	</script>
	<noscript>Please enable JavaScript to view comments</noscript>
```

You must have some getId() function on your website, for example:

**For a PHP website**

```php
    <?php
        function getId(){
            $id = 0;
            // unique id for each page of your website
            return $id;
        }
    ?>
```

If you don't have such ID, you can use this function that generates a unique ID from the URL:

```php
    <?php
        function getId(){
            return stringToInteger($_SERVER['REQUEST_URI']);
        }
        function stringToInteger($string) {
            $string = md5($string);
            $output = '1';
            for ($i = 0; $i < strlen($string); $i++) {
                $output .= (string) ord($string[$i]);
            }
            return (int) $output;
        }
    ?>
```

### Comments Counter


You may optionally put a "# of comments" counter anywhere on the page with the following code:

```html
<span id="nodebb-comments-count"></span> Comments
```

A mechanism to query the number of comments on another separate page will be available in a future release.

### Author and Category information

![](http://i.imgur.com/NyLs4LQ.png)

To use NodeBB's category and author information (instead of using Ghost's user/tag system), there are two elements that this plugin searches for:

```html
Published by <span id="nodebb-comments-author"></span> in <span id="nodebb-comments-category"></span>
```

### Publishing

Head over to the article that you'd like to publish. The code will detect if you're both an administrator of your blog and NodeBB (so ensure that you're logged into both) and will display a publish button if so.

You may also create a `publishers` group in NodeBB to allow a group of regular users to have publish rights.
