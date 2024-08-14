from flask import Flask, request, jsonify
import subprocess

app = Flask(__name__)

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
    if script:
        # Assuming the script is in the current directory or specify the full path if not
        result = subprocess.run(["python3", script], capture_output=True, text=True)
        return jsonify({'output': result.stdout})
    return jsonify({'error': 'No script specified'})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)  # Accessible from any IP address
