<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>विलोम शब्द अभ्यास</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; padding: 20px; background-color: #f8f8f8; }
        h1 { color: #333; }
        .button { padding: 10px 20px; margin: 5px; font-size: 18px; cursor: pointer; border: none; border-radius: 5px; }
        .quiz-mode { background: #007bff; color: white; }
        .practice-mode { background: #28a745; color: white; }
        .flashcard { width: 300px; height: 200px; margin: 20px auto; display: flex; align-items: center; justify-content: center; font-size: 22px; background: blue; color: white; border: 1px solid #ccc; border-radius: 10px; cursor: pointer; transition: transform 0.6s, background 0.6s; }
        .flipped { background: green; }
        .hidden { display: none; }
        #options button { display: block; margin: 10px auto; padding: 10px; font-size: 18px; cursor: pointer; background: #fff; border: 1px solid #ccc; border-radius: 5px; }
        .option:hover { background: #e0e0e0; }
        #result { font-size: 20px; margin-top: 20px; font-weight: bold; }
        #nextBtn, #quitBtn { margin-top: 20px; padding: 10px 20px; font-size: 18px; border: none; cursor: pointer; border-radius: 5px; display: none; }
        #nextBtn { background: #28a745; color: white; }
        #quitBtn { background: #dc3545; color: white; }
        #progress { font-size: 18px; margin-top: 10px; }
    </style>
</head>
<body>
    <h1>विलोम शब्द अभ्यास</h1>
    <div id="progress">Your Progress: 0/0</div>
    <div id="modeSelection">
        <button class="button practice-mode" onclick="startPractice()">प्रैक्टिस मोड</button>
        <button class="button quiz-mode" onclick="startQuiz()">टेस्ट मोड</button>
    </div>
    
    <div id="flashcardContainer" class="hidden">
        <div class="flashcard" onclick="flipCard()" id="flashcard">Loading...</div>
        <button class="known" onclick="markKnown()">✅ ज्ञात</button>
        <button class="unknown" onclick="markUnknown()">❌ अज्ञात</button>
        <button id="quitBtn" onclick="confirmQuit()">🚪 छोड़ें</button>
    </div>
    
    <div id="quizContainer" class="hidden">
        <p id="question">Loading...</p>
        <div id="options"></div>
        <p id="result"></p>
        <button id="nextBtn" onclick="loadQuestion()">अगला प्रश्न</button>
        <button id="quitBtn" onclick="confirmQuit()">🚪 छोड़ें</button>
    </div>
    
    <script>
        const words = [
            { word: "अंगीकरण", antonym: "अनंगीकरण", known: false },
            { word: "अंगीकार", antonym: "अनंगीकार", known: false },
            { word: "अत्यधिक", antonym: "अत्यल्प", known: false },
            { word: "अंत", antonym: "आदि", known: false },
            { word: "अथ", antonym: "इति", known: false }
        ];
        let rememberedCount = 0;

        function updateProgress() {
            document.getElementById("progress").textContent = `Your Progress: ${rememberedCount}/${words.length}`;
            if (rememberedCount === words.length) {
                document.getElementById("flashcard").textContent = "सभी शब्द सीख लिए गए! कृपया याद रखने के लिए निरंतर अभ्यास करते रहें!";
            }
        }

        function startPractice() {
            document.getElementById("modeSelection").classList.add("hidden");
            document.getElementById("flashcardContainer").classList.remove("hidden");
            loadFlashcard();
        }

        function startQuiz() {
            document.getElementById("modeSelection").classList.add("hidden");
            document.getElementById("quizContainer").classList.remove("hidden");
            loadQuestion();
        }

        function loadQuestion() {
            let wordObj = words[Math.floor(Math.random() * words.length)];
            let correctAnswer = wordObj.antonym;
            let options = words.map(w => w.antonym).sort(() => Math.random() - 0.5);
            document.getElementById("question").textContent = `"${wordObj.word}" का विलोम शब्द क्या है?`;
            document.getElementById("options").innerHTML = options.map(option => `<button class='option' onclick='checkAnswer("${option}", "${correctAnswer}")'>${option}</button>`).join('');
            document.getElementById("nextBtn").style.display = "none";
        }

        function checkAnswer(selected, correct) {
            document.getElementById("result").textContent = selected === correct ? "सही उत्तर!" : "गलत! सही उत्तर: " + correct;
            document.getElementById("nextBtn").style.display = "block";
        }

        function confirmQuit() {
            if (confirm("कृपया अभ्यास ना छोड़ें!")) {
                location.reload();
            }
        }
    </script>
</body>
</html>
