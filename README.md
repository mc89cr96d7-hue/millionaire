<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Millionaire Trivia Quiz</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #0c1033;
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }
        .quiz-container {
            background: linear-gradient(145deg, #111646, #070a24);
            border: 2px solid #3d4a94;
            border-radius: 12px;
            padding: 30px;
            width: 100%;
            max-width: 650px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            text-align: center;
        }
        .header {
            font-size: 1.1rem;
            color: #00e5ff;
            text-transform: uppercase;
            letter-spacing: 2px;
            margin-bottom: 5px;
        }
        .prize {
            font-size: 1.8rem;
            color: #ffd700;
            font-weight: bold;
            margin-bottom: 25px;
        }
        .question-text {
            font-size: 1.3rem;
            line-height: 1.5;
            margin-bottom: 30px;
            min-height: 70px;
        }
        .options-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 15px;
            margin-bottom: 25px;
        }
        @media(min-width: 480px) {
            .options-grid { grid-template-columns: 1fr 1fr; }
        }
        .btn {
            background-color: #161d58;
            border: 1.5px solid #3d4a94;
            color: white;
            padding: 15px 20px;
            font-size: 1rem;
            border-radius: 30px;
            cursor: pointer;
            transition: all 0.2s ease;
            text-align: left;
            display: flex;
            align-items: center;
        }
        .btn:hover:not(:disabled) {
            background-color: #242f83;
            border-color: #00e5ff;
            box-shadow: 0 0 10px rgba(0,229,255,0.3);
        }
        .btn:disabled {
            cursor: not-allowed;
        }
        .btn.correct {
            background-color: #2e7d32 !important;
            border-color: #4caf50 !important;
        }
        .btn.incorrect {
            background-color: #c62828 !important;
            border-color: #f44336 !important;
        }
        .prefix {
            color: #ffd700;
            font-weight: bold;
            margin-right: 10px;
        }
        .action-space {
            margin-top: 20px;
            min-height: 50px;
        }
        .hint-btn {
            background: none;
            border: 1px dashed #00e5ff;
            color: #00e5ff;
            padding: 8px 16px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 0.9rem;
        }
        .hint-btn:hover {
            background: rgba(0, 229, 255, 0.1);
        }
        .hint-text {
            margin-top: 15px;
            font-style: italic;
            color: #b0bec5;
            font-size: 0.95rem;
        }
        .next-btn, .restart-btn {
            background: #ffd700;
            color: #0c1033;
            border: none;
            padding: 12px 30px;
            font-size: 1.1rem;
            font-weight: bold;
            border-radius: 25px;
            cursor: pointer;
        }
        .next-btn:hover, .restart-btn:hover {
            background: #ffea00;
        }
        .results-screen h2 {
            color: #ffd700;
            font-size: 2.2rem;
            margin-bottom: 10px;
        }
        .results-score {
            font-size: 1.4rem;
            margin-bottom: 30px;
        }
    </style>
</head>
<body>

<div class="quiz-container" id="quiz-card">
    <!-- Dynamic content loads here -->
</div>

