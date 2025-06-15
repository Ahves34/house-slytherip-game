<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🏰 House Slytherip: Büyülü Eşleştirme</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        /* Ensuring Inter font is applied as fallback for Tailwind's default */
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Card specific styles */
        .card-container {
            perspective: 1000px; /* For 3D flip effect */
        }
        .card-inner {
            width: 100%;
            height: 100%;
            transition: transform 0.6s;
            transform-style: preserve-3d;
            position: relative;
        }
        .card-container.flipped .card-inner {
            transform: rotateY(180deg);
        }
        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden; /* Hide back of the card when flipped */
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 0.75rem; /* rounded-xl */
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06); /* shadow-md */
        }
        .card-front {
            background-color: #2c3e50; /* Dark blue-gray for card back */
            border: 2px solid #5cb85c; /* Slytherip green border */
        }
        .card-back {
            background-color: #4a5568; /* Slightly lighter gray for card front */
            transform: rotateY(180deg);
            color: #d1d5db; /* text-gray-300 */
        }
        .card-front span, .card-back span {
            font-size: 3rem; /* text-5xl */
        }
        .card-front img {
            width: 70%;
            height: 70%;
            object-fit: contain;
            filter: grayscale(100%) brightness(50%) sepia(100%) hue-rotate(100deg) saturate(300%); /* Greenish tint for the back design */
        }
    </style>
