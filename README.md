# Composer template for Drupal projects

[![Build Status](https://travis-ci.org/jasloe/drupal-project.svg?branch=8.x)](https://travis-ci.org/jasloe/drupal-project)

This project template provides a starter kit for managing Drupal site
dependencies with [Composer](https://getcomposer.org/).

This fork is a modified version of the "official" drupal-composer repository. It includes a handful of conveniences and customizations for use in both development and production.

These include:

- require oomphinc/composer-installers-extender": "^1.1"
- adding library and npm-asset installer types
- including custom paths for commonly used third-party libraries
- baked in support for DrupalVM
- custom vm directory for use by DrupalVM
- post-install composer scripts (create library directory, build resources for theme suggestions)
- Travis CI build tests, etc.
- post-install cmds
- will inevitably include core patches
  ....

Usage notes below are excerpted from the drupal-composer repository.

## Usage

```
composer create-project jasloe/drupal-project:8.x-dev some-dir
```

The `composer create-project` command passes ownership of all files to the
project that is created. You should create a new git repository, and commit
all files not excluded by the .gitignore file.

## What does the template do?

When installing the given `composer.json` some tasks are taken care of:

- Drupal will be installed in the `web`-directory.
- Autoloader is implemented to use the generated composer autoloader in `vendor/autoload.php`,
  instead of the one provided by Drupal (`web/vendor/autoload.php`).
- Modules (packages of type `drupal-module`) will be placed in `web/modules/contrib/`
- Theme (packages of type `drupal-theme`) will be placed in `web/themes/contrib/`
- Profiles (packages of type `drupal-profile`) will be placed in `web/profiles/contrib/`
- Creates default writable versions of `settings.php` and `services.yml`.
- Creates `web/sites/default/files`-directory.
- Latest version of drush is installed locally for use at `vendor/bin/drush`.
- Latest version of DrupalConsole is installed locally for use at `vendor/bin/drupal`.
- Creates environment variables based on your .env file. See [.env.example](.env.example).

## Updating Drupal Core

This project will attempt to keep all of your Drupal Core files up-to-date; the
project [drupal/core-composer-scaffold](https://github.com/drupal/core-composer-scaffold)
is used to ensure that your scaffold files are updated every time drupal/core is
updated. If you customize any of the "scaffolding" files (commonly .htaccess),
you may need to merge conflicts if any of your modified files are updated in a
new release of Drupal core.

Follow the steps below to update your core files.

1. Run `composer update drupal/core drupal/core-dev --with-dependencies` to update Drupal Core and its dependencies.
2. Run `git diff` to determine if any of the scaffolding files have changed.
   Review the files for any changes and restore any customizations to
   `.htaccess` or `robots.txt`.
3. Commit everything all together in a single commit, so `web` will remain in
   sync with the `core` when checking out branches or running `git bisect`.
4. In the event that there are non-trivial conflicts in step 2, you may wish
   to perform these steps on a branch, and use `git merge` to combine the
   updated core files with your customized files. This facilitates the use
   of a [three-way merge tool such as kdiff3](http://www.gitshah.com/2010/12/how-to-setup-kdiff-as-diff-tool-for-git.html). This setup is not necessary if your changes are simple;
   keeping all of your modifications at the beginning or end of the file is a
   good strategy to keep merges easy.

## Generate composer.json from existing project

With using [the "Composer Generate" drush extension](https://www.drupal.org/project/composer_generate)
you can now generate a basic `composer.json` file from an existing project. Note
that the generated `composer.json` might differ from this project's file.

## FAQ

### Should I commit the contrib modules I download?

Composer recommends **no**. They provide [argumentation against but also
workrounds if a project decides to do it anyway](https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md).

### Should I commit the scaffolding files?

The [Drupal Composer Scaffold](https://github.com/drupal/core-composer-scaffold) plugin can download the scaffold files (like
index.php, update.php, …) to the web/ directory of your project. If you have not customized those files you could choose
to not check them into your version control system (e.g. git). If that is the case for your project it might be
convenient to automatically run the drupal-scaffold plugin after every install or update of your project. You can
achieve that by registering `@composer drupal:scaffold` as post-install and post-update command in your composer.json:

```json
"scripts": {
    "post-install-cmd": [
        "@composer drupal:scaffold",
        "..."
    ],
    "post-update-cmd": [
        "@composer drupal:scaffold",
        "..."
    ]
},
```

### How can I apply patches to downloaded modules?

If you need to apply patches (depending on the project being modified, a pull
request is often a better solution), you can do so with the
[composer-patches](https://github.com/cweagans/composer-patches) plugin.

To add a patch to drupal module foobar insert the patches section in the extra
section of composer.json:

```json
"extra": {
    "patches": {
        "drupal/foobar": {
            "Patch description": "URL or local path to patch"
        }
    }
}
```
