import os
import json
from flask import Flask, render_template, request, jsonify
app = Flask(__name__)
DATA_FILE = 'physics_db.json'
# Ensure the data file exists to avoid errors
if not os.path.exists(DATA_FILE):
    with open(DATA_FILE, 'w') as f:
        json.dump([], f)
@app.route('/')
def index():
    return render_template('index.html')
# Your secret URL for "Training" (The Admin Panel)
@app.route('/admin-portal-99x') 
def admin():
    return render_template('admin.html')
@app.route('/upload', methods=['POST'])
def upload():
    if 'file' not in request.files:
        return "No file part", 400
    file = request.files['file']
    if file.filename == '':
        return "No selected file", 400
    file.save(DATA_FILE)
    return "Database updated successfully!"
@app.route('/search')
def search():
    query = request.args.get('q', '').lower()
    with open(DATA_FILE, 'r') as f:
        data = json.load(f)
    # Simple filter logic for your physics database
    results = [item for item in data if query in item['name'].lower() or query in item['category'].lower()]
    return jsonify(results)
if __name__ == '__main__':
    app.run(debug=True)
    
