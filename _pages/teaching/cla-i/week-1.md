---
layout: page
permalink: /teaching/cla-i/week-1/
title: week 1
description: 
nav: false
---
<div style="margin-top: -10px;"></div>
### Numbers, Quadratics and Polynomials
<div style="margin-top: 20px;"></div>

In a 70-minute tutorial, there are only so many things to do. To concentrate the tutorial time on fruitful discussions, I will provide short video tutorials for the problem sets, starting from next week.


<div style="margin-top: 10px;"></div>

#### tutorial 1

<center>
<iframe src="/assets/pdf/teaching/cla-1/Tutorial 1 - CLA (I).pdf/" width="100%" height="1000" style="border: none;">
  <p>Your browser does not support iframes.</p>
</iframe>
</center>

<div style="margin-top: 30px;"></div>
<div style="display: flex; justify-content: space-between; align-items: center;">
  <a href="/teaching/cla-i/" style="font-size: 1.5em; text-decoration: none; text-align: center;"> course homepage </a>
  <a href="/teaching/cla-i/week-2/" style="font-size: 1.5em; text-decoration: none; text-align: right;"> week 2 > </a>
</div>


<!--

### Quiz 1
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Multiple Questions</title>
  <style>
    body {
      margin: 20px;
    }
    .question {
      font-size: 18px;
      margin-bottom: 10px;
    }
    .answer-options {
      margin: 10px 0;
    }
    .feedback {
      display: none;
      margin-top: 10px;
      font-weight: bold;
    }
    .button-container {
      display: flex;
      gap: 10px;
      margin-top: 10px;
    }
    .reset-btn, .check-btn {
      padding: 10px 15px;
      font-size: 16px;
      border-radius: 30px;
      border: 2px solid #007BFF;
      background-color: #f5f5f5;
      cursor: pointer;
      text-align: center;
    }
    .reset-btn {
      background-color: #ffffff;
      color: #007BFF;
      display: flex;
      align-items: center;
    }
    .reset-btn::before {
      content: "🔄";
      margin-right: 8px;
    }
    .check-btn {
      background-color: #007BFF;
      color: #fff;
      text-decoration: none;
    }
    .check-btn:hover {
      background-color: #0056b3;
    }
    .feedback.correct {
      color: green;
    }
    .feedback.incorrect {
      color: red;
    }
  </style>
</head>
<body>

 Question 1
<div class="question">
  Find the (complex) roots of the polynomial
  <br>
  <span>\[ p(x) = x^2 + 4x + 13 \]</span>
</div>
<div class="answer-options">
  <input type="radio" id="answer1_1" name="answer_1" value="1">
  <label for="answer1_1"> \(x_1=-2-3i, x_2=-2+3i\) </label><br>
  <input type="radio" id="answer1_2" name="answer_1" value="2">
  <label for="answer1_2"> \(x_1=-3+2i, x_2=-3-2i\) </label><br>
  <input type="radio" id="answer1_3" name="answer_1" value="3">
  <label for="answer1_3"> \(x_1=+2+3i, x_2=+2-3i\) </label><br>
  <input type="radio" id="answer1_4" name="answer_1" value="4">
  <label for="answer1_4"> \(x_1=+3-2i, x_2=+3+2i\) </label>
</div>

<div class="button-container">
  <div class="reset-btn" onclick="resetQuestion(1)"> Reset</div>
  <button class="check-btn" onclick="checkAnswer(1)">Check</button>
</div>

<div class="feedback" id="feedback_1">
  Correct! The roots are: \( x_1 = -2 - 3i, x_2 = -2 + 3i \)
</div>

<script>
  function checkAnswer(questionNumber) {
    const selectedAnswer = document.querySelector(`input[name="answer_${questionNumber}"]:checked`);
    const feedback = document.getElementById(`feedback_${questionNumber}`);

    if (selectedAnswer) {
      const isCorrect = selectedAnswer.value === "1"; // Correct answer for all questions is always the first option
      if (isCorrect) {
        feedback.style.display = "block";
        feedback.className = "feedback correct";
      } else {
        feedback.style.display = "block";
        feedback.className = "feedback incorrect";
        feedback.innerHTML = "Incorrect! Please try again.";
      }
    } else {
      alert("Please select an answer.");
    }
  }

  function resetQuestion(questionNumber) {
    // Deselect radio buttons
    const radios = document.querySelectorAll(`input[name="answer_${questionNumber}"]`);
    radios.forEach(radio => radio.checked = false);

    // Hide feedback
    document.getElementById(`feedback_${questionNumber}`).style.display = "none";
  }
</script>

</body>
</html>
{% endraw %}

 -->
 
 
 

