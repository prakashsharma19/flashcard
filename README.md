<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flashcard App</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f9f9f9;
        }

        #app {
            width: 80%;
            max-width: 1200px;
            text-align: center;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.1);
            padding: 20px;
        }

        .screen {
            display: none;
        }

        h1, h2 {
            color: #333;
            margin-bottom: 20px;
        }

        select, input {
            padding: 10px;
            font-size: 16px;
            width: 250px;
            margin: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }

        button {
            margin-top: 10px;
            padding: 10px 20px;
            border: none;
            background-color: #4CAF50;
            color: white;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
            margin: 10px;
        }

        button:hover {
            background-color: #45a049;
        }

        #flashcards-container {
            display: flex;
            flex-wrap: wrap-reverse;
            justify-content: center;
            margin: 20px;
        }

        .flashcard {
            width: 150px;
            height: 200px;
            margin: 10px;
            background-color: white;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            display: flex;
            justify-content: center;
            align-items: center;
            text-align: center;
            cursor: pointer;
            perspective: 1000px;
            position: relative;
        }

        .flashcard-inner {
            width: 100%;
            height: 100%;
            transition: transform 0.6s;
            transform-style: preserve-3d;
            position: absolute;
        }

        .flashcard.flipped .flashcard-inner {
            transform: rotateY(180deg);
        }

        .flashcard-front, .flashcard-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .flashcard-front {
            background-color: #fff;
        }

        .flashcard-back {
            background-color: #f0f0f0;
            transform: rotateY(180deg);
        }

        .flashcard-content {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }

        .flashcard-actions {
            margin-top: 10px;
            display: flex;
            justify-content: space-between;
        }

        .flashcard-actions button {
            padding: 5px;
            width: 40px;
            font-size: 14px;
            cursor: pointer;
        }

        .flashcard .delete-icon {
            position: absolute;
            top: 10px;
            right: 10px;
            cursor: pointer;
        }

        .flashcard.green {
            background-color: #a8d5a5;
        }

        .flashcard.red {
            background-color: #f8d7da;
        }

        .flashcard-icon {
            width: 24px;
            height: 24px;
        }
    </style>
