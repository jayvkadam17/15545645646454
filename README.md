
Step 1: Set up the project
1. Create a new directory for your project.
2. Open a terminal or command prompt, navigate to the project directory, and run the following command to initialize a new Node.js project:
```
npm init -y
```
3. Install the required dependencies by running the following commands:
```
npm install express mongoose body-parser bcrypt jsonwebtoken
```

Step 2: Create the Express.js server
Create a file named `server.js` and add the following code:
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');

const app = express();
const port = process.env.PORT || 3000;

// Middleware
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

// Connect to MongoDB
mongoose.connect('mongodb://localhost/mydatabase', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connected to MongoDB'))
  .catch(err => console.log('Failed to connect to MongoDB', err));

// Start the server
app.listen(port, () => console.log(`Server is running on port ${port}`));
```

Step 3: Define the User model
Create a file named `models/user.js` and add the following code:
```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true,
    unique: true
  },
  password: {
    type: String,
    required: true
  }
});

module.exports = mongoose.model('User', userSchema);
```

Step 4: Implement user registration and login endpoints
In `server.js`, add the following code to define the registration and login endpoints:
```javascript
const User = require('./models/user');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');

// User registration
app.post('/api/register', async (req, res) => {
  try {
    const { name, email, password } = req.body;

    // Check if the email already exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({ message: 'Email already exists' });
    }

    // Hash the password
    const hashedPassword = await bcrypt.hash(password, 10);

    // Create a new user
    const user = new User({
      name,
      email,
      password: hashedPassword
    });

    // Save the user to the database
    await user.save();

    res.status(201).json({ message: 'User registered successfully' });
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Internal server error' });
  }
});

// User login
app.post('/api/login', async (req, res) => {
  try {
    const { email, password } = req.body;

    // Check if the user exists
    const user = await User.findOne({ email });
    if (!user) {
      return res.status(400).json({ message: 'Invalid email or password' });
    }

    // Check if the password is correct
    const passwordMatch = await bcrypt.compare(password, user.password);
    if (!passwordMatch) {
      return res.status(400).json({ message: 'Invalid email or password' });
    }

    // Create a JWT token
    const token = jwt.sign({ userId: user._id }, 'secretkey');

    res.json({ token });
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Internal server error' });
  }
});
```

Step 5: Implement API documentation
Create a README.md file in your project directory and add the API documentation, including endpoints, request/response formats, and error handling.

Step 6: Review and submit
Review your code to ensure it follows best practices and is well-structured. Double-check the functionality, error handling, and documentation. Finally, upload your code to GitHub, including the API documentation in the README file.

That's it! You've implemented a RESTful API using Express.js and MongoDB for handling user-related operations. Remember to handle other user-related operations like fetching user information, updating user details, etc., according to your requirements.
