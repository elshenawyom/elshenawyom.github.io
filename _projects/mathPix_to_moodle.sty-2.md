---
layout: page
title: massPix to moodle.sty
description: a RegEx project to speed up digital transformation to moodle
img: assets/img/mathPix_to_moodle.sty/mathPix_to_moodle.sty.png
permalink: /projects/mathPix_to_moodle.sty/2/
hidden: true
importance: 1
category: tools for math education
related_publications: false
---

<span style="float:left; font-size: 1.6em;">  [< back: `mathPix-to-moodle.sty`](/projects/mathPix_to_moodle.sty/2/) </span> 


<br>
<div style="margin-top: 35px;"></div>

{% raw %}

---
 
**Table of Contents**

<ol type=i>

	<li> <a href="#pr">prelim</a>
		<ol type=i>
			<li> <a href="#re"><code>read</code></a> </li>
			<li> <a href="#no"><code>no_rationale</code></a> </li>
			<li> <a href="#ans"><code>&lt;ans&gt;</code></a> </li>
			<li> <a href="#id"><code>&lt;id&gt;</code></a> </li>
			<li> <a href="#bo"><code>&lt;body&gt;</code></a> </li>
			<li> <a href="#ty"><code>&lt;type&gt;</code></a> </li>
			<li> <a href="#sh"><code>shortanswer</code></a> </li>
			<li> <a href="#mu"><code>multi</code></a> </li>
			<li> <a href="#pa"><code>parse</code></a> </li>
		</ol>
	</li>

	<li> <a href="#ha">handling</a> </li>
		<ol type=i>
			<li> <a href="#pro">processing</a> </li>
			<li> <a href="#th">the <code>\section*{ID:</code> string</a> </li>
			<li> <a href="#ta"><code>tabular</code> environments</a> </li>
		</ol>

	<li> <a href="#bp">bad parse</a> </li>
		<ol type=i>
			<li> <a href="#cpi">common parsing issues</a> </li>
			<li> <a href="#rw">recommended workflow</a> </li>
		</ol>

	<li> <a href="#me">media</a> </li>
		<ol type=i>
			<li> <a href="#di">directories</a> </li>
			<li> <a href="#ap">action plan</a> </li>
		</ol>

	<li> <a href="#cl">closing words</a> </li>
</ol>

--- 

# prelim {#pr}


`massPix-to-moodle.sty` does what `mathPix-to-moodle.sty` does, just in masses. We do not expect the migration to be a smooth process, therefore we set some checks on the consistency of our `.txt` input.  


This will be a re-definition of the previoously referenced functions in `mathPix-to-moodle.sty`. Feel free to skip to 

[`ii.` handling](#ha).  

```python
filename = 'nonlin-fn-easy' # place in txt/ directory!
```


```python
import re
import os
import requests
import pandas as pd
```


#### `i.i` - `read` {#re}
```python
def read(file_path):
    with open(file_path, 'r', encoding='utf-8') as file:
        content = file.read()
    return content
```
#### `i.ii` - `no_rationale` {#no}
The `no_rationale` tool removes the string starting with `\section*{Rationale}` inclusive and ending with `\section*{` exclusive.


```python
def no_rationale(text):
    # This pattern looks for '\section*{Rationale}' and removes everything until the next '\section*{' or end of string
    text = re.sub(r'\\section\*\{Rationale\}.*?(?=\\section\*|\Z)', '', text, flags=re.DOTALL)
    return text
```

#### `i.iii` - `<ans>` {#ans}
An answer is comprised of either literals `A, B, C, D` or combinations of numbers, signs `-`, fractions `/`, decimals `.`, and delimiters `,` for `shortanswer` questions.


```python
def fetch_ans(text):
    # Capture everything after 'Correct Answer:' until an invalid character is encountered, including whitespaces
    match = re.search(r'Correct\s*Answer:\s*([A-D0-9/.,\s-]+)', text, re.IGNORECASE)
    return match.group(1).strip() if match else None
```
####  `i.iv` - `<id>` {#id}

The question `id` is fetched by looking up instances of the string

- `\section*{Question ID`
- `ID:`

and assigning `<ans>` to be the following string.


```python
def fetch_id(text):
    # Look for the question ID in both formats: section command and plain text
    match = re.search(r'\\section\*\{Question ID\s+(\w+)\}|ID:\s*(\w+)', text)
    if match:
        return match.group(1) or match.group(2)  # Return the matched ID from either format
    return None
```
####  `i.v` - `<body>` {#bo}
A question body should between the two strings

- `\section*{ID: <q-id>}`
- `\section*{ID: <q-id> Answer}` or `A.`

exclusive.


```python
def fetch_body(text, question_id):
    # Pattern to capture everything between \section*{ID: <question-id>} or ID: <question-id>
    # and the start of the choices (A., B., C., D.) or the start of the section for the answer
    pattern = rf'(\\section\*\{{ID: {question_id}\}}|ID: {question_id})\s*(.*?)(?=\s*[A-D]\.\s|\s*\\section\*\{{ID: {question_id} Answer\}}|ID: {question_id} Answer)'
    match = re.search(pattern, text, flags=re.DOTALL)
    
    if match:
        body = match.group(2).strip()  # Group 2 contains the body content
        if body.startswith('$') and not body.startswith('$$'):
            body = re.sub(r'^\$(.*?)\$', r'$$\1$$', body, count=1)            
        return body

    return None
```

#### `i.vi` - `<type>` {#ty}
The question type is determined by the existence of an isolated `A.`, `B.`, `C.` or `D.`. The type is subsequently multiple-choice if one of the literals exist, and short-answer otherwise.


```python
def fetch_qtype(answer):
    # Determine if the question is a short answer or multiple-choice based on the answer format
    if re.search(r'\s*\b[a-dA-D]\b\s*', answer):  # Checks for isolated A, B, C, or D with optional surrounding spaces
        return "multi"
    else:
        return "shortanswer"  # If not, it's immediately a shortanswer
```

#### `i.vii` - `shortanswer` {#sh}
The `shortanswer` function formats a short-answer-type question as per `moodle.sty`.


```python
def shortanswer(question_id, question_body, answer):
    # Split answer on comma to handle multiple answers if present
    answers = answer.split(",")  # This will give a list of answers

    # Initialize the formatted string for shortanswer
    formatted = f"\\begin{{shortanswer}}[fraction=100]{{{question_id}}}\n{question_body}\n"
    
    # Add each answer as a separate item with fraction=100
    for ans in answers:
        formatted += f"\\item[fraction=100] {ans.strip()}\n"
    
    # Close the shortanswer environment and add \newpage at the end
    formatted += "\\end{shortanswer}\n\\newpage"
    
    return formatted

```


#### `i.viii` - `multi` {#mu}

##### `choice` {#ch}

The `choice` function fetches all the choices in a question.


```python
def choice(text):
    # Adjusted pattern to capture choices starting with A., B., C., D., without look-behind
    pattern = r'(?:^|\s)([A-D]\.\s)(.*?)(?=(?:\s[A-D]\.\s)|(?:\\section\*\{ID:)|$)'

    # Find all matches for choices
    matches = re.findall(pattern, text, flags=re.DOTALL)

    choices = []
    for _, match in matches:
        # Clean up line breaks within each choice text and strip whitespace
        choice_text = match.replace('\n', ' ').strip()
        choices.append(choice_text)
    
    return choices if choices else None
```

`multi` finally returns a `moodle.sty` formatted multiple-choice question.


```python
def multi(question_id, question_body, answer, choices):
    # Map answer to corresponding choice and format multiple-choice question
    answer_index = ord(answer) - ord('A')
    items = [f"\\item * {choice}" if i == answer_index else f"\\item {choice}" for i, choice in enumerate(choices)]
    formatted_items = "\n".join(items)
    
    formatted = f"""\\begin{{multi}}{{{question_id}}}
{question_body}

{formatted_items}
\\end{{multi}}
\\newpage"""  # Add \newpage at the end
    
    return formatted
```
#### `i.ix` - `parse` {#pa}


Main function to orchestrate parsing and formatting...


```python
def parse(text):
    text = no_rationale(text)
    question_id = fetch_id(text)
    answer = fetch_ans(text)
    question_body = fetch_body(text, question_id)
    
    # Check which part is missing and provide detailed feedback
    if not question_id:
        print("question_id"+str(question_id))
        return "Error: Unable to parse question ID."
    elif not question_body:
        return "Error: Unable to parse question body."
    elif not answer:
        return "Error: Unable to parse answer."

    question_type = fetch_qtype(answer)
    if question_type == "shortanswer":
        return shortanswer(question_id, question_body, answer)
    elif question_type == "multi":
        choices = choice(text)
        if choices and len(choices) == 4:
            return multi(question_id, question_body, answer, choices)
        else:
            return "Error: Multiple-choice question lacks choices or has an incorrect format."
    else:
        return "Error: Unknown question type or unrecognized answer format."
```

# `ii.` handling {#ha}

`mathPIX` is a great tool, but is prone to error. Here, we start to make quality checks on the parsed questions.

### `ii.i` processing {#pro}

Start by reading the contents of the converted mathPix `.txt` file. Always check that your `.txt` file is in the `txt/` directory; or more generally, in a directory that could be read.

*N.B: The variable `txt` is defined in the first block.* 


```python
with open("txt/" + filename + ".txt", 'r', encoding='latin-1') as file:
    txt = file.read()   
```

We start by separating questions.


```python
def q_split(text):
    # Split the text by lines and initialize a list for questions
    lines = text.splitlines()
    questions = []
    current_question = []

    # Insert a marker line to handle the first question correctly
    lines.insert(0, "START")

    # Regular expression to detect "\section*{Question ID" with possible line breaks or spaces
    question_start_pattern = re.compile(r'\\section\*\{\s*Question\s+ID', re.IGNORECASE)

    for line in lines:
        # Check if the line matches the start of a question with flexible spacing/newlines
        if question_start_pattern.search(line):
            # If current_question has content, save it as a complete question
            if current_question:
                questions.append("\n".join(current_question).strip())
                current_question = []  # Reset for the next question
        # Add line to the current question content
        current_question.append(line)

    # Add any remaining question content after the loop finishes
    if current_question:
        questions.append("\n".join(current_question).strip())

    # Return all questions except the first marker
    return questions[1:]  # Skip the initial marker question
```

One may hope for `n` to be the actual number of questions in the document.


```python
question = q_split(txt)
n = len(question)
print(n)
```

    25
    

If result is not equal to the number questions, then the string `Question ID` must be carefully checked, since two (or more) questions are counted as one.  

### `ii.ii` the `\section*{ID: ` string {#th}

Earlier, we discussed how the question `id` is read using the following setup,

```
\section*{ID: <id>}

<body>
<choices> %if any

\section*{ID: <id> Answer}

```
This is not immediately always the case. With `mathPIX,` there are instances where we get
```
ID: <id>

<body>
<choices> %if any

\section*{ID: <id> Answer}

```
without the `\section*{` string.  

The `choice` function responsible for fetching choices works by scanning initials A-D from `\section*{` to `\section*{`. Therefore, we would like to manually add this string to standalone `ID: <id>` instances for the sake of consistency.


```python
def section_string(text):
    # Look for standalone "ID:" not already in \section*{...}
    # Using a negative lookbehind to ensure it’s not preceded by "\section*{"
    s = re.sub(r'(?<!\\section\*\{)ID:', r'\\section*{ID:', text)
    
    return s
```


```python
for i in range(n):
    question[i] = section_string(question[i])
```

### `ii.iii` - `tabular` environments {#ta}

We are then faced with two tabular problems,

- `moodle.sty` cannot handle tables properly. Therefore we need to embed them as pictures when compiling the $$\LaTeX$$ file.
- `mathPix` includes tables on top which are unnecessary.

This is easy to handle. We use `tabular_handling` to

- get rid of the annoying tables that indicate the difficulty, skill and assessment, as in

<div style="text-align: center;">
<img src="/assets/img/mathPIX-to-moodle.sty/download.png" class="img-fluid rounded z-depth-1" style="width: 70%; height: auto;" />
</div>
<div style="margin-top: 15px;"></div>
- use `\embedaspict{}` for tabular items, which embeds the contents of its two braces as a picture upon compilation.


This is effectively done by deleting tables with the string `Assessment & Test & Domain & Skill & Difficulty`, and wrapping with `\embedaspict{}` otherwise.


```python
def tabular_handling(text):
    # Define the regex pattern to match the entire tabular environment
    pattern = r'\\begin\{tabular\}.*?\\end\{tabular\}'

    # Use re.findall to get all instances of tabular environments
    tabular_instances = re.findall(pattern, text, flags=re.DOTALL)

    # Check each instance and remove it if it contains the specified string
    for tabular in tabular_instances:
        if "Assessment & Test & Domain & Skill & Difficulty" in tabular:
            text = text.replace(tabular, '')
        else:
            # Wrap tabular in \begin{center} \end{center} and \embedaspict{...}
            wrapped_tabular = f"\\begin{{center}}\n\\embedaspict{{\n{tabular}\n}}\\end{{center}}"
            text = text.replace(tabular, wrapped_tabular)
    return text.strip()
```


```python
for i in range(n):
    question[i]= tabular_handling(question[i])
```

**Disclaimer.** This code snippet behaves badly with nested `\tabular` environments. Especially a problem, as `mathPIX` inherently writes nested `\tabular` environments.

# `iii.` bad parse {#bp}

We classify inaccuracies into

1. **coding**, which arise from uncareful handling

We pass on our questions for parsing and see which ones are _bad_. Our `parse` function is the same one we built for single-questions, and is well-designed to trace errors.

2. **logic**, which cannot be indicated through code.

There is little we can do to automate the process - one must carefully review the final output. 


```python
def bad_parse(questions):
    bad_index = []

    for i in range(n):
        # parse every question
        result = parse(questions[i])  
        # if error 
        if "Error:" in result:  
            # append the bad index i
            bad_index.append(i) 
    return bad_index
```


```python
bad_questions = bad_parse(question)
print(bad_questions)
```

    []
    
### `iii.i` common parsing issues {#cpi}

With a list of _bad_ questions, we inspect on our way to a more complicated construction. Here are all troubleshooting cases I faced.

1. **`Correct Answer: <ans>` segment of the question is not supplied.** 

This happens sometimes with questions. The fix is to have the `pdf` document to manually supply `<ans>`.

2. **`<body>` cannot be supplied.** 

In this case, you need to manually include `\section*{ID: <id>}` and `\section*{ID: <id> Answer}` yourself around the question body.

3. **`<choice>` cannot be supplied.** 

This occurs as a result of a mis-identified literal, e.g a sentence that ends with a variable `c.`. It conflicts with The solution in that case is to wrap `$c.$` in `math` to avoid `regex` detection.

4. **Bad `<char>` in text.** 

If there is a bad character that e.g. prevents `fetch_body` from detecting a character `A`, a simple fix is to backspace the suspected area and rewrite the character.

5. **MathPix renders `<id>` in LaTeX.** 

This means, `<id>` = `$`$c_1 c_2 c_3\quad$  $c_4c_5\quad$ $c_6c_7c_8$  `$`. The issue is simply fixed by removing `$$` and glueing `<id>` back in place.  



6. **`Question ID <id>`$\neq$`ID:<id>`**. 

Rare, happens with instances as Cc or 0Oo.

#### `iii.ii` recommended workflow {#rw}

- Keep your `.txt`, `.pdf` files on standby. 
- Fetch question `id` using `q_id`.
- Detect which component cannot be fetched (`id`, `type`, `ans`, `choice`, ...).
- Look up `id` in your `.txt` file.
- Fix the problem using one of the common troubleshooting cases.

upon appropriate changes to the `.txt` file, you should restart the notebook and observe the behaviour.


```python
q = question[0] #debug here by question, default = 0 

q_type = fetch_qtype(q); 
q_id = fetch_id(q); 
q_parse = parse(q);  
q_ans = fetch_ans(q)
q_choices = choice(q)
q_body = fetch_body(q, q_id)

# multi only
q_format = multi(q_id, q_body, q_ans, q_choices)


### COMMENT OUT WHAT YOU DON'T NEED

print(
      "id: " + str(q_id) + "\n" + 
      "type: " + str(q_type)+ "\n" +
      "ans: "+ str(q_ans) + "\n" +
      "choices: "+ str(q_choices) + "\n---\n" +
      "body: " + "\n\n"+ str(q_body) + "\n---\n" +    
      "parse result:" + "\n\n" +str(q_parse)+ "\n==="
)
```

    id: 6abec9a8
    type: multi
    ans: B
    choices: ['$(-1,-9)$', '$(0,-5)$', '$(0,-4)$', '$(0,0)$']
    ---
    body: 
    
    ![](https://cdn.mathpix.com/cropped/2024_11_04_03d5d09e2296f53345b9g-01.jpg?height=857&width=866&top_left_y=671&top_left_x=121)
    
    What is the $y$-intercept of the graph shown?
    ---
    parse result:
    
    \begin{multi}{6abec9a8}
    ![](https://cdn.mathpix.com/cropped/2024_11_04_03d5d09e2296f53345b9g-01.jpg?height=857&width=866&top_left_y=671&top_left_x=121)
    
    What is the $y$-intercept of the graph shown?
    
    \item $(-1,-9)$
    \item * $(0,-5)$
    \item $(0,-4)$
    \item $(0,0)$
    \end{multi}
    \newpage
    ===
    

# `iv.` media {#me}

By now we should be fairly ready to start parsing into $$\LaTeX.$$


```python
import os
import requests
import re
import shutil
```

#### `iv.i` directories {#di}

The first thing we would do now is create a directory with our `.tex` file. This is necessary due to the way that $$\LaTeX$$ handles file referencing. We would like to also overwrite any existing directory upon re-run.

```python
directory = "TeX"+"-"+filename

if os.path.exists(directory):
    shutil.rmtree(directory)
    print(f"{directory} directory and its contents have been deleted.")
else:
    print(f"{directory} directory does not exist. Creating...")
    os.makedirs(directory) 

# Full path to the .tex file inside the "TeX" folder
tex_path = os.path.join(directory, filename + ".tex")
```

    TeX-nonlin-fn-easy directory and its contents have been deleted.
    

The next command creates a directory of assets.


```python
directory = directory +"/assets"

# Create the nested directories if they don't already exist
if not os.path.exists(directory):
    os.makedirs(directory)
```

#### `iv.ii` action plan {#ap}

We would like to

- detect all hyperlinks to media files
- download the media files to `assets`
- name each media file according to the hyperlink's `<img-name>.jpg`
- extract height & width for proper scaling
- replace the URL with `\includegraphics[width=<width>, height=<height>]{<image_path>}`

using one function! A lot, right?


```python
def media(question, directory=directory):
    # Ensure 'question' is a string. If it's a list, process each item in the list.
    if isinstance(question, list):
        return [media(q, directory) for q in question]  # Recursively apply the function to each item
    
    # Find the image URL in the question
    matches = re.findall(r"(https://cdn\.mathpix\.com/[^\s]+?\.jpg(\?[^)]*)?)", question)
    
    result = question  # Start with the original question text
    
    for match in matches:
        url = match[0]
        
        # Extract the filename from the URL (without the date part and without underscores)
        filename = url.split('/')[-1].split('?')[0]  # e.g., "2024_11_04_a374deafc4bc3bc8f639g-10.jpg"

        # Remove the date part (everything before the first underscore)
        filename = re.sub(r'^\d{4}_\d{2}_\d{2}_', '', filename)  # Remove "2024_11_04_"
        
        # Remove underscores for LaTeX
        filename = filename.replace("_", "")
        
        # Use the directory as is (no replacement of backslashes here)
        path = directory
        
        # Escape underscores in the directory part
        path = path.replace("_", r"\_")
        
        # Combine the path and filename
        file_path = os.path.join(path, filename)
        
        # Create the directory if it does not exist
        os.makedirs(path, exist_ok=True)
        
        # Extract dimensions from the URL query parameters if they exist
        dimensions = {}
        if '?' in url:
            query_params = url.split('?')[-1]
            params = dict(re.findall(r"(\w+)=([\d]+)", query_params))
            dimensions = {
                "height": int(params.get("height", 0)),
                "width": int(params.get("width", 0)),
            }
        
        # Download and save the image
        try:
            response = requests.get(url)
            response.raise_for_status()
            
            # Save the image to the correct directory
            with open(file_path, "wb") as img_file:
                img_file.write(response.content)
            print(f"Downloaded and saved: {file_path}")
            
            # Prepare LaTeX includegraphics line
            if dimensions.get("height") and dimensions.get("width"):
                # Divide width and height by f
                f = 3
                width = dimensions['width'] / f
                height = dimensions['height'] / f

                # Replace backslashes and clean up the path format
                file_path = file_path.replace("\\", "/")  # Use forward slashes in LaTeX path
                #file_path = file_path.split('assets/', 1)[-1]
                #file_path = 'assets/' + file_path
                latex_command = f"\\includegraphics[width={width}px, height={height}px]{{{file_path}}}"
                
                # Replace the URL in the question with LaTeX command
                result = result.replace(f"![]({url})", latex_command)
            
        except requests.exceptions.RequestException as e:
            print(f"Failed to download {url}: {e}")
    
    return result
```

The `media` function leaves media-free questions unaffected.


```python
for i in range(n):
    question[i] = media(question[i])
```

    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-01.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-04.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-04.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-04.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-04.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-08.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-10.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-11.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-14.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-16.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-18.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-19.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-19.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-19.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-19.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-22.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-26.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-27.jpg
    Downloaded and saved: TeX-nonlin-fn-easy/assets\03d5d09e2296f53345b9g-28.jpg
    

# `v.` closing words {#cl}

Let us collect all parsed questions in one string, separated by two lines.


```python
latex = ""
for i in range(n):
    temp = parse(question[i])
    latex += temp + "\n\n"
```


```python
with open(tex_path, "a") as file:
    file.write(latex)
```

Now that the questions are ready, one only needs to compile into `moodle.sty`. Even with a considerable human element of reviewing the `.txt` file for consistency, the solution has spared me much more time than I had originally invested. 

Instead of dealing with 1000 questions, I only had to deal with 70-80 badly parsed questions. Even then, I did not have to start from scratch, but implement simple edits to the `.txt` file. 

$$\LaTeX$$, `RegEx`, `moodle`, `mathPIX`, and `SAT`. Who would have thought?   $$\boxed \xi$$

{% endraw %}

