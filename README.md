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
        .flashcard { width: 300px; height: 200px; margin: 20px auto; display: flex; align-items: center; justify-content: center; font-size: 22px; background: blue; color: white; border: 1px solid #ccc; border-radius: 10px; cursor: pointer; transition: transform 0.6s; }
        .flipped { transform: rotateY(180deg); background: green; }
        .hidden { display: none; }
        #options button { display: block; margin: 10px auto; padding: 10px; font-size: 18px; cursor: pointer; background: #fff; border: 1px solid #ccc; border-radius: 5px; }
        .option:hover { background: #e0e0e0; }
        #result { font-size: 20px; margin-top: 20px; font-weight: bold; }
        #nextBtn, #quitBtn { margin-top: 20px; padding: 10px 20px; font-size: 18px; border: none; cursor: pointer; border-radius: 5px; }
        #nextBtn { background: #28a745; color: white; }
        #quitBtn { background: #dc3545; color: white; }
        #progress { font-size: 18px; margin-top: 10px; }
        #zoomControls { margin-top: 10px; }
    </style>
</head>
<body>
    <h1>विलोम शब्द अभ्यास</h1>
    <div id="progress">Your Progress: 0/0</div>
    <div id="zoomControls">
        <button onclick="zoomIn()">➕</button>
        <button onclick="zoomOut()">➖</button>
    </div>
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

        let rememberedCount = 0;
        let wordsCompleted = 0;

        function updateProgress() {
            document.getElementById("progress").textContent = `Your Progress: ${rememberedCount}/${words.length}`;
        }

        function startPractice() {
            document.getElementById("modeSelection").classList.add("hidden");
            document.getElementById("flashcardContainer").classList.remove("hidden");
            loadFlashcard();
        }

        function loadFlashcard() {
            if (wordsCompleted >= words.length) {
                document.getElementById("flashcard").textContent = "सभी शब्द सीख लिए गए! कृपया याद रखने के लिए निरंतर अभ्यास करें।";
                return;
            }
            currentFlashcard = words[wordsCompleted];
            document.getElementById("flashcard").textContent = currentFlashcard.word;
            document.getElementById("flashcard").classList.remove("flipped");
        }

        function flipCard() {
            let flashcard = document.getElementById("flashcard");
            if (flashcard.classList.contains("flipped")) {
                flashcard.textContent = currentFlashcard.word;
                flashcard.classList.remove("flipped");
            } else {
                flashcard.textContent = currentFlashcard.antonym;
                flashcard.classList.add("flipped");
            }
        }

        function markKnown() {
            rememberedCount++;
            wordsCompleted++;
            updateProgress();
            loadFlashcard();
        }

        function markUnknown() {
            loadFlashcard();
        }

        function zoomIn() {
            document.body.style.fontSize = "larger";
        }

        function zoomOut() {
            document.body.style.fontSize = "smaller";
        }

        function confirmQuit() {
            if (confirm("कृपया अभ्यास ना छोड़ें!")) {
                location.reload();
            }
        }
    </script>
</body>
</html>
