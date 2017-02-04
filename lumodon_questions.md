# Lumodon's Questions:
========== Chapter 2: Firstclass ==============
[Chapter 2: Firstclass](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch2.html#why-favor-first-class)

```
// go back to every httpGet call in the application and explicitly pass err
// along.
httpGet('/post/2', function(json, err) {
  return renderPost(json, err);
});
```

becomes 

```
// renderPost is called from within httpGet with however many arguments it wants
httpGet('/post/2', renderPost);
```
### My Question: 
- but comment doesn't make sense. Where is renderPost declared? What about when you're doing something different each time you render post? i.e. you're using anonomyous functions, so you must be doing something different with each httpGet call.

========== Chapter 5: Pointfree ==============
[Chapter 5: Pointfree](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch5.html#pointfree)
## Portion 1:
```
//not pointfree because we mention the data: word
var snakeCase = function(word) {
  return word.toLowerCase().replace(/\s+/ig, '_');
};

//pointfree
var snakeCase = compose(replace(/\s+/ig, '_'), toLowerCase);
```
### Author:
- See how we partially applied replace? What we're doing is piping our data through each function of 1 argument. Currying allows us to prepare each function to just take its data, operate on it, and pass it along. Something else to notice is how we don't need the data to construct our function in the pointfree version, whereas in the pointful one, we must have our word available before anything else.

### My Question:
- But what if the order matters? What if we do something to a string, and run a regex on the result of that string, that depends on the prior change we made? e.g. what if the 'i' regex modifer didn't exist?

## Portion 2:
```
//not pointfree because we mention the data: name
var initials = function(name) {
  return name.split(' ').map(compose(toUpperCase, head)).join('. ');
};

//pointfree
var initials = compose(join('. '), map(compose(toUpperCase, head)), split(' '));

initials("hunter stockton thompson");
// 'H. S. T'
```

### My Question:
- Didn't they just say order doesn't matter? But in this case, if you join before you split, you'll end up with:
```
// 'H . S . T'
```
instead of:
```
// 'H. S. T'
```
### Answer:
from doing compose exercises:
```
// Exercise 2:
// ============
// use _.compose(), _.prop() and _.head() to retrieve the name of the first car
var nameOfFirstCar = _.compose( _.prop('name'), _.head);
```
- It would seem that order DOES matter, and the *compose* function takes things in backwards order.
- When trying to do the above exercise like this:
```
// Exercise 2:
// ============
// use _.compose(), _.prop() and _.head() to retrieve the name of the first car
var nameOfFirstCar = _.compose( _.head, _.prop('name') );
```
It doesn't pass.

====================== Notes ======================
## Important Rule:
// map's composition law
var law = compose(map(f), map(g)) === map(compose(f, g));

## Exercise 4 from functors
Note: This line from chapter 8 functors exercise makes so much sense! You're piping information from one function to another. Not sure why this only just now makes sense.
```
var ex4 = _.compose( Maybe.of, parseInt );
```

==================== Code Wars =====================
# Code Wars Exercises
## Exercise 1: Calculate average

My Solution:
```
function find_average(array) {
  let r = array.reduce( (a,b) => {
    return a + b
  })
  
  return r / array.length
}
```

Best Solution (as voted on by others):
```
function find_average(array) {
  return array.reduce((result, current) => result + current, 0) / array.length;
}
```

## Exercise 2: Multiply All

My Solution:
```
const multiplyAll = array => {
  return multiplicand => {
    const resultArray = []
    array.forEach( element => {
      resultArray.push( element * multiplicand )
    })
    return resultArray
  }
}
```

Best Solution (as voted on by others):
```
multiplyAll = a => x => a.map(e => e * x);
```

## Exercise 3: Higher-Order Functions Series - Find the first Python developer

My Solution:
```
const getFirstPython = list => {
  for(let developer of list) {
    if(developer.language === 'Python') {
      return developer.firstName + ', ' + developer.country
    }
  }
  return 'There will be no Python developers'
}
```

Best Solution (as voted on by others):
```
function getFirstPython(list) {
  const dev = list.find(x => x.language === "Python")
  return dev ? `${dev.firstName}, ${dev.country}` : "There will be no Python developers"
}
```

## Exercise 4: Higher-Order Functions Series - Order the food

My Solution:
```
const orderFood = list => {
  let listOfMeals = {}
  for(let person of list) {
    if( person.meal ) {
      listOfMeals[ person.meal ] = listOfMeals.hasOwnProperty( person.meal ) ? listOfMeals[ person.meal ] + 1 : 1
    }
  }
  return listOfMeals
}
```

Best Solution (as voted on by others):
```
const orderFood = a => a.reduce( (acc,v) => ( acc[v.meal] = ( acc[v.meal] || 0 ) + 1, acc ), {} );
```

## Exercise 5: Stringing me along
My Solution:
```
const createMessage = stringX => stringY => stringY ? createMessage(stringX + ' ' + stringY) : stringX
```

Best Solution (as voted on by others):
```
function createMessage(str) {
    return function(next){
      if (next === undefined) {return str;}
      return createMessage(str + " "+ next);
    }
}
```

## Exercise 5: Dollars and Cents
My Solution:
```
const formatMoney = integerX => integerX.toFixed(2).toString().replace(/^/,'$')
```

Best Solution (as voted on by others):
```
function formatMoney(amount){
  return '$' + amount.toFixed(2);
}
```

## Exercise 6: Fun with Lists: filter
My Code:
```
function filter(head, p) {
  let iterator = head
  if(!iterator) return null
  let tail = undefined
  let newHead = undefined
  
  while(iterator){
    if(p(iterator.data)){
      if(tail){
        tail.next = new Node(iterator.data)
        tail = tail.next
      } else {
        tail = new Node(iterator.data)
        newHead = tail
      }
    }
    iterator = iterator.next
  }
  return newHead;
}
```

Best Solution (as voted on by others):
```
function filter(head, p) {
  return !head ? null : p(head.data) ? new Node(head.data, filter(head.next, p)) : filter(head.next, p);
}
```

## Exercise 7: Calculating with Functions

My Code:
```
const zero = (...args) => args.length > 0 ? args[0](0) : 0
const one = (...args) => args.length > 0 ? args[0](1) : 1
const two = (...args) => args.length > 0 ? args[0](2) : 2
const three = (...args) => args.length > 0 ? args[0](3) : 3
const four = (...args) => args.length > 0 ? args[0](4) : 4
const five = (...args) => args.length > 0 ? args[0](5) : 5
const six = (...args) => args.length > 0 ? args[0](6) : 6
const seven = (...args) => args.length > 0 ? args[0](7) : 7
const eight = (...args) => args.length > 0 ? args[0](8) : 8
const nine = (...args) => args.length > 0 ? args[0](9) : 9

const plus = rightOperand => leftOperand => leftOperand + rightOperand
const minus = rightOperand => leftOperand => leftOperand - rightOperand
const times = rightOperand => leftOperand => leftOperand * rightOperand
const dividedBy = rightOperand => leftOperand => leftOperand / rightOperand
```

Best Solution (as voted on by others):
```
var n = function(digit) {
  return function(op) {
    return op ? op(digit) : digit;
  }
};
var zero = n(0);
var one = n(1);
var two = n(2);
var three = n(3);
var four = n(4);
var five = n(5);
var six = n(6);
var seven = n(7);
var eight = n(8);
var nine = n(9);

function plus(r) { return function(l) { return l + r; }; }
function minus(r) { return function(l) { return l - r; }; }
function times(r) { return function(l) { return l * r; }; }
function dividedBy(r) { return function(l) { return l / r; }; }
```

## Exercise 8: Flatten

My Code:
```
var flatten = function (array){
  let newArr = []
  for( let element of array ) {
    if(element.constructor === Array) {
      element.forEach( e => {
        newArr.push(e)
      })
    } else {
      newArr.push(element)
    }
  }
  return newArr
}
```

Best Solution:
```
var flatten = function (lol){
  return [].concat.apply([],lol);
}
```

## Exercise 9: Functional Add

My Code:
```
const add = n => e => n+e
```

Best Solution:
```
var add = a=> n=>n+a
```

## Exercise 10: 

My Code:
```
const _if = (bool, func1, func2) => bool ? func1() : func2()
```

Best Solution: The 'if' function
```
function _if(bool, func1, func2) {
  return bool ? func1() : func2();
}
```
