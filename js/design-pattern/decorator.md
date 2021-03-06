[TOC]

## 装饰者模式

### 介绍
装饰者提供比继承更有弹性的替代方案。装饰者用于包装具有相同接口的不同对象，不仅允许你向方法添加行为，而且还可以将方法设置成原始对象调用(例如装饰者的构造函数)。

装饰者用于通过重载的方法的形式添加新功能，该模式可以在被装饰者前面或后面加上自己的行为以达到特定的目的。

### 基本实现
实现装饰者模式的其中一种方法是使得每个装饰者成为一个对象，并且该对象包含了应该被重载的方法。每个装饰者实际上继承了目前已经被前一个装饰者进行增强后的对象。每个装饰方法在uber(继承对象)上调用了同样的方法并获取其值，此外它还继续执行了一些操作。

该实现是从一个构造函数和一个原型方法开始的：
```
function Sale(price) {
  this.price = price || 100;
}
Sale.prototype.getPrice = function() {
  return this.price;
}

// decorators
Sale.decorators = {};
Sale.decorators.fedtax = {
  getPrice: function() {
    var price = this.uber.getPrice();
    price += price * 5 / 100;
    return price;
  }
};
Sale.decorators.quebec = {
  getPrice: function() {
    var price = this.uber.getPrice();
    price += price * 7.5 / 100;
    return price;
  }
};
Sale.decorators.money = {
  getPrice: function() {
    return '$' + this.uber.getPrice().toFixed(2);
  }
}
Sale.decorators.cdn = {
  getPrice: function() {
    return 'CDN$' + this.uber.getPrice().toFixed(2);
  }
}

// decorators link with Sale
Sale.prototype.decorate = function(decorator) {
  var F = function() {},
      overrides = this.constructor.decorators[decorator],
      i, newobj;
      
  F.prototype = this;
  newobj = new F();
  newobj.uber = F.prototype;
  
  for (i in overrides) {
    if (overrides.hasOwnProperty(i)) {
      newobj[i] = overrides[i];
    }
  }
  return newobj;
}

// test1
var sale = new Sale(100);
sale = sale.decorate('fedtax');
sale = sale.decorate('cdn');
sale.getPrice();  // 'CDN$105.00'

-------------------------------------
// test2
var sale = new Sale(100);
sale = sale.decorate('fedtax');
sale = sale.decorate('quebec');
sale = sale.decorate('money');
sale.getPrice();  // '$112.88'
```
最终的效果是，当你在上一个用法示例中执行 `sale.getPrice()` 时，调用了 `money` 装饰者的方法。但由于每一个装饰者方法都首先调用父对象的方法，money的 `getPrice()` 将会首先调用quebec的 `getPrice()`，这又需要一次调用fedtax的 `getPrice()`等，倚着原型链一路攀升到由 `Sale()` 构造函数所实现的原始未经装饰的 `getPrice()`。
![prototype link](https://github.com/yiyunShm/NoteBook/blob/master/js/images/decorators_link.png)

### 列表实现
现在让我们探讨一个稍微不同的实现方法，它利用了Javascript语言的动态性质，并且根本不需要使用继承。这种实现方法还可以很容易的支持反装饰或撤销装饰，这意味着可以简单地从装饰者列表中删除一个项目。
```
function Sale(price) {
  this.price = price || 100;
  this.decorators_list = [];
}

// decorators
Sale.decorators = {};
Sale.decorators.fedtax = {
  getPrice: function(price) {
    return price + price * 5 / 100;
  }
};
Sale.decorators.quebec = {
  getPrice: function(price) {
    return price + price * 7.5 / 100;
  }
};
Sale.decorators.money = {
  getPrice: function(price) {
    return '$' + price.toFixed(2);
  }
}
Sale.decorators.cdn = {
  getPrice: function(price) {
    return 'CDN$' + price.toFixed(2);
  }
}

Sale.prototype.decorate = function(decorator) {
  this.decorators_list.push(decorator);
};
Sale.prototype.getPrice = function() {
  var price = this.price,
      i, name,
      len = this.decorators_list.length;
      
  for (i = 0; i < len; i += 1) {
    name = this.decorators_list[i];
    price = Sale.decorators[name].getPrice(price);
  }
  return price;
};
```
装饰者模式的第二种实现更为简单，并且不涉及继承，避免了原型链查找带来的性能消耗。此外，装饰方法也是非常简单的，通过"同意"被装饰的那个方法来完成。在这个实现示例中，`getPrice()` 是唯一允许装饰的方法。如果想拥有更多被装饰的方法，可以抽象成一个辅助方法来接受"可装饰"的方法，在这样的实现中decorators_list属性变成了一个对象，且该对象中的每个属性都是以装饰对象数组中的方法和值命名。

### 总结
装饰者模式是为已有功能动态地添加更多功能的一种方式，把每个要装饰的功能放在单独的函数里，然后用该函数包装所要装饰的已有函数对象，因此，当需要执行特殊行为的时候，调用代码就可以根据需要有选择地、按顺序地使用装饰功能来包装对象。优点是把类（函数）的核心职责和装饰功能区分开了。