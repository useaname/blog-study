---
tag:'javascript,prototype'
date: 2016-05-11 20:07
status: public
title: javascript原型
---

创建的每一个函数都有一个property(原型)属性，这是属性的一个指针，指向一个对象，而这个对象的用途通过调用构造函数而创建那个对象实例的原型对象。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。换句话说，<b>*不必在构造函数中定义对象的实例的信息，而是可以将这些信息直接添加到原型对象中。*</b>
1.理解原型对象
无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个prototype属性，这个属性指向函数的原型对象。在默认情况下，所有原型对象都会自动获得constructor(构造函数)属性，这个属性包含一个指向prototype属性所在函数的指针。就拿前面例子来说Person.prototype.constructor指向Person。而通过这个构造函数，还可以继续为原型对象添加其他属性和方法。
创建了自定义构造方法之后，其中原型对象默认只会取得constructor属性。至于其他方法，都是从Object继承而来的。当调用构造函数创建一个新实例后，该实例的内部将包含一个指针(内部属性)，指向构造函数的原型对象。ECMA-262第5版中管这个指针叫做[Prototype]]。虽然在脚本中没有标准的方式访问[Prototype]]，但firefox,Safari和Chrome在每个对象都支持一个属性\_proto\_,而在其他实现中，这个属性针对脚本则是完全不可见的。<b>*要明确一点，这个连接存在于实例与构造函数的原型对象之间，而不是存在实例与构造函数之间。*</b>

<b>*当代码读取对象的某个属性时，都会执行一次搜索，目标是具有给定名字的属性。搜索首先从对象实例本身开始。如果在实例中找到了具有给定名字的属性，则返回该属性的值；如果没找到，则继续搜索指针指向的原型对象，在原型对象中查找具有给定名字的属性。如果在原型对象中找到这个属性，则返回该属性的值。也就是说，在调用person1.sayName()的时候，会先执行两次搜索。首先解析器会问，实例person1有sayName属性吗？答没有。然后继续搜索，再问person1的原型有sayName属性吗？答有。于是就读取那个保存在原型对象中的函数。*</b>
虽然可以通过对象实例访问保存在原型中的值，但却不能通过对想实例重写原型中的值。如果我们在实例中添加一个属性，而该属性与实例一个属性同名，那么我们就在实例中创建该属性，该属性将会屏蔽原型中的那个属性。
```javascript
  function P2(){}
    P2.prototype.name = "Nic";
    P2.prototype.age = 12;
    P2.prototype.job = "SE";
    P2.prototype.sayName = function () {
        console.log(this.name);
    }

     var p21 = new P2();
     var p22 = new P2();
     p22.name = "Tim";

    console.log(p21.name);  //Nic 来自实例
    console.log(p22.name); //Tim 来自原型
```
<b>*当对对象添加一个属性时，这个属性就会屏蔽原型对象中保存的同名属性；换句话说，添加这个属性只会阻止我们访问原型中的那个属性，但不会修改那个属性。即使这个属性设置为null，也只会在实例中设置这个属性，而不会恢复其指向原型的链接。不过可以试用delete操作符可以完全删除实例属性，从而能重新访问原型中的属性。*</b>
```javascript
    function Person() {

    }

    Person.prototype.name = "Nic";
    Person.prototype.age = 29;
    Person.prototype.job = "SE";
    Person.prototype.sayName = function () {
        console.log(this.name);
    }

    var p1 = new Person();
    var p2 = new Person();

    console.log(p1.hasOwnProperty("name")); //false

    p1.name = "Grey";
    console.log(p1.name); //Grey 来自实例
    console.log(p1.hasOwnProperty("name")); //true

    console.log(p2.name); //Nic 来自原型
    console.log(p2.hasOwnProperty("name")); //false
    delete p1.name;
    console.log(p1.name); //Nic 来自原型
    console.log(p1.hasOwnProperty("name")); //false

```
通过hasOwnPrototype()方法，什么时候访问真实属性，什么时候访问是原型属性就会一清二楚。调用p1.hasOwnProperty("name")时，只有当p1重写name属性后才会返回true。因为只有name才是一个实例属性，而非原型属性。
2.原型和in操作符
两种方式使用in操作符单独使用和在for-in循环中使用。在单独使用时，in操作符会在通过对象能够访问给定属性时返回true,<b>*无论该属性是否存在实例还是原型中。*</b>
```javascript
    function Person() {

    }
    Person.prototype.name = "Nic";
    Person.prototype.age = 29;
    Person.prototype.job = "SE";
    Person.prototype.sayName = function () {
        console.log(this.name);
    }

    var person1 = new Person();
    var person2 = new Person();

    console.log(person1.hasOwnProperty("name")); //false
    console.log("name" in person1); //true

    person1.name = "Grey";
    console.log(person1.name); //来自实例
    console.log(person1.hasOwnProperty("name")); //true
    console.log("name" in person1); //true

    console.log(person2.name); //来自原型
    console.log(person2.hasOwnProperty("name")); //false
    console.log("name" in person2); //true

    delete  person1.name;
    console.log(person1.name); //Nic 来自原型
    console.log(person1.hasOwnProperty("name")); //false
    console.log("name" in person1); //true
```
同时试用in和hasOwnProperty()方法判断属性是否存在于原型中:
```javascript
function hasPrototypeProperty(object, name) {
        return !object.hasOwnProperty(name) && (name in object);
    }
```
由于in操作符只要通过对象能够访问到的属性就返回true，hasOwnProperty()只在属性存在于实例中才返回true，因为只要in操作符返回true而hasOwnProperty()返回false，就可以确定是原型属性。

