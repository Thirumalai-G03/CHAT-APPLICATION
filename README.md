# CHAT-APPLICATION
server.js
const express = require('express');
const app = express();
const http = require('http').createServer(app);
const io = require('socket.io')(http, {
  cors: { origin: "*" }
});

const PORT = 3000;

app.use(express.static('../frontend'));

io.on('connection', (socket) => {
  console.log('A user connected');

  socket.on('chat message', (msg) => {
    io.emit('chat message', msg);
  });

  socket.on('disconnect', () => {
    console.log('User disconnected');
  });
});

http.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});

index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Real-Time Chat</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="chat-container">
    <ul id="messages"></ul>
    <form id="chat-form">
      <input id="message-input" autocomplete="off" placeholder="Type your message..." />
      <button>Send</button>
    </form>
  </div>

  <script src="https://cdn.socket.io/4.0.0/socket.io.min.js"></script>
  <script src="script.js"></script>
</body>
</html>

style.css
body {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.chat-container {
  width: 400px;
  background-color: white;
  padding: 20px;
  border-radius: 10px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  padding: 8px 10px;
  border-bottom: 1px solid #ddd;
}

form {
  display: flex;
}

input {
  flex: 1;
  padding: 10px;
}

button {
  padding: 10px;
}

script.js
const socket = io('http://localhost:3000');

const form = document.getElementById('chat-form');
const input = document.getElementById('message-input');
const messages = document.getElementById('messages');

form.addEventListener('submit', (e) => {
  e.preventDefault();
  if (input.value) {
    socket.emit('chat message', input.value);
    input.value = '';
  }
});

socket.on('chat message', (msg) => {
  const item = document.createElement('li');
  item.textContent = msg;
  messages.appendChild(item);
  window.scrollTo(0, document.body.scrollHeight);
});

