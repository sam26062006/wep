<html><head><base href="." /><title>darkwepmess</title><meta charset="UTF-8" />
    <link rel="icon" href="img.png" type="img.png">
    <style>
    :root {
      --neon-green: #0f0;
      --dark-bg: #000;
      --terminal-font: 'Courier New', monospace;
    }
    
    body {
      margin: 0;
      padding: 0;
      background: var(--dark-bg);
      color: var(--neon-green);
      font-family: var(--terminal-font);
    }
    
    .container {
      max-width: 800px;
      margin: 50px auto;
      padding: 20px;
    }
    
    .login-form, .chat-container {
      background: rgba(0,20,0,0.9);
      border: 1px solid var(--neon-green);
      padding: 20px;
      border-radius: 5px;
      box-shadow: 0 0 10px var(--neon-green);
    }
    
    .contacts-list {
      width: 250px;
      border-right: 1px solid var(--neon-green);
      padding: 10px;
      height: 400px;
      overflow-y: auto;
    }
    
    .contact {
      padding: 10px;
      margin: 5px 0;
      border: 1px solid var(--neon-green);
      cursor: pointer;
      transition: all 0.3s;
    }
    
    .contact:hover {
      background: rgba(0,255,0,0.1);
    }
    
    .contact.active {
      background: var(--neon-green);
      color: var(--dark-bg);
    }
    
    .chat-interface {
      display: flex;
    }
    
    input, button {
      background: var(--dark-bg);
      border: 1px solid var(--neon-green);
      color: var(--neon-green);
      padding: 10px;
      margin: 5px 0;
      width: 100%;
      font-family: var(--terminal-font);
    }
    
    button {
      cursor: pointer;
      transition: all 0.3s;
    }
    
    button:hover {
      background: var(--neon-green);
      color: var(--dark-bg);
    }
    
    .messages {
      height: 300px;
      overflow-y: auto;
      margin: 20px 0;
      padding: 10px;
      border: 1px solid var(--neon-green);
      flex-grow: 1;
    }
    
    .message {
      margin: 5px 0;
      padding: 5px;
      border-left: 2px solid var(--neon-green);
    }
    
    .chat-container {
      display: none;
    }
    
    .file-input {
      display: none;
    }
    
    .upload-btn {
      display: inline-block;
      width: auto;
      margin-right: 10px;
    }
    
    .typing-effect::after {
      content: '|';
      animation: blink 1s infinite;
    }
    
    @keyframes blink {
      0%, 100% { opacity: 1; }
      50% { opacity: 0; }
    }
    
    .glitch {
      animation: glitch 1s infinite;
    }
    
    @keyframes glitch {
      2%, 64% { transform: translate(2px,0) skew(0deg); }
      4%, 60% { transform: translate(-2px,0) skew(0deg); }
      62% { transform: translate(0,0) skew(5deg); }
    }
    
    .chat-main {
      flex-grow: 1;
      padding-left: 20px;
    }
    </style>
    </head>
    <body>
    <div class="container">
      <div id="loginForm" class="login-form">
        <h2 class="glitch">SECURE ACCESS TERMINAL</h2>
        <div class="typing-effect">Enter credentials:</div>
        <input type="text" id="username" placeholder="Username" autocomplete="username">
        <input type="password" id="password" placeholder="Password" autocomplete="current-password">
        <button onclick="login()">INITIALIZE CONNECTION</button>
      </div>
    
      <div id="chatContainer" class="chat-container">
        <h2>SECURE TRANSMISSION CHANNEL</h2>
        <div class="chat-interface">
          <div class="contacts-list" id="contactsList">
            <h3>CONTACTS</h3>
            <!-- Contacts will be added here dynamically -->
          </div>
          <div class="chat-main">
            <div class="messages" id="messages"></div>
            <input type="text" id="messageInput" placeholder="Enter message...">
            <button onclick="sendMessage()">TRANSMIT</button>
            <div class="upload-controls">
              <button class="upload-btn" onclick="document.getElementById('fileInput').click()">UPLOAD FILE</button>
              <input type="file" id="fileInput" class="file-input" accept="audio/*,video/*,image/*">
              <span id="fileName"></span>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <script>
    // Store user credentials and messages in localStorage
    const users = JSON.parse(localStorage.getItem('users')) || {};
    const messages = JSON.parse(localStorage.getItem('messages')) || {};
    let currentChat = null;
    
    function login() {
      const username = document.getElementById('username').value;
      const password = document.getElementById('password').value;
    
      if (!username || !password) {
        alert('ACCESS DENIED: All fields required');
        return;
      }
    
      if (!users[username]) {
        // New user registration
        users[username] = password;
        localStorage.setItem('users', JSON.stringify(users));
      } else if (users[username] !== password) {
        alert('ACCESS DENIED: Invalid credentials');
        return;
      }
    
      // Login successful
      document.getElementById('loginForm').style.display = 'none';
      document.getElementById('chatContainer').style.display = 'block';
      localStorage.setItem('currentUser', username);
      updateContactsList();
    }
    
    function updateContactsList() {
      const contactsList = document.getElementById('contactsList');
      const currentUser = localStorage.getItem('currentUser');
      contactsList.innerHTML = '<h3>CONTACTS</h3>';
      
      Object.keys(users).forEach(user => {
        if (user !== currentUser) {
          const contactDiv = document.createElement('div');
          contactDiv.className = 'contact';
          contactDiv.textContent = user;
          contactDiv.onclick = () => selectContact(user);
          contactsList.appendChild(contactDiv);
        }
      });
    }
    
    function selectContact(user) {
      currentChat = user;
      document.querySelectorAll('.contact').forEach(contact => {
        contact.classList.remove('active');
        if (contact.textContent === user) {
          contact.classList.add('active');
        }
      });
      
      // Load chat history
      loadChatHistory();
    }
    
    function loadChatHistory() {
      const messagesDiv = document.getElementById('messages');
      messagesDiv.innerHTML = '';
      
      const chatKey = getChatKey();
      const chatHistory = messages[chatKey] || [];
      
      chatHistory.forEach(message => {
        const messageElement = document.createElement('div');
        messageElement.className = 'message';
        messageElement.innerHTML = message;
        messagesDiv.appendChild(messageElement);
      });
      
      messagesDiv.scrollTop = messagesDiv.scrollHeight;
    }
    
    function getChatKey() {
      const currentUser = localStorage.getItem('currentUser');
      return [currentUser, currentChat].sort().join('-');
    }
    
    function sendMessage() {
      if (!currentChat) {
        alert('SELECT A CONTACT FIRST');
        return;
      }
      
      const messageInput = document.getElementById('messageInput');
      const message = messageInput.value;
      
      if (!message) return;
    
      const currentUser = localStorage.getItem('currentUser');
      const chatKey = getChatKey();
      
      if (!messages[chatKey]) {
        messages[chatKey] = [];
      }
      
      messages[chatKey].push(`${currentUser}: ${message}`);
      localStorage.setItem('messages', JSON.stringify(messages));
      
      messageInput.value = '';
      loadChatHistory();
    }
    
    // File handling
    document.getElementById('fileInput').addEventListener('change', function(e) {
      if (!currentChat) {
        alert('SELECT A CONTACT FIRST');
        return;
      }
      
      const file = e.target.files[0];
      if (file) {
        const fileName = document.getElementById('fileName');
        fileName.textContent = `Selected: ${file.name}`;
        
        const reader = new FileReader();
        reader.onload = function(e) {
          const currentUser = localStorage.getItem('currentUser');
          let messageContent = '';
          
          if (file.type.startsWith('image/')) {
            messageContent = `<img src="${e.target.result}" alt="Uploaded image" style="max-width: 200px;">`;
          } else if (file.type.startsWith('video/')) {
            messageContent = `<video controls style="max-width: 200px;"><source src="${e.target.result}"></video>`;
          } else if (file.type.startsWith('audio/')) {
            messageContent = `<audio controls><source src="${e.target.result}"></audio>`;
          }
          
          const chatKey = getChatKey();
          if (!messages[chatKey]) {
            messages[chatKey] = [];
          }
          
          messages[chatKey].push(`${currentUser}: [FILE UPLOAD] ${file.name}<br>${messageContent}`);
          localStorage.setItem('messages', JSON.stringify(messages));
          loadChatHistory();
        };
        reader.readAsDataURL(file);
      }
    });
    
    // Check for stored login
    window.onload = function() {
      const currentUser = localStorage.getItem('currentUser');
      if (currentUser && users[currentUser]) {
        document.getElementById('loginForm').style.display = 'none';
        document.getElementById('chatContainer').style.display = 'block';
        updateContactsList();
      }
    };
    </script>
    </body></html>
