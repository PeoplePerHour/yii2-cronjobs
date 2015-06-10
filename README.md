Yii2 cronjobs extension
========
Easiest way to put crontab on your console scripts.

This extension is based on [this](https://github.com/Yiivgeny/Yii-PHPDocCrontab).
Thanks [Yiivgeny](https://github.com/Yiivgeny)

and for yii2 is based on [this](https://github.com/DenisOgr/yii2-cronjobs).

But with a few changes:
- Work with yii2
- Set config in params (not in phpDocs).

I transfer ​​settings of crontab in local settings(params) configuration, so that the application can be run on different servers with different sets of crontab.

Installation
------------

- **Step 1:** The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist peopleperhour/yii2-cronjobs "dev-master"
```

or add

```
"peopleperhour/yii2-cronjobs": "dev-master"
```

to the require section of your `composer.json` file.
- **Step 2:** Set aliase  @runnerScript in console config. This absolutely path to runner script (I can not find another way to get runner script).
Change path to runner script as your project. 
```
Yii::setAlias('@runnerScript', dirname(dirname(dirname(__FILE__))) .'/yii');
```
- **Step 3:** Add to console config:
```
'controllerMap' => [
       'cron' => [
           'class' => 'peopleperhour\cronjobs\CronController'
       ],
   ],
```
- **Step 4:**  Add task to system scheduler (cron on unix, task scheduler on windows) to run every minute:

```sh
* * * * * /path/to/yii/application/protected/yii cron
```
Usage
-----

Add in params array with cron sets:
```
'params' => [
  'test/example1' => [
    'cron'      => '* * * * *',            
  ],
  'test/example2' => [
    'cron'      => '10 * * * *',            
  ],
],
```
- **Step 5:** Add logging

You can add info about logging in the controllerMap cron config as well as on each cron configuration.

In the controllerMap you can add the logFileName variable as well as the up[dateLogFile. In the cron config you can add stdout and stderr.

Usage
----
```
'params' => [
  'test/example1' => [
    'cron'        => '* * * * *',
    'cron-stdout' => 'example1-out',
    'cron-stderr' => 'example1-err'
  ],
  'test/example2' => [
    'cron'      => '10 * * * *',            
  ],
],
```

and you can also add to the controllerMap

Usage
----
```
'controllerMap' => [
  'cron' => [
   'class' => 'peopleperhour\cronjobs\CronController'
   'logFileName'    => '/cron-output.log',
   'updateLogFile'  => true    
  ],
],
```

- **Step 6:** Add tags to run on specific environments

Should you need to separate environments when running your crons you can add a cron-tags parameter to the cron config

Usage
----
```
'params' => [
  'test/example1' => [
    'cron'        => '* * * * *',
    'cron-stdout' => 'example1-out',
    'cron-stderr' => 'example1-err'
    'cron-tags'   => 'live staging'
  ],
],
```

Then run your cron command with the tag requested inside your scheduler

eg

```sh
* * * * * /path/to/yii/application/protected/yii cron live
```