```javascript
 function Person() {

    }
    Person.prototype.name = "Nic";
    Person.prototype.age = 29;
    Person.prototype.job = "SE";
    Person.prototype.sayName = function () {
        console.log(this.name);
    }

    var person1 = new Person();
    person1.name = "Tim";
    var person2 = new Person();
    
    hasPrototypeProperty(person1, "name");  //false
    hasPrototypeProperty(person2, "name");  //true        
```
在使用for-in循环中返回的是所有<b>*能够通过对象访问，可枚举的(enumerated)属性*</b>，其中既包括存在实例中的属性，也包括存在于原型中的属性。屏蔽了原型中不可枚举的属性(即将[[Enumerable]]标记为false的属性)的实例属性也会在for-in循环中返回。因为根据规定，所有开发人员定义的属性都是可枚举的---在IE8及更早的版本中例外。
获取对象上可枚举的实例属性，可以使用ECMAScript5的Object.keys()方法。这个方法接收一个对象作为参数，返回一个包含所有可枚举属性的字符串数组。
```javascript
    function Person() {}
    Person.prototype.name = "Nic";
    Person.prototype.age = 29;
    Person.prototype.job = "SE";
    Person.prototype.sayName = function () {
        console.log(this.name);
    }
    Object.keys(Person.prototype); //["name", "age", "job", "sayName"]
    var person = new Person();
    person.name = "Tim";
    person.age = 21;
    //person实例调用
    Object.keys(person); //["name", "age"]
```
得到所有的实例属性，无论是否可被枚举。
```javascript
    function Person() {}
    Person.prototype.name = "Nic";
    Person.prototype.age = 29;
    Person.prototype.job = "SE";
    Person.prototype.sayName = function () {
        console.log(this.name);
    }
Object.getOwnPropertyNames(Person.prototype); //["constructor", "name", "age", "job", "sayName"]
```
3.更简单的原型语法
前面的例子中每添加一个属性和方法都要敲一遍Person.prototype.为减少不必要的输入，也为了从视觉上更好的封装原型功能，更常见的方法是用一个包含所有属性和方法的对象字面量来重写整个原型对象:
```javascript
    function Person() {}
    Person.prototype = {
        name : "Tim",
        age : 29,
        job : "SE",
        syaName : function () {
            console.log(this.name);
        }
    };
```
上面的代码中，将Person.prototype设置为等于一个以对象字面量形式创建的对象，最终结果相同，但有一个例外:constructor这个属性不再指向Person了。<b>*之前说过，每创建一个对象，就会同时创建prototype对象，这个对象自动获得constructor属性。*</b>而这里使用的语法，从本质上重写了默认的prototype对象，因此constructor属性也就编程了新对象的constructor属性(指向Object构造函数)，不再指向Person函数。此时尽管instanceof操作符能返回正常的结果，但通过constructor已无法确定对象类型。如下：
```javascript
    var friend = new Person();
    console.log(friend instanceof Object); //true
    console.log(friend instanceof Person); //true
    console.log(friend.constructor == Person); //false
    console.log(friend.constructor == Object); //true
```
如果在constructor的值真的很重要，可以像下面这样特意将它设置成适当的值得。
```javascript
    function Person() {}
    Person.prototype = {
        constructor : Person,
        name : "Tim",
        age : 29,
        job : "SE",
        syaName : function () {
            console.log(this.name);
        }
    };
```
这种方式重设constructor属性会导致它的[[Enumerable]]特性设置为true。默认情况下，原生的constructor是不可枚举的，因此如果你是用兼容ECMAScript5的Javascript引擎,可以试一试Object.defineProperty().
```javascript
//重设构造函数,只适用ECMAScript5兼容的游览器
    Object.defineProperty(Person.prototype, "constructor", {
        enumerable : false,
        value : Person
    });
```
4.原型的动态性
由于在原型中查找值的过程是一次搜索，因此我们对原型对象所做的任何修改都能够立即从实例上反应出来---即使是先创建了实例后修改原型也照样如此。
```javascript
    function Person() {}
    Person.prototype = {
        constructor : Person,
        name : "Tim",
        age : 29,
        sayName : function(){
            console.log(this.name);
        }
    }

    var p1 = new Person();
    p1.sayName();
    Person.prototype.sayHi = function(){
        console.log(this.name + " say Hi.");
    }
```
尽管可以随时为原型对象添加属性和方法，并且修改能立刻在所有对象实例中反应出来，但是如果重写整个原型对象，那么情况就不一样了。调用构造函数时会为实例添加一个指向最初原型的[[prototype]]的指针，而把原型修改为另外一个对象就等于切断了构造函数与最初原型之间的联系。<b>*实例中指针仅指向原型，而不指向构造函数。*</b>
5.原生对象的原型
原型模式的重要性不仅体现在创建自定义类型方面。连所有原生的引用类型，都是采用这种模式创建的。所有原生的引用类型(Object,Array,String等等)都在其构造函数的原型上定义了方法。在Array.prototype中可以找到sort方法。
通过原型对象，不仅可以取得所有默认方法的引用，而且可以定义新方法。可以像修改自定义对象的原型一样修改原生对象的原型，因此可以随时添加方法。下面的代码给基本包装类型String添加一个名为startWith()的方法：
```javascript
String.prototype.starsWith = function (text) {
        return this.indexOf(text) == 0;
    }
    var msg = "Hello";
```
6.原型对象的问题
原型模式省略了为构造函数传递初始化参数这一环节，结果所有的实例默认情况下都将取得相同的属性值，虽然这会在某种问题上带来一些不方便，单还不是原型最大的问题最大问题是由其共享的本性导致的。
对于包含引用类型值的属性来说，问题就比较突出了。
```javascript
 function Person() {
        
    }
    Person.prototype = {
        constructor: Person,
        name : "Nic",
        age : 29,
        job : "SE",
        friends: ["A","B"],
        sayName : function () {
            console.log(this.name);
        }
    }

    var person1 = new Person();
    var person2 = new Person();

    person1.friends.pusp("C");

    console.log(person1.friends);
    console.log(person2.friends);
    console.log(person1.friends == person2.friends);
```
6.2.4 组合试用构造函数和原型模式
创建自定义类型最常见方式，就是组合试用构造函数与原型模式。构造函数模式用于定义实例属性，而原型模式用于定义方法和共享属性。结果每个实例都会有自己的一份实例属性的副本，但同事又共享这对方的引用，最大限度的节省内存。这种模式还支持向构造函数传递参数。可谓集两种模式之长。
这种构造函数与原型的混成的模式，是目前ECMAScript中使用最广泛的一种创建自定义类型的方法。
```javascript
function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.friends = ["Shelby", "Court"];
    }

    Person.prototype = {
        constructor : Person,
        sayName : function(){
            console.log(this.name);
        }
    }

    var person1 = new Person("Tim", 29, "SE");
    var person2 = new Person("Tom", 30, "AB");

    person1.friends.push("newFriends");
    console.log(person1.friends);
    console.log(person2.friends);
    console.log(person1.friends == person2.friends);
    console.log(person1.sayName == person2.sayName);
```