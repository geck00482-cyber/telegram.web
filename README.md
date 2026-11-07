
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Telegram Web</title>
<style>
    * { margin: 0; padding: 0; box-sizing: border-box;
        font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; }

    body {
        background: #e6ebee;
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        padding: 20px;
        overflow: hidden;
    }

    .card {
        background: #fff;
        width: 100%;
        max-width: 400px;
        border-radius: 10px;
        box-shadow: 0 2px 10px rgba(0,0,0,0.08);
        padding: 40px 30px 30px;
        position: relative;
        overflow: hidden;
    }

    .top-bar { position: absolute; top: 14px; right: 14px; }
    .close-btn {
        width: 32px; height: 32px;
        border: none; background: transparent;
        color: #a2acb4; font-size: 22px;
        cursor: pointer; border-radius: 50%;
        transition: background 0.15s;
    }
    .close-btn:hover { background: #f0f2f4; }

    .logo { width: 90px; height: 90px; margin: 0 auto 18px; }
    .logo svg { width: 100%; height: 100%; }

    h1 {
        font-size: 21px; font-weight: 500; color: #222;
        text-align: center; margin-bottom: 8px;
    }
    .subtitle {
        font-size: 14px; color: #707579;
        text-align: center; margin-bottom: 26px; line-height: 1.4;
    }

    .field { position: relative; margin-bottom: 18px; }
    .field input {
        width: 100%; padding: 15px 16px;
        border: 1px solid #dadce0; border-radius: 10px;
        font-size: 16px; outline: none;
        transition: border-color 0.15s; background: #fff;
    }
    .field input:focus { border-color: #3390ec; }
    .field input.error { border-color: #e53935; }

    .code-input {
        width: 100%; padding: 15px 16px;
        border: 1px solid #dadce0; border-radius: 10px;
        font-size: 22px; letter-spacing: 8px;
        text-align: center; outline: none;
        transition: border-color 0.15s;
    }
    .code-input:focus { border-color: #3390ec; }
    .code-input.error { border-color: #e53935; }

    .btn {
        width: 100%; padding: 14px;
        background: #3390ec; color: #fff;
        border: none; border-radius: 10px;
        font-size: 16px; font-weight: 500;
        cursor: pointer; transition: background 0.15s; margin-top: 6px;
    }
    .btn:hover { background: #2b7cd3; }
    .btn:disabled { background: #a8c7e8; cursor: default; }

    .link-btn {
        display: block; width: 100%; margin-top: 16px;
        padding: 8px; background: transparent; border: none;
        color: #3390ec; font-size: 14px;
        cursor: pointer; text-align: center;
    }
    .link-btn:hover { text-decoration: underline; }
    .link-btn:disabled { color: #a2acb4; cursor: default; text-decoration: none; }

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
        color: #a2acb4; text-align: center; line-height: 1.4;
    }

    /* ================= ЭКРАН С ЧАТАМИ ================= */
    .app {
        display: none;
        width: 100%;
        max-width: 1000px;
        height: 90vh;
        max-height: 720px;
        background: #fff;
        border-radius: 10px;
        box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        overflow: hidden;
        grid-template-columns: 320px 1fr;
    }
    .app.active { display: grid; }

    .sidebar {
        display: flex; flex-direction: column;
        border-right: 1px solid #e6ebee;
        background: #fff; min-height: 0;
    }
    .sidebar-header {
        display: flex; align-items: center; gap: 10px;
        padding: 12px 14px; border-bottom: 1px solid #e6ebee;
    }
    .menu-btn {
        width: 36px; height: 36px; border: none; background: transparent;
        border-radius: 50%; cursor: pointer; color: #707579;
        font-size: 18px; display: flex; align-items: center; justify-content: center;
    }
    .menu-btn:hover { background: #f0f2f4; }
    .search-box {
        flex: 1; display: flex; align-items: center;
        background: #f4f4f5; border-radius: 20px;
        padding: 8px 14px; gap: 8px;
    }
    .search-box input {
        border: none; background: transparent; outline: none;
        font-size: 14px; width: 100%; color: #222;
    }
    .search-box input::placeholder { color: #a2acb4; }

    .chat-list { flex: 1; overflow-y: auto; }
    .chat-item {
        display: flex; align-items: center; gap: 12px;
        padding: 10px 14px; cursor: pointer;
        transition: background 0.12s;
    }
    .chat-item:hover { background: #f4f4f5; }
    .chat-item.active { background: #3390ec; color: #fff; }
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
    .chat-time { font-size: 12px; color: #a2acb4; flex-shrink: 0; }
    .chat-preview {
        font-size: 13px; color: #707579;
        white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
        margin-top: 2px;
    }

    .chat-panel {
        display: flex; flex-direction: column;
        background: #e6ebee; min-height: 0;
    }
    .chat-panel-header {
        display: flex; align-items: center; gap: 12px;
        padding: 10px 18px; background: #fff;
        border-bottom: 1px solid #e6ebee;
    }
    .back-btn {
        display: none;
        background: transparent; border: none;
        font-size: 20px; color: #707579;
        cursor: pointer; margin-right: 4px;
    }
    .chat-panel-header .chat-name { font-size: 15px; }
    .chat-panel-header .status { font-size: 13px; color: #707579; margin-top: 1px; }

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
        background: #fff; color: #222;
        border-bottom-left-radius: 4px;
    }
    .msg.out {
        align-self: flex-end;
        background: #effdde; color: #222;
        border-bottom-right-radius: 4px;
    }
    .msg .time {
        font-size: 11px; color: #a2acb4;
        margin-left: 8px; float: right; margin-top: 4px;
    }
    
    .typing-indicator {
        display: none; align-self: flex-start;
        background: #fff; padding: 10px 14px; border-radius: 12px;
        border-bottom-left-radius: 4px; margin-bottom: 8px;
    }
    .typing-indicator.show { display: flex; gap: 4px; }
    .typing-dot {
        width: 6px; height: 6px; background: #a2acb4; border-radius: 50%;
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
        padding: 12px 18px; background: #fff;
        border-top: 1px solid #e6ebee;
    }
    .composer input {
        flex: 1; padding: 12px 16px;
        border: none; background: #f4f4f5;
        border-radius: 20px; font-size: 14px; outline: none;
    }
    .composer input:focus { background: #eef0f2; }
    .send-btn {
        width: 44px; height: 44px; border-radius: 50%;
        background: #3390ec; color: #fff; border: none;
        font-size: 18px; cursor: pointer; flex-shrink: 0;
        display: flex; align-items: center; justify-content: center;
        transition: background 0.15s;
    }
    .send-btn:hover { background: #2b7cd3; }

    @media (max-width: 700px) {
        body { padding: 0; background: #fff; }
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
    }
</style>
</head>
<body>

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

<div class="app" id="app">
    <div class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <button class="menu-btn" title="Меню" onclick="alert('Это демонстрационная страница.')">☰</button>
            <div class="search-box">
                <span style="color:#a2acb4; font-size:14px;">🔍</span>
                <input type="text" placeholder="Поиск" id="search">
            </div>
        </div>
        <div class="chat-list" id="chat-list"></div>
    </div>

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
                // --- ИНФА ПРО VALORANT ---
                { keywords: ['что такое валорант', 'что за валорант', 'расскажи про валорант'], replies: ['Valorant — это тактический шутер от Riot Games (те же ребята, что сделали LoL). Суть: две команды по 5 человек, у атакующих есть спайк (бомба), у защиты — время. Первый до 13 раундов побеждает.'] },
                { keywords: ['агенты', 'персонажи', 'герои'], replies: ['В валике куча агентов: дуэлянты (Джэтт, Рейна), контроллеры (Омен, Бримстоун), инициаторы (Сова, Скай) и стражи (Сайфер, Киллджой). У каждого свои абилки!'] },
                { keywords: ['ранги', 'звания', 'рейтинг'], replies: ['Ранги в валоранте: Железо, Бронза, Серебро, Золото, Платина, Алмаз, Бессмертный и Радиант. Чтобы получить Радианта, нужно быть в топ-500 на сервере!'] },
                { keywords: ['скины', 'скин', 'оружие', 'пушки'], replies: ['Скины в валике покупаются за очки (VP). Топовые: Prime, Reaver, Elderflame (дракон). Они меняют анимацию и звук выстрелов.'] },
                { keywords: ['оператор', 'авик', 'снайперка', 'винтовка'], replies: ['Оператор (AWP) — это снайперка за 4700 кредитов. Ваншот в корпус! Но перезарядка долгая, так что нужно играть аккуратно.'] },
                { keywords: ['карты', 'локации', 'мапы'], replies: ['Основные карты: Ascent, Bind, Haven, Split, Icebox, Breeze, Fracture, Pearl, Lotus, Sunset. На каждой свои фишки и телепорты!'] },
                { keywords: ['патч', 'обновление', 'новая карта', 'новый агент'], replies: ['Riot часто добавляет новых агентов (последний — Clove) и карты. Патчи выходят каждые 2-3 недели, балансят оружие и абилки.'] },
                
                // --- ИНФА ПРО DDNET ---
                { keywords: ['что такое ddnet', 'что за ddnet', 'расскажи про ddnet', 'дднет'], replies: ['DDNet (Dusty Death Network) — это бесплатный платформер, форк Teeworlds. Суть: бегаешь человечком с крюком-кошкой, проходишь карты на скорость, стреляешь лазером и гранатами. Очень хардкорная игра на скилл!'] },
                { keywords: ['карты в ddnet', 'карты', 'уровни'], replies: ['В DDNet тысячи карт! Есть лёгкие (Easy), средние (Moderate), сложные (Hard), очень сложные (Brutal) и безумные (Insane). Названия типа "Aim", "KZ", "Sunra", "Flying".'] },
                { keywords: ['крюк', 'хук', 'кошка'], replies: ['Крюк (Hook) — главная механика DDNet. Притягиваешься к блокам и летаешь. Без него карты не пройти. Есть даже карты, где нельзя касаться пола!'] },
                { keywords: ['скины в ddnet', 'скины', 'вид'], replies: ['В DDNet скины кастомизируются. Можно поставить себе иконку, изменить цвет, выбрать эмоции. Есть даже скины с мемами.'] },
                { keywords: ['как играть в ddnet', 'управление', 'клавиши'], replies: ['Управление простое: WASD — движение, левая кнопка мыши — крюк, правая — стрелять, Shift — граната. Сначала сложно, но потом затягивает!'] },
                { keywords: ['сервера ddnet', 'сервер', 'играть'], replies: ['Серверов DDNet очень много по всему миру. Есть официальные (DDNet GER, DDNet RUS) и куча неофициальных. Заходишь и выбираешь карту.'] },
                
                // --- ОБЩИЕ ТЕМЫ ---
                { keywords: ['привет', 'здарова', 'йо', 'хай'], replies: ['Салам! Как сам?', 'О, здарова. Че делаешь?'] },
                { keywords: ['валорант', 'валик', 'valorant'], replies: ['Го в валик, я щас ранкед катаю.', 'Тащу катку на Рейне! 🏹', 'Скинь ник, добавлю тебя в лобби.'] },
                { keywords: ['дота', 'dota', 'доту'], replies: ['Не, дота это не мое. Я только в валик и DDNet.', 'Доту не играю, она мне нервы треплет.'] },
                { keywords: ['игра', 'играть', 'го', 'катку'], replies: ['Го, я готов! Валик или DDNet?', 'Я в деле. Качаем?'] },
                { keywords: ['как дела', 'как ты', 'дела'], replies: ['Да заебись. Вчера в валике имбу дал.', 'Норм, тащу потихоньку.'] },
                { keywords: ['роналдо', 'криштиану', 'cr7', 'футбол'], replies: ['Сиууу! 🐐', 'Роналду лучший, это факт.'] },
                { keywords: ['пока', 'до связи', 'давай'], replies: ['Давай, спишемся.', 'Пока, не забудь про катку.'] }
            ]
        },
        { 
            id: 6, name: 'Илья Бруй', color: '#65aadd', letter: 'И',
            messages: [
                { out: true,  text: 'Папа привет, как дела, что делаешь?', time: '14:10' },
                { out: false, text: 'Все хорошо сын. Я на работе.', time: '14:15' },
                { out: true,  text: 'Понятно, я вот сайт создал.', time: '14:16' },
                { out: false, text: 'Красавчик сын.', time: '14:20' },
            ],
            rules: [
                { keywords: ['привет', 'здравствуй', 'хай', 'добрый день', 'доброе утро'], replies: ['Привет сынок! Как сам?', 'О, привет! Рад тебя слышать.'] },
                { keywords: ['как дела', 'как ты', 'как жизнь', 'как настроение'], replies: ['Да всё отлично, на работе вот сижу. А у тебя как?', 'Всё хорошо, не жалуюсь. Ты как?'] },
                { keywords: ['что делаешь', 'чем занят', 'работа', 'делаешь'], replies: ['Работаю, отчеты делаю. Скоро домой поеду.', 'Да вот, на работе завал. А ты чем занят?'] },
                { keywords: ['сайт', 'код', 'программ', 'создал', 'сделал', 'компьютер'], replies: ['Молодец! Далеко пойдешь. Покажешь, что получилось?', 'Ого, да ты у нас программист растешь!'] },
                { keywords: ['учеба', 'школа', 'универ', 'уроки', 'экзамен'], replies: ['Учись хорошо, сынок. Знания — это сила.', 'Как там учеба? Не запускай.'] },
                { keywords: ['пока', 'до свидания', 'спокойной ночи', 'давай'], replies: ['Давай сын, до вечера! Не засиживайся.', 'Пока! Хорошего дня.'] },
                { keywords: ['деньги', 'купить', 'дай', 'перевод'], replies: ['Что нужно купить? Вечером обсудим.', 'Сколько нужно? Скину позже.'] },
                { keywords: ['погода', 'холодно', 'жарко', 'дождь', 'снег'], replies: ['Да, на улице сегодня прохладно. Одевайся теплее!', 'Оденься по погоде, не простудись.'] },
                { keywords: ['еда', 'кушать', 'обед', 'ужин', 'голодный'], replies: ['Не забудь пообедать!', 'Иди поешь, а то голодный сидишь.'] },
                { keywords: ['игра', 'играть', 'комп', 'игры'], replies: ['В игры играешь? Лучше делом займись.', 'Долго не сиди за компом.'] },
                { keywords: ['мама', 'мать'], replies: ['Мама на кухне, что-то готовит.', 'Маме привет передавай!'] },
                { keywords: ['люблю', 'скучаю'], replies: ['И я тебя люблю, сынок.', 'Я тоже скучаю.'] }
            ]
        },
        { 
            id: 1, name: 'Анна Смирнова', color: '#e17076', letter: 'А',
            messages: [
                { out: false, text: 'Привет! Как дела?', time: '14:02' },
                { out: true,  text: 'Привет! Всё хорошо, спасибо 😊', time: '14:03' },
                { out: false, text: 'Пойдём гулять вечером?', time: '14:05' },
            ],
            rules: [
                { keywords: ['привет', 'здравствуй', 'хай', 'приветик'], replies: ['Приветик! 😊', 'Привет! Как ты?'] },
                { keywords: ['как дела', 'как ты', 'как жизнь'], replies: ['Отлично! А у тебя как?', 'Всё супер! Ты как?'] },
                { keywords: ['гулять', 'пойдем', 'вечером', 'встретимся', 'прогулка'], replies: ['Давай! Во сколько?', 'С удовольствием! Где встретимся?'] },
                { keywords: ['люблю', 'скучаю', 'ты классная'], replies: ['Оу, это так мило 🥰', 'Ты тоже классный 😊'] },
                { keywords: ['пока', 'до встречи', 'спокойной'], replies: ['Пока-пока! До вечера 😘', 'До встречи!'] },
                { keywords: ['еда', 'кушать', 'кафе', 'ресторан', 'пицца'], replies: ['Я всегда за покушать! 🍕', 'О да, давай сходим в кафе!'] },
                { keywords: ['кино', 'фильм', 'сериал'], replies: ['О, я как раз хотела посмотреть что-нибудь!', 'Давай в кино! Что смотрим?'] },
                { keywords: ['работа', 'учеба', 'школа', 'универ'], replies: ['Ой, даже не напоминай... 😅', 'Устаю, но стараюсь.'] },
                { keywords: ['спасибо', 'благодарю'], replies: ['Всегда пожалуйста! 😉', 'Обращайся!'] }
            ]
        },
        { 
            id: 2, name: 'Рабочий чат', color: '#7bc862', letter: 'Р',
            messages: [
                { out: false, text: 'Совещание в 15:00', time: '10:00' },
                { out: true,  text: 'Понял, буду', time: '10:01' },
            ],
            rules: [
                { keywords: ['привет', 'здравствуйте', 'добрый'], replies: ['Здравствуйте. Чем могу помочь?', 'Добрый день. Слушаю вас.'] },
                { keywords: ['сделал', 'готово', 'отчет', 'задача', 'выполнил'], replies: ['Отлично, спасибо. Отправьте на почту.', 'Принято, проверю.'] },
                { keywords: ['проблема', 'ошибка', 'не работает', 'баг'], replies: ['Понял, сейчас разберемся.', 'Опишите подробнее, в чем проблема.'] },
                { keywords: ['пока', 'ушел', 'до свидания', 'конец'], replies: ['Всего доброго.', 'До завтра.'] },
                { keywords: ['время', 'созвон', 'встреча', 'созвон'], replies: ['Созвон в 15:00. Не опаздывайте.', 'Встреча назначена на завтра.'] },
                { keywords: ['зарплата', 'отпуск', 'премия'], replies: ['Все вопросы по зарплате к бухгалтерии.', 'Заявление на отпуск пишите на имя руководителя.'] }
            ]
        },
        { 
            id: 3, name: 'Дмитрий', color: '#e5a54b', letter: 'Д',
            messages: [
                { out: true,  text: 'Скинь файл, пожалуйста', time: '09:20' },
                { out: false, text: 'Сейчас отправлю', time: '09:22' },
            ],
            rules: [
                { keywords: ['привет', 'здорово', 'хай'], replies: ['О, привет.', 'Здорово, брат.'] },
                { keywords: ['файл', 'документ', 'скинь', 'отправь'], replies: ['Уже кидаю на почту.', 'Сейчас скину.'] },
                { keywords: ['игра', 'играть', 'го', 'доту', 'кс', 'танки'], replies: ['Я за! Через 10 минут буду готов.', 'Го, только катку доиграю.'] },
                { keywords: ['пока', 'до связи', 'давай'], replies: ['Давай, спишемся.', 'Ок, до связи.'] },
                { keywords: ['пиво', 'бухать', 'встретиться', 'пойти'], replies: ['Можно вечером пересечься.', 'Я только за!'] },
                { keywords: ['как дела', 'как ты'], replies: ['Да норм, сам как?', 'Всё путём.'] }
            ]
        },
        { 
            id: 4, name: 'Telegram', color: '#3390ec', letter: 'T',
            messages: [
                { out: false, text: 'Код подтверждения: 12345', time: 'вчера' },
            ],
            rules: [
                { keywords: ['код', 'войти'], replies: ['Это системный бот. Он не отвечает на сообщения.', 'Для входа используйте код из сообщения.'] },
                { keywords: ['спам', 'жалоба', 'бан'], replies: ['Ваша жалоба принята.', 'Мы заблокировали нарушителя.'] },
                { keywords: ['привет', 'помощь', 'help'], replies: ['Это служебный аккаунт Telegram.', 'Если нужна помощь, обратитесь в поддержку.'] }
            ]
        },
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

    /* ================= МАСКА ТЕЛЕФОНА ================= */
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
        
        const genericReplies = [
            'Понял тебя.', 
            'Ага, интересно.', 
            'Да, ясно.', 
            'Хорошо.',
            'Не совсем понял, но ладно.',
            'Расскажи подробнее.',
            'Ого!'
        ];
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
