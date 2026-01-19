# Tik-tik-tapp
Time pass gems 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Neon 0x Pro</title>
    
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

    <style>
        :root {
            --neon-blue: #00d4ff;
            --neon-pink: #ff007b;
            --bg-dark: #050510;
        }

        body {
            background-color: var(--bg-dark);
            color: white;
            font-family: 'Segoe UI', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            overflow: hidden;
        }

        /* --- LOGIN SECTION --- */
        #login-screen {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(15px);
            padding: 40px;
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            text-align: center;
            box-shadow: 0 0 40px rgba(0, 212, 255, 0.2);
            width: 320px;
        }

        h2 { color: var(--neon-blue); text-shadow: 0 0 10px var(--neon-blue); margin-bottom: 25px; }

        .input-group { position: relative; margin-bottom: 20px; }
        .input-group i { position: absolute; left: 12px; top: 12px; color: #666; }

        input {
            width: 100%;
            padding: 12px 12px 12px 40px;
            background: #101025;
            border: 1px solid #333;
            border-radius: 8px;
            color: white;
            box-sizing: border-box;
            outline: none;
            transition: 0.3s;
        }

        input:focus { border-color: var(--neon-pink); box-shadow: 0 0 10px var(--neon-pink); }

        .btn {
            width: 100%;
            padding: 14px;
            background: var(--neon-blue);
            border: none;
            border-radius: 8px;
            color: #050510;
            font-weight: bold;
            cursor: pointer;
            text-transform: uppercase;
            letter-spacing: 1px;
            transition: 0.3s;
        }

        .btn:hover { box-shadow: 0 0 25px var(--neon-blue); transform: translateY(-2px); }

        /* --- GAME SECTION --- */
        #game-screen { display: none; text-align: center; }

        #status { font-size: 1.4rem; margin: 15px 0; color: #aaa; }

        .grid {
            display: grid;
            grid-template-columns: repeat(3, 100px);
            grid-gap: 15px;
            margin: 20px auto;
        }

        .cell {
            width: 100px;
            height: 100px;
            background: #16162d;
            border: 2px solid #2a2a40;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            border-radius: 15px;
            transition: 0.2s;
        }

        .cell:hover { border-color: var(--neon-blue); background: #1f1f3a; }

        /* Icon Styles */
        .fa-xmark { color: var(--neon-pink); font-size: 3rem; filter: drop-shadow(0 0 10px var(--neon-pink)); }
        .fa-circle { color: var(--neon-blue); font-size: 2.5rem; filter: drop-shadow(0 0 10px var(--neon-blue)); }

        .winner {
            background: rgba(255, 255, 255, 0.15) !important;
            border-color: white !important;
            animation: pulse 1s infinite;
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body>

    <div id="login-screen">
        <h2><i class="fa-solid fa-ghost"></i> NEON 0x</h2>
        <div class="input-group">
            <i class="fa-solid fa-user"></i>
            <input type="text" id="username" placeholder="Enter Player Name">
        </div>
        <button class="btn" onclick="startGame()">Start Battle</button>
    </div>

    <div id="game-screen">
        <h1 id="welcome-msg">Welcome</h1>
        <div id="status">Your Turn (X)</div>
        <div class="grid" id="grid">
            <div class="cell" data-index="0"></div>
            <div class="cell" data-index="1"></div>
            <div class="cell" data-index="2"></div>
            <div class="cell" data-index="3"></div>
            <div class="cell" data-index="4"></div>
            <div class="cell" data-index="5"></div>
            <div class="cell" data-index="6"></div>
            <div class="cell" data-index="7"></div>
            <div class="cell" data-index="8"></div>
        </div>
        <button onclick="resetGame()" style="background:none; border:1px solid #444; color:#888; padding:8px 20px; border-radius:5px; cursor:pointer;">Reset Game</button>
    </div>

    <script>
        let pName = "Player X";
        let currentPlayer = "X";
        let board = ["", "", "", "", "", "", "", "", ""];
        let gameActive = true;
        const cells = document.querySelectorAll('.cell');
        const statusText = document.getElementById('status');

        function startGame() {
            const input = document.getElementById('username').value;
            if(input) pName = input;
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('game-screen').style.display = 'block';
            document.getElementById('welcome-msg').innerText = `Elite: ${pName}`;
            statusText.innerText = `${pName}'s Turn (X)`;
        }

        cells.forEach(cell => {
            cell.addEventListener('click', () => {
                const i = cell.getAttribute('data-index');
                if (board[i] !== "" || !gameActive) return;

                board[i] = currentPlayer;
                cell.innerHTML = currentPlayer === "X" 
                    ? '<i class="fa-solid fa-xmark"></i>' 
                    : '<i class="fa-regular fa-circle"></i>';
                
                checkWin();
            });
        });

        function checkWin() {
            const wins = [[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
            let won = false;
            
            for (let cond of wins) {
                let [a, b, c] = cond;
                if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                    won = true;
                    cond.forEach(idx => cells[idx].classList.add('winner'));
                    break;
                }
            }

            if (won) {
                statusText.innerText = `🎉 ${currentPlayer === "X" ? pName : "O"} Wins!`;
                gameActive = false;
                confetti({ particleCount: 150, spread: 70, origin: { y: 0.6 } });
                return;
            }

            if (!board.includes("")) {
                statusText.innerText = "Draw!";
                gameActive = false;
                return;
            }

            currentPlayer = currentPlayer === "X" ? "O" : "X";
            statusText.innerText = currentPlayer === "X" ? `${pName}'s Turn (X)` : "O's Turn";
        }

        function resetGame() {
            board = ["", "", "", "", "", "", "", "", ""];
            gameActive = true;
            currentPlayer = "X";
            statusText.innerText = `${pName}'s Turn (X)`;
            cells.forEach(c => { c.innerHTML = ""; c.classList.remove('winner'); });
        }
    </script>
</body>
</html>
