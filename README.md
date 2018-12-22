# Linking_App_Setup
For iOS-Universal-Links, Deep Link, URL Scheme to open App

### Create 'apple-app-site-association' json format file without extension '.json'
```json
{
    "applinks": {
        "apps": [],
        "details": [
            {
                "appID": "TeamID_001.com.demo.abc",
                "paths": [ "/page_1/data/*", "/page_2/item/201?/*"]
            },
            {
                "appID": "TeamID_002.com.demo.abcd",
                "paths": [ "*" ]
            }
        ]
    }
}
```

### Place 'apple-app-site-association' on folders like as below(Use XAMPP for Sample)
```
Sample domain: linkingappsetup.domain.com
The root folder of structure:
/opt/lampp/htdocs/linkingappsetup.domain.com
+-- apple-app-site-association
+-- .htacess
+-- .well-known
|   +-- apple-app-site-association
|   +-- .htacess
```

### Define 'apple-app-site-association' is json on .htacess
```
<Files "apple-app-site-association">
ForceType 'application/json'
</Files>
```

### Verify 'apple-app-site-association'
Can visit the link like as below:
https://linkingappsetup.domain.com/apple-app-site-association
https://linkingappsetup.domain.com/.well-known/apple-app-site-association

Note:Setup SSL would be better(Guide)[https://github.com/oliguo/Server_Deployment/blob/master/XAMPP.md#ssl-setup-with-xamppauthorized-with-godaddy]

And can verify on (Apple App Site Association (AASA) Validator)[https://branch.io/resources/aasa-validator/]

### Linking Program Page(Use PHP for sample)
```php
<?php
$PATH_INFO = $_SERVER['PATH_INFO'];//pass from htaccss
$PI = explode("/", mb_substr(trim($PATH_INFO), 1));
//print_r($PI);
$deeplink_type = trim($PI[0]);
$deeplink_id = trim($PI[1]);
//Detect special conditions devices
$iPod = stripos($_SERVER['HTTP_USER_AGENT'], "iPod");
$iPhone = stripos($_SERVER['HTTP_USER_AGENT'], "iPhone");
$iPad = stripos($_SERVER['HTTP_USER_AGENT'], "iPad");
$Android = stripos($_SERVER['HTTP_USER_AGENT'], "Android");
$AndroidDownurl = "https://play.google.com/store/apps/details?id=com.domain.linkingappsetup";
$iOSDownurl = "https://itunes.apple.com/hk/app/happiness/id123456789?ls=1&mt=8";

$deeplink_url = "linkingappsetup://linkingappsetup.domain.com/" . $deeplink_type . "/" . $deeplink_id;
$app_url = "";
if ($iPod || $iPhone) {
    //browser reported as an iPhone/iPod touch -- do something here
    //header("Location: $iOSDownurl");
    $app_url = $iOSDownurl;
} else if ($iPad) {
    //browser reported as an iPad -- do something here
    //header("Location: $iOSDownurl");
    $app_url = $iOSDownurl;
} else if ($Android) {
    //browser reported as an Android device -- do something here
    //header("Location: $AndroidDownurl");
    $app_url = $AndroidDownurl;
} else {
    //browser reported as a webOS device -- do something here
    if (strpos($_SERVER['HTTP_USER_AGENT'], "Win") !== FALSE) {
        //header("Location: $AndroidDownurl");
        $app_url = $AndroidDownurl;
    } else if (strpos($_SERVER['HTTP_USER_AGENT'], "Mac") !== FALSE) {
        //header("Location: $iOSDownurl");
        $app_url = $iOSDownurl;
    }
}

$og_url="linkingappsetup://linkingappsetup.domain.com/". $deeplink_type . "/" . $deeplink_id;
$og_site_name = "Linking App Setup";
$og_title = "Linking App Setup";
$og_description = "For iOS-Universal-Links, Deep Link, URL Scheme to open App";
$og_image = "../Your_icon.png";
$og_image_width = "512";
$og_image_height = "512";
if (!empty($deeplink_type) && (!empty($deeplink_id))) {
    if (in_array($deeplink_type, ['page_1', 'page_2'])) {
        //fetch some information to replace og data.
        //$og_image='...';
        //$og_description='...';
    } else {
        $deeplink_url="";
    }
} else {
    $deeplink_url="";
}
if (!empty($deeplink_url)) {
    //sleep(1);
    //header("Location:".$deeplink_url);
} else {
    $deeplink_url = $app_url;
}
?>
<!DOCTYPE html>
<html>
    <head prefix="og: http://ogp.me/ns# fb: http://ogp.me/ns/fb# website: http://ogp.me/ns/website#">
        <title><?php echo $og_title; ?></title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy" crossorigin="anonymous"></script>
        <link rel="icon" href="../images/Your_icon_ico.ico" type="image/x-icon" />
        <link rel="shortcut icon" href="../images/Your_icon_ico.ico" type="image/x-icon" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta property="og:type" content="website">
        <meta property="fb:app_id" content="Add_Your_fb_app_id_if_you_have_for_graph_api"/>
        <meta propery="og:site_name" content="<?php echo $og_site_name ?>"/>
        <meta property="og:url"      content="<?php echo $og_url ?>" />
        <meta property="og:title"    content="<?php echo $og_title ?>" />
        <meta property="og:description"  content="<?php echo $og_description ?>" />

        <link rel="apple-touch-icon" href="../images/Your_apple_touch_icon.png"/>
        <meta property="al:ios:url" content="<?php echo $og_url ?>">
        <meta property="al:ios:app_store_id" content="Your_apple_store_app_id">
        <meta property="al:ios:app_name" content="Your_App_Name">
        <meta property="al:android:url" content="<?php echo $og_url ?>">
        <meta property="al:android:package" content="com.domain.linkingappsetup">
        <meta property="al:android:app_name" content="linkappsetup">
        <?php
        if (!empty($og_image)) {
            ?>
            <meta property="og:image"        content="<?php echo $og_image ?>"/>
            <meta property="og:image:width"  content="<?php echo $og_image_width ?>"/>
            <meta property="og:image:height"  content="<?php echo $og_image_height ?>"/>
            <?php
        }
        ?>
    </head>
    <body>
        <div class="row">
            <div class="col-md-12">
                <a class="btn btn-block"><img src="../images/Your_app_icon.png" height="100"/></a>
                <a class="btn btn-block" href="<?php echo $AndroidDownurl;?>"><img src="../images/Google_Play.png" height="130"/></a>
                <a class="btn btn-block" href="<?php echo $iOSDownurl;?>"><img src="../images/Apple_Store.png" height="100"/></a>
            </div>
        </div>
        <script type="text/javascript">
            setTimeout(function () {
                location.href = "<?php echo $deeplink_url; ?>";
                return false;
            }, 500);
        </script>
    </body>
</html>
```

### Add Rewrite Rule to .htacess for handle URL
```
RewriteEngine on

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

RewriteRule ^(.*)$ index.php/$1 [L]
```

### Verify and Test it

https://linkingappsetup.domain.com/page_1/data/123

And can check with (App Search API Validation Tool)[https://search.developer.apple.com/appsearch-validation-tool/]

Reference:

[The Open Graph protocol](http://ogp.me/)


