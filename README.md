<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CodeMentor Hub - Programming Education Platform</title>
  <!-- CodeMirror for code editor -->
  <link rel="stylesheet" href="https://unpkg.com/codemirror@5.65.17/lib/codemirror.css">
  <script src="https://unpkg.com/codemirror@5.65.17/lib/codemirror.js"></script>
  <script src="https://unpkg.com/codemirror@5.65.17/mode/python/python.js"></script>
  <script src="https://unpkg.com/codemirror@5.65.17/mode/javascript/javascript.js"></script>
  <script src="https://unpkg.com/codemirror@5.65.17/mode/clike/clike.js"></script> <!-- For Java/C++ -->

  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f7fb;
      margin: 0;
      padding: 0;
    }
    header {
      background: #007bff;
      color: white;
      padding: 1em;
      text-align: center;
      font-size: 1.5em;
    }
    nav {
      background: #e9ecef;
      padding: 0.5em;
      text-align: center;
    }
    nav a {
      margin: 0 1em;
      text-decoration: none;
      color: #007bff;
      font-weight: bold;
    }
    .container {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
      padding: 1em;
    }
    .section {
      background: white;
      border-radius: 8px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      margin: 1em;
      padding: 1em;
      flex: 1 1 300px;
      max-width: 600px;
    }
    h2 {
      color: #333;
      border-bottom: 2px solid #007bff;
      padding-bottom: 0.3em;
    }
    #editor {
      border: 1px solid #ddd;
      border-radius: 4px;
      min-height: 200px;
      margin-bottom: 1em;
    }
    button {
      background: #007bff;
      color: white;
      border: none;
      padding: 0.7em 1.2em;
      border-radius: 4px;
      cursor: pointer;
      margin: 0.5em 0;
    }
    button:hover {
      background: #0056b3;
    }
    .feedback {
      background: #fff3cd;
      border-left: 4px solid #ffc107;
      padding: 1em;
      margin-top: 1em;
      border-radius: 4px;
    }
    .success {
      background: #d4edda;
      border-left: 4px solid #28a745;
    }
    .exercises, .tutorials {
      background: #e9ecef;
      padding: 1em;
      border-radius: 4px;
    }
    .tutorial-step {
      margin-bottom: 1em;
    }
    select {
      padding: 0.5em;
      margin-bottom: 1em;
    }
  </style>
