---
layout: post
title: "Self diagnosis for PHP applications"
description: "Diagnostic checks for php web applications using symfony 2 or zend framework 2"
category: development
tags: [monitoring, symfony2, zf2, oss]
---
{% include JB/setup %}

Every IT infrastructure has a monitoring solution these days. Probably you use Nagios or Icinga to keep it working. But 
how to monitor dependencies of your PHP application? Lets assume your app uses a MySQL database, a memcached server and 
has some deps to different config files which are externally maintained. Because your app writes file caches, there must 
also be some writable directories.

You can take your Nagios and watch the mysql process and check if the files are available, but will this do the whole 
job? What if the PHP-MySQL module is missing, or the webservers’ host can’t establish a connection to the database 
server?

At this point it may makes sense, that the web-application itself checks its dependencies.

## ZendDiagnostics

[This library](https://github.com/zendframework/ZendDiagnostics) allows every PHP-App to run self-diagnostic checks for 
different dependencies. There is a full [integration for ZF2](https://github.com/zendframework/ZendDiagnostics#using-diagnostics-with-zend-framework-2)
, but the checks can be used in every [plain PHP](https://github.com/zendframework/ZendDiagnostics#using-diagnostics-in-plain-php) 
app.

## LiipMonitorBundle

You may be asking yourself “wheres the Symfony integration?”. This is the [LiipMonitorBundle](https://github.com/liip/LiipMonitorBundle)
. It integrates all currently stable checks from ZendDiagnostics and has some more Symfony specific checks like testing 
Symfonys requirements, version and Doctrine dbal conections.

## Overview of currently available checks

### ZendDiagnostics

* **ApcFragmentation** – check if APC memory fragmentation is below given threshold,
* **ApcMemory** – check available APC memory,
* **Callback** – call a user-defined diagnostic function,
* **ClassExists** – make sure class exists in current environment,
* **CpuPerformance** – check server CPU performance is above baseline,
* **DirReadable** – make sure given path is readable,
* **DirWritable** – make sure given path is writable,
* **DiskFree** – check there’s enough free space on given path,
* **ExtensionLoaded** – make sure extension is loaded,
* **HttpService** – check if given http host is responding,
* **Memcache** – check if memcache extension is loaded and given server is reachable,
* **PhpVersion** – make sure that PHP version matches constraint,
* **PhpFlag** – make sure that given PHP flag (feature) is turned on or off.
* **ProcessRunning** – check if a process with given name or ID is currently running,
* **SecurityAdvisory** – check installed composer dependencies against SensioLabs SA database,
* **StreamWrapperExists** – make sure given stream wrapper is available.
* **IniFile** – check if given INI file is available and valid
* **JsonFile** – check if given JSON file is available and valid
* **XmlFile** – check if given XML file is available and valid
* **YamlFile** – check if given YAML file is available and valid

### LiipMonitorBundle

* **CustomErrorPagesCheck** – Checks if error pages have been customized for given error codes.
* **DiskUsageCheck** – Checks that disk usage percentage is under a specific warning/critical threshold.
* **DoctrineDbalCheck** – Checks that a doctrine dbal connection is available.
* **RabbitMQCheck** – Checks that a RabbitMQ server is running.
* **RedisCheck** – Checks that a Redis server is running.
* **SymfonyVersionCheck** – Checks the version of this website against the latest stable release.
* **SymfonyRequirements** – Checks Symfony2 requirements file.

The lists may be incomplete. Check the GitHub pages for up-to-date checks overview:

[github.com/zendframework/ZendDiagnostics](https://github.com/zendframework/ZendDiagnostics)

[github.com/liip/LiipMonitorBundle](https://github.com/liip/LiipMonitorBundle)

## Rolling your own

Of course you can write your own tests. All you have to do, is to implement ```ZendDiagnostics\Check\CheckInterface```. 
Follow [these instructions](https://github.com/zendframework/ZendDiagnostics#writing-custom-checks) to get your own 
tests running…