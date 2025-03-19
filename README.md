const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const bodyParser = require('body-parser');

const app = express();
app.use(cors());
app.use(bodyParser.json());

mongoose.connect('mongodb://localhost:27017/universaltale', {
    useNewUrlParser: true,
    useUnifiedTopology: true
}).then(() => console.log('MongoDB Connected'))
.catch(err => console.log(err));

const BlogSchema = new mongoose.Schema({
    title: String,
    content: String,
    author: String,
    date: { type: Date, default: Date.now }
});
const Blog = mongoose.model('Blog', BlogSchema);

app.get('/blogs', async (req, res) => {
    const blogs = await Blog.find();
    res.json(blogs);
});

app.post('/blogs', async (req, res) => {
    const newBlog = new Blog(req.body);
    await newBlog.save();
    res.json(newBlog);
});

app.listen(5000, () => console.log('Server running on port 5000'));

// Frontend - React.js (pages/index.js)
import { useEffect, useState } from 'react';
import axios from 'axios';

export default function Home() {
    const [blogs, setBlogs] = useState([]);
    
    useEffect(() => {
        axios.get('http://localhost:5000/blogs')
            .then(res => setBlogs(res.data))
            .catch(err => console.log(err));
    }, []);
    
    return (
        <div>
            <h1>Welcome to UniversalTale</h1>
            <h2>Latest Blogs</h2>
            {blogs.map(blog => (
                <div key={blog._id}>
                    <h3>{blog.title}</h3>
                    <p>{blog.content}</p>
                    <small>By {blog.author}</small>
                </div>
            ))}
        </div>
    );
}

