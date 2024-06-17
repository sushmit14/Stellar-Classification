use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

```
const ps = require('ps-node');
const fs = require('fs');
const path = require('path');

// Read the application mappings from the JSON file
const appMappingsPath = path.join(__dirname, 'mappings.json');
const appMappings = JSON.parse(fs.readFileSync(appMappingsPath, 'utf-8'));

// Map to store unique application PPIDs and their last log times
const loggedApplications = new Map();

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

// Function to check for actively running .exe processes
const checkRunningExe = () => {
  ps.lookup({ command: '.exe', psargs: 'ux' }, (err, resultList) => {
    if (err) {
      throw new Error(err);
    }

    // Get current time and date
    const timestamp = new Date();
    const time = formatTime(timestamp);
    const date = formatDate(timestamp);

    // Set to track current running application PPIDs
    const currentAppSet = new Set();

    resultList.forEach((process) => {
      const exeName = process.command.split('\\').pop();
      const ppid = process.ppid;
      const key = `${exeName}-${ppid}`;
      currentAppSet.add(key);

      // Check if the application has already been logged
      if (!loggedApplications.has(key)) {
        // Check if the application has a mapping
        const category = appMappings[exeName] || 'unknown';

        // Log new applications with category from mappings
        const newLogEntry = {
          exeName,
          ppid,
          category,
          time,
          date
        };
        if (appMappings[exeName])
          console.log(`New application detected: ${JSON.stringify(newLogEntry)}\n`);

        // Store the application PPID and its log time
        loggedApplications.set(key, timestamp);
      }
    });

    // Remove applications that are no longer running
    loggedApplications.forEach((logTime, key) => {
      if (!currentAppSet.has(key)) {
        loggedApplications.delete(key);
      }
    });
  });
};

// Periodically check for new processes every 10 seconds
setInterval(checkRunningExe, 10000);

// Initial check on startup
checkRunningExe();


```