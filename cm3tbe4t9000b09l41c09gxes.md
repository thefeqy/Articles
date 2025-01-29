---
title: "PHP 8.4 Is Here !"
seoTitle: "PHP 8.4 Features Explained: What's New in the Latest PHP Release."
seoDescription: "Discover the exciting new features of PHP 8.4, including property hooks, asymmetric visibility, multibyte trim functions, and more. Learn with detailed desc"
datePublished: Fri Nov 22 2024 22:30:11 GMT+0000 (Coordinated Universal Time)
cuid: cm3tbe4t9000b09l41c09gxes
slug: php-84-is-here
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1732314448180/ff461c12-b98b-48c8-9997-df6c04ef6d6f.png
tags: php, php8

---

PHP continues to evolve as a versatile and developer-friendly language, and its latest release, PHP 8.4, is no exception. Packed with enhancements designed to simplify syntax, improve performance, and make code more expressive, this version further strengthens PHP's position as a leading choice for web development. In this article, we’ll delve into the most notable new features of PHP 8.4, complete with descriptions and practical examples to help you leverage them effectively.

---

### **1\. Property Hooks**

Property Hooks allow developers to define custom behavior when accessing or modifying class properties, reducing the need for boilerplate getter and setter methods.

*Example:*

```php
class User {
    private bool $isModified = false;

    public function __construct(private string $first, private string $last) {}

    public string $fullName {
        get => $this->first . ' ' . $this->last;
        set {
            [$this->first, $this->last] = explode(' ', $value, 2);
            $this->isModified = true;
        }
    }
}

$user = new User('John', 'Doe');
echo $user->fullName; // Outputs: John Doe
$user->fullName = 'Jane Smith';
echo $user->fullName; // Outputs: Jane Smith
```

In this example, accessing `$user->fullName` triggers the `get` hook, while assigning a value to it triggers the `set` hook, allowing for custom logic during these operations.

### **2\. Asymmetric Visibility**

Asymmetric Visibility enables different visibility levels for reading and writing class properties, offering more granular control over property access.

*Example:*

```php
class Product {
    public private(set) float $price;

    public function __construct(float $price) {
        $this->price = $price;
    }
}

$product = new Product(99.99);
echo $product->price; // Outputs: 99.99
$product->price = 79.99; // Error: Cannot modify readonly property
```

Here, the `price` property is publicly readable but can only be modified within the class, preventing external modification.

### **3\. Chaining** `new` Without Parentheses

PHP 8.4 allows method chaining directly on new class instances without requiring parentheses around the instantiation, simplifying syntax.

*Example:*

```php
class Request {
    public function withMethod(string $method): static {
        // Set the method
        return $this;
    }

    public function withUri(string $uri): static {
        // Set the URI
        return $this;
    }
}

$request = new Request()->withMethod('GET')->withUri('/home');
```

This feature enhances code readability by eliminating unnecessary parentheses during method chaining.

### **4\. New Array Find Functions**

PHP 8.4 introduces several array functions to streamline common operations:

* `array_find()`: Returns the first element that satisfies a given condition.
    
* `array_find_key()`: Returns the key of the first element that satisfies a given condition.
    
* `array_any()`: Checks if any array element satisfies a given condition.
    
* `array_all()`: Checks if all array elements satisfy a given condition.
    

*Example:*

```php
$array = [1, 2, 3, 4, 5];

$firstEven = array_find($array, fn($value) => $value % 2 === 0);
echo $firstEven; // Outputs: 2

$hasNegative = array_any($array, fn($value) => $value < 0);
var_dump($hasNegative); // Outputs: bool(false)
```

These functions provide concise and readable ways to perform common array operations.

### **5\. HTML5 Parsing Support**

PHP 8.4 enhances the DOM extension with support for HTML5, introducing new classes in the `Dom` namespace that are compliant with the WHATWG specification.

*Example:*

```php
use Dom\HTMLDocument;

$html = '<!DOCTYPE html><html><body><p>Hello, World!</p></body></html>';
$doc = HTMLDocument::createFromString($html);
echo $doc->body->textContent; // Outputs: Hello, World!
```

This update allows for more accurate parsing and manipulation of HTML5 documents.

### **6\. Multibyte Trim Functions**

New multibyte string functions have been added to handle trimming operations on strings with multibyte characters:

* `mb_trim()`: Trims whitespace from both ends of a string.
    
* `mb_ltrim()`: Trims whitespace from the beginning of a string.
    
* `mb_rtrim()`: Trims whitespace from the end of a string.
    

*Example:*

```php
$str = " こんにちは ";
$trimmed = mb_trim($str);
echo $trimmed; // Outputs: こんにちは
```

These functions ensure proper handling of multibyte characters during trimming operations.

### **7\. Just-In-Time (JIT) Compiler Enhancements**

PHP 8.4 includes improvements to the JIT compiler, resulting in better performance and reduced memory usage in certain scenarios.

These enhancements aim to optimize the execution of PHP scripts, particularly those that are CPU-intensive.

### **8\. Deprecation of Implicit Nullable Types**

PHP 8.4 deprecates the implicit conversion of typed parameters with default `null` values to nullable types. Developers are now required to explicitly declare nullable types.

*Example:*

```php
// Deprecated in PHP 8.4
function processData(string $data = null) {
    // ...
}

// Recommended in PHP 8.4
function processData(?string $data = null) {
    // ...
}
```

This change promotes clearer type declarations and reduces potential ambiguities in code.

---

## **Conclusion**

PHP 8.4 marks another milestone in the journey of the PHP language, bringing a host of features that enhance developer experience, boost productivity, and ensure better performance. From property hooks and asymmetric visibility to the introduction of multibyte trim functions and HTML5 parsing support, this update demonstrates PHP’s commitment to innovation and practicality. Whether you’re a seasoned developer or just getting started, these features empower you to write cleaner, more efficient, and modern PHP code.

As you explore PHP 8.4, take the time to incorporate these new tools into your projects. They not only simplify everyday tasks but also make your applications more robust and maintainable. So, update your environments, experiment with the features, and let PHP 8.4 elevate your coding experience to new heights! Happy coding!