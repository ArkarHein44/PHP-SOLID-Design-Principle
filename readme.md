> [!IMPORTANT]
> Solid design principle ကိုလေ့လာရန် OOP Conceptများအား နားလည်သဘောပေါက်ထားရန်လိုအပ်ပါသည်

# SOLID Design Principle in PHP
- [S - Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
- [O - Open Closed Principle (OCP)](#open-closed-principle-ocp)
- [L - Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
- [I - Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
- [D - Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)

## Single Responsibility Principle (SRP)
> A class should have one, and only one, reason to change. This means a class should only have one responsibility or functionality. 
> class တစ်ခုဟာ functionality တစ်ခု (သို့) responsibility တစ်ခုကိုသာ လုပ်ဆောင်သင့်ပါတယ်။ method ၁ခုတည်းရှိရမယ်လို့မဆိုလိုပါ။
```php
// Violating SRP
class UserManager {
    public function addUser($user) {
        // Logic to add user
    }

    public function deleteUser($user) {
        // Logic to delete user
    }

    public function sendEmail($user, $message) {
        // Logic to send email
    }
}

// Applying SRP
class UserManager {
    public function addUser($user) {
        // Logic to add user
    }

    public function deleteUser($user) {
        // Logic to delete user
    }
}

class EmailService {
    public function sendEmail($user, $message) {
        // Logic to send email
    }
}
```

## Open Closed Principle (OCP)
> Software entities (classes, modules, functions) should be open for extension but closed for modification. You should be able to extend a class's behavior without modifying its source code.
> Software entities တွေဖြစ်တဲ့ classတွေ၊ muduleတွေနဲ့ functionတွေဟာ extension ပြုလုပ်ဖို့ဖွင့်ထားသင့်ပြီး modificationပြုလုပ်ဖို့အတွက်တော့ ပိတ်ထားသင့်ပါတယ်။ class တစ်ခုကို ခဏခဏ modification ပြုလုပ်နေတာမျိုးဟာမဖြစ်သင့်ပါဘူး။ ဒါကြောင့် main source code ကို interface အနေနဲ့ဖန်တီးကာ အခြား class များမှ implements ပြုလုပ်ကာအသုံးပြုရန်ဖွင့်ထားရမှာဖြစ်ပါတယ်။ process လုပ်တဲ့အခါမှာတော့ Class Type hinting ဖြင့်ပိတ်ထားလိုက်ခြင်းဖြင့် OCP ကို followလုပ်ရမှာပါ။ SRP ကိုလည်း follow လုပ်နေတာကိုလည်းတွေ့ရမှာပါ။
```php
// Violating OCP
class PaymentProcessor {
    public function process($paymentType) {
        if ($paymentType === 'creditCard') {
            // Process credit card payment
        } elseif ($paymentType === 'paypal') {
            // Process PayPal payment
        }
    }
}

// Applying OCP
interface PaymentMethod {
    public function pay();
}

class CreditCardPayment implements PaymentMethod {
    public function pay() {
        // Process credit card payment
    }
}

class PayPalPayment implements PaymentMethod {
    public function pay() {
        // Process PayPal payment
    }
}

class PaymentProcessor {
    public function process(PaymentMethod $paymentMethod) {
        $paymentMethod->pay();
    }
}
```

## Liskov Substitution Principle (LSP)
> Objects of a superclass should be replaceable with objects of a subclass without altering the correctness of the program. This ensures that derived classes extend the functionality of the base class without changing its behavior.
> Superclass (သို့) base class ရဲ့ object တစ်ခုကို subclass ရဲ့ object နဲ့ အစားထိုးသုံးလို့ရပြီး၊ program ရဲ့ လုပ်ဆောင်ချက်တွေ မထိခိုက်စေရပါဘူး။
```php
// Violating LSP
class Bird {
    public function fly() {
        return "Flying";
    }
}

class Penguin extends Bird {
    public function fly() {
        throw new Exception("Can't fly");
    }
}

function makeBirdFly(Bird $bird) {
    echo $bird->fly();
}

$penguin = new Penguin();
makeBirdFly($penguin); // Throws an exception: "Can't fly"
```
> အပေါ်ကကုတ်မှာဆိုရင် Penguin သည် LSP ကို ချိုးဖောက်ပါတယ်။ ဘာကြောင့်လဲဆိုတော့ fly() method ကို ခေါ်တဲ့အခါ exception ပစ်လိုက်လို့ပါ။ ဒါကြောင့် base class (Bird) ရဲ့ contract ကို ဖျက်လိုက်သလို ဖြစ်သွားပါတယ်။
```php
// // Applying LSP
interface Flyable{
    public function fly();
}

class Bird{
}

class Sparrow extends Bird implements Flyable{
    public function fly()
    {
        echo "Flying";
    }
}

class Parrot extends Bird implements Flyable{
    public function fly()
    {
        echo "Flying";
    }
}

class Penguin extends Bird{
}

function makeBirdFly(Flyable $bird){
    $bird->fly();
}

$sparrow = new Sparrow();
makeBirdFly($sparrow); // Output: Flying 

$parrot = new Parrot();
makeBirdFly($parrot); // Output: Flying 

$penguin = new Penguin();
makeBirdFly($penguin); // Error
```
> ပျံသန်းနိုင်တဲ့ငှက်တွေကို Flyable ဆိုတဲ့ interfaceကို implement လုပ်ပေးခြင်း၊ makeBirdFly() function တွင် Flyable interface ကို type hinting ပြုလုပ်ထားခြင်းဖြင့် မူရင်း bird ရဲ့ contractကိုမချိုးဖောက်ပဲ LSP ကိုလိုက်နာလိုက်တာပါ။ Penguin ဟာ Flyable ကို implement မလုပ်ထားတာမို့ သူနဲ့ makeBirdFly()ဟာ လုံးဝမသက်ဆိုင်တော့ပါဘူး။

## Interface Segregation Principle (ISP)
> A class should not be forced to implement interfaces it does not use. Instead of one large interface, break it into smaller, more specific interfaces.
> class တစ်ခုအနေနဲ့ မလိုအပ်တဲ့ interface တွေကို implement လုပ်ဖို့မလိုပါဘူး။ အဲ့ဒီအစားသီးသန့် interface အသေးလေးတွေအဖြစ်ခွဲထုတ်ပြီး implement လုပ်သင့်ပါတယ်။
```php
// Violating ISP
interface Worker {
    public function work();
    public function eat();
}

class HumanWorker implements Worker {
    public function work() {
        // Human working
    }

    public function eat() {
        // Human eating
    }
}

class RobotWorker implements Worker {
    public function work() {
        // Robot working
    }

    public function eat() {
        throw new Exception("Robots do not eat");
    }
}

// Applying ISP
interface Workable {
    public function work();
}

interface Eatable {
    public function eat();
}

class HumanWorker implements Workable, Eatable {
    public function work() {
        // Human working
    }

    public function eat() {
        // Human eating
    }
}

class RobotWorker implements Workable {
    public function work() {
        // Robot working
    }
}
```
> worker ဆိုတဲ့ interfaceမှာ workနဲ့eat method ၂ခုကို contract လုပ်ထားပါတယ်။ HumanWorkerမှာ reasonable ဖြစ်ပေမယ့် RobotWorkerမှာတော့ eat method ဟာမလိုအပ်ပါဘူး။ ဒီအခြေအနေမျိုးမှာ work methodကို Workable interfaceဖြင့်လည်းကောင်း eat method ကို Eatable interfaceဖြင့်လည်းကောင်း ခွဲထုတ်လိုက်ပြီး ISP ကိုသုံးကာ ဖြေရှင်းလိုက်ခြင်းဖြစ်ပါတယ်။

## Dependency Inversion Principle (DIP)
> High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details; details should depend on abstractions.
> High-level modules တွေဟာ low-level modules တွေကို မမှီခိုသင့်ပါဘူး။ အဲ့ဒီအစား abstractions တွေကို dependency injection ဖြင့် ခေါ်သုံးသင့်ပါတယ်။
```php
// Violating DIP
class MySQLDatabase {
    public function connect() {
        // Connect to MySQL database
    }
}

class UserService {
    private $db;

    public function __construct() {
        $this->db = new MySQLDatabase();
    }
}

// Applying DIP
interface Database {
    public function connect();
}

class MySQLDatabase implements Database {
    public function connect() {
        // Connect to MySQL database
    }
}

class UserService {
    private $db;

    public function __construct(Database $db) {
        $this->db = $db;
    }
}
```
> [!TIP]
> SOLID principleများဟာ တစ်ခုနှင့်တစ်ခုချိတ်ဆက်လျှက်ရှိပြီး တစ်ခုကိုမလိုက်နာပါက ကျန် principle များကိုပါချိုးဖောက်ရာရောက်သွားနိုင်ပါတယ်။ SOLID principleကိုလိုက်နာခြင်းဖြင့် clean code, Maintainable Code, Scalable Code, Testable Code, Bug နည်းခြင်း, Team Collaborationအတွက် ကောင်းမွန်ခြင်း, Future-Proof Code ဖြစ်ခြင်း စသည့်အကျိုးကျေးဇူးများကို ရရှိမှာဖြစ်ပါတယ်။ 
