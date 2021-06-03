## Techniques Used 

- Using VSCode to commit files to github 
- Creating a .gitignore file
- Exporting modules
- Non-web server Node.js JavaScript code
    - Lambda expressions
    - Object destructuring
    - for loops
    - Tenary operater
    - .push() method 
    - .reduce() method
    - switch operater
    - if...else statments
    - template literals
- Web server Node.js JavaScript code
  - Installing fastify
  - Initializing as a node.js file
  - Using http "GET" verb
  - Utilizing the html MIME type
  - Returning a status code
  - Responding to client requests and handling query parameters
  - Using readFile() fs method

## Objectives


### Write functions which can identify and handle valid coin objects. Export the coinCount() which calculates the value of coin objects.

```
//returns true if coin is a valid value
let validDenomination = (coin) => {let coinDenom = [1, 5, 10, 25, 50, 100]; return (coinDenom.indexOf(coin)!== -1 ? true : false)};


//returns calculated coin object
let valueFromCoinObject = (obj) => {
     
    const {denom = 0, count = 0} = obj;
    return denom*count;
};
//console.log(valueFromCoinObject({denom: 5, count: 3}))

//returns value of coin objects within an array
let valueFromArray = (arr) => {
   
    let coinArr = [];
    for(i=0; i< arr.length; i++) {
        coinArr.push(valueFromCoinObject(arr[i]));
    }
    return coinArr.reduce((add, current) => add + current);
};


let coinCount = (...coinage) => `${valueFromArray(coinage)}`;


console.log("{}", coinCount({denom: 5, count: 3}));
console.log("{}s", coinCount({denom: 5, count: 3},{denom: 10, count: 2}));
const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];
console.log("...[{}]", coinCount(...coins));
console.log("[{}]", coinCount(coins));

module.exports = {coinCount, coins};
```


### Create a server that requires fastify. Read the index.html file. Handle three routes and requests from the client to respond with the provided coin objects value.  

```
const fs = require("fs");
const fastify = require("fastify")();
const {coinCount, coins} = require("./p3-module.js");


fastify.get("/", (req, res) => {
    fs.readFile(`${__dirname}/index.html`, (err, data) => {
        if (err){
            res.code = 500;
            res.header('Content-Type', 'text/html');
        }else{
            res.statusCode = 200;
            res.header('Content-Type', 'text/html');
            res.send(data);
        }
    })
});

fastify.get("/coin", (req, res) => {
    
    const {denom = 0, count = 0} = req.query;
    console.log(req.query)
    let coinValue = coinCount({denom, count});

        res.statusCode = 200;
        res.header('Content-Type', 'text/html');
        res.send(`<h2>Value of ${count} of ${denom} is ${coinValue}</h2><br /><a href="/">Home</a>`);
});

fastify.get("/coins", (req, res) => {
    const {option} = req.query
    console.log(option)
    switch(option) {
        case "1":
            console.log("hello")
            coinValue = coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2 }); // option = 1
            break;
        case "2":
            coinValue = coinCount(...coins);    // option = 2
            break;
        case "3":
            coinValue = coinCount(coins);    // Extra credit: option = 3
        default:
            coinValue = 0;
    }

        res.statusCode = 200;
        res.header('Content-Type', 'text/html');
        res.send(`<h2>Option ${option} value is ${coinValue}</h2><br /><a href="/">Home</a>`);
});

const hostname = 'localhost';
const port = 8080;
fastify.listen(port, hostname, () => {
    console.log(`server running @ http://${hostname}:${port}/`)
});

```
Index.html (provided by proffesor):

[index.html](https://lizz02.github.io/cit281-p3/index1.html)

Node.js configuration file:

[package.json](https://lizz02.github.io/cit281-p3/package.json)
