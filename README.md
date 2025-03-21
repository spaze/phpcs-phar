# PHP_CodeSniffer phar releases, automated and always up-to-date

[![Create a phar release](https://github.com/spaze/phpcs-phar/actions/workflows/create-release.yml/badge.svg)](https://github.com/spaze/phpcs-phar/actions/workflows/create-release.yml)
[![PHP Tests](https://github.com/spaze/phpcs-phar/actions/workflows/php.yml/badge.svg)](https://github.com/spaze/phpcs-phar/actions/workflows/php.yml)

This repo contains phar releases of [PHPCSStandards/PHP_CodeSniffer](https://github.com/PHPCSStandards/PHP_CodeSniffer).

The files are downloaded from the [PHP_CodeSniffer release pages](https://github.com/PHPCSStandards/PHP_CodeSniffer/releases),
and if changed, they're commited and a release is created with the same version number as the upstream release.

The whole thing is powered by a GitHub Actions [workflow](.github/workflows/create-release.yml).

I have created this for my personal usage to overcome the [performance issues](https://github.com/microsoft/WSL/discussions/9412) of the 9P filesystem as used by WSL2.
The PHPCSStandards organization is also planning to [create a Composer phar release](https://github.com/PHPCSStandards/PHP_CodeSniffer/issues/318) so this repository will cease to exist one day,
either when the official Composer phar is released, or when the 9P performace is sufficient, or when I retire. Consider yourself warned.

## Installation
Install with [Composer](https://getcomposer.org/):
```
composer require --dev spaze/phpcs-phar
```

## Installing coding standards
The phar releases in this repository do not work with the PHP_CodeSniffer Standards Composer Installer Plugin (the [`dealerdirect/phpcodesniffer-composer-installer`](https://packagist.org/packages/dealerdirect/phpcodesniffer-composer-installer) package).
Be warned that this package even replaces the installer plugin in its `composer.json`.

There are several reasons for that:
1. The installer plugin [looks](https://github.com/PHPCSStandards/composer-installer/blob/290bcb677628f4d829f64a4337bf0b9237238f0b/src/Plugin.php#L47) for a different installed package, `squizlabs/php_codesniffer` to add more standards to
2. The plugin creates a configuration file with a relative path to the standard being installed, so the code sniffer then expects the standard to be present inside the phar file, which is not the case

However, there are multiple different ways to install a standard. Let's say you want to add [`slevomat/coding-standard`](https://github.com/slevomat/coding-standard), you can:

### Auto-install the available standards
If the `CodeSniffer.conf` file doesn't exist, this package will find and auto-install all available coding standards on each execution.
The altered configuration is not persisted, no config file will be created, because it would be removed on each package update anyway.
This is the recommended option emulating the Standards Composer Installer Plugin to a certain point. The performance is comparable to
using the installer plugin as only Composer metadata are used to find the installed coding standards.

### Modify the config file
1. In your project, run
   ```
   vendor/bin/phpcs --config-set installed_paths ../../slevomat/coding-standard
   ```
3. Locate the `CodeSniffer.conf` file in your `vendor/spaze/phpcs-phar` directory
4. Edit the path to the installed standard and make it an absolute path, for example change
   ```php
   'installed_paths' => '../../slevomat/coding-standard'
   ```
   to
   ```php
   'installed_paths' => __DIR__ . '/../../slevomat/coding-standard'
   ```
Please be aware that the `CodeSniffer.conf` file will be removed on each package update.

### Install the standard using an absolute path
In your project, run
```
vendor/bin/phpcs --config-set installed_paths $(realpath vendor/slevomat/coding-standard)
```
or use any absolute path for the `installed_paths` value.

Please be aware that the `CodeSniffer.conf` file will be removed on each package update.
