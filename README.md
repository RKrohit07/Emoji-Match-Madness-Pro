# Emoji-Match-Madness-Pro
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Emoji Match Madness Pro</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@400;700;800&display=swap');
        
        body {
            font-family: 'Nunito', sans-serif;
            background-color: #f0f2f5;
            min-height: 100vh;
            color: #2d3748;
        }
        
        .game-container {
            max-width: 1000px;
            margin: 0 auto;
        }
        
        .memory-card {
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.5s;
            cursor: pointer;
        }
        
        .memory-card.flip {
            transform: rotateY(180deg);
        }
        
        .front-face, .back-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 0.5rem;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        .front-face {
            transform: rotateY(180deg);
            background-color: #fff;
            font-size: 2rem;
        }
        
        .back-face {
            background-color: #4c51bf;
            transform: rotateY(0deg);
        }
        
        .back-face::after {
            content: "?";
            font-size: 2rem;
            color: white;
            font-weight: bold;
        }
        
        .memory-card.matched .front-face {
            background-color: #c6f6d5;
            animation: pulse 0.5s;
        }
        
        .memory-card.wrong .front-face {
            background-color: #fed7d7;
            animation: shake 0.5s;
        }
        
        @keyframes pulse {
            0% { transform: scale(1) rotateY(180deg); }
            50% { transform: scale(1.05) rotateY(180deg); }
            100% { transform: scale(1) rotateY(180deg); }
        }
        
        @keyframes shake {
            0% { transform: translateX(0) rotateY(180deg); }
            25% { transform: translateX(5px) rotateY(180deg); }
            50% { transform: translateX(-5px) rotateY(180deg); }
            75% { transform: translateX(5px) rotateY(180deg); }
            100% { transform: translateX(0) rotateY(180deg); }
        }
        
        .difficulty-card {
            transition: transform 0.3s, box-shadow 0.3s;
        }
        
        .difficulty-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
        }
        
        .ad-container {
            background-color: #edf2f7;
            border: 1px dashed #a0aec0;
            display: flex;
            justify-content: center;
            align-items: center;
            color: #718096;
            font-weight: bold;
            margin: 1rem 0;
        }
        
        /* Confetti Animation */
        .confetti {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1000;
            display: none;
        }
        
        .confetti-piece {
            position: absolute;
            width: 10px;
            height: 20px;
            background-color: #fbd38d;
            top: -20px;
            opacity: 0;
        }
        
        @keyframes confettiRain {
            0% {
                opacity: 1;
                transform: translateY(0) rotate(0deg);
            }
            100% {
                opacity: 0;
                transform: translateY(100vh) rotate(360deg);
            }
        }
    </style>
