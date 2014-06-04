JsonRPC PHP Client and Server
=============================

A simple Json-RPC client/server that just works.

Features
--------

- JSON-RPC 2.0 protocol only
- The server support batch requests and notifications
- Authentication and IP based client restrictions
- Minimalist: there is only 2 files
- License: Unlicense http://unlicense.org/

Requirements
------------

- The only dependency is the cURL extension
- PHP >= 5.3

Author
------

[Frédéric Guillot](http://fredericguillot.com)

Examples
--------

### Server

```php
<?php

require 'JsonRPC/Server.php';

use JsonRPC\Server;

$server = new Server;

// Procedures registration

$server->register('addition', function ($a, $b) {

    return $a + $b;
});

$server->register('random', function ($start, $end) {

    return mt_rand($start, $end);
});

// Return the response to the client
echo $server->execute();
```

### Client

Example with positional parameters:

```php
<?php

require 'JsonRPC/Client.php';

use JsonRPC\Client;

$client = new Client('http://localhost/server.php');
$result = $client->execute('addition', array(3, 5));

var_dump($result);
```

Example with named arguments:

```php
<?php

require 'JsonRPC/Client.php';

use JsonRPC\Client;

$client = new Client('http://localhost/server.php');
$result = $client->execute('random', array('end' => 10, 'start' => 1));

var_dump($result);
```

Arguments are called in the right order.
If there is an error, the `execute()` method return `NULL`.

Example with shortcut methods:

```php
<?php

require 'JsonRPC/Client.php';

use JsonRPC\Client;

$client = new Client('http://localhost/server.php');
$result = $client->random([50, 100]);

var_dump($result);
```

### IP based client restrictions

The server can allow only some IP adresses:

```php
<?php

require 'JsonRPC/Server.php';

use JsonRPC\Server;

$server = new Server;

// IP client restrictions
$server->allowHosts(array('192.168.0.1', '127.0.0.1'));

// Procedures registration

[...]

// Return the response to the client
echo $server->execute();
```

If the client is blocked, you got a 403 Forbidden HTTP response.

### HTTP Basic Authentication

If you use HTTPS, you can allow client by using a username/password.

```php
<?php

require 'JsonRPC/Server.php';

use JsonRPC\Server;

$server = new Server;

// List of users to allow
$server->authentication(array('jsonrpc' => 'toto'));

// Procedures registration

[...]

// Return the response to the client
echo $server->execute();
```

On the client, set credentials like that:

```php
<?php

require 'JsonRPC/Client.php';

use JsonRPC\Client;

$client = new Client('http://localhost/server.php');

// Credentials
$client->authentication('jsonrpc', 'toto');

$result = $client->execute('addition', array('a' => 2, 'b' => 2));
```
