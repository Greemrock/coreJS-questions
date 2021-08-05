# CoreJS-Interview

## Questions

## JavaScript:

## __Advanced Expressions__

### - __Hoisting__
Hoisting (поднятие) - это механизм в JavaScript в котором переменные и объявления функций передвигаются вверх своей области видимости перед тем, как код будет выполнен. Это происходит, т.к. компиляция кода происходит в два прохода. При первом проходе компилятор получает все объявления переменных, все идентификаторы. При этом никакой код не выполняется, методы не вызываются. При втором проходе собственно происходит выполнение.
- _var_
```javascript
console.log(foo); //ReferenceError: foo is not defined
```
```javascript
console.log(foo); //undefined
var foo = "Tom";
```
```javascript
var c = a * b;
var a = 7;
var b = 3;
console.log(c); //NaN
```
Область видимости var заканчивается внутри функции:
```javascript
function hoist() {
  console.log(message);
  var message = 'Hoisting is all the rage!'
}
hoist(); //undefined
```
Вот как интерпретатор видит код выше:
```javascript
function hoist() {
  var message;
  console.log(message);
  message = 'Hoisting is all the rage!'
}
hoist(); // Вывод: undefined
```
- _let_
```javascript
console.log(hoist); //ReferenceError: hoist is not defined
let hoist = 'The variable has been hoisted.';
```
```javascript
let hoist;
console.log(hoist); //undefined
hoist = 'Hoisted'
```
Переменные объявленные как let заключены в область видимости блока, а не функции.
- _const_
```javascript
console.log(hoist); //ReferenceError: hoist is not defined
const hoist = 'The variable has been hoisted.';
```
```javascript
const PI;
console.log(PI); //SyntaxError: Missing initializer in const declaration
PI=3.142;
```
Переменные объявленные с let и const остаются неинициализированными в начале выполнения, в то время как переменные объявленные с var инициализируются со значением undefined.
- _function declaration_
```javascript
display(); //"Hello Hoisting"

function display(){
    console.log("Hello Hoisting"); 
}
```
- _function expression_
```javascript
display(); //TypeError: display is not a function.

var display = function (){
    console.log("Hello Hoisting"); 
}
```
- _class declaration_ (не всплывает)
```javascript
var Frodo = new Hobbit();
Frodo.height = 100;
Frodo.weight = 300;
console.log(Frodo); //ReferenceError: Hobbit is not defined
class Hobbit {
  constructor(height, weight) {
    this.height = height;
    this.weight = weight;
  }
}
```
- _class expression_ (не всплывает)
```javascript
var Square = new Polygon();
Square.height = 10;
Square.width = 10;
console.log(Square); //TypeError: Polygon is not a constructor
var Polygon = class Polygon {
constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
```

