CronTaskBot
=====================
2018-03-22 -> 2021-03-05



A helper to monitor your cron tasks with a database.


This is part of the [universe framework](https://github.com/karayabin/universe-snapshot).


Install
==========
Using the [planet installer](https://github.com/lingtalfi/Light_PlanetInstaller) via [light-cli](https://github.com/lingtalfi/Light_Cli)
```bash
lt install Ling.CronTaskBot
```

Using the [uni](https://github.com/lingtalfi/universe-naive-importer) command.
```bash
uni import Ling/CronTaskBot
```

Or just download it and place it where you want otherwise.





Targeted audience
---------------
You have an app and a backoffice, and you want to be able to monitor all your app related cron tasks from your backoffice.




Overview
-------------

There is a bot object (CronTaskBot), and a collection of tasks, which is just an array of CronTask object.

A CronTask object has an execute method, and a label properties.
Once executed, it gives us information about the task execution: 

- was it successful?
- does it have any info you want to log?
- does it have any error messages?


The CronTask object is designed so that the records appear like this in a database:

- task_name: the name of the task (dev version)
- task_label: the label of the task (human version)
- execution_date: the precise datetime at which the task was executed
- is_successful: 0|1 
- info_messages: a serialized version of an array of some info delivered by the task 
- error_messages: a serialized version of an array of some error messages delivered by the task 
- exception: the string version of the exception (if an exception occurred) 





How to
-------------

```php
<?php


use Ling\Bat\SessionTool;
use Core\Services\A;
use Ling\CronTaskBot\CronTask\BaseCronTask;
use Ling\CronTaskBot\CronTaskBot;
use Ling\CronTaskBot\Exception\CrontTaskBotException;
use Module\Ekom\Api\Entity\CartModelEntity;
use Module\Ekom\Api\Layer\CartLayer;
use Module\Ekom\Api\Layer\OrderStatusLayer;
use Module\Ekom\Api\Util\CartUtil;
use Module\ThisApp\Ekom\PaymentMethodHandler\CreditCardWalletPaymentMethodHandler;
use Ling\QuickPdo\QuickPdo;
use Ling\QuickPdo\Util\QuickPdoListInfoUtil;


// using kamille framework here (https://github.com/lingtalfi/kamille)
require_once __DIR__ . "/../boot.php";
require_once __DIR__ . "/../init.php";


A::testInit();


$tasks = [
    "sayHello" => BaseCronTask::create()
        ->setLabel("Saying hello")
        ->setTaskCallback(function (array &$infoMessages, array &$errorMessages) {
            $infoMessages[] = "hello cat was said";
            return true;
        }),
];


$rows = CronTaskBot::create()->executeTasks($tasks);

a($rows);

?>
Output is something like this:


array(1) {
    [0] => array(8) {
        ["task_name"] => string(8) "sayHello"
        ["task_label"] => string(12) "Saying hello"
        ["execution_start_date"] => string(19) "2018-03-22 11:35:55"
        ["execution_end_date"] => string(19) "2018-03-22 11:35:55"
        ["is_successful"] => int(1)
        ["info_messages"] => array(1) {
            [0] => string(18) "hello cat was said"
        }
        ["error_messages"] => array(0) {
        }
        ["exception"] => string(0) ""
    }
}


```




History Log
------------------

- 1.0.3 -- 2021-03-05

    - update README.md, add install alternative

- 1.0.2 -- 2020-12-08

    - Fix lpi-deps not using natsort.

- 1.0.1 -- 2020-12-04

    - Add lpi-deps.byml file

- 1.0.0 -- 2018-03-22

    - initial commit


