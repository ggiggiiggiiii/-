<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Mines Game</title>
    <style>
        :root {
            --neon-blue: #00f3ff;
            --neon-pink: #ff00f7;
            --neon-green: #00ff9d;
            --deep-space: #0a0a1a;
            --star-glow: rgba(255, 255, 255, 0.8);
        }
        
        * {
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: 'Orbitron', sans-serif;
            background-color: var(--deep-space);
            color: white;
            margin: 0;
            padding: 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            overflow-x: hidden;
            touch-action: manipulation;
            background-image: 
                radial-gradient(circle at 20% 30%, rgba(0, 243, 255, 0.1) 0%, transparent 20%),
                radial-gradient(circle at 80% 70%, rgba(255, 0, 247, 0.1) 0%, transparent 20%);
        }
        
        .container {
            width: 100%;
            max-width: 500px;
            position: relative;
            z-index: 1;
        }
        
        .stars-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 0;
        }
        
        .star-bg {
            position: absolute;
            background-color: white;
            border-radius: 50%;
            animation: twinkle 3s infinite alternate;
        }
        
        @keyframes twinkle {
            0% { opacity: 0.2; }
            100% { opacity: 1; }
        }
        
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            padding: 12px;
            background: rgba(10, 10, 26, 0.7);
            border-radius: 8px;
            border: 1px solid var(--neon-blue);
            box-shadow: 0 0 10px rgba(0, 243, 255, 0.3);
            width: 100%;
        }
        
        h1 {
            margin: 0;
            font-size: clamp(20px, 6vw, 28px);
            background: linear-gradient(90deg, var(--neon-blue), var(--neon-pink));
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 0 8px rgba(0, 243, 255, 0.5);
            white-space: nowrap;
        }
        
        .online {
            color: var(--neon-green);
            font-weight: bold;
            font-size: clamp(14px, 4vw, 16px);
            text-shadow: 0 0 4px var(--neon-green);
        }
        
        .controls {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            margin-bottom: 15px;
            gap: 8px;
            width: 100%;
        }
        
        .cross-selector {
            display: flex;
            gap: 4px;
            background: rgba(10, 10, 26, 0.7);
            padding: 8px;
            border-radius: 8px;
            border: 1px solid var(--neon-pink);
            box-shadow: 0 0 10px rgba(255, 0, 247, 0.3);
            flex-grow: 1;
            justify-content: space-around;
        }
        
        .cross-option {
            padding: 4px 8px;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.2s;
            border: 1px solid transparent;
            font-size: clamp(14px, 4vw, 16px);
            text-align: center;
            flex: 1;
        }
        
        .cross-option:hover {
            border-color: var(--neon-blue);
        }
        
        .cross-option.active {
            background: var(--neon-pink);
            color: black;
            font-weight: bold;
            box-shadow: 0 0 8px var(--neon-pink);
        }
        
        .game-board {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 8px;
            margin-bottom: 15px;
            width: 100%;
            aspect-ratio: 1/1;
            max-height: 80vw;
            max-width: 80vw;
            margin-left: auto;
            margin-right: auto;
        }
        
        .cell {
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(20, 20, 40, 0.7);
            border-radius: 6px;
            font-size: clamp(20px, 6vw, 30px);
            cursor: pointer;
            transition: all 0.2s;
            position: relative;
            overflow: hidden;
            border: 1px solid rgba(0, 243, 255, 0.3);
            box-shadow: 0 0 8px rgba(0, 0, 0, 0.5);
            user-select: none;
        }
        
        .cell::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255, 255, 255, 0.1) 0%, transparent 70%);
            opacity: 0;
            transition: opacity 0.2s;
        }
        
        .cell:hover::before {
            opacity: 1;
        }
        
        .cell:active {
            transform: scale(0.95);
        }
        
        .star {
            color: gold;
            text-shadow: 0 0 8px gold;
            animation: float 3s infinite ease-in-out;
        }
        
        @keyframes float {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            50% { transform: translateY(-6px) rotate(5deg); }
        }
        
        .cross {
            color: red;
            text-shadow: 0 0 12px red;
            animation: pulse 0.5s ease-out;
            position: relative;
            z-index: 2;
        }
        
        @keyframes pulse {
            0% { transform: scale(0.5); opacity: 0; }
            70% { transform: scale(1.2); opacity: 1; }
            100% { transform: scale(1); }
        }
        
        .cross::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: radial-gradient(circle, rgba(255, 0, 0, 0.4) 0%, transparent 70%);
            border-radius: 50%;
            animation: fadeOut 1s forwards;
        }
        
        @keyframes fadeOut {
            to { opacity: 0; }
        }
        
        .buttons {
            display: flex;
            justify-content: space-between;
            margin-bottom: 15px;
            gap: 8px;
            width: 100%;
        }
        
        button {
            padding: 10px 15px;
            border: none;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s;
            font-family: 'Orbitron', sans-serif;
            font-size: clamp(14px, 4vw, 16px);
            position: relative;
            overflow: hidden;
            z-index: 1;
            flex: 1;
            min-height: 50px;
        }
        
        button::before {
            content: '';
            position: absolute;
            top: -2px;
            left: -2px;
            right: -2px;
            bottom: -2px;
            background: linear-gradient(45deg, var(--neon-blue), var(--neon-pink), var(--neon-blue));
            z-index: -2;
            border-radius: 8px;
            opacity: 0.7;
        }
        
        button::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(10, 10, 26, 0.9);
            z-index: -1;
            border-radius: 6px;
        }
        
        button:active {
            transform: translateY(2px) scale(0.98);
        }
        
        .signal-btn {
            color: var(--neon-blue);
        }
        
        .win-btn {
            color: var(--neon-pink);
        }
        
        .winners-list {
            background: rgba(10, 10, 26, 0.7);
            border-radius: 8px;
            padding: 12px;
            margin-top: 15px;
            border: 1px solid var(--neon-green);
            box-shadow: 0 0 12px rgba(0, 255, 157, 0.3);
            width: 100%;
        }
        
        .winners-list h3 {
            margin-top: 0;
            margin-bottom: 8px;
            color: var(--neon-green);
            text-shadow: 0 0 4px var(--neon-green);
            border-bottom: 1px solid rgba(0, 255, 157, 0.3);
            padding-bottom: 8px;
            font-size: clamp(16px, 5vw, 20px);
        }
        
        .winner-item {
            display: flex;
            justify-content: space-between;
            padding: 8px 0;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            animation: slideIn 0.5s ease-out;
            font-size: clamp(14px, 4vw, 16px);
        }
        
        @keyframes slideIn {
            from { opacity: 0; transform: translateX(-15px); }
            to { opacity: 1; transform: translateX(0); }
        }
        
        .winner-amount {
            color: var(--neon-green);
            font-weight: bold;
            text-shadow: 0 0 4px var(--neon-green);
            animation: glow 2s infinite alternate;
        }
        
        @keyframes glow {
            from { text-shadow: 0 0 4px var(--neon-green); }
            to { text-shadow: 0 0 10px var(--neon-green); }
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap" rel="stylesheet">
    <meta name="theme-color" content="#0a0a1a">
    <link rel="manifest" href="manifest.json">
</head>
<body>
    <div class="stars-bg" id="stars-bg"></div>
    
    <div class="container">
        <div class="header">
            <h1>MINES</h1>
            <div class="online">ONLINE: <span id="online-counter">21</span></div>
        </div>
        
        <div class="controls">
            <div class="cross-selector">
                <div class="cross-option active" data-count="1">1</div>
                <div class="cross-option" data-count="3">3</div>
                <div class="cross-option" data-count="5">5</div>
                <div class="cross-option" data-count="7">7</div>
            </div>
        </div>
        
        <div class="game-board" id="game-board"></div>
        
        <div class="buttons">
            <button class="signal-btn" id="signal-btn">GO TO SIGNAL</button>
            <button class="win-btn" id="win-btn">GO 1Win🚀</button>
        </div>
        
        <div class="winners-list">
            <h3>СЕГОДНЯШНИЕ ВЫИГРЫШИ</h3>
            <div id="winners-container">
                <div class="winner-item">
                    <span>**** 3467</span>
                    <span class="winner-amount">+9567₽</span>
                </div>
                <div class="winner-item">
                    <span>**** 4255</span>
                    <span class="winner-amount">+4567₽</span>
                </div>
                <div class="winner-item">
                    <span>**** 6035</span>
                    <span class="winner-amount">+1789₽</span>
                </div>
                <div class="winner-item">
                    <span>**** 4471</span>
                    <span class="winner-amount">+56$</span>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Инициализация игрового поля
        const initGameBoard = () => {
            const gameBoard = document.getElementById('game-board');
            const boardSize = 5;
            
            gameBoard.innerHTML = '';
            
            for (let i = 0; i < boardSize * boardSize; i++) {
                const cell = document.createElement('div');
                cell.className = 'cell star';
                cell.innerHTML = '★';
                cell.dataset.index = i;
                gameBoard.appendChild(cell);
            }
        };
        
        // Создание фоновых звёзд
        const createStars = () => {
            const starsBg = document.getElementById('stars-bg');
            starsBg.innerHTML = '';
            
            for (let i = 0; i < 80; i++) {
                const star = document.createElement('div');
                star.className = 'star-bg';
                star.style.width = `${Math.random() * 2 + 1}px`;
                star.style.height = star.style.width;
                star.style.left = `${Math.random() * 100}%`;
                star.style.top = `${Math.random() * 100}%`;
                star.style.animationDelay = `${Math.random() * 3}s`;
                starsBg.appendChild(star);
            }
        };
        
        // Адаптация размеров
        const adaptLayout = () => {
            const vh = window.innerHeight * 0.01;
            document.documentElement.style.setProperty('--vh', `${vh}px`);
            
            if (window.innerWidth < 500) {
                document.querySelector('.game-board').style.maxHeight = '80vw';
                document.querySelector('.game-board').style.maxWidth = '80vw';
            } else {
                document.querySelector('.game-board').style.maxHeight = '400px';
                document.querySelector('.game-board').style.maxWidth = '400px';
            }
        };
        
        // Основная функция
        const initApp = () => {
            initGameBoard();
            createStars();
            adaptLayout();
            
            let signalPositions = [];
            let currentCrossCount = 1;
            
            // Выбор количества крестиков
            document.querySelectorAll('.cross-option').forEach(option => {
                option.addEventListener('click', function() {
                    document.querySelectorAll('.cross-option').forEach(opt => opt.classList.remove('active'));
                    this.classList.add('active');
                    currentCrossCount = parseInt(this.dataset.count);
                });
            });
            
            // Кнопка "GO TO SIGNAL"
            document.getElementById('signal-btn').addEventListener('click', function() {
                const cells = document.querySelectorAll('.cell');
                
                cells.forEach(cell => {
                    cell.className = 'cell star';
                    cell.innerHTML = '★';
                });
                
                signalPositions = [];
                const totalCells = 25;
                
                while (signalPositions.length < currentCrossCount) {
                    const pos = Math.floor(Math.random() * totalCells);
                    if (!signalPositions.includes(pos)) {
                        signalPositions.push(pos);
                        cells[pos].className = 'cell cross';
                        cells[pos].innerHTML = '✖';
                    }
                }
            });
            
            // Кнопка "GO 1Win🚀"
            document.getElementById('win-btn').addEventListener('click', function() {
                window.location.href = 'https://1wilib.life/?p=qaq5';
            });
            
            // Фейковый онлайн
            const updateOnlineCounter = () => {
                const onlineCounter = document.getElementById('online-counter');
                let currentOnline = parseInt(onlineCounter.textContent);
                const change = Math.random() > 0.5 ? 1 : -1;
                currentOnline += change * Math.floor(Math.random() * 2 + 1);
                
                onlineCounter.textContent = Math.min(33, Math.max(10, currentOnline));
                setTimeout(updateOnlineCounter, 15000);
            };
            
            // Анимация звёзд
            const animateStars = () => {
                document.querySelectorAll('.star').forEach(star => {
                    const x = (Math.random() * 8) - 4;
                    const y = (Math.random() * 8) - 4;
                    star.style.transform = `translate(${x}px, ${y}px)`;
                });
                requestAnimationFrame(animateStars);
            };
            
            updateOnlineCounter();
            animateStars();
            
            // Обработка изменения ориентации
            window.addEventListener('resize', adaptLayout);
            window.addEventListener('orientationchange', adaptLayout);
        };
        
        // Запуск приложения
        document.addEventListener('DOMContentLoaded', initApp);
    </script>
</body>
</html>
