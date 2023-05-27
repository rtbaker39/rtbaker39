from flask import Flask, render_template, request, redirect, session

app = Flask(__name__)
app.secret_key = 'your_secret_key'

# Sample data
users = {
    'user1@example.com': 'password1',
    'user2@example.com': 'password2'
}

gift_cards = [
    {
        'brand': 'Amazon',
        'value': 50,
        'expiration_date': '2023-12-31',
        'seller': 'user1@example.com'
    },
    {
        'brand': 'Starbucks',
        'value': 25,
        'expiration_date': '2023-10-15',
        'seller': 'user2@example.com'
    }
]

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        email = request.form['email']
        password = request.form['password']
        if email in users and users[email] == password:
            session['email'] = email
            return redirect('/dashboard')
        else:
            return render_template('login.html', error='Invalid email or password')
    return render_template('login.html')

@app.route('/dashboard')
def dashboard():
    if 'email' in session:
        user_cards = [card for card in gift_cards if card['seller'] == session['email']]
        return render_template('dashboard.html', cards=user_cards)
    else:
        return redirect('/login')

@app.route('/logout')
def logout():
    session.pop('email', None)
    return redirect('/')

if __name__ == '__main__':
    app.run(debug=True)
