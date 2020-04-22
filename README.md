# Rector for Drupal

Automate fixing deprecated Drupal code.

## Introduction

https://www.palantir.net/blog/jumpstart-your-drupal-9-upgrade-drupal-rector

## Issues are managed on [drupal.org](https://www.drupal.org/project/rector)

https://www.drupal.org/project/rector

For contribution suggestions, please see the later section of this document.

## Installation

### Install Drupal Rector inside a Drupal project.

```bash
$ composer require --dev palantirnet/drupal-rector
```

### Create a configuration file in your project

You will need to have a `rector.yml` configuration in the root of your repository. This should sit beside your document root such as `web` or `docroot`.

This project provides starting files that should handle most use cases.

If your document root directory is `web`, you can copy the `rector-config-web-dir.yml`

```bash
cp vendor/palantirnet/drupal-rector/rector-config-web-dir.yml rector.yml
```

If your document root directory is `docroot`, you can copy the `rector-config-docroot-dir.yml`

```bash
cp vendor/palantirnet/drupal-rector/rector-config-docroot-dir.yml rector.yml
```

If your document root directory is something else you will need to manually copy and edit `rector.yml`.

Replace the `web` in these paths with your document root.

```
parameters:
  autoload_paths:
    - 'web/core'
    - 'web/core/modules'
    - 'web/modules'
    - 'web/profiles'
```

# Suggested workflow

1. Analyze your code with Rector and review suggested changes:

```sh
$ vendor/bin/rector process web/modules/contrib/[YOUR_MODULE] --dry-run
```

2. Apply suggested changes:

```sh
$ vendor/bin/rector process web/modules/contrib/[YOUR_MODULE]
```

You can find more information about Rector [here](https://github.com/rectorphp/rector).

## Development

We recommend using our `drupal-rector-sandbox` development environment [https://github.com/palantirnet/drupal-rector-sandbox](https://github.com/palantirnet/drupal-rector-sandbox)

Alternatively, you can use your existing Drupal project and follow the instructions in [README](https://github.com/palantirnet/drupal-rector-sandbox/blob/master/README.md#developing-with-drupal-rector)

## Troubleshooting

### PhpStan composer issues

You may need to upgrade `phpstan/phpstan` with Composer before installing this package.

Rector itself has conflicts with older versions of PhpStan.

### Unable to find Rector rule classes

If you are getting errors like

`[ERROR] Class "DrupalRector\Rector\Deprecation\EntityManagerRector" was not found while loading`

You may need to rebuild your autoload file.

`composer dump-autoload`

## Contribution Suggestions

Thanks for your interest in contributing!

Our goal is to make contributing to this project easy for people. While we've made certain architectural decisions here to hopefully achieve that goal, it's a work in progress and feedback is appreciated.

### Adding a Rector rule

If you would like to submit a Rector rule, we are looking for the following:

- A Rector rule class, see `/src/Rector/Deprecation` for existing rules
- An example file or files that show(s) the before and after, see `/rector_examples` and `/rector_examples_updated`
- An updated configuration file that registers the Rector rule, see `/config/drupal-8`
- A listing in the index file, see `/deprecation-index.yml`
- An automated Behat functional test

#### Create a Rector rule class

Rector rules should be named after the deprecation, including the class name.

`Drupal::url()` -> `DrupalUrlRector.php`
`drupal_set_message()` -> `DrupalSetMessageRector.php`

We would like one Rector rule per deprecation. Some deprecations include updating multiple things and those would be separate rules.

To avoid duplication, we have created base classes for simple repeated patterns where possible. These end in `Base.php` and are located in `/src/Rector/Deprecation/Base`. In many of these rules, you will extend the base class, define class properties, add a class comment, and define the definition.

Rector supports passing parameters to rules and you can also define your rules in a variety of ways. To avoid confusion for new developers, we're trying to avoid these advanced features so that someone with limited familiarity with the tool can easily determine where things are located and what they are doing. If the copy & paste challenge isn't worth this trade-off, we can re-evaluate it as we go. Suggestions appreciated.

#### Create examples

We are creating pairs of example files.

These should be named the same thing as the deprecation. So, `DrupalUrlRector` has a `rector_examples/drupal_url.php` example. An example `rector_examples_updated/drupal_url.php` should also be created to show the updated code. You can run Drupal Rector on this file to show the update.

Example

`DrupalUrlRector` -> `rector_examples/drupal_url.php` and `rector_examples_updated/drupal_url`

If you would like to show how the code is used in a class, you can add the class to the appropriate place in the `/rector_examples/src` or `/rector_examples/test` directories. Most of the examples in the example module are `services` in that they are stand alone classes.

Since these classes can use static calls, dependency injection, or traits to get access to services, constants, etc, we have added more details to some class names. For example, `*Static` to indicate that the class is not using dependency injection.

Example

`DrupalUrlRector` -> `rector_examples/src/DrupalUrlStatic.php` and `rector_examples_updated/src/DrupalUrlStatic.php`

#### Create / Update a configuration file

The configuration files in `/config/drupal-8` are broken down by Drupal minor versions.

Add your Rector rule to the relevant file.

The key is the fully qualified class name of the Rector rule. The key is the yaml null value `~`.

#### Update the index file

The index file is used in part to provide automated updates to https://dev.acquia.com/drupal9/deprecation_status/errors which is a helpful way to track coverage. The `PHPStan` messages are listed there as well as in the change record comments throughout the Drupal codebase.

#### Create an automated Behat functional test

For more information on testing, see [README-automated-testing.md](README-automated-testing.md).

You can copy the example feature, `features/drupal_url.feature` and provide the example files you created above.

## Credits

Current development is sponsored by [Palantir.net](https://www.palantir.net).<br/>
Initial development is sponsored by [Pronovix](https://pronovix.com).
