# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

const fs = require('fs');

// Function to load JSON data from a file
function loadJson(filePath) {
    const rawData = fs.readFileSync(filePath);
    const data = JSON.parse(rawData);
    return data;
}

// Function to fetch entitlements for a given username
function getEntitlements(data, username) {
    for (const entry of data.Entitlements) {
        if (entry.username === username) {
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
const username = 'abc';

logCurrentTimestamp();

const data = loadJson(filePath);
const entitlements = getEntitlements(data, username);
const lastEntitlement = getLastEntitlement(data, username);

console.log(`Entitlements for ${username}:`, entitlements);
if (lastEntitlement !== null) {
    console.log(`Last entitlement for ${username}:`, lastEntitlement);
} else {
    console.log(`No entitlements found for ${username}.`);