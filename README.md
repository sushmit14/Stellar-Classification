use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

```
import React, { useState } from 'react';

const DropdownComponent = () => {
    const [selectedHour, setSelectedHour] = useState('');
    const [selectedDay, setSelectedDay] = useState('');

    const handleHourChange = (event) => {
        setSelectedHour(event.target.value);
    };

    const handleDayChange = (event) => {
        setSelectedDay(event.target.value);
    };

    return (
        <div>
            <label>
                Hours:
                <select value={selectedHour} onChange={handleHourChange}>
                    <option value="">Select Hours</option>
                    <option value="1hour">1 Hour</option>
                    <option value="6hours">6 Hours</option>
                    <option value="12hours">12 Hours</option>
                    <option value="24hours">24 Hours</option>
                </select>
            </label>
            <br />
            <label>
                Days:
                <select value={selectedDay} onChange={handleDayChange}>
                    <option value="">Select Days</option>
                    <option value="1day">1 Day</option>
                    <option value="2days">2 Days</option>
                    <option value="5days">5 Days</option>
                    <option value="14days">14 Days</option>
                    <option value="45days">45 Days</option>
                </select>
            </label>
        </div>
    );
};

export default DropdownComponent;


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