[read more ...](https://medium.com/@stasonmars/%D1%80%D0%B0%D0%B7%D0%B1%D0%B8%D1%80%D0%B0%D0%B5%D0%BC%D1%81%D1%8F-%D1%81-%D0%BF%D0%BE%D0%B4%D0%BD%D1%8F%D1%82%D0%B8%D0%B5%D0%BC-hoisting-%D0%B2-javascript-7d2d27bc51f1)

### - __Temporal dead zone__

letи constимеют два основных отличия от var:

Они имеют блочную область видимости .
Доступ к a varдо его объявления дает результат undefined; доступ к a letили constдо того, как он объявлен, бросает ReferenceError:
```javascript
console.log(aVar); // undefined
console.log(aLet); // causes ReferenceError: aLet is not defined
var aVar = 1;
let aLet = 2;
```
Из этих примеров видно, что letобъявления (и const, который работает таким же образом) не могут быть подняты , поскольку aLetони не существуют до того, как им будет присвоено значение.

Это не так, however- letи const которые поднимают (например var, classи function), но есть период между вводом и объем объявляется , где они не могут быть доступны. Этот период является временной мертвой зоной (TDZ) .

Протяжен заканчивается , когда aLetбудет объявлен , а не назначается :
```javascript
//console.log(aLet)  // would throw ReferenceError
let aLet;
console.log(aLet); // undefined
aLet = 10;
console.log(aLet); // 10
```
Этот пример показывает, что letподнимается:
```javascript
let x = 'outer value';
(function() {
  // start TDZ for x
  console.log(x);
  let x = 'inner value'; // declaration ends TDZ for x
}());
```
Доступ xво внутренней области по-прежнему вызывает ошибку ReferenceError. Если бы letне были подняты, он бы залез outer value.

TDZ - это хорошо, потому что он помогает выявить ошибки - доступ к значению до того, как оно было объявлено, редко бывает преднамеренным.

TDZ также применяется к аргументам функции по умолчанию. Аргументы оцениваются слева направо, и каждый аргумент находится в TDZ, пока не будет назначен:
```javascript
// b is in TDZ until its value is assigned
function testDefaults(a=b, b) { }
testDefaults(undefined, 1); // throws ReferenceError because the evaluation of a reads b before it has been evaluated.
```
TDZ по умолчанию отключен в транспиляторе babel.js. Включите режим «высокого соответствия», чтобы использовать его в REPL . Поставьте es6.spec.blockScopingфлаг, чтобы использовать его с CLI или как библиотеку.

Рекомендуемая дополнительная литература: демистификация TDZ и ES6 Let, Const и «Temporal Dead Zone» (TDZ) in Depth .

## __Objects Built-in methods.__

### - __Know static Object methods__

Статические методы, как и многие другие функции, представленные в ES6, предназначены для предоставления специфичных для класса методов для объектно-ориентированного программирования на Javascript. Статические методы в Javascript аналогичны методам классов в Ruby. Чтобы объявить статический метод, просто добавьте к объявлению метода слово static внутри объявления класса.

```javascript
class User {
  static staticMethod() {
    alert(this === User);
  }
}
User.staticMethod(); // true
```
Это фактически то же самое, что присвоить метод напрямую как свойство функции:
```javascript
class User { }

User.staticMethod = function() {
  alert(this === User);
}; 
```
Как описывает MDN: «Статические методы вызываются без создания экземпляра своего класса и также не могут быть вызваны при создании экземпляра класса. Статические методы часто используются для создания служебных функций для приложения ». Другими словами, статические методы не имеют доступа к данным, хранящимся в определенных объектах.

### - __Property flags & descriptors__

Помимо значения value, свойства объекта имеют три специальных атрибута (так называемые «флаги»).

writable – если true, свойство можно изменить, иначе оно только для чтения.
enumerable – если true, свойство перечисляется в циклах, в противном случае циклы его игнорируют.
configurable – если true, свойство можно удалить, а эти атрибуты можно изменять, иначе этого делать нельзя.
Мы ещё не встречали эти атрибуты, потому что обычно они скрыты. Когда мы создаём свойство «обычным способом», все они имеют значение true. Но мы можем изменить их в любое время.

Сначала посмотрим, как получить их текущие значения.

Метод Object.getOwnPropertyDescriptor позволяет получить полную информацию о свойстве.

Его синтаксис:
```javascript
let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);
```
obj
Объект, из которого мы получаем информацию.
propertyName
Имя свойства.
Возвращаемое значение – это объект, так называемый «дескриптор свойства»: он содержит значение свойства и все его флаги.

Например:
```javascript
let user = { };

Object.defineProperty(user, "name", {
  value: "John",
  // для нового свойства необходимо явно указывать все флаги, для которых значение true
  enumerable: true,
  configurable: true
});

alert(user.name); // John
user.name = "Pete"; // Ошибка
```

### - __Know how to create iterable objects, Symbol.iterator usage__

Перебираемые (или итерируемые) объекты – это концепция, которая позволяет использовать любой объект в цикле for..of.

Конечно же, сами массивы являются перебираемыми объектами. Но есть и много других встроенных перебираемых объектов, например, строки.

Если объект не является массивом, но представляет собой коллекцию каких-то элементов, то удобно использовать цикл for..of для их перебора, так что давайте посмотрим, как это сделать.

#### Symbol.iterator

Мы легко поймём принцип устройства перебираемых объектов, создав один из них.

Например, у нас есть объект. Это не массив, но он выглядит подходящим для for..of.

Например, объект range, который представляет собой диапазон чисел:

```javascript
let range = {
  from: 1,
  to: 5
};

// Мы хотим, чтобы работал for..of:
// for(let num of range) ... num=1,2,3,4,5
```
Чтобы сделать range итерируемым (и позволить for..of работать с ним), нам нужно добавить в объект метод с именем Symbol.iterator (специальный встроенный Symbol, созданный как раз для этого).

1. Когда цикл for..of запускается, он вызывает этот метод один раз (или выдаёт ошибку, если метод не найден). Этот метод должен вернуть итератор – объект с методом next.
2. Дальше for..of работает только с этим возвращённым объектом.
3. Когда for..of хочет получить следующее значение, он вызывает метод next() этого объекта.
4. Результат вызова next() должен иметь вид {done: Boolean, value: any}, где done=true означает, что итерация закончена, в противном случае value содержит очередное значение.

Вот полная реализация range с пояснениями:

```javascript
let range = {
  from: 1,
  to: 5
};

// 1. вызов for..of сначала вызывает эту функцию
range[Symbol.iterator] = function() {

  // ...она возвращает объект итератора:
  // 2. Далее, for..of работает только с этим итератором, запрашивая у него новые значения
  return {
    current: this.from,
    last: this.to,

    // 3. next() вызывается на каждой итерации цикла for..of
    next() {
      // 4. он должен вернуть значение в виде объекта {done:.., value :...}
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// теперь работает!
for (let num of range) {
  alert(num); // 1, затем 2, 3, 4, 5
}  
```

Обратите внимание на ключевую особенность итераторов: разделение ответственности.

- У самого range нет метода next().
- Вместо этого другой объект, так называемый «итератор», создаётся вызовом range[Symbol.iterator](), и именно его next() генерирует значения.

Таким образом, итератор отделён от самого итерируемого объекта.

## __Object as Hash__

### - __Be able to loop through Object keys__
Для простых объектов доступны следующие методы:

- Object.keys(obj) – возвращает массив ключей.
- Object.values(obj) – возвращает массив значений.
- Object.entries(obj) – возвращает массив пар [ключ, значение].

Обратите внимание на различия (по сравнению с map). Map возвращает	перебираемый объект, а Object - «реальный» массив.

Например:
```javascript
let user = {
  name: "John",
  age: 30
};
```
```javascript
Object.keys(user) = ["name", "age"]
Object.values(user) = ["John", 30]
Object.entries(user) = [ ["name","John"], ["age",30] ]
```
Вот пример использования Object.values ​​для перебора значений свойств в цикле:
```javascript
let user = {
  name: "John",
  age: 30
};

// перебор значений
for (let value of Object.values(user)) {
  alert(value); // John, затем 30
}
```
https://learn.javascript.ru/keys-values-entries

## __Arrays Built-in methods__

### - __Know how to copy array part__

#### concat
Метод arr.concat создаёт новый массив, в который копирует данные из других массивов и дополнительные значения.
Его синтаксис:
```javascript
arr.concat(arg1, arg2...)
```

В результате мы получаем новый массив, включающий в себя элементы из arr, а также arg1, arg2 и так далее…

Если аргумент argN – массив, то все его элементы копируются. Иначе скопируется сам аргумент.

Например:
```javascript
let arr = [1, 2];

// создать массив из: arr и [3,4]
alert( arr.concat([3, 4]) ); // 1,2,3,4

// создать массив из: arr и [3,4] и [5,6]
alert( arr.concat([3, 4], [5, 6]) ); // 1,2,3,4,5,6

// создать массив из: arr и [3,4], потом добавить значения 5 и 6
alert( arr.concat([3, 4], 5, 6) ); // 1,2,3,4,5,6
```

#### slice

Метод slice() возвращает новый массив, содержащий копию части исходного массива.
Его синтаксис:
```javascript
arr.slice([begin[, end]])
```
```javascript
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];

console.log(animals.slice(2));
// expected output: Array ["camel", "duck", "elephant"]

console.log(animals.slice(2, 4));
// expected output: Array ["camel", "duck"]

console.log(animals.slice(2, -1));
// expected output: Array ["camel", "duck"]
```

### - __Know how to flatten nested array__

Метод flat() возвращает новый массив, в котором все элементы вложенных подмассивов были рекурсивно "подняты" на указанный уровень depth.
```javascript
var newArray = arr.flat(depth);
```
depth Необязательный

  На сколько уровней вложенности уменьшается мерность исходного массива. По умолчанию 1.
```javascript
var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]
```

## __Arrays Iterating, Sorting, Filtering__

### - __Be able to custom sorting for Array__

#### sort

Метод sort() на месте сортирует элементы массива и возвращает отсортированный массив.
```javascript
arr.sort([compareFunction])
```
Если функция сравнения compareFunction не предоставляется, элементы сортируются путём преобразования их в строки и сравнения строк в порядке следования кодовых точек Unicode. Например, слово "Вишня" идёт перед словом "бананы". При числовой сортировке, 9 идёт перед 80, но поскольку числа преобразуются в строки, то "80" идёт перед "9" в соответствии с порядком в Unicode.
```javascript
var fruit = ['арбузы', 'бананы', 'Вишня'];
fruit.sort(); // ['Вишня', 'арбузы', 'бананы']

var scores = [1, 2, 10, 21];
scores.sort(); // [1, 10, 2, 21]

var things = ['слово', 'Слово', '1 Слово', '2 Слова'];
things.sort(); // ['1 Слово', '2 Слова', 'Слово', 'слово']
// В Unicode, числа находятся перед буквами в верхнем регистре,
// а те, в свою очередь, перед буквами в нижнем регистре.
```
Метод sort можно удобно использовать с функциональными выражениями (и замыканиями):
```javascript
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function(a, b) {
  return a - b;
});
console.log(numbers); // [1, 2, 3, 4, 5]
```
Объекты могут быть отсортированы по значению одного из своих свойств.
```javascript
var items = [
  { name: 'Edward', value: 21 },
  { name: 'Sharpe', value: 37 },
  { name: 'And', value: 45 },
  { name: 'The', value: -12 },
  { name: 'Magnetic' },
  { name: 'Zeros', value: 37 }
];
items.sort(function (a, b) {
  if (a.name > b.name) {
    return 1;
  }
  if (a.name < b.name) {
    return -1;
  }
  // a должно быть равным b
  return 0;
});
```

### - __Be able to filter Array elements__

#### filter 

Метод filter() создаёт новый массив со всеми элементами, прошедшими проверку, задаваемую в передаваемой функции.
```javascript
let newArray = arr.filter(callback(element[, index, [array]])[, thisArg])
```
Параметры

  - callback
  Функция, которая будет вызвана для каждого элемента массива. Если функция возвращает true, то элемент остаётся в массиве, если false, то удаляется.

    В функцию будет передано три аргумента:
    - element
      Текущий обрабатываемый элемент в массиве.
    - index Необязательный

      Индекс текущего обрабатываемого элемента в массиве.
    - array Необязательный

      Массив, по которому осуществляется проход.
  - thisArg Необязательный

    Значение, используемое в качестве this при вызове функции callback

Отфильтровывание всех маленьких значений
Следующий пример использует filter() для создания отфильтрованного массива, все элементы которого больше или равны 10, а все меньшие 10 удалены.
```javascript
function isBigEnough(value) {
  return value >= 10;
}

let filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// массив filtered равен [12, 130, 44]
```
Следующий пример возвращает все простые числа в массиве:
```javascript
let array = [4, 6, 8, 9, 12, 53, -17, 2, 5, 7, 31, 97, -1, 17];

function isPrime(num) {
    if (num <= 1)
        return false;
    else if (num === 2)
        return true;
    else {
        for (let i = 2; i < num; i++)
            if (num % i === 0)
                return false;
        return true;
    }
}

console.log(array.filter(isPrime));   // [53, 2, 5, 7, 31, 97, 17]
```

## __Functional Scope__

### - __Know global scope and functional scope__

#### _Глобальная область видимости или Global Scope_

В JavaScript есть только одна глобальная область. Область за пределами всех функций считается глобальной областью, и переменные, определенные в глобальной области, могут быть доступны и изменены в любых других областях.
```javascript
/ Глобальная область

var num = 5;
console.log(num);   // 5

function getNum(){
  console.log(num); // num доступен здесь
}

getNum();           // 5
```
#### _Локальная область видимости или Local Scope_
Переменные, объявленные внутри функций, становятся локальными для функции и рассматриваются в соответствующей локальной области. Каждая функция имеет свою область видимости. Одна и та же переменная может использоваться в разных функциях, поскольку они связаны с соответствующими функциями и не являются взаимно видимыми.
```javascript
// Глобальная область

function foo1(){
    // Локальная область 1
  function foo2(){
    // Локальная область 2
  }
}

// Глобальная область
function foo3(){
  // Локальная область 3
}
```
#### _Область видимости функции_
Локальная область видимости может быть разделена на область видимости функции и область видимости блока. Концепция область видимости блока или block scope была представлена в ECMAScript6 (ES6) вместе с новыми способами объявления переменных - const и let.
```javascript
function foo(){
  var num = 10;
  console.log('inside function: ', num);
}

foo();                   // inside function: 10
console.log(num);       // ReferenceError: num is not defined
```
#### _Область видимости блока_

Область видимости блока - это область в условиях if и switch или циклов for, и while. Вообще говоря, всякий раз, когда мы видим фигурные скобки {} - это блок. В ES6 ключевые слова const и let позволяют разработчикам объявлять переменные в области видимости блока, что означает, что эти переменные существуют только в соответствующем блоке.
```javascript
function foo(){
  if (true) {
    var num1 = 5;        // существуют в области видимости функции
    const num2 = 10;     // существуют в области видимости блока
    let num3 = 23;       // существуют в области видимости блока
  }
  console.log(num1);
  console.log(num2);
  console.log(num3);
}
```
#### _Лексическая область видимости_

Ещё один момент, о котором стоит упомянуть - это лексическая область. Лексическая область означает, что дочерняя область имеет доступ к переменным, определенным в родительской области. Дочерние функции лексически связаны с контекстом исполнения их родителей.
```javascript
function foo1(){
  var num1 = 5;
  const num2 = 10;
  let num3 = 23;
  function foo2(){
    console.log(num1);
    console.log(num2);
    console.log(num3);
  }
  foo2();
}

foo1();

// 5
// 10
// 23
```
#### _Динамическая область видимости_

Лексическая область видимости - это набор правил о том, как и где движок JavaScript может найти переменную. Ключевой характеристикой лексического контекста является то, что он определяется во время написания кода (при условии, что мы не используем eval () или with).

Динамическая область видимости, по понятным причинам, подразумевает, что существует модель, в которой область видимости может определяться динамически во время выполнения, а не статически во время создания. Например:
```javascript
function foo(){
  console.log(a); // 5
}

function bar(){
  var a = 10;
  foo();
}

var a = 5;

bar();
```
В лексической области видимости указывается, что ссылка на a в foo() будет преобразована в глобальную переменную a, что приведет к выводу значения 5.
https://frontend-stuff.com/blog/javascript-introduction-to-scope/

### - __Know variables visibility areas__

#### let, var, const
- _var_

Область видимости переменной, объявленной через var, это её текущий контекст выполнения. Который может ограничиваться функцией или быть глобальным, для переменных, объявленных за пределами функции.

Подробнее: https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/var

- _let_

Директива let позволяет объявить локальную переменную с областью видимости, ограниченной текущим блоком кода . В отличие от ключевого слова var, которое объявляет переменную глобально или локально во всей функции, независимо от области блока.

Подробнее: https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/let

- _const_

Значение констант не может быть изменено новым присваиванием, а также не может быть переопределено. Константы (const) подчиняются области видимости уровня блока, так же как переменные объявленные с использованием ключевого слова let.

Подробнее: https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/const

## __Functions Parameters / Arguments__

### - __Know how to define Function parameters__

Параметры функции по умолчанию позволяют инициализировать именованные параметры значениями по умолчанию, если не передается значение или значение undefined.
```javascript
function multiply(a, b = 1) {
  return a * b;
}

console.log(multiply(5, 2));
// expected output: 10

console.log(multiply(5));
// expected output: 5
```

### - __Know difference between parameters passing by value and by reference__

(Передача данных по ссылке и по значению) 
Примеры
- _Передача по значению_
```javascript
function change(x){
x = 2 * x;
console.log("x in change:", x);
}

let n = 10;
console.log("n before change:", n); // n before change: 10
change(n);                          // x in change: 20
console.log("n after change:", n);  // n after change: 10
```
Строки, числа, логические значения передаются в функцию по значению (передается их копия).
- _Передача по ссылке_
```javascript
function change(user){
user.name = "Tom";
}

var bob ={ 
name: "Bob"
};
console.log("before change:", bob.name);    // Bob
change(bob);
console.log("after change:", bob.name);     // Tom
```
Объекты и массивы передаются по ссылке. То есть функция получает сам объект или массив, а не их копию.
```javascript
function change(array){
array[0] = 8;
}
function changeFull(array){
array = [9, 8, 7];
}

var numbers = [1, 2, 3];

console.log("before change:", numbers);     // [1, 2, 3]
change(numbers);
console.log("after change:", numbers);      // [8, 2, 3]
changeFull(numbers);
console.log("after changeFull:", numbers);  // [8, 2, 3]
```

## __ECMAScript Intermediate__
### - __Know how to use spread operator for Function arguments__

Spread syntax позволяет расширить доступные для итерации элементы (например, массивы или строки) в местах
```javascript
function myFunction(x, y, z) { }
var args = [0, 1, 2];
myFunction(...args);
```
Копирование массива
```javascript
var arr = [1, 2, 3];
var arr2 = [...arr]; // like arr.slice()
arr2.push(4);

// arr2 becomes [1, 2, 3, 4]
// arr remains unaffected
```
Лучший способ конкатенации массивов
Для конкатенации массива часто используется Array.concat:
```javascript
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
// Append all items from arr2 onto arr1
arr1 = arr1.concat(arr2);
Copy to Clipboard
```
С использованием spread syntax:
```javascript
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1 = [...arr1, ...arr2];
```
### - __Rest parameters__
Синтаксис Rest parameters позволяет функции принимать неопределенное количество аргументов в виде массива, обеспечивая способ представления вариативных функций в JavaScript.
```javascript
function myFun(a,  b, ...manyMoreArgs) {
  console.log("a", a)
  console.log("b", b)
  console.log("manyMoreArgs", manyMoreArgs)
}

myFun("one", "two", "three", "four", "five", "six")

// Console Output:
// a, one
// b, two
// manyMoreArgs, ["three", "four", "five", "six"]
```
### - __String templates__

(Шаблонные строки)

Шаблонными литералами называются строковые литералы, допускающие использование выражений внутри. С ними вы можете использовать многострочные литералы и строковую интерполяцию. В спецификациях до ES2015 они назывались "шаблонными строками".

```javascript
`строка текста`

`строка текста 1
 строка текста 2`

`строка текста ${выражение} строка текста`

tag `строка текста ${выражение} строка текста`
```

### - __Know how `for..of` loop works__

Оператор for...of относится к типу оператора for, который циклически повторяет итерируемые объекты ( iterable objects)), пока не достигнет конца строки.

