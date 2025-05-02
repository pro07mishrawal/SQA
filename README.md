<!DOCTYPE html>
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
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }

    .container {
      text-align: center;
      max-width: 600px;
      padding: 20px;
      background-color: #00587a;
      border-radius: 10px;
    }

    h1 {
      margin-bottom: 20px;
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
    }

    #result {
      margin-top: 20px;
      font-size: 1.1em;
    }

    #prize {
      margin-bottom: 10px;
      font-size: 1.1em;
      font-weight: bold;
    }

    #category {
      margin-bottom: 10px;
      font-style: italic;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>🪙 Kaun Banega Crorepati 🪙</h1>
    <div id="prize">Prize: ₹0</div>
    <div id="category">Category: General Knowledge</div>
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
  </div>

  <script>
    const questions = [
      // General Knowledge
      { question: "What is the capital of Nepal?", category: "General Knowledge", options: ["Pokhara", "Biratnagar", "Kathmandu", "Lalitpur"], answer: 2 },
      { question: "Which planet is known as the Red Planet?", category: "General Knowledge", options: ["Earth", "Venus", "Mars", "Jupiter"], answer: 2 },
      
      // Math
      { question: "What is 12 x 8?", category: "Math", options: ["96", "84", "108", "112"], answer: 0 },
      { question: "What is the square root of 144?", category: "Math", options: ["10", "11", "12", "13"], answer: 2 },

      // English
      { question: "Choose the correct spelling:", category: "English", options: ["Recieve", "Receive", "Receeve", "Receiv"], answer: 1 },
      { question: "Synonym of 'Happy'?", category: "English", options: ["Sad", "Angry", "Joyful", "Upset"], answer: 2 },

      // Nepali
      { question: "Who wrote 'Muna Madan'?", category: "Nepali", options: ["Laxmi Prasad Devkota", "Bhanubhakta", "Parijat", "Lekhnath Paudyal"], answer: 0 },
      { question: "Nepali New Year falls on?", category: "Nepali", options: ["Baisakh 1", "Ashad 15", "Kartik 5", "Chaitra 30"], answer: 0 },

      // Chemistry
      { question: "What is the chemical symbol of water?", category: "Chemistry", options: ["H2O", "O2", "CO2", "HO2"], answer: 0 },
      { question: "Atomic number of Oxygen?", category: "Chemistry", options: ["6", "7", "8", "9"], answer: 2 },

      // Physics
      { question: "What is the speed of light?", category: "Physics", options: ["299,792,458 m/s", "300,000,000 m/s", "150,000,000 m/s", "3,000 m/s"], answer: 0
