use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

```
const express = require('express');
const bodyParser = require('body-parser');
const Queue = require('queue');

// Create an Express application
const app = express();
const port = 3000;

// Use body-parser middleware to parse JSON bodies
app.use(bodyParser.json());

// Create a queue with default options
const q = Queue({
    concurrency: 1, // Number of concurrent jobs
    autostart: true // Automatically start processing jobs
});

const queueElements = []; // Array to hold current elements in the queue

// Function to add key-value pairs to the queue
function enqueue(username, entitlement) {
    const element = { username, entitlement };
    q.push((cb) => {
        // Simulate an asynchronous job with setTimeout
        setTimeout(() => {
            console.log(`Processed ${username}: ${entitlement}`);
            queueElements.splice(queueElements.indexOf(element), 1); // Remove element once processed
            cb(null, element); // Callback when job is done
        }, 1000);
    });
    queueElements.push(element);
}

// Function to print current elements in the queue
function printQueue(queueElements) {
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
    printQueue(queueElements);
    res.send('Request received and added to queue');
});

// Event listeners for queue
q.on('success', (result, job) => {
    console.log(`Job completed: ${result.username}: ${result.entitlement}`);
});

q.on('error', (error, job) => {
    console.log(`Job failed: ${error}`);
});

// Start the server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});



```