```javascript
let arr = [2,4,6,8,10]
for(let a of arr) {
    log(a)
}
// It logs:
// 2
// 4
// 6
// 8
// 10
```

## __Advanced Functions__

### - __`this` scope__

Для доступа к текущему объекту из метода используется ключевое слово this.
Значением this является объект перед «точкой», в контексте которого вызван метод, например:
```javascript
let user = {
name: 'Василий',
sayHi: function() {
  alert( this.name );
}
};

user.sayHi(); // sayHi в контексте user
```
Здесь при выполнении функции user.sayHi() в this будет храниться ссылка на текущий объект user.
Вместо this внутри sayHi можно было бы обратиться к объекту, используя переменную user:
```javascript
sayHi: function() {
  alert( user.name );
}
```
…Однако, такое решение нестабильно. Если мы решим скопировать объект в другую переменную, например admin = user, а в переменную user записать что-то другое – обращение будет совсем не по адресу:
```javascript
let user = {
name: 'Василий',
sayHi: function() {
  alert( user.name ); // приведёт к ошибке
}
};

let admin = user;
user = null;

admin.sayHi(); // упс! внутри sayHi обращение по старому имени, ошибка!
```
Использование this гарантирует, что функция работает именно с тем объектом, в контексте которого вызвана.
Через this метод может не только обратиться к любому свойству объекта, но и передать куда-то ссылку на сам объект целиком:
```javascript
let user = {
name: 'Василий',
sayHi: function() {
  showName(this); // передать текущий объект в showName
}
};

function showName(namedObj) {
alert( namedObj.name );
}
user.sayHi(); // Василий
```
Если одну и ту же функцию запускать в контексте разных объектов, она будет получать разный this:
```javascript
let user = { firstName: "Вася" };
let admin = { firstName: "Админ" };

function func() {
alert( this.firstName );
}

user.f = func;
admin.g = func;

// this равен объекту перед точкой:
user.f(); // Вася
admin.g(); // Админ
admin['g'](); // Админ (не важно, доступ к объекту через точку или квадратные скобки)
```
Значение this, при вызове без контекста, получает значение глобального объекта window:

```javascript
function func() {
alert( this ); // выведет [object Window] или [object global]
}

func();
```
Таково поведение в старом стандарте.
А в режиме use strict вместо глобального объекта this будет undefined:
```javascript
function func() {
"use strict";
alert( this ); // выведет undefined (кроме IE9-)
}

func();
```
Обычно если в функции используется this, то она, всё же, служит для вызова в контексте объекта, так что такая ситуация – скорее исключение.

Подробнее:
http://learn.javascript.ru/object-methods
https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/this
https://github.com/azat-io/you-dont-know-js-ru/tree/master/this%20%26%20object%20prototypes

### - __Understand difference between function and method__

