---
layout: page
title: mathPix to moodle.sty
description: a RegEx project to speed up digital transformation to moodle
img: assets/img/mathPIX-to-moodle.sty/mathpix-to-moodle.sty.png
permalink: /projects/mathPix_to_moodle.sty/1/
importance: 1
category: tools for math education
related_publications: false
---

**Table of Contents**

<ol type="i">
  <li><a href="#mwe">a minimal working example</a></li>
  <li><a href="#pr">processing</a>
    <ol type="i">
      <li><a href="#no">no rationale</a></li>
    </ol>
  </li>
  <li><a href="#da">data extraction</a>
    <ol type="i">
      <li><a href="#co">correct answer</a></li>
      <li><a href="#id">question id</a></li>
      <li><a href="#bo">question body</a></li>
      <li><a href="#sm">shortanswer or multi?</a></li>
    </ol>
  </li>
  <li><a href="#pa">parsing</a>
    <ol type="i">
      <li><a href="#te">question templates</a>
        <ol type="i">
          <li><a href="#sh">shortanswer</a></li>
          <li><a href="#mu">multi</a></li>
        </ol>
      </li>
      <li><a href="#al">altogether</a>
        <ol type="i">
          <li><a href="#ou">output</a></li>
          <li><a href="#br">a brief note</a></li>
        </ol>
      </li>
    </ol>
  </li>
</ol>

---

## `i.` a minimal working example {#mwe}

Here, we have an SAT mathematics exercise.

<div style="text-align: center;">
<img src="/assets/img/mathPIX-to-moodle.sty/09d21d79-collegeboard.png" class="img-fluid rounded z-depth-1" style="width: 60%; height: auto;" />
</div>
<div style="margin-top: 15px;"></div>
With 200+ students in my class, it is very difficult to give personalized feedback. Therefore, we would like to have this exercise in nice digital format. 
<div style="margin-top: 15px;"></div>
<div style="text-align: center;">
<img src="/assets/img/mathPIX-to-moodle.sty/09d21d79.png" class="img-fluid rounded z-depth-1" style="width: 60%; height: auto;" />
</div>
<div style="margin-top: 15px;"></div>
This may of course be done using `moodle.sty`, and lots of copy-pasting if you'd like. It can, however, become impractical to repeat that for 1000+ questions. We propose the following question: is there a better way?  

Yes is the answer to the previous question. Instead of mindlessly copy-pasting questions, we decided to invest time and effort into a tool that does most of the work for us.

