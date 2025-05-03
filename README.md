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
      font-size: 1.4em;
      margin-bottom: 20px;
    }

    .options {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
    }

    .option-btn {
      padding: 15px;
      background-color: #00adb5;
      border: none;
      border-radius: 5px;
      color: white;
      font-weight: bold;
      cursor: pointer;
      transition: background-color 0.3s;
      font-size: 1.1em;
    }

    .option-btn.selected {
      background-color: #ffaa00 !important;
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

    .super-sawaal {
      background-color: orange;
      padding: 2px 5px;
      border-radius: 5px;
      color: #000;
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
        <button class="option-btn" onclick="selectOption(0)">A</button>
        <button class="option-btn" onclick="selectOption(1)">B</button>
        <button class="option-btn" onclick="selectOption(2)">C</button>
        <button class="option-btn" onclick="selectOption(3)">D</button>
      </div>
      <div style="text-align:center; margin-top:10px">
        <button onclick="lockAnswer()">🔒 Lock Answer</button>
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
    const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
    recognition.continuous = true;
    recognition.interimResults = false;
    recognition.lang = 'en-US';
    recognition.start();

    recognition.onresult = function(event) {
      const transcript = event.results[event.results.length - 1][0].transcript.toLowerCase();
      if (transcript.includes("lock")) {
        lockAnswer();
      } else if (transcript.includes("pause")) {
        clearInterval(timerInterval);
      } else if (transcript.includes("resume")) {
        startTimer();
      } else {
        ['a','b','c','d'].forEach((val, idx) => {
          if (transcript.includes(val)) {
            selectOption(idx);
          }
        });
      }
    };

    const questions = [
      { question: "What is the capital of Nepal?", category: "General Knowledge", options: ["Pokhara", "Biratnagar", "Kathmandu", "Lalitpur"], answer: 2 },
      // ... other questions
    ];

    const prizeLevels = [1000, 2000, 5000, 10000, 20000, 40000, 80000, 160000, 320000, 640000, 1250000, 2500000];

    let currentQuestion = 0;
    let selectedOption = -1;
    let currentPrize = 0;
    let lifelineUsed = false;
    let timerInterval;
    let timeLeft = 20;

    const optionBtns = document.querySelectorAll(".option-btn");

    function startTimer() {
      timeLeft = 20;
      document.getElementById("timer").textContent = `⏳ Time left: ${timeLeft}s`;
      clearInterval(timerInterval);
      timerInterval = setInterval(() => {
        timeLeft--;
        document.getElementById("timer").textContent = `⏳ Time left: ${timeLeft}s`;
        if (timeLeft <= 0) {
          clearInterval(timerInterval);
          lockAnswer();
        }
      }, 1000);
    }

    function loadQuestion() {
      selectedOption = -1;
      const q = questions[currentQuestion];
      document.getElementById("question").textContent = q.question;
      document.getElementById("category").textContent = `Category: ${q.category}`;
      optionBtns.forEach((btn, index) => {
        btn.textContent = `${String.fromCharCode(65 + index)}. ${q.options[index]}`;
        btn.disabled = false;
        btn.style.visibility = "visible";
        btn.classList.remove("correct", "incorrect", "selected");
      });
      document.getElementById("result").textContent = "";
      document.getElementById("levelComplete").textContent = "";
      document.getElementById("prize").textContent = `Prize: ₹${currentPrize}`;
      updatePrizeLevels();
      startTimer();
    }

    function selectOption(index) {
      selectedOption = index;
      optionBtns.forEach(btn => btn.classList.remove("selected"));
      optionBtns[index].classList.add("selected");
    }

    function lockAnswer() {
      if (selectedOption === -1) return;
      clearInterval(timerInterval);
      const correct = questions[currentQuestion].answer;
      optionBtns.forEach((btn, idx) => {
        btn.disabled = true;
        if (idx === correct) btn.classList.add("correct");
        if (idx === selectedOption && idx !== correct) btn.classList.add("incorrect");
      });
      if (selectedOption === correct) {
        currentPrize = prizeLevels[currentQuestion];
        document.getElementById("result").textContent = `✅ Correct! You won ₹${currentPrize}`;
        document.getElementById("levelComplete").textContent = `🎉 Level ${currentQuestion + 1} Complete!`;
        currentQuestion++;
        if (currentQuestion < questions.length) {
          setTimeout(loadQuestion, 2000);
        } else {
          document.getElementById("result").textContent = `🏆 Congratulations! You are a Crorepati! Total: ₹${currentPrize}`;
        }
      } else {
        document.getElementById("result").textContent = `❌ Wrong! Correct answer: ${questions[currentQuestion].options[correct]}`;
      }
    }

    function useLifeline() {
      if (lifelineUsed) return;
      lifelineUsed = true;
      document.getElementById("lifeline-btn").disabled = true;
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
      document.getElementById("result").textContent = `🏁 You quit! Total winnings: ₹${currentPrize}`;
      optionBtns.forEach(btn => btn.disabled = true);
      document.getElementById("lifeline-btn").disabled = true;
      document.getElementById("timer").textContent = "";
    }

    function updatePrizeLevels() {
      const prizeLevelsEl = document.getElementById("prize-levels");
      prizeLevelsEl.innerHTML = "";
      prizeLevels.forEach((amt, idx) => {
        const div = document.createElement("div");
        let levelClass = "level";
        if (idx === currentQuestion) levelClass += " active-level";
        if (idx === 6) div.innerHTML = `<span class='super-sawaal'>Super Sawaal</span> - ₹${amt}`;
        else div.textContent = `Level ${idx + 1}: ₹${amt}`;
        div.className = levelClass;
        prizeLevelsEl.appendChild(div);
      });
    }

    loadQuestion();
  </script>
</body>
</html>
