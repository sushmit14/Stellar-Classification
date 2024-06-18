use# Stellar-Classification

This repository contaions the complete code for Stellar Classification - Stars, Galaxies and Quasars. An ensemble of multiple boosting and bagging classifiers and a snapshot ensembled ANN, with their weights being determined by an analysis of their respective confusion matrices, F-1 scores, and accuracies has been used.

```
const sql = require('msnodesqlv8');

// Connection string for SQL Server LocalDB
const connectionString = 'server=(localdb)\\MSSQLLocalDB;Database=MyDatabase;Trusted_Connection=Yes;Driver={SQL Server Native Client 11.0}';

// SQL query to insert data
const insertQuery = `INSERT INTO YourTableName (ColumnName1, ColumnName2) VALUES (?, ?)`;

const params = ['Value1', 'Value2'];

sql.open(connectionString, (err, conn) => {
    if (err) {
        console.error('Error opening the database connection:', err);
        return;
    }

    conn.query(insertQuery, params, (err, rows) => {
        if (err) {
            console.error('Error executing the insert query:', err);
            return;
        }

        console.log('Data inserted successfully:', rows);
    });
});



```