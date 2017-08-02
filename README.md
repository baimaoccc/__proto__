###一、浅谈原型

####首先我们要知道创建对象的方法有两种：
1.通过**字面量**的方式直接创建

```

	var obj = {
		name:'baimao',
		age:21
	}
```
2.通过**构造函数**创建对象(更经常用的实际是这一种)

```
	function People(name,age) {
		this.name = name;
		this.age = age;
	}
```


&#x3000;&#x3000;而当我们使用构造函数创建对象时，对对象的属性或者方法的设置是通过传参的方式进行的，考虑一个问题，公有的属性或者方法如果仍在构造函数中通过传参设置，每次new出来的新的对象都保留有一份构造函数中的信息，且有部分信息时相同并不会更改的，比如，人生来就有四肢，能吃饭能说话等。这样就浪费了内存，相同的信息在内存中存在了很多份（每通过构造函数new一个实例就复刻了一份相同的东西）。

&#x3000;&#x3000;而原型就是为了解决相同信息的复用问题，使用原型可以使得所有通过构造函数new出来的对象的原型指向的是同一个地址空间，避免了内存上的浪费，也使得继承概念更合理。

&#x3000;&#x3000;这里面要指明的是，放在原型中的属性和方法在内存中只存在一份，所有通过该构造函数new出来的对象都使用的是这一份原型。所以js中的原型是引用原型。


>来声明一下对象的 \__proto__ 与构造函数的prototype区别。任何对象都存在\__proto__ 属性，而通过调用构造函数new出来的对象，其\__proto__ 属性是指向构造函数的prototype属性的，而构造函数的prototype属性本身也是一个对象，仍存在 \__proto__ 属性，这将会引入后续的原型链的问题，在后面会介绍。

3.为构造函数设置原型的方法

```
	(1)		People.prototype.arms = 2;
			People.prototype.eat = function() {
				console.log(this.name + '会吃饭');
			}
	
```

```
	(2)		var people = new People('baimao',21);
			people.__proto__.arms = 2;
			people.__proto__.eat = function() {
				console.log(this.name + '会吃饭');
			}

```


***

###2.js中的继承

&#x3000;&#x3000;我们都理解的是js中实际是不存在类的概念的，所谓继承只是通过构造函数的继承和构造函数中的原型的继承来实现的。</br>

&#x3000;&#x3000;（1）首先介绍构造函数的继承

```
	function People(name,age) {
        this.name = name;
        this.age = age;
    }
    function Student(name,age) {
        People.call(this,name,age);
    }
    console.log(new Student('baimao',21));

```

 ![](/Users/baimao/Desktop/git 原型/img/img1.png)
	



