---
title: "Use laravel's eloquent traits to avoid code duplication"
seoTitle: "Laravel Eloquent Traits"
seoDescription: "Consider that you have a particular event that you need to fire in several places, such as deleting the model’s related image after the deletion of the mode"
datePublished: Tue Apr 05 2022 06:11:05 GMT+0000 (Coordinated Universal Time)
cuid: cl1lqtiiw00uh4pnvep583mv9
slug: use-laravels-eloquent-traits-to-avoid-code-duplication
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1649224875645/8Q3Z9rZbS.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1649379352373/ChuFKpRcT.png
tags: laravel, php

---

# Introduction
Consider that you have a particular event that you need to fire in several places, such as deleting the model’s related image after the deletion of the model itself.
you may use the same code for posts, products, categories, ..etc.

```php
public function destroy(Post $post) {
    // Delete all post images
    $post->images()->each(function ($image) {
        Storage::delete($image->path);
        $image->delete();
    });
    $post->delete();
    // ... Some logic
}
```

it would be a headache to take care of this each time you need to delete a model.
so, how we can avoid this?

***

# Use Eloquent events
laravel eloquent ships with several events, it allows you to do a particular task at the exact time you need.

> in our example (delete the model’s related images when the model is deleted).

you can read more about [Eloquent events](https://laravel.com/docs/8.x/eloquent#events) In the laravel official Docs
to reach that, you can use the boot static method in your model :

```php
<?PHP

use Illuminate\Support\Facades\Storage;

class Post extends Model
{
    protected static function boot() {
        parent::boot();
        static::deleted(function ($post) {
            $post->images()->each(function ($image) {
                Storage::delete($image->path);
                $image->delete();
            });
        });
    }
    // .. Some logic
}
```

now, you don’t have to do anything while you’re deleting a post (Model). laravel will take care of that by firing an event to delete the images each time you try to delete a post.

Ok!, you’re doing great so far, you are just made your code much cleaner by delegating the image deletion logic into the model itself.

but we still have that code duplication problem right?. every time you want to add the same event (deleting the model’s image), you have to copy the same code in that particular model.

and as you already know, this is bad in software development, you’ll have to edit each model once you have an improvement to this piece of code.

so you should make your code DRY (do not repeat yourself) as much as possible.

here, the Bootable Traits are having our backs.

# Bootable Traits

you can create a trait that has the same logic and use it wherever you need it.
Just create a static method with a name boot{TraitName} (replace {TraitName} with your actual trait name). it will be executed as a boot() method on your Eloquent model.
here’s an example:

```php
<?php

use Illuminate\Support\Facades\Storage;


trait DeleteModelImages {
    /**
     * the bootable trait should have static
     * method with name boot{TraitName}
     * 
     * @example bootDeleteModelImages()
     */
    protected static function bootDeleteModelImages() {
        static::deleted(function ($model) {
            $model->images()->each(function ($image) {
                Storage::delete($image->path);
                $image->delete();
            });
        });
    }
}

class Post extends Model
{
    use DeleteModelImages;
    
    // .. Some logic
}
```

As you can see, we encapsulate the logic of deleting the image in a single **bootable trait**, and now it’s a reusable component. just use the same trait `DeleteModelImages` with any model you need.

now you can delete any post without worrying about deleting its images, you can leave that for laravel to handle.

***

> You can also follow me on [Twitter](https://twitter.com/thefeqy) for more tips.