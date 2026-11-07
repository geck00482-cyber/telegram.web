
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Telegram Web</title>
<style>
    /* ================= БАЗОВЫЕ ПЕРЕМЕННЫЕ (ТЕМА) ================= */
    :root {
        --bg-body: #e6ebee;
        --bg-card: #fff;
        --bg-sidebar: #fff;
        --bg-chat: #e6ebee;
        --bg-input: #f4f4f5;
        --text-main: #222;
        --text-muted: #707579;
        --text-light: #a2acb4;
        --border: #e6ebee;
        --accent: #3390ec;
        --accent-hover: #2b7cd3;
        --msg-in: #fff;
        --msg-out: #effdde;
        --msg-text: #222;
        --overlay: rgba(0,0,0,0.4);
    }

    /* Тёмная тема */
    body.dark-theme {
        --bg-body: #212121;
        --bg-card: #2b2b2b;
        --bg-sidebar: #212121;
        --bg-chat: #0f0f0f;
        --bg-input: #2b2b2b;
        --text-main: #e1e1e1;
        --text-muted: #a2acb4;
        --text-light: #707579;
        --border: #3a3a3a;
        --accent: #52a8ff;
        --accent-hover: #3a8de0;
        --msg-in: #2b2b2b;
        --msg-out: #2b5278;
        --msg-text: #fff;
        --overlay: rgba(0,0,0,0.7);
    }

    * { margin: 0; padding: 0; box-sizing: border-box;
        font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; }

    body {
        background: var(--bg-body);
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        padding: 20px;
        overflow: hidden;
        transition: background 0.3s;
    }

    .card {
        background: var(--bg-card);
        width: 100%;
        max-width: 400px;
        border-radius: 10px;
        box-shadow: 0 2px 10px rgba(0,0,0,0.08);
        padding: 40px 30px 30px;
        position: relative;
        overflow: hidden;
        transition: background 0.3s;
    }

    .top-bar { position: absolute; top: 14px; right: 14px; }
    .close-btn {
        width: 32px; height: 32px;
        border: none; background: transparent;
        color: var(--text-light); font-size: 22px;
        cursor: pointer; border-radius: 50%;
        transition: background 0.15s;
    }
    .close-btn:hover { background: var(--bg-input); }

    .logo { width: 90px; height: 90px; margin: 0 auto 18px; }
    .logo svg { width: 100%; height: 100%; }

    h1 {
        font-size: 21px; font-weight: 500; color: var(--text-main);
        text-align: center; margin-bottom: 8px;
    }
    .subtitle {
        font-size: 14px; color: var(--text-muted);
        text-align: center; margin-bottom: 26px; line-height: 1.4;
    }

    .field { position: relative; margin-bottom: 18px; }
    .field input {
        width: 100%; padding: 15px 16px;
        border: 1px solid #dadce0; border-radius: 10px;
        font-size: 16px; outline: none;
        transition: border-color 0.15s; background: var(--bg-card);
        color: var(--text-main);
    }
    .field input:focus { border-color: var(--accent); }
    .field input.error { border-color: #e53935; }

    .code-input {
        width: 100%; padding: 15px 16px;
        border: 1px solid #dadce0; border-radius: 10px;
        font-size: 22px; letter-spacing: 8px;
        text-align: center; outline: none;
        transition: border-color 0.15s;
        background: var(--bg-card); color: var(--text-main);
    }
    .code-input:focus { border-color: var(--accent); }
    .code-input.error { border-color: #e53935; }

    .btn {
        width: 100%; padding: 14px;
        background: var(--accent); color: #fff;
        border: none; border-radius: 10px;
        font-size: 16px; font-weight: 500;
        cursor: pointer; transition: background 0.15s; margin-top: 6px;
    }
    .btn:hover { background: var(--accent-hover); }
    .btn:disabled { background: #a8c7e8; cursor: default; }

    .link-btn {
        display: block; width: 100%; margin-top: 16px;
        padding: 8px; background: transparent; border: none;
        color: var(--accent); font-size: 14px;
        cursor: pointer; text-align: center;
    }
    .link-btn:hover { text-decoration: underline; }
    .link-btn:disabled { color: var(--text-light); cursor: default; text-decoration: none; }

    .step { display: none; animation: fade 0.25s ease; }
    .step.active { display: block; }
    @keyframes fade {
        from { opacity: 0; transform: translateX(12px); }
        to   { opacity: 1; transform: translateX(0); }
    }

    .error-text {
        color: #e53935; font-size: 13px;
        margin: -8px 0 14px; text-align: left; display: none;
    }
    .error-text.show { display: block; }

    .footnote {
        margin-top: 22px; font-size: 12px;
        color: var(--text-light); text-align: center; line-height: 1.4;
    }

    /* ================= ЭКРАН С ЧАТАМИ ================= */
    .app {
        display: none;
        width: 100%;
        max-width: 1000px;
        height: 90vh;
        max-height: 720px;
        background: var(--bg-card);
        border-radius: 10px;
        box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        overflow: hidden;
        grid-template-columns: 320px 1fr;
        position: relative;
        transition: background 0.3s;
    }
    .app.active { display: grid; }

    /* --- Боковая панель меню (Сайдбар) --- */
    .menu-overlay {
        display: none; position: fixed;
        top: 0; left: 0; width: 100%; height: 100%;
        background: var(--overlay); z-index: 100;
        animation: fadeIn 0.2s ease;
    }
    .menu-overlay.show { display: block; }
    @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

    .menu-drawer {
        position: absolute; top: 0; left: -300px;
        width: 280px; height: 100%;
        background: var(--bg-sidebar);
        z-index: 101; box-shadow: 4px 0 10px rgba(0,0,0,0.1);
        display: flex; flex-direction: column;
        transition: left 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
    }
    .menu-drawer.show { left: 0; }

    .menu-header {
        padding: 20px 16px; border-bottom: 1px solid var(--border);
        display: flex; align-items: center; gap: 12px;
    }
    .menu-header .avatar {
        width: 56px; height: 56px; border-radius: 50%;
        background: var(--accent); color: white;
        display: flex; justify-content: center; align-items: center;
        font-size: 22px; font-weight: bold;
    }
    .menu-header-info { flex: 1; }
    .menu-header-name { font-size: 16px; font-weight: 600; color: var(--text-main); }
    .menu-header-phone { font-size: 13px; color: var(--text-muted); margin-top: 2px; }

    .menu-items { flex: 1; overflow-y: auto; padding: 8px 0; }
    .menu-item {
        display: flex; align-items: center; gap: 14px;
        padding: 14px 16px; cursor: pointer;
        font-size: 15px; color: var(--text-main);
        transition: background 0.15s;
    }
    .menu-item:hover { background: var(--bg-input); }
    .menu-item-icon { font-size: 18px; width: 24px; text-align: center; }

    /* --- Настройки профиля (внутри меню) --- */
    .menu-settings {
        display: none; flex-direction: column;
        padding: 16px; gap: 16px;
        animation: slideRight 0.2s ease;
    }
    .menu-settings.show { display: flex; }
    @keyframes slideRight { from { transform: translateX(-10px); opacity: 0; } to { transform: translateX(0); opacity: 1; } }
    .settings-title { font-size: 18px; font-weight: 600; color: var(--text-main); margin-bottom: 8px; }
    .settings-row {
        display: flex; justify-content: space-between; align-items: center;
        padding: 12px 0; border-bottom: 1px solid var(--border);
    }
    .settings-label { font-size: 15px; color: var(--text-main); }
    
    /* Переключатель (Toggle) */
    .toggle {
        position: relative; width: 44px; height: 24px;
        background: #ccc; border-radius: 12px;
        cursor: pointer; transition: background 0.3s;
    }
    .toggle.on { background: var(--accent); }
    .toggle::after {
        content: ''; position: absolute;
        top: 2px; left: 2px; width: 20px; height: 20px;
        background: white; border-radius: 50%;
        transition: transform 0.3s;
    }
    .toggle.on::after { transform: translateX(20px); }

    .input-group { display: flex; flex-direction: column; gap: 6px; }
    .input-group input {
        padding: 10px; border: 1px solid var(--border);
        border-radius: 8px; font-size: 14px; outline: none;
        background: var(--bg-input); color: var(--text-main);
    }
    .btn-sm {
        padding: 10px; background: var(--accent); color: white;
        border: none; border-radius: 8px; font-size: 14px;
        cursor: pointer; font-weight: 500;
    }
    .btn-sm:hover { background: var(--accent-hover); }
    .back-menu {
        display: flex; align-items: center; gap: 8px;
        padding: 12px 16px; cursor: pointer;
        font-size: 15px; font-weight: 500; color: var(--accent);
        border-bottom: 1px solid var(--border);
    }

    /* --- Левая колонка со списком чатов --- */
    .sidebar {
        display: flex; flex-direction: column;
        border-right: 1px solid var(--border);
        background: var(--bg-sidebar); min-height: 0;
        transition: background 0.3s;
    }
    .sidebar-header {
        display: flex; align-items: center; gap: 10px;
        padding: 12px 14px; border-bottom: 1px solid var(--border);
    }
    .menu-btn {
        width: 36px; height: 36px; border: none; background: transparent;
        border-radius: 50%; cursor: pointer; color: var(--text-muted);
        font-size: 18px; display: flex; align-items: center; justify-content: center;
    }
    .menu-btn:hover { background: var(--bg-input); }
    .search-box {
        flex: 1; display: flex; align-items: center;
        background: var(--bg-input); border-radius: 20px;
        padding: 8px 14px; gap: 8px;
    }
    .search-box input {
        border: none; background: transparent; outline: none;
        font-size: 14px; width: 100%; color: var(--text-main);
    }
    .search-box input::placeholder { color: var(--text-light); }

    .chat-list { flex: 1; overflow-y: auto; }
    .chat-item {
        display: flex; align-items: center; gap: 12px;
        padding: 10px 14px; cursor: pointer;
        transition: background 0.12s;
    }
    .chat-item:hover { background: var(--bg-input); }
    .chat-item.active { background: var(--accent); color: #fff; }
    .chat-item.active .chat-preview,
    .chat-item.active .chat-time { color: rgba(255,255,255,0.8); }

    .avatar {
        width: 46px; height: 46px; border-radius: 50%;
        display: flex; align-items: center; justify-content: center;
        font-size: 24px; font-weight: 500; color: #fff; flex-shrink: 0;
        background: #ccc;
    }
    .chat-info { flex: 1; min-width: 0; }
    .chat-top {
        display: flex; justify-content: space-between;
        align-items: baseline; gap: 6px;
    }
    .chat-name {
        font-size: 15px; font-weight: 500;
        white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
    }
    .chat-time { font-size: 12px; color: var(--text-light); flex-shrink: 0; }
    .chat-preview {
        font-size: 13px; color: var(--text-muted);
        white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
        margin-top: 2px;
    }

    .chat-panel {
        display: flex; flex-direction: column;
        background: var(--bg-chat); min-height: 0;
        transition: background 0.3s;
    }
    .chat-panel-header {
        display: flex; align-items: center; gap: 12px;
        padding: 10px 18px; background: var(--bg-sidebar);
        border-bottom: 1px solid var(--border);
        transition: background 0.3s;
    }
    .back-btn {
        display: none;
        background: transparent; border: none;
        font-size: 20px; color: var(--text-muted);
        cursor: pointer; margin-right: 4px;
    }
    .chat-panel-header .chat-name { font-size: 15px; color: var(--text-main); }
    .chat-panel-header .status { font-size: 13px; color: var(--text-muted); margin-top: 1px; }

    .messages {
        flex: 1; overflow-y: auto; padding: 20px;
        display: flex; flex-direction: column; gap: 8px;
    }
    .msg {
        max-width: 75%; padding: 8px 12px; border-radius: 12px;
        font-size: 14px; line-height: 1.4; word-wrap: break-word;
        position: relative; animation: pop 0.2s ease;
    }
    @keyframes pop {
        from { opacity: 0; transform: translateY(6px); }
        to   { opacity: 1; transform: translateY(0); }
    }
    .msg.in {
        align-self: flex-start;
        background: var(--msg-in); color: var(--msg-text);
        border-bottom-left-radius: 4px;
    }
    .msg.out {
        align-self: flex-end;
        background: var(--msg-out); color: var(--msg-text);
        border-bottom-right-radius: 4px;
    }
    .msg .time {
        font-size: 11px; color: var(--text-light);
        margin-left: 8px; float: right; margin-top: 4px;
    }
    
    .typing-indicator {
        display: none; align-self: flex-start;
        background: var(--msg-in); padding: 10px 14px; border-radius: 12px;
        border-bottom-left-radius: 4px; margin-bottom: 8px;
    }
    .typing-indicator.show { display: flex; gap: 4px; }
    .typing-dot {
        width: 6px; height: 6px; background: var(--text-light); border-radius: 50%;
        animation: bounce 1.4s infinite ease-in-out both;
    }
    .typing-dot:nth-child(1) { animation-delay: -0.32s; }
    .typing-dot:nth-child(2) { animation-delay: -0.16s; }
    @keyframes bounce {
        0%, 80%, 100% { transform: scale(0); }
        40% { transform: scale(1); }
    }

    .composer {
        display: flex; align-items: center; gap: 10px;
        padding: 12px 18px; background: var(--bg-sidebar);
        border-top: 1px solid var(--border);
        transition: background 0.3s;
    }
    .composer input {
        flex: 1; padding: 12px 16px;
        border: none; background: var(--bg-input);
        border-radius: 20px; font-size: 14px; outline: none;
        color: var(--text-main);
    }
    .composer input:focus { background: var(--bg-input); filter: brightness(0.95); }
    .send-btn {
        width: 44px; height: 44px; border-radius: 50%;
        background: var(--accent); color: #fff; border: none;
        font-size: 18px; cursor: pointer; flex-shrink: 0;
        display: flex; align-items: center; justify-content: center;
        transition: background 0.15s;
    }
    .send-btn:hover { background: var(--accent-hover); }

    @media (max-width: 700px) {
        body { padding: 0; background: var(--bg-sidebar); }
        .card {
            max-width: 100%; height: 100vh;
            border-radius: 0; box-shadow: none;
            display: flex; flex-direction: column; justify-content: center;
        }
        .app {
            max-width: 100%; height: 100vh; max-height: none;
            border-radius: 0; box-shadow: none;
            grid-template-columns: 1fr;
        }
        .sidebar { display: none; width: 100%; }
        .sidebar.show { display: flex; }
        .chat-panel { display: none; width: 100%; }
        .chat-panel.show { display: flex; }
        .back-btn { display: block; }
        .chat-panel-header { padding: 10px 14px; }
        .composer { padding: 10px 14px; }
        
        /* На телефоне меню выезжает поверх */
        .menu-drawer { width: 85%; max-width: 320px; }
    }
</style>
</head>
<body>

<!-- ================= ОКНО ВХОДА ================= -->
<div class="card" id="auth-card">
    <div class="top-bar">
        <button class="close-btn" title="Закрыть" onclick="alert('Это демонстрационная страница.')">×</button>
    </div>
    <div class="logo">
        <svg viewBox="0 0 240 240" xmlns="http://www.w3.org/2000/svg">
            <defs>
                <linearGradient id="tg" x1="0%" y1="0%" x2="100%" y2="100%">
                    <stop offset="0%" stop-color="#37AEE2"/>
                    <stop offset="100%" stop-color="#1E96C8"/>
                </linearGradient>
            </defs>
            <circle cx="120" cy="120" r="120" fill="url(#tg)"/>
            <path fill="#FFFFFF" d="M54.3 118.8l155.9-60.1c7.2-2.7 13.5 1.7 11.1 12.1l-26.5 125.1c-2 9-7.3 11.2-14.8 7l-40.9-30.2-19.7 19c-2.2 2.2-4 4-8.2 4l2.9-41.7 75.9-68.6c3.3-2.9-.7-4.5-5.1-1.6l-93.8 59.1-40.4-12.6c-8.8-2.8-9-8.8 1.6-12.1z"/>
        </svg>
    </div>

    <div class="step active" id="step-phone">
        <h1>Вход в Telegram</h1>
        <p class="subtitle">Введите номер телефона, чтобы<br>получить код подтверждения</p>
        <div class="field">
            <input type="tel" id="phone" placeholder="Номер телефона" autocomplete="tel">
        </div>
        <div class="error-text" id="phone-error">Введите корректный номер телефона</div>
        <button class="btn" onclick="sendCode()">Получить код</button>
        <div class="footnote">Демонстрационная страница. Данные никуда не отправляются.</div>
    </div>

    <div class="step" id="step-code">
        <h1>Введите код</h1>
        <p class="subtitle" id="code-subtitle">Мы отправили код в Telegram</p>
        <div class="field">
            <input type="tel" inputmode="numeric" maxlength="5" class="code-input"
                   id="code" placeholder="•••••" autocomplete="one-time-code">
        </div>
        <div class="error-text" id="code-error">Неверный код. Попробуйте ещё раз.</div>
        <button class="btn" onclick="verifyCode()">Войти</button>
        <button class="link-btn" id="resend-btn" onclick="resendCode()" disabled>Отправить снова</button>
        <button class="link-btn" onclick="goBack()">Изменить номер</button>
    </div>
</div>

<!-- ================= ОСНОВНОЕ ПРИЛОЖЕНИЕ ================= -->
<div class="app" id="app">
    
    <!-- Затемнение и Меню (Сайдбар) -->
    <div class="menu-overlay" id="menu-overlay" onclick="closeMenu()"></div>
    
    <div class="menu-drawer" id="menu-drawer">
        <!-- Главное меню -->
        <div id="main-menu-view">
            <div class="menu-header">
                <div class="avatar" id="profile-avatar">A</div>
                <div class="menu-header-info">
                    <div class="menu-header-name" id="profile-name">Александр</div>
                    <div class="menu-header-phone">+7 999 123-45-67</div>
                </div>
            </div>
            <div class="menu-items">
                <div class="menu-item" onclick="openSettings()">
                    <span class="menu-item-icon">⚙️</span> Настройки
                </div>
                <div class="menu-item" onclick="alert('Раздел в разработке')">
                    <span class="menu-item-icon">👤</span> Контакты
                </div>
                <div class="menu-item" onclick="alert('Раздел в разработке')">
                    <span class="menu-item-icon">📞</span> Звонки
                </div>
                <div class="menu-item" onclick="alert('Раздел в разработке')">
                    <span class="menu-item-icon">⭐</span> Избранное
                </div>
            </div>
        </div>

        <!-- Настройки профиля -->
        <div id="settings-view" class="menu-settings">
            <div class="back-menu" onclick="closeSettings()">← Назад</div>
            <div class="settings-title">Настройки</div>
            
            <div class="settings-row">
                <span class="settings-label">Тёмная тема</span>
                <div class="toggle" id="theme-toggle" onclick="toggleTheme()"></div>
            </div>

            <div class="input-group" style="margin-top: 16px;">
                <span class="settings-label">Ваше имя</span>
                <input type="text" id="name-input" value="Александр" placeholder="Введите имя">
                <button class="btn-sm" onclick="saveName()">Сохранить имя</button>
            </div>
            
            <div class="settings-row" style="border:none; margin-top: 10px;">
                <span class="settings-label">Уведомления</span>
                <div class="toggle on"></div>
            </div>
        </div>
    </div>

    <!-- Левая колонка -->
    <div class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <button class="menu-btn" onclick="openMenu()">☰</button>
            <div class="search-box">
                <span style="color:var(--text-light); font-size:14px;">🔍</span>
                <input type="text" placeholder="Поиск" id="search">
            </div>
        </div>
        <div class="chat-list" id="chat-list"></div>
    </div>

    <!-- Правая колонка -->
    <div class="chat-panel" id="chat-panel">
        <div class="chat-panel-header">
            <button class="back-btn" onclick="backToChats()">←</button>
            <div class="avatar" id="header-avatar">?</div>
            <div>
                <div class="chat-name" id="header-name">Выберите чат</div>
                <div class="status" id="header-status"></div>
            </div>
        </div>
        
        <div class="messages" id="messages"></div>

        <div class="composer">
            <input type="text" id="message-input" placeholder="Написать сообщение…">
            <button class="send-btn" onclick="sendMessage()">➤</button>
        </div>
    </div>
</div>

<script>
    /* ================= ДАННЫЕ ЧАТОВ И БАЗА ЗНАНИЙ ================= */
    const chats = [
        { 
            id: 7, name: 'Женя Киселев', color: '#2a5885', letter: '🇵🇹',
            messages: [
                { out: false, text: 'Йо, го в валик?', time: '15:30' },
                { out: true,  text: 'Я в DDNet пока что, давай позже.', time: '15:32' },
                { out: false, text: 'Ок, пиши как освободишься', time: '15:33' }
            ],
            rules: [
                { keywords: ['что такое валорант', 'что за валорант', 'расскажи про валорант'], replies: ['Valorant — это тактический шутер от Riot Games. Суть: две команды по 5 человек, у атакующих есть спайк.'] },
                { keywords: ['агенты', 'персонажи', 'герои'], replies: ['В валике куча агентов: дуэлянты, контроллеры, инициаторы и стражи.'] },
                { keywords: ['ранги', 'звания', 'рейтинг'], replies: ['Ранги: Железо, Бронза, Серебро, Золото, Платина, Алмаз, Бессмертный и Радиант.'] },
                { keywords: ['что такое ddnet', 'что за ddnet', 'дднет'], replies: ['DDNet (Dusty Death Network) — это бесплатный платформер, форк Teeworlds.'] },
                { keywords: ['привет', 'здарова', 'йо', 'хай'], replies: ['Салам! Как сам?', 'О, здарова. Че делаешь?'] },
                { keywords: ['валорант', 'валик', 'valorant'], replies: ['Го в валик, я щас ранкед катаю.', 'Тащу катку на Рейне! 🏹'] },
                { keywords: ['дота', 'dota', 'доту'], replies: ['Не, дота это не мое. Я только в валик и DDNet.'] },
                { keywords: ['как дела', 'как ты', 'дела'], replies: ['Да заебись. Вчера в валике имбу дал.', 'Норм, тащу потихоньку.'] }
            ]
        },
        { 
            id: 6, name: 'Илья Бруй', color: '#65aadd', letter: 'И',
            messages: [
                { out: true,  text: 'Папа привет, как дела, что делаешь?', time: '14:10' },
                { out: false, text: 'Все хорошо сын. Я на работе.', time: '14:15' },
            ],
            rules: [
                { keywords: ['привет', 'здравствуй'], replies: ['Привет сынок! Как сам?'] },
                { keywords: ['как дела', 'как ты'], replies: ['Да всё отлично, на работе вот сижу. А у тебя как?'] },
                { keywords: ['сайт', 'код', 'программ', 'создал'], replies: ['Молодец! Далеко пойдешь. Покажешь?'] }
            ]
        },
        { 
            id: 1, name: 'Анна Смирнова', color: '#e17076', letter: 'А',
            messages: [
                { out: false, text: 'Привет! Как дела?', time: '14:02' },
            ],
            rules: [
                { keywords: ['привет', 'приветик'], replies: ['Приветик! 😊'] },
                { keywords: ['гулять', 'пойдем', 'вечером'], replies: ['Давай! Во сколько?'] }
            ]
        }
    ];

    let activeChatId = null;
    let resendTimer = null;
    let resendSeconds = 0;
    let isBotTyping = false;

    /* ================= ЭЛЕМЕНТЫ ================= */
    const card       = document.getElementById('auth-card');
    const app        = document.getElementById('app');
    const phoneInput = document.getElementById('phone');
    const codeInput  = document.getElementById('code');
    const stepPhone  = document.getElementById('step-phone');
    const stepCode   = document.getElementById('step-code');
    const phoneError = document.getElementById('phone-error');
    const codeError  = document.getElementById('code-error');
    const codeSub    = document.getElementById('code-subtitle');
    const resendBtn  = document.getElementById('resend-btn');
    const chatListEl = document.getElementById('chat-list');
    const messagesEl = document.getElementById('messages');
    const msgInput   = document.getElementById('message-input');
    const sidebar    = document.getElementById('sidebar');
    const chatPanel  = document.getElementById('chat-panel');
    const menuDrawer = document.getElementById('menu-drawer');
    const menuOverlay= document.getElementById('menu-overlay');
    const mainMenuView = document.getElementById('main-menu-view');
    const settingsView = document.getElementById('settings-view');

    /* ================= УПРАВЛЕНИЕ МЕНЮ ================= */
    function openMenu() {
        menuOverlay.classList.add('show');
        // Небольшая задержка для анимации
        setTimeout(() => menuDrawer.classList.add('show'), 10);
    }

    function closeMenu() {
        menuDrawer.classList.remove('show');
        menuOverlay.classList.remove('show');
        // Возвращаем главное меню на место
        closeSettings();
    }

    function openSettings() {
        mainMenuView.style.display = 'none';
        settingsView.classList.add('show');
    }

    function closeSettings() {
        settingsView.classList.remove('show');
        mainMenuView.style.display = 'block';
    }

    /* ================= НАСТРОЙКИ (ТЕМА И ИМЯ) ================= */
    function toggleTheme() {
        const body = document.body;
        const toggle = document.getElementById('theme-toggle');
        
        body.classList.toggle('dark-theme');
        toggle.classList.toggle('on');
        
        // Сохраняем в память браузера
        if (body.classList.contains('dark-theme')) {
            localStorage.setItem('theme', 'dark');
        } else {
            localStorage.setItem('theme', 'light');
        }
    }

    function saveName() {
        const newName = document.getElementById('name-input').value.trim();
        if (newName) {
            document.getElementById('profile-name').textContent = newName;
            document.getElementById('profile-avatar').textContent = newName.charAt(0).toUpperCase();
            localStorage.setItem('username', newName);
            alert('Имя сохранено!');
            closeMenu();
        }
    }

    // Восстанавливаем настройки при загрузке
    window.onload = () => {
        const savedTheme = localStorage.getItem('theme');
        if (savedTheme === 'dark') {
            document.body.classList.add('dark-theme');
            document.getElementById('theme-toggle').classList.add('on');
        }
        
        const savedName = localStorage.getItem('username');
        if (savedName) {
            document.getElementById('profile-name').textContent = savedName;
            document.getElementById('profile-avatar').textContent = savedName.charAt(0).toUpperCase();
            document.getElementById('name-input').value = savedName;
        }
    };

    /* ================= ЛОГИКА ВХОДА ================= */
    phoneInput.addEventListener('input', () => {
        let digits = phoneInput.value.replace(/\D/g, '');
        if (digits.startsWith('8')) digits = '7' + digits.slice(1);
        if (!digits.startsWith('7') && digits.length > 0) digits = '7' + digits;

        let out = '';
        if (digits.length > 0) out = '+' + digits[0];
        if (digits.length > 1) out += ' ' + digits.slice(1, 4);
        if (digits.length > 4) out += ' ' + digits.slice(4, 7);
        if (digits.length > 7) out += '-' + digits.slice(7, 9);
        if (digits.length > 9) out += '-' + digits.slice(9, 11);

        phoneInput.value = out;
        phoneError.classList.remove('show');
        phoneInput.classList.remove('error');
    });

    codeInput.addEventListener('input', () => {
        codeInput.value = codeInput.value.replace(/\D/g, '').slice(0, 5);
        codeError.classList.remove('show');
        codeInput.classList.remove('error');
    });

    codeInput.addEventListener('keydown', e => { if (e.key === 'Enter') verifyCode(); });

    function sendCode() {
        const digits = phoneInput.value.replace(/\D/g, '');
        if (digits.length < 11) {
            phoneError.classList.add('show');
            phoneInput.classList.add('error');
            return;
        }
        const masked = '+7 ' + digits.slice(1,4) + ' ***-**-' + digits.slice(9,11);
        codeSub.textContent = 'Мы отправили код в Telegram на номер ' + masked;

        stepPhone.classList.remove('active');
        stepCode.classList.add('active');
        codeInput.value = '';
        codeInput.focus();
        startResendTimer(60);
    }

    function startResendTimer(seconds) {
        if (resendTimer) clearInterval(resendTimer);
        resendSeconds = seconds;
        updateResendBtn();

        resendTimer = setInterval(() => {
            resendSeconds--;
            if (resendSeconds <= 0) {
                clearInterval(resendTimer);
                resendTimer = null;
                resendBtn.disabled = false;
                resendBtn.textContent = 'Отправить код снова';
            } else {
                updateResendBtn();
            }
        }, 1000);
    }

    function updateResendBtn() {
        const m = Math.floor(resendSeconds / 60);
        const s = String(resendSeconds % 60).padStart(2, '0');
        resendBtn.disabled = true;
        resendBtn.textContent = `Отправить снова через ${m}:${s}`;
    }

    function resendCode() {
        if (resendBtn.disabled) return;
        codeInput.value = '';
        codeError.classList.remove('show');
        codeInput.classList.remove('error');
        codeInput.focus();
        startResendTimer(60);
    }

    function verifyCode() {
        const code = codeInput.value;
        if (code.length < 5) {
            codeError.textContent = 'Код должен состоять из 5 цифр.';
            codeError.classList.add('show');
            codeInput.classList.add('error');
            return;
        }
        if (code === '12345') {
            if (resendTimer) clearInterval(resendTimer);
            showChats();
        } else {
            codeError.textContent = 'Неверный код. Попробуйте ещё раз.';
            codeError.classList.add('show');
            codeInput.classList.add('error');
        }
    }

    function goBack() {
        if (resendTimer) clearInterval(resendTimer);
        stepCode.classList.remove('active');
        stepPhone.classList.add('active');
        phoneInput.focus();
    }

    function showChats() {
        card.style.display = 'none';
        app.classList.add('active');
        renderChats();
        if (window.innerWidth <= 700) {
            sidebar.classList.add('show');
            chatPanel.classList.remove('show');
        }
    }

    function renderChats(filter = '') {
        chatListEl.innerHTML = '';
        chats
            .filter(c => c.name.toLowerCase().includes(filter.toLowerCase()))
            .forEach(chat => {
                const last = chat.messages[chat.messages.length - 1];
                const item = document.createElement('div');
                item.className = 'chat-item' + (chat.id === activeChatId ? ' active' : '');
                item.innerHTML = `
                    <div class="avatar" style="background:${chat.color}">${chat.letter}</div>
                    <div class="chat-info">
                        <div class="chat-top">
                            <div class="chat-name">${chat.name}</div>
                            <div class="chat-time">${last ? last.time : ''}</div>
                        </div>
                        <div class="chat-preview">${last ? last.text : 'Нет сообщений'}</div>
                    </div>
                `;
                item.onclick = () => openChat(chat.id);
                chatListEl.appendChild(item);
            });
    }

    document.getElementById('search').addEventListener('input', e => {
        renderChats(e.target.value);
    });

    function openChat(id) {
        activeChatId = id;
        const chat = chats.find(c => c.id === id);

        document.getElementById('header-avatar').style.background = chat.color;
        document.getElementById('header-avatar').textContent = chat.letter;
        document.getElementById('header-name').textContent = chat.name;
        document.getElementById('header-status').textContent = 'был(а) недавно';

        renderMessages();
        renderChats(document.getElementById('search').value);

        if (window.innerWidth <= 700) {
            sidebar.classList.remove('show');
            chatPanel.classList.add('show');
        }
    }

    function backToChats() {
        if (window.innerWidth <= 700) {
            chatPanel.classList.remove('show');
            sidebar.classList.add('show');
        }
    }

    function renderMessages() {
        const chat = chats.find(c => c.id === activeChatId);
        if (!chat) { messagesEl.innerHTML = ''; return; }

        messagesEl.innerHTML = '';
        chat.messages.forEach(m => {
            const div = document.createElement('div');
            div.className = 'msg ' + (m.out ? 'out' : 'in');
            div.innerHTML = `${m.text}<span class="time">${m.time}</span>`;
            messagesEl.appendChild(div);
        });
        
        if (isBotTyping) {
            const typing = document.createElement('div');
            typing.className = 'typing-indicator show';
            typing.innerHTML = '<div class="typing-dot"></div><div class="typing-dot"></div><div class="typing-dot"></div>';
            messagesEl.appendChild(typing);
        }
        
        messagesEl.scrollTop = messagesEl.scrollHeight;
    }

    /* ================= ЛОГИКА ОТВЕТА БОТА ================= */
    function getBotReply(chat, text) {
        const lowerText = text.toLowerCase();
        
        if (chat.rules) {
            for (let rule of chat.rules) {
                for (let keyword of rule.keywords) {
                    if (lowerText.includes(keyword)) {
                        const repliesArray = rule.replies || [rule.reply];
                        return repliesArray[Math.floor(Math.random() * repliesArray.length)];
                    }
                }
            }
        }
        
        const genericReplies = ['Понял тебя.', 'Ага, интересно.', 'Да, ясно.', 'Хорошо.', 'Расскажи подробнее.'];
        return genericReplies[Math.floor(Math.random() * genericReplies.length)];
    }

    function sendMessage() {
        const text = msgInput.value.trim();
        if (!text || !activeChatId) return;

        const chat = chats.find(c => c.id === activeChatId);
        const now = new Date();
        const time = String(now.getHours()).padStart(2,'0') + ':' + String(now.getMinutes()).padStart(2,'0');

        chat.messages.push({ out: true, text, time });
        msgInput.value = '';
        renderMessages();
        renderChats(document.getElementById('search').value);

        isBotTyping = true;
        renderMessages(); 

        setTimeout(() => {
            const reply = getBotReply(chat, text);
            chat.messages.push({ out: false, text: reply, time });
            isBotTyping = false;
            
            renderMessages();
            renderChats(document.getElementById('search').value);
        }, 1200); 
    }

    msgInput.addEventListener('keydown', e => { if (e.key === 'Enter') sendMessage(); });
</script>
</body>
</html>
