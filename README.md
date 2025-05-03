# reply.sql
sql
CREATE TABLE Groups(
 GroupID INT PRIMARY KEY AUTO_INCREMENT,
    GroupName VARCHAR(100),
    Description VARCHAR(255)
);


You can then link the *Contacts* table with the *Groups* table if you want to organize contacts into categories, like *Family*, *Friends*, etc.

*Step 3: Build Your API*

To build your API, you have two options based on the programming language you're using:

*Option 1: Using Node.js + Express (JavaScript)*

1. *Install Dependencies*:
   - Install *Node.js* and *Express*.
   - Use *mysql2* or *sequelize* to interact with your MySQL database.

2. *Install Packages*:
   bash
   npm install express mysql2 body-parser
   

3. *Create an Express Server*:

javascript
const express = require('express');
const mysql = require('mysql2');
const bodyParser = require('body-parser');

const app = express();
const port = 3000;

// Middleware
app.use(bodyParser.json());

// MySQL connection
const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'password',  // Your MySQL password
  database: 'contact_book'
});

// Connect to MySQL
db.connect((err) => {
  if (err) throw err;
  console.log('Connected to MySQL Database!');
});
// CREATE operation - Add a contact
app.post('/contacts', (req, res) => {
const { firstName, lastName, phoneNumber, email } = req.body;
  const sql = 'INSERT INTO Contacts (FirstName, LastName, PhoneNumber, Email) VALUES (?, ?, ?, ?)';
  db.query(sql, [firstName, lastName, phoneNumber, email], (err, result) => {
    if (err) throw err;
    res.send('Contact added successfully');
  });
});

// READ operation - Get all contacts
app.get('/contacts', (req, res) => {
  const sql = 'SELECT * FROM Contacts';
  db.query(sql, (err, result) => {
    if (err) throw err;
    res.json(result);
  });
});

// UPDATE operation - Update contact details
app.put('/contacts/:id', (req, res) => {
  const { id } = req.params;
  const { firstName, lastName, phoneNumber, email } = req.body;
  const sql = 'UPDATE Contacts SET FirstName = ?, LastName = ?, PhoneNumber = ?, Email = ? WHERE ContactID = ?';
  db.query(sql, [firstName, lastName, phoneNumber, email, id], (err, result) => {
    if (err) throw err;
    res.send('Contact updated successfully');
  });
});

// DELETE operation - Delete a contact
app.delete('/contacts/:id', (req, res) => {
  const { id } = req.params;
  const sql = 'DELETE FROM Contacts WHERE ContactID = ?';
  db.query(sql, [id], (err, result) => {
    if (err) throw err;
    res.send('Contact deleted successfully');
  });
});

// Start the server
app.listen(port, () => {
  console.log(Server running at http://localhost:${port});
});


This will create an API where you can *Add, View, Update, and Delete* contacts using the respective HTTP methods.

*Option 2: Using FastAPI (Python)*

If you prefer Python, you can use *FastAPI* for creating a CRUD API. Here’s how:

1. *Install Dependencies*:
   bash
   pip install fastapi uvicorn mysql-connector-python
   

2. *Create a FastAPI Application*:

python
from fastapi import FastAPI
from pydantic import BaseModel
import mysql.connector

Initialize FastAPI app
app = FastAPI()

MySQL connection
db = mysql.connector.connect(
    host="localhost",
    user="root",
    password="password",  # Your MySQL password
    database="contact_book"
)

Create cursor to interact with MySQL
cursor = db.cursor()

class Contact(BaseModel):
    firstName: str
    lastName: str
    phoneNumber: str
    email: str

@app.post("/contacts/")
def create_contact(contact: Contact):
    query = "INSERT INTO Contacts (FirstName, LastName, PhoneNumber, Email) VALUES (%s, %s, %s, %s)"
    cursor.execute(query, (contact.firstName, contact.lastName, contact.phoneNumber, contact.email))
    db.commit()
    return {"message": "Contact added successfully"}

@app.get("/contacts/")
def get_contacts():
 cursor.execute("SELECT * FROM Contacts")
    result = cursor.fetchall()
    return result

@app.put("/contacts/{contact_id}")
def update_contact(contact_id: int, contact: Contact):
    query = "UPDATE Contacts SET FirstName = %s, LastName = %s, PhoneNumber = %s, Email = %s WHERE ContactID = %s"
    cursor.execute(query, (contact.firstName, contact.lastName, contact.phoneNumber, contact.email, contact_id))
    db.commit()
    return {"message": "Contact updated successfully"}

@app.delete("/contacts/{contact_id}")
def delete_contact(contact_id: int):
    query = "DELETE FROM Contacts WHERE ContactID = %s"
    cursor.execute(query, (contact_id,))
    db.commit()
    return {"message": "Contact deleted successfully"}
```
