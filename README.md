<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Kaun Banega Crorepati</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #0f3057;
      color: #ffffff;
      display: flex;
      justify-content: space-around;
      align-items: flex-start;
      height: 100vh;
      margin: 0;
      padding: 20px;
    }

    .container {
      max-width: 600px;
      background-color: #00587a;
      border-radius: 10px;
      padding: 20px;
      flex: 1;
    }

    .sidebar {
      background-color: #003f5c;
      padding: 20px;
      border-radius: 10px;
      margin-left: 20px;
      min-width: 250px;
    }

    h1 {
      text-align: center;
    }

    #question {
      font-size: 1.2em;
      margin-bottom: 20px;
    }

    .options {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
    }

    .option-btn {
      padding: 10px;
      background-color: #00adb5;
      border: none;
      border-radius: 5px;
      color: white;
      font-weight: bold;
      cursor: pointer;
      transition: background-color 0.3s;
    }

    .option-btn.correct {
      background-color: #28a745 !important;
    }

    .option-btn.incorrect {
      background-color: #dc3545 !important;
    }

    .option-btn:disabled {
      cursor: not-allowed;
    }

    .lifeline-box {
      margin-top: 20px;
      text-align: center;
    }

    #result, #levelComplete {
      margin-top: 20px;
      font-size: 1.1em;
      text-align: center;
    }

    #timer {
      font-size: 1.2em;
      margin-bottom: 10px;
      text-align: center;
    }

    .level {
      padding: 5px 0;
      border-bottom: 1px solid #ffffff22;
    }

    .active-level {
      color: gold;
      font-weight: bold;
    }

    .team {
      margin-top: 20px;
      font-size: 0.9em;
      color: #aaa;
      text-align: center;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>🪙 Kaun Banega Crorepati 🪙</h1>
    <div id="prize">Prize: ₹0</div>
    <div id="category">Category: General Knowledge</div>
    <div id="timer">⏳ Time left: 20s</div>
    <div id="question-box">
      <h2 id="question">Loading...</h2>
      <div class="options">
        <button class="option-btn" onclick="selectAnswer(0)">A</button>
        <button class="option-btn" onclick="selectAnswer(1)">B</button>
        <button class="option-btn" onclick="selectAnswer(2)">C</button>
        <button class="option-btn" onclick="selectAnswer(3)">D</button>
      </div>
    </div>
    <div class="lifeline-box">
      <button id="lifeline-btn" onclick="useLifeline()">Use 50:50 Lifeline</button>
      <button onclick="quitGame()">Quit</button>
    </div>
    <div id="result"></div>
    <div id="levelComplete"></div>
  </div>

  <div class="sidebar">
    <h3>🏆 Prize Levels</h3>
    <div id="prize-levels"></div>
    <div class="team">
      <p>Created by:</p>
      <p>Promish Rawal<br>Sandesh GC<br>Subodh Karki<br>Saurav Thapa<br>Aaryan Bohora</p>
    </div>
  </div>

 <script>
  const questions = [
    { question: "What is the capital of Nepal?", category: "General Knowledge", options: ["Pokhara", "Biratnagar", "Kathmandu", "Lalitpur"], answer: 2 },
    { question: "Which planet is known as the Red Planet?", category: "General Knowledge", options: ["Earth", "Venus", "Mars", "Jupiter"], answer: 2 },
    { question: "What is 12 x 8?", category: "Math", options: ["96", "84", "108", "112"], answer: 0 },
    { question: "What is the square root of 144?", category: "Math", options: ["10", "11", "12", "13"], answer: 2 },
    { question: "Choose the correct spelling:", category: "English", options: ["Recieve", "Receive", "Receeve", "Receiv"], answer: 1 },
    { question: "Synonym of 'Happy'?", category: "English", options: ["Sad", "Angry", "Joyful", "Upset"], answer: 2 },
    { question: "Who wrote 'Muna Madan'?", category: "Nepali", options: ["Laxmi Prasad Devkota", "Bhanubhakta", "Parijat", "Lekhnath Paudyal"], answer: 0 },
    { question: "Nepali New Year falls on?", category: "Nepali", options: ["Baisakh 1", "Ashad 15", "Kartik 5", "Chaitra 30"], answer: 0 },
    { question: "What is the chemical symbol of water?", category: "Chemistry", options: ["H2O", "O2", "CO2", "HO2"], answer: 0 },
    { question: "Atomic number of Oxygen?", category: "Chemistry", options: ["6", "7", "8", "9"], answer: 2 },
    { question: "What is the speed of light?", category: "Physics", options: ["299,792,458 m/s", "300,000,000 m/s", "150,000,000 m/s", "3,000 m/s"], answer: 0 },
    { question: "Who discovered gravity?", category: "Physics", options: ["Einstein", "Newton", "Tesla", "Faraday"], answer: 1 }
  ];

  const prizeLevels = [1000, 5000, 20000, 80000, 320000, 1250000];

  let currentQuestion = 0;
  let currentLevel = 0;
  let currentPrize = 0;
  let lifelineUsed = false;
  let timerInterval;
  let timeLeft = 20;

  const questionEl = document.getElementById("question");
  const prizeEl = document.getElementById("prize");
  const resultEl = document.getElementById("result");
  const optionBtns = document.querySelectorAll(".option-btn");
  const lifelineBtn = document.getElementById("lifeline-btn");
  const categoryEl = document.getElementById("category");
  const timerEl = document.getElementById("timer");
  const levelCompleteEl = document.getElementById("levelComplete");
  const prizeLevelsEl = document.getElementById("prize-levels");

  function startTimer() {
    timeLeft = 20;
    timerEl.textContent = `⏳ Time left: ${timeLeft}s`;
    clearInterval(timerInterval);
    timerInterval = setInterval(() => {
      timeLeft--;
      timerEl.textContent = `⏳ Time left: ${timeLeft}s`;
      if (timeLeft <= 0) {
        clearInterval(timerInterval);
        selectAnswer(-1);
      }
    }, 1000);
  }

  function loadQuestion() {
    const q = questions[currentQuestion];
    questionEl.textContent = q.question;
    categoryEl.textContent = `Category: ${q.category}`;
    optionBtns.forEach((btn, index) => {
      btn.textContent = `${String.fromCharCode(65 + index)}. ${q.options[index]}`;
      btn.disabled = false;
      btn.style.visibility = "visible";
      btn.classList.remove("correct", "incorrect");
    });
    resultEl.textContent = "";
    levelCompleteEl.textContent = "";
    prizeEl.textContent = `Prize: ₹${currentPrize}`;
    updatePrizeLevels();
    startTimer();
  }

  function selectAnswer(index) {
    clearInterval(timerInterval);
    const correct = questions[currentQuestion].answer;
    optionBtns.forEach((btn, idx) => {
      btn.disabled = true;
      if (idx === correct) btn.classList.add("correct");
      if (idx === index && idx !== correct) btn.classList.add("incorrect");
    });

    if (index === correct) {
      resultEl.textContent = `✅ Correct!`;
      currentQuestion++;

      // Level completed every 2 questions
      if (currentQuestion % 2 === 0) {
        currentPrize = prizeLevels[currentLevel];
        levelCompleteEl.textContent = `🎉 Level ${currentLevel + 1} Complete! You won ₹${currentPrize}`;
        currentLevel++;
      }

      if (currentQuestion < questions.length) {
        setTimeout(loadQuestion, 2000);
      } else {
        resultEl.textContent = `🏆 Congratulations! You completed all levels! Total: ₹${currentPrize}`;
      }
    } else {
      if (index === -1) {
        resultEl.textContent = "⏰ Time's up!";
      } else {
        resultEl.textContent = `❌ Wrong! Correct answer: ${questions[currentQuestion].options[correct]}`;
      }
    }
  }

  function useLifeline() {
    if (lifelineUsed) return;
    lifelineUsed = true;
    lifelineBtn.disabled = true;
    const correct = questions[currentQuestion].answer;
    let hidden = 0;
    while (hidden < 2) {
      const rand = Math.floor(Math.random() * 4);
      if (rand !== correct && optionBtns[rand].style.visibility !== "hidden") {
        optionBtns[rand].style.visibility = "hidden";
        hidden++;
      }
    }
  }

  function quitGame() {
    clearInterval(timerInterval);
    resultEl.textContent = `🏁 You quit! Total winnings: ₹${currentPrize}`;
    optionBtns.forEach(btn => btn.disabled = true);
    lifelineBtn.disabled = true;
    timerEl.textContent = "";
  }

  function updatePrizeLevels() {
    prizeLevelsEl.innerHTML = "";
    prizeLevels.forEach((amt, idx) => {
      const div = document.createElement("div");
      div.className = "level" + (idx === currentLevel ? " active-level" : "");
      div.textContent = `Level ${idx + 1}: ₹${amt}`;
      prizeLevelsEl.appendChild(div);
    });
  }

  loadQuestion();
</script>

</body>
</html>
