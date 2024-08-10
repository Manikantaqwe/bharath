const express = require('express');
const mongoose = require('mongoose');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 3000;

// Serve static files from the 'public' directory
app.use(express.static(path.join(__dirname, 'public')));

// Middleware for parsing form data
app.use(express.urlencoded({ extended: true }));
app.use(express.json());

// Set view engine and views directory
app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

// Connect to MongoDB
const uri = 'mongodb+srv://chitikilamanikanta:82e1lvNxAAoZNfiy@cluster0.uywihhs.mongodb.net/mydb?retryWrites=true&w=majority&appName=Cluster0';
mongoose.connect(uri, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('Connected to MongoDB Atlas'))
    .catch(err => console.error('Error connecting to MongoDB Atlas:', err));

// Define schema and model
const loginSchema = new mongoose.Schema({
    name: String,
    email: String,
    password: String
});

const Login = mongoose.model('Login', loginSchema);

// Handle form submission
app.post('/register', async (req, res) => {
    const { name, email, password } = req.body;
    console.log(`Received registration: Name: ${name}, Email: ${email}, Password: ${password}`);

    try {
        const newUser = new Login({ name, email, password });
        await newUser.save();
       // res.send('<h1>Registration successfull</h1>');
       res.send(`<div style="font-family: Arial, sans-serif; background-color: #f4f4f4; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0;">
    <div style="background-color: #4CAF50; color: white; padding: 20px; border-radius: 5px; box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); text-align: center; max-width: 400px; width: 100%;">
        <h1 style="margin: 0; font-size: 24px;">Registration Successful!</h1>
        <p style="margin: 10px 0 0; font-size: 16px;">Thank you for registering. You can now <a href="http://localhost:3000/" style="color: #ffffff; text-decoration: underline; font-weight: bold;">log in</a> to your account.</p>
    </div>
</div>`);
    } catch (error) {
        console.error('Error saving user:', error);
        res.status(500).send('<h1>Error saving user</h1>');
    }
});

// Render the index page
app.get('/', (req, res) => {
    res.render('index'); // This should match the file name in 'views'
});

// Start the server
app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
