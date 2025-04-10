<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Snake Game</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.0.0/css/all.min.css">
    <style>
        body {
            touch-action: none;
            overflow: hidden;
            background-color: #1a202c;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        #game-canvas {
            background-color: #2d3748;
            border-radius: 8px;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
        }
        
        .control-btn {
            background-color: rgba(99, 102, 241, 0.8);
            color: white;
            border: none;
            border-radius: 100%;
            width: 50px;
            height: 50px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            transition: transform 0.1s, background-color 0.2s;
        }
        
        .control-btn:active {
            transform: scale(0.95);
            background-color: rgba(79, 82, 231, 1);
        }
        
        .control-pad {
            position: relative;
            width: 170px;
            height: 170px;
        }
        
        .btn-up {
            position: absolute;
            top: 0;
            left: 60px;
        }
        
        .btn-left {
            position: absolute;
            left: 0;
            top: 60px;
        }
        
        .btn-right {
            position: absolute;
            right: 0;
            top: 60px;
        }
        
        .btn-down {
            position: absolute;
            bottom: 0;
            left: 60px;
        }
        
        .game-btn {
            background-color: rgba(99, 102, 241, 0.9);
            color: white;
            font-weight: bold;
            padding: 10px 15px;
            border-radius: 8px;
            border: none;
            transition: all 0.2s;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }
        
        .game-btn:active {
            transform: scale(0.97);
        }
        
        .pulse-animation {
            animation: pulse 2s infinite;
        }
        
        @keyframes pulse {
            0% {
                box-shadow: 0 0 0 0 rgba(99, 102, 241, 0.7);
            }
            70% {
                box-shadow: 0 0 0 15px rgba(99, 102, 241, 0);
            }
            100% {
                box-shadow: 0 0 0 0 rgba(99, 102, 241, 0);
            }
        }
        
        .slide-in {
            animation: slideIn 0.3s forwards;
        }
        
        @keyframes slideIn {
            from {
                transform: translateY(-20px);
                opacity: 0;
            }
            to {
                transform: translateY(0);
                opacity: 1;
            }
        }
        
        .speed-option {
            cursor: pointer;
            padding: 8px 16px;
            border-radius: 20px;
            background-color: #374151;
            color: white;
            transition: all 0.2s;
        }
        
        .speed-active {
            background-color: #4f46e5;
        }
        
        .fade-in {
            animation: fadeIn 0.5s forwards;
        }
        
        @keyframes fadeIn {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }
    </style>
