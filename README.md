import tkinter as tk
import subprocess
import threading
from flask import Flask, request, jsonify

# Initialize Flask app
app = Flask(__name__)

# Tkinter App Setup
def create_tkinter_app():
    root = tk.Tk()
    root.title("Programele.py")
    
    # Example UI elements
    label = tk.Label(root, text="This is the Programele.py Tkinter App")
    label.pack(pady=20)
    
    def on_exit():
        root.quit()
        root.destroy()

    exit_button = tk.Button(root, text="Exit", command=on_exit)
    exit_button.pack(pady=20)
    
    root.mainloop()

# Flask routes for remote control
@app.route('/')
def index():
    return '''
        <h1>Remote Control</h1>
        <form action="/run_script" method="post">
            <button type="submit" name="script" value="Programele.py">Run Programele.py</button>
        </form>
    '''

@app.route('/run_script', methods=['POST'])
def run_script():
    script = request.form.get('script')
    if script == "Programele.py":
        # You could trigger any specific function here
        # Since Tkinter is already running, this might not do anything
        return jsonify({'output': 'Programele.py is already running'})
    return jsonify({'error': 'No script specified or incorrect script name'})

# Function to start Flask app
def start_flask_app():
    app.run(host='0.0.0.0', port=5000)

# Start Flask app in a new thread so it doesn't block the Tkinter loop
flask_thread = threading.Thread(target=start_flask_app)
flask_thread.daemon = True  # Ensures the thread exits when the main program does
flask_thread.start()

# Start the Tkinter app
create_tkinter_app()
