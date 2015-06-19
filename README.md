Yii2 cronjobs extension
========
Easiest way to put crontab on your console scripts.

This extension is based on [this repo](https://github.com/Yiivgeny/Yii-PHPDocCrontab) for yii1 and [this](https://github.com/DenisOgr/yii2-cronjobs) for yii2.

The peopleperhour package adds some more features such as
1) extra logging
2) correct cron-tags functionality

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
- **Step 4:**  Add task to system scheduler (cron on unix, task scheduler on windows, systemd service) to run every minute:

```sh
* * * * * /path/to/yii/application/protected/yii cron
```

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

There are two levels of logging provided.

a) Log the output of the cron mechanism

Logs events coming into the cron mechanism.

Sample Output:

2015-06-11 09:48:36 [-][-][-][info][cronjobs] Running task [1]: runtestcron test
    in /var/www/vendor/peopleperhour/yii2-cronjobs/CronController.php:260
2015-06-11 09:48:36 [-][-][-][info][cronjobs] Runned 1 task(s) at Thu, 11 Jun 2015 09:48:36 +0000
    in /var/www/vendor/peopleperhour/yii2-cronjobs/CronController.php:264

Set up:

In your config, set the below in your log component:

```
'components' => [
  'log' => [
    'targets' => [
      [
        'class'         => 'yii\log\FileTarget',
        'logFile'       => 'somelogfile.log',
        'categories'    => ['yii2-cronjobs']
      ],
    ],
  ],
]
```

The "categories" element is the default category used in the peopleperhour/yii2-cronjobs extension. If you want to change it then enter the name desired above and also set the logsCategory field in the controllerMap.

eg.

```
'controllerMap' => [
       'cron' => [
           'class'        => 'peopleperhour\cronjobs\CronController',
           'logsCategory' => 'somenewcategory',
       ],
   ],
```

b) Log the output of commands running

There are two ways to log the output of the commands running. 

First is the catchall way, where you add the filename to your controllerMap.

eg

```
'controllerMap' => [
       'cron' => [
           'class'        => 'peopleperhour\cronjobs\CronController',
           'logFileName'  => 'somelog',
       ],
   ],
```

Secondly you can specify the output of each cron command specifically by changing the cron specification using the "cron-stdout" directive.

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

The above will overwrite the catchall output of the controllerMap and send the output to the cron-stdout specified log file.

- **Step 6:** Add tags to run on specific environments

Should you need to separate environments when running your crons you can add a cron-tags parameter to the cron config

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

