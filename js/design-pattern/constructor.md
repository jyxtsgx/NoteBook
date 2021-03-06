[TOC]

## 构造函数模式

### 介绍
构造函数用于创建特定类型的对象——不仅声明了使用的对象，构造函数还可以接受参数以便第一次创建对象的时候设置对象的成员值。你可以自定义自己的构造函数，然后在里面声明自定义类型对象的属性或方法。

### 基本实现
在Javascript里，构造函数通常认为用来创建实例的，Javascript没有类的概念，但是有特殊的构造函数。通过new关键字来调用自定义的构造函数，this关键字引用的是新创建的对象。
```
function Car(model, year, miles) {
  this.model = model;
  this.year = year;
  this.miles = miles;
  this.output = function() {
    return this.model + ' run ' + this.miles + ' km';
  };
}

var dudu = new Car('Dudu', 2009, 20000);
console.log(dudu.output());
```
上面的例子有个小问题，首先是继承很麻烦，其次是output()在每次创建对象的时候都重新定义了，最好的方法是让所有Car类型的实例都能共享这个output()方法，这样在批量创建对象的时候就能有效节约内存。

我们可以这样做：
```
function Car(model, year, miles) {
  this.model = model;
  this.year = year;
  this.miles = miles;
  this.output = formatCar;
}

function formatCar() {
  return this.model + ' run ' + this.miles + ' km';
}
```
这个方式虽然可用，但我们有更好的方式。

### 构造函数与原型
Javascript里函数有个原型属性叫prototype，当调用构造函数创建对象时，所有新创建对象都可以共享该原型的属性。
```
function Car(model, year, miles) {
  this.model = model;
  this.year = year;
  this.miles = miles;
}

Car.prototype.output = function() {
  return this.model + ' run ' + this.miles + ' km';
}

var dudu = new Car('Dudu', 2009, 20000);
console.log(dudu.output());
```

### 只能用new吗？
上面的示例中都是用new来创建对象的，只有这种方式吗？
```
function Car(model, year, miles) {
    this.model = model;
    this.year = year;
    this.miles = miles;
    this.output = function () {
      return this.model + ' run ' + this.miles + ' km';
    }
}

// 1: call as function
Car("Dudu", 2009, 20000);  // add to window
console.log(window.output());

// 2: call as prop of an object
var o = new Object();
Car.call(o, "Dudu", 2010, 5000);
console.log(o.output());
```
该代码的方法1有点特殊，如果不适用new直接调用函数的话，this指向的是全局对象window.

### 强制使用new
通过判断 `this` 的 `instanceof` 是不是Car来决定返回new Car还是继续执行代码，如果使用的是new关键字，则 `this instanceof Car` 为真，会继续执行下面的参数赋值，如果没有用new，`this instanceof Car` 就为假，就会重新new一个实例返回。
```
function Car(model, year, miles) {
  if (!(this instanceof Car)) {
    return new Car(model, year, miles);
  }
  
  this.model = model;
  this.year = year;
  this.miles = miles;
}

Car.prototype.output = function() {
  return this.model + ' run ' + this.miles + ' km';
}

var dudu = new Car('Dudu', 2009, 20000);
console.log(dudu.output());
```