[PHP](https://www.php.net) is a widely-used open source multi-purpose scripting language, which can be embedded in HTML and is especially suitable for Web development.

This article mainly introduces how to use the `php-mqtt/client` client library in PHP projects to implement the functions of connection, subscription, unsubscribing, message receiving and sending between [MQTT client](https://www.emqx.com/en/blog/introduction-to-the-commonly-used-mqtt-client-library) and [MQTT server](https://www.emqx.io).



## MQTT client library selection

This article chooses the client library `php-mqtt/client`, which has the highest downloads on composer. For more PHP-MQTT client libraries, please view in [Packagist-Search MQTT](https://packagist.org/search/?query =mqtt).

For more documentation about php-mqtt/client, please refer to [Packagist php-mqtt/client](https://packagist.org/packages/php-mqtt/client).

MQTT communication belongs to a network communication scenario outside the HTTP system. Due to the limitation of PHP characteristics, using the extensions for network communication such as Swoole/Workerman in the PHP system can bring a better experience. Its use will not be repeated in this article. The relevant MQTT client libraries are as follows:

- [workerman/mqtt](https://packagist.org/packages/workerman/mqtt)：Asynchronous MQTT client for PHP based on workerman.
- [simps/mqtt](https://packagist.org/packages/simps/mqtt)：[MQTT protocol](https://www.emqx.com/en/mqtt) Analysis and Coroutine Client for PHP.



## Project initialization

### Confirm PHP version

This project uses 7.4.21 for development and testing. Readers can confirm the PHP version with the following command.

```
php --version

PHP 7.4.21 (cli) (built: Jul 12 2021 11:52:30) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.21, Copyright (c), by Zend Technologies
```

### Use Composer to install php-mqtt/client

Composer is a dependency management tool for PHP, which can manage all the dependencies your PHP project needs.

```bash
composer require php-mqtt/client
```



## PHP MQTT usage

### Connect to MQTT server

This article will use the [Free Public MQTT Server](https://www.emqx.com/en/mqtt/public-mqtt5-broker) provided by EMQX, which is created on EMQX's [MQTT Cloud Service](https://www.emqx.com/en/cloud). The server access information is as follows:

- Broker: **broker.emqx.io**
- TCP Port: **1883**
- SSL/TLS Port: **8883**

#### Import composer autoload file and php-mqtt/client

```php
require('vendor/autoload.php');

use \PhpMqtt\Client\MqttClient;
```

#### Set MQTT Broker connection parameters

Set the MQTT Broker connection address, port and topic. At the same time, we call the PHP `rand` function to generate the MQTT client id randomly.

```php
$server   = 'broker.emqx.io';
$port     = 1883;
$clientId = rand(5, 15);
$username = 'emqx_user';
$password = null;
$clean_session = false;
```

#### Write MQTT connection function

Use the above parameters to connect, and set the connection parameters through `ConnectionSettings`, such as:

```php
$connectionSettings  = new ConnectionSettings();
$connectionSettings
  ->setUsername($username)
  ->setPassword(null)
  ->setKeepAliveInterval(60)
  ->setLastWillTopic('emqx/test/last-will')
  ->setLastWillMessage('client disconnect')
  ->setLastWillQualityOfService(1);
```

### Subscribe

Program to subscribe to the topic of `emqx/test`, and configure a callback function for the subscription to process the received message. Here we print out the topic and message obtained from the subscription:

```php
$mqtt->subscribe('emqx/test', function ($topic, $message) {
    printf("Received message on topic [%s]: %s\n", $topic, $message);
}, 0);
```

### Publish

Construct a payload and call the `publish` function to publish messages to the `emqx/test` topic. After publishing, the client needs to enter the polling status to process the incoming messages and the retransmission queue:

```php
for ($i = 0; $i< 10; $i++) {
  $payload = array(
    'protocol' => 'tcp',
    'date' => date('Y-m-d H:i:s'),
    'url' => 'https://github.com/emqx/MQTT-Client-Examples'
  );
  $mqtt->publish(
    // topic
    'emqx/test',
    // payload
    json_encode($payload),
    // qos
    0,
    // retain
    true
  );
  printf("msg $i send\n");
  sleep(1);
}

// The client loop to process incoming messages and retransmission queues
$mqtt->loop(true);
```

### Complete code

Server connection, message publishing and receiving code.

```php
<?php

require('vendor/autoload.php');

use \PhpMqtt\Client\MqttClient;
use \PhpMqtt\Client\ConnectionSettings;

$server   = 'broker.emqx.io';
$port     = 1883;
$clientId = rand(5, 15);
$username = 'emqx_user';
$password = null;
$clean_session = false;

$connectionSettings  = new ConnectionSettings();
$connectionSettings
  ->setUsername($username)
  ->setPassword(null)
  ->setKeepAliveInterval(60)
  ->setLastWillTopic('emqx/test/last-will')
  ->setLastWillMessage('client disconnect')
  ->setLastWillQualityOfService(1);


$mqtt = new MqttClient($server, $port, $clientId);

$mqtt->connect($connectionSettings, $clean_session);
printf("client connected\n");

$mqtt->subscribe('emqx/test', function ($topic, $message) {
    printf("Received message on topic [%s]: %s\n", $topic, $message);
}, 0);

for ($i = 0; $i< 10; $i++) {
  $payload = array(
    'protocol' => 'tcp',
    'date' => date('Y-m-d H:i:s'),
    'url' => 'https://github.com/emqx/MQTT-Client-Examples'
  );
  $mqtt->publish(
    // topic
    'emqx/test',
    // payload
    json_encode($payload),
    // qos
    0,
    // retain
    true
  );
  printf("msg $i send\n");
  sleep(1);
}

$mqtt->loop(true);

```


## Test

After running the MQTT message publishing code, we will see that the client has successfully connected, and the messages have been published one by one and received successfully:

```bash
php pubsub_tcp.php
```

![PHP MQTT Test](https://static.emqx.net/images/61618d56823886f101feaf6741a20c3f.png)


## Summary

So far, we have used the **php-mqtt/client** to connect to the [public MQTT server](https://www.emqx.com/en/mqtt/public-mqtt5-broker), and implemented the connection, message publishing and subscription between the test client and the MQTT server.


<section class="promotion">
    <div>
        Try EMQX Cloud for Free
        <div class="is-size-14 is-text-normal has-text-weight-normal">No credit card required</div>
    </div>
    <a href="https://www.emqx.com/en/signup?continue=https://cloud-intl.emqx.com/console/deployments/0?oper=new" class="button is-gradient px-5">Get Started →</a >
</section>
