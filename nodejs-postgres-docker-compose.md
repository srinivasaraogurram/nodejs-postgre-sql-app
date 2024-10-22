Sure! Let's create a standalone Node.js application that connects to PostgreSQL running in Docker, and use Docker Compose to set up PostgreSQL and pgAdmin. Here's a step-by-step guide:

### Step 1: Set Up Docker Compose for PostgreSQL and pgAdmin

1. **Create a `docker-compose.yml` file:**
   ```yaml
   version: '3.8'

   services:
     postgres:
       image: postgres:latest
       container_name: postgres
       environment:
         POSTGRES_USER: yourusername
         POSTGRES_PASSWORD: yourpassword
         POSTGRES_DB: mydatabase
       ports:
         - "5432:5432"
       volumes:
         - postgres-data:/var/lib/postgresql/data

     pgadmin:
       image: dpage/pgadmin4
       container_name: pgadmin
       environment:
         PGADMIN_DEFAULT_EMAIL: admin@example.com
         PGADMIN_DEFAULT_PASSWORD: admin
       ports:
         - "8080:80"
       depends_on:
         - postgres

   volumes:
     postgres-data:
   ```

2. **Run Docker Compose:**
   ```bash
   docker-compose up -d
   ```

### Step 2: Connect to PostgreSQL via pgAdmin

1. Open your browser and go to `http://localhost:8080`.
2. Log in with the email `admin@example.com` and password `admin`.
3. Add a new server in pgAdmin:
   - **Name:** PostgreSQL
   - **Host name/address:** postgres
   - **Port:** 5432
   - **Username:** yourusername
   - **Password:** yourpassword

### Step 3: Create the Node.js Application

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

3. **Create an `index.js` file:**
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

This setup will allow you to perform CRUD operations on the `users` table using Node.js and PostgreSQL running in Docker. Let me know if you need any further assistance!
