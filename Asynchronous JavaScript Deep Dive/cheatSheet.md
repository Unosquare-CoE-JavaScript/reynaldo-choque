# Asynchronous JavaScript Deep Dive

## _UnoSquare CoE Jvascript_

https://www.udemy.com/course/asynchronous-javascript-deep-dive/ 

## 02. Understanding Asynchronous Coding
- Improve performance and eliminates code blocking
- Not easy to understand at first glance
- Event loop makes possible handle asynchronous calls

## 03. The necessity of Callbacks
Many times there is a necessity to do something else inside a function, callback fuctions are parameters to do that labor.
``` javascript
//example
//some data
let studentsData = [ {name: "Homer", note: 40}, 
                     {name: "Lisa", note: 100}, 
                     {name: "Bart", note: 0} ];

//Callback function, it can be also an annonymous function
let studentStatus = student => return student.note >= 51 ? "Approved":"Not Approved";

//main function is receiving a callback parameter                     
let processStudents = (data, callbackFunction) {
    if(typeof callbackFunction !== "function")
      return;   //callback function not sent
    data.forEach(
      student => {
          console.log(`Student: ${student.name}: ${callbackFunction(student)}`);
      }
    );
};

//calling the main function
processStudents(studentsData, studentStatus);   //studentStatus function is sent as parameter, this is a callback function
```

##### Main problems with callbacks
- Callback hell
- Dificult to reason about
- Inversion of control 

#### EXERCISE 0
``` javascript
/*
Making function call asynchronous (very basic with setTimeout)
*/
let students = [{name:"Mary",score:90,school:"East"},
{name:"James",score:100,school:"East"},
{name:"Steve",score:40,school:"East"},
{name:"Gabe",score:90,school:"West"},
{name:"Rachel",score:85,school:"East"},
{name:"Rochelle",score:95,school:"West"},
{name:"Lynette",score:75,school:"East"}];

let processStudents = function(data, callback) {
    for (let i = 0; i < data.length; i++) {
        if (data[i].school.toLowerCase() === "east") {
            if (typeof callback === "function") {
                callback(data[i]);
            }
        }
    }
}

console.log("Before determineTotal");

let determineTotal = function() {
    let total = 0,
        count = 0;
    processStudents(students, function(obj) {
        total = total + obj.score;
        count++;
    });
    console.log("Total Score: " + total + " - Total Count: " + count);
}

setTimeout(determineTotal,0);  //here we send the function, we are not executing it
console.log("End of code.")
```
## 04. Promises
- An object with properties
- Represents the eventual completion or failure of an asynchronous operation
- Provides a resulting value
#### Example
``` javascript
"use strict";

const swapi = function(num) {
    let url = "https://swapi.dev/api/people/";

    fetch(url + num + "/")
    .then(data => data.json())
    .then(obj => {
        console.log(obj);
        return fetch(obj.homeworld);
    })
    .then(hwdata => hwdata.json())
    .then(hwobj => console.log(hwobj));
};
swapi(9);
console.log("Other commands!");
```

#### Immediately Invoked Function Expressions (IIFEs)
As name suggest, IIFE is a function expression that automatically invokes after completion of the definition. The parenthesis () plays important role in IIFE pattern. In JavaScript, parenthesis cannot contain statements; it can only contain an expression.
###### Example
``` javascript
(function () {
  let x = 6;
  console.log(x * x);
})();
```

#### EXERCISE 01
``` html
<!DOCTYPE html>
<html>
<head>
    <title>Asynchronous JavaScript and Promises</title>
</head>
<body>
    <div>
        Promises Exercise 1
    </div>
    <div>
    </div>
    <div>
        <input type="text" id="word"><span id="results"></span>
        <input type="submit" value="Check It!" id="submitBtn">
    </div>
</body>
</html>
```
``` javascript
var MAINAPP = MAINAPP || {};

(function(nsp) {
    "use strict";

    let wordnikWord = "http://api.wordnik.com/v4/word.json/",
        apiKey = "?api_key=2efe06dd56a60633b30010e4d970da03b55279db9896d7127",
        field = document.querySelector('#word'),
        btn = document.querySelector('#submitBtn'),
        results = document.querySelector('#results'),
        word,
        scrabbleVal = 0;

    const getValue = function(word) {
        fetch(wordnikWord + word + '/scrabbleScore' + apiKey)
        .then(function(data) {
            return data.json();
        })
        .then(function(score) {
            scrabbleVal = score.value;
            results.innerHTML = scrabbleVal;
        });
    };

    btn.addEventListener('click', function(e) {
        word = field.value;
        getValue(word);
    });

    nsp.scrabbleVal = scrabbleVal;
})(MAINAPP);
```

#### EXERCISE 2
``` javascript
var MAINAPP = (function(nsp) {
    "use strict";
    let url = 'https://jsonplaceholder.typicode.com/';
    fetch(url + 'posts/')
    .then(response1 => response1.json())
    .then(posts => nsp.posts = posts)
    .catch(err => console.log(`Problem retrieving posts: ${err}`));

    fetch(url + 'comments/')
    .then(response2 => response2.json())
    .then(comments => nsp.comments = comments)
    .catch(err => console.log(`Problem retrieving comments: ${err}`));

    fetch(url + 'todos/')
    .then(response3 => response3.json())
    .then(todos => nsp.todos = todos)
    .catch(err => console.log(`Problem retrieving todos: ${err}`));

    return nsp;
})(MAINAPP || {});
```

