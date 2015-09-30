---
layout: post
title: Membuat Script Handler [Composer Hook]
categories: [composer, script-handler, composer-hook]
tags: [composer, hook, post-install, post-update]
description: Tutorial singkat cara membuat script handler [composer hook] untuk otomatisasi "task" yang akan dijalankan ketika composer install atau composer update. 
comments: true
---

## Apa itu script handler?

Script handler adalah sebuah callable function yang dipanggil selama proses composer install dan/atau update. Lebih tepatnya, script ini akan di eksekusi setelah composer selesai
mendownload dependencies. Script handler dapat juga disebut sebagai composer hook.

## Membuat Script handler

Untuk membuat script handler sangatlah mudah, berikut adalah contohnya:

{% highlight php %}
<?php

namespace Composer\Hook;

use Sensio\Bundle\DistributionBundle\Composer\ScriptHandler;
use Composer\Script\CommandEvent;

class DoctrineClearCache extends ScriptHandler
{
    public static function doctrineClearCache(CommandEvent $event)
    {
        $options = self::getOptions($event);
        $appDir = $options['symfony-app-dir'];

        if (!is_dir($appDir)) {
            echo 'The symfony-app-dir ('.$appDir.') specified in composer.json was not found in '.getcwd().', can not clear the cache.'.PHP_EOL;

            return;
        }

        static::executeCommand($event, $appDir, 'doctrine:cache:clear-metadata', $options['process-timeout']);
        static::executeCommand($event, $appDir, 'doctrine:cache:clear-query', $options['process-timeout']);
        static::executeCommand($event, $appDir, 'doctrine:cache:clear-result', $options['process-timeout']);
    }
}
{% endhighlight %}

Script diatas akan menghapus doctrine cache yaitu metadata, query dan juga result cache.

## Register Script handler ke composer.json

Script diatas tinggal kita registerkan di composer.json

{% highlight json %}
{
    "scripts": {
        "post-install-cmd": [
            "Composer\\Hook\\DoctrineClearCache::doctrineClearCache"
        ],
        "post-update-cmd": [
            "Composer\\Hook\\DoctrineClearCache::doctrineClearCache"
        ]
    }
}
{% endhighlight %}

Mudah bukan? Semoga bermanfaat 