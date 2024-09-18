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
            background-color: #f0f0f0;
        }

        #app {
            width: 100%;
            text-align: center;
        }

        .screen {
            display: none;
        }

        h1, h2 {
            color: #333;
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
        }

        button:hover {
            background-color: #45a049;
        }

        input {
            padding: 10px;
            font-size: 16px;
            width: 200px;
        }

        select {
            padding: 10px;
            font-size: 16px;
            width: 220px;
        }

        #flashcards-container {
            display: flex;
            flex-wrap: wrap-reverse;
            justify-content: right;
            margin: 20px;
        }

        .flashcard {
            width: 100px;
            height: 150px;
            margin: 10px;
            background-color: white;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            display: flex;
            justify-content: center;
            align-items: center;
            text-align: center;
            cursor: pointer;
            position: relative;
            transition: transform 0.3s ease;
        }

        .flashcard.flipped {
            transform: rotateY(180deg);
        }

        .flashcard.green {
            background-color: #a8d5a5;
        }

        .flashcard.red {
            background-color: #f8d7da;
        }

        .flashcard-content {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }

        .flashcard-content p {
            margin: 0;
            padding: 0;
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

        .delete-icon {
            position: absolute;
            top: 5px;
            right: 5px;
            width: 20px;
            height: 20px;
            cursor: pointer;
            fill: #e8eaed;
        }
    </style>
</head>
<body>
    <div id="app">
        <div id="subject-screen" class="screen">
            <h1>Select Subject</h1>
            <select id="subject-select">
                <option value="">--Select a Subject--</option>
                <option value="Hindi">Hindi</option>
                <option value="Polity">Polity</option>
                <option value="History">History</option>
                <option value="Science">Science</option>
                <option value="Geography">Geography</option>
                <option value="UP Special">UP Special</option>
            </select>
            <button onclick="loadSubject()">Next</button>
        </div>
        
        <div id="topic-screen" class="screen">
            <h1>Subject: <span id="subject-name"></span></h1>
            <h2>Select or Add Topic</h2>
            <input type="text" id="topic-input" placeholder="Enter topic name" />
            <button onclick="addTopic()">Add Topic</button>
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

        function loadSubject() {
            const subject = document.getElementById('subject-select').value;
            if (subject) {
                currentSubject = subject;
                document.getElementById('subject-name').innerText = subject;
                document.getElementById('subject-screen').style.display = 'none';
                document.getElementById('topic-screen').style.display = 'block';
                updateTopics();
            }
        }

        function addTopic() {
            const topic = document.getElementById('topic-input').value;
            if (topic) {
                currentTopic = topic;
                if (!topics[currentSubject]) topics[currentSubject] = {};
                if (!topics[currentSubject][topic]) topics[currentSubject][topic] = [];
                updateTopics();
                showFlashcards();
            }
        }

        function updateTopics() {
            const topicList = document.getElementById('topic-list');
            topicList.innerHTML = '';
            const topicsForSubject = topics[currentSubject] || {};

            for (const topic in topicsForSubject) {
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

            const currentFlashcards = topics[currentSubject][currentTopic] || [];

            currentFlashcards.forEach((card, index) => {
                const flashcard = document.createElement('div');
                flashcard.classList.add('flashcard');
                flashcard.onclick = () => flipFlashcard(index);

                const content = document.createElement('div');
                content.classList.add('flashcard-content');
                const question = document.createElement('p');
                question.innerText = card.flipped ? card.question : card.answer;

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
                    content.appendChild(actions);
                }

                content.appendChild(question);
                flashcard.appendChild(content);

                if (card.correct === true) flashcard.classList.add('green');
                if (card.correct === false) flashcard.classList.add('red');

                // Delete icon
                const deleteIcon = document.createElement('svg');
                deleteIcon.classList.add('delete-icon');
                deleteIcon.setAttribute('xmlns', 'http://www.w3.org/2000/svg');
                deleteIcon.setAttribute('height', '24px');
                deleteIcon.setAttribute('viewBox', '0 -960 960 960');
                deleteIcon.setAttribute('width', '24px');
                deleteIcon.innerHTML = '<path d="M280-120q-33 0-56.5-23.5T200-200v-520h-40v-80h200v-40h240v40h200v80h-40v520q0 33-23.5 56.5T680-120H280Zm400-600H280v520h400v-520ZM360-280h80v-360h-80v360Zm160 0h80v-360h-80v360ZM280-720v520-520Z"/>';
                deleteIcon.onclick = (event) => deleteFlashcard(event, index);

                flashcard.appendChild(deleteIcon);
                container.appendChild(flashcard);
            });

            saveSession();
        }

        function flipFlashcard(index) {
            topics[currentSubject][currentTopic][index].flipped = !topics[currentSubject][currentTopic][index].flipped;
            renderFlashcards();
        }

        function markCorrect(event, index) {
            event.stopPropagation();
            topics[currentSubject][currentTopic][index].correct = true;
            renderFlashcards();
        }

        function markWrong(event, index) {
            event.stopPropagation();
            topics[currentSubject][currentTopic][index].correct = false;
            renderFlashcards();
        }

        function addFlashcard() {
            const question = prompt('Enter the question:');
            const answer = prompt('Enter the answer:');
            if (question && answer) {
                topics[currentSubject][currentTopic].push({ question, answer, flipped: false, correct: null });
                renderFlashcards();
            }
        }

        function deleteFlashcard(event, index) {
            event.stopPropagation();
            topics[currentSubject][currentTopic].splice(index, 1);
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
            }
        }
    </script>
</body>
</html>
