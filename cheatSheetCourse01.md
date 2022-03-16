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

//Callback function, it can be also an annonymous function
let studentStatus = student => return student.note >= 51 ? "Approved":"Not Approved".

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