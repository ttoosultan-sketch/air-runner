<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gas Dodger Game</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            background-color: #f0f0f0;
        }
        #gameCanvas {
            display: block;
            margin: auto;
            background-image: url('factory_background.jpg'); /* يمكن استبداله بأي خلفية صناعية */
            background-size: cover;
        }
        .character {
            width: 50px;
            height: 50px;
            background-color: red; /* هذا لون البديل عند عدم وجود صورة */
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const characterImage = new Image();
        characterImage.src = 'character_image.png'; // هنا يمكنك إضافة صورة الشخصية التي تريديها

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // Game Variables
        let characterX = 100;
        let characterY = canvas.height - 150;
        let characterSpeed = 10;
        let gameScore = 0;

        // Clouds
        let clouds = [];

        // Oxygen balls
        let oxygenBalls = [];

        // Draw character and obstacles
        function drawCharacter() {
            ctx.drawImage(characterImage, characterX, characterY, 50, 50);
        }

        function drawOxygenBalls() {
            ctx.fillStyle = "green";
            oxygenBalls.forEach(ball => {
                ctx.beginPath();
                ctx.arc(ball.x, ball.y, 20, 0, Math.PI * 2);
                ctx.fill();
                ball.y += 5; // Move oxygen up
                if (ball.y > canvas.height) oxygenBalls = oxygenBalls.filter(b => b !== ball); // Remove off-screen oxygen
            });
        }

        function drawClouds() {
            ctx.fillStyle = "gray";
            clouds.forEach(cloud => {
                ctx.beginPath();
                ctx.arc(cloud.x, cloud.y, 30, 0, Math.PI * 2);
                ctx.fill();
                cloud.y += 2; // Move clouds down
                if (cloud.y > canvas.height) clouds = clouds.filter(c => c !== cloud); // Remove off-screen clouds
            });
        }

        // Handle key events
        window.addEventListener('keydown', function(e) {
            if (e.key === 'ArrowUp' && characterY > 0) characterY -= characterSpeed;
            if (e.key === 'ArrowDown' && characterY < canvas.height - 50) characterY += characterSpeed;
            if (e.key === 'ArrowLeft' && characterX > 0) characterX -= characterSpeed;
            if (e.key === 'ArrowRight' && characterX < canvas.width - 50) characterX += characterSpeed;
        });

        // Collision detection
        function checkCollisions() {
            oxygenBalls.forEach(ball => {
                if (Math.abs(ball.x - characterX) < 30 && Math.abs(ball.y - characterY) < 30) {
                    gameScore += 10; // Increase score on collecting oxygen
                    oxygenBalls = oxygenBalls.filter(b => b !== ball);
                }
            });
            clouds.forEach(cloud => {
                if (Math.abs(cloud.x - characterX) < 30 && Math.abs(cloud.y - characterY) < 30) {
                    gameScore -= 10; // Decrease score on hitting clouds
                    clouds = clouds.filter(c => c !== cloud);
                }
            });
        }

        // Update function
        function gameLoop() {
            ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear canvas

            drawCharacter();
            drawOxygenBalls();
            drawClouds();
            checkCollisions();

            ctx.fillStyle = "white";
            ctx.font = "30px Arial";
            ctx.fillText("Score: " + gameScore, 20, 50);

            requestAnimationFrame(gameLoop);
        }

        // Generate Oxygen and Cloud objects randomly
        setInterval(() => {
            oxygenBalls.push({ x: Math.random() * canvas.width, y: -20 });
            clouds.push({ x: Math.random() * canvas.width, y: -30 });
        }, 2000);

        gameLoop(); // Start the game loop
    </script>
</body>
</html>
