use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

```
     from flask import Flask, request, jsonify
import pandas as pd
from sklearn.ensemble import IsolationForest
import matplotlib.pyplot as plt
import pyodbc

# Flask application setup
app = Flask(__name__)

# Function to fetch data from MS SQL Server using connection string
def fetch_data_from_sql(accountAccessID, conn_str):
    # Establish connection
    conn = pyodbc.connect(conn_str)
    
    # Example query, replace with your actual query
    query = f"SELECT accessTime FROM login_data WHERE accountAccessID = '{accountAccessID}'"
    df = pd.read_sql(query, conn)
    
    conn.close()
    return df

# Function to perform outlier detection using Isolation Forest
def detect_outliers(data):
    # Feature engineering: convert to hours
    data['hour'] = data['accessTime'].dt.hour
    
    # Train Isolation Forest model
    model = IsolationForest(contamination=0.1)  # Adjust contamination based on expected outlier percentage
    model.fit(data[['hour']])
    
    # Predict outliers
    data['anomaly'] = model.predict(data[['hour']])
    
    # Detected outliers
    detected_outliers = data[data['anomaly'] == -1]
    return detected_outliers

# Flask route to handle POST requests
@app.route('/detect_outliers', methods=['POST'])
def handle_post_request():
    # Get accountAccessID from POST request
    accountAccessID = request.json['accountAccessID']
    
    # Replace with your MS SQL Server connection string
    conn_str = 'DRIVER={SQL Server};SERVER=your_server_name;DATABASE=your_database_name;UID=your_username;PWD=your_password'
    
    # Fetch data from MS SQL Server based on accountAccessID
    data = fetch_data_from_sql(accountAccessID, conn_str)
    
    # Perform outlier detection using Isolation Forest
    detected_outliers = detect_outliers(data)
    
    # Convert detected outliers to JSON format
    outliers_json = detected_outliers.to_json(orient='records')
    
    # Return JSON response with detected outliers
    return jsonify(outliers_json)

if __name__ == '__main__':
    app.run(debug=True, port =6000)

```
```
const { faker } = require('@faker-js/faker');


// Function to generate random login time between 10 AM and 8 PM
function generateRandomTime() {
    const startHour = 10;
    const endHour = 20;
  
    const hour = faker.datatype.number({ min: startHour, max: endHour - 1 });
    const minute = faker.datatype.number({ min: 0, max: 59 });
    const second = faker.datatype.number({ min: 0, max: 59 });
  
    return new Date(2024, 6, 6, hour, minute, second); // Adjust the date as needed
  }
  
  // Function to generate random outlier login time
  function generateOutlierTime() {
    const hour = faker.datatype.boolean() ? faker.datatype.number({ min: 0, max: 9 }) : faker.datatype.number({ min: 21, max: 23 });
    const minute = faker.datatype.number({ min: 0, max: 59 });
    const second = faker.datatype.number({ min: 0, max: 59 });
  
    return new Date(2024, 6, 6, hour, minute, second); // Adjust the date as needed
  }
  
  // Function to generate fake login data
  function generateLoginData(totalEntries, outlierPercentage) {
    const loginData = [];
  
    for (let i = 0; i < totalEntries; i++) {
      const isOutlier = faker.datatype.number({ min: 1, max: 100 }) <= outlierPercentage;
      const loginTime = isOutlier ? generateOutlierTime() : generateRandomTime();
      loginData.push({ loginTime: loginTime.toISOString() });
    }
  
    return loginData;
  }
  
  
  // Generate 100 fake login entries with 5% outliers
  const fakeLoginData = generateLoginData(1000, 5);
  console.log("Generated Login Data:", fakeLoginData);
  
```
