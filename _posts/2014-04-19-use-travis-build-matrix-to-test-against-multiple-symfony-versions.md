---
layout: post
title: "Use Travis build matrix to test against multiple Symfony versions"
description: "Travis CI build matrixes ca be used to test about multiple symfony and php versions"
category: quality-assurance
tags: [travis, ci, symfony, php]
---
{% include JB/setup %}

[Travis CI](https://travis-ci.org/) is a great Continuous Integration service for building your open source software. 
It’s similar to [Jenkins CI](http://jenkins-ci.org/). The cloud based executor Vm’s offer different PHP versions which 
can be arranged in a build matrix. Especially for projects like Symfony 2 bundles, one would test against different 
PHP- and Symfony versions. This can be easily configured with a [Composer setup](https://getcomposer.org/) and the 
right Travis configuration.

First, we want to test our bundle with PHP versions 5.3, 5.4, 5.5, and hhvm:

```yaml
# .travis.yml
language: php
php:
  - 5.3
  - 5.4
  - 5.5
  - hhvm
```

With this configration, Travis will execute 4 builds with the PHP versions above. To test also against different 
Symfony versions, we have to configure our travis environment variables in a similar way:

```yaml
# .travis.yml

env:
  - SYMFONY_VERSION=2.3.*
  - SYMFONY_VERSION=2.4.*
  - SYMFONY_VERSION=2.5.*@dev
 
cache:
  directories:
    - $COMPOSER_CACHE_DIR
```

The cache directory configuration makes the VMs cache the composer packages. This is not mandatory, but improves build 
performance. With a last step, we tell composer to install the Symfony package for each build on demand. The 
SYMFONY_VERSION variable provides the version string for Composer:

```yaml
# .travis.yml

before_script:
  - composer require --prefer-source --dev symfony/symfony:${SYMFONY_VERSION}
```

PHP and Symfony versions are multiplied in the Travis build matrix, so for each PHP version there run 3 builds for the 
different Symfony versions (like the screenshot above). Tee following build matrix shows the configuration of the 
versions:

![travis build matrix](/assets/images/20140419_travis_build_matrix.png)

The configuration can be extended to your needs, but builds will take more time, the more versions you add.

If you want to see this build configuration in the wild, the 
[Neo4jUserBundle](https://github.com/frne/Neo4jUserBundle#neo4juserbundle) is tested using this configuration:

```yaml
# .travis.yml

language: php
 
php:
  - 5.3
  - 5.4
  - 5.5
  - hhvm
 
env:
  - SYMFONY_VERSION=2.3.*
  - SYMFONY_VERSION=2.4.*
  - SYMFONY_VERSION=2.5.*@dev
 
allow_failures:
    - php: hhvm
 
before_script:
  - composer require --prefer-source --dev symfony/symfony:${SYMFONY_VERSION}
 
script: phpunit --coverage-text --coverage-clover=coverage.clover
 
after_script:
  - wget https://scrutinizer-ci.com/ocular.phar
  - php ocular.phar code-coverage:upload --format=php-clover coverage.clover
 
cache:
  directories:
    - $COMPOSER_CACHE_DIR
```