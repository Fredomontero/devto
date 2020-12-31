# Scope, closure, context and the this keyword explained

## Scope
Each variable has a scope, which is the part of the program in which the variable is visible. for variables defined outside of any function or block, the scope is the whole program - we can refer to these variables wherever we want. These are called global variables.

Variables created for function parameters or declared inside a function can be referenced only in that function, so they are known as local variables. Every time the function is called, new instances of these variables are created. This provides some isolation between functions - each function call acts in its own little world (it's local environment).

variables declared with let and const are in fact local to the block that they are declared in, so if you create on of those inside a for loop, the code before and after the loop cannot "see" it. In pre-2015 JavaScript, only functions created new scopes, so old-style variables, created with the var keyword are visible throughout the whole function that they appear in - or throughout the global scope if they are not in a function.

```javascript
let x = 10;

if(true){
	let y = 20;
	var z = 30;
	console.log(x + y + z); //60
}
//y is not visible
console.log(x + z); //40

```

Each scope can "look out" into the scope around it, so x is visible inside the block in the example. The exception is when multiple variables have the same name - in that case, code can see only the innermost one. For example, when the code inside that halve function refers to n, it is seeing its own n, not the global n.
```javascript
const halve = function(n){
	return n/2;
}

let n = 10;
console.log(halve(100)); //50
console.log(n); //10

```
###  Nested Scope

JavaScript distinguish not just global and local variables. Blocks and functions can be created inside other blocks and functions can be created inside other blocks and functions, producing multiple degress of locally, for example, this function:

```javascript
const hummus = function(factor){
  const ingredient = function(amount, unit, name){
    let ingredientAmount = amount * factor;
    if(ingredientAmount > 1){
      unit += "s"
    }
    console.log(`${ingredientAmount} ${unit} ${name}`);
  };
  ingredient(1, "can", "chickpeas");
  ingredient(0.25, "cup", "tahini");
  ingredient(0.25, "cup", "lemon juice");
  ingredient(1, "clove", "garlic");
  ingredient(2, "tablespoon", "olive oil");
  ingredient(0.5, "teaspoon", "cumin");
}

```
the code inside the ingredinet function can see the factor variable from the outer function. But its local variables such as unit, ingredientAmount, are not visible in the outer function. the set of variables visible  inside a block is determined by the place of that block in the program text. Each local scope can also see all the local scopes that contain it, and all scopes can see the global scope. This approach to variables visibility is called lexical scoping.

## Closure

The ability to treat functions as values, combined with the fact that local variables are re-created every time a function is called, brings up an interesting question. What happens to local variables when the function call that created is no longer active?

The following code shows an example of this. It defines a function, wrapValue, that creates a local binding. It then returns a function that accesses and returns this local binding:

```javascript
function wrapValue(n){
  let local = n;
  return () => local;
}

let wrap1 = wrapValue(1);
let wrap2 = wrapValue(2);

console.log(wrap1()); //1
console.log(wrap2()); //2
```

This is allowed and works as you'd hope - both instances of the binding can still be accessed. This situation is a good demonstration of the fact that local bindings are created anew for every call, and different calls can't trample on one another's local bindings.

This feature - being able to reference a specific instance of a local binding in an enclosing scope - is called a closure. a function that references bindings from local scopes around it is called a closure. This behavior not only frees you from having to worry about lifetimes of binfings but also make ir possible to use function values in some creative ways.

Thinking about programs like this takes some practice. A good mental model is to think of function values as containing both the code in their body and the environment in which they are created. When called, the function body sees the environment in which it was created, not the environment in which it is called.