### Create new Promises
``` javascript
let customPromise = (time) => {
	return new Promise((resolveFn, rejectFn) => {
  	//throw "It's over";
  	if(isNaN(time)){
      rejectFn("Fatal, not a number");
    } else {
    	setTimeout(resolveFn, time, "done");
    }
  });
};
customPromise(4000).then(
 itsOk => {
 	console.log(itsOk);
 },
 error => {
 	console.log(error);
 }
).catch( err => console.log("catch,", err));

customPromise("test").then(
 itsOk => {
 	console.log(itsOk);
 }
).catch( err => console.log("catch,", err));
```
#### EXERCISE 3
``` javascript
"use strict";
//Modify the code by creating a promise so that the code can run asynchronously.
const massiveProcess = function(num) {
    return new Promise(function(resolve, reject) {
        if (isNaN(num)) {
            reject("Please enter a number!");
        } else {
            let result = 0;
            setTimeout(function() {
                for (let i = num ** 7; i >= 0; i--) {        
                    result += Math.atan(i) * Math.tan(i);
                };
                resolve(result);
            }, 0);
        }
    });
};

massiveProcess(10)
    .then(function(amt) {
        console.log("The number is: " + amt);
    })
    .catch(function(msg) {
        console.error(msg);
    });

//More processing later on
console.log(5 * 5 + 100);
```
### Additional Methods

- > The design purpose of Promise.all is to fulfill many promises. For example, when a user logs into a web app like Facebook, several network requests probably need to be made to populate the user’s personalized content:


- > The method name Promise.race is befitting because it causes all of the promises to “race” against each other with only a single winner


- > The Promise.allSettled() method returns a promise that resolves after all of the given promises have either fulfilled or rejected, with an array of objects that each describes the outcome of each promise.

- > Promise.any() takes an iterable of Promise objects. It returns a single promise that resolves as soon as any of the promises in the iterable fulfills, with the value of the fulfilled promise.
 
#### EXERCISE 4
``` javascript
var MAINAPP = (function(nsp) {
    "use strict";
    let url = 'https://jsonplaceholder.typicode.com/';
    let p1 = fetch(url + 'posts/').then(response1 => response1.json()),
        p2 = fetch(url + 'comments/').then(response2 => response2.json()),
        p3 = fetch(url + 'todos/').then(response3 => response3.json());

    Promise.all([p1, p2, p3])
    .then(msg => {
        nsp.posts = msg[0];
        nsp.comments = msg[1];
        nsp.todos = msg[2];
        console.log("We have received the data!");
    })
    .catch(err => console.log(`Problem retrieving data: ${err}`));
    return nsp;
})(MAINAPP || {});
```

## 05. ASYNC / AWAIT
Async/Await is the extension of promises which we get as a support in the language.

##### Async:
It simply allows us to write promises based code as if it was synchronous and it checks that we are not breaking the execution thread. It operates asynchronously via the event-loop. Async functions will always return a value. It makes sure that a promise is returned and if it is not returned then javascript automatically wraps it in a promise which is resolved with its value.

##### Await:
Await function is used to wait for the promise. It could be used within the async block only. It makes the code wait until the promise returns a result. It only makes the async block wait.
Dillinger uses a number of open source projects to work properly:

#### EXERCISE 5
``` javascript
let user3Posts = null;
const retrievePosts = async function(userID) {
    let url = 'https://jsonplaceholder.typicode.com/posts',
        posts = [];
    posts = await fetch(url).then(resp => resp.json());
    return posts.filter(obj => obj.userId === userID);
};
retrievePosts(3).then(val => user3Posts = val);
```

#### EXERCISE 6
``` javascript
"use strict";
let addTodo = async function(todo) {
    try {
        let resp = await fetch('https://jsonplaceholder.typicode.com/todos/', {
            method: 'POST',
            headers: {
                "Content-type": "application/json"
            },
            body: JSON.stringify(todo)
        });
        let results = await resp.json();
        console.log(results);
    } catch(e) {
        console.error(`Unable to create todo ${e}`);
    }
};

let todo = {
    completed: false,
    userId: 1,
    title: "Learn Promises"
};
addTodo(todo);
```

#### EXERCISE 7
``` javascript
var MAINAPP = (function(nsp) {
    "use strict";
    let url = 'https://jsonplaceholder.typicode.com/';
    
    (async function() {
        try {
            let data = await fetch(url + 'posts/'),
                posts = await data.json();
            nsp.posts = posts;
        } catch(e) {
            console.log(`Problem retrieving posts: ${e}`);
        }
    })();
    return nsp;
})(MAINAPP || {});
```

#### EXERCISE 8
``` javascript
var MAINAPP = (function(nsp) {
    "use strict";

    let url = 'https://jsonplaceholder.typicode.com/';

    (async function() {
        try {
            let p1 = fetch(url + 'posts/'),
                p2 = fetch(url + 'comments/'),
                p3 = fetch(url + 'todos/');

            let results = await Promise.all([p1, p2, p3]);

            nsp.posts = await results[0].json();
            nsp.comments = await results[1].json();
            nsp.todos = await results[2].json();
            console.log("data received.");
        } catch(e) {
            console.error(`Problem retrieving data: ${e}`)
        }
    })();

    console.log("Remaining Code.")
    return nsp;
})(MAINAPP || {});
```

## 06. GENERATORS
ES6 introduced a new way of working with functions and iterators in the form of Generators (or generator functions). A generator is a function that can stop midway and then continue from where it stopped. In short, a generator appears to be a function but it behaves like an iterator.

``` javascript
function* iterableObj() {
  yield 'This';
  yield 'is';
  yield 'iterable.'
}
for (const val of iterableObj()) {
  console.log(val);
}
```


#### EXERCISE 9
``` javascript
const randomNum = function* (end) {
    while (true) {
        let rand = Math.floor(Math.random() * end) + 1;
        yield rand;
    }
};

 let rand100 = randomNum(100);
 let rand10 = randomNum(10);
 
 console.log(rand100.next().value);
 console.log(rand10.next().value);
```

