# zizonehanbin
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>문학 개념 타자 연습</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700&display=swap');
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #f3f4f6;
        }
        .correct { color: #10b981; }
        .incorrect { color: #ef4444; }
        .cursor { border-right: 2px solid #3b82f6; animation: blink 1s infinite; }
        @keyframes blink { 50% { opacity: 0; } }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen">

    <div id="app" class="w-full max-w-2xl p-8 bg-white rounded-2xl shadow-xl border border-gray-100">
        <!-- Start Screen -->
        <div id="start-screen" class="text-center">
            <h1 class="text-3xl font-bold text-gray-800 mb-4">📖 문학 1-2단원 타자 연습</h1>
            <p class="text-gray-600 mb-8">핵심 개념 문장을 따라 치며 점수를 획득하세요!</p>
            <button id="start-btn" class="px-8 py-4 bg-blue-600 hover:bg-blue-700 text-white font-bold rounded-full transition-all transform hover:scale-105 shadow-lg">
                게임 시작하기
            </button>
        </div>

        <!-- Game Screen -->
        <div id="game-screen" class="hidden">
            <div class="flex justify-between items-center mb-6">
                <div class="text-lg font-semibold text-gray-700">
                    점수: <span id="score" class="text-blue-600">0</span>
                </div>
                <div class="text-lg font-semibold text-gray-700">
                    남은 시간: <span id="timer" class="text-red-500">60</span>초
                </div>
            </div>

            <div class="bg-gray-50 p-6 rounded-xl mb-6 border border-gray-200">
                <p id="target-text" class="text-xl text-gray-400 mb-2 leading-relaxed break-keep select-none"></p>
                <div id="visual-feedback" class="text-xl font-bold leading-relaxed break-keep"></div>
            </div>

            <input type="text" id="input-field" 
                class="w-full p-4 text-lg border-2 border-blue-300 rounded-lg focus:outline-none focus:border-blue-500 transition-colors"
                placeholder="위 문장을 똑같이 입력하세요..." autocomplete="off">
            
            <div class="mt-4 text-sm text-gray-500 text-center">
                Enter를 누르면 다음 문장으로 넘어갑니다.
            </div>
        </div>

        <!-- Result Screen -->
        <div id="result-screen" class="hidden text-center">
            <h2 class="text-3xl font-bold text-gray-800 mb-2">연습 종료!</h2>
            <p class="text-xl text-gray-600 mb-6">당신의 최종 점수는 <span id="final-score" class="font-bold text-blue-600">0</span>점입니다.</p>
            <button id="restart-btn" class="px-6 py-3 bg-gray-800 hover:bg-black text-white font-semibold rounded-lg transition-all">
                다시 도전하기
            </button>
        </div>
    </div>

    <script>
        const sentences = [
            "문학은 언어 형식을 통해 심미적 가치를 구현하는 예술이다.",
            "작가는 세계에 대한 자신의 인식과 가치관을 작품에 투영한다.",
            "독자는 능동적으로 작품의 의미를 재구성하며 감상해야 한다.",
            "반영론적 관점은 작품과 시대 상황의 관계를 중시한다.",
            "상호텍스트성은 작품이 다른 텍스트와 맺는 유기적 관련성이다.",
            "표현론적 관점은 작가의 창작 의도와 전기를 바탕으로 분석한다.",
            "내재적 관점은 작품 내부의 형식과 언어 요소에 주목한다.",
            "심미적 체험은 즐거움, 감동, 깨달음을 모두 포함하는 개념이다.",
            "문학은 타인의 삶을 간접 체험하게 하여 삶의 지평을 넓혀준다.",
            "효용론적 관점은 작품이 독자에게 주는 영향과 가치를 중시한다."
        ];

        let currentSentence = "";
        let score = 0;
        let timeLeft = 60;
        let timerId = null;

        const startScreen = document.getElementById('start-screen');
        const gameScreen = document.getElementById('game-screen');
        const resultScreen = document.getElementById('result-screen');
        const targetTextDisplay = document.getElementById('target-text');
        const visualFeedback = document.getElementById('visual-feedback');
        const inputField = document.getElementById('input-field');
        const scoreDisplay = document.getElementById('score');
        const timerDisplay = document.getElementById('timer');
        const finalScoreDisplay = document.getElementById('final-score');

        function startGame() {
            score = 0;
            timeLeft = 60;
            scoreDisplay.textContent = score;
            timerDisplay.textContent = timeLeft;
            
            startScreen.classList.add('hidden');
            resultScreen.classList.add('hidden');
            gameScreen.classList.remove('hidden');
            
            nextSentence();
            inputField.focus();
            
            timerId = setInterval(() => {
                timeLeft--;
                timerDisplay.textContent = timeLeft;
                if (timeLeft <= 0) endGame();
            }, 1000);
        }

        function nextSentence() {
            const randomIndex = Math.floor(Math.random() * sentences.length);
            currentSentence = sentences[randomIndex];
            targetTextDisplay.textContent = currentSentence;
            visualFeedback.innerHTML = "";
            inputField.value = "";
        }

        function updateFeedback() {
            const val = inputField.value;
            let html = "";
            for (let i = 0; i < currentSentence.length; i++) {
                if (i < val.length) {
                    if (val[i] === currentSentence[i]) {
                        html += `<span class="correct">${currentSentence[i]}</span>`;
                    } else {
                        html += `<span class="incorrect">${currentSentence[i]}</span>`;
                    }
                } else if (i === val.length) {
                    html += `<span class="cursor">${currentSentence[i]}</span>`;
                } else {
                    html += `<span>${currentSentence[i]}</span>`;
                }
            }
            visualFeedback.innerHTML = html;

            if (val === currentSentence) {
                score += 10;
                scoreDisplay.textContent = score;
                setTimeout(nextSentence, 100);
            }
        }

        function endGame() {
            clearInterval(timerId);
            gameScreen.classList.add('hidden');
            resultScreen.classList.remove('hidden');
            finalScoreDisplay.textContent = score;
        }

        inputField.addEventListener('input', updateFeedback);
        inputField.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                // 정확하지 않아도 넘어가고 싶을 때를 대비한 기능 (필요시 점수 차감 등 로직 추가 가능)
                if (inputField.value !== currentSentence) {
                    nextSentence();
                }
            }
        });

        document.getElementById('start-btn').addEventListener('click', startGame);
        document.getElementById('restart-btn').addEventListener('click', startGame);

        window.onload = () => {
            // 초기 세팅
        };
    </script>
</body>
</html>