</head>
<body>
  <header>CodeMentor Hub: Learn Programming Interactively</header>
  <nav>
    <a href="#dashboard">Dashboard</a>
    <a href="#exercises">Exercises</a>
    <a href="#tutorials">Tutorials</a>
  </nav>
  <div class="container">
    <!-- Code Editor Section -->
    <div class="section" id="dashboard">
      <h2>Code Playground</h2>
      <select id="languageSelect">
        <option value="python">Python</option>
        <option value="javascript">JavaScript</option>
        <option value="text/x-java">Java</option>
        <option value="text/x-c++src">C++</option>
      </select>
      <textarea id="editor"></textarea><br>
      <button id="runBtn">Run & Analyze</button>
      <button id="debugBtn">Get Debugging Suggestions</button>
      <div id="output" class="feedback">Output will appear here.</div>
      <div id="errors" class="feedback"></div>
      <div id="suggestions" class="feedback"></div>
    </div>

    <!-- Exercise Generation Section -->
    <div class="section" id="exercises">
      <h2>Exercise Generator</h2>
      <select id="levelSelect">
        <option value="beginner">Beginner</option>
        <option value="intermediate">Intermediate</option>
        <option value="advanced">Advanced</option>
      </select>
      <button onclick="generateExercise()">Generate Exercise</button>
      <div id="exerciseBox" class="exercises"></div>
    </div>

    <!-- Interactive Tutorial Section -->
    <div class="section" id="tutorials">
      <h2>Interactive Tutorials</h2>
      <button onclick="nextTutorialStep()">Next Step</button>
      <div id="tutorialBox" class="tutorials"></div>
    </div>
  </div>

  <script>
    // CodeMirror Initialization
    const editor = CodeMirror.fromTextArea(document.getElementById("editor"), {
      lineNumbers: true,
      mode: "python",
      theme: "default",
      indentUnit: 4
    });

    // Language Switcher
    document.getElementById("languageSelect").addEventListener("change", function() {
      editor.setOption("mode", this.value);
    });

    // Run & Analyze Button
    document.getElementById("runBtn").onclick = function() {
      const code = editor.getValue();
      const lang = document.getElementById("languageSelect").value;
      const outputBox = document.getElementById("output");
      const errorsBox = document.getElementById("errors");
      let errors = [];
      let output = "Simulated Output: ";

      // Basic multi-language checks (simulated)
      if (code.trim() === "") {
        errors.push("Code is empty. Please write some code.");
      } else {
        if (lang === "python") {
          if (!code.includes("print(")) output += "No print statement found.";
          // Simulated error detection
          if (code.match(/print ['"]/g)) errors.push("Syntax error: Use print('text') in Python.");
        } else if (lang === "javascript") {
          if (!code.includes("console.log(")) output += "No console.log found.";
          if (code.match(/console\.log ['"]/g)) errors.push("Syntax error: Use console.log('text') in JS.");
        } else if (lang === "text/x-java") {
          if (!code.includes("System.out.println(")) output += "No System.out.println found.";
          if (code.match(/public class/)) output += "Class detected.";
        } else if (lang === "text/x-c++src") {
          if (!code.includes("cout <<")) output += "No cout statement found.";
          if (code.match(/#include/)) output += "Include detected.";
        }
        // General checks
        const lines = code.split('\n');
        lines.forEach((line, idx) => {
          if ((line.match(/\(/g) || []).length !== (line.match(/\)/g) || []).length) {
            errors.push(`Line ${idx+1}: Unmatched parentheses.`);
          }
        });
      }

      outputBox.innerHTML = errors.length === 0 ? `<div class="success">${output} (Run successful!)</div>` : output;
      errorsBox.innerHTML = errors.length > 0 ? `<strong>Errors Detected:</strong><ul>${errors.map(e => `<li>${e}</li>`).join('')}</ul>` : "";
    };

    // Debugging Suggestions Button
    document.getElementById("debugBtn").onclick = function() {
      const code = editor.getValue();
      const lang = document.getElementById("languageSelect").value;
      let suggestions = [];
      // Simulated suggestions
      if (lang === "python" && code.includes("for ") && !code.includes("range(")) {
        suggestions.push("In Python, use range() for numeric loops, e.g., for i in range(5):");
      }
      if (code.includes("if ") && !code.includes("else")) {
        suggestions.push("Consider adding an 'else' clause for complete conditional logic.");
      }
      if (code.match(/var /) && lang === "javascript") {
        suggestions.push("Use 'let' or 'const' instead of 'var' for better scoping in modern JS.");
      }
      document.getElementById("suggestions").innerHTML = suggestions.length > 0 ? `<strong>Debugging Suggestions:</strong><ul>${suggestions.map(s => `<li>${s}</li>`).join('')}</ul>` : "No suggestions available.";
    };

    // Exercise Generator
    const exercises = {
      beginner: [
        { title: "Hello World", desc: "Print 'Hello, World!' in your chosen language." },
        { title: "Add Numbers", desc: "Add two numbers and display the result." }
      ],
      intermediate: [
        { title: "FizzBuzz", desc: "Implement FizzBuzz up to 100." },
        { title: "Array Sum", desc: "Sum elements in an array/list." }
      ],
      advanced: [
        { title: "Binary Search", desc: "Implement binary search on a sorted array." },
        { title: "Linked List", desc: "Create a simple linked list class." }
      ]
    };
    function generateExercise() {
      const level = document.getElementById("levelSelect").value;
      const rand = Math.floor(Math.random() * exercises[level].length);
      const ex = exercises[level][rand];
      document.getElementById("exerciseBox").innerHTML = `<b>${ex.title}</b><p>${ex.desc}</p>`;
    }

    // Interactive Tutorials
    const tutorialSteps = [
      { title: "Step 1: Basics", content: "Start by writing a simple print statement in the editor." },
      { title: "Step 2: Variables", content: "Declare a variable and assign a value, then print it." },
      { title: "Step 3: Conditionals", content: "Use if-else to check a condition." },
      { title: "Step 4: Loops", content: "Write a loop to repeat an action." },
      { title: "Step 5: Functions", content: "Define and call a function." }
    ];
    let tIndex = 0;
    function nextTutorialStep() {
      const step = tutorialSteps[tIndex];
      document.getElementById("tutorialBox").innerHTML = `<div class="tutorial-step"><b>${step.title}</b><p>${step.content}</p></div>`;
      tIndex = (tIndex + 1) % tutorialSteps.length;
    }
    // Initial calls
    generateExercise();
    nextTutorialStep();
  </script>
</body>
</html>