</head>
<body>
    <div id="app">
        <div id="subject-screen" class="screen">
            <h1>Select Subject</h1>
            <select id="subject-dropdown">
                <option value="" disabled selected>Select a subject</option>
                <option value="Hindi">Hindi</option>
                <option value="Polity">Polity</option>
                <option value="History">History</option>
                <option value="Science">Science</option>
                <option value="Geography">Geography</option>
                <option value="UP Special">UP Special</option>
            </select>
            <button onclick="setSubject()">Submit</button>
        </div>
        
        <div id="topic-screen" class="screen">
            <h1>Subject: <span id="subject-name"></span></h1>
            <h2>Select or Add Topic</h2>
            <input type="text" id="topic-input" placeholder="Enter topic name" />
            <button onclick="setTopic()">Add Topic</button>
            <ul id="topic-list"></ul>
        </div>
        
        <div id="flashcard-screen" class="screen">
            <h2 id="flashcard-subject-topic"></h2>
            <div id="flashcards-container"></div>
            <button onclick="addFlashcard()">Add Flashcard</button>
            <button onclick="clearSession()">Clear Session</button>
        </div>
    </div>

    <script>
        let flashcards = [];
        let currentSubject = '';
        let currentTopic = '';
        let topics = {};

        // Initialize App
        window.onload = () => {
            loadSession();
            document.getElementById('subject-screen').style.display = 'block';
        };

        function setSubject() {
            const subject = document.getElementById('subject-dropdown').value;
            if (subject) {
                currentSubject = subject;
                document.getElementById('subject-name').innerText = subject;
                document.getElementById('subject-screen').style.display = 'none';
                document.getElementById('topic-screen').style.display = 'block';
                updateTopics();
            }
        }

        function setTopic() {
            const topic = document.getElementById('topic-input').value;
            if (topic) {
                if (!topics[topic]) {
                    topics[topic] = [];
                }
                currentTopic = topic;
                updateTopics();
                showFlashcards();
            }
        }

        function updateTopics() {
            const topicList = document.getElementById('topic-list');
            topicList.innerHTML = '';
            for (const topic in topics) {
                const li = document.createElement('li');
                li.innerText = topic;
                li.onclick = () => {
                    currentTopic = topic;
                    showFlashcards();
                };
                topicList.appendChild(li);
            }
        }

        function showFlashcards() {
            document.getElementById('topic-screen').style.display = 'none';
            document.getElementById('flashcard-screen').style.display = 'block';
            document.getElementById('flashcard-subject-topic').innerText = `${currentSubject} - ${currentTopic}`;
            renderFlashcards();
        }

        function renderFlashcards() {
            const container = document.getElementById('flashcards-container');
            container.innerHTML = '';

            const currentFlashcards = topics[currentTopic] || [];

            currentFlashcards.forEach((card, index) => {
                const flashcard = document.createElement('div');
                flashcard.classList.add('flashcard');
                flashcard.onclick = () => flipFlashcard(index);

                const flashcardInner = document.createElement('div');
                flashcardInner.classList.add('flashcard-inner');

                const flashcardFront = document.createElement('div');
                flashcardFront.classList.add('flashcard-front');

                const flashcardBack = document.createElement('div');
                flashcardBack.classList.add('flashcard-back');

                const contentFront = document.createElement('div');
                contentFront.classList.add('flashcard-content');
                const question = document.createElement('p');
                question.innerText = card.question;
                contentFront.appendChild(question);
                flashcardFront.appendChild(contentFront);

                const contentBack = document.createElement('div');
                contentBack.classList.add('flashcard-content');
                const answer = document.createElement('p');
                answer.innerText = card.answer;

                if (card.flipped) {
                    const actions = document.createElement('div');
                    actions.classList.add('flashcard-actions');

                    const correctBtn = document.createElement('button');
                    correctBtn.innerHTML = '&#x2714;';
                    correctBtn.onclick = (event) => markCorrect(event, index);

                    const wrongBtn = document.createElement('button');
                    wrongBtn.innerHTML = '&#x2718;';
                    wrongBtn.onclick = (event) => markWrong(event, index);

                    actions.appendChild(correctBtn);
                    actions.appendChild(wrongBtn);
                    contentBack.appendChild(actions);
                }

                contentBack.appendChild(answer);
                flashcardBack.appendChild(contentBack);

                flashcardInner.appendChild(flashcardFront);
                flashcardInner.appendChild(flashcardBack);
                flashcard.appendChild(flashcardInner);

                const deleteIcon = document.createElement('div');
                deleteIcon.classList.add('delete-icon');
                deleteIcon.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="#e8eaed"><path d="M280-120q-33 0-56.5-23.5T200-200v-520h-40v-80h200v-40h240v40h200v80h-40v520q0 33-23.5 56.5T680-120H280Zm400-600H280v520h400v-520ZM360-280h80v-360h-80v360Zm160 0h80v-360h-80v360ZM280-720v520-520Z"/></svg>';
                deleteIcon.onclick = (event) => deleteFlashcard(event, index);
                flashcard.appendChild(deleteIcon);

                if (card.correct === true) flashcard.classList.add('green');
                if (card.correct === false) flashcard.classList.add('red');

                container.appendChild(flashcard);
            });

            saveSession();
        }

        function flipFlashcard(index) {
            topics[currentTopic][index].flipped = !topics[currentTopic][index].flipped;
            renderFlashcards();
        }

        function markCorrect(event, index) {
            event.stopPropagation();
            topics[currentTopic][index].correct = true;
            renderFlashcards();
        }

        function markWrong(event, index) {
            event.stopPropagation();
            topics[currentTopic][index].correct = false;
            renderFlashcards();
        }

        function addFlashcard() {
            const question = prompt('Enter the question:');
            const answer = prompt('Enter the answer:');
            if (question && answer) {
                topics[currentTopic].push({ question, answer, flipped: false, correct: null });
                renderFlashcards();
            }
        }

        function deleteFlashcard(event, index) {
            event.stopPropagation();
            topics[currentTopic].splice(index, 1);
            renderFlashcards();
        }

        function clearSession() {
            localStorage.clear();
            location.reload();
        }

        function saveSession() {
            localStorage.setItem('flashcardApp', JSON.stringify({ topics, currentSubject }));
        }

        function loadSession() {
            const savedData = localStorage.getItem('flashcardApp');
            if (savedData) {
                const { topics: savedTopics, currentSubject: savedSubject } = JSON.parse(savedData);
                topics = savedTopics;
                currentSubject = savedSubject;
                document.getElementById('subject-dropdown').value = savedSubject;
                if (savedSubject) {
                    document.getElementById('subject-name').innerText = savedSubject;
                    document.getElementById('subject-screen').style.display = 'none';
                    document.getElementById('topic-screen').style.display = 'block';
                    updateTopics();
                }
            }
        }
    </script>
</body>
</html>
