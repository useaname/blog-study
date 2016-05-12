---
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
