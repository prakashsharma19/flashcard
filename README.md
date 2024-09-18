<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Interactive Study Flashcards</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f5f5f5;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      height: 100vh;
    }

    h1, h2 {
      color: #333;
      text-align: center;
      margin-bottom: 20px;
    }

    .container {
      width: 90%;
      max-width: 900px;
      display: flex;
      flex-wrap: wrap;
      justify-content: flex-end;
    }

    .flashcard {
      width: 100px;
      height: 100px;
      background-color: #007BFF;
      color: white;
      margin: 10px;
      border-radius: 10px;
      text-align: center;
      cursor: pointer;
      position: relative;
      user-select: none;
      transition: transform 0.6s, background-color 0.3s;
      transform-style: preserve-3d;
      perspective: 1000px;
    }

    .flashcard.flipped {
      transform: rotateY(180deg);
    }

    .flashcard-content {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      backface-visibility: hidden;
    }

    .flashcard .front {
      font-size: 14px;
      padding: 10px;
    }

    .flashcard .back {
      position: absolute;
      transform: rotateY(180deg);
      display: flex;
      flex-direction: column;
      gap: 5px;
      align-items: center;
    }

    .answer-options {
      display: flex;
      gap: 10px;
      margin-top: 10px;
    }

    .answer-options button {
      background-color: transparent;
      border: none;
      font-size: 24px;
      cursor: pointer;
      transition: transform 0.3s ease;
    }

    .answer-options button:hover {
      transform: scale(1.2);
    }

    button.correct {
      color: green;
    }

    button.incorrect {
      color: red;
    }

    .add-topic-btn {
      margin: 20px 0;
      padding: 10px 20px;
      background-color: #007BFF;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    .add-topic-btn:hover {
      background-color: #0056b3;
    }

    .form-container {
      display: none;
      flex-direction: column;
      align-items: center;
    }

    .input-group {
      margin-bottom: 10px;
    }

    .input-group label {
      display: block;
      margin-bottom: 5px;
      font-size: 14px;
    }

    .input-group input {
      padding: 8px;
      width: 100%;
      max-width: 300px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    .form-container button {
      padding: 10px 20px;
      background-color: #007BFF;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    .form-container button:hover {
      background-color: #0056b3;
    }

    .delete-flashcard {
      position: absolute;
      top: 5px;
      right: 5px;
      background-color: red;
      color: white;
      padding: 2px 6px;
      border: none;
      border-radius: 50%;
      cursor: pointer;
    }

  </style>
</head>
<body>

  <div id="subject-container" class="form-container">
    <h1>Select Subject</h1>
    <div class="input-group">
      <label for="subject">Enter Subject</label>
      <input type="text" id="subject-input" placeholder="e.g. Math">
    </div>
    <button id="start-subject-btn">Start</button>
  </div>

  <div id="topic-container" class="form-container">
    <h1>Select Topic</h1>
    <div class="input-group">
      <label for="topic">Enter Topic</label>
      <input type="text" id="topic-input" placeholder="e.g. Algebra">
    </div>
    <button id="start-topic-btn">Start</button>
  </div>

  <h2 id="subject-topic-title"></h2>

  <div id="flashcards-container" class="container"></div>

  <button id="add-topic" class="add-topic-btn">+ Add Topic</button>

  <script>
    const subjectContainer = document.getElementById('subject-container');
    const topicContainer = document.getElementById('topic-container');
    const flashcardsContainer = document.getElementById('flashcards-container');
    const addTopicBtn = document.getElementById('add-topic');
    const subjectInput = document.getElementById('subject-input');
    const topicInput = document.getElementById('topic-input');
    const subjectTopicTitle = document.getElementById('subject-topic-title');
    let subject = '';
    let topic = '';

    let flashcards = JSON.parse(localStorage.getItem('flashcards')) || [];

    document.getElementById('start-subject-btn').addEventListener('click', () => {
      subject = subjectInput.value.trim();
      if (subject) {
        subjectContainer.style.display = 'none';
        topicContainer.style.display = 'flex';
      }
    });

    document.getElementById('start-topic-btn').addEventListener('click', () => {
      topic = topicInput.value.trim();
      if (topic) {
        topicContainer.style.display = 'none';
        subjectTopicTitle.textContent = `${subject} - ${topic}`;
        flashcardsContainer.style.display = 'flex';
        renderFlashcards();
      }
    });

    function createFlashcardElement(flashcard, index) {
      const flashcardElement = document.createElement('div');
      flashcardElement.classList.add('flashcard');
      
      const flashcardContent = document.createElement('div');
      flashcardContent.classList.add('flashcard-content');
      
      const front = document.createElement('div');
      front.classList.add('front');
      front.textContent = flashcard.question;
      
      const back = document.createElement('div');
      back.classList.add('back');
      back.innerHTML = `
        <div class="answer-options">
          <button class="correct">✓</button>
          <button class="incorrect">✗</button>
        </div>
      `;
      
      back.querySelector('.correct').addEventListener('click', () => {
        flashcardElement.style.backgroundColor = 'green';
      });
      
      back.querySelector('.incorrect').addEventListener('click', () => {
        flashcardElement.style.backgroundColor = 'red';
      });
      
      const deleteBtn = document.createElement('button');
      deleteBtn.classList.add('delete-flashcard');
      deleteBtn.textContent = '×';
      deleteBtn.addEventListener('click', () => {
        flashcards.splice(index, 1);
        localStorage.setItem('flashcards', JSON.stringify(flashcards));
        renderFlashcards();
      });
      
      flashcardContent.appendChild(front);
      flashcardContent.appendChild(back);
      flashcardElement.appendChild(flashcardContent);
      flashcardElement.appendChild(deleteBtn);
      
      flashcardElement.addEventListener('click', () => {
        flashcardElement.classList.toggle('flipped');
      });
      
      flashcardsContainer.appendChild(flashcardElement);
    }

    function renderFlashcards() {
      flashcardsContainer.innerHTML = '';
      flashcards.forEach((flashcard, index) => {
        createFlashcardElement(flashcard, index);
      });
    }

    addTopicBtn.addEventListener('click', () => {
      const question = prompt('Enter Flashcard Question');
      if (question) {
        const newFlashcard = { question, answer: 'Answer not displayed' };
        flashcards.push(newFlashcard);
        localStorage.setItem('flashcards', JSON.stringify(flashcards));
        renderFlashcards();
      }
    });

    renderFlashcards();

  </script>
</body>
</html>
