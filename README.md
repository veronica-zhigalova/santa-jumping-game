<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Santa Jump – Secret Code Game</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
      background: #e8f3ff;
      text-align: center;
      user-select: none;
    }
    h1 {
      margin-top: 20px;
    }
    #game-container {
      position: relative;
      width: 360px;
      height: 520px;
      border: 3px solid #c00;
      border-radius: 10px;
      margin: 20px auto;
      background: linear-gradient(#dff4ff, #ffffff);
      overflow: hidden;
    }
    canvas {
      background: transparent;
      display: block;
      margin: 0 auto;
    }
    #win {
      display: none;
      font-size: 1.4em;
      color: green;
      margin-bottom: 20px;
    }
  </style>
</head>
<body>
  <h1>🎄 Santa Jump 🎄</h1>
  <p>Jump on platforms! If you fall, you lose. Reach 1500 points to unlock the code.</p>
  <div id="game-container">
    <canvas id="game" width="360" height="520"></canvas>
  </div>
  <div id="win">✨ Code: <strong>838</strong> ✨</div>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");
    const winMessage = document.getElementById("win");

    let santa = {
      x: 180,
      y: 440,
      width: 60,
      height: 60,
      vy: 0,
    };

    let platforms = [];
    let score = 0;
    let gameOver = false;
    let hasLandedOnce = false;
    const gravity = 0.2;
    const jumpStrength = -8;

    function createPlatforms() {
      for (let i = 0; i < 10; i++) {
        platforms.push({
          x: Math.random() * 300,
          y: 150 + i * 60,
          width: 70,
          height: 10,
        });
      }
    }

    function resetGame() {
      santa.x = 180;
      santa.y = 440;
      santa.vy = 0;
      platforms = [];
      score = 0;
      gameOver = false;
      hasLandedOnce = false;
      winMessage.style.display = "none";
      createPlatforms();
      update();
    }

    function jump() {
      santa.vy = jumpStrength;
    }

    document.addEventListener("keydown", (e) => {
      if (e.key === "ArrowLeft") santa.x -= 25;
      if (e.key === "ArrowRight") santa.x += 25;
      if (gameOver) resetGame();
    });

    // touchscreen controls: tap left/right half
    canvas.addEventListener('touchstart', (e) => {
      const rect = canvas.getBoundingClientRect();
      const x = e.touches[0].clientX - rect.left;
      if (x < rect.width / 2) santa.x -= 25; else santa.x += 25;
      e.preventDefault();
    });

    canvas.addEventListener("click", () => {
      if (gameOver) resetGame();
    });

    createPlatforms();

    function update() {
      if (gameOver) return;

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      santa.vy += gravity;
      santa.y += santa.vy;

      if (santa.y > canvas.height) {
        if (!hasLandedOnce) {
          // Prevent death before first successful landing
          santa.y = 400;
          santa.vy = 0;
        } else {
          ctx.fillStyle = "red";
          ctx.font = "26px Arial";
          ctx.fillText("Game Over! Tap to restart", 20, 260);
          gameOver = true;
          return;
        }
      }

      // draw Santa (emoji)
      ctx.font = "28px serif";
      ctx.fillText("🎅", santa.x, santa.y + santa.height);

      // draw platforms and check collisions
      platforms.forEach((p) => {
        ctx.fillStyle = "green";
        ctx.fillRect(p.x, p.y, p.width, p.height);

        if (
          santa.vy > 0 &&
          santa.x + santa.width > p.x &&
          santa.x < p.x + p.width &&
          santa.y + santa.height > p.y &&
          santa.y + santa.height < p.y + p.height + santa.vy
        ) {
          jump();
          score += 50;
          hasLandedOnce = true;
        }
      });

      if (score >= 1500) {
        winMessage.style.display = "block";
      }

      if (santa.y < 200) {
        santa.y = 200;
        platforms.forEach((p) => {
          p.y += 2;
          if (p.y > canvas.height) {
            p.y = -10;
            p.x = Math.random() * 300;
          }
        });
      }

      // keep Santa inside horizontal bounds
      if (santa.x < 0) santa.x = 0;
      if (santa.x + santa.width > canvas.width) santa.x = canvas.width - santa.width;

      requestAnimationFrame(update);
    }

    update();
  </script>
</body>
</html>
