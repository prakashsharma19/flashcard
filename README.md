<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Study Flashcards</title>
  <style>
    /* Basic Reset */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: linear-gradient(135deg, #74ebd5 0%, #acb6e5 100%);
      color: #333;
    }

    h1 {
      color: #fff;
      text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
      font-size: 2.5rem;
      margin-bottom: 20px;
      text-align: center;
    }

    .container {
      background-color: white;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0px 10px 30px rgba(0, 0, 0, 0.1);
      max-width: 400px;
      width: 100%;
      text-align: center;
    }

    .flashcard {
      background-color: #007BFF;
      color: white;
      border-radius: 10px;
      padding: 20px;
      text-align: center;
      cursor: pointer;
      user-select: none;
      margin-bottom: 20px;
      transition: transform 0.6s, background-color 0.3s;
      perspective: 1000px;
      height: 150px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 18px;
      position: relative;
    }

    .flashcard.flipped {
      background-color: #28a745;
      transform: rotateY(180deg);
    }

    .flashcard-content {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 100%;
      height: 100%;
      transition: transform 0.6s;
      transform-style: preserve-3d;
    }

    .flashcard .front,
    .flashcard .back {
      position: absolute;
      backface-visibility: hidden;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 20px;
    }

    .flashcard .back {
      transform: rotateY(180deg);
    }

    .form {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    input, textarea {
      padding: 12px;
      font-size: 16px;
      border-radius: 8px;
      border: 1px solid #ccc;
      width: 100%;
      background-color: #f9f9f9;
    }

    button {
      padding: 12px;
      font-size: 16px;
      background-color: #007BFF;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }

    button:hover {
      background-color: #0056b3;
    }

    /* Media query for responsiveness */
    @media (max-width: 500px) {
      .container {
        padding: 15px;
      }

      .flashcard {
        height: 120px;
        font-size: 16px;
      }

      input, textarea {
        font-size: 14px;
      }

      button {
        font-size: 14px;
        padding: 10px;
      }
    }

  </style>
</head>
<body>

  <div class="container">
    <h1>Study Flashcards</h1>
    <div id="flashcards"></div>

    <div class="form">
      <input type="text" id="question" placeholder="Enter the question" required>
      <textarea id="answer" placeholder="Enter the answer" required></textarea>
      <button id="addCard">Add Flashcard</button>
    </div>
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', () => {
      const flashcardsContainer = document.getElementById('flashcards');
      const questionInput = document.getElementById('question');
      const answerInput = document.getElementById('answer');
      const addCardButton = document.getElementById('addCard');

      let flashcards = JSON.parse(localStorage.getItem('flashcards')) || [];

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
        back.textContent = flashcard.answer;

        flashcardContent.appendChild(front);
        flashcardContent.appendChild(back);
        flashcardElement.appendChild(flashcardContent);

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

      addCardButton.addEventListener('click', () => {
        const question = questionInput.value.trim();
        const answer = answerInput.value.trim();

        if (question && answer) {
          const newFlashcard = { question, answer };
          flashcards.push(newFlashcard);
          localStorage.setItem('flashcards', JSON.stringify(flashcards));
          createFlashcardElement(newFlashcard, flashcards.length - 1);
          questionInput.value = '';
          answerInput.value = '';
        }
      });

      renderFlashcards();
    });
  </script>

</body>
</html>