</head>
<body class="h-screen w-screen flex flex-col items-center justify-center text-white">
    <div id="game-container" class="w-full max-w-md flex flex-col items-center justify-center px-4">
        <div class="w-full flex justify-between items-center mb-3">
            <div class="flex items-center">
                <i class="fas fa-trophy text-yellow-400 mr-2"></i>
                <span id="score" class="text-xl font-bold">0</span>
            </div>
            <div class="flex space-x-2">
                <button id="settings-btn" class="game-btn">
                    <i class="fas fa-cog"></i>
                </button>
                <button id="play-pause-btn" class="game-btn">
                    <i class="fas fa-play"></i>
                </button>
                <button id="restart-btn" class="game-btn">
                    <i class="fas fa-redo-alt"></i>
                </button>
            </div>
        </div>
        
        <canvas id="game-canvas" class="w-full" width="300" height="300"></canvas>
        
        <div id="mobile-controls" class="mt-8 flex justify-center w-full">
            <div class="control-pad">
                <button class="control-btn btn-up" id="btn-up">
                    <i class="fas fa-chevron-up"></i>
                </button>
                <button class="control-btn btn-left" id="btn-left">
                    <i class="fas fa-chevron-left"></i>
                </button>
                <button class="control-btn btn-right" id="btn-right">
                    <i class="fas fa-chevron-right"></i>
                </button>
                <button class="control-btn btn-down" id="btn-down">
                    <i class="fas fa-chevron-down"></i>
                </button>
            </div>
        </div>
    </div>
    
    <!-- Game Over Modal -->
    <div id="game-over" class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50 hidden">
        <div class="bg-gray-800 rounded-lg p-8 max-w-sm w-full text-center">
            <h2 class="text-2xl font-bold mb-2 text-red-500">Game Over!</h2>
            <p class="mb-2">Your score: <span id="final-score" class="text-yellow-400 font-bold">0</span></p>
            <p class="mb-4 text-gray-400">Best score: <span id="best-score" class="text-green-400">0</span></p>
            <button id="play-again-btn" class="game-btn w-full py-3 mb-3 pulse-animation">
                Play Again
            </button>
            <p class="text-xs text-gray-500 mt-4">
                Use arrow keys or swipe on desktop<br>
                Use the on-screen controls on mobile
            </p>
        </div>
    </div>
    
    <!-- Settings Modal -->
    <div id="settings-modal" class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50 hidden">
        <div class="bg-gray-800 rounded-lg p-8 max-w-sm w-full">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-2xl font-bold">Settings</h2>
                <button id="close-settings" class="text-gray-400 hover:text-white">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>
            
            <div class="mb-6">
                <p class="mb-3 text-gray-300">Game Speed:</p>
                <div class="flex justify-between space-x-2">
                    <div id="speed-slow" class="speed-option">Slow</div>
                    <div id="speed-medium" class="speed-option speed-active">Medium</div>
                    <div id="speed-fast" class="speed-option">Fast</div>
                </div>
            </div>
            
            <button id="save-settings" class="game-btn w-full py-3">
                Save Settings
            </button>
        </div>
    </div>
    
    <!-- Start Screen -->
    <div id="start-screen" class="fixed inset-0 bg-gray-900 flex flex-col items-center justify-center z-50">
        <h1 class="text-4xl font-bold mb-6 text-center">
            <span class="text-indigo-500">Snake</span> Game
        </h1>
        <div class="mb-8 text-center">
            <p class="text-gray-300 mb-1">Eat the food to grow longer</p>
            <p class="text-gray-300 mb-4">Don't hit the walls or yourself!</p>
            <div class="flex justify-center space-x-6 mb-6">
                <div class="flex flex-col items-center">
                    <div class="rounded-full bg-red-500 w-6 h-6 mb-2"></div>
                    <p class="text-sm text-gray-400">Food</p>
                </div>
                <div class="flex flex-col items-center">
                    <div class="rounded-full bg-green-500 w-6 h-6 mb-2"></div>
                    <p class="text-sm text-gray-400">Snake</p>
                </div>
            </div>
        </div>
        <button id="start-game-btn" class="game-btn py-3 px-8 text-lg pulse-animation">
            Start Game
        </button>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Game variables
            const canvas = document.getElementById('game-canvas');
            const ctx = canvas.getContext('2d');
            const gameContainer = document.getElementById('game-container');
            const mobileControls = document.getElementById('mobile-controls');
            const gameOverModal = document.getElementById('game-over');
            const settingsModal = document.getElementById('settings-modal');
            const startScreen = document.getElementById('start-screen');
            const scoreDisplay = document.getElementById('score');
            const finalScoreDisplay = document.getElementById('final-score');
            const bestScoreDisplay = document.getElementById('best-score');
            
            // Set canvas dimensions to be square and responsive
            function resizeCanvas() {
                const containerWidth = gameContainer.clientWidth;
                const size = Math.min(containerWidth, window.innerHeight * 0.5);
                canvas.style.width = `${size}px`;
                canvas.style.height = `${size}px`;
                
                // Show/hide mobile controls based on device width
                if (window.innerWidth < 768) {
                    mobileControls.classList.remove('hidden');
                } else {
                    mobileControls.classList.add('hidden');
                }
            }
            
            window.addEventListener('resize', resizeCanvas);
            resizeCanvas();
            
            // Game settings
            let gameSpeed = 100; // Default medium speed
            let speedSetting = 'medium';
            
            // Game state
            let snake = [];
            let food = {};
            let direction = 'right';
            let nextDirection = 'right';
            let score = 0;
            let bestScore = localStorage.getItem('snakeBestScore') || 0;
            let gameInterval;
            let isPaused = false;
            let isGameOver = false;
            let gridSize = 15;
            let cellSize = canvas.width / gridSize;
            
            // Initialize game
            function initGame() {
                snake = [
                    {x: 7, y: 7},
                    {x: 6, y: 7},
                    {x: 5, y: 7}
                ];
                
                generateFood();
                
                score = 0;
                scoreDisplay.textContent = '0';
                bestScoreDisplay.textContent = bestScore;
                
                direction = 'right';
                nextDirection = 'right';
                
                isGameOver = false;
                isPaused = false;
                
                updatePlayPauseButton();
            }
            
            function generateFood() {
                // Generate random position for food
                let foodPos;
                let onSnake;
                
                do {
                    foodPos = {
                        x: Math.floor(Math.random() * gridSize),
                        y: Math.floor(Math.random() * gridSize)
                    };
                    
                    // Check if position is on snake
                    onSnake = snake.some(segment => segment.x === foodPos.x && segment.y === foodPos.y);
                } while (onSnake);
                
                food = foodPos;
            }
            
            function drawGame() {
                // Clear canvas
                ctx.fillStyle = '#2d3748';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                // Draw grid lines (subtle)
                ctx.strokeStyle = '#3a4556';
                ctx.lineWidth = 0.5;
                
                for (let i = 1; i < gridSize; i++) {
                    // Vertical lines
                    ctx.beginPath();
                    ctx.moveTo(i * cellSize, 0);
                    ctx.lineTo(i * cellSize, canvas.height);
                    ctx.stroke();
                    
                    // Horizontal lines
                    ctx.beginPath();
                    ctx.moveTo(0, i * cellSize);
                    ctx.lineTo(canvas.width, i * cellSize);
                    ctx.stroke();
                }
                
                // Draw food with glow effect
                ctx.fillStyle = '#ef4444'; // Red food
                ctx.shadowColor = '#ef4444';
                ctx.shadowBlur = 10;
                ctx.beginPath();
                ctx.arc(
                    food.x * cellSize + cellSize / 2,
                    food.y * cellSize + cellSize / 2,
                    cellSize / 2 - 2,
                    0,
                    Math.PI * 2
                );
                ctx.fill();
                
                // Reset shadow
                ctx.shadowBlur = 0;
                
                // Draw snake
                snake.forEach((segment, index) => {
                    // Head is different color
                    if (index === 0) {
                        ctx.fillStyle = '#10b981'; // Green head
                    } else {
                        // Gradient for body segments
                        const greenValue = Math.max(140, 185 - index * 3);
                        ctx.fillStyle = `rgb(52, ${greenValue}, 72)`;
                    }
                    
                    // Draw rounded segments
                    ctx.beginPath();
                    ctx.arc(
                        segment.x * cellSize + cellSize / 2,
                        segment.y * cellSize + cellSize / 2,
                        cellSize / 2 - 1,
                        0,
                        Math.PI * 2
                    );
                    ctx.fill();
                    
                    // Draw eyes on the head
                    if (index === 0) {
                        ctx.fillStyle = 'white';
                        
                        // Position eyes based on direction
                        let leftEyeX, leftEyeY, rightEyeX, rightEyeY;
                        const eyeOffset = cellSize / 4;
                        const eyeSize = cellSize / 8;
                        
                        switch(direction) {
                            case 'up':
                                leftEyeX = segment.x * cellSize + cellSize / 3;
                                leftEyeY = segment.y * cellSize + cellSize / 3;
                                rightEyeX = segment.x * cellSize + cellSize * 2/3;
                                rightEyeY = segment.y * cellSize + cellSize / 3;
                                break;
                            case 'down':
                                leftEyeX = segment.x * cellSize + cellSize * 2/3;
                                leftEyeY = segment.y * cellSize + cellSize * 2/3;
                                rightEyeX = segment.x * cellSize + cellSize / 3;
                                rightEyeY = segment.y * cellSize + cellSize * 2/3;
                                break;
                            case 'left':
                                leftEyeX = segment.x * cellSize + cellSize / 3;
                                leftEyeY = segment.y * cellSize + cellSize / 3;
                                rightEyeX = segment.x * cellSize + cellSize / 3;
                                rightEyeY = segment.y * cellSize + cellSize * 2/3;
                                break;
                            case 'right':
                                leftEyeX = segment.x * cellSize + cellSize * 2/3;
                                leftEyeY = segment.y * cellSize + cellSize * 2/3;
                                rightEyeX = segment.x * cellSize + cellSize * 2/3;
                                rightEyeY = segment.y * cellSize + cellSize / 3;
                                break;
                        }
                        
                        // Draw eyes
                        ctx.beginPath();
                        ctx.arc(leftEyeX, leftEyeY, eyeSize, 0, Math.PI * 2);
                        ctx.fill();
                        
                        ctx.beginPath();
                        ctx.arc(rightEyeX, rightEyeY, eyeSize, 0, Math.PI * 2);
                        ctx.fill();
                        
                        // Draw pupils
                        ctx.fillStyle = 'black';
                        ctx.beginPath();
                        ctx.arc(leftEyeX, leftEyeY, eyeSize / 2, 0, Math.PI * 2);
                        ctx.fill();
                        
                        ctx.beginPath();
                        ctx.arc(rightEyeX, rightEyeY, eyeSize / 2, 0, Math.PI * 2);
                        ctx.fill();
                    }
                });
            }
            
            function moveSnake() {
                // Update direction from nextDirection
                direction = nextDirection;
                
                // Create new head position
                const head = {...snake[0]};
                
                switch(direction) {
                    case 'up':
                        head.y -= 1;
                        break;
                    case 'down':
                        head.y += 1;
                        break;
                    case 'left':
                        head.x -= 1;
                        break;
                    case 'right':
                        head.x += 1;
                        break;
                }
                
                // Check for collision with walls
                if (head.x < 0 || head.x >= gridSize || head.y < 0 || head.y >= gridSize) {
                    gameOver();
                    return;
                }
                
                // Check for collision with self
                if (snake.some(segment => segment.x === head.x && segment.y === head.y)) {
                    gameOver();
                    return;
                }
                
                // Add new head to snake
                snake.unshift(head);
                
                // Check if snake ate food
                if (head.x === food.x && head.y === food.y) {
                    // Increase score
                    score += 10;
                    scoreDisplay.textContent = score;
                    
                    // Generate new food
                    generateFood();
                    
                    // Play eat sound (just a visual feedback in this implementation)
                    canvas.classList.add('pulse-animation');
                    setTimeout(() => {
                        canvas.classList.remove('pulse-animation');
                    }, 300);
                } else {
                    // Remove tail if no food eaten
                    snake.pop();
                }
            }
            
            function gameLoop() {
                if (!isPaused && !isGameOver) {
                    moveSnake();
                    drawGame();
                }
            }
            
            function startGame() {
                clearInterval(gameInterval);
                initGame();
                gameInterval = setInterval(gameLoop, gameSpeed);
                
                // Hide start screen
                startScreen.classList.add('hidden');
                
                // Update play/pause button
                updatePlayPauseButton();
            }
            
            function pauseGame() {
                isPaused = true;
                updatePlayPauseButton();
            }
            
            function resumeGame() {
                isPaused = false;
                updatePlayPauseButton();
            }
            
            function updatePlayPauseButton() {
                const playPauseBtn = document.getElementById('play-pause-btn');
                const icon = playPauseBtn.querySelector('i');
                
                if (isPaused) {
                    icon.className = 'fas fa-play';
                } else {
                    icon.className = 'fas fa-pause';
                }
            }
            
            function gameOver() {
                isGameOver = true;
                clearInterval(gameInterval);
                
                // Update best score
                if (score > bestScore) {
                    bestScore = score;
                    localStorage.setItem('snakeBestScore', bestScore);
                }
                
                // Show game over modal
                finalScoreDisplay.textContent = score;
                bestScoreDisplay.textContent = bestScore;
                gameOverModal.classList.remove('hidden');
                gameOverModal.classList.add('fade-in');
            }
            
            function restartGame() {
                // Hide game over modal if visible
                gameOverModal.classList.add('hidden');
                
                // Start a new game
                startGame();
            }
            
            // Event Listeners for game controls
            document.addEventListener('keydown', function(e) {
                // Prevent default behavior for arrow keys
                if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight', ' '].includes(e.key)) {
                    e.preventDefault();
                }
                
                // Handle arrow keys for direction
                switch(e.key) {
                    case 'ArrowUp':
                        if (direction !== 'down') nextDirection = 'up';
                        break;
                    case 'ArrowDown':
                        if (direction !== 'up') nextDirection = 'down';
                        break;
                    case 'ArrowLeft':
                        if (direction !== 'right') nextDirection = 'left';
                        break;
                    case 'ArrowRight':
                        if (direction !== 'left') nextDirection = 'right';
                        break;
                    case ' ':
                        if (isPaused) resumeGame();
                        else pauseGame();
                        break;
                }
            });
            
            // Mobile control buttons
            document.getElementById('btn-up').addEventListener('touchstart', function(e) {
                e.preventDefault();
                if (direction !== 'down') nextDirection = 'up';
            });
            
            document.getElementById('btn-down').addEventListener('touchstart', function(e) {
                e.preventDefault();
                if (direction !== 'up') nextDirection = 'down';
            });
            
            document.getElementById('btn-left').addEventListener('touchstart', function(e) {
                e.preventDefault();
                if (direction !== 'right') nextDirection = 'left';
            });
            
            document.getElementById('btn-right').addEventListener('touchstart', function(e) {
                e.preventDefault();
                if (direction !== 'left') nextDirection = 'right';
            });
            
            // Prevent mobile control buttons from triggering multiple times
            const controlButtons = document.querySelectorAll('.control-btn');
            controlButtons.forEach(btn => {
                btn.addEventListener('touchstart', e => e.preventDefault());
                btn.addEventListener('touchend', e => e.preventDefault());
            });
            
            // Game control buttons
            document.getElementById('play-pause-btn').addEventListener('click', function() {
                if (isPaused) {
                    resumeGame();
                } else {
                    pauseGame();
                }
            });
            
            document.getElementById('restart-btn').addEventListener('click', restartGame);
            
            document.getElementById('play-again-btn').addEventListener('click', restartGame);
            
            document.getElementById('start-game-btn').addEventListener('click', startGame);
            
            // Settings
            document.getElementById('settings-btn').addEventListener('click', function() {
                settingsModal.classList.remove('hidden');
                pauseGame();
            });
            
            document.getElementById('close-settings').addEventListener('click', function() {
                settingsModal.classList.add('hidden');
            });
            
            // Speed settings
            const speedOptions = {
                'slow': 150,
                'medium': 100,
                'fast': 60
            };
            
            // Speed setting buttons
            document.getElementById('speed-slow').addEventListener('click', function() {
                updateSpeedSetting('slow');
            });
            
            document.getElementById('speed-medium').addEventListener('click', function() {
                updateSpeedSetting('medium');
            });
            
            document.getElementById('speed-fast').addEventListener('click', function() {
                updateSpeedSetting('fast');
            });
            
            function updateSpeedSetting(speed) {
                // Update active class
                document.querySelectorAll('.speed-option').forEach(option => {
                    option.classList.remove('speed-active');
                });
                document.getElementById(`speed-${speed}`).classList.add('speed-active');
                
                // Save setting
                speedSetting = speed;
                gameSpeed = speedOptions[speed];
            }
            
            document.getElementById('save-settings').addEventListener('click', function() {
                settingsModal.classList.add('hidden');
                
                // Apply new settings
                clearInterval(gameInterval);
                gameInterval = setInterval(gameLoop, gameSpeed);
                
                if (!isPaused) {
                    resumeGame();
                }
            });
            
            // Swipe controls for mobile
            let touchStartX = 0;
            let touchStartY = 0;
            
            canvas.addEventListener('touchstart', function(e) {
                touchStartX = e.changedTouches[0].screenX;
                touchStartY = e.changedTouches[0].screenY;
            });
            
            canvas.addEventListener('touchend', function(e) {
                const touchEndX = e.changedTouches[0].screenX;
                const touchEndY = e.changedTouches[0].screenY;
                
                const dx = touchEndX - touchStartX;
                const dy = touchEndY - touchStartY;
                
                // Determine swipe direction based on which axis had larger movement
                if (Math.abs(dx) > Math.abs(dy)) {
                    // Horizontal swipe
                    if (dx > 0 && direction !== 'left') {
                        nextDirection = 'right';
                    } else if (dx < 0 && direction !== 'right') {
                        nextDirection = 'left';
                    }
                } else {
                    // Vertical swipe
                    if (dy > 0 && direction !== 'up') {
                        nextDirection = 'down';
                    } else if (dy < 0 && direction !== 'down') {
                        nextDirection = 'up';
                    }
                }
            });
            
            // Prevent default touch behavior on canvas
            canvas.addEventListener('touchmove', function(e) {
                e.preventDefault();
            }, { passive: false });
            
            // Display best score at start
            bestScoreDisplay.textContent = bestScore;
            
            // Draw initial game state
            drawGame();
        });
    </script>
</body>
</html>
