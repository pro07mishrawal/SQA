<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>KBC Voice Quiz</title>
  <style>
    /* [Same CSS as before — Keep it unchanged] */
  </style>
</head>
<body>
  <div class="game-area">
    <div class="container">
      <h1>🎤 Kaun Banega Crorepati</h1>
      <div id="timer">⏳ Time left: 20s</div>
      <div id="question">Loading question...</div>
      <div class="options" id="options"></div>
      <div id="lock-msg"></div>
      <div id="result"></div>
    </div>
    <div class="sidebar">
      <h3>🏆 Prize Levels</h3>
      <div id="prize-levels"></div>
      <div class="team">
        <p>Created by:</p>
        <p>Promish Rawal<br>Sandesh GC<br>Subodh Karki<br>Saurav Thapa<br>Aaryan Bohora</p>
      </div>
    </div>
  </div>

  <script>
    const questions = [
      { question: "What is the capital of Nepal?", options: ["Pokhara", "Biratnagar", "Kathmandu", "Lalitpur"], answer: 2 },
      { question: "Which planet is known as the Red Planet?", options: ["Earth", "Venus", "Mars", "Jupiter"], answer: 2 },
      { question: "What is 12 x 8?", options: ["96", "84", "108", "112"], answer: 0 },
    ];

    const superSawaal = {
      question: "Who is known as the Missile Man of India?",
      options: ["A. R. Rahman", "Dr. A. P. J. Abdul Kalam", "C. V. Raman", "Narendra Modi"],
      answer: 1,
      prize: 10000000
    };

    const prizeLevels = [
      2500000, 1250000, 640000, 320000, 160000,
      80000, 40000, 20000, 10000, 5000,
      2000, 1000
    ];

    let currentQuestion = 0;
    let currentPrize = 0;
    let timeLeft = 20;
    let timer;
    let isSuperSawaal = false;

    const questionEl = document.getElementById("question");
    const optionsEl = document.getElementById("options");
    const resultEl = document.getElementById("result");
    const lockMsgEl = document.getElementById("lock-msg");
    const timerEl = document.getElementById("timer");
    const prizeLevelsEl = document.getElementById("prize-levels");

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
      let q = isSuperSawaal ? superSawaal : questions[currentQuestion];
      questionEl.textContent = q.question;
      optionsEl.innerHTML = "";
      lockMsgEl.textContent = "";
      resultEl.textContent = "";

      q.options.forEach((opt, i) => {
        const btn = document.createElement("button");
        btn.className = "option-btn";
        btn.textContent = opt;
        btn.onclick = () => handleSelection(btn, i);
        optionsEl.appendChild(btn);
      });

      updatePrizeLevels();
      startTimer();
      startVoiceRecognition();
    }

    function handleSelection(button, index) {
      disableButtons();
      lockMsgEl.textContent = `🔒 Computer ji, option ${String.fromCharCode(65 + index)} ko lock kiya jaye...`;
      setTimeout(() => {
        lockAnswer(index);
      }, 1500);
    }

    function lockAnswer(index) {
      clearInterval(timer);
      const q = isSuperSawaal ? superSawaal : questions[currentQuestion];
      const correct = q.answer;
      const buttons = document.querySelectorAll(".option-btn");

      buttons.forEach((btn, i) => {
        if (i === correct) btn.classList.add("correct");
        if (i === index && index !== correct) btn.classList.add("incorrect");
      });

      if (index === correct) {
        currentPrize = isSuperSawaal ? superSawaal.prize : prizeLevels[11 - currentQuestion];
        resultEl.textContent = `✅ Correct! You won ₹${currentPrize}`;

        if (!isSuperSawaal) {
          currentQuestion++;
          if (currentQuestion < questions.length) {
            setTimeout(loadQuestion, 3000);
          } else {
            isSuperSawaal = true;
            setTimeout(loadQuestion, 3000);
          }
        } else {
          resultEl.innerHTML = `🏆 Super Sawaal correct! You are a Super Crorepati!<br>Total: ₹${currentPrize}`;
        }
      } else {
        resultEl.innerHTML = index === -1
          ? "⏰ Time's up!"
          : `❌ Wrong answer! Correct: ${q.options[correct]}`;
      }
    }

    function disableButtons() {
      document.querySelectorAll(".option-btn").forEach(btn => btn.disabled = true);
    }

    function updatePrizeLevels() {
      prizeLevelsEl.innerHTML = "";
      prizeLevels.forEach((amt, i) => {
        const index = 11 - i;
        const div = document.createElement("div");
        div.className = "level" + (index === currentQuestion ? " active-level" : "");
        div.textContent = `Level ${index + 1}: ₹${amt}`;
        prizeLevelsEl.appendChild(div);
      });

      if (isSuperSawaal) {
        const div = document.createElement("div");
        div.className = "level active-level";
        div.textContent = `💎 Super Sawaal: ₹${superSawaal.prize}`;
        prizeLevelsEl.prepend(div);
      }
    }

    // 🗣️ Voice Recognition using Web Speech API
    function startVoiceRecognition() {
      if (!('webkitSpeechRecognition' in window)) return;

      const recognition = new webkitSpeechRecognition();
      recognition.lang = 'en-US';
      recognition.continuous = false;
      recognition.interimResults = false;

      recognition.start();

      recognition.onresult = (event) => {
        const transcript = event.results[0][0].transcript.toLowerCase();
        const match = transcript.match(/option\s([abcd])/i);
        if (match) {
          const index = { a: 0, b: 1, c: 2, d: 3 }[match[1]];
          handleSelection(document.querySelectorAll(".option-btn")[index], index);
        }
      };

      recognition.onerror = () => {
        recognition.stop();
      };
    }

    loadQuestion();
  </script>
</body>
</html>
