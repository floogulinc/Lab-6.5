# Lab 6.5 (NodeJS & programmatic DB access)

## Table of Contents
- [Accessing a database programmatically](#accessing-a-database-programmatically)
  - [Interacting with the database](#interacting-with-the-database)
    - [HTML](#html)
  - [CSS](#css)
  - [Javascript](#javascript)
  - [Server Side Scripting](#server-side-scripting)
    - [Getting it to run](#getting-it-to-run)
    - [Explanations Galore](#explanations-galore)
    - [Tutorials](#tutorials)
  - [To DO](#to-do)
  
# Accessing a database programmatically

Most programming languages have libraries or modules that allow your program to dynamically interact with information stored in a database.  With our end-goal in mind we are going to use the *Node.js* framework.  The reason we are using Node.js is because a lot of infrastructure on the web is built around JavaScript.  The node website explains itself well:


>Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js uses 
an event-driven, non-blocking I/O model that makes it lightweight and efficient. 

>Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries 
in the world.

Node.js (along with various packages) will allows us to
* Create code that changes the contents of your database
* Create code that downloads the results of a query from your database
* Create code the runs an SQL stored procedure on the database server.

In addition, we will also use Node.js to write a simple HTTP server that will provide access to our Point of Sales system.  We will use client-side javascript technology like *Bootstrap* to simplify the process of making our webApp look nice, and we will use 'angular.js' to **bind** various components of our webpage to the data provided by our *node.js* server.  We will also use various package to make our code a bit cleaner and easier to deal with.

## Interacting with the database

These technologies are intimately inter-related with producing web pages, so you are going to want to have a passing familiarity with HTML, CSS, and the use of JavaScript in web pages.  If this is new to you, then I recommend starting with some tutorials.  This is NOT a class in web technology, and you are not expected to master it, but you really should gain a comfortable familiarity with the ideas.  Below is a big-picture explanation along with some links to free tutorials that are available on the internet.  In addition, as a University of Minnesota, Morris student you have access to a variety of lynda tutorials.  Go check out <lynda.umn.edu>.  The HTML Essential Training (for example) is a tutorial on HTML for example.

### HTML

HTML controls the underlying information that is used to render a web page into what you see on your browser.  It is a text file containing a *markup language* that is very similar to XML.  The interaction between the HTML file and your browser is a client-server based model that uses the HTTP (or HTTPS) protocol for communication.  Your browser is the **client** and the web page's address indicates how to communicate with the **server**.  

At the request of the browser the server produces an HTML document.  Like XML, the tags in the HTML file organize data hierarchically into nodes with attributes.  A web browser takes the contents of these nodes and uses their attributes to **render** the data into a web page.  The CSS (Cascading Style Sheets) are documents that influence the appearance of the rendered document, and the JavaScript allows for further control over appearance **and** allows the content and layout of the webpage to be modified dynamically.

The [w3schools HTML(5) Tutorial](http://www.w3schools.com/html/) will give you an overview of how HTML works.  When you are working with your HTML examples you do not need an explicit server.  It is enough to use a browser to open the file.  In fact, if you give your file a `.htm` or `.html` extension then you can double click on the file to see how it will be rendered by the browser.  In fact, if you are using the terminal and are currently in the same directory as your html file then you can open the HTML file by typing.

```
firefox ./myfile.html &
```

The ampersand at the end of the command launches firefox in a new thread.  You do not need to include the final `&`, but if you do not, then you will be unable to use your current terminal session until firefox is closed.

## CSS

As mentioned, Cascading Style Sheets (CSS) control how the contents of the HTML page are meant to be rendered.  These are also text files and special tags in the HTML file refer to them.

The key idea here is that the CSS document associates display properties to specific nodes in the HTML document.  The association is also hierarchical-- so specifying that the <body> ... </body> node should have a 'background-color' of 'red', would apply a background-color of 'red' to all the children of that nodes (in other words to EVERYTHING in the document).  The property can be over-ridden further down the tree.  For example, we could specify that document nodes marked up with `<span>` tags that also have a class attribute of "funky" (`<span class="funky"> ... </span>`) will have their background-color changed to blue).  (Protip:  Browsers are designed to deal with people writing "outside the spec".  Unlike XML which has no innate restrictions on how data should be organized, HTML has many restrictions.  The Browser will do its best to fix any problems it encounters-- this can lead to the structure of your HTML content being a shifted around in ways that you might not expect-- always use the browser's inspector to see the hierarchical structure if things seem to be behaving unexpectedly)

The [w3schools css Tutorial](http://www.w3schools.com/css/) will give you an overview of how CSS works.  Again, we're not aiming for master-- just basic literacy.

## Javascript

If you have **not** had any experience programming in javascript, start with the [w3schools tutorial](http://www.w3schools.com/js/).  The tutorial shows how javascript is an integral part of many web pages.  You can (and should) produce and test the examples yourself (remember-- copy and paste is the enemy of understanding).  All the javascript that you include is part of the HTML document-- either because the script is inline or because it is included.  The tutorial is showing you **Client Side** scripting.  The key idea that makes HTML, CSS and Javascript fit together is the **DOM (Document Object Model)**.  The DOM is the hierarchical data structure built by your browser to encode the information contained in the HTML and CSS files.  The included javascript can modify the content and attributes of various nodes in the DOM which, in turn, modify how the document is rendered.

Newer version of HTTP allow a client to request data from a server without re-rendering the entire tree.  One technology for doing this is known as AJAX.  We are **not** going to be working with AJAX directly-- instead we will use various javascript modules to handle this for us.

## Server Side Scripting

Make sure you are working in a clone of your repository.    Start by generating two files in the empty directory `node`:
* a node.js script called `showDatabases.js`, and
* a json file called `credentials.json` to hold our username and password.  
 
We will run `showDatabases.js` from the command line as follows:

```
node showDatabases.js
```

First the **credentials.json** file:

```json
{
"user" : "testy_pete",
"password" : "not really my password"
}
```

And now for all the magic in **showDatabases.js**:

```js
var credentials = require('./credentials.json');

var mysql=require("mysql");

credentials.host="ids";
var connection = mysql.createConnection(credentials);

connection.connect(function(err){
  if(err){
    console.log("Problems with MySQL: "+err);
  } else {
    console.log("Connected to Database.");
  }
});

connection.query('SHOW DATABASES',function(err,rows,fields){
  if(err){
    console.log('Error looking up databases');
  } else {
    console.log('Returned values were ',rows);
  }
});

connection.end()
console.log("All done now.");
```

You will note that I did not include any comments.  I'll discuss things below, **after** you have typed in the contents of the two files.

### Getting it to run

As-is, the file will not run.  First you need to download the required 'node.js' modules.  In our case we need `mysql`.  So, from the root directory of your project type the following:

```
npm install mysql
```

(If you are familiar with `package.json` files for configuring a node.js app, then feel free to create one.  If you don't know what that means don't worry about it.

Now that the dependencies have been installed, try running it.

I get something like this:

```
$ node showDatabases.js
All done now.
Connected to Database.
Returned values were  [ { Database: 'information_schema' }, { Database: 'test' } ]
```

If you see alot of databases then you did not properly restrict your accounts `GRANTS`.  However, any databases to which your account has been given access should show up... so seeing more than two is not a problem.

### Explanations Galore

If you pay close attention to the output you might notice something a bit odd:  The output *All done now.* appears before everything else.  What's up with that?

Let me simplify the code a bit:
```js
connection=<setup>;
connection.connect(function(err){...});
connection.query('SHOW DATABASES',function(err,rows,fields){...});
connection.end();
console.log("All done now.");
```

The key things I want you to notice are that 

1. The **only** argument given to `connection.connect` is a function.
1. The **two** arguments given to `conneciton.query` are an SQL statement and a function.

Node.js is fundamentally asyncronous.  The run-time executes the commands in the order given:

1. Setup the connection
1. Call the `connect` method
1. Call the `query` method
1. End the connection
1. Say "All done now."

But then, the `connect` method finishes doing its magic and now
* the anonymous callback function is executed (which tells us if we succeeded or failed)
* And **then** the `query` method (which was waiting for `connect()` to finish) could do its job.

Finally, the callback function of `query()` is fired and carries the data with it.

**There was no guarantee** that the "All done now." message would appear first-- we could put the process to sleep for a bit after ending the connection and make sure that our "All done now." message appears later... but why wouuld we?

The important thing to realize now is that **node.js is asynchronous**.  And that you should **be very careful** about making unexamined assumptions about the order in which callback functions will occur.

<a name="nodeEx"></a>**Node Exercise:**  
  * Create the script `summarize-db.js` which will show the name and structure of every every table in every database to which you have access.
  * Add it to your group's repository
  * Don't forget to include the repository URL when you submit on Canvas.

The output should be in the following format (I've included an example further below):
```
---|DATABASE 1>
.....|Name of Table 1>
Description
.....|Name of Table 2>
Description
...
---|DATABASE 2>
...
```

The `Description` should be in the following format:
```
          FieldName: `_fieldname_` (_datatype_)`
```

My program generated a lot of output (because I used an account that can see every database).  Here's what the top of my output looks like:

```
Connected to Database.
        Acquiring data.  This may take a bit...
---|Andy>
......|Andy.Table_A>
        FieldName: `id`         (int(2))
        FieldName: `name`       (text)
......|Andy.Table_B>
        FieldName: `id`         (int(2))
        FieldName: `name`       (text)
......|Andy.fk_A>
        FieldName: `idA`        (int(11))
        FieldName: `foreignId`  (int(11))
......|Andy.fk_B>
        FieldName: `idB`        (int(11))
        FieldName: `text`       (text)
......|Andy.inventory>
        FieldName: `item`       (varchar(255))
        FieldName: `unit`       (varchar(255))
        FieldName: `amount`     (int(11))
        FieldName: `id`         (int(11))
......|Andy.prices>
        FieldName: `id`         (int(11))
        FieldName: `prices`     (decimal(2,0))
        FieldName: `notes`      (text)
......|Andy.randA>
        FieldName: `colA`       (int(11))
        FieldName: `colB`       (int(11))
......|Andy.randB>
        FieldName: `colA`       (int(11))
        FieldName: `colB`       (int(11))
......|Andy.test_field_Lau>
        FieldName: `ColumnUno`  (text)
......|Andy.testinggg>
        FieldName: `data`       (text)
......|Andy.workers>
        FieldName: `EMPLOYEE`   (text)
        FieldName: `MANAGER`    (text)
        FieldName: `JOB`        (text)
        FieldName: `SALARY`     (decimal(10,2))
        FieldName: `YEARS_WORKED`       (decimal(3,1))
---|BBXD>
......|BBXD.CurrentCharacter>
        FieldName: `Name`       (text)
        FieldName: `Class`      (text)
```

I have a few words of wisdom for you:  If you are not careful, the value of **variables in asynchronous function calls can change from underneath you**.  I highly recommend extensive use of the `console.log()` function and be sure to read the following:

<http://stackoverflow.com/questions/20819826/how-to-pass-parameters-to-mysql-query-callback-in-nodejs>

If you get extremely frustrated you might want to look into using packages that use **promises** (we'll talk about that in a later lab).  like `bluebird`.  In the past, many students have found the `async` package helpful:  

<https://blog.engineyard.com/2015/taming-asynchronous-javascript-with-async>

In addition, you need to figure out someway of getting your connection to end gracefully.  Plenty of challenges.  Enough for one week.

Here are a few tutorials explicitly on node.js that might help.  Be certain you make the example servers.  Seriously... start early this is challenging!

### Tutorials

* [Friendly Introduction to node](https://blog.codeship.com/node-js-tutorial/)
* [Introduction to node](https://www.airpair.com/javascript/node-js-tutorial):  You can skip the material on compiling node.js from scratch.  You will also need to replace `**filename` with `__filename` and `**dirname` with `__dirname`
* [Deeper introduction](https://github.com/felixge/node-mysql/):  This will be of more use to you next week.
* [Nice overview](http://excellencemagentoblog.com/blog/2014/05/04/nodejs-async-programming-callbacks-variable-scopes/)

## To DO

- [ ] All the tutorials listed above
- [ ] Read the following links (do sample exercises when appropriate):
  - [ ] https://mariadb.com/kb/en/data-manipulation-kill-connection-query/
  - [ ] [w3schools HTML(5) Tutorial](http://www.w3schools.com/html/)
  - [ ] [w3schools css tutorial](http://www.w3schools.com/css/)
  - [ ] [w3schools tutorial](http://www.w3schools.com/js/)
  - [ ] <http://stackoverflow.com/questions/20819826/how-to-pass-parameters-to-mysql-query-callback-in-nodejs>
  - [ ] <https://blog.engineyard.com/2015/taming-asynchronous-javascript-with-async>
- [ ] Complete the following exercises:
  - [ ] The [node exercise](#nodeEx)
     - [ ] program `showDatabases.js`
     - [ ] program `summarize-db.js`

# Cautions

Many of the node tutorials floating around are based off of 0.8 and we are now at (or past) 0.10.  Here is an example of a working minimal http server:

```js
const http = require('http');

http.createServer(function(req, res){
  res.writeHead(200,{'Content-Type': 'text/plain'});
  res.end('Hello World\n');
}).listen(3456);

console.log('server running on port 3456');
```

If you see the similar (but **different**) definition:

```js
const http = require('http');

http.createServer(function(request,response){
    request.on("end",function(){
        response.writeHead(200,{'Content-Type':'text/plain'});
        response.end('Hello HTTP!');
    });
 }).listen(3456);

console.log('server running on port 3456');
```

then your tutorial is using an older version of node.
