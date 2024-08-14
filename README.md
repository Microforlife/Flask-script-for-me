import tkinter as tk
import threading
from flask import Flask, request, jsonify

# Initialize Flask app
app = Flask(__name__)

# Global variable to hold the Tkinter app instance
tk_instance = None

# Function to perform when the button is pressed
def on_button_click():
    # Example action: print a message or execute some logic
    print("Button was clicked!")

# Flask route to trigger the button click remotely
@app.route('/trigger_button', methods=['POST'])
def trigger_button():
    if tk_instance is not None:
        tk_instance.event_generate("<<RemoteButtonClick>>")
        return jsonify({'status': 'Button triggered successfully!'})
    return jsonify({'error': 'Tkinter app is not running'})

# Tkinter App Setup
def create_tkinter_app():
    global tk_instance
    root = tk.Tk()
    tk_instance = root
    root.title("Programele.py")

    # Label in Tkinter app
    label = tk.Label(root, text="This is the Programele.py Tkinter App")
    label.pack(pady=20)

    # Button in Tkinter app
    button = tk.Button(root, text="Click Me", command=on_button_click)
    button.pack(pady=20)

    # Bind a custom event for remote triggering
    root.bind("<<RemoteButtonClick>>", lambda e: on_button_click())

    # Start the Tkinter loop
    root.mainloop()

# Function to start Flask app
def start_flask_app():
    app.run(host='0.0.0.0', port=5000)

# Start Flask app in a new thread so it doesn't block the Tkinter loop
flask_thread = threading.Thread(target=start_flask_app)
flask_thread.daemon = True  # Ensures the thread exits when the main program does
flask_thread.start()

# Start the Tkinter app
create_tkinter_app()
