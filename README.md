<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Water Drop Defender – charity: water mini-game</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      background: #b3ecff;
      font-family: Arial, sans-serif;
      display: flex; justify-content: center; align-items: center;
      height: 100vh;
    }
    #game {
      width: 360px; height: 640px;
      background: linear-gradient(to top, #d0f0ff, #80d4ff);
      border: 10px solid black; border-radius: 30px;
      position: relative; overflow: hidden;
    }
    .notify50 {
      position: absolute;
      left: 50%;
      top: 40%;
      transform: translate(-50%, -50%);
      background: #fff8c6;
      color: #0077cc;
      font-size: 22px;
      font-weight: bold;
      padding: 16px 32px;
      border-radius: 20px;
      box-shadow: 0 2px 16px #0077cc33;
      z-index: 1001;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.5s;
    }
    h1 { text-align: center; color: #004466; margin-top: 10px; }
    #subtitle {
      text-align: center; font-size: 14px;
      color: #004466; margin-bottom: 15px;
    }
    #scoreboard {
      position: absolute; top: 80px; left: 10px;
      background: #ffffffdd; padding: 6px 12px;
      border-radius: 10px; font-size: 16px;
      color: #003344; display: flex; flex-direction: column; align-items: center;
      gap: 6px; box-shadow: 0 0 5px rgba(0,0,0,0.3);
    }
    #scoreboard img { width: 20px; height: 20px; }
    #lives {
      position: absolute; top: 80px; right: 10px;
      font-size: 20px; color: red;
    }
    #streak {
      margin-top: 4px;
      background: #fff8c6;
      color: #b8860b;
      padding: 2px 10px;
      border-radius: 12px;
      font-size: 15px;
      font-weight: bold;
      box-shadow: 0 2px 8px #ffe06644;
      z-index: 3;
      display: none;
      position: static;
      transform: none;
    }
    .drop {
      position: absolute; top: -50px;
      width: 30px; height: 40px;
      background-size: contain; background-repeat: no-repeat;
      background-position: center;
    }
    #player {
      position: absolute; bottom: 100px;
      left: 50%; transform: translateX(-50%);
      width: 60px; height: 80px;
      background: 
        linear-gradient(to bottom, #e6f3ff 0%, #b3d9ff 30%, #80ccff 70%, #4da6ff 100%),
        url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="%23ffffff" d="M12,2A10,10 0 0,1 22,12A10,10 0 0,1 12,22A10,10 0 0,1 2,12A10,10 0 0,1 12,2M12,4A8,8 0 0,0 4,12A8,8 0 0,0 12,20A8,8 0 0,0 20,12A8,8 0 0,0 12,4Z"/></svg>') no-repeat center/60%;
      border: 3px solid #2c5282;
      border-radius: 12px 12px 8px 8px;
      z-index: 3;
      position: absolute;
    }
    .splash {
      position: absolute;
      width: 40px;
      height: 40px;
      pointer-events: none;
      z-index: 1000;
      background: radial-gradient(circle at 60% 40%, #b3ecff 60%, #4da6ff 100%);
      border-radius: 50%;
      opacity: 0.8;
      animation: splashpop 0.5s ease-out forwards;
    }
    @keyframes splashpop {
      0% { transform: scale(0.5); opacity: 0.8; }
      60% { transform: scale(1.2); opacity: 1; }
      100% { transform: scale(1.5); opacity: 0; }
    }
    #player::before {
      content: "💧";
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 24px;
      z-index: 4;
    }
    #sliderBar {
      position: absolute; bottom: 70px; left: 10px; right: 10px;
      height: 20px; background: #888; border-radius: 10px;
      z-index: 1;
    }
    #sliderHandle {
      width: 30px; height: 20px; background: yellow;
      border-radius: 50%; position: absolute; top: 0;
      left: 50%; transform: translateX(-50%);
      cursor: pointer;
      z-index: 2;
    }
    #branding {
      position: absolute; bottom: 10px; width: 100%;
      text-align: center; font-family: serif;
      background: #fff1a8; padding: 10px 0;
    }
    #branding img { width: 20px; vertical-align: middle; }
    #endScreen {
      position: absolute; top: 0; left: 0;
      width: 100%; height: 100%;
      background: rgba(255,255,255,0.95);
      display: none; flex-direction: column;
      justify-content: center; align-items: center;
      padding: 20px; text-align: center; z-index: 999;
    }
    #endScreen h2 { color: #003344; margin-bottom: 15px; }
    #endScreen p { margin: 10px 0; }
    #restartBtn {
      padding: 10px 20px; background: #0077cc;
      color: white; border: none; border-radius: 10px;
      cursor: pointer; font-size: 16px;
    }
  </style>
