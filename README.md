# 📚 CrewAI-Poem-Writer-With-UV

# 🔹 Step 1: Create a Project Folder
First, create a new folder where you will store your project:

```bash
mkdir CrewAI-Poem-Writer

```
# Navigate into the folder:
```bash
cd CrewAI-Poem-Writer

```
# 🔹 Step 2: Open the Project in VS Code
Now, open VS Code in this folder:
```bash
code .

```
# 🔹 Step 3: Create a .env File with mentioned gemini model 
Inside your CrewAI-Poem-Writer folder, create a .env file:
```bash
GEMINI_API_KEY=your_gemini_api_key
MODEL =  gemini/gemini-1.5-flash

```
# 🔹 Step 4: Create a Python Virtual Environment
Create a virtual environment to manage dependencies:
```bash
python -m venv venv

```
# 🔹 Windows (cmd/PowerShell):
```bash
venv\Scripts\activate

```
# 🔹 Step 5: Create the Main Python Script
Inside the CrewAI-Poem-Writer folder, create a new Python file:
```bash
touch main.py
```
# Open main.py in VS Code and add the following code:
```bash
#!/usr/bin/env python
from random import randint

from pydantic import BaseModel

from crewai.flow import Flow, listen, start

from project4.crews.poem_crew.poem_crew import PoemCrew


class PoemState(BaseModel):
    sentence_count: int = 1
    poem: str = ""


class PoemFlow(Flow[PoemState]):

    @start()
    def generate_sentence_count(self):
        print("Generating sentence count")
        self.state.sentence_count = randint(1, 5)

    @listen(generate_sentence_count)
    def generate_poem(self):
        print("Generating poem")
        result = (
            PoemCrew()
            .crew()
            .kickoff(inputs={"sentence_count": self.state.sentence_count})
        )

        print("Poem generated", result.raw)
        self.state.poem = result.raw

    @listen(generate_poem)
    def save_poem(self):
        print("Saving poem")
        with open("poem.txt", "w") as f:
            f.write(self.state.poem)


def kickoff():
    poem_flow = PoemFlow()
    poem_flow.kickoff()


def plot():
    poem_flow = PoemFlow()
    poem_flow.plot()


if __name__ == "__main__":
    kickoff()

```
# 🔹 Step 6: Open pyproject.toml
If you don’t already have this file, create it in your CrewAI-Poem-Writer folder:
Then open it in VS Code and add the following:
```bash
[project]
name = "project4"
version = "0.1.0"
description = "project4 using crewAI"
authors = [{ name = "Your Name", email = "you@example.com" }]
requires-python = ">=3.10,<3.13"
dependencies = [
    "crewai[tools]>=0.100.1,<1.0.0",
]

[project.scripts]
kickoff = "CrewAI-Poem-Writer.main:kickoff"
plot = "CrewAI-Poem-Writer.main:plot"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.crewai]
type = "flow"


