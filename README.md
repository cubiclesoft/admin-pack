Admin Pack
==========

A PHP toolkit designed specifically for programmers to quickly create a nice-looking, custom-built, secure administrative web interface.  Choose from a MIT or LGPL license.  Proven to cut development time by at least 50% over traditional frameworks and template engines.  But it isn't a CMS nor a framework or a template engine.  Admin Pack is very different.  Give it a go to power your next PHP-based administrative backend.

Be sure to check out [Admin Pack with Extras](https://github.com/cubiclesoft/admin-pack-with-extras).  It is Admin Pack plus some extra options with Javascript components (e.g. a convenient visual date picker widget) in a slightly beefier package.

[![Donate](https://cubiclesoft.com/res/donate-shield.png)](https://cubiclesoft.com/donate/)

Features
--------

* Quick-n-dirty custom administrative interface builder.  [Live demo](https://barebonescms.com/demos/admin_pack/admin.php)
* The default templates look nice enough.  Gets the job done.
* Integrated CSRF/XSRF defenses.
* Supports custom logos/favicon.
* Very lightweight (~180KB).
* Has a liberal open source license.  MIT or LGPL, your choice.
* Designed for relatively painless integration into your project.
* Sits on GitHub for all of that pull request and issue tracker goodness to easily submit changes and ideas respectively.

Getting Started
---------------

Download or clone the latest software release.  Transfer the files to a web server.  Open up `admin.php` in your favorite text editor.  Find this code:

```php
	// $bb_randpage is used in combination with a user token to prevent hackers from sending malicious URLs.
	// [Put random content into the string.  Try www.random.org.]
	// https://www.random.org/integers/?num=100&min=0&max=255&col=10&base=16&format=plain&rnd=new
	$bb_randpage = "[Random content goes here]";
	$bb_rootname = "Tool Name";

	// [Put your login management, permissions to access this page, and any obvious initialization logic here.]
	// [If you want, you can make any error messages look nice using the BB_GeneratePage() call.]
	// [$bb_usertoken should be a string that uniquely identifies the user without directly identifying them.  For example, a session ID.]
	$bb_usertoken = "";


	BB_ProcessPageToken("action");
```

And integrate a login system.  If you do not have a login system, try CubicleSoft [Single Sign-On Server/Client](https://github.com/cubiclesoft/sso-server).

There are three global variables that need to be defined:

* $bb_randpage - Set to a randomly generated string of characters.  The more random, the better.
* $bb_rootname - A string containing the global name of the tool and is also used as part of the title of each page.
* $bb_usertoken - A string containing a secret session ID that uniquely identifies the user without directly identifying them.

Example:

```php
	// Force SSL.
	if (!BB_IsSSLRequest())
	{
		header("Location: " . BB_GetFullRequestURLBase("https"));
		exit();
	}

	// $bb_randpage is used in combination with a user token to prevent hackers from sending malicious URLs.
	$bb_randpage = "33f724f3724f2679614855896571bc8c9406355f";
	$bb_rootname = "RSS Feed Manager";

	require_once "../login/required.php";
	if (!$user->admin)
	{
		echo "You must be an admin.";

		exit();
	}

	$bb_usertoken = $user->secretid;

	BB_ProcessPageToken("action");
```

Example [SSO Server/Client](https://github.com/cubiclesoft/sso-server) integration:

```php
	// Force SSL.
	if (!BB_IsSSLRequest())
	{
		header("Location: " . BB_GetFullRequestURLBase("https"));
		exit();
	}

	// $bb_randpage is used in combination with a user token to prevent hackers from sending malicious URLs.
	$bb_randpage = "33f724f3724f2679614855896571bc8c9406355f";
	$bb_rootname = "RSS Feed Manager";

	require_once "client/config.php";
	require_once SSO_CLIENT_ROOT_PATH . "/index.php";

	$sso_client = new SSO_Client;
	$sso_client->Init(array("sso_impersonate", "sso_remote_id"));

	$extra = array();
	if (isset($_REQUEST["sso_impersonate"]) && is_string($_REQUEST["sso_impersonate"]))  $extra["sso_impersonate"] = $_REQUEST["sso_impersonate"];
	if (!$sso_client->LoggedIn())  $sso_client->Login("", "You must login to use this system.", $extra);

	// Send the browser cookies.
	$sso_client->SaveUserInfo();

	// Test permissions for the user.
	if (!$sso_client->IsSiteAdmin() && !SSO_HasTag("rss_manager"))  SSO_Login("", "insufficient_permissions");

	// Get the internal token for use with XSRF defenses.
	$bb_usertoken = $sso_client->GetSecretToken();

	BB_ProcessPageToken("action");
```

Next, set up the menu of options.  The default menu is the one found in the demo.

Finally, start writing code for each admin interface action.  The source code found in '[admin.php](https://github.com/cubiclesoft/admin-pack/blob/master/admin.php)' demonstrates many common patterns that can simply be copied, pasted, and adjusted for each action.

Under the Hood
--------------

Admin Pack uses [FlexForms](https://github.com/cubiclesoft/php-flexforms), which makes it easy to extend Admin Pack with custom functionality (see `support/view_print_layout.php`).  For example, [FlexForms Modules](https://github.com/cubiclesoft/php-flexforms-modules) contains several official extensions (e.g. charts, HTML editor, character/word counter).

Admin Pack has its origins in [Barebones CMS](http://barebonescms.com/), which is where the "bb_" prefixes come from.  Admin Pack is its own product.

More Information
----------------

* [Page Basics](https://github.com/cubiclesoft/php-flexforms/blob/master/docs/page_basics.md) - Convenient global functions included with Admin Pack.
* [Creating Flexible Relational Databases](http://cubicspot.blogspot.com/2012/05/creating-flexible-relational-database.html) - Alternative approach to relational database design that works well with Admin Pack.
* [CSDB Data Access Layer](https://github.com/cubiclesoft/csdb) - A set of classes for cross-database SQL.
* [FlexForms Modules](https://github.com/cubiclesoft/php-flexforms-modules) - Additional functionality for Admin Pack.
