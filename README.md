<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Вход</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; }
        body {
            background: linear-gradient(135deg, #1c92d2, #0b5e8a);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .login-card {
            background: white;
            width: 360px;
            padding: 40px 30px;
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            text-align: center;
        }
        .logo {
            width: 80px;
            height: 80px;
            margin: 0 auto 20px;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .logo svg {
            width: 100%;
            height: 100%;
            filter: drop-shadow(0 4px 10px rgba(0, 136, 204, 0.35));
        }
        h1 { font-size: 22px; margin-bottom: 10px; color: #333; }
        p { font-size: 14px; color: #666; margin-bottom: 25px; }
        input {
            width: 100%;
            padding: 12px 15px;
            margin-bottom: 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
            outline: none;
            transition: border 0.2s;
        }
        input:focus { border-color: #0088cc; }
        button {
            width: 100%;
            padding: 12px;
            background: #0088cc;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 500;
            cursor: pointer;
            transition: background 0.2s;
        }
        button:hover { background: #0077b3; }
        .footer {
            margin-top: 20px;
            font-size: 12px;
            color: #999;
        }
    </style>
</head>
<body>
    <div class="login-card">
        <div class="logo">
            <svg viewBox="0 0 240 240" xmlns="http://www.w3.org/2000/svg">
                <defs>
                    <linearGradient id="tg-grad" x1="0%" y1="0%" x2="100%" y2="100%">
                        <stop offset="0%" stop-color="#37AEE2"/>
                        <stop offset="100%" stop-color="#1E96C8"/>
                    </linearGradient>
                </defs>
                <circle cx="120" cy="120" r="120" fill="url(#tg-grad)"/>
                <path fill="#FFFFFF" d="M54.3 118.8l155.9-60.1c7.2-2.7 13.5 1.7 11.1 12.1l-26.5 125.1c-2 9-7.3 11.2-14.8 7l-40.9-30.2-19.7 19c-2.2 2.2-4 4-8.2 4l2.9-41.7 75.9-68.6c3.3-2.9-.7-4.5-5.1-1.6l-93.8 59.1-40.4-12.6c-8.8-2.8-9-8.8 1.6-12.1z"/>
            </svg>
        </div>
        <h1>Вход в аккаунт</h1>
        <p>Введите номер телефона, чтобы продолжить</p>
        <input type="tel" placeholder="Номер телефона" id="phone">
        <button onclick="alert('Это учебная страница. Вход не выполняется.')">Далее</button
    </div>
</body>
</html>
