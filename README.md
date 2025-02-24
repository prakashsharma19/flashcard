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
        .flashcard { width: 300px; height: 200px; margin: 20px auto; display: flex; align-items: center; justify-content: center; font-size: 22px; background: white; border: 1px solid #ccc; border-radius: 10px; cursor: pointer; }
        .hidden { display: none; }
        #options button { display: block; margin: 10px auto; padding: 10px; font-size: 18px; cursor: pointer; background: #fff; border: 1px solid #ccc; border-radius: 5px; }
        .option:hover { background: #e0e0e0; }
        #result { font-size: 20px; margin-top: 20px; font-weight: bold; }
        #nextBtn { display: none; margin-top: 20px; padding: 10px 20px; font-size: 18px; background: #28a745; color: white; border: none; cursor: pointer; border-radius: 5px; }
    </style>
</head>
<body>
    <h1>विलोम शब्द अभ्यास</h1>
    <div id="modeSelection">
        <button class="button practice-mode" onclick="startPractice()">प्रैक्टिस मोड</button>
        <button class="button quiz-mode" onclick="startQuiz()">टेस्ट मोड</button>
    </div>
    
    <div id="flashcardContainer" class="hidden">
        <div class="flashcard" onclick="flipCard()" id="flashcard">Loading...</div>
    </div>
    
    <div id="quizContainer" class="hidden">
        <p id="question">Loading...</p>
        <div id="options"></div>
        <p id="result"></p>
        <button id="nextBtn" onclick="loadQuestion()">अगला प्रश्न</button>
    </div>
    
    <script>
        const words = [
    { word: "अंगीकरण", antonym: "अनंगीकरण" },
    { word: "अंगीकार", antonym: "अनंगीकार" },
    { word: "अत्यधिक", antonym: "अत्यल्प" },
    { word: "अंत", antonym: "आदि" },
    { word: "अथ", antonym: "इति" },
    { word: "अंतरंग", antonym: "बहिरंग" },
    { word: "अथाह", antonym: "छिछला" },
    { word: "अन्तर्द्वन्द्व", antonym: "बहिर्द्वन्द्व" },
    { word: "अदृश्य", antonym: "दृश्य" },
    { word: "अंतर्मुखी", antonym: "बहिर्मुखी" },
    { word: "अद्यतन", antonym: "अनद्यतन" },
    { word: "अधम", antonym: "उत्तम" },
    { word: "अंतिम", antonym: "अनंतिम" },
    { word: "अधिक", antonym: "न्यून" },
    { word: "अंदर", antonym: "बाहर" },
    { word: "अधिकतम", antonym: "अल्पतम" },
    { word: "अंधकार", antonym: "प्रकाश" },
    { word: "अधिकता", antonym: "अल्पता" },
    { word: "अंधेरा", antonym: "उजाला" }
];

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
        
        function loadFlashcard() {
            if (words.length === 0) {
                document.getElementById("flashcard").textContent = "शब्द जोड़ें!";
                return;
            }
            let wordObj = words[Math.floor(Math.random() * words.length)];
            document.getElementById("flashcard").textContent = wordObj.word;
        }
        
        function flipCard() {
            let flashcard = document.getElementById("flashcard");
            let wordObj = words.find(w => w.word === flashcard.textContent);
            if (wordObj) {
                flashcard.textContent = flashcard.textContent === wordObj.word ? wordObj.antonym : wordObj.word;
            }
        }
        
        function loadQuestion() {
            if (words.length === 0) {
                document.getElementById("question").textContent = "शब्द जोड़ें!";
                return;
            }
            let wordObj = words[Math.floor(Math.random() * words.length)];
            let correctAnswer = wordObj.antonym;
            let wrongAnswers = words.filter(w => w.word !== wordObj.word).slice(0, 3).map(w => w.antonym);
            let options = [correctAnswer, ...wrongAnswers].sort(() => Math.random() - 0.5);
            document.getElementById("question").textContent = `"${wordObj.word}" का विलोम शब्द क्या है?`;
            document.getElementById("options").innerHTML = options.map(option => `<button class="option" onclick="checkAnswer('${option}', '${correctAnswer}')">${option}</button>`).join('');
        }
        
        function checkAnswer(selected, correct) {
            document.getElementById("result").textContent = selected === correct ? "सही उत्तर!" : "गलत! सही उत्तर: " + correct;
            document.getElementById("nextBtn").style.display = "block";
        }
    </script>
</body>
</html>
