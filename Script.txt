// Βήμα 1: Βρες τα στοιχεία του DOM
const questionTextElement = document.getElementById('question-text');
const answersListElement = document.getElementById('answers-list');
const nextButton = document.getElementById('next-button');

let currentQuestionIndex = 0;
let questions = [];

// Βήμα 2: Φόρτωσε τις ερωτήσεις από το JSON
fetch('data.json')
  .then(response => {
    if (!response.ok) {
      throw new Error('Δεν ήταν δυνατή η φόρτωση του αρχείου JSON');
    }
    return response.json();
  })
  .then(data => {
    questions = data.questions;
    // Αφού φορτωθούν οι ερωτήσεις, ξεκινάμε το παιχνίδι
    showQuestion(currentQuestionIndex);
  })
  .catch(error => {
    console.error('Σφάλμα:', error);
    questionTextElement.textContent = 'Δεν ήταν δυνατή η φόρτωση των ερωτήσεων.';
  });

// Βήμα 3: Λειτουργία εμφάνισης ερώτησης
function showQuestion(index) {
  // Καθάρισε τις προηγούμενες απαντήσεις
  answersListElement.innerHTML = '';
  
  const currentQuestion = questions[index];
  questionTextElement.textContent = currentQuestion.q;

  // Δημιούργησε τα κουμπιά για τις απαντήσεις
  currentQuestion.a.forEach((answer, i) => {
    const listItem = document.createElement('li');
    const button = document.createElement('button');
    button.textContent = answer;
    button.classList.add('answer-button');
    
    // Πρόσθεσε event listener για τον έλεγχο της απάντησης
    button.addEventListener('click', () => selectAnswer(i, currentQuestion.correct));

    listItem.appendChild(button);
    answersListElement.appendChild(listItem);
  });
}

// Βήμα 4: Λειτουργία ελέγχου απάντησης
function selectAnswer(selectedIndex, correctIndex) {
  const answerButtons = answersListElement.querySelectorAll('.answer-button');
  
  // Απενεργοποίησε όλα τα κουμπιά απάντησης
  answerButtons.forEach(button => {
    button.disabled = true;
    button.style.cursor = 'not-allowed';
  });

  // Μαρκάρισε τη σωστή και τη λάθος απάντηση
  if (selectedIndex === correctIndex) {
    answerButtons[selectedIndex].classList.add('correct');
  } else {
    answerButtons[selectedIndex].classList.add('wrong');
    answerButtons[correctIndex].classList.add('correct');
  }

  // Εμφάνισε το κουμπί για την επόμενη ερώτηση
  nextButton.style.display = 'block';
}

// Βήμα 5: Λειτουργία για την επόμενη ερώτηση
nextButton.addEventListener('click', () => {
  currentQuestionIndex++;
  // Ελέγξε αν υπάρχουν κι άλλες ερωτήσεις
  if (currentQuestionIndex < questions.length) {
    showQuestion(currentQuestionIndex);
    nextButton.style.display = 'none';
  } else {
    // Εμφάνισε μήνυμα τέλους παιχνιδιού
    questionTextElement.textContent = 'Το παιχνίδι τελείωσε! Ελπίζω να διασκεδάσατε!';
    answersListElement.innerHTML = '';
    nextButton.style.display = 'none';
  }
});