<script>
    const quizData = [
        { q: "Which of the following is the traditional primary ingredient used to make guacamole?", o: ["Tomato", "Avocado", "Broccoli", "Cucumber"], a: 1, p: "$100", h: "It's a green, pear-shaped fruit famous for healthy fats." },
        { q: "Which planet in our solar system is famously known as the 'Red Planet'?", o: ["Venus", "Mars", "Jupiter", "Saturn"], a: 1, p: "$500", h: "Named after the Roman god of war." },
        { q: "How many constant humps does a traditional Dromedary camel have on its back?", o: ["One", "Two", "Three", "None"], a: 0, p: "$1,000", h: "It has one fewer hump than a Bactrian camel." },
        { q: "What is the standard currency used across the United Kingdom?", o: ["Euro", "Pound Sterling", "Dollar", "Franc"], a: 1, p: "$2,000", h: "It uses the symbol £." },
        { q: "Which of these classic authors wrote the iconic gothic novel 'Frankenstein'?", o: ["Mary Shelley", "Bram Stoker", "Jane Austen", "Edgar Allan Poe"], a: 0, p: "$4,000", h: "She was married to the poet Percy Bysshe Shelley." },
        { q: "What is the largest ocean on Earth?", o: ["Atlantic Ocean", "Indian Ocean", "Arctic Ocean", "Pacific Ocean"], a: 3, p: "$8,000", h: "This ocean borders the West Coast of the United States." },
        { q: "How many vibrant colors make up a standard, natural rainbow?", o: ["Five", "Six", "Seven", "Eight"], a: 2, p: "$16,000", h: "Remember the acronym acronym ROYGBIV." },
        { q: "Which distinct island continent country is the natural home of the kangaroo?", o: ["South Africa", "Australia", "Brazil", "New Zealand"], a: 1, p: "$32,000", h: "It's often referred to as the land 'Down Under'." },
        { q: "Which wizard serves as the headmaster of Hogwarts for most of the Harry Potter series?", o: ["Severus Snape", "Albus Dumbledore", "Lord Voldemort", "Ron Weasley"], a: 1, p: "$64,000", h: "He sports a long silver beard and owns a pet phoenix named Fawkes." },
        { q: "Which famous iron lattice tower landmark is located in Paris, France?", o: ["Eiffel Tower", "Leaning Tower of Pisa", "Big Ben", "Colosseum"], a: 0, p: "$125,000", h: "It was constructed as the entrance arch for the 1889 World's Fair." }
    ];

    let currentIndex = 0;
    let score = 0;
    let hintUsed = false;

    const container = document.getElementById('quiz-card');

    function loadQuestion() {
        hintUsed = false;
        if (currentIndex >= quizData.length) {
            showResults();
            return;
        }

        const current = quizData[currentIndex];
        const prefixes = ['A:', 'B:', 'C:', 'D:'];
        
        let optionsHTML = '';
        current.o.forEach((opt, idx) => {
            optionsHTML += `
                <button class="btn" onclick="checkAnswer(${idx}, this)">
                    <span class="prefix">${prefixes[idx]}</span> ${opt}
                </button>
            `;
        });

        container.innerHTML = `
            <div class="header">Question ${currentIndex + 1} of ${quizData.length}</div>
            <div class="prize">For ${current.p}</div>
            <div class="question-text">${current.q}</div>
            <div class="options-grid">${optionsHTML}</div>
            <div class="action-space" id="action-zone">
                <button class="hint-btn" onclick="revealHint()">Use Lifeline: Ask for a Hint</button>
                <div id="hint-text-box" class="hint-text" style="display:none;"></div>
            </div>
        `;
    }

    function checkAnswer(selectedIdx, clickedBtn) {
        const current = quizData[currentIndex];
        const buttons = container.querySelectorAll('.options-grid .btn');
        
        // Disable all buttons immediately
        buttons.forEach(btn => btn.disabled = true);
        
        if (selectedIdx === current.a) {
            clickedBtn.classList.add('correct');
            score++;
        } else {
            clickedBtn.classList.add('incorrect');
            buttons[current.a].classList.add('correct');
        }

        document.getElementById('action-zone').innerHTML = `
            <button class="next-btn" onclick="nextQuestion()">Next Question &rarr;</button>
        `;
    }

    function revealHint() {
        if (hintUsed) return;
        hintUsed = true;
        const box = document.getElementById('hint-text-box');
        box.innerText = quizData[currentIndex].h;
        box.style.display = 'block';
    }

    function nextQuestion() {
        currentIndex++;
        loadQuestion();
    }

    function showResults() {
        let dynamicMessage = "Spectacular job!";
        if (score === quizData.length) dynamicMessage = "Perfect score! You're a virtual millionaire!";
        else if (score < 5) dynamicMessage = "Better luck next time!";

        container.innerHTML = `
            <div class="results-screen">
                <h2>Game Over!</h2>
                <div class="results-score">${dynamicMessage}</div>
                <p style="font-size: 1.2rem; margin-bottom: 30px;">You answered <strong>${score}</strong> out of ${quizData.length} questions correctly.</p>
                <button class="restart-btn" onclick="restartQuiz()">Restart Quiz</button>
            </div>
        `;
    }

    function restartQuiz() {
        currentIndex = 0;
        score = 0;
        loadQuestion();
    }

    // Initialize on load
    loadQuestion();
</script>

</body>
</html>