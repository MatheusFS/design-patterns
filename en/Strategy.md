# 1. Design Patterns: Strategy
### Summary
1. Design Patterns: Strategy
    - 1.1. [Problem](#11-problem)
    - 1.2. [What you should NOT do](#12-what-you-should-not-do)
        - 1.2.1. [Working Tree](#121-working-tree)
        - 1.2.2. [Animal.php](#122-animal-php)
        - 1.2.3. [Dog.php](#123-dog-php)
        - 1.2.4. [Bird.php](#124-bird-php)
    - 1.3. [How you should consider doing](#13-how-you-should-consider-doing)
        - 1.3.1. [Working Tree](#131-working-tree)
        - 1.3.2. [Flies.php](#132-flies-php)
        - 1.3.3. [ItFlies.php](#133-itflies-php)
        - 1.3.4. [CantFly.php](#134-cantfly-php)
        - 1.3.5. [Animal.php](#135-animal-php)
        - 1.3.6. [Dog.php](#136-dog-php)
        - 1.3.7. [Bird.php](#137-bird-php)
        - 1.3.8. [runtime.php](#138-runtime-php)

# 1.1. Problem
Animal::class
 - Dog::class
 - Bird::class

Give the animal capability to fly

# 1.2. What you should NOT do
Insert method in Animal::class is bad code, because it is not general to every subclass of animals (a bird can fly, but not a dog).
## 1.2.1. Working Tree
```
╔ 📁 project (root)
╚══╦ 📁 Models
───╠═══ 📄 Animal.php
───╚══╦ 📁 Animal
──────╠═══ 📄 Dog.php
──────╚═══ 📄 Bird.php
```
## 1.2.2. Animal.php (*project\Models*)
```php
class Animal{
    ...
    public function fly(){...}
    ...
}
```
## 1.2.3. Dog.php (*project\Models\Animal*)
```php
class Dog extends Animal{
    ...
    public function fly(){ return 'Cant fly'; }
    ...
}
```
## 1.2.4. Bird.php (*project\Models\Animal*)
```php
class Bird extends Animal{
    ...
    public function fly(){ return 'Flying'; }
    ...
}
```
# 1.3. How you should consider doing
## 1.3.1. Working Tree
```
╔ 📁 project (root)
╠══╦ 📁 Strategies
║──╠═══ 📄 Flies.php
║──╚══╦ 📁 Flies
║─────╠═══ 📄 ItFlies.php
║─────╚═══ 📄 CantFly.php
╚══╦ 📁 Models
───╠═══ 📄 Animal.php
───╚══╦ 📁 Animal
──────╠═══ 📄 Dog.php
──────╚═══ 📄 Bird.php
```
## 1.3.2. Flies.php (*project\Strategies*)
```php
interface Flies{
    ...
    public function fly();
    ...
}
```
## 1.3.3. ItFlies.php (*project\Strategies\Flies*)
```php
class ItFlies implements Flies{
    ...
    public function fly(){ return 'Flying'; }
    ...
}
```
## 1.3.4. CantFly.php (*project\Strategies\Flies*)
```php
class CantFly implements Flies{
    ...
    public function fly(){ return 'Cant fly'; }
    ...
}
```
**Decoupling** - encapsulating the *concept* or the *behavior* that varies, in this case, the *capability to fly*.

**Composition** - instead of heritage the class is composed with objects with the right ability built in.

`Behavior can change without side effects`

## 1.3.5. Animal.php (*project\Models*)
```php
use Project\Strategies\Flies;
...
class Animal{

    protected $flying_type;
    ...
    public function set_flying_type(Flies $flying_type){

        $this->flying_type = $flying_type;
    }

    public function try_to_fly(){

        return $flying_type->fly();
    }
    ...
}
```
## 1.3.6. Dog.php (*project\Models\Animal*)
```php
use Project\Strategies\Flies\CantFly;
...
class Dog extends Animal{
    ...
    public function __construct(){
        
        // Set behavior class that the
        // object should refer to
        $this->set_flying_type(new CantFly);
    }
    ...
}
```
## 1.3.7. Bird.php (*project\Models\Animal*)
```php
use Project\Strategies\Flies\ItFlies;
...
class Bird extends Animal{
    ...
    public function __construct(){
        
        // Behavior the object should refer to
        $this->set_flying_type(new ItFlies);
    }
    ...
}
```
## 1.3.8. runtime.php
```php
$doggy = new Dog;
$birdy = new Bird;

$doggy->try_to_fly(); // 'Cant fly'
$birdy->try_to_fly(); // 'Flying'

$doggy->grow_wings(); // set_flying_type(new ItFlies)

$doggy->try_to_fly(); // 'Flying'
```