Функция - это часть кода, которая называется по имени. Он может передавать данные для работы (т. е. параметры) и при необходимости возвращать данные (возвращаемое значение). Все данные, которые передаются в функцию, передаются явно.

Метод - это фрагмент кода, который вызывается именем, связанным с объектом. Во многих отношениях он идентичен функции за исключением двух ключевых отличий:

  1. Метод неявно передает объект, на котором он был вызван.
  2. Метод способен работать с данными, содержащимися в классе (помня, что объект является экземпляром класса-класс является определением, объект является экземпляром этих данных).

### - __Apply, call, bind__
- _call_

Метод call() вызывает функцию с указанным значением this и индивидуально предоставленными аргументами. Вы можете присваивать различные объекты this при вызове существующей функции. this ссылается на текущий объект, вызвавший объект. С помощью call вы можете написать метод один раз, а затем наследовать его в других объектах, без необходимости переписывать метод для каждого нового объекта.

```javascript
function showFullName() {
alert( this.firstName + " " + this.lastName );
}

const user = {
firstName: "Василий",
lastName: "Петров"
};

// функция вызовется с this=user
showFullName.call(user) // "Василий Петров"
```
- _apply_

Метод apply() вызывает функцию с указанным значением this и аргументами, предоставленными в виде массива (либо массивоподобного объекта). Вы можете присваивать различные объекты this при вызове существующей функции. this ссылается на текущий объект, вызывающий объект. С помощью apply() вы можете написать метод один раз, а затем наследовать его в других объектах без необходимости переписывать метод для каждого нового объекта.

```javascript
//эти две строчки сработают одинаково:
showFullName.call(user, 'firstName', 'surname');
showFullName.apply(user, ['firstName', 'surname']);
```
```javascript
var arr = [];
arr.push(1);
arr.push(5);
arr.push(2);

// получить максимум из элементов arr
alert( Math.max.apply(null, arr) ); // 5
```
Преимущество apply() перед call() отчётливо видно, когда мы формируем массив аргументов динамически.
- _bind_

Метод bind() создаёт новую функцию, которая при вызове устанавливает в качестве контекста выполнения this предоставленное значение. В метод также передаётся набор аргументов, которые будут установлены перед переданными в привязанную функцию аргументами при её вызове.

```javascript
// Пример потери контекста
var user = {
firstName: "Вася",
sayHi: function() {
  alert( this.firstName );
}
};

setTimeout(user.sayHi, 1000); // undefined (не Вася!)
```
```javascript
// привязка контекста
var user = {
firstName: "Вася",
sayHi: function() {
  alert( this.firstName );
}
};

setTimeout(user.sayHi.bind(user), 1000); // Вася
```
Вызов bind часто используют для привязки функции к контексту, чтобы затем присвоить её в обычную переменную и вызывать уже без явного указания объекта.
- фундаментальное различие между этими методами заключается в том, что функция call() принимает список аргументов, в то время, как функция apply() - одиночный массив аргументов. Методы call/apply вызывают функцию с заданным контекстом и аргументами. А bind не вызывает функцию. Он только возвращает «обёртку», которую мы можем вызвать позже, и которая передаст вызов в исходную функцию, с привязанным контекстом.

Подробнее:

https://vc.ru/dev/133379-5-otlichiy-mezhdu-obychnymi-i-strelochnymi-funkciyami

https://learn.javascript.ru/call-apply

https://learn.javascript.ru/bind

## __Functional Patterns__

### - __Immediately invoked functional expression `(IIFE)`__

Немедленно вызываемая функция (Immediately Invoked Function Expression — IIFE) в JavaScript — это конструкция, позволяющая вызывать функцию непосредственно после ее определения.
```javascript
(function() {
  /* */
})()

(() => {
  /* */
})()
```

### - __Know IIFE pattern__

Управлять областью видимости переменных в JavaScript можно, пользуясь паттерном «Модуль». Для того чтобы создать приватную область видимости, можно воспользоваться замыканием. Как известно, функции создают собственные области видимости, содержимое которых отделено от глобальной области видимости.

```javascript
var Module = (function () {
  var myModule = {};
  var privateMethod = function () {

  };
  myModule.publicMethod = function () {

  };
  myModule.anotherPublicMethod = function () {

  };
  return myModule; // возвращает объект с общедоступными методами
})();
```
// использование модуля
Module.publicMethod();

### - __Callback (Function as argument)__

Функция обратного вызова - это функция, которая передается в качестве аргумента другой функции для «обратного вызова» позже. Функция, которая принимает другие функции в качестве аргументов, называется функцией высшего порядка, которая содержит логику выполнения функции обратного вызова. Комбинация этих двух факторов позволяет нам расширять нашу функциональность.
```javascript
function createQuote(quote, callback){ 
  var myQuote = "Like I always say, " + quote;
  callback(myQuote); // 2
}

function logQuote(quote){
  console.log(quote);
}

createQuote("eat your vegetables!", logQuote); // 1

// Result in console: 
// Like I always say, eat your vegetables!
```

### - __Callback Hell__ 

Это паттерн для управления конкурирующими (асинхронными) запросами, который обеспечивает последовательность их выполнения.

Асинхронная функция в паттерне Callback Hell не может возвращать значение, так как такие функции выполняются с задержкой и позже кода, который за ними последует. Вместо этого они принимают коллбек, который будет запущен, когда функция выполнит свою работу. Как правило таких коллбеков два - один для случая успешного выполнения, а второй для обработки возникших ошибок. 
```javascript
function onGetUserInfo(id, handler) {
    setTimeout(() => {
        const userData = {
            id: 1,
            name: "test"
        }
        handler(userData);
    },
    100);
}

function onGetPermissions(userData, handler) {
    setTimeout(() => {
        const permissions = {
            write: "y",
            read: "y"
        }
        handler(permissions, userData);
    },
    50);
}

function makePost(permissions, userData) {
    if (permissions.write === "y") {
        console.log(`Пользователь ${userData.name} написал пост`);
    }
}


// реализация паттерна Callback Hell
onGetUserInfo(1, (userData) => {
    onGetPermissions(userData, (permissions, userData) => {
        makePost(permissions, userData); // будет выведено "Пользователь test написал пост"
    });
});
```

### - __Carrying and partial functions__

Каррирование – это трансформация функций таким образом, чтобы они принимали аргументы не как f(a, b, c), а как f(a)(b)(c).

Каррирование не вызывает функцию. Оно просто трансформирует её.

## __Network requests__

### - __`Fetch` (with usage)__
Базовый синтаксис:
```javascript
let promise = fetch(url, [options])
```
  - url – URL для отправки запроса.
  - options – дополнительные параметры: метод, заголовки и так далее.

Без options это простой GET-запрос, скачивающий содержимое по адресу url.

Браузер сразу же начинает запрос и возвращает промис, который внешний код использует для получения результата.

Процесс получения ответа обычно происходит в два этапа.

Процесс получения ответа обычно происходит в два этапа.

Во-первых, promise выполняется с объектом встроенного класса Response в качестве результата, как только сервер пришлёт заголовки ответа.

На этом этапе мы можем проверить статус HTTP-запроса и определить, выполнился ли он успешно, а также посмотреть заголовки, но пока без тела ответа.

Промис завершается с ошибкой, если fetch не смог выполнить HTTP-запрос, например при ошибке сети или если нет такого сайта. HTTP-статусы 404 и 500 не являются ошибкой.

Мы можем увидеть HTTP-статус в свойствах ответа:

status – код статуса HTTP-запроса, например 200.
ok – логическое значение: будет true, если код HTTP-статуса в диапазоне 200-299.
Например:
```javascript
let response = await fetch(url);

if (response.ok) { // если HTTP-статус в диапазоне 200-299
  // получаем тело ответа (см. про этот метод ниже)
  let json = await response.json();
} else {
  alert("Ошибка HTTP: " + response.status);
}
```
Во-вторых, для получения тела ответа нам нужно использовать дополнительный вызов метода:

  - response.text()/.json()/.formData()/.blob()/.arrayBuffer()

Например, получим JSON-объект с последними коммитами из репозитория на GitHub:
```javascript
let url = 'https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits';
let response = await fetch(url);

let commits = await response.json(); // читаем ответ в формате JSON

alert(commits[0].author.login);
```
То же самое без await, с использованием промисов:
```javascript
fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits')
  .then(response => response.json())
  .then(commits => alert(commits[0].author.login));
```

### - __`XMLHTTPRequest` (concept)__

XMLHttpRequest – это встроенный в браузер объект, который даёт возможность делать HTTP-запросы к серверу без перезагрузки страницы.

В современной веб-разработке XMLHttpRequest используется по трём причинам:

  1. По историческим причинам: существует много кода, использующего XMLHttpRequest, который нужно поддерживать.
  2. Необходимость поддерживать старые браузеры и нежелание использовать полифилы (например, чтобы уменьшить количество кода).
  3. Потребность в функциональности, которую fetch пока что не может предоставить, к примеру, отслеживание прогресса отправки на сервер.

