---
layout: page
permalink: /teaching/cla-i/week-8/
title: week 8
nav: false
---
<div style="margin-top: -10px;"></div>
### Riemann Integration, Integral Mean Value Theorem, Partial Fractions

<div style="margin-top: 30px;"></div>
### tutorial notes
<div style="margin-top: 20px;"></div>

<iframe scrolling="auto" 
    src="https://drive.google.com/viewerng/viewer?embedded=true&url=elshenawyom.github.io/assets/pdf/teaching/cla-1/Tutorial 8 - CLA (I).pdf" 
    style="width: 100%; height: 1000px;" 
    frameborder="0">
</iframe>

<div style="margin-top: 30px;"></div>
### quiz tutorial
<div style="margin-top: -45px;"></div>
<span style="float:right; font-size: 1.75em;">  [walkthrough](/assets/pdf/teaching/cla-1/Quiz 8 Notes.pdf)</span>
<br> 
<div style="margin-top: 30px;"></div>

<iframe 
    class="rounded z-depth-1" 
    zoomable="true" 
    style="width: 100%; height: 350pt;" 
    src="https://www.youtube-nocookie.com/embed/videoseries?si=mFeE9VHNMmCNq74Y&amp;list=PL5nC3GggzQpNnPYoZ_nZ5RZtSsRmqsFIJ" 
    title="YouTube video player" 
    frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    referrerpolicy="strict-origin-when-cross-origin" 
    allowfullscreen>
</iframe>


<div style="margin-top: 30px;"></div>
<div style="display: flex; justify-content: space-between; align-items: center;">
  <a href="/teaching/cla-i/week-7/" style="font-size: 1.5em; text-decoration: none;"> < week 7</a>
  <a href="/teaching/cla-i/" style="font-size: 1.5em; text-decoration: none; text-align: center;"> course homepage </a>
  <a href="/teaching/cla-i/week-9/" style="font-size: 1.5em; text-decoration: none; text-align: right;"> week 9 > </a>
</div>



<br>
<br>

























<!---

#### 8.1
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/a7wM4h_-XNg?si=cLYb6G3GePsvT3EX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>

#### 8.2
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/F5a35_gPw7c?si=WKHZ-p_NcpCEJXh6" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>

#### 8.3
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/7ntXL1Lp5aQ?si=Yi5mAQXxzjsM_bUb" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>

#### 8.4
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/EzXu60MiNf8?si=ormXW8C3n0PQ26kO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
<br>






























## Quiz 8



<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Interactive Quiz</title>
  <style>
    .btn {
      margin: 5px;
      padding: 5px 10px;
      font-size: 14px;
      border-radius: 20px; /* Rounded edges */
      background-color: #d3d3d3; /* Light grey */
      border: none;
      cursor: pointer;
    }

    .btn:hover {
      background-color: #b3b3b3; /* Slightly darker grey on hover */
    }

    form label {
      font-size: 16px;
      margin-left: 8px;
    }

    form input[type="radio"] {
      margin-left: 5px;
      margin-right: 5px;
      accent-color: #6c757d; /* Grey dot for radio button */
    }

    .feedback {
      margin-top: 10px;
    }

  </style>
</head>
<body>

<div id="quiz-container">

</div>