</head>
<body>
    <!-- Ana oyun kapsayıcısı -->
    <div class="min-h-screen bg-gray-900 text-gray-100 flex items-center justify-center p-4 font-inter">
        <div class="bg-gray-800 rounded-3xl shadow-lg p-6 sm:p-8 md:p-10 w-full max-w-lg mx-auto relative overflow-hidden">
            <!-- Arka plan yılan deseni -->
            <div class="absolute inset-0 opacity-10 pointer-events-none">
                <svg class="w-full h-full" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid slice">
                    <defs>
                        <pattern id="snake-pattern-bg" x="0" y="0" width="20" height="20" patternUnits="userSpaceOnUse">
                            <path d="M10 0 L15 5 L10 10 L5 5 Z" fill="#4a5568" />
                            <path d="M0 10 L5 15 L10 10 L5 5 Z" fill="#4a5568" transform="translate(10,10)" />
                        </pattern>
                    </defs>
                    <rect x="0" y="0" width="100%" height="100%" fill="url(#snake-pattern-bg)" />
                </svg>
            </div>

            <div class="relative z-10 text-center">
                <!-- Oyun başlığı -->
                <h1 class="text-4xl sm:text-5xl font-bold mb-4 text-green-400 drop-shadow-lg">
                    🏰 House Slytherip
                </h1>
                <!-- Oyun alt başlığı -->
                <h2 class="text-2xl sm:text-3xl font-semibold mb-6 text-green-300">
                    Büyülü Eşleştirme Oyunu
                </h2>

                <!-- Oyun açıklaması -->
                <p class="text-md sm:text-lg mb-6 text-gray-300">
                    Gizemli kartları çevirerek sihirli çiftleri eşleştir! Tüm çiftleri bulmak için süreyi ve hamlelerini iyi kullan.
                </p>

                <!-- Skor ve süre göstergeleri -->
                <div class="flex justify-between items-center mb-6 text-xl sm:text-2xl font-bold">
                    <div class="bg-gray-700 px-4 py-2 rounded-xl">Hamle: <span id="moves">0</span></div>
                    <div class="bg-gray-700 px-4 py-2 rounded-xl">Süre: <span id="timer">90</span>s</div>
                </div>

                <!-- Oyun tahtası -->
                <div id="game-board" class="grid grid-cols-4 gap-4 mb-8">
                    <!-- Kartlar buraya JS tarafından eklenecek -->
                </div>

                <!-- Oyunu Başlat/Yeniden Başlat düğmesi -->
                <button id="start-game-btn" class="bg-gradient-to-r from-green-500 to-green-700 hover:from-green-600 hover:to-green-800 text-white font-bold py-4 px-8 rounded-full text-xl transition duration-300 transform hover:scale-105 shadow-lg">
                    Oyunu Başlat
                </button>

                <!-- Özel mesaj kutusu -->
                <div id="message-box" class="hidden fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50">
                    <div class="bg-gray-700 rounded-2xl p-6 sm:p-8 text-center shadow-2xl relative">
                        <h3 id="message-title" class="text-3xl font-bold mb-4 text-green-400"></h3>
                        <p id="message-text" class="text-lg text-gray-200 mb-6"></p>
                        <button id="close-message-btn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-full transition duration-300 transform hover:scale-105">
                            Kapat
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        // Tailwind CSS özel yapılandırması (isteğe bağlı)
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        inter: ['Inter', 'sans-serif'], // Inter fontunu kullan
                    },
                    colors: {
                        // Burada özel renkler tanımlanabilir
                    }
                }
            }
        }
    </script>
    <script>
        // JavaScript oyun mantığı
        document.addEventListener('DOMContentLoaded', () => {
            // DOM Elementleri
            const gameBoard = document.getElementById('game-board');
            const movesDisplay = document.getElementById('moves');
            const timerDisplay = document.getElementById('timer');
            const startGameBtn = document.getElementById('start-game-btn');

            // Mesaj kutusu elementleri
            const messageBox = document.getElementById('message-box');
            const messageTitle = document.getElementById('message-title');
            const messageText = document.getElementById('message-text');
            const closeMessageBtn = document.getElementById('close-message-btn');

            // Oyun değişkenleri
            let cards = []; // Oyun kartlarını tutacak dizi
            let flippedCards = []; // Çevrilmiş iki kartı tutar
            let matchedPairs = 0; // Eşleşen çift sayısı
            let totalMoves = 0; // Toplam hamle sayısı
            let timeLeft = 90; // Saniye cinsinden süre (90 saniye)
            let gameInterval; // Zamanlayıcı interval'ı
            let gameActive = false; // Oyunun aktif olup olmadığını belirler
            let lockBoard = false; // Kartların çevrilmesini engeller (eşleşme kontrolü sırasında)

            // Kart içerikleri (emoji veya ikon)
            const cardEmojis = [
                '🔮', '🧪', '📜', '🦉', '🐍', '✨',
                '🕯️', '🗝️', '🐸', '🕷️', '🌙', '🌟'
            ]; // 12 farklı kart tipi

            /**
             * Kartları oluşturur ve oyun tahtasına yerleştirir.
             */
            function createBoard() {
                gameBoard.innerHTML = ''; // Tahtayı temizle
                cards = []; // Kart dizisini sıfırla
                flippedCards = [];
                matchedPairs = 0;
                totalMoves = 0;
                movesDisplay.textContent = totalMoves;
                timeLeft = 90;
                timerDisplay.textContent = timeLeft;

                // Kart çiftlerini oluştur (her emojiden iki tane)
                let gameCards = [...cardEmojis.slice(0, 6), ...cardEmojis.slice(0, 6)]; // 6 çift = 12 kart
                shuffle(gameCards); // Kartları karıştır

                gameCards.forEach((emoji, index) => {
                    const cardContainer = document.createElement('div');
                    cardContainer.classList.add('card-container', 'relative', 'w-full', 'h-24', 'sm:h-28', 'md:h-32', 'rounded-xl', 'cursor-pointer');
                    cardContainer.dataset.cardIndex = index;
                    cardContainer.dataset.emoji = emoji;

                    const cardInner = document.createElement('div');
                    cardInner.classList.add('card-inner');

                    const cardFront = document.createElement('div');
                    cardFront.classList.add('card-front', 'bg-gray-700', 'hover:bg-gray-600', 'transition', 'duration-200');
                    // Add Slytherin-like snake icon to the front of the card
                    cardFront.innerHTML = `
                        <svg class="w-16 h-16 sm:w-20 sm:h-20 text-green-500 opacity-70" fill="currentColor" viewBox="0 0 24 24">
                            <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 15h2v-2h-2v2zm0-4h2V7h-2v6z"/>
                            <path d="M12 17c.55 0 1-.45 1-1s-.45-1-1-1-1 .45-1 1 .45 1 1 1zm0-4c.55 0 1-.45 1-1s-.45-1-1-1-1 .45-1 1 .45 1 1 1zM11 7h2v2h-2z"/>
                            <path d="M14.07 7.82C13.88 7.37 13.56 7 13.2 6.64c-.66-.66-1.55-1.04-2.52-1.04-1.95 0-3.52 1.57-3.52 3.52 0 .97.38 1.86 1.04 2.52.36.36.73.68 1.18.87.49.2 1.01.3 1.54.3s1.05-.1 1.54-.3c.45-.19.82-.51 1.18-.87.66-.66 1.04-1.55 1.04-2.52 0-.97-.38-1.86-1.04-2.52zM12 16c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1zm0-4c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1zm0-4c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1z"/>
                            <path fill="#5cb85c" d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 15h2v-2h-2v2zm0-4h2V7h-2v6z"/>
                            <path fill="#2c3e50" d="M12 16c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1zm0-4c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1zm0-4c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1z"/>
                            <!-- Simplified snake shape for the back of the card -->
                            <path d="M12 4.5c-2.48 0-4.5 2.02-4.5 4.5 0 1.24.5 2.37 1.3 3.2L12 16l5.2-3.8c.8-.83 1.3-1.96 1.3-3.2 0-2.48-2.02-4.5-4.5-4.5z" fill="#4CAF50"/>
                            <path d="M12 17c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1zm0-4c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1zm0-4c-.55 0-1 .45-1 1s.45 1 1 1 1-.45 1-1-.45-1-1-1z"/>
                        </svg>
                    `;

                    const cardBack = document.createElement('div');
                    cardBack.classList.add('card-back', 'bg-gray-600');
                    const span = document.createElement('span');
                    span.classList.add('text-5xl');
                    span.textContent = emoji;
                    cardBack.appendChild(span);

                    cardInner.appendChild(cardFront);
                    cardInner.appendChild(cardBack);
                    cardContainer.appendChild(cardInner);
                    gameBoard.appendChild(cardContainer);
                    cards.push(cardContainer);

                    cardContainer.addEventListener('click', flipCard);
                });
            }

            /**
             * Kartları karıştırır (Fisher-Yates algoritması).
             * @param {Array} array - Karıştırılacak dizi.
             */
            function shuffle(array) {
                for (let i = array.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [array[i], array[j]] = [array[j], array[i]];
                }
            }

            /**
             * Kartı çevirir ve eşleşme kontrolü yapar.
             * @param {Event} event - Tıklama olayı.
             */
            function flipCard(event) {
                if (!gameActive || lockBoard) return; // Oyun aktif değilse veya tahta kilitliyse işlem yapma

                const clickedCard = event.currentTarget;
                if (clickedCard.classList.contains('flipped') || flippedCards.includes(clickedCard)) {
                    return; // Zaten çevrili veya aynı kart tekrar tıklanmışsa bir şey yapma
                }

                clickedCard.classList.add('flipped');
                flippedCards.push(clickedCard);

                if (flippedCards.length === 2) {
                    totalMoves++; // Hamle sayısını artır
                    movesDisplay.textContent = totalMoves; // Hamle sayısını güncelle
                    lockBoard = true; // Tahtayı kilitle
                    checkForMatch();
                }
            }

            /**
             * Çevrilmiş iki kartın eşleşip eşleşmediğini kontrol eder.
             */
            function checkForMatch() {
                const [firstCard, secondCard] = flippedCards;
                const isMatch = firstCard.dataset.emoji === secondCard.dataset.emoji;

                if (isMatch) {
                    disableCards(); // Kartları pasif hale getir
                } else {
                    unflipCards(); // Kartları geri çevir
                }
            }

            /**
             * Eşleşen kartları pasif hale getirir (tıklanamaz yapar).
             */
            function disableCards() {
                flippedCards.forEach(card => {
                    card.removeEventListener('click', flipCard);
                    card.classList.add('matched'); // Eşleşen kartlara özel bir sınıf ekle
                });
                matchedPairs++; // Eşleşen çift sayısını artır
                resetBoard(); // Kart seçimi durumunu sıfırla

                if (matchedPairs === cardEmojis.slice(0, 6).length) {
                    endGame(true); // Tüm çiftler bulunduğunda oyunu bitir
                }
            }

            /**
             * Eşleşmeyen kartları geri çevirir.
             */
            function unflipCards() {
                setTimeout(() => {
                    flippedCards.forEach(card => card.classList.remove('flipped'));
                    resetBoard(); // Kart seçimi durumunu sıfırla
                }, 1000); // 1 saniye sonra geri çevir
            }

            /**
             * Çevrilmiş kartları ve tahta kilidini sıfırlar.
             */
            function resetBoard() {
                [flippedCards, lockBoard] = [[], false];
            }

            /**
             * Zamanlayıcıyı her saniye günceller.
             * Süre bittiğinde oyunu sonlandırır.
             */
            function updateTimer() {
                timeLeft--; // Süreyi azalt
                timerDisplay.textContent = timeLeft; // Süreyi güncelle

                if (timeLeft <= 0) {
                    endGame(false); // Süre bittiğinde oyunu bitir (kayıp durumu)
                }
            }

            /**
             * Oyunu başlatır veya yeniden başlatır.
             * Oyun değişkenlerini sıfırlar ve tahtayı yeniden oluşturur.
             */
            function startGame() {
                gameActive = true; // Oyunu aktif hale getir
                startGameBtn.textContent = 'Yeniden Başlat'; // Düğme metnini değiştir
                // Düğme stilini oyun aktifken farklı yap
                startGameBtn.classList.remove('bg-gradient-to-r');
                startGameBtn.classList.add('bg-red-700', 'hover:bg-red-600');

                createBoard(); // Yeni oyun tahtası oluştur
                clearInterval(gameInterval); // Mevcut zamanlayıcıyı temizle
                gameInterval = setInterval(updateTimer, 1000); // Yeni zamanlayıcıyı başlat (her saniye)
            }

            /**
             * Oyunu sonlandırır.
             * Zamanlayıcıyı durdurur ve sonuç mesajını gösterir.
             * @param {boolean} won - Oyuncunun kazanıp kazanmadığı.
             */
            function endGame(won) {
                gameActive = false; // Oyunu pasif hale getir
                clearInterval(gameInterval); // Zamanlayıcıyı durdur

                if (won) {
                    showMessage('Tebrikler Büyücü!', `Tüm çiftleri ${totalMoves} hamlede ve ${90 - timeLeft} saniyede eşleştirdin!`);
                } else {
                    showMessage('Oyun Bitti!', `Süre doldu! Toplamda ${totalMoves} hamle yaptın.`);
                }
                startGameBtn.textContent = 'Oyunu Başlat'; // Düğme metnini sıfırla
                // Düğme stilini başlangıç durumuna döndür
                startGameBtn.classList.remove('bg-red-700', 'hover:bg-red-600');
                startGameBtn.classList.add('bg-gradient-to-r', 'from-green-500', 'to-green-700');
            }

            /**
             * Özel bir mesaj kutusu gösterir.
             * @param {string} title - Mesaj kutusunun başlığı.
             * @param {string} text - Mesaj kutusunun içeriği.
             */
            function showMessage(title, text) {
                messageTitle.textContent = title;
                messageText.textContent = text;
                messageBox.classList.remove('hidden'); // Mesaj kutusunu görünür yap
            }

            /**
             * Özel mesaj kutusunu gizler.
             */
            function hideMessage() {
                messageBox.classList.add('hidden'); // Mesaj kutusunu gizle
            }

            // Olay Dinleyicileri
            startGameBtn.addEventListener('click', startGame); // Oyunu başlat düğmesi tıklama olayı
            closeMessageBtn.addEventListener('click', hideMessage); // Mesaj kutusu kapatma düğmesi

            // Başlangıçta tahtayı oluştur
            createBoard();
        });
    </script>
</body>
</html>
