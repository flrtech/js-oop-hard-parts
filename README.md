## These are my notes for the course The Hard Parts of Object Oriented JavaScript by Will Sentance. If you want any of this to make any sense, go whatch the course on frontendmasters.com.
<br>


<details>
<summary>Object Creation + Prototype & New</summary>
<br>

# Object Creation + Prototype & New:

## Ways of storing data and functionality
<br>

### Objects (object literals)
Store functions with their associated data!
<br>

Let's create some objects
```javascript
// One way of creating an object literal

const user1 = {
  name: "Phil",
  score: 4,
  increment: function() {
    user1.score++;
  }
};

user1.increment(); //user1.score => 5


// Another way, using dot notation

const user2 = {}; //create an empty object
user2.name = "Julia"; //assign properties to that object
user2.score = 5;
user2.increment = function() {
  user2.score++;
};


// Creating user3 using Object.create

const user3 = Object.create(null); // whatever we pass into it, will always return an empty object
user3.name = "Eva";
user3.score = 9;
user3.increment = function() {
  user3.score++;
};
```

It's pretty obvious that by creating objects (*users* in our case) in this way, we are breaking the DRY principle. Meaning, for each *user* we want to create, we have to write the same code and functionality.
<br>

### Solution 1. Generate objects using a function
```javascript
function userCreator(name, score) {
  const newUser = {};
  newUser.name = name;
  newUser.score = score;
  newUser.increment = function() {
    newUser.score++;
  };
  return newUser;
};

const user1 = userCreator("Phil", 4);
const user2 = userCreator("Julia", 5);
user1.increment()
```
### Problems:
Each time we create a new user we make space in our computer's memory for all our data and functions. But our functions are just copies.
<br>

### Having seen solution 1. what do we want ideally?
To store the increment function in just one object and have the **interpreter**, if it doesn't find the function on *user1*, look up to that object to check if it's there.

### Solution 2. Using the prototype chain and making the link with *Object.create()* technique
```javascript
function userCreator (name, score) {
  const newUser = Object.create(userFunctionStore);
  newUser.name = name;
  newUser.score = score;
  return newUser;
};

const userFunctionStore = {
  increment: function(){this.score++;},
  login: function(){console.log("You're loggedin");}
};

const user1 = userCreator("Phil", 4);
const user2 = userCreator("Julia", 5);
user1.increment();
```
In this way, if the **interpreter** doesn't find **.increment** on *user1*, it looks up the **prototype chain** to the next object and finds **.increment** 1 level up.  
But how does this happen? The *user1* object will have a hidden property **_ _proto\_ _** which, when we create an object using *Object.create(objectPassed)*, will be set to point to *objectPassed*.
So in our example, the interpreter looks into *user1* for its *.increment* method, doesn't find it, then looks into its **_ _proto\_ _**, sees that it links to *userFunctionStore*, looks into that object, and finds the method.
<br>

### Solution 3. Introducing the keyword that automates the hard work: new
```javascript
const user1 = new userCreator("Phil", 4)
```
When we call the constructor function with new in front we automate 3 things
1. Create a new *user* object
2. Return the new *user* object
3. The new *user* object that was returned will have its **_ _proto\_ _** property link to the **prototype** object from *userCreator*

### Interlude - functions are both objects and functions 
```javascript
function multiplyBy2(num){
  return num*2
}

multiplyBy2.stored = 5
// when called with ( ), multiplyBy2 does its "function" duties
multiplyBy2(3) // 6

// whe used with "dot", it does its "object" duties
console.log(multiplyBy2.stored) // 5
console.log(multiplyBy2.prototype) // {}
```
<br>

### So what does the fact that functions are both functions and object have to do with the *new* keyword?
The fact that all functions have a default property on their object version, called **prototype**, which is itself an object - makes it possible to replace our *functionStore* object.

```javascript
function UserCreator(name, score){
  this.name = name;
  this.score = score;
}

UserCreator.prototype.increment = function(){
  this.score++;
};
UserCreator.prototype.login = function(){
  console.log("login");
};

const user1 = new UserCreator(“Eva”, 9)
user1.increment()
```

So lets's focus on the main things tha happen when this code runs:  
* The *user1* constant will be saved in the **Global Memory**, and at first it will be uninitialized.
* The function *UserCreator* will be run with arguments "Eva" and 9, but since it has the **new** keyword in front of it some special things will happen in the function's **Execution Context** in **Local Memory**:  
  * The *name* and *score* parameters will be initialized with the arguments passed into the function
  * A local variable called **this** will also be saved in **Local Memory** and initialized as an empty object.
  * The **this** object will receive two properties called *name* and *score* each having assigned their respective value ("Eva", 9). **BUT**, thanks to the **new** keyword, another parameter will be added to the **this** object, called **_ _proto\_ _**, which is a hidden parameter and will link to *userCreator*'s **prototype** object  
  * The object assigned to **this** will be returned.
* Now *user1* is initialized with the object that was returned from calling *UserCreator*  
* When we call *user1.increment()* the JS interpeter will look inside *user1* for the *.increment* method, will not find it, then it will look on *user1*'s **_ _proto\_ _** property which links to the **prototype** object on *userCreator*, and on that object, the *.increment* method will be found and called.
</details>