
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FrogDrop — 3D & Games</title>
<style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', system-ui, sans-serif; }
    body {
        background: #0b0e14;
        color: #fff;
        min-height: 100vh;
        overflow-x: hidden;
    }

    /* --- Фон с монетами TON --- */
    #ton-background {
        position: fixed;
        top: 0; left: 0;
        width: 100vw; height: 100vh;
        pointer-events: none;
        z-index: 0;
        overflow: hidden;
    }
    .ton-coin {
        position: absolute;
        background-image: url('data:image/svg+xml;utf8,<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg"><circle cx="50" cy="50" r="50" fill="%230091E9" opacity="0.2"/><path d="M 30 30 L 50 75 L 70 30 Z" fill="%23ffffff" opacity="0.25"/></svg>');
        background-size: cover;
        opacity: 0.1;
        animation: floatUp linear infinite;
    }
    @keyframes floatUp {
        0% { transform: translateY(100vh) rotate(0deg); }
        100% { transform: translateY(-100px) rotate(360deg); }
    }

    /* Остальное поверх фона */
    header, .container, #profilePage, .modal-overlay {
        position: relative;
        z-index: 1;
    }

    /* --- Шапка --- */
    header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 15px 40px;
        background: rgba(11, 14, 20, 0.7);
        border-bottom: 1px solid rgba(255,255,255,0.05);
        backdrop-filter: blur(10px);
        position: sticky;
        top: 0;
        z-index: 100;
    }
    .logo {
        font-size: 26px;
        font-weight: 900;
        background: linear-gradient(90deg, #4cd137, #00d2d3, #feca57);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        letter-spacing: 1px;
        cursor: pointer;
    }
    .header-right {
        display: flex;
        align-items: center;
        gap: 15px;
    }
    .balance-display {
        background: rgba(0, 255, 136, 0.1);
        border: 1px solid rgba(0, 255, 136, 0.3);
        padding: 10px 20px;
        border-radius: 10px;
        font-size: 18px;
        font-weight: 700;
        color: #00ff88;
        display: flex;
        align-items: center;
        gap: 5px;
        text-shadow: 0 0 10px rgba(0, 255, 136, 0.3);
    }
    .btn-deposit {
        background: linear-gradient(90deg, #feca57, #ff8f00);
        color: #000;
        border: none;
        padding: 10px 20px;
        border-radius: 10px;
        font-weight: 700;
        font-size: 16px;
        cursor: pointer;
        transition: transform 0.2s;
        box-shadow: 0 4px 15px rgba(254, 202, 87, 0.3);
    }
    .btn-deposit:hover { transform: scale(1.05); }

    .nav-buttons button {
        background: rgba(255,255,255,0.1);
        border: none;
        color: white;
        padding: 10px 20px;
        border-radius: 8px;
        cursor: pointer;
        font-weight: 600;
        transition: 0.2s;
    }
    .nav-buttons button:hover { background: rgba(255,255,255,0.2); }

    .container {
        max-width: 1200px;
        margin: 0 auto;
        padding: 40px 20px;
    }

    /* --- 3D NFT --- */
    .hero-section {
        display: flex;
        align-items: center;
        gap: 40px;
        margin-bottom: 60px;
        flex-wrap: wrap;
    }
    .hero-text { flex: 1; min-width: 300px; }
    .hero-text h1 {
        font-size: 48px;
        margin-bottom: 20px;
        line-height: 1.1;
    }
    .hero-text p {
        color: #a0a0a0;
        font-size: 18px;
        margin-bottom: 30px;
    }
    .btn-primary {
        background: linear-gradient(90deg, #4cd137, #00d2d3);
        border: none;
        color: #000;
        padding: 15px 40px;
        font-size: 18px;
        font-weight: 700;
        border-radius: 12px;
        cursor: pointer;
        transition: transform 0.2s;
    }
    .btn-primary:hover { transform: scale(1.05); }

    #canvas-container {
        flex: 1;
        min-width: 300px;
        height: 450px;
        background: radial-gradient(circle at center, #1a2a1a 0%, #0b0e14 70%);
        border-radius: 20px;
        border: 1px solid rgba(76, 209, 55, 0.2);
        overflow: hidden;
        position: relative;
        box-shadow: 0 0 30px rgba(76, 209, 55, 0.1);
        cursor: grab;
    }
    #canvas-container:active { cursor: grabbing; }
    .nft-badge {
        position: absolute;
        top: 20px;
        left: 20px;
        background: rgba(0,0,0,0.6);
        padding: 8px 16px;
        border-radius: 20px;
        font-size: 12px;
        font-weight: 600;
        color: #4cd137;
        border: 1px solid rgba(76, 209, 55, 0.3);
        z-index: 10;
        pointer-events: none;
    }

    /* --- Профиль --- */
    #profilePage {
        display: none;
        min-height: calc(100vh - 80px);
        padding: 40px 20px;
        align-items: center;
        justify-content: center;
    }
    .profile-card {
        background: #151a24;
        border: 1px solid rgba(255,255,255,0.1);
        border-radius: 24px;
        padding: 40px;
        width: 100%;
        max-width: 500px;
        text-align: center;
        box-shadow: 0 10px 40px rgba(0,0,0,0.5);
        position: relative;
        overflow: hidden;
    }
    .profile-card::before {
        content: '';
        position: absolute;
        top: 0; left: 0; width: 100%; height: 100px;
        background: linear-gradient(90deg, rgba(76, 209, 55, 0.2), rgba(0, 210, 211, 0.2));
        z-index: 0;
    }
    .profile-avatar {
        width: 140px;
        height: 140px;
        border-radius: 50%;
        border: 4px solid #feca57;
        box-shadow: 0 0 20px rgba(254, 202, 87, 0.4);
        margin: 0 auto 20px;
        position: relative;
        z-index: 1;
        background: #fff;
        object-fit: cover;
    }
    .profile-name {
        font-size: 28px;
        font-weight: 800;
        margin-bottom: 5px;
        position: relative;
        z-index: 1;
    }
    .profile-id {
        font-size: 16px;
        color: #00ff88;
        font-weight: 600;
        letter-spacing: 2px;
        margin-bottom: 30px;
        font-family: monospace;
        position: relative;
        z-index: 1;
    }
    .profile-stats {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 15px;
        margin-bottom: 30px;
        position: relative;
        z-index: 1;
    }
    .stat-box {
        background: rgba(0,0,0,0.3);
        padding: 15px;
        border-radius: 12px;
        border: 1px solid rgba(255,255,255,0.05);
    }
    .stat-val {
        font-size: 24px;
        font-weight: 800;
        color: #feca57;
    }
    .stat-label {
        font-size: 12px;
        color: #888;
        text-transform: uppercase;
        margin-top: 5px;
    }

    /* --- Игры --- */
    .games-section { margin-top: 40px; }
    .games-section h2 {
        font-size: 32px;
        margin-bottom: 30px;
        text-align: center;
    }
    .games-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
        gap: 30px;
    }
    .game-card {
        background: rgba(21, 26, 36, 0.7);
        border: 1px solid rgba(255,255,255,0.08);
        border-radius: 20px;
        padding: 30px;
        text-align: center;
        transition: 0.3s;
        position: relative;
        overflow: hidden;
        backdrop-filter: blur(5px);
    }
    .game-card:hover {
        border-color: rgba(255,255,255,0.2);
        transform: translateY(-5px);
    }
    
    /* Улучшенные заголовки игр */
    .game-card h3 {
        font-size: 26px;
        margin-bottom: 15px;
        font-weight: 900;
        background: linear-gradient(90deg, #4cd137, #00d2d3, #feca57);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        letter-spacing: 1px;
    }

    .game-icon { font-size: 48px; margin-bottom: 15px; }
    .game-card p { color: #888; font-size: 14px; margin-bottom: 20px; }
    .play-btn {
        background: #00d2d3;
        color: #000;
        border: none;
        padding: 10px 30px;
        border-radius: 8px;
        font-weight: 700;
        cursor: pointer;
        transition: 0.2s;
    }
    .play-btn:hover { background: #0abde3; }

    /* --- Модальные окна --- */
    .modal-overlay {
        display: none;
        position: fixed;
        top: 0; left: 0; width: 100%; height: 100%;
        background: rgba(0,0,0,0.85);
        backdrop-filter: blur(8px);
        z-index: 200;
        justify-content: center;
        align-items: center;
    }
    .modal-overlay.active { display: flex; }
    .modal-content {
        background: #151a24;
        padding: 30px;
        border-radius: 20px;
        border: 1px solid rgba(255,255,255,0.1);
        text-align: center;
        max-width: 600px;
        width: 95%;
        position: relative;
        box-shadow: 0 10px 40px rgba(0,0,0,0.5);
    }
    .close-btn {
        position: absolute;
        top: 15px; right: 20px;
        background: none; border: none;
        color: #888; font-size: 28px;
        cursor: pointer;
        z-index: 10;
    }
    .close-btn:hover { color: #fff; }

    /* --- Пополнение --- */
    .deposit-options {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
        gap: 15px;
        margin-top: 25px;
    }
    .deposit-card {
        background: rgba(255,255,255,0.05);
        border: 1px solid rgba(255,255,255,0.1);
        border-radius: 15px;
        padding: 20px;
        cursor: pointer;
        transition: 0.2s;
    }
    .deposit-card:hover {
        background: rgba(255,255,255,0.1);
        border-color: #feca57;
    }
    .deposit-card .icon { font-size: 36px; margin-bottom: 10px; }
    .deposit-card .title { font-weight: 700; font-size: 16px; margin-bottom: 5px; }
    .deposit-card .desc { font-size: 12px; color: #888; }
    
    .sub-options {
        display: none;
        grid-template-columns: 1fr 1fr 1fr;
        gap: 10px;
        margin-top: 15px;
    }
    .sub-options.active { display: grid; }

    .bank-card { padding: 15px 10px; }
    .bank-card .title { font-size: 14px; }

    /* --- Crash Game --- */
    #crash-arena {
        width: 100%;
        height: 350px;
        background: #0b0e14;
        border-radius: 12px;
        position: relative;
        overflow: hidden;
        margin: 20px 0;
    }
    #crash-canvas { width: 100%; height: 100%; }
    .crash-ui {
        position: absolute;
        top: 20px; left: 20px;
        z-index: 10;
    }
    .multiplier {
        font-size: 48px;
        font-weight: 900;
        color: #4cd137;
        text-shadow: 0 0 20px rgba(76, 209, 55, 0.5);
        font-family: monospace;
    }
    .multiplier.crashed { color: #ff4757; text-shadow: 0 0 20px rgba(255, 71, 87, 0.5); }
    .crash-controls {
        position: absolute;
        bottom: 20px; left: 0; width: 100%;
        display: flex; justify-content: center; gap: 20px;
        z-index: 10;
    }
    .bet-input {
        padding: 12px 20px;
        border-radius: 10px;
        border: 1px solid rgba(255,255,255,0.2);
        background: rgba(0,0,0,0.5);
        color: #fff;
        font-size: 16px;
        width: 150px;
        outline: none;
    }
    .action-btn {
        padding: 12px 30px;
        border-radius: 10px;
        border: none;
        font-weight: 700;
        font-size: 16px;
        cursor: pointer;
        transition: 0.2s;
    }
    .btn-green { background: #4cd137; color: #000; }
    .btn-green:hover { background: #44bd32; }
    .btn-disabled { background: #555; color: #aaa; cursor: not-allowed; }

    /* --- Апгрейд Колесо --- */
    .upgrade-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 25px;
        margin-top: 20px;
        position: relative;
    }
    
    .upgrade-wheel-wrapper {
        position: relative;
        width: 340px;
        height: 340px;
        margin: 0 auto;
        border-radius: 50%;
        border: 6px solid #feca57;
        box-shadow: 0 0 30px rgba(254, 202, 87, 0.4), inset 0 0 30px rgba(0,0,0,0.9);
        background: #0b0e14;
        z-index: 2;
    }
    
    /* ИСПРАВЛЕНО: Теперь фон не залезает в центр, потому что он под маской */
    .upgrade-wheel-bg {
        width: 100%;
        height: 100%;
        border-radius: 50%;
        position: absolute;
        top: 0; left: 0;
        transition: background 0.1s linear;
        z-index: 1;
        box-shadow: inset 0 0 60px rgba(0,0,0,0.9);
        overflow: hidden;
    }

    /* --- Стрелка --- */
    .upgrade-arrow-container {
        position: absolute;
        top: 0; left: 0;
        width: 100%; height: 100%;
        z-index: 3;
        pointer-events: none;
        transition: transform 8s cubic-bezier(0.25, 0.8, 0.25, 1); 
        transform: rotate(0deg);
        transform-origin: center center;
        will-change: transform;
    }

    /* Улучшенный центральный кружок */
    .upgrade-center-wrapper {
        position: absolute;
        top: 50%; left: 50%;
        transform: translate(-50%, -50%);
        width: 110px; height: 110px;
        background: radial-gradient(circle, #2a2e38 0%, #0b0e14 100%);
        border: 3px solid #feca57;
        border-radius: 50%;
        box-shadow: 0 0 30px rgba(254, 202, 87, 0.7), inset 0 0 20px rgba(0,0,0,0.9);
        z-index: 5;
        display: flex; flex-direction: column; justify-content: center; align-items: center;
        
        /* Красивая SVG иконка внутри центра */
        background-image: url('data:image/svg+xml;utf8,<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg"><circle cx="50" cy="50" r="45" fill="none" stroke="%23feca57" stroke-width="2" opacity="0.3"/><path d="M 50 15 L 60 40 L 85 40 L 65 55 L 75 80 L 50 65 L 25 80 L 35 55 L 15 40 L 40 40 Z" fill="none" stroke="%2300ff88" stroke-width="3" opacity="0.8"/></svg>');
        background-size: 80%;
        background-position: center;
        background-repeat: no-repeat;
    }

    .frogdrop-logo-text {
        font-size: 18px;
        font-weight: 900;
        text-transform: uppercase;
        letter-spacing: 1px;
        color: #00ff88;
        text-shadow: 
            1px 1px 0px #006633, 
            2px 2px 0px #006633, 
            3px 3px 0px #006633,
            0 0 20px rgba(0, 255, 136, 0.8);
        line-height: 1.1;
        text-align: center;
        /* Скрываем текст внутри кружка, чтобы был только фон (можно убрать display: none, если нужен текст) */
        display: none; 
    }
    .frogdrop-logo-text span { display: block; font-size: 12px; color: #feca57; text-shadow: 0 0 10px rgba(254,202,87,0.5); margin-top: 4px; letter-spacing: 2px;}

    .upgrade-controls {
        display: flex;
        flex-direction: column;
        gap: 15px;
        align-items: center;
        background: rgba(255,255,255,0.02);
        padding: 20px;
        border-radius: 16px;
        border: 1px solid rgba(255,255,255,0.05);
        z-index: 2;
        width: 100%;
    }
    
    .input-row {
        display: flex;
        gap: 15px;
        width: 100%;
        justify-content: center;
    }

    .upgrade-input-group {
        display: flex;
        flex-direction: column;
        gap: 8px;
        text-align: left;
    }
    .upgrade-input-group label { 
        font-size: 13px; 
        color: #a0a0a0; 
        font-weight: 600;
        text-transform: uppercase;
        letter-spacing: 0.5px;
    }
    .upgrade-input-group input {
        padding: 12px 15px;
        border-radius: 10px;
        border: 1px solid rgba(255,255,255,0.1);
        background: rgba(0,0,0,0.4);
        color: #fff;
        font-size: 18px;
        font-weight: 700;
        width: 130px;
        outline: none;
        transition: border-color 0.2s;
    }
    .upgrade-input-group input:focus {
        border-color: #feca57;
        box-shadow: 0 0 10px rgba(254, 202, 87, 0.2);
    }

    /* Кнопки процентов */
    .chance-buttons {
        display: flex;
        gap: 10px;
        width: 100%;
        justify-content: center;
        flex-wrap: wrap;
    }
    .chance-btn {
        background: rgba(255,255,255,0.05);
        border: 1px solid rgba(255,255,255,0.1);
        color: #a0a0a0;
        padding: 10px 15px;
        border-radius: 8px;
        font-size: 15px;
        font-weight: 700;
        cursor: pointer;
        transition: 0.2s;
    }
    .chance-btn:hover {
        background: rgba(255,255,255,0.1);
    }
    .chance-btn.active {
        background: #00ff88;
        color: #000;
        border-color: #00ff88;
        box-shadow: 0 0 15px rgba(0, 255, 136, 0.4);
    }
    
    .upgrade-info {
        font-size: 18px;
        color: #a0a0a0;
        background: linear-gradient(90deg, rgba(0, 255, 136, 0.1), rgba(0, 255, 136, 0.02));
        padding: 15px 30px;
        border-radius: 12px;
        border: 1px solid rgba(0, 255, 136, 0.2);
        width: 100%;
        text-align: center;
        z-index: 2;
    }
    .upgrade-info span { 
        color: #00ff88; 
        font-weight: 900; 
        font-size: 20px;
        text-shadow: 0 0 10px rgba(0, 255, 136, 0.5);
    }
    
    #upgradeResult {
        font-size: 24px;
        font-weight: 900;
        min-height: 35px;
        margin-top: 10px;
        text-transform: uppercase;
        letter-spacing: 1px;
        z-index: 2;
    }

    /* --- Конфети --- */
    #confetti-canvas {
        position: absolute;
        top: 0; left: 0;
        width: 100%; height: 100%;
        pointer-events: none;
        z-index: 10;
        display: none;
    }
</style>
</head>
<body>

<!-- ФОН С МОНЕТАМИ TON -->
<div id="ton-background"></div>

<header>
    <div class="logo" onclick="showHome()">FrogDrop</div>
    <div class="header-right">
        <div class="balance-display" id="balanceDisplay">
            <span id="balanceValue">100</span> ₽
        </div>
        <button class="btn-deposit" onclick="openDeposit()">Пополнить</button>
        <div class="nav-buttons">
            <button onclick="showProfile()">Профиль</button>
        </div>
    </div>
</header>

<!-- ГЛАВНАЯ СТРАНИЦА -->
<div class="container" id="homePage">
    <section class="hero-section">
        <div class="hero-text">
            <h1>3D Пепе <br>Твой NFT</h1>
            <p>Уникальный 3D-Пепе. Крути, верти и рассматривай со всех сторон. Web3 уже здесь.</p>
            <button class="btn-primary" onclick="alert('Минт в разработке. Нужен блокчейн.')">Сминетить NFT</button>
        </div>
        <div id="canvas-container">
            <div class="nft-badge">Legendary Pepe #001</div>
        </div>
    </section>

    <section class="games-section">
        <h2>Игры</h2>
        <div class="games-grid">
            <div class="game-card">
                <div class="game-icon">🚀</div>
                <h3>Crash</h3>
                <p>Ракета летит вверх, множитель растет. Успей забрать кэш до краша!</p>
                <button class="play-btn" onclick="openGame('crash')">Играть</button>
            </div>
            <div class="game-card">
                <div class="game-icon">💎</div>
                <h3>Апгрейд</h3>
                <p>Выбери процент выигрыша и забери свой куш, если повезет.</p>
                <button class="play-btn" onclick="openGame('upgrade')">Играть</button>
            </div>
        </div>
    </section>
</div>

<!-- СТРАНИЦА ПРОФИЛЯ -->
<div id="profilePage">
    <div class="profile-card">
        <img src="https://i.ibb.co/L5hZ4sN/pepe-drawing.jpg" alt="Pepe Avatar" class="profile-avatar" onerror="this.src='https://i.pinimg.com/736x/8c/6c/03/8c6c03b4f49c4f5b3e4a2b1c8d9e0f1a.jpg'">
        
        <div class="profile-name">Frog Player</div>
        <div class="profile-id" id="userIdDisplay">ID: FRG-0000-00</div>

        <div class="profile-stats">
            <div class="stat-box">
                <div class="stat-val" id="profileBalance">100 ₽</div>
                <div class="stat-label">Баланс</div>
            </div>
            <div class="stat-box">
                <div class="stat-val">0</div>
                <div class="stat-label">Побед</div>
            </div>
        </div>

        <button class="btn-primary" style="width: 100%;" onclick="showHome()">На главную</button>
    </div>
</div>

<!-- Окно пополнения -->
<div class="modal-overlay" id="depositModal">
    <div class="modal-content">
        <button class="close-btn" onclick="closeDeposit()">×</button>
        <h2>Пополнение баланса</h2>
        
        <div class="deposit-options">
            <div class="deposit-card" onclick="selectDeposit('stars')">
                <div class="icon">⭐</div>
                <div class="title">Звезды Telegram</div>
                <div class="desc">Быстро и безопасно</div>
            </div>
            <div class="deposit-card" onclick="toggleCrypto()">
                <div class="icon">🪙</div>
                <div class="title">Криптовалюта</div>
                <div class="desc">USDT / TON</div>
            </div>
            <div class="deposit-card" onclick="toggleFiat()">
                <div class="icon">💳</div>
                <div class="title">Рубли</div>
                <div class="desc">СБП / Банки</div>
            </div>
        </div>

        <div class="sub-options" id="cryptoOptions">
            <div class="deposit-card" onclick="selectDeposit('usdt')">
                <div class="icon">💵</div>
                <div class="title">USDT</div>
                <div class="desc">Tether (TRC20)</div>
            </div>
            <div class="deposit-card" onclick="selectDeposit('ton')">
                <div class="icon">💎</div>
                <div class="title">TON</div>
                <div class="desc">Toncoin</div>
            </div>
        </div>

        <div class="sub-options" id="fiatOptions" style="grid-template-columns: repeat(3, 1fr);">
            <div class="deposit-card bank-card" onclick="selectDeposit('tbank')">
                <div class="icon" style="color:#ffdd00; font-weight:900; font-size:24px;">Т</div>
                <div class="title">Т-Банк</div>
            </div>
            <div class="deposit-card bank-card" onclick="selectDeposit('sber')">
                <div class="icon" style="color:#0f9d58; font-weight:900; font-size:24px;">С</div>
                <div class="title">Сбербанк</div>
            </div>
            <div class="deposit-card bank-card" onclick="selectDeposit('alfa')">
                <div class="icon" style="color:#ef3124; font-weight:900; font-size:24px;">А</div>
                <div class="title">Альфа-Банк</div>
            </div>
        </div>
    </div>
</div>

<!-- Модальное окно Игр -->
<div class="modal-overlay" id="gameModal">
    <div class="modal-content" id="gameModalContent">
        <button class="close-btn" onclick="closeGame()">×</button>
        <h2 id="gameTitle">Игра</h2>
        
        <!-- CRASH GAME -->
        <div id="gameCrash" style="display:none;">
            <div id="crash-arena">
                <div class="crash-ui">
                    <div class="multiplier" id="multiplierDisplay">1.00x</div>
                </div>
                <canvas id="crash-canvas"></canvas>
                <div class="crash-controls">
                    <input type="number" id="betInput" class="bet-input" value="100" min="10">
                    <button id="actionBtn" class="action-btn btn-green" onclick="crashAction()">Забрать</button>
                    <button id="startCrashBtn" class="action-btn btn-green" onclick="startCrash()" style="display:none;">Начать</button>
                </div>
            </div>
        </div>

        <!-- UPGRADE WHEEL GAME -->
        <div id="gameUpgrade" style="display:none;">
            <div class="upgrade-container">
                <canvas id="confetti-canvas"></canvas>

                <div class="upgrade-wheel-wrapper">
                    <div class="upgrade-wheel-bg" id="upgradeWheelBg"></div>
                    
                    <div class="upgrade-arrow-container" id="upgradeArrow">
                        <svg viewBox="0 0 340 340" style="width: 100%; height: 100%;">
                            <defs>
                                <linearGradient id="goldGradient" x1="0%" y1="0%" x2="100%" y2="100%">
                                    <stop offset="0%" stop-color="#fff200" />
                                    <stop offset="40%" stop-color="#ffcc00" />
                                    <stop offset="100%" stop-color="#cc8800" />
                                </linearGradient>
                                <linearGradient id="goldDark" x1="0%" y1="0%" x2="100%" y2="100%">
                                    <stop offset="0%" stop-color="#b37700" />
                                    <stop offset="100%" stop-color="#664400" />
                                </linearGradient>
                            </defs>
                            
                            <g transform="translate(170, 170) rotate(180)">
                                <path d="M -4 -10 L -4 -140 L -10 -140 L 0 -160 L 10 -140 L 4 -140 L 4 -10 Z" 
                                      fill="url(#goldDark)" transform="translate(1, 1)" />
                                <path d="M -4 -10 L -4 -140 L -10 -140 L 0 -160 L 10 -140 L 4 -140 L 4 -10 Z" 
                                      fill="url(#goldGradient)" />
                                <path d="M 0 -155 L -1 -140 L 0 -140 Z" fill="rgba(255,255,255,0.8)" />
                            </g>
                        </svg>
                    </div>

                    <div class="upgrade-center-wrapper">
                        <!-- Текст скрыт через CSS, показывается иконка -->
                        <div class="frogdrop-logo-text">FROG<br>DROP<span>x100</span></div>
                    </div>
                </div>

                <div class="upgrade-result" id="upgradeResult"></div>
                
                <div class="upgrade-controls">
                    <div class="input-row">
                        <div class="upgrade-input-group">
                            <label>Ставка (₽)</label>
                            <input type="number" id="upgradeBet" value="10" min="10">
                        </div>
                    </div>
                    
                    <div class="chance-buttons" id="chanceButtons">
                        <button class="chance-btn" data-chance="10">10%</button>
                        <button class="chance-btn" data-chance="20">20%</button>
                        <button class="chance-btn active" data-chance="35">35%</button>
                        <button class="chance-btn" data-chance="50">50%</button>
                        <button class="chance-btn" data-chance="70">70%</button>
                    </div>

                    <button class="btn-primary" style="padding: 14px 30px; font-size: 16px; width: 100%;" onclick="spinUpgrade()" id="spinUpgradeBtn">Апгрейд</button>
                </div>
                <div class="upgrade-info">
                    Потенциальный выигрыш: <span id="potentialWin">28.57</span> ₽
                </div>
            </div>
        </div>

    </div>
</div>

<!-- Three.js -->
<script type="importmap">
{
    "imports": {
        "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
        "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
    }
}
</script>

<script type="module">
    import * as THREE from 'three';
    import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

    // ================= ГЕНЕРАЦИЯ ФОНА TON =================
    function initTonBackground() {
        const bg = document.getElementById('ton-background');
        const coinCount = 100;
        
        for (let i = 0; i < coinCount; i++) {
            const coin = document.createElement('div');
            coin.classList.add('ton-coin');
            
            const size = Math.random() * 12 + 8;
            coin.style.width = size + 'px';
            coin.style.height = size + 'px';
            
            coin.style.left = Math.random() * 100 + 'vw';
            
            const duration = Math.random() * 20 + 15;
            const delay = Math.random() * -35;
            coin.style.animationDuration = duration + 's';
            coin.style.animationDelay = delay + 's';
            
            bg.appendChild(coin);
        }
    }
    initTonBackground();

    // ================= ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ =================
    let currentBalance = 100;
    let confettiActive = false;
    let confettiParticles = [];
    let confettiAnimId = null;
    let upgradeSpinning = false;
    let currentArrowRotation = 0;
    let crashActive = false;
    let crashCrashed = false;
    let crashMultiplier = 1.00;
    let crashStartTime = 0;
    let crashAnimId = null;
    let currentCrashBet = 0;
    let currentChance = 35;

    // ================= СИСТЕМА БАЛАНСА =================
    const balanceEl = document.getElementById('balanceValue');
    const profileBalanceEl = document.getElementById('profileBalance');

    window.updateBalance = function(amount) {
        currentBalance += amount;
        if(currentBalance < 0) currentBalance = 0;
        balanceEl.textContent = currentBalance.toFixed(0);
        if(profileBalanceEl) profileBalanceEl.textContent = currentBalance.toFixed(0) + ' ₽';
    };

    // ================= ГЕНЕРАЦИЯ ID =================
    function generateUserId() {
        const part1 = Math.floor(1000 + Math.random() * 9000);
        const part2 = Math.floor(10 + Math.random() * 90);
        return `FRG-${part1}-${part2}`;
    }

    let userId = localStorage.getItem('frogdrop_userid');
    if(!userId) {
        userId = generateUserId();
        localStorage.setItem('frogdrop_userid', userId);
    }
    document.getElementById('userIdDisplay').textContent = `ID: ${userId}`;

    // ================= НАВИГАЦИЯ =================
    window.showProfile = () => {
        document.getElementById('homePage').style.display = 'none';
        document.getElementById('profilePage').style.display = 'flex';
        updateBalance(0);
    };

    window.showHome = () => {
        document.getElementById('profilePage').style.display = 'none';
        document.getElementById('homePage').style.display = 'block';
    };

    // ================= ПОПОЛНЕНИЕ =================
    window.openDeposit = () => {
        document.getElementById('depositModal').classList.add('active');
    };
    window.closeDeposit = () => {
        document.getElementById('depositModal').classList.remove('active');
        document.getElementById('cryptoOptions').classList.remove('active');
        document.getElementById('fiatOptions').classList.remove('active');
    };
    
    window.toggleCrypto = () => {
        const crypto = document.getElementById('cryptoOptions');
        const fiat = document.getElementById('fiatOptions');
        if(!crypto.classList.contains('active')) fiat.classList.remove('active');
        crypto.classList.toggle('active');
    };
    
    window.toggleFiat = () => {
        const crypto = document.getElementById('cryptoOptions');
        const fiat = document.getElementById('fiatOptions');
        if(!fiat.classList.contains('active')) crypto.classList.remove('active');
        fiat.classList.toggle('active');
    };

    window.selectDeposit = (type) => {
        alert(`Выбран способ: ${type.toUpperCase()}. (Функция в разработке)`);
        closeDeposit();
    };

    // ================= 3D ПЕПЕ =================
    function createPepe3D() {
        const group = new THREE.Group();
        
        const greenMat = new THREE.MeshStandardMaterial({ color: 0x55a82e, roughness: 0.5, metalness: 0.1 });
        const darkGreenMat = new THREE.MeshStandardMaterial({ color: 0x3d7a1c, roughness: 0.8 });
        const blueMat = new THREE.MeshStandardMaterial({ color: 0x1a5fb4, roughness: 0.5 });
        const redMat = new THREE.MeshStandardMaterial({ color: 0xc01c28, roughness: 0.4 });
        const whiteMat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 0.1 });
        const darkBlueMat = new THREE.MeshStandardMaterial({ color: 0x0a2a4a, roughness: 0.2 });
        const yellowMat = new THREE.MeshStandardMaterial({ color: 0xffcc00, roughness: 0.2 });
        const orangeMat = new THREE.MeshStandardMaterial({ color: 0xff8800, roughness: 0.4 });

        const bodyGeo = new THREE.SphereGeometry(1.2, 32, 32);
        const body = new THREE.Mesh(bodyGeo, greenMat);
        body.scale.set(1, 1.2, 0.8);
        body.position.y = -0.5;
        group.add(body);

        const headGeo = new THREE.SphereGeometry(1, 32, 32);
        const head = new THREE.Mesh(headGeo, greenMat);
        head.scale.set(1.1, 0.9, 0.9);
        head.position.y = 0.6;
        group.add(head);

        const backGeo = new THREE.SphereGeometry(1.05, 16, 16);
        const back = new THREE.Mesh(backGeo, darkGreenMat);
        back.scale.set(1.1, 1.3, 1);
        back.position.set(0, -0.4, -0.6);
        group.add(back);

        const bandanaGeo = new THREE.CylinderGeometry(1.12, 1.12, 0.4, 32, 1, true);
        const bandana = new THREE.Mesh(bandanaGeo, blueMat);
        bandana.position.y = 0.8;
        group.add(bandana);
        
        const tailGeo = new THREE.ConeGeometry(0.25, 0.9, 4);
        const tail1 = new THREE.Mesh(tailGeo, blueMat);
        tail1.position.set(-0.7, 0.6, -0.8);
        tail1.rotation.z = Math.PI / 2;
        tail1.rotation.y = Math.PI / 4;
        group.add(tail1);
        
        const tail2 = new THREE.Mesh(tailGeo, blueMat);
        tail2.position.set(-1.0, 0.3, -0.8);
        tail2.rotation.z = Math.PI / 2;
        tail2.rotation.y = Math.PI / 4;
        group.add(tail2);

        const eyeGeo = new THREE.SphereGeometry(0.5, 32, 32);
        
        const eyeL = new THREE.Mesh(eyeGeo, whiteMat);
        eyeL.position.set(-0.55, 0.6, 0.75);
        eyeL.scale.set(1, 1.1, 0.5);
        group.add(eyeL);

        const pupilGeo = new THREE.SphereGeometry(0.28, 32, 32);
        const pupilL = new THREE.Mesh(pupilGeo, darkBlueMat);
        pupilL.position.set(-0.55, 0.6, 1.0);
        pupilL.scale.set(1, 1.1, 0.5);
        group.add(pupilL);

        const highlightGeo = new THREE.SphereGeometry(0.1, 16, 16);
        const highlightL = new THREE.Mesh(highlightGeo, whiteMat);
        highlightL.position.set(-0.45, 0.75, 1.15);
        group.add(highlightL);

        const eyeR = new THREE.Mesh(eyeGeo, whiteMat);
        eyeR.position.set(0.55, 0.6, 0.75);
        eyeR.scale.set(1, 1.1, 0.5);
        group.add(eyeR);

        const pupilR = new THREE.Mesh(pupilGeo, darkBlueMat);
        pupilR.position.set(0.55, 0.6, 1.0);
        pupilR.scale.set(1, 1.1, 0.5);
        group.add(pupilR);

        const highlightR = new THREE.Mesh(highlightGeo, whiteMat);
        highlightR.position.set(0.65, 0.75, 1.15);
        group.add(highlightR);

        const lipsGeo = new THREE.TorusGeometry(0.55, 0.22, 16, 32, Math.PI);
        const lips = new THREE.Mesh(lipsGeo, redMat);
        lips.position.set(0, 0.1, 0.95);
        lips.rotation.x = Math.PI;
        lips.rotation.z = Math.PI;
        group.add(lips);

        const heartGroup = new THREE.Group();
        const heartSphereL = new THREE.Mesh(new THREE.SphereGeometry(0.35, 16, 16), yellowMat);
        heartSphereL.position.set(-0.15, 0.15, 0);
        heartGroup.add(heartSphereL);
        const heartSphereR = new THREE.Mesh(new THREE.SphereGeometry(0.35, 16, 16), yellowMat);
        heartSphereR.position.set(0.15, 0.15, 0);
        heartGroup.add(heartSphereR);
        const heartCone = new THREE.Mesh(new THREE.ConeGeometry(0.5, 0.7, 16), yellowMat);
        heartCone.rotation.z = Math.PI;
        heartCone.position.set(0, -0.3, 0);
        heartGroup.add(heartCone);

        const heartOutline = new THREE.Mesh(new THREE.TorusGeometry(0.5, 0.06, 8, 32), orangeMat);
        heartOutline.position.set(0, -0.1, 0.1);
        heartOutline.scale.set(1, 1.2, 1);
        heartGroup.add(heartOutline);

        heartGroup.position.set(0, -0.5, 0.8);
        heartGroup.scale.set(1.2, 1.2, 1.2);
        group.add(heartGroup);

        const armLGeo = new THREE.CylinderGeometry(0.15, 0.15, 1.2);
        const armL = new THREE.Mesh(armLGeo, greenMat);
        armL.position.set(-1, -0.3, 0.3);
        armL.rotation.z = Math.PI / 3;
        armL.rotation.x = -Math.PI / 4;
        group.add(armL);
        const cuffL = new THREE.Mesh(new THREE.CylinderGeometry(0.18, 0.18, 0.3), blueMat);
        cuffL.position.set(-1.4, -0.7, 0.5);
        cuffL.rotation.z = Math.PI / 3;
        cuffL.rotation.x = -Math.PI / 4;
        group.add(cuffL);

        const armRGeo = new THREE.CylinderGeometry(0.15, 0.15, 1.2);
        const armR = new THREE.Mesh(armRGeo, greenMat);
        armR.position.set(1, -0.3, 0.3);
        armR.rotation.z = -Math.PI / 3;
        armR.rotation.x = -Math.PI / 4;
        group.add(armR);
        const cuffR = new THREE.Mesh(new THREE.CylinderGeometry(0.18, 0.18, 0.3), blueMat);
        cuffR.position.set(1.4, -0.7, 0.5);
        cuffR.rotation.z = -Math.PI / 3;
        cuffR.rotation.x = -Math.PI / 4;
        group.add(cuffR);

        const legL = new THREE.Mesh(new THREE.CylinderGeometry(0.2, 0.2, 0.8), greenMat);
        legL.position.set(-0.6, -1.5, 0.2);
        legL.rotation.x = Math.PI / 4;
        legL.rotation.z = -Math.PI / 6;
        group.add(legL);
        const cuffLegL = new THREE.Mesh(new THREE.CylinderGeometry(0.22, 0.22, 0.3), blueMat);
        cuffLegL.position.set(-0.6, -1.2, 0.4);
        cuffLegL.rotation.x = Math.PI / 4;
        cuffLegL.rotation.z = -Math.PI / 6;
        group.add(cuffLegL);

        const legR = new THREE.Mesh(new THREE.CylinderGeometry(0.2, 0.2, 0.8), greenMat);
        legR.position.set(0.6, -1.5, 0.2);
        legR.rotation.x = Math.PI / 4;
        legR.rotation.z = Math.PI / 6;
        group.add(legR);
        const cuffLegR = new THREE.Mesh(new THREE.CylinderGeometry(0.22, 0.22, 0.3), blueMat);
        cuffLegR.position.set(0.6, -1.2, 0.4);
        cuffLegR.rotation.x = Math.PI / 4;
        cuffLegR.rotation.z = Math.PI / 6;
        group.add(cuffLegR);

        return group;
    }

    // ================= 3D СЦЕНА =================
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    
    const camera = new THREE.PerspectiveCamera(45, container.clientWidth / container.clientHeight, 0.1, 1000);
    camera.position.set(0, 0, 7);

    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setSize(container.clientWidth, container.clientHeight);
    renderer.setPixelRatio(window.devicePixelRatio);
    container.appendChild(renderer.domElement);

    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;
    controls.autoRotate = true;
    controls.autoRotateSpeed = 1.5;
    controls.enableZoom = false;
    controls.enablePan = false;

    const ambientLight = new THREE.AmbientLight(0xffffff, 0.8);
    scene.add(ambientLight);

    const dirLight = new THREE.DirectionalLight(0xffffff, 2);
    dirLight.position.set(2, 5, 5);
    scene.add(dirLight);

    const backLight = new THREE.PointLight(0x00d2d3, 2, 10);
    backLight.position.set(-3, 2, -3);
    scene.add(backLight);

    const mainPepe = createPepe3D();
    mainPepe.position.y = 0.5;
    mainPepe.scale.set(0.9, 0.9, 0.9);
    scene.add(mainPepe);

    function animate3D() {
        requestAnimationFrame(animate3D);
        mainPepe.rotation.z = Math.sin(Date.now() * 0.001) * 0.05;
        controls.update();
        renderer.render(scene, camera);
    }

    window.addEventListener('resize', () => {
        camera.aspect = container.clientWidth / container.clientHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(container.clientWidth, container.clientHeight);
    });

    animate3D();

    // ================= ИГРЫ =================
    window.openGame = function(gameId) {
        document.getElementById('gameModal').classList.add('active');
        document.getElementById('gameCrash').style.display = 'none';
        document.getElementById('gameUpgrade').style.display = 'none';
        
        if(gameId === 'crash') {
            document.getElementById('gameTitle').textContent = 'Crash';
            document.getElementById('gameCrash').style.display = 'block';
            resetCrash();
        } else if(gameId === 'upgrade') {
            document.getElementById('gameTitle').textContent = 'Апгрейд';
            document.getElementById('gameUpgrade').style.display = 'block';
            initUpgradeWheel();
            resizeConfettiCanvas();
        }
    };

    window.closeGame = function() {
        document.getElementById('gameModal').classList.remove('active');
        stopCrash();
        stopConfetti();
    };

    // ================= ИГРА CRASH =================
    const multiplierDisplay = document.getElementById('multiplierDisplay');
    const actionBtn = document.getElementById('actionBtn');
    const startCrashBtn = document.getElementById('startCrashBtn');
    const canvas = document.getElementById('crash-canvas');
    const ctx = canvas.getContext('2d');

    const rocketImg = new Image();
    rocketImg.src = "data:image/svg+xml;utf8,<svg viewBox='0 0 100 100' xmlns='http://www.w3.org/2000/svg'><path d='M 50 10 L 65 40 L 60 80 L 50 90 L 40 80 L 35 40 Z' fill='%23ffffff'/><path d='M 50 10 L 65 40 L 50 45 L 35 40 Z' fill='%23ff4757'/><circle cx='50' cy='40' r='5' fill='%230b0e14'/><path d='M 40 80 L 30 95 L 50 90 L 70 95 L 60 80' fill='%23feca57'/></svg>";

    function resizeCanvas() {
        canvas.width = canvas.parentElement.clientWidth;
        canvas.height = canvas.parentElement.clientHeight;
    }
    window.addEventListener('resize', resizeCanvas);
    setTimeout(resizeCanvas, 100);

    function resetCrash() {
        stopCrash();
        crashMultiplier = 1.00;
        multiplierDisplay.textContent = '1.00x';
        multiplierDisplay.classList.remove('crashed');
        actionBtn.className = 'action-btn btn-green';
        actionBtn.textContent = 'Забрать';
        actionBtn.disabled = true;
        startCrashBtn.style.display = 'block';
        startCrashBtn.className = 'action-btn btn-green';
        
        resizeCanvas();
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawGrid();
    }

    function drawGrid() {
        ctx.strokeStyle = 'rgba(255,255,255,0.05)';
        ctx.lineWidth = 1;
        for(let i = 0; i < canvas.width; i += 40) {
            ctx.beginPath(); ctx.moveTo(i, 0); ctx.lineTo(i, canvas.height); ctx.stroke();
        }
        for(let i = 0; i < canvas.height; i += 40) {
            ctx.beginPath(); ctx.moveTo(0, i); ctx.lineTo(canvas.width, i); ctx.stroke();
        }
    }

    function stopCrash() {
        crashActive = false;
        if(crashAnimId) cancelAnimationFrame(crashAnimId);
    }

    window.startCrash = function() {
        currentCrashBet = parseFloat(document.getElementById('betInput').value);
        if(isNaN(currentCrashBet) || currentCrashBet < 10) { alert('Минимальная ставка 10₽'); return; }
        if(currentCrashBet > currentBalance) { alert('Недостаточно средств!'); return; }

        updateBalance(-currentCrashBet);
        resetCrash();
        crashActive = true;
        crashCrashed = false;
        crashMultiplier = 1.00;
        crashStartTime = Date.now();
        
        startCrashBtn.style.display = 'none';
        actionBtn.disabled = false;
        
        const r = Math.random();
        let crashPoint = 1.0;
        if (r < 0.4) crashPoint = 1.0 + Math.random() * 0.5;
        else if (r < 0.7) crashPoint = 1.5 + Math.random() * 1.5;
        else if (r < 0.9) crashPoint = 3.0 + Math.random() * 3.0;
        else crashPoint = 6.0 + Math.random() * 4.0;

        let points = [];
        
        function animateCrash() {
            if(!crashActive) return;
            
            const elapsed = (Date.now() - crashStartTime) / 1000;
            crashMultiplier = Math.pow(Math.E, 0.3 * elapsed);
            
            if (crashMultiplier >= crashPoint) {
                crashActive = false;
                crashCrashed = true;
                multiplierDisplay.classList.add('crashed');
                multiplierDisplay.textContent = 'CRASHED 💥';
                actionBtn.disabled = true;
                actionBtn.textContent = 'Краш';
                startCrashBtn.style.display = 'block';
                startCrashBtn.textContent = 'Еще раз';
                drawCrashScreen();
                return;
            }
            
            multiplierDisplay.textContent = crashMultiplier.toFixed(2) + 'x';
            
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawGrid();
            
            const x = (elapsed * 50) % canvas.width;
            const y = canvas.height - (Math.log(crashMultiplier) * 100);
            
            points.push({x: x, y: y});
            if(points.length > 100) points.shift();
            
            ctx.beginPath();
            ctx.strokeStyle = '#4cd137';
            ctx.lineWidth = 4;
            ctx.lineCap = 'round';
            ctx.lineJoin = 'round';
            
            const offset = Math.max(0, (elapsed * 50) - canvas.width);
            
            for(let i = 0; i < points.length; i++) {
                const px = points[i].x - offset;
                if(px < 0) continue;
                if(i === 0) ctx.moveTo(px, points[i].y);
                else ctx.lineTo(px, points[i].y);
            }
            ctx.stroke();
            
            if (rocketImg.complete) {
                ctx.drawImage(rocketImg, canvas.width - 70, y - 30, 40, 40);
            }
            
            ctx.lineTo(canvas.width, canvas.height);
            ctx.lineTo(0, canvas.height);
            ctx.fillStyle = 'rgba(76, 209, 55, 0.1)';
            ctx.fill();
            
            crashAnimId = requestAnimationFrame(animateCrash);
        }
        
        animateCrash();
    };

    function drawCrashScreen() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = 'rgba(255, 71, 87, 0.2)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = '#ff4757';
        ctx.font = 'bold 40px Arial';
        ctx.textAlign = 'center';
        ctx.fillText('CRASHED', canvas.width/2, canvas.height/2);
    }

    window.crashAction = function() {
        if(!crashActive) return;
        
        crashActive = false;
        if(crashAnimId) cancelAnimationFrame(crashAnimId);
        
        const winAmount = currentCrashBet * crashMultiplier;
        updateBalance(winAmount);
        alert(`Ты забрал на ${crashMultiplier.toFixed(2)}x! Выигрыш: ${winAmount.toFixed(2)}₽`);
        
        actionBtn.disabled = true;
        actionBtn.textContent = 'Забрал';
        startCrashBtn.style.display = 'block';
        startCrashBtn.textContent = 'Еще раз';
    };

    // ================= КОНФЕТИ =================
    const confettiCanvas = document.getElementById('confetti-canvas');
    const confettiCtx = confettiCanvas.getContext('2d');

    function resizeConfettiCanvas() {
        const container = document.querySelector('.upgrade-container');
        confettiCanvas.width = container.clientWidth;
        confettiCanvas.height = container.clientHeight;
    }

    window.addEventListener('resize', resizeConfettiCanvas);

    function startConfetti() {
        resizeConfettiCanvas();
        confettiCanvas.style.display = 'block';
        confettiActive = true;
        confettiParticles = [];

        const colors = ['#feca57', '#ff6b6b', '#48dbfb', '#1dd1a1', '#f368e0', '#ffffff'];

        for (let i = 0; i < 150; i++) {
            confettiParticles.push({
                x: confettiCanvas.width / 2,
                y: confettiCanvas.height / 2,
                vx: (Math.random() - 0.5) * 20,
                vy: (Math.random() - 1) * 15,
                size: Math.random() * 8 + 4,
                color: colors[Math.floor(Math.random() * colors.length)],
                rotation: Math.random() * 360,
                rotationSpeed: (Math.random() - 0.5) * 10,
                gravity: 0.3,
                drag: 0.98,
                life: 1
            });
        }

        if (confettiAnimId) cancelAnimationFrame(confettiAnimId);
        animateConfetti();
    }

    function animateConfetti() {
        if (!confettiActive) return;
        
        confettiCtx.clearRect(0, 0, confettiCanvas.width, confettiCanvas.height);
        
        let activeParticles = 0;

        confettiParticles.forEach(p => {
            if (p.life <= 0) return;
            activeParticles++;

            p.vx *= p.drag;
            p.vy *= p.drag;
            p.vy += p.gravity;
            p.x += p.vx;
            p.y += p.vy;
            p.rotation += p.rotationSpeed;
            p.life -= 0.01;

            confettiCtx.save();
            confettiCtx.translate(p.x, p.y);
            confettiCtx.rotate(p.rotation * Math.PI / 180);
            confettiCtx.fillStyle = p.color;
            confettiCtx.globalAlpha = Math.max(0, p.life);
            
            confettiCtx.fillRect(-p.size / 2, -p.size / 4, p.size, p.size / 2);
            
            confettiCtx.restore();
        });

        if (activeParticles > 0) {
            confettiAnimId = requestAnimationFrame(animateConfetti);
        } else {
            confettiCanvas.style.display = 'none';
            confettiActive = false;
        }
    }

    function stopConfetti() {
        confettiActive = false;
        if (confettiAnimId) cancelAnimationFrame(confettiAnimId);
        confettiCtx.clearRect(0, 0, confettiCanvas.width, confettiCanvas.height);
        confettiCanvas.style.display = 'none';
    }

    // ================= ИГРА АПГРЕЙД КОЛЕСО =================
    const upgradeWheelBgEl = document.getElementById('upgradeWheelBg');
    const upgradeArrowEl = document.getElementById('upgradeArrow');
    const upgradeResultEl = document.getElementById('upgradeResult');
    const spinUpgradeBtn = document.getElementById('spinUpgradeBtn');
    const upgradeBetInput = document.getElementById('upgradeBet');
    const potentialWinEl = document.getElementById('potentialWin');
    const chanceBtns = document.querySelectorAll('.chance-btn');

    function updatePotentialWin() {
        const bet = parseFloat(upgradeBetInput.value) || 0;
        const multiplier = 100 / currentChance;
        const win = bet * multiplier;
        potentialWinEl.textContent = win.toFixed(2);
    }

    upgradeBetInput.addEventListener('input', updatePotentialWin);

    chanceBtns.forEach(btn => {
        btn.addEventListener('click', (e) => {
            if(upgradeSpinning) return;
            
            chanceBtns.forEach(b => b.classList.remove('active'));
            e.target.classList.add('active');
            
            currentChance = parseInt(e.target.dataset.chance);
            updatePotentialWin();
            initUpgradeWheel();
        });
    });

    function initUpgradeWheel() {
        const chance = currentChance;
        
        const halfGreenAngle = (chance * 3.6) / 2;
        const greenStart = 180 - halfGreenAngle;
        const greenEnd = 180 + halfGreenAngle;

        upgradeWheelBgEl.style.background = `conic-gradient(
            #1e2329 0deg ${greenStart}deg, 
            #00ff88 ${greenStart}deg ${greenEnd}deg,
            #1e2329 ${greenEnd}deg 360deg
        )`;
        
        upgradeWheelBgEl.style.boxShadow = `inset 0 0 60px rgba(0,0,0,0.9), inset 0 0 20px rgba(0, 255, 136, ${chance/100 * 0.3})`;
        
        currentArrowRotation = 0;
        upgradeArrowEl.style.transform = `rotate(0deg)`;
        
        upgradeResultEl.textContent = '';
        upgradeResultEl.style.color = '#fff';
        updatePotentialWin();
        stopConfetti();
    }

    window.spinUpgrade = function() {
        if(upgradeSpinning) return;
        
        const bet = parseFloat(upgradeBetInput.value);
        const chance = currentChance;
        
        if(isNaN(bet) || bet < 10) { alert('Минимальная ставка 10₽'); return; }
        if(bet > currentBalance) { alert('Недостаточно средств!'); return; }
        
        updateBalance(-bet);

        upgradeSpinning = true;
        spinUpgradeBtn.disabled = true;
        spinUpgradeBtn.textContent = 'Крутится...';
        upgradeResultEl.textContent = '';
        stopConfetti();
        
        const isWin = Math.random() * 100 < chance;
        
        let targetWheelAngle;
        
        if (isWin) {
            const halfGreenAngle = (chance * 3.6) / 2;
            const greenStart = 180 - halfGreenAngle;
            const greenEnd = 180 + halfGreenAngle;
            targetWheelAngle = Math.random() * (greenEnd - greenStart - 6) + greenStart + 3;
        } else {
            const halfGreenAngle = (chance * 3.6) / 2;
            const greenStart = 180 - halfGreenAngle;
            const greenEnd = 180 + halfGreenAngle;
            
            const isFirstHalf = Math.random() < 0.5;
            
            if (isFirstHalf) {
                targetWheelAngle = Math.random() * (greenStart - 6) + 3;
            } else {
                targetWheelAngle = Math.random() * (360 - greenEnd - 6) + greenEnd + 3;
            }
        }
        
        const rotations = Math.random() < 0.5 ? 2 : 3;
        const fullRotations = 360 * rotations;
        
        let angleDiff = targetWheelAngle - (currentArrowRotation % 360);
        while (angleDiff < 0) angleDiff += 360;
        
        const finalAngle = currentArrowRotation + fullRotations + angleDiff;
        
        upgradeArrowEl.style.transform = `rotate(${finalAngle}deg)`;
        currentArrowRotation = finalAngle;
        
        setTimeout(() => {
            upgradeSpinning = false;
            spinUpgradeBtn.disabled = false;
            spinUpgradeBtn.textContent = 'Апгрейд';
            
            const winAmount = bet * (100 / chance);
            
            if(isWin) {
                updateBalance(winAmount);
                upgradeResultEl.textContent = `УСПЕШНОЕ УЛУЧШЕНИЕ! +${winAmount.toFixed(2)} ₽`;
                upgradeResultEl.style.color = '#00ff88';
                upgradeResultEl.style.textShadow = '0 0 15px rgba(0, 255, 136, 0.6)';
                
                startConfetti();
                
            } else {
                upgradeResultEl.textContent = `НЕУДАЧНОЕ УЛУЧШЕНИЕ. -${bet.toFixed(2)} ₽`;
                upgradeResultEl.style.color = '#ff4757';
                upgradeResultEl.style.textShadow = '0 0 15px rgba(255, 71, 87, 0.6)';
            }
        }, 8000);
    };

</script>
</body>
</html>
