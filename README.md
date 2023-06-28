# Guide for adding Gravity Forms as a Composer dependency

Hey there! This guide is here to assist you in seamlessly integrating Gravity Forms WordPress Plugin as a composer dependency.

## Before you dive in

Just a heads up, this guide is specifically designed to aid you in adding Gravity Forms version `2.7.9`, which happens to be the latest release as on 2023 June 27th. Before we get started, it's always a good idea to double-check the Gravity Forms changelog to ensure you're up to date and make any necessary version adjustments.

## Let's get this party started

### 1) Add the Gravity Forms key into the `.env`

`WP_PLUGIN_GF_KEY` needs to be defined on `.env` file, on the project root directory. A line containing `WP_PLUGIN_GF_KEY=<YOUR-GF-LICENSE-KEY>` would be enough for that.

To get your Gravity Forms license key you may [login to Gravity Forms](https://www.gravityforms.com/wp-login.php), and check/manage your license. 

This piece of code below will append your Gravity Forms key to your `.env` file. If the file doesn't exist, it will create one for you in the root directory of your project.
Remember changing `<YOUR-GF-LICENSE-KEY>` to your Gravity Forms license key.

```bash
# Append the Gravity Forms key to your `.env` file.
echo "WP_PLUGIN_GF_KEY=<YOUR-GF-LICENSE-KEY>" >> .env
```

### 2) Registering the Gravity Forms repository

The following block should be added to your `composer.json` to registering the Gravity Forms repository

```json
{
  "type": "package",
  "package": {
    "name": "gravityforms/gravityforms",
    "version": "2.7.9",
    "type": "wordpress-plugin",
    "dist": {
      "type": "zip",
      "url": "https://www.gravityhelp.com/wp-content/plugins/gravitymanager/api.php?op=get_plugin&slug=gravityforms&key={%WP_PLUGIN_GF_KEY}"
    },
    "require": {
      "composer/installers": "^1.4",
      "gotoandplay/gravityforms-composer-installer": "^2.3"
    }
  }
}
```

The following `jq` command *attempts* to accomplish this for you by checking if `repositories` is an object with the type function. If it is, it adds a new repository object as a new field in the repositories. In case `repositories` is not an object (possibly an array), it appends the new repository as an item in the repositories array.

If `jq` is not installed on your system, you can easily add it on a Debian-based GNU/Linux by using `sudo apt-get install jq` or on macOS with `brew install jq`.

```bash
# Add Gravity Forms repository
jq '
  if .repositories|type=="object" then 
    .repositories.gravityforms = {
      "type": "package",
      "package": {
        "name": "gravityforms/gravityforms",
        "version": "2.7.9",
        "type": "wordpress-plugin",
        "dist": {
          "type": "zip",
          "url": "https://www.gravityhelp.com/wp-content/plugins/gravitymanager/api.php?op=get_plugin&slug=gravityforms&key={%WP_PLUGIN_GF_KEY}"
        },
        "require": {
          "composer/installers": "^1.4",
          "gotoandplay/gravityforms-composer-installer": "^2.3"
        }
      }
    } 
  else 
    .repositories += [{
      "type": "package",
      "package": {
        "name": "gravityforms/gravityforms",
        "version": "2.7.9",
        "type": "wordpress-plugin",
        "dist": {
          "type": "zip",
          "url": "https://www.gravityhelp.com/wp-content/plugins/gravitymanager/api.php?op=get_plugin&slug=gravityforms&key={%WP_PLUGIN_GF_KEY}"
        },
        "require": {
          "composer/installers": "^1.4",
          "gotoandplay/gravityforms-composer-installer": "^2.3"
        }
      }
    }] 
  end
' composer.json > tmp.$$.json && mv tmp.$$.json composer.json
```

### 3) Permitting necessary plugins

You'll need to add the `ffraenz/private-composer-installer` and `gotoandplay/gravityforms-composer-installer` plugins to the list of permitted plugins. The commands below will try to take care of this for you:

```bash
# Add config section if it does not exist
jq 'if .config then . else . + { "config": {} } end' composer.json > tmp.$$.json && mv tmp.$$.json composer.json

# Add config.allow-plugins section if it does not exist
jq 'if .config["allow-plugins"] then . else .config += { "allow-plugins": {} } end' composer.json > tmp.$$.json && mv tmp.$$.json composer.json

# Add plugins to the allow-plugins list
jq '.config["allow-plugins"] += {"ffraenz/private-composer-installer": true, "gotoandplay/gravityforms-composer-installer": true}' composer.json > tmp.$$.json && mv tmp.$$.json composer.json
```

### 4) Include Gravity Forms

Use the following command to include Gravity Forms in your project:

```bash
# Include Gravity Forms
composer require gravityforms/gravityforms 2.7.9
```

### 5) Add the `.env` file to `.deployignore`

This ensures that your environment variables file doesn't get included in your deployments, protecting your sensitive data.

```bash
# Add the `env file to .deployignore
if [ ! -f .deployignore ] || ! grep -Fxq ".env" .deployignore; then
    echo -e "# environment variables file\n.env" >> .deployignore
fi
```

## Enjoy the rewards of your efforts
I hope it gives you more time for your beloved pursuits, like backing an incredible environmental volunteer project =0)
[![Buy a fruit tree sapling](https://img.shields.io/badge/Buy%20a%20fruit%20tree%20sapling-support-%23FFDD00.svg?style=flat-square&logo=buy-me-a-coffee&logoColor=white)](https://www.buymeacoffee.com/frutourbano)

Did you get very inspired by the idea of volunteers establishing community orchards for feeding, focusing on preserving endangered fruit tree species and empowering underserved communities?
Kindly consider supporting the Urban Fruit (Fruto Urbano) initiative on Catarse! [![Support on Catarse](https://img.shields.io/badge/Support%20on-Catarse-%23ED6D3D.svg?style=flat-square&logo=catarse&logoColor=white)](https://www.catarse.me/frutourbano)

## Plus: Automation - Why not give it a whirl?
Behold! I've ventured into the realm of time-saving wizardry and emerged with the command lines below. Consider it an experiment.
Just change `<YOUR-GF-LICENSE-KEY>` to your Gravity Forms license key, and copy/paste it on your command line.

```bash
# 1) Append the Gravity Forms key to your `.env` file.
echo -e "WP_PLUGIN_GF_KEY=<YOUR-GF-LICENSE-KEY>" >> .env

# 2) Add Gravity Forms repository
jq '
  if .repositories|type=="object" then 
    .repositories.gravityforms = {
      "type": "package",
      "package": {
        "name": "gravityforms/gravityforms",
        "version": "2.7.9",
        "type": "wordpress-plugin",
        "dist": {
          "type": "zip",
          "url": "https://www.gravityhelp.com/wp-content/plugins/gravitymanager/api.php?op=get_plugin&slug=gravityforms&key={%WP_PLUGIN_GF_KEY}"
        },
        "require": {
          "composer/installers": "^1.4",
          "gotoandplay/gravityforms-composer-installer": "^2.3"
        }
      }
    } 
  else 
    .repositories += [{
      "type": "package",
      "package": {
        "name": "gravityforms/gravityforms",
        "version": "2.7.9",
        "type": "wordpress-plugin",
        "dist": {
          "type": "zip",
          "url": "https://www.gravityhelp.com/wp-content/plugins/gravitymanager/api.php?op=get_plugin&slug=gravityforms&key={%WP_PLUGIN_GF_KEY}"
        },
        "require": {
          "composer/installers": "^1.4",
          "gotoandplay/gravityforms-composer-installer": "^2.3"
        }
      }
    }] 
  end
' composer.json > tmp.$$.json && mv tmp.$$.json composer.json

# 3) Permitting necessary plugins

# Add config section if it does not exist
jq 'if .config then . else . + { "config": {} } end' composer.json > tmp.$$.json && mv tmp.$$.json composer.json

# Add config.allow-plugins section if it does not exist
jq 'if .config["allow-plugins"] then . else .config += { "allow-plugins": {} } end' composer.json > tmp.$$.json && mv tmp.$$.json composer.json

# Add plugins to the allow-plugins list
jq '.config["allow-plugins"] += {"ffraenz/private-composer-installer": true, "gotoandplay/gravityforms-composer-installer": true}' composer.json > tmp.$$.json && mv tmp.$$.json composer.json

# 4) Include Gravity Forms - adding --with-all-dependencies as some dependency upgrade/downgrade may be necessary
composer require gravityforms/gravityforms 2.7.9 --with-all-dependencies

# 5) Add the `env file to .deployignore
if [ ! -f .deployignore ] || ! grep -Fxq ".env" .deployignore; then
    echo -e "# environment variables file\n.env" >> .deployignore
fi
```
