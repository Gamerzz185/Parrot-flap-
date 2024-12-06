<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Parrot Game</title>
    <style>
        body { margin: 0; }
        canvas { background-color: #87CEEB; display: block; }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = 800;
        canvas.height = 600;

        // Parrot object
        const parrot = {
            x: 100,
            y: canvas.height / 2,
            width: 50,
            height: 50,
            upSpeed: 5,
            downSpeed: 2
        };

        // Bullet object
        const bullets = [];
        const bulletSpeed = 3;

        // Obstacle object
        const obstacles = [];
        const obstacleSpeed = 2;

        // Game loop and updating
        let lastTime = 0;
        function gameLoop(timestamp) {
            const deltaTime = timestamp - lastTime;
            lastTime = timestamp;

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Move the parrot
            if (keyState['ArrowUp']) {
                parrot.y -= parrot.upSpeed;
            }
            if (keyState['ArrowDown']) {
                parrot.y += parrot.downSpeed;
            }

            // Prevent parrot from going out of bounds
            if (parrot.y < 0) parrot.y = 0;
            if (parrot.y + parrot.height > canvas.height) parrot.y = canvas.height - parrot.height;

            // Draw the parrot (using a simple rectangle as placeholder)
            ctx.fillStyle = 'green';
            ctx.fillRect(parrot.x, parrot.y, parrot.width, parrot.height);

            // Update bullets
            updateBullets();

            // Update obstacles
            updateObstacles();

            // Spawn obstacles
            if (Math.random() < 0.02) {  // approximately every 50ms on average
                spawnObstacle();
            }

            requestAnimationFrame(gameLoop);
        }

        // Bullet logic
        function updateBullets() {
            for (let i = 0; i < bullets.length; i++) {
                bullets[i].x += bulletSpeed;

                if (bullets[i].x > canvas.width) {
                    bullets.splice(i, 1);
                    i--;
                } else {
                    ctx.fillStyle = 'red';
                    ctx.fillRect(bullets[i].x, bullets[i].y, 10, 5);
                }
            }
        }

        // Spawn a new bullet
        function shootBullet() {
            bullets.push({
                x: parrot.x + parrot.width,
                y: parrot.y + parrot.height / 2 - 2.5
            });
        }

        // Obstacle logic
        function updateObstacles() {
            for (let i = 0; i < obstacles.length; i++) {
                obstacles[i].x -= obstacleSpeed;

                if (obstacles[i].x + obstacles[i].width < 0) {
                    obstacles.splice(i, 1);
                    i--;
                } else {
                    ctx.fillStyle = 'brown';
                    ctx.fillRect(obstacles[i].x, obstacles[i].y, obstacles[i].width, obstacles[i].height);
                }
            }
        }

        // Spawn a new obstacle
        function spawnObstacle() {
            const height = Math.random() * (canvas.height - 20) + 20;
            obstacles.push({
                x: canvas.width,
                y: height,
                width: 30,
                height: height
            });
        }

        // Key handling
        const keyState = {};

        window.addEventListener('keydown', (e) => {
            keyState[e.key] = true;
            if (e.key === ' ' && !keyState['shooting']) {
                keyState['shooting'] = true;
                shootBullet();
            }
        });

        window.addEventListener('keyup', (e) => {
            keyState[e.key] = false;
            if (e.key === ' ') {
                keyState['shooting'] = false;
            }
        });

        // Start game loop
        requestAnimationFrame(gameLoop);
    </script>
</body>
</html>
