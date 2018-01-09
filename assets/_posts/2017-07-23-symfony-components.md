---
layout: post
title: "Symfony Components In The Wild - Jakub Zalas!"
date: 2017-07-23T23:47:13+05:00
categories: symfony
comments: true
<!-- published: false -->
---

Symfony is a set of reusable and decoupled PHP components designed to solve common web development problems. While as a framework it might not be the best for some of your projects, you can always build on top of its solid foundation of well written, tested and flexible components.

## Key Points

* Symfony1 is much different from symfony2. Symfony1 was just a glue between couple of open source projects and you can't use symfony without symfony framework.
* Symfony2 is a completely new rewrite.
* Symfony is basically a Request Response Framework. (HTTP Foundation)
* Symfony Just raises the bar that How PHP works.


## Symfony Compnentes By Categories

* There was something broken/missing in PHP that need to be fix. Borken doens't meant broken , actually it means it needs alot improvment. And Symfony tries to fix some of these things like HTTP Foundation, etc.


### HTTP Foundation

#### Background:

* Symfony HTTP foundation tries to fix PHP as below global states and variables;

{% highlight php %}
 $_GET
 $_POST
 $_SERVER
 $_COOKIE
 $_FILES
{% endhighlight %}

* Global Variables are difficult to maintain Because global variables can be modified by the process without other parts of your code knowing it producing unexpected results.
* Global variables if not used carefully can make problems harder to find. Let's say you request a php script and you get a warning saying you're trying to access an index of an array that does not exist in some function.

* If the array you're trying to access is local to the function, you check the function to see if you have made a mistake there. It might be a problem with an input to the function so you check the places where the function is called.

* But if that array is global, you need to check all the places where you use that global variable, and not only that, you have to figure out in what order those references to the global variable are accessed.


####  WHAT HTTP Foundation Does:

* 1st thing need to fix that these Golabal Variables should'nt be access like this.
* It should implemented via some oop way.

{% highlight php %}
<?php
use Symfony\Component\HttpFoundation\Request;

$request = Request::createFromGlobals();

$request->query
$request->request
$request->server
$request->cookies
$request->files
$request->headers
$request->attributes

Now by using above HTTP Foundation Request class we get all those global variables via an oop interface.

$id = isset($_GET['id']) ? $_GET['id'] : -1;
becomes
$id = $request->query->get('id', -1);
{% endhighlight %}

So now if we need to do something with these global variables we can do this way;

{% highlight php %}
<?php
use Symfony\Component\HttpFoundation\Request;

$request = Request::createFromGlobals();
$request->attributes->set('id', 15);

function myController(Request $request) {
	$id = $request->attributes->get('id');

}
{% endhighlight %}

Same can go for Response and other's too.
{% highlight php %}
<?php
// in normal php we need to send some header's info

header('X-Event: SymfonyCon`);
header('Cache-Control: public`);
header('Expires: '.$dateto,e`);

// but using HTTP Foundation
use Symfony\Component\HttpFoundation\Request;

$response = new Response('Hello', 200);
$response->headers->set('X-Event: SymfonyCon`);
$response->setPublic();
$response->setExpires(new \DateTime('+1hour'));
$response->send();
{% endhighlight %}

Behind the Scene HTTP Foundation uses Guzzle HTTP to work.


### YML

* For a long time php was missing some native support for config. So Yaml was introduces in Symfony.
{% highlight php %}
	use Symfony\Component\Yaml\Yaml;
{% endhighlight %}

Symfony doens't implement the whole yaml, it just used that only needed for symfony i.e alias, fixtures, configs etc.

### DEBUG

For a long time we see ugly type of error list in php.
![ugly type of error list in php]({{ site.raw_static_url }}/2017-07-23-symfony-components/004.png)

So, by enable symfony debug component in you development mode `\Symfony\Component\Debug\Debug::enable();` we get nice exception with details too.

![ugly type of error list in php]({{ site.raw_static_url }}/2017-07-23-symfony-components/005.png)

### PROCESS
* Another thing about component is they sometimes hide the ugliness of PHP. Like process component to run process from php. It hides the details of the OS you run.
* You can run a OS proces with combiniation of PHP like stop a proces, sleep, md etc.

{% highlight php %}
<?php

use Symfony\Component\Process\Process;

$process = new Porcess('ls -l');
$exitCode = $process->run();
$output = $process->getOutput();
{% endhighlight %}

* If you see the source code of process code you will see alot of if else and exception handling. So this Process component hides that Part of the Job.
* it gives you the Object oriented interface and ensure that it will run on any plateform. Like it starts your php webserver regardless of the OS with same command.

{% highlight php %}
<?php

$server = new HttpServer\HttpServer( 'var/www', 'localhost', 8000);

$server->start();
$contents = file_get_contents('http://localhost:8080/example.php');
$server->stop();
{% endhighlight %}

and it invokes this classs
{% highlight php %}
<?php

class HttpServer extends PhpProcess{

   public function start($callback = null){ }
}
{% endhighlight %}

### Event Dispatcher

* When you are working with Symfony you become better developer because symfony always promotes best practices using best patterns like decoupling in Event Dispatcher.

![ugly]({{ site.raw_static_url }}/2017-07-23-symfony-components/006.png)

* Like if you have to send Email on User Registration. You just make mailer listner and attache it to dispather. Then on registraiton process complete just call the dispatcher.