https://learn.javascript.ru/xmlhttprequest

### - __`WebSocket` (concept)__

Протокол WebSocket («веб-сокет»), описанный в спецификации RFC 6455, обеспечивает возможность обмена данными между браузером и сервером через постоянное соединение. Данные передаются по нему в обоих направлениях в виде «пакетов», без разрыва соединения и дополнительных HTTP-запросов.

WebSocket особенно хорош для сервисов, которые нуждаются в постоянном обмене данными, например онлайн игры, торговые площадки, работающие в реальном времени, и т.д.

https://learn.javascript.ru/websocket

## __Web components__

### - __Web components, shadow DOM (concept)__
Web components

Как мы определяем, что является компонентом? Это приходит из соображений здравого смысла, а также с интуицией и опытом. Обычно это объект, отделимый визуально, который мы можем описать с точки зрения того, что он делает и как он взаимодействует со страницей. В примере выше, страница содержит блоки, каждый из которых играет свою роль, и логично выделить их в компоненты.

Компонент имеет:

  - свой собственный JavaScript-класс.
  - DOM-структура управляется исключительно своим классом, и внешний код не имеет к ней доступа (принцип «инкапсуляции»).
  - CSS-стили, применённые к компоненту.
  - API: события, методы класса и т.п., для взаимодействия с другими компонентами.

Shadow DOM

Теневой DOM («Shadow DOM») используется для инкапсуляции. Благодаря ему в компоненте есть собственное «теневое» DOM-дерево, к которому нельзя просто так обратиться из главного документа, у него могут быть изолированные CSS-правила и т.д.

Например, ```<input type="range">:```

<input type="range">

Браузер рисует их своими силами и по своему усмотрению. Их DOM-структура обычно нам не видна, но в инструментах разработчика можно её посмотреть. К примеру, в Chrome для этого нужно активировать пункт «Show user agent shadow DOM».

То, что находится под #shadow-root – и называется «shadow DOM» (теневой DOM).

https://learn.javascript.ru/shadow-dom

## __Date & time__

### - __Timezones__

### - __Internationalization js (Intl)__

## __Closures Advanced__(замыкание)

Замыкание — это комбинация функции и лексического окружения, в котором эта функция была определена.
```javascript
function makeFunc() {
let name = "Mozilla";
function displayName() {
  alert(name);
}
return displayName;
};

let myFunc = makeFunc();
myFunc(); //Mozilla
```
Подробнее:

https://developer.mozilla.org/ru/docs/Web/JavaScript/Closures

http://learn.javascript.ru/closures

https://github.com/azat-io/you-dont-know-js-ru/tree/master/scope%20%26%20closures

## __Object Oriented Programming__

### - __`new` keyword__

Оператор (операторная функция) new создаёт экземпляр объекта, встроенного или определённого пользователем, имеющего конструктор.
```javascript
new constructor[([arguments])]
```
  - constructor

    Функция, задающая тип объекта.
  - arguments

    Список параметров, с которыми будет вызван конструктор.

Создание объекта, определённого пользователем, требует два шага:

Написать функцию, которая задаст тип объекта.
Создать экземпляр объекта, используя  new.
Чтобы определить новый тип объекта, создайте функцию, которая задаст его и имя и свойства. Свойство объекта также может быть объектом. Примеры приведены ниже.

Когда исполняется new Foo(...) , происходит следующее:

  1. Создаётся новый объект, наследующий Foo.prototype.
  2. Вызывается конструктор — функция Foo с указанными аргументами и this, привязанным к только что созданному объекту. new Foo эквивалентно new Foo(), то есть если аргументы не указаны, Foo вызывается без аргументов.
  3. Результатом выражения new становится объект, возвращённый конструктором. Если конструктор не возвращает объект явно, используется объект из п. 1. (Обычно конструкторы не возвращают значение, но они могут делать это, если нужно переопределить обычный процесс создания объектов.)

```javascript
function Car() {}
car1 = new Car();

console.log(car1.color);    // undefined

Car.prototype.color = null;
console.log(car1.color);    // null

car1.color = "black";
console.log(car1.color);   // black
```

### - __Function constructor__

Функции-конструкторы являются обычными функциями. Но есть два соглашения:

  1. Имя функции-конструктора должно начинаться с большой буквы.
  2. Функция-конструктор должна вызываться при помощи оператора "new".

Например:
```javascript
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User("Вася");

alert(user.name); // Вася
alert(user.isAdmin); // false
```

Когда функция вызывается как new User(...), происходит следующее:

  1. Создаётся новый пустой объект, и он присваивается this.
  2. Выполняется код функции. Обычно он модифицирует this, добавляет туда новые свойства.
  3. Возвращается значение this.

## __Prototypal Inheritance Basics__

### - __`__proto__`, `prototype` property__

Объекты в JavaScript можно организовать в цепочки так, чтобы свойство, не найденное в одном объекте, автоматически искалось бы в другом.
Связующим звеном выступает специальное свойство __proto__.
Если один объект имеет специальную ссылку __proto__ на другой объект, то при чтении свойства из него, если свойство отсутствует в самом объекте, оно ищется в объекте __proto__.
```javascript
var animal = {
eats: true
};
var rabbit = {
jumps: true
};

rabbit.__proto__ = animal;

// в rabbit можно найти оба свойства
alert( rabbit.jumps ); // true
alert( rabbit.eats ); // true
```
Объект, на который указывает ссылка __proto__, называется «прототипом». В данном случае получилось, что animal является прототипом для rabbit.
У объекта, который является __proto__, может быть свой __proto__, у того – свой, и так далее. При этом свойства будут искаться по цепочке.
__proto__ не работает в IE10.
К счастью, в JavaScript с древнейших времён существует альтернативный, встроенный в язык и полностью кросс-браузерный способ.
Чтобы новым объектам автоматически ставить прототип, конструктору ставится свойство prototype.
При создании объекта через new, в его прототип __proto__ записывается ссылка из prototype функции-конструктора.

Например, код ниже полностью аналогичен предыдущему, но работает всегда и везде:
```javascript
let animal = {
eats: true
};

function Rabbit(name) {
this.name = name;
}

Rabbit.prototype = animal;

let rabbit = new Rabbit("Кроль"); //  rabbit.__proto__ == animal

alert( rabbit.eats ); // true
```
Установка Rabbit.prototype = animal буквально говорит интерпретатору следующее: "При создании объекта через new Rabbit запиши ему __proto__ = animal".
Свойство prototype имеет смысл только у конструктора
Свойство с именем prototype можно указать на любом объекте, но особый смысл оно имеет, лишь если назначено функции-конструктору.
Само по себе, без вызова оператора new, оно вообще ничего не делает, его единственное назначение – указывать __proto__ для новых объектов.

Пример наследования:
```javascript
// 1. Конструктор Animal
function Animal(name) {
this.name = name;
this.speed = 0;
}

// 1.1. Методы -- в прототип

Animal.prototype.stop = function() {
this.speed = 0;
alert( this.name + ' стоит' );
}

Animal.prototype.run = function(speed) {
this.speed += speed;
alert( this.name + ' бежит, скорость ' + this.speed );
};

// 2. Конструктор Rabbit
function Rabbit(name) {
this.name = name;
this.speed = 0;1
}

// 2.1. Наследование
Rabbit.prototype = Object.create(Animal.prototype);
Rabbit.prototype.constructor = Rabbit;

// 2.2. Методы Rabbit
Rabbit.prototype.jump = function() {
this.speed++;
alert( this.name + ' прыгает, скорость ' + this.speed );
}
```

Подробнее:

http://learn.javascript.ru/class-inheritance

http://learn.javascript.ru/prototype

http://learn.javascript.ru/new-prototype

### - __Как создать объект без `prototype`?__

```javascript
const data = Object.create(null);
data.text = "Привет";

alert(data.text); // Привет
alert(data.toString); // undefined
```

Объект, создаваемый при помощи Object.create(null) не имеет прототипа, а значит в нём нет лишних свойств. 

Подробнее: 

https://learn.javascript.ru/prototype#object-create-null

## __ECMAScript Classes__

### - __Understand difference between `class` and `constructor function`__

На самом деле классы — это "специальные функции", поэтому точно также, как вы определяете функции, вы можете определять и классы. В JavaScript есть отдельные термины для таких объявлений:

  - [class declaration](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class)
  - [function declaration](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/function)

Разница между объявлением функции (function declaration) и объявлением класса (class declaration) в том, что объявление функции совершает подъём (hoisted), в то время как объявление класса — нет. Поэтому вначале необходимо объявить ваш класс и только затем работать с ним.

