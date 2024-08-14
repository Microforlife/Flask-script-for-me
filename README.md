import tkinter as tk
import subprocess
import os
import threading
from flask import Flask, request, jsonify

# Initialize Flask app
app = Flask(__name__)

# Tkinter app setup
root = tk.Tk()
root.title("My Raspberry Pi App")

def run_script(script_name):
    # Function to run a script and show output in Tkinter
    script_path = os.path.join("/path/to/scripts", script_name)
    result = subprocess.run(["python3", script_path], capture_output=True, text=True)
    print(result.stdout)  # Replace with Tkinter widget update logic

# Flask route for remote control
@app.route('/run_script', methods=['POST'])
def run_script_remote():
    script = request.form.get('script')
    if script:
        run_script(script)
        return jsonify({'status': 'Script executed'})
    return jsonify({'status': 'No script specified'})

def start_flask_app():
    app.run(host='0.0.0.0', port=5000)

# Start Flask app in a new thread
flask_thread = threading.Thread(target=start_flask_app)
flask_thread.start()

# Tkinter main loop
root.mainloop()
