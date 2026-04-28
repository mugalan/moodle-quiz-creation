# Moodle AI Quiz Creation Cookbook
A lightweight Google Colab workflow for creating Moodle-compatible multiple-choice quiz XML files from AI-generated structured quiz data.
This project helps teachers, lecturers, and course designers quickly generate Moodle quiz questions using an AI assistant, convert them into Moodle XML format, download the XML file, and upload it directly into Moodle.
---
## Overview
This repository provides a Google Colab notebook that:
1. Takes a list of multiple-choice questions written as Python dictionaries.
2. Converts them into Moodle-compatible XML.
3. Saves the generated XML file.
4. Allows the XML file to be downloaded.
5. Lets the user upload the XML file into Moodle’s question bank or quiz import interface.
The workflow is designed to pair with an AI quiz-generation assistant that produces structured quiz data in a simple list-of-dictionaries format.
---
## Repository Contents
```text
.
├── Moodle_Ai_Quiz_creation_API_Cookbook_Public.ipynb
└── README.md

The main notebook contains the function create_moodle_quiz(questionsdict), which converts structured quiz-question dictionaries into Moodle XML, and save_quiz_xml(...), which saves the generated XML to a file.

⸻

Main Features

* Generate Moodle-compatible XML for multiple-choice questions.
* Use AI-generated quiz content with minimal formatting work.
* Supports one correct answer per question.
* Supports multiple incorrect answer choices.
* Automatically marks the correct answer with 100% credit.
* Marks all other choices with 0% credit.
* Uses Moodle’s multichoice question type.
* Enables shuffled answers.
* Saves XML output from inside Google Colab.
* Downloads XML file for direct Moodle upload.

⸻

Typical Workflow

The notebook follows this workflow:

1. Install required dependencies.
2. Generate quiz data using an AI quiz generator.
3. Paste the generated question dictionaries into the notebook.
4. Run the XML-generation function.
5. Save the XML file.
6. Download the XML file.
7. Upload the XML file into Moodle.

⸻

Requirements

The notebook is intended to run in Google Colab.

Required Python package:

pip install xmltodict

The notebook also uses:

copy
xmltodict
google.colab.files

google.colab.files is available automatically inside Google Colab.

⸻

Input Format

The quiz data should be provided as a Python list of dictionaries.

Each dictionary represents one multiple-choice question.

Each question dictionary should contain the following required keys:

Key	Description
name	Internal question name used by Moodle
grade	Default grade assigned to the question
question	Question text shown to the student
correct	Correct answer choice

Any additional non-empty keys are treated as incorrect choices.

Example:

questionsdict = [
    {
        "name": "Entropy and the Second Law",
        "grade": 50,
        "question": "According to the Second Law of Thermodynamics, which statement about entropy is correct?",
        "correct": "Entropy tends to increase over time.",
        "choice1": "Entropy always decreases spontaneously.",
        "choice2": "Entropy remains constant in all processes.",
        "choice3": "Entropy only changes during chemical reactions."
    },
    {
        "name": "Entropy Change in Melting Ice",
        "grade": 50,
        "question": "What happens to the entropy of the universe when ice melts in a glass of water at room temperature?",
        "correct": "The entropy of the universe increases.",
        "choice1": "The entropy of the universe decreases.",
        "choice2": "The entropy of the universe stays the same.",
        "choice3": "The entropy of the system decreases while the surroundings increase equally."
    }
]

⸻

Core Function

create_moodle_quiz(questionsdict)

Creates a Moodle-compatible XML quiz from a list of question dictionaries.

response = create_moodle_quiz(questionsdict)

Parameters

questionsdict : list[dict]

A list of dictionaries, where each dictionary defines one Moodle multiple-choice question.

Each dictionary must include:

"name"
"grade"
"question"
"correct"

Additional keys such as choice1, choice2, choice3, etc. are interpreted as incorrect answers.

⸻

Return Format

The function returns a dictionary:

{
    "status": "success",
    "response": {
        "meta_data": "Moodle XML quiz generated",
        "xml": "... generated XML string ...",
        "message": "Moodle XML quiz generated"
    }
}

You can access the XML string using:

xml_content = response.get("response", {}).get("xml", "")

⸻

Saving the XML File

The notebook includes a helper function:

save_quiz_xml(filename="quiz", xml_content="")

Example:

filename = "thermodynamics_quiz"
xml_content = response.get("response", {}).get("xml", "")
save_quiz_xml(filename=filename, xml_content=xml_content)

This creates:

thermodynamics_quiz.xml

⸻

Downloading the XML File in Google Colab

After saving the XML file, download it using:

from google.colab import files
files.download(f"{filename}.xml")

⸻

Uploading to Moodle

To import the generated quiz into Moodle:

1. Log in to your Moodle course.
2. Go to the course question bank or quiz import section.
3. Choose Import.
4. Select Moodle XML format.
5. Upload the generated .xml file.
6. Complete the import process.
7. Review the imported questions.

⸻

Example End-to-End Usage

!pip install xmltodict
import copy
import xmltodict
from google.colab import files
questionsdict = [
    {
        "name": "Entropy and the Second Law",
        "grade": 50,
        "question": "According to the Second Law of Thermodynamics, which statement about entropy is correct?",
        "correct": "Entropy tends to increase over time.",
        "choice1": "Entropy always decreases spontaneously.",
        "choice2": "Entropy remains constant in all processes.",
        "choice3": "Entropy only changes during chemical reactions."
    }
]
response = create_moodle_quiz(questionsdict)
print(f"Response status: {response.get('status', 'error')}")
print(f"Response message: {response.get('response', {}).get('message', 'error')}")
filename = "entropy_quiz"
xml_content = response.get("response", {}).get("xml", "")
save_quiz_xml(filename=filename, xml_content=xml_content)
files.download(f"{filename}.xml")

⸻

Generated Moodle Question Type

The notebook generates Moodle questions of type:

<question type="multichoice">

Each question is configured with:

* single correct answer
* shuffled answer order
* no answer numbering
* default correct feedback
* default incorrect feedback
* default partially correct feedback

The correct answer receives:

fraction="100"

Incorrect answers receive:

fraction="0"

⸻

Important Notes

1. Use valid XML-safe text

Moodle XML is sensitive to invalid characters. Avoid unescaped special characters such as:

&
<
>

For example, instead of:

A & B

use:

A and B

or ensure the character is properly escaped.

This is especially important when copying AI-generated content.

⸻

2. Be careful with LaTeX

If using LaTeX in Moodle questions or choices, use Moodle-compatible notation and ensure the XML remains valid.

For example:

"correct": "\$begin:math:text$ \\\\frac\{1\}\{3\} \\$end:math:text$"

In Python strings, backslashes must usually be escaped.

⸻

3. Question dictionaries are modified by the current implementation

The current implementation removes some keys from each question dictionary using pop().

That means the input object may be mutated during quiz generation.

To preserve the original data, call the function with a deep copy:

import copy
response = create_moodle_quiz(copy.deepcopy(questionsdict))

Alternatively, the function can be modified internally to avoid mutating the input.

⸻

4. Empty answer choices are skipped

If an answer choice has value "" or None, it is not included in the generated Moodle XML.

Example:

{
    "name": "Example",
    "grade": 1,
    "question": "Which answer is correct?",
    "correct": "A",
    "choice1": "B",
    "choice2": "",
    "choice3": None
}

Only A and B will be included.

⸻

Recommended AI Prompt Format

When using an AI assistant to generate questions, ask it to return a Python list of dictionaries in the following format:

Create 10 multiple-choice questions on [topic] for [student level].
Return only a Python list of dictionaries.
Each dictionary must have the keys:
"name", "grade", "question", "correct", "choice1", "choice2", "choice3".
Use plain text unless LaTeX is necessary.
Do not include explanations.
Do not include markdown fences.

Example:

Create 5 undergraduate sophomore-level multiple-choice questions on thermodynamic entropy.
Return only a Python list of dictionaries with keys:
"name", "grade", "question", "correct", "choice1", "choice2", "choice3".

⸻

Suggested Improvements

Future versions may include:

* XML character escaping and validation.
* Input schema validation.
* Automatic detection of missing required keys.
* Support for multiple correct answers.
* Support for numerical questions.
* Support for true/false questions.
* Support for short-answer questions.
* Category creation in Moodle XML.
* Feedback per answer choice.
* Randomized question banks by topic.
* Direct Moodle API upload.

⸻

Troubleshooting

Moodle says: Invalid character

This usually means one of the question fields contains a character that breaks XML parsing.

Common causes:

* unescaped &
* copied invisible Unicode characters
* malformed HTML
* malformed LaTeX
* incomplete quotes in the Python dictionary
* text copied from a PDF or web page containing hidden characters

Try replacing:

&

with:

and

or XML-safe:

&amp;

⸻

Moodle says: Error parsing XML

Check that:

1. The XML file was generated successfully.
2. The file extension is .xml.
3. The question text does not contain invalid XML characters.
4. The Python dictionary syntax is valid.
5. All string quotes are closed.
6. The XML was not manually edited incorrectly.

⸻

Python gives a syntax error

This usually means the pasted quiz dictionary is not valid Python.

Check for:

* missing commas between dictionary entries
* missing quotation marks
* unclosed strings
* unclosed braces {} or brackets []
* smart quotes copied from rich text editors

⸻

Moodle imports the questions but formatting looks wrong

Moodle may interpret question text as HTML. Check whether your text contains HTML-sensitive characters.

For clean formatting, keep question and answer text simple unless HTML or LaTeX is required.

⸻

Minimal Example

questionsdict = [
    {
        "name": "Basic Addition",
        "grade": 1,
        "question": "What is 2 + 2?",
        "correct": "4",
        "choice1": "3",
        "choice2": "5",
        "choice3": "6"
    }
]
response = create_moodle_quiz(questionsdict)
filename = "basic_addition"
xml_content = response["response"]["xml"]
save_quiz_xml(filename, xml_content)
files.download(f"{filename}.xml")

⸻

License

This project can be released under the MIT License.

Suggested LICENSE file:

MIT License
Copyright (c) 2026 mugalan
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files...

⸻

Acknowledgements

This notebook was designed as a simple AI-assisted Moodle quiz creation workflow. It combines structured AI-generated quiz content with Moodle XML export through Python and Google Colab.

