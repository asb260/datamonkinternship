<img width="2366" height="1519" alt="Screenshot from 2025-09-26 19-26-10" src="https://github.com/user-attachments/assets/95d8bbb1-8d22-40f7-b73c-dad579a860e7" />

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Docker Compose CRUD App</title>
    <!-- Load Tailwind CSS via CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #f1f5f9; }
    </style>
</head>
<body class="p-6 md:p-10 min-h-screen bg-gray-50">

    <div class="max-w-xl mx-auto bg-white p-6 md:p-8 rounded-xl shadow-2xl">
        <h1 class="text-3xl font-bold text-gray-800 mb-6 text-center">
            Docker Compose Todo List
        </h1>
        <p class="text-center text-sm text-gray-500 mb-6">
            Frontend running on <span class="font-mono bg-gray-100 p-1 rounded">localhost:3000</span>, talking to Backend API on <span class="font-mono bg-gray-100 p-1 rounded">localhost:5000</span>.
        </p>

        <!-- CREATE Form -->
        <div class="flex space-x-3 mb-8">
            <input type="text" id="newTodoInput" placeholder="Add a new task..."
                   class="flex-grow p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition duration-150">
            <button onclick="addTodo()"
                    class="bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 px-5 rounded-lg shadow-md transition duration-200 transform hover:scale-[1.02]">
                Add Task
            </button>
        </div>

        <!-- READ List -->
        <div id="todoList" class="space-y-3">
            <!-- Todos will be injected here -->
            <p id="loadingMessage" class="text-center text-gray-400">Loading tasks...</p>
        </div>
    </div>

    <script>
        // NOTE: Since the frontend is served on host port 3000 and the backend is exposed on host port 5000,
        // we access the API using localhost:5000.
        const API_URL = 'http://localhost:5000/todos';
        const todoList = document.getElementById('todoList');
        const loadingMessage = document.getElementById('loadingMessage');

        // --- RENDER FUNCTIONS ---
        async function fetchTodos() {
            try {
                loadingMessage.classList.remove('hidden');
                const response = await fetch(API_URL);
                if (!response.ok) throw new Error('Failed to fetch data');
                const todos = await response.json();
                renderTodos(todos);
            } catch (error) {
                console.error("Error fetching todos:", error);
                // Enhanced error message for the user in the UI, pointing to the likely issue (CORS/API down)
                todoList.innerHTML = `<p class="text-red-500 text-center">
                    Error connecting to API (localhost:5000). Check two things:
                    <br>1. Is the 'backend' container running?
                    <br>2. **CORS Issue**: The backend API needs to allow requests from http://localhost:3000.
                    </p>`;
            } finally {
                loadingMessage.classList.add('hidden');
 }
        }

        function renderTodos(todos) {
            todoList.innerHTML = '';
            if (todos.length === 0) {
                todoList.innerHTML = `<p class="text-center text-gray-500 mt-4">No tasks yet. Add one above!</p>`;
                return;
            }

            todos.forEach(todo => {
                const item = document.createElement('div');
                item.className = 'flex items-center justify-between p-4 bg-white border border-gray-200 rounded-lg shadow-sm transition hover:shadow-md';
                item.innerHTML = `
                    <div class="flex items-center flex-grow">
                        <input type="checkbox" ${todo.completed ? 'checked' : ''}
                               onchange="toggleComplete(${todo.id}, this.checked)"
                               class="mr-4 h-5 w-5 text-blue-600 border-gray-300 rounded focus:ring-blue-500 cursor-pointer">
                        <span class="text-lg text-gray-800 ${todo.completed ? 'line-through text-gray-400' : ''}"
                              id="title-${todo.id}" onclick="editTitle(${todo.id}, '${todo.title}')"
                              style="cursor: pointer;">
                            ${todo.title}
                        </span>
                    </div>
                    <button onclick="deleteTodo(${todo.id})"
                            class="ml-4 text-red-500 hover:text-red-700 p-2 rounded-full transition duration-150">
                        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.8>
                    </button>
                `;
                todoList.appendChild(item);
            });
        }

        // --- CRUD FUNCTIONS ---

        // CREATE
        async function addTodo() {
            const input = document.getElementById('newTodoInput');
            const title = input.value.trim();
            if (!title) return;

            try {
                await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ title })
                });
                input.value = '';
                fetchTodos();
            } catch (error) {
                console.error("Error adding todo:", error);
                // Use console error instead of disruptive alert
            }
        }

        // UPDATE (Toggle Completed Status)
        async function toggleComplete(id, completed) {
            try {
                await fetch(`${API_URL}/${id}`, {
                    method: 'PUT',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ completed })
                });
                fetchTodos();
            } catch (error) {
             console.error("Error toggling todo:", error);
            }
        }

        // UPDATE (Edit Title - In-place editing)
        function editTitle(id, currentTitle) {
            const span = document.getElementById(`title-${id}`);
            const input = document.createElement('input');
            input.type = 'text';
            input.value = currentTitle;
            input.className = 'p-1 border border-blue-400 rounded-md focus:outline-none';

            input.onblur = async () => {
                const newTitle = input.value.trim();
                if (newTitle && newTitle !== currentTitle) {
                    try {
                        await fetch(`${API_URL}/${id}`, {
                            method: 'PUT',
                            headers: { 'Content-Type': 'application/json' },
                            body: JSON.stringify({ title: newTitle })
                        });
                    } catch (error) {
                        console.error("Error updating title:", error);
                    }
                }
                fetchTodos();
            };

            span.parentNode.replaceChild(input, span);
            input.focus();
        }


        // DELETE
        async function deleteTodo(id) {
            // Replaced disruptive confirm() with a custom solution message since confirm() is often blocked in iFrames
            const userConfirmed = window.prompt("Type 'DELETE' to confirm you want to delete this task:");
            if (userConfirmed !== 'DELETE') return;

            try {
                await fetch(`${API_URL}/${id}`, {
                    method: 'DELETE'
                });
                fetchTodos();
            } catch (error) {
                console.error("Error deleting todo:", error);
            }
        }

        // Initial load
        document.addEventListener('DOMContentLoaded', fetchTodos);
    </script>
</body>
</html>

