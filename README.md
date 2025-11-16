<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Memory Game</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f4f4;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    h1 { margin-top: 20px; }

    #lives, #round, #countdown {
      font-size: 22px;
      margin: 10px;
      font-weight: bold;
    }

    #game-board {
      display: grid;
      grid-template-columns: repeat(4, 120px);
      grid-gap: 10px;
      margin-top: 20px;
    }

    .card {
      width: 120px;
      height: 120px;
      background: #ffffff;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 40px;
      cursor: pointer;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      transition: transform 0.3s ease, background 0.3s ease;
    }

    .card:hover { transform: scale(1.1); }

    .hidden {
      background: #ccc;
      font-size: 0;
      transform: rotateY(180deg);
      transition: transform 0.4s ease;
    }

    #result {
      margin-top: 20px;
      font-size: 26px;
      font-weight: bold;
    }
  </style>
</head>
<body>

  <h1>Memory Game</h1>
  <div id="round">Round: 1 / 3</div>
  <div id="lives">Lives: 5</div>
  <div id="countdown"></div>
  <div id="game-board"></div>
  <div id="result"></div>

  <script>
    const rounds = [
      ["🍎", "🍌", "🍇", "🍓", "🍍", "🥝", "🍉", "🍒"], // Fruits
      ["🐻", "🐶", "🐱", "🐰", "🐼", "🦊", "🐨", "🐮"], // Cute animals
      ["🍔", "🍟", "🍕", "🌭", "🥐", "🍰", "🍣", "🌮"]  // Foods
    ];

    let currentRound = 0;
    let lives = 5;
    let firstCard = null;
    let lock = false;
    let matchedCount = 0;

    const board = document.getElementById("game-board");
    const result = document.getElementById("result");
    const livesText = document.getElementById("lives");
    const roundText = document.getElementById("round");
    const countdown = document.getElementById("countdown");

    function shuffle(arr) { return arr.sort(() => Math.random() - 0.5); }

    function restartGame() {
      currentRound = 0;
      lives = 5;
      livesText.textContent = `Lives: ${lives}`;
      roundText.textContent = `Round: 1 / 3`;
      result.textContent = "Restarting...";
      setTimeout(startRoundCountdown, 1500);
    }

    function startRoundCountdown() {
      countdown.textContent = "Get Ready!";
      let timeLeft = 3;
      const timer = setInterval(() => {
        countdown.textContent = `Starting in ${timeLeft}...`;
        timeLeft--;
        if (timeLeft < 0) {
          clearInterval(timer);
          countdown.textContent = "";
          startRound();
        }
      }, 1000);
    }

    function startRound() {
      result.textContent = "Memorize the tiles!";
      board.innerHTML = "";
      firstCard = null;
      lock = true;
      matchedCount = 0;
      lives = 5;
      livesText.textContent = `Lives: ${lives}`;

      let tilesData = rounds[currentRound];
      let cards = shuffle([...tilesData, ...tilesData]);

      cards.forEach((tile) => {
        const card = document.createElement("div");
        card.classList.add("card");
        card.dataset.fruit = tile;
        card.innerHTML = tile;
        board.appendChild(card);
      });

      setTimeout(() => {
        document.querySelectorAll('.card').forEach(card => card.classList.add("hidden"));
        lock = false;
        enableClick();
        result.textContent = "Start Matching!";
      }, 5000);
    }

    function enableClick() {
      document.querySelectorAll('.card').forEach(card => {
        card.addEventListener("click", () => {
          if (lock || !card.classList.contains("hidden")) return;

          card.classList.remove("hidden");

          if (!firstCard) {
            firstCard = card;
          } else {
            if (firstCard.dataset.fruit === card.dataset.fruit) {
              matchedCount++;
              firstCard = null;

              if (matchedCount === rounds[currentRound].length) {
                if (currentRound === 2) {
                  result.textContent = "✨ YOU WON THE GAME! ✨";
                  lock = true;
                } else {
                  currentRound++;
                  roundText.textContent = `Round: ${currentRound + 1} / 3`;
                  result.textContent = "Next Round!";
                  setTimeout(startRoundCountdown, 1500);
                }
              }

            } else {
              lock = true;
              setTimeout(() => {
                firstCard.classList.add("hidden");
                card.classList.add("hidden");
                firstCard = null;
                lock = false;

                lives--;
                livesText.textContent = `Lives: ${lives}`;
                if (lives === 0) {
                  result.textContent = "💀 GAME OVER 💀";
                  lock = true;
                  setTimeout(restartGame, 2000);
                }
              }, 800);
            }
          }
        });
      });
    }

    startRoundCountdown();
  </script>
</body>
</html>
