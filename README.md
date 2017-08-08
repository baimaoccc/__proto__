一、浅谈原型
=============

首先我们要知道创建对象的方法有两种：
-----------------------------
1.通过**字面量**的方式直接创建

```

	var obj = {
		name:'baimao',
		age:21
	}
```
2.通过**构造函数**创建对象

```
	function People(name,age) {
		this.name = name;
		this.age = age;
	}
```


&#x3000;&#x3000;而当我们使用构造函数创建对象时，对对象的属性或者方法的设置是通过传参的方式进行的，考虑一个问题，公有的属性或者方法如果仍在构造函数中通过传参设置，每次new出来的新的对象都保留有一份构造函数中的信息，且有部分信息时相同并不会更改的，比如，人生来就有四肢，能吃饭能说话等。这样就浪费了内存，相同的信息在内存中存在了很多份（每通过构造函数new一个实例就复刻了一份相同的东西）。

&#x3000;&#x3000;而原型就是为了解决相同信息的复用问题，使用原型可以使得所有通过构造函数new出来的对象的原型指向的是同一个地址空间，避免了内存上的浪费，也使得继承概念更合理。

&#x3000;&#x3000;这里面要指明的是，放在原型中的属性和方法在内存中只存在一份，所有通过该构造函数new出来的对象都使用的是这一份原型。所以js中的原型是引用原型。


>来声明一下对象的 \__proto__ 与构造函数的prototype区别。对象都存在\__proto__ 属性，而通过调用构造函数new出来的对象，其\__proto__ 属性是指向构造函数的prototype属性的，而构造函数的prototype属性本身也是一个对象，仍存在 \__proto__ 属性，这将会引入后续的原型链的问题，在后面会介绍。

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

二.js中的继承
===========

&#x3000;&#x3000;我们都理解的是js中实际是不存在类的概念的，所谓继承只是通过构造函数的继承和构造函数中的原型的继承来实现的。但我们下面直接用类的概念来讲更容易理解，假设js中存在类吧</br>

1.构造函数的继承
--------------
&#x3000;&#x3000;（1）首先介绍构造函数的继承

