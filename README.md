
# wp-bootstrap
Utils for bootstrapping a WordPress installations. Automates installation, configuration and content bootstrapping of WordPress installation. Wp-bootstrap uses two configuration files, appsettings.json and localsettings.json to control it's behaviour. 


## Installation

To add this package as a local, per-project dependency to your project, simply add a dependency on `eriktorsner/wp-bootstrap` to your project's `composer.json` file. Here is a minimal example of a `composer.json` file that just defines a dependency wp-bootstrap:

    {
        "require": {
            "eriktorsner/wp-bootstrap": "0.1.*"
        }
    }

**Note!:** wp-bootstrap assumes that wp-cli is globally available on the machine using the alias "wp". 

### Quick start

Wp-bootstrap can be called directly from it's binary, located in vendor/bin. To reduce typing, you can add the bootstrap commands to your composer file:

    $ vendor/bin/wpbootstrap wp-init-composer
    
Then to run a command:

    $ composer wp-export
    

## Commands

wp-bootstrap exposes a few command that can be called from various automation envrionments

| Command | Arguments | Decription |
|---------|------------|------------|
| wp-install || Download and install WordPress core. Creates a default WordPress installation |
| wp-setup  || Add themes and plugins and import content |
| wp-bootstrap || Alias for wp-install followed by wp-setup|
| wp-update |none, "themes" or "plugins"| Updates core, themes or plugins that are installed from the WordPress repos |
| wp-export || Exports content from the WordPress database into text and media files on disk|
| wp-import || Imports content in text and media files on disk into the database. Updates existing pages / media if it already exists |
| wp-pullsettings || Helper: Adds a wpbootstrap section to the appsettings file (if it doesn't already exist) |

## Usage

wp-bootstrap is intended to be executed from a script or task runner like Grunt, Gulp or Composer. It can be called directly from the command line or as part of a larger task in i.e Grunt:

**Command line usage:**

    $ vendor/bin/wpbootstrap wp-export
    $ vendor/bin/wpbootstrap wp-update plugins


**Grunt usage:**
Sample Grunt setup (just showing two methods):

    grunt.registerTask('wp-export', '', function() {
        cmd = "vendor/bin/wpbootstrap wp-export";
        shell.exec(cmd);
    });

    grunt.registerTask('wp-update', '', function() {
        cmd = "vendor/bin/wpbootstrap wp-update";
        if (typeof grunt.option('what') != 'undefined') cmd += ' ' + grunt.option('what');
        shell.exec(cmd);
    }); 

Then run your grunt task like this:

    $ grunt wp-export
    $ grunt wp-update --what=plugins



**Composer usage:**
Wp-bootstrap exposes it's commands as static functions so they are callable directly from a Composer script:

    "scripts": {
        "wp-bootstrap": "Wpbootstrap\\Bootstrap::bootstrap",
        "wp-install": "Wpbootstrap\\Bootstrap::install",
        "wp-setup": "Wpbootstrap\\Bootstrap::setup",
        "wp-update": "Wpbootstrap\\Bootstrap::update",
        "wp-export": "Wpbootstrap\\Export::export",
        "wp-import": "Wpbootstrap\\Import::import",
        "wp-pullsettings": "Wpbootstrap\\Initbootstrap::updateAppSettings",
        "wp-init-composer": "Wpbootstrap\\Initbootstrap::initComposer"
    }

Then run a method from the cli like this:

    $ composer wp-install
    $ composer wp-update plugins    



## Settings 

wp-bootstrap relies on 2 config files in your project root

**localsettings.json:**

    {
        "environment": "development",
        "url": "www.wordpressapp.local",
        "dbhost": "localhost",
        "dbname": "wordpress",
        "dbuser": "wordpress",
        "dbpass": "wordpress",
        "wpuser": "admin",
        "wppass": "admin",
        "wppath": "/vagrant/www/wordpress-default"
    
    }

The various settings in localsettings.json are self explanatory. This file is not supposed to be managed in source code control but rather be unique for each server where your WordPress site is installed (development, staging etc). 

**appsettings.json:**

    {
        "title": "Your WordPress site title",
        "plugins": {
            "standard": ["google-analyticator", "if-menu:0.2.1"],
            "local": ["myplugin"]
        },
        "themes": {
            "standard": ["twentyfourteen"],
            "local": ["mychildtheme"],
            "active": "mychildtheme"
        },
        "settings": {
            "blogname": "New title 2",
            "blogdescription": "The next tagline"
        },
        "wpbootstrap": {
            "posts": {
                "page": ["about","members"],
            },
            "menus": {
                "main": ["primary", "footer"]
            }
        }
    }

### Section: plugins:
This section consists of two sub arrays "standard" and "local". Each array contains plugin names that should be installed and activated on the target WordPress site. 

 - **standard** Fetches plugins from the official WordPress repository. If a specific version is needed, specify the version using a colon and the version identifier i.e **if-menu:0.2.1**
 - **local** A list of plugins in your local project folder. Plugins are expected to be located in folder projectroot/wp-content/plugins/. Local plugins are symlinked into place in the wp-content folder of the WordPress installation specified by wppath in localsettings.json

### Section: themes
Similar to the plugins section but for themes. 

 - **standard** Fetches themes from the official WordPress repository. If a specific version is needed, specify the version using a colon and the version identifier i.e **footheme:1.1**
 - **local** A list of themes in your local project folder. The themes are expected to be located in folder projectroot/wp-content/themes/. Local themes are symlinked into place in the wp-content folder of the WordPress installation specified by wppath in localsettings.json
 - **active** A string specifying what theme to activate.


### Section: settings

A list of settings that will be applied to the WordPress installation using the wp-cli command "option update %s". Currently only supports simple scalar values (strings and integers)

###Section: wpbootstrap

An associative array of content that can be serialized to disk using the wp-export method and later unserialized back into a WordPress install using the wp-import method.

**posts** contains zero or more keys with an associated array. The key specifies a post_type (page, post etc) and the array contains post_name for each post to include. The export process also includes any media (images) that are attached to the specific post.

**menus** contains zero or more keys with an associated array. The key represents the menu name (as defined in WordPress admin) and the array should contain each *location* that the menu appears in. The location identifiers are unique for each theme.


## Testing

Since wp-bootstrap relies a lot on WordPress, there's a separate Github repository for testing using Vagrant. The test repo is available at [https://github.com/eriktorsner/wp-bootstrap-test](https://github.com/eriktorsner/wp-bootstrap-test).

## Contributing

Contributions are welcome. Apart from code, the project is in need of better documentation, more test cases, testing with popular themes and plugins and so on. Any type of help is appreciated.

