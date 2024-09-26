<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Savaş Oyunu</title>
    <style>
        canvas {
            background-color: #000;
            display: block;
            margin: 0 auto;
        }
        body {
            text-align: center;
            color: white;
        }
    </style>
</head>
<body>
    <h1>Basit Savaş Oyunu</h1>
    <p>WASD ile hareket et, Space ile ateş et!</p>
    <canvas id="gameCanvas" width="800" height="600"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let player = {
            x: canvas.width / 2,
            y: canvas.height - 50,
            width: 50,
            height: 50,
            speed: 5,
            dx: 0,
            dy: 0,
            bullets: []
        };

        let enemies = [];
        let score = 0;
        let lives = 3;

        // Oyuncuyu çizme fonksiyonu
        function drawPlayer() {
            ctx.fillStyle = 'blue';
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }

        // Kurşunları çizme fonksiyonu
        function drawBullets() {
            player.bullets.forEach((bullet, index) => {
                ctx.fillStyle = 'yellow';
                ctx.fillRect(bullet.x, bullet.y, 5, 10);
                bullet.y -= bullet.speed;

                // Ekrandan çıkmış kurşunları sil
                if (bullet.y < 0) {
                    player.bullets.splice(index, 1);
                }
            });
        }

        // Düşman oluşturma
        function spawnEnemy() {
            const enemy = {
                x: Math.random() * canvas.width,
                y: 0,
                width: 50,
                height: 50,
                speed: 2
            };
            enemies.push(enemy);
        }

        // Düşmanları çizme
        function drawEnemies() {
            enemies.forEach((enemy, index) => {
                ctx.fillStyle = 'red';
                ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
                enemy.y += enemy.speed;

                // Oyuncuya çarparsa
                if (enemy.y + enemy.height > player.y && enemy.x < player.x + player.width && enemy.x + enemy.width > player.x) {
                    lives--;
                    enemies.splice(index, 1);
                }

                // Ekrandan çıkarsa
                if (enemy.y > canvas.height) {
                    enemies.splice(index, 1);
                }
            });
        }

        // Düşman ve kurşun çarpışması kontrolü
        function checkCollisions() {
            player.bullets.forEach((bullet, bulletIndex) => {
                enemies.forEach((enemy, enemyIndex) => {
                    if (bullet.x < enemy.x + enemy.width &&
                        bullet.x + 5 > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + 10 > enemy.y) {
                        enemies.splice(enemyIndex, 1);
                        player.bullets.splice(bulletIndex, 1);
                        score += 10;
                    }
                });
            });
        }

        // Skor ve canları ekrana yazdırma
        function drawScore() {
            ctx.fillStyle = 'white';
            ctx.font = '20px Arial';
            ctx.fillText('Skor: ' + score, 20, 30);
            ctx.fillText('Can: ' + lives, canvas.width - 100, 30);
        }

        // Klavye kontrolleri
        function keyDown(e) {
            if (e.key === 'w' || e.key === 'ArrowUp') player.dy = -player.speed;
            if (e.key === 's' || e.key === 'ArrowDown') player.dy = player.speed;
            if (e.key === 'a' || e.key === 'ArrowLeft') player.dx = -player.speed;
            if (e.key === 'd' || e.key === 'ArrowRight') player.dx = player.speed;
            if (e.key === ' ') fireBullet();
        }

        function keyUp(e) {
            if (e.key === 'w' || e.key === 'ArrowUp' || e.key === 's' || e.key === 'ArrowDown') player.dy = 0;
            if (e.key === 'a' || e.key === 'ArrowLeft' || e.key === 'd' || e.key === 'ArrowRight') player.dx = 0;
        }

        // Kurşun ateşleme
        function fireBullet() {
            const bullet = {
                x: player.x + player.width / 2 - 2.5,
                y: player.y,
                speed: 7
            };
            player.bullets.push(bullet);
        }

        // Oyuncunun hareketi
        function movePlayer() {
            player.x += player.dx;
            player.y += player.dy;

            // Ekran dışına çıkmasını engelle
            if (player.x < 0) player.x = 0;
            if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;
            if (player.y < 0) player.y = 0;
            if (player.y + player.height > canvas.height) player.y = canvas.height - player.height;
        }

        // Ana oyun döngüsü
        function update() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            movePlayer();
            drawPlayer();
            drawBullets();
            drawEnemies();
            checkCollisions();
            drawScore();

            // Oyuncunun canı biterse oyunu bitir
            if (lives > 0) {
                requestAnimationFrame(update);
            } else {
                alert('Oyun bitti! Skorunuz: ' + score);
                document.location.reload();
            }
        }

        // Oyun başlangıcı
        setInterval(spawnEnemy, 1000); // Her saniyede bir düşman oluştur
        update();

        // Klavye olay dinleyicileri
        document.addEventListener('keydown', keyDown);
        document.addEventListener('keyup', keyUp);
    </script>
</body>
</html>