```
	 function People(name,age) {
        this.name = name;
        this.age = age;
    }
    People.prototype.eat = function () {
	    console.log(this.name + '正在吃...');
    }
    function Student(name,age) {
        People.call(this,name,age);
        //People.apply(this,arguments);
    }
    console.log(new Student('baimao',21));

```

 ![](https://github.com/baimaoccc/__proto__/blob/master/img/img1.png?raw=true)
 
&#x3000;&#x3000;通过上图我们可以看到通过call或者apply的方法调用父类的构造函数是可以使用父类的构造函数对子类实例进行赋值的，总结一下，也就是说通过call,apply方法可以使用父类的构造函数。但是这种方法有局限性，细心的人可能会观察到，使用call或者apply方法继承到的只有父类的构造方法中的属性或者方法，父类原型中的属性和方法不会被继承，为了解决这一问题，我们可以通过如下方式：

2.子类原型的继承
--------------

```
	方法一：
	Student.prototype = Object.create(People.prototype);
	//在这里之所以要自己添加子类原型中的构造函数是因为我们直接使用
	//Object.create(People.prototype)创建一个对象出来给子类的原型赋值，会
	//覆盖掉子类原有的构造函数，所以需要手动设置
	Student.prototype.constructor = Student;
	
	方法二：
	Student.prototype.__proto__ = People.prototype;
	
	var student = new Student('baimao',21);
	student.eat();
	console.log(student);
```

 ![](https://github.com/baimaoccc/__proto__/blob/master/img/img2.png?raw=true)

&#x3000;&#x3000;实际中我们可以发现，通过上述两种方式是在子类构造函数的原型的原型中添加方法，当我们通过new子类对象并调用方法时，该方法会先在子类自己的构造函数中找，找不到则在子类的原型中找，再找不到就去子类原型的原型中找，直到找到为止，否则报错，该方法未定义 not defined
即为原型链的概念：

```
关于原型链：
		先在对象自身去找（构造函数中找）,找到后调用即可；
		如果找不到，就去自身原型中找，找到调用即可；
		如果还找不到，就去原型的原型中找，找到调用即可...
      	直到找到Object的原型，还是找不到，就抛出异常
```

&#x3000;&#x3000;通过继承父类而得到的子类对象，如果继续在子类的原型中添加父类中存在的属性或者方法，意义上为重写父类的方法，但实际意义上只是在子类的原型中添加了一个方法，并没有真正覆盖父类的方法，通过子类构造函数的原型的原型中还是可以查看到父类原型的属性或者方法.

 ![](https://github.com/baimaoccc/__proto__/blob/master/img/img3.png?raw=true)
 
> 关于isPrototypeOf 和 hasOwnProperty()
> 
> 1. isPrototypeOf<br>
> 		判断原型和对象之间的关系<br>
      isPrototypeOf 判断原型和对象之间的关系:判断对象是否存在于另一个对象的原型链中
> 2. hasOwnProperty()<br>
> 		用于指示一个对象自身(不包括原型链)是否具有指定名称的属性。 如果有，返回true，否则返回false。  
> 3. in <br>
> 		可以查看原型链中是否存在某属性<br>
> 		console.log('family' in dog)


&#x3000;&#x3000;注意我们在修改原型时不能使用对象的打点调用：
dog.family = '哈哈';这样写是为对象新增加一个属性，不会修改到原型，通过打点调用的方法相当于只能get不能set


3.修改父类原型中的方法
-------------------


&#x3000;&#x3000;子类继承父类，对父类的原型中修改原先存在的属性，修改只供自身使用，对父类没有影响但如果子类对父类的原型中添加了父类不存在的属性，添加操作会影响父类。

```
	function Animal(family) {
        this.family = family;
        Animal.prototype.run = function () {
            console.log('动物都会跑');
        }
        Animal.prototype.legs = 4;
    }

    function Dog(family, name, color) {
        Animal.call(this, family);
        this.name = name;
        this.color = color;
    }

    // 用一个中间变量obj 作为桥梁去完成子类对父类的继承
    var obj = Object.create(Animal.prototype);
    Dog.prototype = obj;
    Dog.prototype.constructor = Dog;

    Dog.prototype.skill = function () {
        console.log('看家');
    }



    //修改了父类
    Dog.prototype.__proto__.add = function () {
        console.log('这是一个通过子类去修改父类的测试');
    }

    //会发现中间变量被改，所以js中对象的拷贝是引用拷贝，即拷贝地址的值拷贝
    console.log(obj);


    Dog.prototype.__proto__.legs = 2;


    var animal = new Animal('test');

    //在这里输出父类对象，会发现add方法增添了，但父类的legs属性并没有被改
    animal.add();
    console.log(animal.legs);
	
	
```
执行结果如下：
 ![](https://github.com/baimaoccc/__proto__/blob/master/img/img4.png?raw=true)
 
 
三、ES6中的继承新语法
==================
在ES6中新增了class和extends关键字

```

		class People {
	        // 构造函数
	        constructor(name, age) {
	            //初始化属性
	            this.name = name;
	            this.age = age;
	        }
	
	        eat(food) {
	            console.log(this.name + '吃：' + food);
	        }
	    }
	
	    //设置原型中的内容
	    People.prototype.legs = 2;
	
	    //创建People对象
	    var people = new People('baimao', 21);
	    people.eat('苹果');
	    console.log(people);
	
	    class Man extends People {
	        constructor(name, age, sex) {
	            //相当于会自动的去执行People的构造函数
	            super(name, age);
	            //新增，扩展自己的属性
	            this.sex = sex;
	        }
	
	        playMJ(hours) {
	            console.log(this.name + '打了' + hours + '个小时麻将');
	        }
	    }
	
	    var man1 = new Man('翠花', 28, '男');
	    man1.playMJ(2);
	
	
```

ES6中类的数据类型就是函数，类本身指向构造函数
在ES6中，除了constructor中的属性是放在类中，其余定义的方法都是放在构造函数的prototype中.
子类继承父类，使用super关键字调用父类的构造方法


 
 