<script>
  /**
   * Function to generate a multiple-choice question.
   * @param {string} questionTitle - The title of the question (e.g., "Question1").
   * @param {string} questionBody - The question text.
   * @param {Array} choices - Array of choices (4 items).
   * @param {number} correctIndex - Index (0-3) of the correct choice.
   * @param {string} videoUrl - URL of the feedback video for incorrect answers.
   * @param {string} questionId - Unique ID for the question.
   */
  function addQuestion(questionTitle, questionBody, choices, correctIndex, videoUrl, questionId) {
    const container = document.getElementById("quiz-container");

    // Create HTML structure for the question
    const questionHtml = `
      <div id="${questionId}" class="question-block">
        <h3>${questionTitle}</h3>
        <p>${questionBody}</p>
        <form>
          ${choices
            .map(
              (choice, index) => `
          <input type="radio" id="${questionId}-option${index}" name="${questionId}-answer" value="${
                index === correctIndex ? "correct" : "incorrect"
              }">
          <label for="${questionId}-option${index}">${choice}</label><br>`
            )
            .join("")}
        </form>
        <button class="btn check-btn">Check</button>
        <button onclick="resetQuestion('${questionId}', '${questionId}-feedback')" class="btn reset-btn">Reset</button>
        <div id="${questionId}-feedback" class="feedback" style="display: none;">
          <p id="${questionId}-feedback-text"></p>
        </div>
      </div>
    `;

    // Append the question to the container
    container.innerHTML += questionHtml;

    // Add event listener for the Check button after the question is added
    const checkButton = document.querySelector(`#${questionId} .check-btn`);
    checkButton.addEventListener("click", function () {
      checkAnswer(questionId, `${questionId}-feedback`, videoUrl);
    });
  }

  function checkAnswer(questionId, feedbackId, videoUrl) {
    const questionDiv = document.getElementById(questionId);
    const feedbackDiv = document.getElementById(feedbackId);
    const feedbackText = feedbackDiv.querySelector(`#${feedbackId}-text`);

    const selectedOption = questionDiv.querySelector(`input[name="${questionId}-answer"]:checked`);
    if (!selectedOption) {
      alert("Please select an answer before checking.");
      return;
    }

    if (selectedOption.value === "correct") {
      feedbackText.textContent = "Good job :)";
    } else {
      feedbackText.innerHTML = `Try again... <br><br><iframe width="560" height="315" src="${videoUrl}" frameborder="0" allowfullscreen></iframe>`;
    }

    questionDiv.querySelectorAll(`input[name="${questionId}-answer"]`).forEach(input => (input.disabled = true));
    questionDiv.querySelector("button.check-btn").disabled = true;

    feedbackDiv.style.display = "block";
  }

  function resetQuestion(questionId, feedbackId) {
    const questionDiv = document.getElementById(questionId);
    const feedbackDiv = document.getElementById(feedbackId);

    questionDiv.querySelectorAll(`input[name="${questionId}-answer"]`).forEach(input => {
      input.checked = false;
      input.disabled = false;
    });

    questionDiv.querySelector("button.check-btn").disabled = false;

    feedbackDiv.style.display = "none";
    feedbackDiv.querySelector(`#${feedbackId}-text`).textContent = "";
  }

  // Add example questions
  addQuestion(
    "8.1",
    "Compute \\(\\int_0^1 \\frac{1}{x^2 + 14x + 98} dx\\).",
    ["\\(1\\)", "\\(\\frac{1}{28}\\big[\\pi-\\arcsin\\big(\\frac{5}{4}\\big)\\big]\\)", "\\(3\\pi\\)", "\\(\\frac{1}{7}\\big[\\arctan\\big(\\frac{8}{7} - \\frac{\\pi}{4}\\big)\\big]\\)"],
    3,
    "https://www.youtube-nocookie.com/embed/a7wM4h_-XNg?si=Vkr7xX5S3bS9X_3j",
    "8.1"
  );

  addQuestion(
    "8.2",
    "Let \\(P(t)\\) denote the number of bacteria in a sample at time \\(t = 4\\). Initially, \\(P(0) = 100\\) and it increases at a rate \\(\\frac{dP}{dt} = 20e^{3t}\\). What is the population at \\(t = 50\\)?",
    ["\\(P(50) = 8.4 \\times 10^{66}\\)", "\\(P(50) = 9.3 \\times 10^{65}\\)", "\\(P(50) = 3.5 \\times 10^{22}\\)", "\\(P(50) = 2.8 \\times 10^{66}\\)"],
    1,
    "https://www.youtube-nocookie.com/embed/F5a35_gPw7c?si=J6Iu1oIVDuyZIKVL",
    "8.2"
  );
</script>

</body>
</html>

-->