</head>
<body>
  <div id="game">
    <div id="notify50" class="notify50"></div>
    <h1>WATER DROP</h1>
    <div id="subtitle">Catch clean water, avoid pollution!</div>
    <div id="scoreboard">
      <img src="https://th.bing.com/th/id/R.5da03ddeed93baf0c9402b6f3a017c1e?rik=gfa31ed3XZRSPw&pid=ImgRaw&r=0" alt="Water Drop" />
      <span id="score">0</span>
      <div id="streak"></div>
    </div>
    <div id="lives">❤️❤️❤️</div>
    <div id="player"></div>
    <div id="sliderBar"><div id="sliderHandle"></div></div>
    <div id="branding"><img src="https://cdn-icons-png.flaticon.com/512/1975/1975682.png" alt="charity water logo" /> charity: water</div>
    <div id="endScreen">
      <h2>Game Over</h2>
      <p id="finalScore">Score: 0</p>
      <p id="timePlayed">Time Played: 0s</p>
      <button id="restartBtn">Restart Game</button>
    </div>
  </div>

  <script>
    function showSplash(x, y) {
      const splash = document.createElement('div');
      splash.className = 'splash';
      splash.style.left = (x - game.getBoundingClientRect().left - 20) + 'px';
      splash.style.top = (y - game.getBoundingClientRect().top - 10) + 'px';
      game.appendChild(splash);
      setTimeout(() => {
        if (splash.parentNode) splash.parentNode.removeChild(splash);
      }, 500);
    }
    const notify50 = document.getElementById('notify50');
    function showNotify50(score) {
      notify50.textContent = `Milestone! ${score} points! 🚰`;
      notify50.style.opacity = '1';
      setTimeout(() => {
        notify50.style.opacity = '0';
      }, 1800);
    }
    let lastScore = localStorage.getItem('lastScore') || 0;
    const streakEl = document.getElementById('streak');
    function showStreak() {
      if (lastScore > 0) {
        streakEl.textContent = `Last Score: ${lastScore}`;
        streakEl.style.display = 'block';
      } else {
        streakEl.style.display = 'none';
      }
    }
    showStreak();
    const maxDrops = 5;
    let activeDrops = 0;

    const player = document.getElementById('player');
    const handle = document.getElementById('sliderHandle');
    const bar = document.getElementById('sliderBar');
    const scoreEl = document.getElementById('score');
    const livesEl = document.getElementById('lives');
    const game = document.getElementById('game');
    const endScreen = document.getElementById('endScreen');
    const finalScore = document.getElementById('finalScore');
    const timePlayed = document.getElementById('timePlayed');
    const restartBtn = document.getElementById('restartBtn');

    let score = 0;
    let lives = 3;
    const maxLives = 5;
    let isDragging = false;
    let gameStartTime = Date.now();

    // Keep track of drop positions so they don't spawn on top of each other
    const dropPositions = [];

    // Mouse events
    handle.addEventListener('mousedown', () => isDragging = true);
    document.addEventListener('mouseup', () => isDragging = false);
    document.addEventListener('mousemove', e => {
      if (!isDragging) return;
      const rect = bar.getBoundingClientRect();
      let x = e.clientX - rect.left;
      x = Math.max(0, Math.min(x, rect.width));
      handle.style.left = x + 'px';
      
      // Only move player horizontally, keep bottom position fixed
      const leftPercent = (x / rect.width * 100);
      player.style.left = leftPercent + '%';
      player.style.transform = 'translateX(-50%)'; // Keep centered on the position
    });

    function canSpawnAt(xPercent) {
      // Prevent spawning drops within 12% left or right of existing drops to avoid overlap
      // Drop width ~30px / 360px = ~8.3%, add margin for safe distance ~12%
      for (const pos of dropPositions) {
        if (Math.abs(pos - xPercent) < 12) {
          return false;
        }
      }
      return true;
    }

    function spawnDrop() {
      if (activeDrops >= maxDrops) return;

      let spawnX;
      let attempts = 0;
      do {
        spawnX = Math.random() * 90; // 0 to 90% to keep drop inside container
        attempts++;
        if (attempts > 20) break; // fail safe to avoid infinite loop
      } while (!canSpawnAt(spawnX));

      dropPositions.push(spawnX);
      activeDrops++;

      const drop = document.createElement('div');
      const isPolluted = Math.random() < 0.25;
      drop.className = 'drop';
      drop.style.left = `${spawnX}%`;
      drop.collected = false; // Add flag to prevent multiple collisions
      
      if (isPolluted) {
        drop.style.background = 'radial-gradient(circle, #8B4513 0%, #654321 100%)';
        drop.style.borderRadius = '50% 50% 50% 50% / 60% 60% 40% 40%';
      } else {
        drop.style.backgroundImage = "url('https://th.bing.com/th/id/R.5da03ddeed93baf0c9402b6f3a017c1e?rik=gfa31ed3XZRSPw&pid=ImgRaw&r=0')";
        drop.style.backgroundSize = "contain";
        drop.style.backgroundRepeat = "no-repeat";
        drop.style.backgroundPosition = "center";
      }
      game.appendChild(drop);

      let y = 0;
      const fallSpeed = 4;
      const interval = setInterval(() => {
        y += fallSpeed;
        drop.style.top = y + 'px';

        const dr = drop.getBoundingClientRect();
        const pr = player.getBoundingClientRect();

        // Check collision with player - only if drop is coming from above and not already collected
        if (!drop.collected && dr.bottom > pr.top && dr.top < pr.bottom && dr.left < pr.right && dr.right > pr.left && y > 0) {
          drop.collected = true; // Mark as collected to prevent multiple hits
          if (isPolluted) {
            lives--;
            updateLives();
            showLifeLost(dr.left, dr.top);
            if (lives === 0) {
              endGame();
            }
          } else {
            score++;
            scoreEl.textContent = score;
            showSplash(dr.left + dr.width / 2, dr.top + dr.height / 2);
            // Extra life every 10 water collected, max lives 5
            if (score % 10 === 0 && lives < maxLives) {
              lives++;
              updateLives();
              showExtraLife(dr.left, dr.top);
            }
          }
          clearDrop();
        } else if (y > game.clientHeight) {
          clearDrop();
        }
      }, 30);

      function clearDrop() {
        clearInterval(interval);
        drop.remove();
        activeDrops--;
        // Remove this drop's spawn position so new drops can spawn there
        const index = dropPositions.indexOf(spawnX);
        if (index > -1) dropPositions.splice(index, 1);
      }
    }

    function updateLives() {
      const maxLives = 5;
      let heartsDisplay = '';
      
      // Add filled hearts for current lives
      for (let i = 0; i < lives; i++) {
        heartsDisplay += '❤️';
      }
      
      // Add empty hearts for remaining lives
      for (let i = lives; i < maxLives; i++) {
        heartsDisplay += '🤍';
      }
      
      heartsDisplay += ` (${lives}/${maxLives})`;
      livesEl.innerHTML = heartsDisplay;
    }

    function endGame() {
      const elapsed = Math.floor((Date.now() - gameStartTime) / 1000);
      finalScore.textContent = `Score: ${score}`;
      timePlayed.textContent = `Time Played: ${elapsed}s`;
      endScreen.style.display = 'flex';
      // Disable dragging and spawning drops after game over
      isDragging = false;
      clearInterval(spawnInterval);
      localStorage.setItem('lastScore', score);
      lastScore = score;
      showStreak();
    }

    restartBtn.onclick = () => location.reload();

    updateLives();
    const spawnInterval = setInterval(spawnDrop, 800);

    function showLifeLost(x, y) {
      // Create a simple floating text popup at the drop location for life lost
      const popup = document.createElement('div');
      popup.style.position = 'absolute';
      popup.style.left = (x - game.getBoundingClientRect().left) + 'px';
      popup.style.top = (y - game.getBoundingClientRect().top) + 'px';
      popup.style.color = '#ff4444';
      popup.style.fontSize = '14px';
      popup.style.fontWeight = 'bold';
      popup.style.zIndex = '1000';
      popup.style.pointerEvents = 'none';
      popup.style.transition = 'all 1.5s ease-out';
      popup.style.opacity = '1';
      popup.textContent = '-1 Life 😢';
      
      game.appendChild(popup);
      
      // Animate upward and fade out
      setTimeout(() => {
        popup.style.transform = 'translateY(-30px)';
        popup.style.opacity = '0';
      }, 100);
      
      // Remove popup after animation
      setTimeout(() => {
        if (popup.parentNode) {
          popup.parentNode.removeChild(popup);
        }
      }, 1600);
    }

    function showExtraLife(x, y) {
      // Create a simple floating text popup at the drop location
      const popup = document.createElement('div');
      popup.style.position = 'absolute';
      popup.style.left = (x - game.getBoundingClientRect().left) + 'px';
      popup.style.top = (y - game.getBoundingClientRect().top) + 'px';
      popup.style.color = '#ff6b6b';
      popup.style.fontSize = '14px';
      popup.style.fontWeight = 'bold';
      popup.style.zIndex = '1000';
      popup.style.pointerEvents = 'none';
      popup.style.transition = 'all 1.5s ease-out';
      popup.style.opacity = '1';
      popup.textContent = '+Extra Life';
      
      game.appendChild(popup);
      
      // Animate upward and fade out
      setTimeout(() => {
        popup.style.transform = 'translateY(-30px)';
        popup.style.opacity = '0';
      }, 100);
      
      // Remove popup after animation
      setTimeout(() => {
        if (popup.parentNode) {
          popup.parentNode.removeChild(popup);
        }
      }, 1600);
    }
  </script>
</body>
</html>
