use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

```
const express = require('express');
const bodyParser = require('body-parser');

// Create an Express application
const app = express();
const port = 3000;

// Use body-parser middleware to parse JSON bodies
app.use(bodyParser.json());

const queueElements = []; // Array to hold current elements in the queue
let processing = false; // Flag to check if the queue is being processed

// Function to simulate an asynchronous job
function asyncJob(element) {
    return new Promise((resolve) => {
        // Simulate some async work, e.g., a database query or API call
        // Here we just resolve immediately for demonstration purposes
        console.log(`Processing ${element.username}: ${element.entitlement}`);
        resolve();
    });
}

// Function to add key-value pairs to the queue
function enqueue(username, entitlement) {
    const element = { username, entitlement };
    queueElements.push(element);
    if (!processing) {
        processQueue();
    }
}

// Function to process the queue
async function processQueue() {
    processing = true;
    while (queueElements.length > 0) {
        const element = queueElements[0];
        await asyncJob(element);
        console.log(`Processed ${element.username}: ${element.entitlement}`);
        queueElements.shift(); // Remove the processed element
    }
    processing = false;
}

// Function to print current elements in the queue
function printQueue() {
    console.log('Current queue elements:');
    for (const element of queueElements) {
        console.log(`Username: ${element.username}, Entitlement: ${element.entitlement}`);
    }
}

// POST request handler
app.post('/enqueue', (req, res) => {
    const { username, entitlement } = req.body;
    if (!username || !entitlement) {
        return res.status(400).send('Username and entitlement are required');
    }
    enqueue(username, entitlement);
    printQueue();
    res.send('Request received and added to queue');
});

// Start the server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});



```
```
const express = require('express');
const bodyParser = require('body-parser');

// Create an Express application
const app = express();
const port = 3000;

// Use body-parser middleware to parse JSON bodies
app.use(bodyParser.json());

const queueElements = []; // Array to hold current elements in the queue

// Function to add key-value pairs to the queue
function enqueue(username, entitlement) {
    const element = { username, entitlement };
    queueElements.push(element);
    processQueue();
}

// Function to process the queue
function processQueue() {
    if (queueElements.length > 0) {
        const element = queueElements[0];
        setTimeout(() => {
            console.log(`Processed ${element.username}: ${element.entitlement}`);
            queueElements.shift(); // Remove the processed element
            processQueue(); // Process the next element
        }, 1000); // Simulate an asynchronous job
    }
}

// Function to print current elements in the queue
function printQueue() {
    console.log('Current queue elements:');
    for (const element of queueElements) {
        console.log(`Username: ${element.username}, Entitlement: ${element.entitlement}`);
    }
}

// POST request handler
app.post('/enqueue', (req, res) => {
    const { username, entitlement } = req.body;
    if (!username || !entitlement) {
        return res.status(400).send('Username and entitlement are required');
    }
    enqueue(username, entitlement);
    printQueue();
    res.send('Request received and added to queue');
});

// Start the server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```