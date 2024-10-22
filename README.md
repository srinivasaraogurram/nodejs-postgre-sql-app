# nodejs-postgre-sql-app

Sure! Let's create a standalone Node.js application that connects to PostgreSQL and performs CRUD operations on a `users` table without using Express.js.

### Step 1: Set Up Your Project

1. **Initialize a new Node.js project:**
   ```bash
   mkdir nodejs-postgres-standalone
   cd nodejs-postgres-standalone
   npm init -y
   ```

2. **Install the `pg` package:**
   ```bash
   npm install pg
   ```

### Step 2: Create the Database and Table

1. **Connect to PostgreSQL and create the `users` table:**
   ```sql
   CREATE DATABASE mydatabase;

   \c mydatabase

   CREATE TABLE users (
       id SERIAL PRIMARY KEY,
       name VARCHAR(100),
       email VARCHAR(100) UNIQUE,
       age INT
   );
   ```

### Step 3: Create the Node.js Application

1. **Create an `index.js` file:**
   ```javascript
   const { Client } = require('pg');

   const client = new Client({
       user: 'yourusername',
       host: 'localhost',
       database: 'mydatabase',
       password: 'yourpassword',
       port: 5432,
   });

   client.connect();

   // Create a new user
   const createUser = async (name, email, age) => {
       try {
           const res = await client.query(
               'INSERT INTO users (name, email, age) VALUES ($1, $2, $3) RETURNING *',
               [name, email, age]
           );
           console.log('User created:', res.rows[0]);
       } catch (err) {
           console.error('Error creating user:', err);
       }
   };

   // Get all users
   const getUsers = async () => {
       try {
           const res = await client.query('SELECT * FROM users');
           console.log('Users:', res.rows);
       } catch (err) {
           console.error('Error getting users:', err);
       }
   };

   // Update a user
   const updateUser = async (id, name, email, age) => {
       try {
           const res = await client.query(
               'UPDATE users SET name = $1, email = $2, age = $3 WHERE id = $4 RETURNING *',
               [name, email, age, id]
           );
           console.log('User updated:', res.rows[0]);
       } catch (err) {
           console.error('Error updating user:', err);
       }
   };

   // Delete a user
   const deleteUser = async (id) => {
       try {
           await client.query('DELETE FROM users WHERE id = $1', [id]);
           console.log('User deleted');
       } catch (err) {
           console.error('Error deleting user:', err);
       }
   };

   // Sample data operations
   (async () => {
       await createUser('John Doe', 'john@example.com', 30);
       await getUsers();
       await updateUser(1, 'Jane Doe', 'jane@example.com', 25);
       await getUsers();
       await deleteUser(1);
       await getUsers();
       client.end();
   })();
   ```

### Step 4: Run the Application

1. **Run your Node.js application:**
   ```bash
   node index.js
   ```

This script will connect to your PostgreSQL database, create a user, retrieve all users, update a user, and delete a user, printing the results to the console. Let me know if you need any further assistance!
