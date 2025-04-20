# Skibidi-showdown
A game based off street fight and skibidi toliet 
<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Skibidi Showdown</title>
  <style>
    canvas {
      background: #222;
      display: block;
      margin: 20px auto;
      border: 4px solid #fff;
    }
    .controls {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 10px;
      margin-top: 10px;
    }
    .btn {
      width: 60px;
      height: 60px;
      background: #555;
      color: white;
      font-size: 20px;
      border-radius: 10px;
      border: none;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="400"></canvas>
  <div class="controls">
    <button class="btn" id="p1-left">P1 ←</button>
    <button class="btn" id="p1-right">P1 →</button>
    <button class="btn" id="p1-jump">P1 ↑</button>
    <button class="btn" id="p1-attack">P1 F</button>
    <button class="btn" id="p2-left">P2 ←</button>
    <button class="btn" id="p2-right">P2 →</button>
    <button class="btn" id="p2-jump">P2 ↑</button>
    <button class="btn" id="p2-attack">P2 L</button>
  </div>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");const gravity = 0.5;
let keys = {};

class Fighter {
  constructor(x, color, controls) {
    this.x = x;
    this.y = 300;
    this.width = 50;
    this.height = 80;
    this.color = color;
    this.dy = 0;
    this.health = 100;
    this.controls = controls;
    this.isAttacking = false;
  }

  draw() {
    ctx.fillStyle = this.color;
    ctx.fillRect(this.x, this.y, this.width, this.height);
    if (this.isAttacking) {
      ctx.fillStyle = "yellow";
      ctx.fillRect(this.x + (this.color === 'red' ? this.width : -20), this.y + 20, 20, 10);
    }
  }

  update() {
    this.y += this.dy;
    if (this.y + this.height < canvas.height) {
      this.dy += gravity;
    } else {
      this.dy = 0;
      this.y = canvas.height - this.height;
    }

    if (this.health < 0) this.health = 0;

    this.draw();
  }

  attack(enemy) {
    if (this.isAttacking &&
        this.x < enemy.x + enemy.width &&
        this.x + this.width > enemy.x &&
        this.y < enemy.y + enemy.height &&
        this.y + this.height > enemy.y) {
      enemy.health -= 10;
      console.log("Skibidi!");
    }
  }
}

const player1 = new Fighter(100, "red", { left: 'a', right: 'd', jump: 'w', attack: 'f' });
const player2 = new Fighter(600, "blue", { left: 'ArrowLeft', right: 'ArrowRight', jump: 'ArrowUp', attack: 'l' });

function drawHealthBar(fighter, x, y) {
  ctx.fillStyle = "gray";
  ctx.fillRect(x, y, 200, 20);
  ctx.fillStyle = "lime";
  ctx.fillRect(x, y, 2 * fighter.health, 20);
}

function animate() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  player1.update();
  player2.update();

  if (keys[player1.controls.left]) player1.x -= 4;
  if (keys[player1.controls.right]) player1.x += 4;
  if (keys[player1.controls.jump] && player1.dy === 0) player1.dy = -10;
  player1.isAttacking = keys[player1.controls.attack];

  if (keys[player2.controls.left]) player2.x -= 4;
  if (keys[player2.controls.right]) player2.x += 4;
  if (keys[player2.controls.jump] && player2.dy === 0) player2.dy = -10;
  player2.isAttacking = keys[player2.controls.attack];

  player1.attack(player2);
  player2.attack(player1);

  drawHealthBar(player1, 20, 20);
  drawHealthBar(player2, 580, 20);

  requestAnimationFrame(animate);
}

window.addEventListener("keydown", e => keys[e.key] = true);
window.addEventListener("keyup", e => keys[e.key] = false);

const bindTouch = (id, key) => {
  const el = document.getElementById(id);
  el.addEventListener('touchstart', e => {
    e.preventDefault();
    keys[key] = true;
  });
  el.addEventListener('touchend', e => {
    e.preventDefault();
    keys[key] = false;
  });
};

bindTouch('p1-left', 'a');
bindTouch('p1-right', 'd');
bindTouch('p1-jump', 'w');
bindTouch('p1-attack', 'f');
bindTouch('p2-left', 'ArrowLeft');
bindTouch('p2-right', 'ArrowRight');
bindTouch('p2-jump', 'ArrowUp');
bindTouch('p2-attack', 'l');

animate();

  </script>
</body>
</html>
