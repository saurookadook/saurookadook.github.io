---
layout: post
title:      "Quirky Bits of JS: Scope and Hoisting"
date:       2018-07-07 21:44:47 +0000
permalink:  quirky_bits_of_js_scope_and_hoisting
---

Because of the way in which the JavaScript language functions (and the primary context in which it does so, via a web browser), it brings with it a fair number of oddities which appear in very few other programming languages. While the main intent of this post will be to discuss some of the issues one may encounter in JavaScript in relation to scope and hoisting, I would like to preface that with a (somewhat) brief review of variables and functions

## Variables 
JavaScript comes with three possibilities for variable declaration: `var`, `let`, and `const`. All of them have their own general uses, though `var` and `let` are nearly identical.

In order to get the messy one out of the way first, let’s begin with `var`. Variables declared with it hold values or functions which are expected to change in some way, and they can be declared and assigned (initialized), and then called with:

```
// initialization
var person = “Cpt. Hook”;
	//=> undefined

// call
 person;
	//=> “Cpt. Hook”
```

Pretty standard and expected, but what about:

```
var person = “Cpt. Hook”;
	//=> undefined
var person = “Rick Astley”;
	//=> undefined
person;
	//=> “Rick Astley”
```

And here you see why it’s largely discouraged to use `var` when declaring variables. On the other hand, `let` is used similarly to `var`, the exception being that it helps us avoid running into this kind of confusion, as it will not allow for the declaration of the same variable twice:

```
let someGuy = “Steve”;

let someGuy = “MoJo JoJo”;
	//=> Uncaught SyntaxError: Identifier ‘someGuy’ has already been declared
```

One last aspect of `var` and `let` which differentiates them from `const` is their ability to be declared without assigning them an initial value:

```
var blankity;
	//=> undefined

let blank;
	//=> undefined

const whoops;
	//=> Uncaught SyntaxError: Missing initializer in const declaration
```

And with that, we’re brought to the final countdown: `const`. Unlike it’s cousins, `const` *must* be assigned a value at declaration, as variables declared with the `const` reserved word cannot be redeclared or reassigned:

```
const pickle = “Rick”;
	//=> undefined 

pickle; 
	//=> “Rick”

const pickle = “Jim!”;
	//=> Uncaught TypeError: Assignment to constant variable
```


## Functions
There are two primary ways to create functions in JavaScript, referred to as ‘named functions’ and ‘anonymous functions’. Implementation of the former works almost exactly as the name implies:

```
// declaration
 function needABeer() {
	return “probably not going to find one here”;
}

// call (by way of the invocation operator `()` )
needABeer();
	//=> “probably not going to find one here”
```

When it comes to the latter, they can be declared similarly to ‘named functions’:
```
functions () {
	return “I’m in a glass case of emotion!”;
}
```

However I’m sure you already notice a pretty big issue with this: how are we supposed to call them if they don’t have a name? That’s why they’re generally used with event handlers, as an argument for other methods or functions, or assigned to a variable when its initialized:
```
// event handler
let button = document.querySelector(‘big button’);

button.onclick = function() {
	return “STOP POKING ME!”;
}

// as an argument
let numbers = [“fournteen”, “fivest”, “twond”]

numbers.forEach(function(numbers) {
	console.log(`Is ${number} really a number….?`);
}

// assigned to variable
let slowDown = function() {
	return “You were going 41 in a 40, bud.”;
}

slowDown();
	//=> “You were going 41 in a 40, bud.”
```

## Scope
Before things get weird, some scope basics. In JavaScript, scope refers to the set of rules which dictate how declared variables and functions are stored and how they can be retrieved for later use. The main scope within JavaScript is known as ‘global scope’. For example, every declaration in the above examples would be considered in ‘global scope’, meaning that they can be accessed by anything else similarly declared in global scope. Aside from ‘global scope’, there is also ‘local scope’, which has several subsets but generally refers to any variables which are declared or initialized within a function or block of code, thereby making it inaccessible to any other functions or variables not defined within its scope:
```
function logStuff() {
	const stuff = “gibberish and rubbish”;

	return stuff;
}

logStuff();
	//=> “gibberish and rubbish”

stuff;
	// Uncaught ReferenceError: stuff is not defined
```

Additionally, scopes can be chained, allowing for situations in which a variable declared inside of a function can be accessed by another function declared in that same function:

```
const dhaWorld = “The earth”;

function sentence() {
	const verb = “is”;

	function words() {
		const adjective = “ROUND”;

		return `${dhaWorld} ${verb} ${adjective}`;
	}
	
	const moarWords = words();

	return moarWords;
}

sentence();
	//=> “The earth is ROUND”
```

From the above example, another aspect of scope in JavaScript becomes apparent: when the JavaScript engine cannot find a reference to variables and functions referenced within the current scope of of a function, it looks up to closest outer scope to find those references, eventually reaching the global scope if none can be found. 


## Hoisting

And now for the weirdness (and why people have largely decided to drop `var` like a bad ex). Hoisting is a phenomenon due to the nature of JavaScript, in that code is run through two different phases: a compilation phase and an execution phase. During the compilation phase, invocations are ignored and declarations are stored in memory. Then in the execution phase, invocations are executed with the stored information from the compilation phase. Though it creates some strange issues like hoisting, it’s a fairly creative way to allow for the running of object-oriented code which would usually need to be compiled well in advance. Consider the following:

```
function meow() {
	return “The cat goes: Meoooowwwwww!”
}

meow();
```

Yup, no surprises. Now:

```
meow();

function meow() {
	return “The cat goes: Meoooowwwwww!”
}
```

Seems like it shouldn’t work, doesn’t it? Well, due to JavaScripts two-part phase when running code, both of these examples will produce the exact same result and is an example of function hoisting. In the latter, the function declaration will be saved in memory in the compilation phase and then the invocation will occur (with the declared function still in memory) during the execution phase.

‘So if there is function hoisting, is there also variable hoisting?’, you might ask. You bet and this leads to one of the biggest issues with using `var`. Because of the way `var` works (in this case, specifically the way it is stored in the compilation phase), nonsense like the following won’t break when run:
```
function isConfusing() {
	console.log(message);
	
	var message = “Just….why?”;
}

isConfusing();
	// LOG: undefined
	//=> undefined
```

Yeah….not ideal and, honestly, pretty confusing at first glance. However, what is essentially happening during the compilation phase (and therefore allowing the code to run) is the following:
```
function isConfusing() {
	var message;

	console.log(message);
	
	message = “Just….why?”;
}
```

Given this format, it’s easy to see why the code is still being allowed to run, though imagine a similar situation involving vastly more complex and intricate code, and then trying to debug an issue where code doesn’t break when using (essentially) improper practices. I shudder to even think about it. And with this in mind, I’ll leave you with why `let` is a beacon of hope among the darkness of `var`’s shadow. Unlike `var`, `let` will not hoist it’s declarations to the top of scope, thereby stopping the problem from above:
```
theLight;

let theLight = “I see it!”;
// ERROR: Uncaught ReferenceError: theLight is not defined
```

Oh, thank the Maker. Sanity is restored.

After exploring these concepts in JavaScript, my hope is that the reasons for their occurrences is made a little more clear for those learning the language who find themselves confused by them (and for those reading who are well-versed in the language, hopefully you at least leave with a giggle).



