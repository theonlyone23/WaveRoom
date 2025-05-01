# WaveRoom
//
 Initial project skeleton for a Discord-like app (MVP)

// Backend: Node.js + Express + Socket.IO

const express = require('express');
const http = require('http');
const socketIO = require('socket.io');
const cors = require('cors');

const app = express();
const server = http.createServer(app);
const io = socketIO(server, {
  cors: {
    origin: '*'
  }
});

app.use(cors());
app.use(express.json());

// In-memory store (for demo — replace with a real database)
let users = [];
let messages = [];

// Routes
app.post('/signup', (req, res) => {
  const { username } = req.body;
  if (users.find(u => u.username === username)) {
    return res.status(400).json({ message: 'Username taken' });
  }
  users.push({ username });
  res.json({ message: 'Signup successful' });
});

app.post('/login', (req, res) => {
  const { username } = req.body;
  if (users.find(u => u.username === username)) {
    return res.json({ message: 'Login successful' });
  }
  res.status(400).json({ message: 'User not found' });
});

// WebSocket events
io.on('connection', (socket) => {
  console.log('New client connected');

  socket.on('send_message', (data) => {
    messages.push(data);
    io.emit('receive_message', data);
  });

  socket.on('disconnect', () => {
    console.log('Client disconnected');
  });
});

const PORT = process.env.PORT || 5000;
server.listen(PORT, () => console.log(`Server running on port ${PORT}`));
