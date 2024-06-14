use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

 pp=== username) {
            return entry.entitlements;
        }
    }
    return [];
}

// Function to get the last entitlement for a given username
function getLastEntitlement(data, username) {
    const entitlements = getEntitlements(data, username);
    if (entitlements.length > 0) {
        return entitlements[entitlements.length - 1];
    }
    return null;
}

// Function to log the current timestamp in HH:mm:ss format
function logCurrentTimestamp() {
    const now = new Date();
    const hours = String(now.getHours()).padStart(2, '0');
    const minutes = String(now.getMinutes()).padStart(2, '0');
    const seconds = String(now.getSeconds()).padStart(2, '0');
    console.log(`Current Timestamp: ${hours}:${minutes}:${seconds}`);
}

// Example usage
const filePath = 'path/to/your/file.json';
const usern

var express = require('express');
var app = express();
var server = require('http').createServer(app);

// Object to store user groups
var userGroupsMap = {};

app.use(function (req, res, next) {
  var nodeSSPI = require('node-sspi');
  var nodeSSPIObj = new nodeSSPI({
    retrieveGroups: true,
  });
  nodeSSPIObj.authenticate(req, res, function (err) {
    if (!res.finished) {
      // Store user groups in the userGroupsMap object
      if (req.connection.user && req.connection.userGroups) {
        userGroupsMap[req.connection.user] = req.connection.userGroups;
      }
      next();
    }
  });
});

app.use(function (req, res, next) {
  if (req.connection.user) {
    var out =
      'Hello ' +
      req.connection.user +
      '! Your sid is ' +
      req.connection.userSid +
      ' and you belong to the following groups:<br/><ul>';
    if (req.connection.userGroups) {
      for (var i in req.connection.userGroups) {
        out += '<li>' + req.connection.userGroups[i] + '</li><br/>\n';
      }
    }
    out += '</ul>';
    res.send(out);
  } else {
    res.send('User not authenticated');
  }
});

// Handle GET requests to check entitlements
app.get('/check-entitlement', function (req, res) {
  var user = req.connection.user;
  var entitlement = req.query.entitlement;

  if (!user) {
    res.status(401).send('User not authenticated');
    return;
  }

  var userGroups = userGroupsMap[user];

  if (userGroups && userGroups.includes(entitlement)) {
    res.send(`User ${user} has the entitlement ${entitlement}`);
  } else {
    res.status(403).send(`User ${user} does not have the entitlement ${entitlement}`);
  }
})

import React from 'react';
import { BrowserRouter as Router, Route, Routes, useNavigate } from 'react-router-dom';
import WeatherButton from './WeatherButton';
import Welcome from './Welcome';

function App() {
    return (
        <Router>
            <Routes>
                <Route path="/" element={<WeatherButton />} />
                <Route path="/welcome" element={<Welcome />} />
            </Routes>
        </Router>
    );
}

export default App;


import React from 'react';
import { useNavigate } from 'react-router-dom';

const WeatherButton = () => {
    const navigate = useNavigate();

    const handleClick = async () => {
        try {
            const response = await fetch('http://localhost:3000/check-entitlement');
            if (response.ok) {
                navigate('/welcome');
            } else {
                console.error('Entitlement check failed.');
            }
        } catch (error) {
            console.error('Error:', error);
        }
    };

    return (
        <div>
            <button onClick={handleClick}>Weather</button>
        </div>
    );
};

export default WeatherButton;

```
const os = require('os');
const EventLog = require('windows-eventlog').EventLog;

// Dictionary to store process information
const processDictionary = {};

// Function to get current timestamp in hh:mm:ss format
const formatTime = (date) => {
  const hours = String(date.getHours()).padStart(2, '0');
  const minutes = String(date.getMinutes()).padStart(2, '0');
  const seconds = String(date.getSeconds()).padStart(2, '0');
  return `${hours}:${minutes}:${seconds}`;
};

// Function to get current date in YYYY-MM-DD format
const formatDate = (date) => {
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const day = String(date.getDate()).padStart(2, '0');
  return `${year}-${month}-${day}`;
};

// Create an EventLog instance
const eventLog = new EventLog('Security');

// Listen for new event log entries
eventLog.on('event', (event) => {
  if (event.EventID === 4688) { // Event ID for process creation
    const exeName = event.InsertionStrings[6].toLowerCase(); // Extract the process name
    if (!processDictionary[exeName] && exeName.endsWith('.exe')) {
      const timestamp = new Date();
      const time = formatTime(timestamp);
      const date = formatDate(timestamp);
      const user = os.userInfo().username;

      processDictionary[exeName] = {
        user,
        time,
        date,
      };

      console.log(`New exe detected: ${exeName}`);
      console.log('Process Dictionary:', processDictionary);
    }
  }
});

// Start listening to the event log
eventLog.start();


```