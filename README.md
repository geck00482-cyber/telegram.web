<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="utf-8" />
  <title>MyChat — пример чат-приложения</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <div class="app">
    <aside class="sidebar" aria-label="Список чатов">
      <div class="brand">MyChat</div>
      <div class="searchBox">
        <input type="text" id="search" placeholder="Поиск чатов" />
      </div>
      <ul id="chatList" class="chat-list"></ul>
    </aside>

    <section class="pane" aria-label="Окно чата">
      <header class="chat-header">
        <div class="avatar"></div>
        <div class="title">
          <div class="name" id="chatName">Чат</div>
          <div class="subtitle" id="chatStatus">online</div>
        </div>
      </header>

      <div class="messages" id="messages"></div>

      <form class="composer" id="composer">
        <input type="text" id="messageInput" placeholder="Введите сообщение…" autocomplete="off" />
        <button type="submit">Отправить</button>
      </form>
    </section>
  </div>

  <script src="app.js"></script>
</body>
</html>
────────────────────────────────

styles.css
────────────────────────────────
:root {
  --bg: #0f111a;
  --sidebar: #141923;
  --panel: #1b1f2a;
  --text: #e6e6e6;
  --accent: #4e9af1;
  --bubble: #2b2f3a;
}
* { box-sizing: border-box; }
html, body { height: 100%; margin: 0; background: var(--bg); color: var(--text); font-family: Inter, system-ui, -apple-system, Arial; }

.app { display: flex; height: 100vh; }

.sidebar {
  width: 320px;
  border-right: 1px solid #2a2a2a;
  background: #0f1220;
  padding: 16px;
  display: flex;
  flex-direction: column;
}
.brand { font-weight: bold; font-size: 20px; padding: 8px 0; }
.searchBox { margin: 8px 0 12px; }
#search { width: 100%; padding: 8px 10px; border-radius: 6px; border: 1px solid #333; background: #1a1f2a; color: var(--text); }
.chat-list { list-style: none; padding: 0; margin: 0; overflow: auto; }
.chat-list li { padding: 10px; border-radius: 6px; display: flex; gap: 10px; align-items: center; cursor: pointer; }
.chat-list li:hover { background: #1e2540; }
.chat-list .avatar { width: 28px; height: 28px; border-radius: 50%; background: #333; }
.chat-list .name { font-size: 14px; }

.pane { flex: 1; display: flex; flex-direction: column; height: 100vh; }
.chat-header { display: flex; align-items: center; gap: 12px; padding: 16px; border-bottom: 1px solid #2a2a2a; background: #111522; }
.chat-header .avatar { width: 40px; height: 40px; border-radius: 50%; background: #333; }
.chat-header .title { display: flex; flex-direction: column; }
.chat-header .name { font-weight: bold; }
.chat-header .subtitle { font-size: 12px; color: #aaa; }

.messages { flex: 1; padding: 16px; overflow: auto; background: #0b0e1a; display: flex; flex-direction: column; gap: 6px; }
.bubble { max-width: 70%; padding: 8px 12px; border-radius: 12px; background: #2a2f3a; }
.bubble.me { background: #4e9af1; color: white; align-self: flex-end; }

.composer { display: flex; padding: 12px; border-top: 1px solid #2a2a2a; background: #0b0e1a; }
.composer input { flex: 1; padding: 12px 14px; border-radius: 20px; border: 1px solid #333; background: #1a1f2a; color: #fff; }
.composer button { margin-left: 8px; padding: 12px 16px; border-radius: 20px; border: none; background: #4e9af1; color: #fff; cursor: pointer; }

@media (max-width: 900px) {
  .sidebar { width: 260px; }
  .chat-header { padding: 12px; }
}
────────────────────────────────

app.js
────────────────────────────────
(function() {
  const chats = [
    { id: 1, name: "Канал уведомлений", messages: [
      { from: "system", text: "Добро пожаловать в MyChat!", time: new Date().toLocaleTimeString() }
    ]},
    { id: 2, name: "Друзья", messages: [
      { from: "you", text: "Привет, как дела?", time: "12:01" },
      { from: "them", text: "Отлично, спасибо!", time: "12:02" }
    ]}
  ];

  let currentChat = chats[0];

  const chatListEl = document.getElementById("chatList");
  const messagesEl = document.getElementById("messages");
  const chatNameEl = document.getElementById("chatName");
  const messageInput = document.getElementById("messageInput");
  const composerForm = document.getElementById("composer");

  function renderChatList(filter = "") {
    chatListEl.innerHTML = "";
    const term = filter.toLowerCase();
    const list = chats.filter(c => c.name.toLowerCase().includes(term));
    list.forEach((c) => {
      const li = document.createElement("li");
      li.className = "chat-item";
      li.dataset.id = c.id;
      li.innerHTML = '<span class="avatar"></span><span class="name" style="font-size:14px;">' + c.name + '</span>';
      li.addEventListener("click", () => {
        currentChat = c;
        chatNameEl.textContent = c.name;
        renderMessages();
      });
      chatListEl.appendChild(li);
    });
  }

  function renderMessages() {
    messagesEl.innerHTML = "";
    currentChat.messages.forEach(m => {
      const bubble = document.createElement("div");
      bubble.className = "bubble" + (m.from === "you" ? " me" : "");
      bubble.textContent = m.text;
      messagesEl.appendChild(bubble);
    });
    messagesEl.scrollTop = messagesEl.scrollHeight;
  }

  composerForm.addEventListener("submit", function(e) {
    e.preventDefault();
    const text = messageInput.value.trim();
    if (!text) return;
    currentChat.messages.push({ from: "you", text, time: new Date().toLocaleTimeString() });
    messageInput.value = "";
    renderMessages();

    // Простая симуляция ответа от собеседника
    setTimeout(() => {
      currentChat.messages.push({ from: "them", text: "Спасибо за сообщение!", time: new Date().toLocaleTimeString() });
      renderMessages();
    }, 600);
  });

  // Инициализация
  renderChatList();
  renderMessages();
})()
