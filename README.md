<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>KBC Voice Quiz</title>
  <style>
    /* Use your existing CSS styles here... */
    body { font-family: sans-serif; background: #0b1f3a; color: #fff; padding: 20px; }
    .option-btn { margin: 10px 0; padding: 10px; font-size: 1.1em; cursor: pointer; }
    .correct { background: green; }
    .incorrect { background: red; }
    .active-level { background: gold; color: black; }
  </style>
</head>
<body>
  <h1>🎤 KBC Voice Quiz Game</h1>
  <label for="questionSelect">Select Question:</label>
  <select id="questionSelect"></select>

  <div id="question"></div>
  <div id="options"></div>
  <p id="timer"></p>
  <p id="result"></p>
  <p id="lock-msg"></p>
  <div id="superSawaal" style="display:none; color: orange; font-size: 1.2em;">🔥 Super Sawaal Activated!</div>

  <script>
    const questions = [
      { question: "What is the capital of Nepal?", options: ["Pokhara", "Biratnagar", "Kathmandu", "Lalitpur"], answer: 2 },
      { question: "Which planet is known as the Red Planet?", options: ["Earth", "Venus", "Mars", "Jupiter"], answer: 2 },
      { question: "Super Sawaal: Who developed the theory of relativity?", options: ["Newton", "Tesla", "Einstein", "Edison"], answer: 2, super: true },
    ];

    const prizeLevels = [1000, 2000, 50000]; // Shortened for demo
    let currentQuestion = 0;
    let timeLeft = 20;
    let timer;

    const questionEl = document.getElementById("question");
    const optionsEl = document.getElementById("options");
    const timerEl = document.getElementById("timer");
    const resultEl = document.getElementById("result");
    const lockMsgEl = document.getElementById("lock-msg");
    const questionSelect = document.getElementById("questionSelect");
    const superSawaalEl = document.getElementById("superSawaal");

    // Populate question dropdown
    questions.forEach((q, idx) => {
      const opt = document.createElement("option");
      opt.value = idx;
      opt.textContent = `Q${idx + 1}${q.super ? " (Super Sawaal)" : ""}`;
      questionSelect.appendChild(opt);
    });

    questionSelect.onchange = () => {
      currentQuestion = parseInt(questionSelect.value);
      loadQuestion();
    };

    function startTimer() {
      timeLeft = 20;
      timerEl.textContent = `⏳ Time left: ${timeLeft}s`;
      clearInterval(timer);
      timer = setInterval(() => {
        timeLeft--;
        timerEl.textContent = `⏳ Time left: ${timeLeft}s`;
        if (timeLeft <= 0) {
          clearInterval(timer);
          lockAnswer(-1);
        }
      }, 1000);
    }

    function loadQuestion() {
      const q = questions[currentQuestion];
      questionEl.textContent = q.question;
      optionsEl.innerHTML = "";
      lockMsgEl.textContent = "";
      resultEl.textContent = "";
      superSawaalEl.style.display = q.super ? "block" : "none";

      q.options.forEach((opt, i) => {
        const btn = document.createElement("button");
        btn.className = "option-btn";
        btn.textContent = `${String.fromCharCode(65 + i)}. ${opt}`;
        btn.onclick = () => lockAnswer(i);
        optionsEl.appendChild(btn);
      });

      startTimer();
      startListening();
    }

    function lockAnswer(index) {
      clearInterval(timer);
      const correct = questions[currentQuestion].answer;
      const buttons = document.querySelectorAll(".option-btn");

      buttons.forEach((btn, i) => {
        btn.disabled = true;
        if (i === correct) btn.classList.add("correct");
        if (i === index && index !== correct) btn.classList.add("incorrect");
      });

      if (index === correct) {
        resultEl.textContent = `✅ Correct! You won ₹${prizeLevels[currentQuestion]}`;
      } else if (index === -1) {
        resultEl.textContent = "⏰ Time's up!";
      } else {
        resultEl.textContent = `❌ Wrong! Correct: ${questions[currentQuestion].options[correct]}`;
      }

      stopListening();
    }

    // 🎤 Voice Command (Speech Recognition)
    let recognition;
    function startListening() {
      const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
      if (!SpeechRecognition) return alert("Voice recognition not supported!");

      recognition = new SpeechRecognition();
      recognition.continuous = true;
      recognition.lang = 'en-US';

      recognition.onresult = (event) => {
        const transcript = event.results[event.results.length - 1][0].transcript.trim().toLowerCase();
        console.log("Heard:", transcript);

        if (transcript.includes("option a")) lockAnswer(0);
        else if (transcript.includes("option b")) lockAnswer(1);
        else if (transcript.includes("option c")) lockAnswer(2);
        else if (transcript.includes("option d")) lockAnswer(3);
        else if (transcript.includes("lock a")) lockAnswer(0);
        else if (transcript.includes("lock b")) lockAnswer(1);
        else if (transcript.includes("lock c")) lockAnswer(2);
        else if (transcript.includes("lock d")) lockAnswer(3);
      };

      recognition.start();
    }

    function stopListening() {
      if (recognition) recognition.stop();
    }

    loadQuestion();
  </script>
</body>
</html>
