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

## Important Rule:
// map's composition law
var law = compose(map(f), map(g)) === map(compose(f, g));