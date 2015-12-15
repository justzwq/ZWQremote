##this is a JavaScript Learining log file##
>"钟万强" first updated on dec 09 2015

---

>updated on dec 10 2015

###1.js函数
	(1)函数内部有一个指向该函数应用的指针，保存在arguments数组里面，即argume	nts.calle。
	在使用函数递归调用的时候最好使用arguments.callee(),而不用函数	名，从而降低对函数名的耦合。
	(2)函数内部有另一个属性，caller指向调用该函数的函数的引用
	function outer(){
	inner();
	}
	function inner(){
	alert(arguments.callee.caller);
	}
	outer();
	该代码会打印outer函数的源代码
	(3)以上两个属性在严格模式访问下会导致错误，目的是保证安全性，不会被第三
	方代码在相同环境下窥探函数的代码

###2.js创建对象常使用的方式
	2.1在 ECMAScript 中无法创建类,但可以通过构造函数来创建特定类型的对象
	例如
	function Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.sayName = function(){
	alert(this.name);
	};
	}
	var person1 = new Person("Nicholas", 29, "Software Engineer");
	var person2 = new Person("Greg", 27, "Doctor");	
	要创建 Person 的新实例,必须使用 new 操作符。以这种方式调用构造函数实际上
	会经历以下 4个步骤:
	(1) 创建一个新对象;
	(2) 将构造函数的作用域赋给新对象(因此 this 就指向了这个新对象)
	(3) 执行构造函数中的代码(为这个新对象添加属性);
	(4) 返回新对象。
	
	2.2任何函数,只要通过 new 操作符来调用,那它就可以作为构造函数;
	而如果不通过new 操作符来调用,那它跟普通函数也不会有什么两样。
	//也可以作为普通函数调用
	Person("Greg", 27, "Doctor"); // 默认把创建的属性添加到Global对象window
	window.sayName(); //"Greg"
	//也可以在另一个对象的作用域中调用
	var o = new Object();
	Person.call(o, "Kristen", 25, "Nurse");
	o.sayName(); //"Kristen"

###3.创建对象的原型模式
	3.1创建的每个函数都有一个 prototype (原型)属性,这个属性是一个指针,
	指向一个对象,而这个对象的用途是包含可以由特定类型的所有实例共享的属性
	和方法。
	使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。
	(类似Java中的静态方法)
	function Person(){
	}
	Person.prototype.name = "Nicholas";
	Person.prototype.age = 29;
	Person.prototype.job = "Software Engineer";
	Person.prototype.sayName = function(){
	alert(this.name);
	};
	var person1 = new Person();
	person1.sayName();//"Nicholas"
	var person2 = new Person();
	person2.sayName();	//"Nicholas"
	alert(person1.sayName == person2.sayName);//true
	
	3.2如果在实例中添加了一个属性,而该属性与实例原型中的一个属性同名,
	那就在实例中创建该属性,该属性将会屏蔽原型中的那个属性。
	function Person(){
	}
	Person.prototype.name = "Nicholas";
	Person.prototype.age = 29;
	Person.prototype.job = "Software Engineer";
	Person.prototype.sayName = function(){
	alert(this.name);
	};
	var person1 = new Person();
	var person2 = new Person();
	person1.name = "Greg";
	alert(person1.name);//"Greg"——来自实例
	alert(person2.name);//"Nicholas"——来自原型
	**原型的属性被覆盖，但并没有改变，可用delete删除实例的属性，从而访问原型
	属性**
	delete person1.name;
	alert(person1.name); //"Nicholas"——来自原型
	
	3.3先新建对象，再修改原型属性，也会在对象上反应出来
	但只限使用这种方法：
	var friend = new Person();
	Person.prototype.sayHi = function(){
	alert("hi");
	};
	friend.sayHi();//"hi"(没有问题!)
	如果重写整个原型对象，就会出现一些问题	
	调用构造函数时会为实例添加一个指向最初原型的[[Prototype]] 指针,
	而把原型修改为另外一个对象就等于切断了构造函数与最初原型之间的联系。
	请记住:实例中的指针仅指向原型,而不指向构造函数。看下面的例子。
	function Person(){
	}
	var friend = new Person();
	Person.prototype = {
	constructor: Person,
	name : "Nicholas",
	age : 29,
	job : "Software Engineer",
	sayName : function () {
	alert(this.name);
	}
	};	
	friend.sayName();//error
###4.组合使用构造函数模式和原型模式
	这种模式创建自定义类型，可以最大程度的节省内存
	function Person(name, age, job){    //构造函数模式，可理解为创建私有属性
	this.name = name;
	this.age = age;
	this.job = job;
	this.friends = ["Shelby", "Court"];
	}
	Person.prototype = {       //原型模式，可理解为创建静态方法，节省内存
	constructor : Person,
	sayName : function(){
	alert(this.name);
	}
	}
	var person1 = new Person("Nicholas", 29, "Software Engineer");
	var person2 = new Person("Greg", 27, "Doctor");
	person1.friends.push("Van");
	alert(person1.friends);//"Shelby,Count,Van"
	alert(person2.friends);//"Shelby,Count"
	alert(person1.friends === person2.friends);//false
	alert(person1.sayName === person2.sayName);//true

