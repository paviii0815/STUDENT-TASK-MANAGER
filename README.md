<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Student Task Manager</title>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f1f5f9;
      margin: 0;
      padding: 30px 15px;
      color: #1e293b;
    }

    .container {
      max-width: 500px;
      margin: auto;
      background: white;
      padding: 25px;
      border-radius: 16px;
      box-shadow: 0 5px 20px #0001;
    }

    h1 {
      text-align: center;
      color: #4f46e5;
    }

    .subtitle {
      text-align: center;
      color: #64748b;
    }

    .task-input {
      display: flex;
      gap: 8px;
      margin: 25px 0;
    }

    input {
      flex: 1;
      min-width: 0;
      padding: 12px;
      border: 1px solid #cbd5e1;
      border-radius: 8px;
    }

    button {
      padding: 11px 14px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      background: #4f46e5;
      color: white;
    }

    ul {
      padding: 0;
      list-style: none;
    }

    li {
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 12px 0;
      border-bottom: 1px solid #e2e8f0;
    }

    li span {
      flex: 1;
      overflow-wrap: anywhere;
    }

    li.done span {
      text-decoration: line-through;
      color: #64748b;
    }

    .delete {
      background: #dc2626;
    }

    #summary {
      text-align: center;
      margin-top: 20px;
      color: #475569;
    }
  </style>
</head>

<body>
  <main class="container">
    <h1>My Task Manager</h1>
    <p class="subtitle">Plan your day. Complete your goals.</p>

    <form id="taskForm" class="task-input">
      <input id="taskInput"
        type="text"
        placeholder="Enter a new task..."
        required>
      <button type="submit">Add</button>
    </form>

    <ul id="taskList"></ul>
    <p id="summary">0 tasks remaining</p>
  </main>

  <script>
    const form = document.getElementById("taskForm");
    const input = document.getElementById("taskInput");
    const list = document.getElementById("taskList");
    const summary = document.getElementById("summary");

    let tasks = JSON.parse(
      localStorage.getItem("studentTasks") || "[]"
    );

    function saveTasks() {
      localStorage.setItem("studentTasks", JSON.stringify(tasks));
    }

    function renderTasks() {
      list.replaceChildren();

      tasks.forEach((task, index) => {
        const item = document.createElement("li");
        const checkbox = document.createElement("input");
        const text = document.createElement("span");
        const removeButton = document.createElement("button");

        item.className = task.done ? "done" : "";
        checkbox.type = "checkbox";
        checkbox.checked = task.done;
        text.textContent = task.name;
        removeButton.textContent = "Delete";
        removeButton.className = "delete";

        checkbox.addEventListener("change", () => {
          tasks[index].done = checkbox.checked;
          saveTasks();
          renderTasks();
        });

        removeButton.addEventListener("click", () => {
          tasks.splice(index, 1);
          saveTasks();
          renderTasks();
        });

        item.append(checkbox, text, removeButton);
        list.appendChild(item);
      });

      const remaining = tasks.filter(task => !task.done).length;
      summary.textContent =
        `${remaining} tasks remaining out of ${tasks.length}`;
    }

    form.addEventListener("submit", event => {
      event.preventDefault();

      const name = input.value.trim();
      if (!name) return;

      tasks.push({ name, done: false });
      saveTasks();
      renderTasks();
      input.value = "";
      input.focus();
    });

    renderTasks();
  </script>
</body>
</html>
