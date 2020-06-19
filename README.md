[![License](https://img.shields.io/packagist/l/payplug/payplug-sylius.svg)](https://github.com/payplug/SyliusPayPlugPlugin/blob/master/LICENSE)
![CI](https://github.com/payplug/SyliusPayPlugPlugin/workflows/CI/badge.svg?branch=master)
[![Version](https://img.shields.io/packagist/v/payplug/payplug-sylius.svg)](https://packagist.org/packages/payplug/payplug-sylius)
[![Total Downloads](https://poser.pugx.org/payplug/payplug-sylius/downloads)](https://packagist.org/packages/payplug/payplug-sylius)

<p align="center">
    <a href="https://sylius.com" target="_blank">
        <img src="https://demo.sylius.com/assets/shop/img/logo.png" />
    </a>
</p>

<h1 align="center">PayPlug payment plugin for Sylius</h1>

<p align="center">This plugin allows you to integrate PayPlug payment with Sylius platform app including payment features and refunding orders.</p>

## Requirements
In the channel settings, the base currency must be set to EUR because the payment gateway only works in this currency. The plugin in the local environment will not work properly because you will not be notified of the status of payments from the payment gateway.

## Installation

1. Add the bundle and dependencies in your composer.json :

    With **Symfony Flex** :

        composer config extra.symfony.allow-contrib true
        composer require payplug/payplug-sylius

    Yon can now skip the next three steps.

    Or **manually** :

        composer require payplug/payplug-sylius

2. Enable the plugin in your `config/bundles.php` file by add

    ```php
    PayPlug\SyliusPayPlugPlugin\PayPlugSyliusPayPlugPlugin::class => ['all' => true],
    ```

3. Import required config in your `config/packages/sylius_payplug.yaml` file:

    ```yaml
    imports:
        - { resource: "@PayPlugSyliusPayPlugPlugin/Resources/config/config.yml" }
        - { resource: "@PayPlugSyliusPayPlugPlugin/Resources/config/services.xml" }
    ```

4. Copy Sylius templates overridden in plugin to your templates directory (e.g templates/bundles/)

   ```shell
    mkdir -p templates/bundles/SyliusAdminBundle/
    cp -R vendor/payplug/sylius-payplug-plugin/src/Resources/views/SyliusAdminBundle/* templates/bundles/SyliusAdminBundle/
    ```

5. Import custom form row theme in your `config/packages/twig.yaml` file:
    ```yaml
   twig:
       ...
       form_themes: [
           'form/form_gateway_config_row.html.twig'
       ]
    ```

6. Copy custom form row theme template

    ```shell
    mkdir -p templates/form/
    cp -R vendor/payplug/sylius-payplug-plugin/src/Resources/views/form/* templates/form/
    ```

7. Clear cache:

    ```bash
   bin/console cache:clear
    ```

## Cronjob
In the case when the IPN is blocked, you can set cron job every minute that updates the payment status.

For example:

```bash
* * * * * bin/console payplug:update-payment-state
```

## Logs

If you want to follow the logs in the production environment, you need to add the configuration in `config/packages/prod/monolog.yaml`, logs should be in `var/log/prod.log` which can be searched after the phrase `[Payum]` or `[PayPlug]`:

 ```yaml
   monolog:
       handlers:
          ...
          
          payum:
              level: debug
              type: stream
              path: "%kernel.logs_dir%/%kernel.environment%.log"
```

## IPN testing on the local machine

In the configuration of the payment gateway in the admin panel, set your url (eg from [ngrok](https://ngrok.com/)) to notifications in the field `Notification url for environment dev`. This url will only work in the dev environment.
 
## Customization

### Available services you can [decorate](https://symfony.com/doc/current/service_container/service_decoration.html) and forms you can [extend](http://symfony.com/doc/current/form/create_form_type_extension.html)

Run the below command to see what Symfony services are shared with this plugin:
 
```bash
$ bin/console debug:container payplug_sylius_payplug_plugin
```

## Testing

```bash
$ composer install
$ cd tests/Application
$ yarn install
$ yarn build
$ bin/console assets:install public -e test
$ bin/console doctrine:database:create -e test
$ bin/console doctrine:schema:create -e test
$ bin/console server:run 127.0.0.1:8080 -d public -e test
$ open http://localhost:8080
$ bin/behat
$ bin/phpspec run
```

## License

This library is under the MIT license.
