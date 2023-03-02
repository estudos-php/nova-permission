# A Laravel Nova tool for Spatie's laravel-permission library

[![Latest Version on Packagist](https://img.shields.io/packagist/v/codehero-mx/nova-permission-tool.svg?style=flat-square)](https://packagist.org/packages/codehero-mx/nova-permission-tool)
[![Total Downloads](https://img.shields.io/packagist/dt/CodeHeroMX/nova-permission-tool.svg?style=flat-square)](https://packagist.org/packages/CodeHeroMX/nova-permission-tool)

![screenshot 1](https://raw.githubusercontent.com/CodeHeroMX/nova-permission-tool/main/docs/user-resource.png)

## Installation

You can install the package in to a Laravel app that uses [Nova](https://nova.laravel.com) via composer:

```bash
composer require codehero-mx/nova-permission-tool
```

Go through the [Installation](https://github.com/spatie/laravel-permission#installation) section in order to setup [laravel-permission](https://packagist.org/packages/spatie/laravel-permission).

Next up, you must register the tool with Nova. This is typically done in the `tools` method of the `NovaServiceProvider`.

```php
// in app/Providers/NovaServiceProvider.php

// ...

public function tools()
{
    return [
        // ...
        \CodeHeroMX\NovaPermission\NovaPermissionTool::make(),
    ];
}
```

Next, add middleware to `config/nova.php`

```php
// in config/nova.php
'middleware' => [
    // ...
    \CodeHeroMX\NovaPermission\ForgetCachedPermissions::class,
],
```

Finally, add `MorphToMany` fields to you `app/Nova/User` resource:

```php
// ...
use Laravel\Nova\Fields\MorphToMany;

public function fields(Request $request)
{
    return [
        // ...
        MorphToMany::make('Roles', 'roles', \CodeHeroMX\NovaPermission\Role::class),
        MorphToMany::make('Permissions', 'permissions', \CodeHeroMX\NovaPermission\Permission::class),
    ];
}
```

Or if you want to attach multiple permissions at once, use `RoleBooleanGroup` and `PermissionBooleanGroup` fields (requires at least Nova 2.6.0):

```php
// ...
use CodeHeroMX\NovaPermission\PermissionBooleanGroup;
use CodeHeroMX\NovaPermission\RoleBooleanGroup;

public function fields(Request $request)
{
    return [
        // ...
        RoleBooleanGroup::make('Roles'),
        PermissionBooleanGroup::make('Permissions'),
    ];
}
```

If your `User` could have a single role at any given time, you can use `RoleSelect` field. This field will render a standard select where you can pick a single role from.

```php
// ...
use CodeHeroMX\NovaPermission\PermissionBooleanGroup;
use CodeHeroMX\NovaPermission\RoleSelect;

public function fields(Request $request)
{
    return [
        // ...
        RoleSelect::make('Role', 'roles'),
    ];
}
```

## Configuration

You can optionally publish the config file with:

```bash
php artisan vendor:publish --provider="CodeHeroMX\NovaPermission\ToolServiceProvider" --tag="config"
```

## Customization

If you want to use custom resource classes you can define them when you register a tool:

```php
// in app/Providers/NovaServiceProvider.php

// ...

public function tools()
{
    return [
        // ...
        \CodeHeroMX\NovaPermission\NovaPermissionTool::make()
            ->roleResource(CustomRole::class)
            ->permissionResource(CustomPermission::class),
    ];
}

```

If you want to show your roles and policies with a custom label, you can set `$labelAttribute` when instantiating your fields:

```php
// ...
use CodeHeroMX\NovaPermission\PermissionBooleanGroup;
use CodeHeroMX\NovaPermission\RoleSelect;

public function fields(Request $request)
{
    return [
        // ...
        RoleBooleanGroup::make('Roles', 'roles', null, 'description'),
        PermissionBooleanGroup::make('Permissions', 'permissions', null, 'description'),
        RoleSelect::make('Role', 'roles', null, 'description'),
    ];
}
```

## Define Policies 

```php
// in app/Providers/NovaServiceProvider.php

// ...

public function tools()
{
    return [
        // ...
        \CodeHeroMX\NovaPermission\NovaPermissionTool::make()
            ->rolePolicy(RolePolicy::class)
            ->permissionPolicy(PermissionPolicy::class),
    ];
}

```

## Usage

A new menu item called "Permissions & Roles" will appear in your Nova app after installing this package.
