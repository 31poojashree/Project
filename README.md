# Project
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# Configure MySQL connection
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://username:password@localhost/new_data'  # Replace with your MySQL connection URL
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

# Define User model
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), unique=True, nullable=False)
    password = db.Column(db.Integer, nullable=False)  # Assuming password is stored as an integer

# Routes for user registration and login
@app.route('/register', methods=['POST'])
def register():
    data = request.get_json()
    username = data.get('username')
    password = data.get('password')

    # Check if username is already taken
    if User.query.filter_by(username=username).first():
        return jsonify({'error': 'Username already exists'}), 400

    # Create new user
    new_user = User(username=username, password=password)
    db.session.add(new_user)
    db.session.commit()

    return jsonify({'message': 'User registered successfully'}), 201

@app.route('/login', methods=['POST'])
def login():
    data = request.get_json()
    username = data.get('username')
    password = data.get('password')

    # Check if user exists and password is correct
    user = User.query.filter_by(username=username).first()
    if user and user.password == int(password):
        return jsonify({'message': 'Login successful'}), 200

    return jsonify({'error': 'Invalid username or password'}), 401

if __name__ == '__main__':
    app.run(debug=True)
