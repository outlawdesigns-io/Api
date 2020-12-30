# Api

This abstract class provides an easy to implement, requirement free REST api framework. Wanting to give credit where credit is due, we tried to find the original article from which we believe we essentially copied and pasted this code. As best we can tell, writing these docs two years after the fact, the inspiration came from [Corey Maynard's 'Creating a RESTful API with PHP'](http://coreymaynard.com/blog/creating-a-restful-api-with-php/).

`public function __construct($request)`

## Requirements

* None

## Properties

* None

## Methods

### public processApi():String<JSON>

Exposes a single public method which calls a method of the same name as the first argument in your url, as defined in your concrete class. Returns a JSON encoded string of the return data from your method.

```
$api = new EndPoint($_REQUEST['request'], $_SERVER['HTTP_ORIGIN']);
echo $api->processAPI();
```

## Usage

### .htaccess

```
<IfModule mod_rewrite.c>
Options +FollowSymLinks
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*)$ controller.php?request=$1 [QSA,NC,L]
</IfModule>
```

### EndPoint.php
```
<?php

require_once __DIR__ . '/libs/api.php';

class EndPoint extends API{

    public function __construct($request,$origin)
    {
        parent::__construct($request);
    }

    protected function example(){
        return array(
          "endPoint"=>$this->endpoint,
          "verb"=>$this->verb,
          "args"=>$this->args,
          "request"=>$this->request
          );
    }
}

```
### Controller
```
<?php

require_once __DIR__ '/EndPoint.php';
if (!array_key_exists('HTTP_ORIGIN', $_SERVER)) {
    $_SERVER['HTTP_ORIGIN'] = $_SERVER['SERVER_NAME'];
}
try{
    $api = new EndPoint($_REQUEST['request'], $_SERVER['HTTP_ORIGIN']);
    echo $api->processAPI();
}catch (Exception $e){
    echo json_encode(array('error'=>$e->getMessage()));
}

```

### Sample Call
#### Call
```
curl http://localhost/example/3/value2/value4
```
#### Response
```
{
    "endPoint": "example",
    "verb": null,
    "args": [
        "3",
        "value2",
        "value4"
    ],
    "request": {
        "request": "example/3/value2/value4"
    }
}
```
