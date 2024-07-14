use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

```
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.ensemble import IsolationForest

# Load fake login data
fake_login_data = pd.read_csv('fake_login_data.csv')
fake_login_data['login_time'] = pd.to_datetime(fake_login_data['login_time'])

# Feature engineering: convert to hours and extract date
fake_login_data['hour'] = fake_login_data['login_time'].dt.hour
fake_login_data['date'] = fake_login_data['login_time'].dt.date

# Train Isolation Forest model
model = IsolationForest(contamination=0.1)  # Adjust contamination based on expected outlier percentage
model.fit(fake_login_data[['hour']])

# Predict outliers
fake_login_data['anomaly'] = model.predict(fake_login_data[['hour']])
fake_login_data['anomaly'] = fake_login_data['anomaly'].map({1: 0, -1: 1})  # Map 1 to normal and -1 to anomaly

# Save detected outliers to CSV
detected_outliers = fake_login_data[fake_login_data['anomaly'] == 1]
detected_outliers.to_csv('detected_outliers.csv', index=False)


# Calculate a rolling mean for predictions (for the sake of this example)
fake_login_data['rolling_mean'] = fake_login_data['hour'].rolling(window=10).mean()

# Plot results with time on the x-axis
plt.figure(figsize=(14, 8))

# Plot true data
plt.plot(fake_login_data.index, fake_login_data['hour'], label='True Data', color='blue')

# Plot predictions (rolling mean)
plt.plot(fake_login_data.index, fake_login_data['rolling_mean'], label='Predictions', color='orange')

# Plot anomalies
plt.scatter(detected_outliers.index, detected_outliers['hour'], color='red', s=100, label='Anomalies')

plt.xlabel('Index')
plt.ylabel('Hour of the Day')
plt.title('Anomaly Detection using Isolation Forest')
plt.legend()
plt.grid(True)

plt.tight_layout()  # Adjust layout to prevent clipping of labels
plt.show()

print("Detected outliers saved to 'detected_outliers.csv'")
```

```
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Sample dataframe with accessTime column
data = {
    'accessTime': pd.date_range(start='2023-01-01', periods=1000, freq='H')
}
df = pd.DataFrame(data)

# Ensure accessTime is in datetime format (if not already)
df['accessTime'] = pd.to_datetime(df['accessTime'])

# Extract the date and hour from accessTime
df['date'] = df['accessTime'].dt.date
df['hour'] = df['accessTime'].dt.hour

# Set up the plot
plt.figure(figsize=(14, 8))
sns.histplot(data=df, x='hour', hue='date', multiple='stack', palette='tab20', binwidth=1)

# Customize the plot
plt.title('Hourly Distribution of Access Times per Day')
plt.xlabel('Hour of Day')
plt.ylabel('Frequency')
plt.xticks(range(0, 24))

# Show the plot
plt.legend(title='Date', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.tight_layout()
plt.show()
```

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
