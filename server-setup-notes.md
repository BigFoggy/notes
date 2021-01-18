1. npm init - creates the JSON file
2. npm install express --save - installs express
	- if downloading  a repository, just do npm install and this will install the dependencies listed in the JSON file.
3. add node_modules/ to the .gitignore folder
	1. optional: add to package.json under "scripts": "start": "node server/server.js"
	2. optional: nodemon --save-dev
4. create a server.js file
```
const express = require('express'); 
const app = express(); 
const PORT = 5000; 
let bodyParser=require('body-parser');
app.use(express.static('server/public')); 
app.use(bodyParser.urlencoded({ extended: true })); 
app.get('/', (req, res) => {res.send( //whatever.. ); });          //a template... replace '/' with whatever data you want to use '/quotes' or whatever
app.post('/', (req, res) => { {req.send( //whatever.. ); });       // a template as well... more to it than this though.
app.listen(PORT, function() { console.log(`listening at http://localhost:${PORT}`);})             //Goes at the bottom no matter what!
```
6. setup your folders in the server folder (the client folders, css, html, js, etc.)
```
├── server/ 
│   ├── public/ 
│   │   ├── scripts/ 
│   │   │   └── client.js 
│   │   ├── vendors/ 
│   │   │   └── jquery.js 
│   │   ├── styles/ 
│   │   │   └── style.css 
│   │   └── index.html 
│   └── server.js 
├── node_modules/ 
│   ├── express/ 
│   └── ... 
└── .gitignore
```
7. add an array of dummy data
8. configure the app.get method to get all of the data, change the URL and send back the data as JSON. Here are some examples:
```
app.get('/currency', function(req, res){ 
    // currency() is required at the top of this file 
    let responseString = currency(); 
    // call the currency function() and send it back 
    // to the browser via res.send() 
    res.send(responseString); 
});
```
- Note that you can only send one response, so set your data up in such a way that it can be sent in one variable
```
// http://localhost:5000/quote would go here 
    app.get('/quote',function(req,res){ 
    console.log('Request for /quote was made'); 
    res.send(getNextQuote()); 
}); 
```
9. TEST with postman!
10. set up your server logic using modules. Decide what your server data will be and have it send back the results you want it to send back. After confirming the GET results that you want, work on the POST data to be sent back.
11. configure the post method. Here are some examples:
```
app.post('/',(req,res)=>{ 
    let quote = req.body.quote_to_add; 
    console.log(`Adding new quote: `, quote); 
    quotesData.push(quote); 
    res.sendStatus(201); 
});
```

```
app.post('/players', (req, res) => { 
    const player = req.body; 
    console.log('incoming player: ', player); 
    //if we are missing a field, send it back 
    if (!player.firstName || !player.lastName || !player.born) { 
        res.status(400).send('Invalid player, please try again'); 
        return; 
    } 
    addPlayer(player); 
    res.status(201).send('Player added successfully!'); 
})
```
12. TEST with postman!
13. Add $.ajax calls on client.js to GET and POST. Here is an example GET ajax method:
```
function getAllQuotes() { 
    $.ajax({ 
        method: 'GET', 
        url: '/quotes/', 
    }).then(function(quotes) { 
        // `quotes` will be an array of quote objects 
        // [ { text: '...', author: '...' }, {...}, {...} ] 
        console.log('We successfully made the GET /quotes request!', quotes); 
        // empty the ul 
        // loop over all the quotes in the array 
        // append each one to the array 
        $('#quotes-list').empty(); 
        for (let quote of quotes) { 
            $('#quotes-list').append(` 
            <li><em>"${quote.text}"</em> --<strong>${quote.author}</strong></li> 
         `) 
        } 
    }).catch(function(error) { 
        // runs if we get a server 500 or some other error, like connection refused 
        console.log(error); 
        alert('Couldnt get quotes'); 
    }); 
}
```
- And Another
```
function getRandomQuote() { 
    // Do an HTTP request to the server to get a random quote 
    // When the server sends the quote to us, put it on the DOM 
    $.ajax({ 
        method: 'GET', 
        url: '/quotes/random', 
        // no data needed because there is no http body on our GET request 
    }).then(function(response) { 
        /* 
            response will be a single quote object like: 
            { 
                text: "hello world", 
                author: "programmers everywhere" 
            } 
        */ 
        // runs if the request was successful (2xx status code back) 
        console.log('We successfully made the random-quote request!', response); 
        $('#random-quote').empty(); 
        $('#random-quote').append(` 
            <blockquote> 
            "${response.text}" --${response.author} 
            </blockquote> 
        `); 
    }).catch(function(error) { 
        // runs if we get a server 500 or some other error, like connection refused 
        console.log(error); 
        alert('Couldnt get quotes'); 
    }); 
}
```
14. add $.ajax calls to post the data. Here is an example:
```
function addQuote() { 
    // Get the values from the input boxes using jQuery 
    const quoteText = $('#quote-text').val(); 
    const quoteAuthor = $('#quote-author').val(); 
    console.log(quoteText, quoteAuthor); 
    // Ship those values off to the server via POST /quotes 
    $.ajax({ 
        method: 'POST', 
        url: '/quotes/', 
        // Pass in a javascript object that you want to have 
        // in the HTTP BODY. In this case, we want it to be 
        // a quote object with two keys: text and author. 
        data: { 
            text: quoteText, // $('#quote-text').val() 
            author: quoteAuthor, // $('#quote-author').val() 
        } 
    }).then(function(response) { 
        // POST endpoint doesnt send us back anything meaningful 
        // we we just ignore the first parameter 
        $('#quote-text').val(''); 
        $('#quote-author').val(''); 
        getAllQuotes(); 
    }).catch(function(error) { 
        console.log(error); 
        alert('Couldnt create a new quote'); 
    });
```

```
function addPlayer() { 
    const firstName = $('#firstName').val(); 
    const lastName = $('#lastName').val(); 
    const born = $('#born').val(); 
    console.log('from addPlayer()', firstName, lastName, born); 
    $.ajax({ 
        method: 'POST', 
        url: '/players', // might be '/players/' 
        data: { 
            firstName: firstName, 
            lastName: lastName, 
            born: born, 
        } 
    }).then(function (response) { 
        $('#firstName').val(''); 
        $('#lastName').val(''); 
        $('#born').val(''); 
        $('#playerTableBody').empty(); 
        getPlayerData(); 
    }).catch(function (error) { 
        console.log(error); 
        alert('couldnt add a new player'); 
    }) 
}
```