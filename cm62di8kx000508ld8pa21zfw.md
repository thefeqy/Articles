---
title: "Streamlining Laravel Projects with Laravel Model Status"
datePublished: Sat Jan 18 2025 15:58:42 GMT+0000 (Coordinated Universal Time)
cuid: cm62di8kx000508ld8pa21zfw
slug: laravel-model-status
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737215833036/46b8881d-c2f3-43f6-84a3-5f796fa21b46.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1737215908579/8c3605ff-65c3-4a52-beb8-ea1ae146db8c.png
tags: laravel

---

Managing user or model statuses is a common task in Laravel projects. Whether you're building a simple application or a large-scale system, adding and handling status columns often becomes repetitive and time-consuming. That's where **Laravel Model Status** comes in—a lightweight package designed to simplify this process and provide a seamless workflow for status management in Laravel models.

---

## The Problem

In many Laravel projects, there’s often a need to manage the "status" of a model, such as marking users as `active` or `inactive`, or controlling whether certain entities are accessible. Typically, this involves:

* Adding a `status` column to the database schema.
    
* Writing scopes to filter models based on their status.
    
* Adding middleware to restrict access for inactive users.
    
* Repeating this logic across multiple projects.
    

This repetitive process consumes valuable development time and can introduce inconsistencies between projects.

---

## The Solution: Laravel Model Status

**Laravel Model Status** was created to address this problem. The package provides a consistent and configurable way to manage statuses in Laravel models while reducing redundancy and improving code readability.

---

## Key Features

* **Configurable Status Column**: Define the name, default value, and length of the `status` column via a configuration file.
    
* **Global Scope for Active Models**: Automatically filter models to return only active records unless explicitly overridden.
    
* **Middleware for User Activity**: Ensure only active users can access protected routes.
    
* **Activation and Deactivation Methods**: Easily activate or deactivate models using built-in methods.
    
* **Custom Artisan Command**: Create models and migrations with a `status` column and include the necessary trait automatically.
    

---

## Installation

You can install the package via Composer:

```bash
composer require thefeqy/laravel-model-status
```

## Configuration

After installation, publish the configuration file:

```bash
php artisan vendor:publish --tag=config
```

The configuration file allows you to customize the `status` column and its behavior:

```php
return [
    'column_name' => 'status', // Name of the status column
    'default_value' => 'active', // Default value for the active state
    'inactive_value' => 'inactive', // Value for the inactive state
    'column_length' => 10, // Maximum length of the status column
];
```

---

## Usage

Adding the Trait To use the package, include the `HasActiveScope` trait in your model:

```php
use Thefeqy\ModelStatus\Traits\HasActiveScope;

class ExampleModel extends Model
{
    use HasActiveScope;

    protected $fillable = ['name'];
}
```

---

## Real-World Example: Product Management

Imagine you are building an e-commerce platform where products can be `active` or `inactive`, determining their visibility to customers. Let’s use the package to handle this efficiently.

### Create the Model and Migration

Use the `make:model-status` command to create the `Product` model and migration with a `status` column:

```bash
php artisan make:model-status Product
```

#### This command will:

* Create the `Product` model with the `HasActiveScope` trait.
    
* Generate a migration with the configured `status` column.
    

#### Product Model

The generated model (app/Models/Product.php) will look like this:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Thefeqy\ModelStatus\Traits\HasActiveScope;

class Product extends Model
{
    use HasFactory, HasActiveScope;

    protected $fillable = ['name', 'price', 'description'];
}
```

#### Migration

The generated migration will include a status column:

```php
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->text('description')->nullable();
    $table->decimal('price', 10, 2);
    $table->string('status', 10)->default('active'); // Configurable status column
    $table->timestamps();
});
```

Run the migration:

```bash
php artisan migrate
```

### Activating and Deactivating Products

The `HasActiveScope` trait provides methods to `activate` and `deactivate` models dynamically:

#### Activating a Product

```php
$product = Product::find(1);
if ($product->activate()) {
    echo "Product activated successfully!";
} else {
    echo "Failed to activate product.";
}
```

#### Deactivating a Product

```php
$product = Product::withoutActive()->find(1);
if ($product->deactivate()) {
    echo "Product deactivated successfully!";
} else {
    echo "Failed to deactivate product.";
}
```

---

### Querying Products

Retrieve Only Active Products (Default Behavior)

```php
$activeProducts = Product::all(); // Filters to only active products
```

Retrieve All Products (Including Inactive)

```php
$allProducts = Product::withoutActive()->get();
```

---

## Middleware Usage

The `EnsureAuthenticatedUserIsActive` middleware restricts access for inactive users. You can also use it to reactivate suspended accounts by guiding users to contact support.

### Example Route Setup

Instead of registering a string alias, use the middleware class directly:

```php
use Illuminate\Support\Facades\Route;
use Thefeqy\ModelStatus\Middleware\EnsureAuthorizedUserIsActive;

Route::middleware(['auth', EnsureAuthenticatedUserIsActive::class])->group(function () {
    Route::get('/dashboard', function () {
        return 'Welcome to your dashboard!';
    });
});
```

### Behavior

* Inactive users attempting to access routes:
    
    * Are logged out.
        
    * Receive a 403 Forbidden response with the message:
        

```php
This account is suspended. Please contact the administrator.
```

## Admin Panel Example: Managing Products

In the admin panel, administrators can view all products (including inactive ones) and toggle their status.

#### Controller Example:

```php
namespace App\Http\Controllers;

use App\Models\Product;

class AdminProductController extends Controller
{
    public function index()
    {
        $products = Product::withoutActive()->get(); // Get all products
        return view('admin.products.index', compact('products'));
    }

    public function activate(Product $product)
    {
        if ($product->activate()) {
            return redirect()->back()->with('success', 'Product activated successfully!');
        }

        return redirect()->back()->with('error', 'Failed to activate product.');
    }

    public function deactivate(Product $product)
    {
        if ($product->deactivate()) {
            return redirect()->back()->with('success', 'Product deactivated successfully!');
        }

        return redirect()->back()->with('error', 'Failed to deactivate product.');
    }
}
```

---

## GitHub Repository

The package is open-source and available on GitHub. Feel free to explore, contribute, or raise issues: [https://github.com/thefeqy/laravel-model-status](https://github.com/thefeqy/laravel-model-status)

---

## Conclusion

Laravel Model Status streamlines the process of managing model statuses in Laravel projects. Whether you’re working on an e-commerce platform, a user management system, or any other project requiring status handling, this package simplifies your workflow and ensures consistency across your applications.

Try it today and make status management effortless! 🚀