- [`moodle`](https://moodle.com/) is an open-source LMS software utilised by many institutions worldwide, including mine. 
- [`mathPIX`](https://mathpix.com/) is an OCR software that specializes in rendering $$\LaTeX$$.
- [`moodle.sty`](https://osl.ugr.es/CTAN/macros/latex/contrib/moodle/moodle.pdf) is a $$\LaTeX$$ package that mass-imports questions with `math` content into `moodle`.

The goal of this project is to start with `pdf` documents rendered into text using `mathPIX`, and then utilise `regEx` to bring it to a `moodle.sty`-compatible format for import into `moodle`. 

This notebook `mathPix-to-moodle.sty` aims to achieve our stated goal for one question. Then, the notebook `massPix-to-moodle.sty` generalises the construction for masses of questions. If you would like to immediately jump to there, please use

#### [> massPix-to-moodle.sty](/projects/mathPix-to-moodle.sty/2/)  

<div style="margin-top: 15px;"></div>

Let us now examine the `mathPIX` text input.

## `ii.` processing {#pr}

```python
import re
```

We start by reading extracted text from our file.


```python
def read_text_file(file_path):
    with open(file_path, 'r', encoding='utf-8') as file:
        content = file.read()
    return content
```

I have included three minimal working examples in the `mwe` directory.

- `q-multi.txt` is a multiple-choice question;
- `q-shortanswer.txt` is a short-answer question;
- `q-shortanswer-media.txt` is a short-answer question with media.

The input is not too nice, but we may start working with it for now. Below you will see the formatted output of the very-same question we saw above. 

**N.B:** this was done using mathPIX, the $$\LaTeX$$ OCR software.


```python
txt = read_text_file("mwe/q-shortanswer-media.txt")
print(txt)
```

    \section*{Question Difficulty: Hard}
    
    \section*{Question ID 09d21d79}
    \begin{tabular}{|l|l|l|l|l|}
    \hline Assessment & Test & Domain & Skill \\
    SAT & Math & Advanced Math & Nonlinear functions & Difficulty \\
    $\square \square$ \\
    \hline
    \end{tabular}
    
    \section*{ID: 09d21d79}
    ![](https://cdn.mathpix.com/cropped/2024_11_04_a374deafc4bc3bc8f639g-10.jpg?height=858&width=861&top_left_y=679&top_left_x=129)
    
    The graph of $y=2 x^{2}+b x+c$ is shown, where $b$ and $c$ are constants. What is the value of $b c$ ?
    
    \section*{ID: 09d21d79 Answer}
    
    Correct Answer: -24
    
    \section*{Rationale}
    
    The correct answer is -24 . Since the graph passes through the point $0,-6$, it follows that when the value of $x$ is 0 , the value of $y$ is -6 . Substituting 0 for $x$ and -6 for $y$ in the given equation yields $-6=20^{2}+b 0+c$, or $-6=c$. Therefore, the value of $c$ is -6 . Substituting -6 for $c$ in the given equation yields $y=2 x^{2}+b x-6$. Since the graph passes through the point $-1,-8$, it follows that when the value of $x$ is -1 , the value of $y$ is -8 . Substituting -1 for $x$ and -8 for $y$ in the equation $y=2 x^{2}+b x-6$ yields $-8=2-1^{2}+b-1-6$, or $-8=2-b-6$, which is equivalent to $-8=-4-b$. Adding 4 to each side of this equation yields $-4=-b$. Dividing each side of this equation by -1 yields $4=b$. Since the value of $b$ is 4 and the value of $c$ is -6 , it follows that the value of $b c$ is $4-6$, or -24 .
    
    Alternate approach: The given equation represents a parabola in the $x y$-plane with a vertex at $-1,-8$. Therefore, the given equation, $y=2 x^{2}+b x+c$, which is written in standard form, can be written in vertex form, $y=a x-h^{2}+k$, where $h, k$ is the vertex of the parabola and $a$ is the value of the coefficient on the $x^{2}$ term when the equation is written in standard form. It follows that $a=2$. Substituting 2 for $a,-1$ for $h$, and -8 for $k$ in this equation yields $y=2 x--1^{2}+-8$, or $y=2 x+1^{2}$ - 8 . Squaring the binomial on the right-hand side of this equation yields $y=2 x^{2}+2 x+1-8$. Multiplying each term inside the parentheses on the right-hand side of this equation by 2 yields $y=2 x^{2}+4 x+2-8$, which is equivalent to $y=2 x^{2}+4 x-6$. From the given equation $y=2 x^{2}+b x+c$, it follows that the value of $b$ is 4 and the value of $c$ is -6 .
    Therefore, the value of $b c$ is $4-6$, or -24 .
    
    Question Difficulty: Hard
    
    
    

### `ii.i` no rationale {#no}

We remove rationale for the sake of simplicity. In a practical sense, students may still retain the _rationale_ section of a question by looking up the question `id`, which in this case is [`09d21d79`](https://www.google.com/search?sca_esv=581abf87ebc1492d&q=09d21d79+sat&source=lnms&fbs=AEQNm0Aa4sjWe7Rqy32pFwRj0UkWd8nbOJfsBGGB5IQQO6L3JyJJclJuzBPl12qJyPx7ESI3cvVcqCdLSFFSD0xy83MxBKgRWp-QNbGOEJPj7KkhLDY4xUCEL9vvEWQyl1UyPMFfBseoDVPegO8KfpTS52Eo5kLtbJpPSyqCy0MiaV6PnhwS9ZmVR0jKN8yGaHpfZ-ZSkEcR&sa=X&ved=2ahUKEwiTm5_D5oWLAxXonf0HHZhODFIQ0pQJegQIGhAB&biw=1536&bih=730&dpr=1.25). One may additionally refer to [CollegeBoard](https://satsuitequestionbank.collegeboard.org/digital/search), choose the respective topic and difficulty, then look up the code using `ctrl + f`.

It is merely a matter of taste. To proceed, notice that _rationale_ is marked by starting with `\section*{Rationale}` and proceeding until the start of the following question. 


```python
def remove_rationale(text):
    # This pattern looks for '\section*{Rationale}' and removes everything until the next '\section*{' or end of string
    text = re.sub(r'\\section\*\{Rationale\}.*?(?=\\section\*|\Z)', '', text, flags=re.DOTALL)
    return text
```


```python
txt = remove_rationale(txt)
print(txt)
```

    \section*{Question Difficulty: Hard}
    
    \section*{Question ID 09d21d79}
    \begin{tabular}{|l|l|l|l|l|}
    \hline Assessment & Test & Domain & Skill \\
    SAT & Math & Advanced Math & Nonlinear functions & Difficulty \\
    $\square \square$ \\
    \hline
    \end{tabular}
    
    \section*{ID: 09d21d79}
    ![](https://cdn.mathpix.com/cropped/2024_11_04_a374deafc4bc3bc8f639g-10.jpg?height=858&width=861&top_left_y=679&top_left_x=129)
    
    The graph of $y=2 x^{2}+b x+c$ is shown, where $b$ and $c$ are constants. What is the value of $b c$ ?
    
    \section*{ID: 09d21d79 Answer}
    
    Correct Answer: -24
    
    
## `iii.` data extraction {#da}

We outline the extraction process of four key elements: `<ans>`, `<id>`, `<body>`, `<type>`.

### `iii.i` correct answer {#co}

Next, let us fetch the correct answer `<ans>`. This is always found within the string `Correct Answer: <ans>`. This is an easy observation once you inspect the `pdf` documents containing the exercises.


```python
def fetch_correct_answer(text):
    # Find the correct answer, allowing for negative signs in the answer
    match = re.search(r'Correct Answer:\s*(-?\d+)', text)
    return match.group(1) if match else None
```


```python
ans = fetch_correct_answer(txt)
print(ans)
```

    -24
    

### `iii.ii` question id {#id}

To fetch `<id>`, we look up the string `\section*{Question ID <id>}`. 


```python
def fetch_question_id(text):
    # Find the question ID
    match = re.search(r'\\section\*\{\s*Question ID\s+(\w+)\s*\}', text)
    return match.group(1) if match else None
```


```python
q_id = fetch_question_id(txt)
print(q_id)
```

    09d21d79
    

### `iii.iii` question body {#id}

The question body is always in the form
```
\section*{ID: <id>}

<body>
<choices> %if any

\section*{ID: <id> Answer}

```

{% raw %}
```python

def fetch_question_body(text, question_id):
    # Pattern to capture everything between \section*{ID: <question-id>} and the start of choices (A., B., C., or D.)
    pattern = rf'\\section\*\{{ID: {question_id}\}}\s*(.*?)(?=\s*[A-D]\.\s|\s*\\section\*\{{ID: {question_id} Answer\}})'
    match = re.search(pattern, text, flags=re.DOTALL)
    if match:
        return match.group(1).strip()
    return None
```
{% endraw %}


```python
q_body = fetch_question_body(txt, q_id)
print(q_body)
```

    ID: 09d21d79
    

### `iii.iv` `shortanswer` or `multi`? {#sm}

To determine question type, we inspect `<ans>`. 
- If `<ans>` is a literal `A - D`, then clearly this is a `multi` question. 

- Otherwise, there is a numerical or fractional answer of the form `a.bc` or `d/e` which characterizes a `shortanswer` question.


```python
def determine_question_type(answer):
    # Determine if the question is a short answer or multiple-choice based on the answer format
    if re.match(r'^-?\d+$|^-?\d+/\d+$', answer):  # Checks if answer is a number (including negative) or fraction
        return "shortanswer"
    elif re.match(r'^[A-D]$', answer):  # Checks if answer is A, B, C, or D
        return "multi"
    return None
```


```python
q_type = determine_question_type(ans)
print(q_type)
```

    shortanswer
    

## `iv.` parsing {#pa}

Upon constructing the necessary `multi` and `shortanswer` templates, we combine our elements in a `parse_question` function to give the intended result.

### `iv.i` question templates {#te}

#### `iv.i.i` - `shortanswer` {#sh}
 
The `shortanswer` moodle template is given as follows,
```
\begin{shortanswer}[fraction=100]{<id>}
<body>
\item[fraction=100] <ans>
\end{shortanswer}
```


```python
def format_shortanswer(question_id, question_body, answer):
    # Format short answer question
    formatted = f"""\\begin{{shortanswer}}[fraction=100]{{{question_id}}}
{question_body}
\\item[fraction=100] {answer}
\\end{{shortanswer}}"""
    return formatted
```


```python
#q = format_shortanswer(q_id, q_body, ans)
#print(q)
```

This finishes the job for `shortanswer`-type questions. Now remains to handle the `multi` case, specifically with choices.

#### `iv.i.ii` - `multi` {#mu}


```python
def extract_choices(text):
    # Extract choices while handling multiline content after each label (A., B., C., D.)
    choices = []
    pattern = r'([A-D]\.\s.*?)(?=\s*[A-D]\.|\\section\*\{ID:|$)'  # Match choices A., B., C., D.
    matches = re.findall(pattern, text, flags=re.DOTALL)
    
    for match in matches:
        # Normalize line breaks and strip leading/trailing whitespace
        choices.append(match.replace('\n', ' ').strip())
        
    # Remove the choice labels (A., B., C., D.) from each extracted choice
    choices = [choice[2:].strip() for choice in choices]  # Removes label (e.g., "A. " to keep just the choice)
    
    return choices if choices else None
```


```python
q_choices = extract_choices(txt)
print(q_choices) ## returns none, as the question is short-answer.
```

    None
    


```python
def format_multi(question_id, question_body, answer, choices):
    # Map answer to corresponding choice and format multiple-choice question
    if len(answer) == 1:
        answer_index = ord(answer) - ord('A')
        items = [f"\\item * {choice}" if i == answer_index else f"\\item {choice}" for i, choice in enumerate(choices)]
        formatted_items = "\n".join(items)
        formatted = f"""\\begin{{multi}}{{{question_id}}}
    {question_body}

    {formatted_items}
    \\end{{multi}}"""
    else:
        formatted = "None"
    return formatted
```


```python
q = format_multi(q_id, q_body, ans, q_choices)
print(q) ## returns none, as the question is short-answer.
```

    None
    

### `iv.ii` altogether {#al}


```python
# Main function to orchestrate parsing and formatting
def parse_question(text):
    text = remove_rationale(text)
    
    question_id = fetch_question_id(text)
    answer = fetch_correct_answer(text)
    question_body = fetch_question_body(text, question_id)
    
    if not all([question_id, answer, question_body]):
        return "Error: Unable to parse question properly."

    question_type = determine_question_type(answer)
    if question_type == "shortanswer":
        return format_shortanswer(question_id, question_body, answer)
    elif question_type == "multi":
        choices = extract_choices(text)
        if choices and len(choices) == 4:
            return format_multi(question_id, question_body, answer, choices)
        else:
            return "Error: Multiple-choice question lacks choices or has an incorrect format."
    else:
        return "Error: Unknown question type or unrecognized answer format."
```

Here is your parsed question...

#### `iv.ii.i` output {#ou}

```python
q = parse_question(txt)
print(q)
```

	\begin{shortanswer}[fraction=100]{09d21d79} 
	![](https://cdn.mathpix.com/cropped/2024_11_04_a374deafc4bc3bc8f639g-10.jpg?height=858&width=861&top_left_y=679&top_left_x=129) 
	 
	The graph of $y=2 x^{2}+b x+c$ is shown, where $b$ and $c$ are constants. What is the value of $b c$ ? 
	\item[fraction=100] -24 
	\end{shortanswer}
    

Looking good. 

#### `iv.ii.ii` brief note {#br}

Before we proceed, one must note that 

`![](https://cdn.mathpix.com/cropped/2024_11_04_a374deafc4bc3bc8f639g-10.jpg?height=858&width=861&top_left_y=679&top_left_x=129)
`

is premature, in the sense that $$\LaTeX$$ may only process the media file within this link once it is 

- downloaded;
- properly referenced within a directory.

In the `massPix-to-moodle.sty` notebook, there is a `media()` function that precisely takes care of this matter. 


<div style="margin-top: 15px;"></div>
<span style="float:right; font-size: 2em;">  [next: `massPix-to-moodle.sty` >](/projects/mathPix_to_moodle.sty/2/) </span> 

<div style="margin-top: 60px;"></div>