</head>
<body>
    <div class="game-container p-4">
        <!-- Header -->
        <header class="text-center py-4">
            <h1 class="text-4xl font-extrabold text-indigo-700 mb-2">Emoji Match Madness Pro</h1>
            <p class="text-gray-600 mb-4">Find matching emoji pairs to win!</p>
            
            <!-- Top Ad Banner -->
            <div class="ad-container h-16 mb-6" id="admob-banner-top">
                AdMob Banner Ad
            </div>
        </header>
        
        <!-- Game Screens -->
        <main class="relative">
            <!-- Home Screen (Difficulty Selection) -->
            <section id="home-screen" class="block">
                <h2 class="text-2xl font-bold text-center mb-6">Select Difficulty</h2>
                
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <!-- Easy -->
                    <div class="difficulty-card bg-white p-6 rounded-lg shadow-md text-center cursor-pointer" data-difficulty="easy">
                        <div class="text-3xl text-green-500 mb-2">
                            <i class="fas fa-smile"></i>
                        </div>
                        <h3 class="text-xl font-bold mb-2">Easy</h3>
                        <p class="text-gray-700 mb-2">4x3 Grid</p>
                        <p class="text-gray-500 text-sm">Perfect for beginners</p>
                    </div>
                    
                    <!-- Medium -->
                    <div class="difficulty-card bg-white p-6 rounded-lg shadow-md text-center cursor-pointer" data-difficulty="medium">
                        <div class="text-3xl text-yellow-500 mb-2">
                            <i class="fas fa-puzzle-piece"></i>
                        </div>
                        <h3 class="text-xl font-bold mb-2">Medium</h3>
                        <p class="text-gray-700 mb-2">6x4 Grid</p>
                        <p class="text-gray-500 text-sm">Challenge your memory</p>
                    </div>
                    
                    <!-- Hard -->
                    <div class="difficulty-card bg-white p-6 rounded-lg shadow-md text-center cursor-pointer" data-difficulty="hard">
                        <div class="text-3xl text-red-500 mb-2">
                            <i class="fas fa-fire"></i>
                        </div>
                        <h3 class="text-xl font-bold mb-2">Hard</h3>
                        <p class="text-gray-700 mb-2">8x4 Grid</p>
                        <p class="text-gray-500 text-sm">For memory masters</p>
                    </div>
                </div>
                
                <!-- Settings Button -->
                <div class="text-center mt-8">
                    <button id="settings-btn" class="bg-gray-200 hover:bg-gray-300 text-gray-800 py-2 px-4 rounded-full shadow transition">
                        <i class="fas fa-cog mr-2"></i> Settings
                    </button>
                </div>
            </section>
            
            <!-- Game Screen -->
            <section id="game-screen" class="hidden">
                <!-- Game Stats -->
                <div class="bg-white rounded-lg shadow-md p-4 mb-4 flex flex-wrap justify-around">
                    <div class="text-center px-4 py-2">
                        <p class="text-gray-600 text-sm">Time</p>
                        <p id="timer" class="text-xl font-bold">00:00</p>
                    </div>
                    <div class="text-center px-4 py-2">
                        <p class="text-gray-600 text-sm">Moves</p>
                        <p id="moves" class="text-xl font-bold">0</p>
                    </div>
                    <div class="text-center px-4 py-2">
                        <p class="text-gray-600 text-sm">Score</p>
                        <p id="score" class="text-xl font-bold">0</p>
                    </div>
                    <div class="text-center px-4 py-2">
                        <button id="restart-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white py-1 px-3 rounded-full text-sm shadow transition">
                            <i class="fas fa-redo-alt mr-1"></i> Restart
                        </button>
                        <button id="home-btn" class="bg-gray-500 hover:bg-gray-600 text-white py-1 px-3 rounded-full text-sm shadow ml-2 transition">
                            <i class="fas fa-home mr-1"></i> Menu
                        </button>
                    </div>
                </div>
                
                <!-- Game Grid -->
                <div id="game-board" class="grid gap-2 sm:gap-3 md:gap-4 mb-6">
                    <!-- Cards will be inserted here by JavaScript -->
                </div>
            </section>
            
            <!-- Victory Screen -->
            <section id="victory-screen" class="hidden text-center bg-white rounded-lg shadow-md p-6">
                <div class="confetti" id="confetti-container"></div>
                
                <h2 class="text-3xl font-bold text-green-600 mb-4">Congratulations!</h2>
                <p class="text-gray-700 mb-4">You've matched all the emojis!</p>
                
                <div class="bg-green-100 rounded-lg p-4 mb-6">
                    <div class="stats-grid grid grid-cols-2 gap-4 mb-4">
                        <div class="text-left">
                            <p class="text-gray-600">Time:</p>
                            <p id="final-time" class="text-xl font-bold">00:00</p>
                        </div>
                        <div class="text-left">
                            <p class="text-gray-600">Moves:</p>
                            <p id="final-moves" class="text-xl font-bold">0</p>
                        </div>
                        <div class="text-left">
                            <p class="text-gray-600">Score:</p>
                            <p id="final-score" class="text-xl font-bold">0</p>
                        </div>
                        <div class="text-left">
                            <p class="text-gray-600">Difficulty:</p>
                            <p id="final-difficulty" class="text-xl font-bold">Easy</p>
                        </div>
                    </div>
                </div>
                
                <!-- Achievement Notification -->
                <div id="achievement-box" class="hidden bg-yellow-100 rounded-lg p-4 mb-6">
                    <h3 class="text-xl font-bold text-yellow-700 mb-2">New Achievement Unlocked!</h3>
                    <p id="achievement-name" class="text-lg font-semibold">Memory Master</p>
                    <p id="achievement-desc" class="text-gray-700">Complete a game in under 1 minute</p>
                </div>
                
                <div class="flex flex-wrap justify-center gap-3">
                    <button id="play-again-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white py-2 px-5 rounded-full shadow transition">
                        <i class="fas fa-redo-alt mr-2"></i> Play Again
                    </button>
                    <button id="victory-home-btn" class="bg-gray-500 hover:bg-gray-600 text-white py-2 px-5 rounded-full shadow transition">
                        <i class="fas fa-home mr-2"></i> Main Menu
                    </button>
                </div>
                
                <!-- Bottom Ad Banner -->
                <div class="ad-container h-32 mt-6" id="admob-interstitial">
                    AdMob Interstitial Ad
                </div>
            </section>
            
            <!-- Settings Screen -->
            <section id="settings-screen" class="hidden">
                <div class="bg-white rounded-lg shadow-md p-6">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold">Settings</h2>
                        <button id="close-settings-btn" class="text-gray-500 hover:text-gray-700">
                            <i class="fas fa-times text-xl"></i>
                        </button>
                    </div>
                    
                    <div class="space-y-6">
                        <!-- Sound Effects -->
                        <div class="flex items-center justify-between">
                            <div>
                                <h3 class="text-lg font-semibold">Sound Effects</h3>
                                <p class="text-gray-600 text-sm">Game sounds and effects</p>
                            </div>
                            <label class="switch relative inline-block w-12 h-6">
                                <input type="checkbox" id="sound-toggle" checked>
                                <span class="slider absolute cursor-pointer top-0 left-0 right-0 bottom-0 bg-gray-300 rounded-full transition-all duration-300 before:absolute before:h-4 before:w-4 before:left-1 before:bottom-1 before:bg-white before:rounded-full before:transition-all before:duration-300"></span>
                            </label>
                        </div>
                        
                        <!-- Background Music -->
                        <div class="flex items-center justify-between">
                            <div>
                                <h3 class="text-lg font-semibold">Background Music</h3>
                                <p class="text-gray-600 text-sm">Play music during gameplay</p>
                            </div>
                            <label class="switch relative inline-block w-12 h-6">
                                <input type="checkbox" id="music-toggle">
                                <span class="slider absolute cursor-pointer top-0 left-0 right-0 bottom-0 bg-gray-300 rounded-full transition-all duration-300 before:absolute before:h-4 before:w-4 before:left-1 before:bottom-1 before:bg-white before:rounded-full before:transition-all before:duration-300"></span>
                            </label>
                        </div>
                        
                        <!-- Vibration -->
                        <div class="flex items-center justify-between">
                            <div>
                                <h3 class="text-lg font-semibold">Vibration</h3>
                                <p class="text-gray-600 text-sm">Vibrate on card match</p>
                            </div>
                            <label class="switch relative inline-block w-12 h-6">
                                <input type="checkbox" id="vibration-toggle" checked>
                                <span class="slider absolute cursor-pointer top-0 left-0 right-0 bottom-0 bg-gray-300 rounded-full transition-all duration-300 before:absolute before:h-4 before:w-4 before:left-1 before:bottom-1 before:bg-white before:rounded-full before:transition-all before:duration-300"></span>
                            </label>
                        </div>
                        
                        <!-- Card Back Design -->
                        <div>
                            <h3 class="text-lg font-semibold mb-2">Card Back Design</h3>
                            <div class="grid grid-cols-4 gap-3">
                                <div class="card-back-option cursor-pointer h-12 bg-indigo-600 rounded-md shadow-sm" data-color="#4c51bf"></div>
                                <div class="card-back-option cursor-pointer h-12 bg-green-600 rounded-md shadow-sm" data-color="#38a169"></div>
                                <div class="card-back-option cursor-pointer h-12 bg-red-600 rounded-md shadow-sm" data-color="#e53e3e"></div>
                                <div class="card-back-option cursor-pointer h-12 bg-yellow-500 rounded-md shadow-sm" data-color="#ecc94b"></div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
        </main>
        
        <!-- Bottom Ad Banner -->
        <footer class="mt-6">
            <div class="ad-container h-16" id="admob-banner-bottom">
                AdMob Banner Ad
            </div>
        </footer>
    </div>
    
    <!-- Audio Elements -->
    <audio id="flip-sound" src="https://cdn.jsdelivr.net/gh/freeCodeCamp/cdn@master/build/testable-projects-fcc/audio/BeepSound.wav" preload="auto"></audio>
    <audio id="match-sound" src="https://cdn.jsdelivr.net/gh/freeCodeCamp/cdn@master/build/testable-projects-fcc/audio/drum-kit/snare.mp3" preload="auto"></audio>
    <audio id="wrong-sound" src="https://cdn.jsdelivr.net/gh/freeCodeCamp/cdn@master/build/testable-projects-fcc/audio/drum-kit/kick.mp3" preload="auto"></audio>
    <audio id="victory-sound" src="https://cdn.jsdelivr.net/gh/freeCodeCamp/cdn@master/build/testable-projects-fcc/audio/drum-kit/crash.mp3" preload="auto"></audio>
    <audio id="bg-music" src="https://cdn.jsdelivr.net/gh/freeCodeCamp/cdn@master/build/testable-projects-fcc/audio/BeepSound.wav" loop preload="auto"></audio>
    
    <script>
        // ===== GAME VARIABLES =====
        let cards = [];
        let hasFlippedCard = false;
        let lockBoard = false;
        let firstCard, secondCard;
        let moves = 0;
        let matchedPairs = 0;
        let totalPairs = 0;
        let score = 0;
        let timerInterval = null;
        let startTime = 0;
        let currentTime = 0;
        let gameActive = false;
        let currentDifficulty = 'easy';
        let gameConfig = {
            easy: { rows: 3, cols: 4 },
            medium: { rows: 4, cols: 6 },
            hard: { rows: 4, cols: 8 }
        };
        
        // Game settings
        let settings = {
            soundEffects: true,
            backgroundMusic: false,
            vibration: true,
            cardBackColor: '#4c51bf'
        };
        
        // Emojis pool for cards
        const emojis = [
            '😀', '😃', '😄', '😁', '😆', '😅', '😂', '🤣', '😊', '😇',
            '🙂', '🙃', '😉', '😌', '😍', '🥰', '😘', '😗', '😙', '😚',
            '😋', '😛', '😝', '😜', '🤪', '🤨', '🧐', '🤓', '😎', '🥸',
            '🤩', '🥳', '😏', '😒', '😞', '😔', '😟', '😕', '🙁', '☹️',
            '😣', '😖', '😫', '😩', '🥺', '😢', '😭', '😤', '😠', '😡',
            '🤬', '🤯', '😳', '🥵', '🥶', '😱', '😨', '😰', '😥', '😓',
            '🤗', '🤔', '🤭', '🤫', '🤥', '😶', '😐', '😑', '😬', '🙄'
        ];
        
        // Achievements
        const achievements = [
            { id: 'first_win', name: 'First Victory', description: 'Complete your first game', unlocked: false },
            { id: 'speed_demon', name: 'Speed Demon', description: 'Complete Easy mode in under 30 seconds', unlocked: false },
            { id: 'memory_master', name: 'Memory Master', description: 'Complete Hard mode with less than 20 moves', unlocked: false },
            { id: 'perfect_match', name: 'Perfect Match', description: 'Complete a game without any wrong moves', unlocked: false },
            { id: 'high_scorer', name: 'High Scorer', description: 'Score over 1000 points in a single game', unlocked: false }
        ];
        
        // ===== DOM ELEMENTS =====
        const homeScreen = document.getElementById('home-screen');
        const gameScreen = document.getElementById('game-screen');
        const victoryScreen = document.getElementById('victory-screen');
        const settingsScreen = document.getElementById('settings-screen');
        const gameBoard = document.getElementById('game-board');
        const timerElement = document.getElementById('timer');
        const movesElement = document.getElementById('moves');
        const scoreElement = document.getElementById('score');
        const finalTimeElement = document.getElementById('final-time');
        const finalMovesElement = document.getElementById('final-moves');
        const finalScoreElement = document.getElementById('final-score');
        const finalDifficultyElement = document.getElementById('final-difficulty');
        const difficultyCards = document.querySelectorAll('.difficulty-card');
        const restartButton = document.getElementById('restart-btn');
        const homeButton = document.getElementById('home-btn');
        const playAgainButton = document.getElementById('play-again-btn');
        const victoryHomeButton = document.getElementById('victory-home-btn');
        const settingsButton = document.getElementById('settings-btn');
        const closeSettingsButton = document.getElementById('close-settings-btn');
        const soundToggle = document.getElementById('sound-toggle');
        const musicToggle = document.getElementById('music-toggle');
        const vibrationToggle = document.getElementById('vibration-toggle');
        const cardBackOptions = document.querySelectorAll('.card-back-option');
        const achievementBox = document.getElementById('achievement-box');
        const achievementName = document.getElementById('achievement-name');
        const achievementDesc = document.getElementById('achievement-desc');
        const confettiContainer = document.getElementById('confetti-container');
        
        // Sound elements
        const flipSound = document.getElementById('flip-sound');
        const matchSound = document.getElementById('match-sound');
        const wrongSound = document.getElementById('wrong-sound');
        const victorySound = document.getElementById('victory-sound');
        const bgMusic = document.getElementById('bg-music');
        
        // ===== EVENT LISTENERS =====
        difficultyCards.forEach(card => {
            card.addEventListener('click', () => {
                currentDifficulty = card.getAttribute('data-difficulty');
                startGame();
            });
        });
        
        restartButton.addEventListener('click', restartGame);
        homeButton.addEventListener('click', goToHome);
        playAgainButton.addEventListener('click', restartGame);
        victoryHomeButton.addEventListener('click', goToHome);
        settingsButton.addEventListener('click', showSettings);
        closeSettingsButton.addEventListener('click', hideSettings);
        
        // Settings toggles
        soundToggle.addEventListener('change', () => {
            settings.soundEffects = soundToggle.checked;
            localStorage.setItem('emojiMatch_settings', JSON.stringify(settings));
        });
        
        musicToggle.addEventListener('change', () => {
            settings.backgroundMusic = musicToggle.checked;
            if (settings.backgroundMusic && gameActive) {
                bgMusic.play();
            } else {
                bgMusic.pause();
            }
            localStorage.setItem('emojiMatch_settings', JSON.stringify(settings));
        });
        
        vibrationToggle.addEventListener('change', () => {
            settings.vibration = vibrationToggle.checked;
            localStorage.setItem('emojiMatch_settings', JSON.stringify(settings));
        });
        
        cardBackOptions.forEach(option => {
            option.addEventListener('click', () => {
                settings.cardBackColor = option.getAttribute('data-color');
                updateCardBackColor();
                localStorage.setItem('emojiMatch_settings', JSON.stringify(settings));
                
                // Visual feedback for selection
                cardBackOptions.forEach(opt => opt.classList.remove('ring-4', 'ring-offset-2', 'ring-indigo-500'));
                option.classList.add('ring-4', 'ring-offset-2', 'ring-indigo-500');
            });
        });
        
        // ===== GAME FUNCTIONS =====
        function initGame() {
            // Load settings from local storage
            const savedSettings = localStorage.getItem('emojiMatch_settings');
            if (savedSettings) {
                settings = JSON.parse(savedSettings);
                soundToggle.checked = settings.soundEffects;
                musicToggle.checked = settings.backgroundMusic;
                vibrationToggle.checked = settings.vibration;
                
                // Highlight the selected card back
                cardBackOptions.forEach(option => {
                    if (option.getAttribute('data-color') === settings.cardBackColor) {
                        option.classList.add('ring-4', 'ring-offset-2', 'ring-indigo-500');
                    }
                });
            }
            
            // Load achievements
            const savedAchievements = localStorage.getItem('emojiMatch_achievements');
            if (savedAchievements) {
                const loadedAchievements = JSON.parse(savedAchievements);
                achievements.forEach((achievement, index) => {
                    if (loadedAchievements[index]) {
                        achievement.unlocked = loadedAchievements[index].unlocked;
                    }
                });
            }
            
            // Init AdMob placeholder (would be replaced with actual AdMob implementation)
            initAdMob();
        }
        
        function startGame() {
            // Reset game state
            resetGame();
            
            // Show game screen, hide others
            homeScreen.classList.add('hidden');
            gameScreen.classList.remove('hidden');
            victoryScreen.classList.add('hidden');
            settingsScreen.classList.add('hidden');
            
            // Get configuration based on difficulty
            const config = gameConfig[currentDifficulty];
            totalPairs = (config.rows * config.cols) / 2;
            
            // Setup game board grid layout
            gameBoard.style.gridTemplateColumns = `repeat(${config.cols}, 1fr)`;
            gameBoard.style.gridTemplateRows = `repeat(${config.rows}, 1fr)`;
            
            // Create and shuffle cards
            createCards(config.rows * config.cols);
            
            // Start timer
            startTimer();
            
            // Start background music if enabled
            if (settings.backgroundMusic) {
                bgMusic.play();
            }
            
            // Game is now active
            gameActive = true;
        }
        
        function createCards(totalCards) {
            // Clear existing cards
            gameBoard.innerHTML = '';
            cards = [];
            
            // Get random emojis for this game
            const gameEmojis = getRandomEmojis(totalCards / 2);
            
            // Double the emojis to create pairs
            const cardValues = [...gameEmojis, ...gameEmojis];
            
            // Shuffle the cards
            shuffle(cardValues);
            
            // Create card elements
            cardValues.forEach((emoji, index) => {
                const card = document.createElement('div');
                card.classList.add('memory-card');
                
                // Calculate card size based on difficulty
                const cardHeight = currentDifficulty === 'easy' ? '100px' : 
                                  currentDifficulty === 'medium' ? '85px' : '70px';
                
                card.style.height = cardHeight;
                
                card.innerHTML = `
                    <div class="front-face">${emoji}</div>
                    <div class="back-face" style="background-color: ${settings.cardBackColor};"></div>
                `;
                
                card.addEventListener('click', flipCard);
                card.dataset.value = emoji;
                gameBoard.appendChild(card);
                cards.push(card);
            });
        }
        
        function getRandomEmojis(count) {
            // Shuffle emojis array
            const shuffledEmojis = [...emojis];
            shuffle(shuffledEmojis);
            
            // Return the required number of emojis
            return shuffledEmojis.slice(0, count);
        }
        
        function shuffle(array) {
            let currentIndex = array.length;
            let temporaryValue, randomIndex;
            
            // While there remain elements to shuffle
            while (currentIndex !== 0) {
                // Pick a remaining element
                randomIndex = Math.floor(Math.random() * currentIndex);
                currentIndex -= 1;
                
                // Swap it with the current element
                temporaryValue = array[currentIndex];
                array[currentIndex] = array[randomIndex];
                array[randomIndex] = temporaryValue;
            }
            
            return array;
        }
        
        function flipCard() {
            // Ignore if board is locked or this card is already flipped
            if (lockBoard) return;
            if (this === firstCard) return;
            if (this.classList.contains('matched')) return;
            
            // Play flip sound
            playSound(flipSound);
            
            // Flip the card
            this.classList.add('flip');
            
            // First card flipped
            if (!hasFlippedCard) {
                hasFlippedCard = true;
                firstCard = this;
                return;
            }
            
            // Second card flipped
            secondCard = this;
            checkForMatch();
            
            // Increment moves counter
            moves++;
            movesElement.textContent = moves;
        }
        
        function checkForMatch() {
            // Lock the board
            lockBoard = true;
            
            // Check if the cards match
            let isMatch = firstCard.dataset.value === secondCard.dataset.value;
            
            if (isMatch) {
                // Cards match
                disableCards();
                matchedPairs++;
                
                // Play match sound
                playSound(matchSound);
                
                // Vibrate if enabled
                if (settings.vibration && window.navigator.vibrate) {
                    window.navigator.vibrate(200);
                }
                
                // Update score
                updateScore(100);
                
                // Check if game is won
                if (matchedPairs === totalPairs) {
                    endGame();
                } else {
                    // Unlock board for next selection
                    lockBoard = false;
                }
            } else {
                // Cards don't match
                unflipCards();
                
                // Play wrong sound
                playSound(wrongSound);
                
                // Vibrate if enabled (short pulses for wrong match)
                if (settings.vibration && window.navigator.vibrate) {
                    window.navigator.vibrate([100, 50, 100]);
                }
                
                // Update score (small penalty)
                updateScore(-10);
            }
        }
        
        function disableCards() {
            // Mark cards as matched
            firstCard.classList.add('matched');
            secondCard.classList.add('matched');
            
            // Remove click event
            firstCard.removeEventListener('click', flipCard);
            secondCard.removeEventListener('click', flipCard);
            
            // Reset board
            resetBoard();
        }
        
        function unflipCards() {
            // Add wrong class for visual feedback
            firstCard.classList.add('wrong');
            secondCard.classList.add('wrong');
            
            setTimeout(() => {
                // Remove wrong class
                firstCard.classList.remove('wrong');
                secondCard.classList.remove('wrong');
                
                // Flip cards back
                firstCard.classList.remove('flip');
                secondCard.classList.remove('flip');
                
                // Reset board
                resetBoard();
            }, 1000);
        }
        
        function resetBoard() {
            // Reset variables
            [hasFlippedCard, lockBoard] = [false, false];
            [firstCard, secondCard] = [null, null];
        }
        
        function updateScore(points) {
            score += points;
            if (score < 0) score = 0;
            scoreElement.textContent = score;
        }
        
        function startTimer() {
            // Reset timer
            startTime = Date.now();
            currentTime = 0;
            timerElement.textContent = '00:00';
            
            // Start interval
            clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                currentTime = Date.now() - startTime;
                const seconds = Math.floor(currentTime / 1000);
                const minutes = Math.floor(seconds / 60);
                const formattedTime = `${minutes.toString().padStart(2, '0')}:${(seconds % 60).toString().padStart(2, '0')}`;
                timerElement.textContent = formattedTime;
            }, 1000);
        }
        
        function endGame() {
            // Stop timer
            clearInterval(timerInterval);
            
            // Stop background music
            bgMusic.pause();
            
            // Play victory sound
            playSound(victorySound);
            
            // Calculate final score
            const timeBonus = calculateTimeBonus();
            const moveBonus = calculateMoveBonus();
            const difficultyBonus = calculateDifficultyBonus();
            
            // Final score calculation (current score + bonuses)
            const finalScore = score + timeBonus + moveBonus + difficultyBonus;
            
            // Update final stats
            finalTimeElement.textContent = timerElement.textContent;
            finalMovesElement.textContent = moves;
            finalScoreElement.textContent = finalScore;
            finalDifficultyElement.textContent = currentDifficulty.charAt(0).toUpperCase() + currentDifficulty.slice(1);
            
            // Check for achievements
            checkAchievements(finalScore);
            
            // Show victory screen
            setTimeout(() => {
                gameScreen.classList.add('hidden');
                victoryScreen.classList.remove('hidden');
                showConfetti();
                
                // Show interstitial ad
                showInterstitialAd();
            }, 1000);
            
            // Game is no longer active
            gameActive = false;
        }
        
        function calculateTimeBonus() {
            const seconds = Math.floor(currentTime / 1000);
            // Faster completion = higher bonus
            // Base bonus is 500, decreases by 5 points per second
            const timeBonus = Math.max(500 - seconds * 5, 0);
            return timeBonus;
        }
        
        function calculateMoveBonus() {
            // Optimal moves = pairs * 2
            const optimalMoves = totalPairs * 2;
            // Fewer moves = higher bonus
            // Base bonus is 500, decreases based on moves above optimal
            const moveDifference = Math.max(moves - optimalMoves, 0);
            const moveBonus = Math.max(500 - moveDifference * 10, 0);
            return moveBonus;
        }
        
        function calculateDifficultyBonus() {
            // Bonus based on difficulty
            switch(currentDifficulty) {
                case 'easy': return 100;
                case 'medium': return 300;
                case 'hard': return 500;
                default: return 0;
            }
        }
        
        function checkAchievements(finalScore) {
            let newAchievement = null;
            
            // Check for First Victory achievement
            if (!achievements[0].unlocked) {
                achievements[0].unlocked = true;
                newAchievement = achievements[0];
            }
            
            // Check for Speed Demon achievement
            if (!achievements[1].unlocked && currentDifficulty === 'easy' && currentTime < 30000) {
                achievements[1].unlocked = true;
                newAchievement = achievements[1];
            }
            
            // Check for Memory Master achievement
            if (!achievements[2].unlocked && currentDifficulty === 'hard' && moves < 20) {
                achievements[2].unlocked = true;
                newAchievement = achievements[2];
            }
            
            // Check for Perfect Match achievement (moves should be exactly optimal)
            if (!achievements[3].unlocked && moves === totalPairs * 2) {
                achievements[3].unlocked = true;
                newAchievement = achievements[3];
            }
            
            // Check for High Scorer achievement
            if (!achievements[4].unlocked && finalScore > 1000) {
                achievements[4].unlocked = true;
                newAchievement = achievements[4];
            }
            
            // Save achievements
            localStorage.setItem('emojiMatch_achievements', JSON.stringify(achievements));
            
            // Display new achievement if unlocked
            if (newAchievement) {
                achievementName.textContent = newAchievement.name;
                achievementDesc.textContent = newAchievement.description;
                achievementBox.classList.remove('hidden');
            } else {
                achievementBox.classList.add('hidden');
            }
        }
        
        function resetGame() {
            // Reset game variables
            cards = [];
            hasFlippedCard = false;
            lockBoard = false;
            firstCard = null;
            secondCard = null;
            moves = 0;
            matchedPairs = 0;
            score = 0;
            
            // Reset UI elements
            movesElement.textContent = '0';
            scoreElement.textContent = '0';
            
            // Stop any running timer
            clearInterval(timerInterval);
        }
        
        function goToHome() {
            // Reset game
            resetGame();
            
            // Stop timer and background music
            clearInterval(timerInterval);
            bgMusic.pause();
            
            // Show home screen, hide others
            homeScreen.classList.remove('hidden');
            gameScreen.classList.add('hidden');
            victoryScreen.classList.add('hidden');
            settingsScreen.classList.add('hidden');
            
            // Game is no longer active
            gameActive = false;
        }
        
        function restartGame() {
            // If coming from victory screen, go back to game screen
            if (!victoryScreen.classList.contains('hidden')) {
                victoryScreen.classList.add('hidden');
                gameScreen.classList.remove('hidden');
            }
            
            // Start a new game with same difficulty
            startGame();
        }
        
        function showSettings() {
            settingsScreen.classList.remove('hidden');
        }
        
        function hideSettings() {
            settingsScreen.classList.add('hidden');
        }
        
        function updateCardBackColor() {
            // Update all back faces to new color
            document.querySelectorAll('.back-face').forEach(face => {
                face.style.backgroundColor = settings.cardBackColor;
            });
        }
        
        function playSound(soundElement) {
            if (settings.soundEffects) {
                soundElement.currentTime = 0;
                soundElement.play();
            }
        }
        
        function showConfetti() {
            confettiContainer.style.display = 'block';
            const colors = ['#f94144', '#f3722c', '#f8961e', '#f9c74f', '#90be6d', '#43aa8b', '#577590'];
            
            // Create confetti pieces
            for (let i = 0; i < 100; i++) {
                const confetti = document.createElement('div');
                confetti.classList.add('confetti-piece');
                
                // Random properties
                const color = colors[Math.floor(Math.random() * colors.length)];
                const left = Math.random() * 100;
                const width = Math.random() * 10 + 5;
                const height = Math.random() * 20 + 10;
                const animationDuration = Math.random() * 3 + 2;
                const animationDelay = Math.random() * 2;
                
                // Apply styles
                confetti.style.backgroundColor = color;
                confetti.style.left = `${left}%`;
                confetti.style.width = `${width}px`;
                confetti.style.height = `${height}px`;
                confetti.style.animation = `confettiRain ${animationDuration}s ease forwards ${animationDelay}s`;
                
                confettiContainer.appendChild(confetti);
            }
            
            // Remove confetti after animation
            setTimeout(() => {
                confettiContainer.innerHTML = '';
                confettiContainer.style.display = 'none';
            }, 5000);
        }
        
        // ===== ADMOB INTEGRATION =====
        function initAdMob() {
            // This would be replaced with actual Google AdMob implementation
            console.log('Initializing AdMob');
            
            // For demonstration purposes, we're just showing placeholder divs
            // In a real implementation, you would initialize AdMob SDK here
            
            /* 
            // Example AdMob initialization code:
            document.addEventListener('deviceready', function() {
                if(typeof admob !== 'undefined') {
                    // Set ad units
                    var admobid = {
                        banner: 'ca-app-pub-XXXXXXXXXXXXXXXX/YYYYYYYYYY',
                        interstitial: 'ca-app-pub-XXXXXXXXXXXXXXXX/ZZZZZZZZZZ'
                    };
                    
                    // Init AdMob
                    admob.setOptions({
                        publisherId: admobid.banner,
                        interstitialAdId: admobid.interstitial,
                        autoShowBanner: true,
                        autoShowInterstitial: false
                    });
                    
                    // Create banners
                    admob.createBannerView();
                }
            }, false);
            */
        }
        
        function showInterstitialAd() {
            // This would be replaced with actual AdMob interstitial display
            console.log('Showing interstitial ad');
            
            // In a real implementation, you would show an AdMob interstitial here
            
            /*
            // Example code to show an interstitial ad:
            if(typeof admob !== 'undefined') {
                admob.requestInterstitialAd({
                    autoShowInterstitial: true
                });
            }
            */
        }
        
        // ===== INITIALIZATION =====
        // Initialize the game when the page loads
        window.addEventListener('load', initGame);
    </script>
</body>
</html>