### - __Getter/setter__

Cвойства-аксессоры (accessor properties). По своей сути это функции, которые используются для присвоения и получения значения, но во внешнем коде они выглядят как обычные свойства объекта.

Свойства-аксессоры представлены методами: «геттер» – для чтения и «сеттер» – для записи. При литеральном объявлении объекта они обозначаются get и set:
```javascript 
let user = {
  name: "John",
  surname: "Smith",

  get fullName() {
    return `${this.name} ${this.surname}`;
  },

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  }
};

// set fullName запустится с данным значением
user.fullName = "Alice Cooper";

alert(user.name); // Alice
alert(user.surname); // Cooper
```

[Геттеры и сеттеры...](https://learn.javascript.ru/property-accessors)

### - __What does `super()` do and where we have to use it?__

Ключевое слово super используется для вызова функций, принадлежащих родителю объекта.
```javascript
class Rectangle {
  constructor(height, width) {
    this.name = 'Rectangle';
    this.height = height;
    this.width = width;
  }
}

class Square extends Rectangle {
  constructor(length) {
    super(length, length)
    this.name = 'Square';
  }
```

## __ECMAScript Data Types & Expressions__

### - __Object `keys/values`__

Для простых объектов доступны следующие методы:

  - Object.keys(obj) – возвращает массив ключей.
  - Object.values(obj) – возвращает массив значений.
  - Object.entries(obj) – возвращает массив пар [ключ, значение].

[more...](https://learn.javascript.ru/keys-values-entries)

### - __`Set/Map` data types__

Map – это коллекция ключ/значение, как и Object. Но основное отличие в том, что Map позволяет использовать любые типы данных для ключей.

Методы и свойства:

  - new Map() – создаёт коллекцию.
  - map.set(key, value) – записывает по ключу key значение value.
  - map.get(key) – возвращает значение по ключу или undefined, если ключ key отсутствует.
  - map.has(key) – возвращает true, если ключ key присутствует в коллекции, иначе false.
  - map.delete(key) – удаляет элемент по ключу key.
  - map.clear() – очищает коллекцию от всех элементов.
  - map.size – возвращает текущее количество элементов.
```javascript
let map = new Map();

map.set("1", "str1");    // строка в качестве ключа
map.set(1, "num1");      // цифра как ключ
map.set(true, "bool1");  // булево значение как ключ

// помните, обычный объект Object приводит ключи к строкам?
// Map сохраняет тип ключей, так что в этом случае сохранится 2 разных значения:
alert(map.get(1)); // "num1"
alert(map.get("1")); // "str1"

alert(map.size); // 3
```
Map может использовать объекты в качестве ключей.

Объект Set – это особый вид коллекции: «множество» значений (без ключей), где каждое значение может появляться только один раз.

Его основные методы это:

  - new Set(iterable) – создаёт Set, и если в качестве аргумента был предоставлен итерируемый объект (обычно это массив), то копирует его значения в новый Set.
  - set.add(value) – добавляет значение (если оно уже есть, то ничего не делает), возвращает тот же объект set.
  - set.delete(value) – удаляет значение, возвращает true, если value было в множестве на момент вызова, иначе false.
  - set.has(value) – возвращает true, если значение присутствует в множестве, иначе false.
  - set.clear() – удаляет все имеющиеся значения.
  - set.size – возвращает количество элементов в множестве.

Основная «изюминка» – это то, что при повторных вызовах set.add() с одним и тем же значением ничего не происходит, за счёт этого как раз и получается, что каждое значение появляется один раз.

[more...](https://learn.javascript.ru/map-set)

### - __`WeakSet/WeakMap` data types__

Первое его отличие от Map в том, что ключи в WeakMap должны быть объектами, а не примитивными значениями:
```javascript
let weakMap = new WeakMap();

let obj = {};

weakMap.set(obj, "ok"); // работает (объект в качестве ключа)

// нельзя использовать строку в качестве ключа
weakMap.set("test", "Whoops"); // Ошибка, потому что "test" не объект
```
Теперь, если мы используем объект в качестве ключа и если больше нет ссылок на этот объект, то он будет удалён из памяти (и из объекта WeakMap) автоматически.
```javascript
let john = { name: "John" };

let weakMap = new WeakMap();
weakMap.set(john, "...");

john = null; // перезаписываем ссылку на объект

// объект john удалён из памяти!
```
WeakMap не поддерживает перебор и методы keys(), values(), entries(), так что нет способа взять все ключи или значения из неё.

В WeakMap присутствуют только следующие методы:

  - weakMap.get(key)
  - weakMap.set(key, value)
  - weakMap.delete(key)
  - weakMap.has(key)

Коллекция WeakSet ведёт себя похоже:

  - Она аналогична Set, но мы можем добавлять в WeakSet только объекты (не примитивные значения).
  - Объект присутствует в множестве только до тех пор, пока доступен где-то ещё.
  - Как и Set, она поддерживает add, has и delete, но не size, keys() и не является перебираемой.

Итого

WeakMap – это Map-подобная коллекция, позволяющая использовать в качестве ключей только объекты, и автоматически удаляющая их вместе с соответствующими значениями, как только они становятся недостижимыми иными путями.

WeakSet – это Set-подобная коллекция, которая хранит только объекты и удаляет их, как только они становятся недостижимыми иными путями.

Обе этих структуры данных не поддерживают методы и свойства, работающие со всем содержимым сразу или возвращающие информацию о размере коллекции. Возможны только операции на отдельном элементе коллекции.

WeakMap и WeakSet используются как вспомогательные структуры данных в дополнение к «основному» месту хранения объекта. Если объект удаляется из основного хранилища и нигде не используется, кроме как в качестве ключа в WeakMap или в WeakSet, то он будет удалён автоматически.

## __JavaScript Errors__

### - __`try..catch` statement__

Работает она так:

  1. Сначала выполняется код внутри блока try {...}.
  2. Если в нём нет ошибок, то блок catch(err) игнорируется: выполнение доходит до конца try и потом далее, полностью пропуская catch.
  3. Если же в нём возникает ошибка, то выполнение try прерывается, и поток управления переходит в начало catch(err). Переменная err (можно использовать любое имя) содержит объект ошибки с подробной информацией о произошедшем.

Таким образом, при ошибке в блоке try {…} скрипт не «падает», и мы получаем возможность обработать ошибку внутри catch.

[more...](https://learn.javascript.ru/try-catch)

### - __Custom errors__

[more...](https://learn.javascript.ru/custom-errors)

## __ECMAScript Advanced__

### - __Garbage collector (concept)__

Основной концепцией управления памятью в JavaScript является принцип достижимости.

Если упростить, то «достижимые» значения – это те, которые доступны или используются. Они гарантированно находятся в памяти.

  1. Существует базовое множество достижимых значений, которые не могут быть удалены.

    Например:

      - Локальные переменные и параметры текущей функции.
      - Переменные и параметры других функций в текущей цепочке вложенных вызовов.
      - Глобальные переменные.
      - (некоторые другие внутренние значения)
      Эти значения мы будем называть корнями.

  2. Любое другое значение считается достижимым, если оно доступно из корня по ссылке или по цепочке ссылок.

    Например, если в локальной переменной есть объект, и он имеет свойство, в котором хранится ссылка на другой объект, то этот объект считается достижимым. И те, на которые он ссылается, тоже достижимы. Далее вы познакомитесь с подробными примерами на эту тему.

В интерпретаторе JavaScript есть фоновый процесс, который называется сборщик мусора. Он следит за всеми объектами и удаляет те, которые стали недостижимы.

[more...](https://learn.javascript.ru/garbage-collection)

### - __Promise__

Для того чтобы поиграться с запросами, можно использовать открытый API http://jsonplaceholder.typicode.com/

Объект Promise (обещание) используется для отложенных и асинхронных вычислений. Promise может находиться в трёх состояниях:

- ожидание (pending): начальное состояние, не выполнено и не отклонено.
- выполнено (fulfilled): операция завершена успешно.
- отклонено (rejected): операция завершена с ошибкой.

```javascript
new Promise(executor);
new Promise(function(resolve, reject) { ... });
```

[learn.javascript.ru...](https://learn.javascript.ru/promise-basics)
[mozilla...](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Promise)


#### 1. Типы данных в JS. Приведение типов
Все типы данных в JavaScript, кроме объектов, являются иммутабельными (значения не могут быть модифицированы, а только перезаписаны новым полным значением). Например, в отличии от C, где строку можно посимвольно корректировать, в JavaScript строки пересоздаются только полностью. Значения таких типов называются «примитивными значениями».

- _Булевый тип данных_

Булевый тип представляет собой результат логического выражения и принимает одно из двух значений: true (истина) или false (ложь).
```javascript
let checked = true;
checked = false;    
```
- _Null_

Этот тип данных имеет всего одно значение: null. Смотрите null и Null для получения подробностей.
```javascript
let age = null;
```
- _Undefined_

Переменная, значение которой не было присвоено, имеет значение undefined. Смотрите undefined и undefined для получения подробностей.
```javascript
let x = 123;
x = undefined;
alert( x ); // "undefined"
```
- _Числа_

В соответствии со стандартом ECMAScript, существует только один числовой тип, который представляет собой 64-битное число двойной точности согласно стандарту IEEE 754. 
Другими словами, специального типа для целых чисел в JavaScript нет. 
Это означает, что при числовых операциях вы можете получить неточное (округлённое) значение. 
В дополнение к возможности представлять числа с плавающей запятой, есть несколько символических значений: +Infinity (положительная бесконечность), -Infinity (отрицательная бесконечность), и NaN (не число).
Для получения самого большого или самого меньшего доступного значения в пределах +/-Infinity, можно использовать константы Number.MAX_VALUE или Number.MIN_VALUE. 
А начиная с ECMAScript 2015, вы также можете проверить, находится ли число в безопасном для целых чисел диапазоне, используя метод Number.isSafeInteger(), либо константы Number.MAX_SAFE_INTEGER и Number.MIN_SAFE_INTEGER. 
За пределами этого диапазона операции с целыми числами будут небезопасными, и возвращать приближённые значения.
Ноль в JavaScript имеет два представления: -0 и +0. («0» это синоним +0). На практике это имеет малозаметный эффект. Например, выражение +0 === -0 является истинным. Однако, это может проявиться при делении на ноль:
> 42 / +0
Infinity
> 42 / -0
-Infinity
```javascript
let n = 123;
n = 12.345;
```

Подробнее: https://learn.javascript.ru/number

- _Текстовые строки_

В JavaScript для представления текстовых данных служит тип String. 
Он представляет собой цепочку «элементов» 16-битных беззнаковых целочисленных значений. 
Каждый такой элемент занимает свою позицию в строке. Первый элемент имеет индекс 0, следующий — 1, и так далее. 
Длина строки — это количество элементов в ней.
В отличие от языков подобных C, строки в JavaScript являются иммутабельными. 
Это означает, что после того, как строковое значение создано, его нельзя модифицировать.
Остаётся лишь создать новую строку путём совершения некой операции над исходной строкой. 
```javascript
let str = "Мама мыла раму";
str = 'Одинарные кавычки тоже подойдут';
```

Подробнее: https://learn.javascript.ru/es-string

- _Тип данных Символ (Symbol)_

Символы являются нововведением JavaScript начиная с ECMAScript 2015. 
Символ — это уникальное и иммутабельное примитивное значение, которое может быть использовано как ключ для свойства объекта. 
В некоторых языках программирования символы называются атомами. Их также можно сравнить с именованными значениями перечисления (enum) в языке C. 

Подробнее: https://learn.javascript.ru/symbol#obyavlenie

- _Объекты_

В JavaScript объект может расцениваться как набор свойств. 
Литеральная инициализация объекта задаёт определённое количество начальных свойств, и в процессе работы приложения поля могут добавляться и удаляться. 
Значения свойств могут иметь любой тип, включая другие объекты, что позволяет строить сложные, разветвлённые иерархии данных. 
Каждое свойство объекта идентифицируется ключом, в качестве которого может выступать значение с типом Строка или Символ.
Создание объекта:
```javascript
1. let a = new Object();
2. let b = {}; // пустые фигурные скобки
```

Подробнее: https://learn.javascript.ru/object

Приведение типов:

##### Строковое преобразование.
Строковое преобразование происходит, когда требуется представление чего-либо в виде строки. Например, его производит функция alert.
```javascript
let a = true;
alert( a ); // "true"
```
Можно также осуществить преобразование явным вызовом String(val):
```javascript
alert( String(null) === "null" ); // true
```
Как видно из примеров выше, преобразование происходит наиболее очевидным способом, «как есть»: false становится "false", null – "null", undefined – "undefined" и т.п.
Также для явного преобразования применяется оператор "+", у которого один из аргументов строка. В этом случае он приводит к строке и другой аргумент, например:
```javascript
alert( true + "test" ); // "truetest"
alert( "123" + undefined ); // "123undefined"
```
##### Численное преобразование.
Численное преобразование происходит в математических функциях и выражениях, а также при сравнении данных различных типов (кроме сравнений ===, !==).
Для преобразования к числу в явном виде можно вызвать Number(val), либо, что короче, поставить перед выражением унарный плюс "+":
```javascript
let a = +"123"; // 123
let a = Number("123"); // 123, тот же эффект
```
##### Логическое преобразование.
Преобразование к true/false происходит в логическом контексте, таком как if(value), и при применении логических операторов.
Все значения, которые интуитивно «пусты», становятся false. Их несколько: 0, пустая строка, null, undefined и NaN.
Остальное, в том числе и любые объекты – true.

Подробнее: https://learn.javascript.ru/types-conversion


#### 5. {a: 10} == {a: 10}. Что вернет код?
```javascript
{a: 10} == {a: 10} //false
```
т.к. сравнивается не содержимое объектов, а их ссылки.
Дя того, чтобы сравнить 2 объекта, существует несколько способов, наиболее простой из которых:
```javascript
function deepEqual (obj1, obj2){
  return JSON.stringify(obj1)===JSON.stringify(obj2);
}
```

#### 9. Sum(1)(2)

1 способ

```javascript
function sum (a) {
return function (b) {
  return a + b;
}
};

sum(2)(3) //5

// раблотаеет только с 2 скобками
```

2 способ

```javascript
function sum(a) {

var currentSum = a;

function f(b) {
  currentSum += b;
  return f;
}

f.toString = function() {
  return currentSum;
};

return f;
}

sum(1)(2); // 3
sum(5)(-1)(2); // 6
```

Подробнее:

https://learn.javascript.ru/task/sum-many-brackets

#### 12. Методы массива, перебирающие элементы массива
- _forEach_
Метод «Array.prototype.forEach(callback[, thisArg])» используется для перебора массива.
Он для каждого элемента массива вызывает функцию callback.
Этой функции он передаёт три параметра callback(item, i, arr):

item – очередной элемент массива.

i – его номер.

arr – массив, который перебирается.

Например:
```javascript
let arr = ["Яблоко", "Апельсин", "Груша"];

arr.forEach(function(item, i, arr) {
alert( i + ": " + item + " (массив:" + arr + ")" );
});
```
Второй, необязательный аргумент forEach позволяет указать контекст this для callback.
Метод forEach ничего не возвращает, его используют только для перебора, как более «элегантный» вариант, чем обычный цикл for.

- _filter_
Метод «Array.prototype.filter(callback[, thisArg])» используется для фильтрации массива через функцию.
Он создаёт новый массив, в который войдут только те элементы arr, для которых вызов callback(item, i, arr) возвратит true.

Например:
```javascript
let arr = [1, -1, 2, -2, 3];

let positiveArr = arr.filter(function(number) {
return number > 0;
});

alert( positiveArr ); // 1,2,3
```

- _map_
Метод «Array.prototype.map(callback[, thisArg])» используется для трансформации массива.
Он создаёт новый массив, который будет состоять из результатов вызова callback(item, i, arr) для каждого элемента arr.

Например:
```javascript
let names = ['HTML', 'CSS', 'JavaScript'];

let nameLengths = names.map(function(name) {
return name.length;
});

// получили массив с длинами
alert( nameLengths ); // 4,3,10
```

- _every/some_

Эти методы используются для проверки массива.

Метод «Array.prototype.every(callback[, thisArg])» возвращает true, если вызов callback вернёт true для каждого элемента arr.
Метод «Array.prototype.some(callback[, thisArg])» возвращает true, если вызов callback вернёт true для какого-нибудь элемента arr.
```javascript
let arr = [1, -1, 2, -2, 3];

function isPositive(number) {
return number > 0;
}

alert( arr.every(isPositive) ); // false, не все положительные
alert( arr.some(isPositive) ); // true, есть хоть одно положительное
```

- _reduce/reduceRight_

Метод «Array.prototype.reduce(callback[, initialValue])» используется для последовательной обработки каждого элемента массива с сохранением промежуточного результата.
Метод reduce используется для вычисления на основе массива какого-либо единого значения, иначе говорят «для свёртки массива». Чуть далее мы разберём пример для вычисления суммы.
Он применяет функцию callback по очереди к каждому элементу массива слева направо, сохраняя при этом промежуточный результат.

Аргументы функции callback(previousValue, currentItem, index, arr):

previousValue – последний результат вызова функции, он же «промежуточный результат».
currentItem – текущий элемент массива, элементы перебираются по очереди слева-направо.

index – номер текущего элемента.

arr – обрабатываемый массив.

Кроме callback, методу можно передать «начальное значение» – аргумент initialValue. Если он есть, то на первом вызове значение previousValue будет равно initialValue, а если у reduce нет второго аргумента, то оно равно первому элементу массива, а перебор начинается со второго.

Пример:

```javascript
let arr = [1, 2, 3, 4, 5]

// для каждого элемента массива запустить функцию,
// промежуточный результат передавать первым аргументом далее
let result = arr.reduce(function(sum, current) {
return sum + current;
}, 0);

alert( result ); // 15
```
При первом запуске sum – исходное значение, с которого начинаются вычисления, равно нулю (второй аргумент reduce).
Сначала анонимная функция вызывается с этим начальным значением и первым элементом массива, результат запоминается и передаётся в следующий вызов, уже со вторым аргументом массива, затем новое значение участвует в вычислениях с третьим аргументом и так далее.

Подробнее: 

https://learn.javascript.ru/array-iteration

https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach

https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Array/filter

https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Array/map

https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Array/every

https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Array/some

https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce
#### 13. “hello world”.repeating(3) -> hello world hello world hello world. Как реализовать?

1 способ

```javascript
// cheat =)
String.prototype.repeating = String.prototype.repeat;
'hello world'.repeating(3);
```

2 способ

```javascript
String.prototype.repeating = function (count) {
var str = '' + this;
count = +count;
var rpt = '';
for (var i = 0; i < count; i++) {
  rpt = rpt + ' ' + str;
}
return rpt;
}
```
#### 14. Браузерные события элементов. Отмена дефолтных событий браузера
Событие – это сигнал от браузера о том, что что-то произошло.
Список самых часто используемых событий:

##### События мыши:
* click – происходит, когда кликнули на элемент левой кнопкой мыши
* contextmenu – происходит, когда кликнули на элемент правой кнопкой мыши
* mouseover – возникает, когда на элемент наводится мышь
* mousedown и mouseup – когда кнопку мыши нажали или отжали
* mousemove – при движении мыши

##### События на элементах управления:
* submit – посетитель отправил форму <form>
* focus – посетитель фокусируется на элементе, например нажимает на <input>

##### Клавиатурные события:
* keydown – когда посетитель нажимает клавишу
* keyup – когда посетитель отпускает клавишу

##### События документа:
* DOMContentLoaded – когда HTML загружен и обработан, DOM документа полностью построен и доступен.

##### События CSS:
* transitionend – когда CSS-анимация завершена.

Назначение обработчиков событий:
Использование атрибута HTML
```html
<input value="Нажми меня" onclick="alert('Клик!')" type="button">
```
Использование свойства DOM-объекта
```html
<input id="elem" type="button" value="Нажми меня" />
<script>
elem.onclick = function() {
  alert( 'Спасибо' );
};
</script>
```

##### addEventListener и removeEventListener
Методы addEventListener и removeEventListener являются современным способом назначить или удалить обработчик, и при этом позволяют использовать сколько угодно любых обработчиков.
Назначение обработчика осуществляется вызовом addEventListener с тремя аргументами:

element.addEventListener(event, handler[, phase]);

event - Имя события, например click

handler - Ссылка на функцию, которую надо поставить обработчиком.

phase - Необязательный аргумент, «фаза», на которой обработчик должен сработать.

Удаление обработчика осуществляется вызовом removeEventListener:
```javascript
// передать те же аргументы, что были у addEventListener
element.removeEventListener(event, handler[, phase]);
```

Удаление требует именно ту же функцию
Многие события автоматически влекут за собой действие браузера.
Например:

- Клик по ссылке инициирует переход на новый URL.
- Нажатие на кнопку «отправить» в форме – отсылку ее на сервер.
- Двойной клик на тексте – инициирует его выделение.

Есть два способа отменить действие браузера:

* Основной способ – это воспользоваться объектом события. Для отмены действия браузера существует стандартный метод event.preventDefault().

* Если же обработчик назначен через onсобытие (не через addEventListener), то можно просто вернуть false из обработчика.

Подробнее: 

https://learn.javascript.ru/introduction-browser-events#addeventlistener-i-removeeventlistener

https://learn.javascript.ru/default-browser-action
#### 15. Всплытие и перехват событий

Основной принцип всплытия:

При наступлении события обработчики сначала срабатывают на самом вложенном элементе, затем на его родителе, затем выше и так далее, вверх по цепочке вложенности.

![](https://learn.javascript.ru/article/event-bubbling/event-order-bubbling.png)

Самый глубокий элемент, который вызывает событие, называется «целевым» или «исходным» элементом и доступен как `event.target`.

Для остановки всплытия нужно вызвать метод `event.stopPropagation()`.

`stopPropagation` препятствует продвижению события дальше, но на текущем элементе все обработчики отработают.

Для того, чтобы не только предотвратить всплытие, но и останавить обработку событий на текущем элементе используется метод `event.stopImmediatePropagation()`

В современном стандарте, кроме "всплытия" событий, предусмотрено ещё и "погружение" (или "захват").

![](https://mdn.mozillademos.org/files/14075/bubbling-capturing.png)

Откройте данный пример: 

[исходный код](https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/show-video-box.html)

[пример](http://mdn.github.io/learning-area/javascript/building-blocks/events/show-video-box.html)

```javascript
video.onclick = function(e) {
e.stopPropagation();
video.play();
};
```
Подробнее:

[developer.mozilla.org](https://developer.mozilla.org/ru/docs/Learn/JavaScript/Building_blocks/%D0%A1%D0%BE%D0%B1%D1%8B%D1%82%D0%B8%D1%8F#%D0%92%D1%81%D0%BF%D0%BB%D1%8B%D1%82%D0%B8%D0%B5_%D0%B8_%D0%BF%D0%B5%D1%80%D0%B5%D1%85%D0%B2%D0%B0%D1%82_%D1%81%D0%BE%D0%B1%D1%8B%D1%82%D0%B8%D0%B9)

https://learn.javascript.ru/event-bubbling

#### 16. Делегирование. Пример
Всплытие событий позволяет реализовать один из самых важных приёмов разработки – делегирование.

Он заключается в том, что если у нас есть много элементов, события на которых нужно обрабатывать похожим образом, то вместо того, чтобы назначать обработчик каждому – мы ставим один обработчик на их общего предка.
Из него можно получить целевой элемент event.target, понять на каком именно потомке произошло событие и обработать его.
```javascript
<div id="menu">
<button data-action="save">Сохранить</button>
<button data-action="load">Загрузить</button>
<button data-action="search">Поиск</button>
</div>

<script>
function Menu(elem) {
  this.save = function() {
    alert( 'сохраняю' );
  };
  this.load = function() {
    alert( 'загружаю' );
  };
  this.search = function() {
    alert( 'ищу' );
  };

  let self = this;

  elem.onclick = function(e) {
    let target = e.target;
    let action = target.getAttribute('data-action');
    if (action) {
      self[action]();
    }
  };
}

new Menu(menu);
</script>
```

Подробнее:

https://learn.javascript.ru/event-delegation
#### 17. Напишите функцию F, так чтобы new F === F
```javascript
function F() { return F; }
```

В данном случае чтобы обеспечить равенство, мы явно задаем `return`, поэтому объект не создается, а `new` отрабатывает просто как функция.

_О `this` and `return`:_

Как правило, конструкторы ничего не возвращают. Их задача – записать всё, что нужно, в `this`, который автоматически станет результатом.

Но если явный вызов return всё же есть, то применяется простое правило:

- При вызове `return` с объектом, будет возвращён он, а не `this`.
- При вызове `return` с примитивным значением, оно будет отброшено.

Иными словами, вызов `return` с объектом вернёт объект, а с чем угодно, кроме объекта – возвратит, как обычно, `this`.

```javascript
function Animal(name) {
// this = {}; это делает интерпритатор

// в this пишем свойства, методы
this.name = name;
this.canWalk = true;

// return this; это делает интерпритатор
}
```
Подробнее:

https://learn.javascript.ru/constructor-new
#### 18. Function.prototype.bind polyfill
```javascript
if (!Function.prototype.bind) {
Function.prototype.bind = function (context /* ...args */) {
  var fn = this;
  var args = Array.prototype.slice.call(arguments, 1);

  if (typeof(fn) !== 'function') {
    throw new TypeError('Function.prototype.bind - context must be a valid function');
  }

  return function () {
    return fn.apply(context, args.concat(Array.prototype.slice.call(arguments)));
  };
};
}
```

https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
#### 19. Object.create polyfill

See here: [developer.mozilla.org](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Object/create#Polyfill)

#### 20. Event loop

https://habr.com/ru/post/461401/

#### 22. Денормализация в ES6

http://learn.javascript.ru/destructuring

#### 23. Spread оператор (...)
