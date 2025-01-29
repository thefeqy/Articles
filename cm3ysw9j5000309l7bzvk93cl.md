---
title: "Observers in Laravel: Simplifying Model Event Handling With Real-World Examples"
datePublished: Tue Nov 26 2024 18:39:01 GMT+0000 (Coordinated Universal Time)
cuid: cm3ysw9j5000309l7bzvk93cl
slug: observers-in-laravel-simplifying-model-event-handling-with-real-world-examples
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1732646246941/39156196-f5c6-4978-9055-8f53eecb5dd6.png
tags: design-patterns, laravel, php

---

In Laravel, **Observers** are classes used to handle model events cleanly and efficiently. Events like `creating`, `created`, `updating`, `updated`, `deleting`, and `deleted` occur when a model undergoes specific changes. Observers allow us to separate this logic from controllers or models, keeping the codebase clean and modular.

---

### **Why Use Observers?**

1. **Separation of Concerns**: Keeps event-handling logic out of models.
    
2. **Reusability**: The same observer can handle events for multiple models.
    
3. **Cleaner Code**: Reduces clutter in the model or controller.
    

### **Real-World Examples of the Observer Pattern**

The Observer pattern is commonly used when you want certain parts of your system to react to changes in an object automatically. Here are some real-world examples where the pattern fits naturally:

### **1\. Sending Emails or Notifications**

* **Scenario**: When a user registers on a website, you might want to send a welcome email or notify admins.
    
* **Observer's Role**: Automatically trigger email or notification logic when the `User` model's `created` event is fired.
    

### **2\. Inventory Management in E-commerce**

* **Scenario**: When an order is placed, the stock levels of items in the order should be reduced.
    
* **Observer's Role**: Listen to the `created` event of an `Order` model to automatically adjust product inventory.
    

### **3\. Automated Billing**

* **Scenario**: If a subscription is updated or canceled, the billing system should react accordingly.
    
* **Observer's Role**: Listen for `updated` or `deleted` events on the `Subscription` model to handle billing updates.
    

### **Setting Up Observers in Laravel**

Here’s how to implement and use observers step-by-step:

### **1\. Create an Observer Class**

Use the Artisan command to generate an observer:

```bash
php artisan make:observer PostObserver --model=Post
```

This creates an `PostObserver` class in the `App\Observers` directory. The `—model=Post` flag associates the observer with the `Post` model.

### **2\. Define Event Methods in the Observer**

Edit the `PostObserver` class to handle desired events:

```php
namespace App\Observers;

use Illuminate\Support\Str;
use App\Models\Post;

class PostObserver
{
    /**
     * Handle the Post "saving" event.
     */
    public function saving(Post $post)
    {
        if (!$post->slug) {
            $post->slug = Str::slug($post->title); // Generate slug from title
        }
    }
}
```

### **3\. Register the Observer**

Register the observer in the `boot` method of the `App\Providers\AppServiceProvider` class:

```php
use App\Models\Post;
use App\Observers\PostObserver;

public function boot()
{
    Post::observe(PostObserver::class);
}
```

### **4\. Test the Observer**

Create or update a blog post to see the slug generation in action:

```php
use App\Models\Post;

// Create a post without a slug
$post = Post::create([
    'title' => 'How to Use Laravel Observers',
    'content' => 'Observers are a great way to handle events.',
]);

// Check the generated slug
dd($post->slug); // Output: "how-to-use-laravel-observers"
```

### **5\. Check Database**

When you inspect the database, you’ll see that the `slug` column is populated automatically:

| **ID** | **Title** | **Slug** |
| --- | --- | --- |
| 1 | How to Use Laravel Observers | how-to-use-laravel-observers |

### **Observer Event Methods**

Here’s a quick reference for available methods in an observer:

* `creating` / `created`: Before/after a model is created.
    
* `updating` / `updated`: Before/after a model is updated.
    
* `saving` / `saved`: Before/after a model is created or updated.
    
* `deleting` / `deleted`: Before/after a model is deleted.
    
* `restoring` / `restored`: Before/after a soft-deleted model is restored.
    

### **Conclusion**

The Observer pattern is a powerful tool in Laravel for handling model events. By implementing it, you can simplify repetitive tasks like sending notifications, maintaining logs, or updating related data. Observers improve code clarity, follow best practices like **Separation of Concerns**, and make applications easier to maintain.