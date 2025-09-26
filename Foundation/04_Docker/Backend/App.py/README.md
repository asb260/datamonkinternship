import os
import time
from flask import Flask, jsonify, request
from flask_cors import CORS
import psycopg2

app = Flask(__name__)
CORS(app) 

# --- Database Configuration ---
DB_HOST = os.environ.get('DB_HOST', 'db')
DB_USER = os.environ.get('DB_USER', 'postgres')
DB_PASSWORD = os.environ.get('DB_PASSWORD', 'password')
DB_NAME = os.environ.get('DB_NAME', 'postgres')

def get_db_connection():
    """Tries to establish a connection to the PostgreSQL database."""
    conn = None
    # Use a loop to wait for the database service to be ready
    for i in range(10):
        try:
            conn = psycopg2.connect(
                host=DB_HOST,
                database=DB_NAME,
                user=DB_USER,
                password=DB_PASSWORD,
                port="5432"
            )
            print("INFO: Successfully connected to the database!")
            return conn
        except psycopg2.OperationalError as e:
            print(f"WARNING: Database connection failed (attempt {i+1}/10). Retrying in 2 seconds...")
            time.sleep(2)
    raise Exception("Failed to connect to the database after multiple retries.")

def init_db():
    """Ensures the 'todos' table exists."""
    conn = get_db_connection()
    with conn.cursor() as cur:
        cur.execute("""
            CREATE TABLE IF NOT EXISTS todos (
                id SERIAL PRIMARY KEY,
                title VARCHAR(255) NOT NULL,
                completed BOOLEAN DEFAULT FALSE
            );
        """)
        conn.commit()
    conn.close()

# Initialize the database table when the API starts
with app.app_context():
    init_db()

# --- API Endpoints (CRUD) ---

@app.route('/todos', methods=['GET'])
def get_todos():
    conn = get_db_connection()
    with conn.cursor() as cur:
        cur.execute("SELECT id, title, completed FROM todos ORDER BY id DESC;")
        items = cur.fetchall()
        # Convert results to a list of dictionaries
        todos = [
            {"id": item[0], "title": item[1], "completed": item[2]}
            for item in items
       ]
        conn.close()
        return jsonify(todos)

@app.route('/todos', methods=['POST'])
def create_todo():
    data = request.json
    title = data.get('title')
    if not title:
        return jsonify({"error": "Title is required"}), 400

    conn = get_db_connection()
    with conn.cursor() as cur:
        cur.execute("INSERT INTO todos (title) VALUES (%s) RETURNING id;", (title,))
        new_id = cur.fetchone()[0]
        conn.commit()
        conn.close()
        return jsonify({"id": new_id, "title": title, "completed": False}), 201

@app.route('/todos/<int:id>', methods=['PUT'])
def update_todo(id):
    data = request.json
    title = data.get('title')
    completed = data.get('completed')

    conn = get_db_connection()
    with conn.cursor() as cur:
        if title is not None and completed is not None:
            cur.execute("UPDATE todos SET title=%s, completed=%s WHERE id=%s;", (title, completed, id))
        elif title is not None:
            cur.execute("UPDATE todos SET title=%s WHERE id=%s;", (title, id))
        elif completed is not None:
            cur.execute("UPDATE todos SET completed=%s WHERE id=%s;", (completed, id))

        # Check if any row was updated
        if cur.rowcount == 0:
            conn.close()
            return jsonify({"error": "Todo not found"}), 404

        conn.commit()
        conn.close()
        return jsonify({"message": "Todo updated"}), 200

@app.route('/todos/<int:id>', methods=['DELETE'])
def delete_todo(id):
    conn = get_db_connection()
    with conn.cursor() as cur:
        cur.execute("DELETE FROM todos WHERE id=%s;", (id,))

        if cur.rowcount == 0:
            conn.close()
            return jsonify({"error": "Todo not found"}), 404

        conn.commit()
        conn.close()
        return jsonify({"message": "Todo deleted"}), 204

if __name__ == '__main__':
    # Flask app is run directly, but CMD in Dockerfile handles this in production setup
    app.run(host='0.0.0.0', port=5000)




