# BookBazaarpip install flask-sqlalchemy
# app.py
from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'
db = SQLAlchemy(app)

class Book(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    author = db.Column(db.String(100), nullable=False)
    is_available = db.Column(db.Boolean, default=True)

@app.route('/')
def index():
    books = Book.query.all()
    return render_template('index.html', books=books)

if __name__ == '__main__':
    app.run(debug=True)
python
from app import db
db.create_all()
exit()
<!-- index.html -->
<!-- ... (previous code) -->
<body>
    <header>
        <h1>Welcome to BookBazaar</h1>
    </header>

    <section id="book-list">
        {% for book in books %}
            <div class="book">
                <h2>{{ book.title }}</h2>
                <p>Author: {{ book.author }}</p>
                {% if book.is_available %}
                    <p>Status: Available</p>
                    <form action="{{ url_for('rent_book', book_id=book.id) }}" method="post">
                        <button type="submit">Rent</button>
                    </form>
                {% else %}
                    <p>Status: Rented</p>
                {% endif %}
            </div>
        {% endfor %}
    </section>

    <footer>
        <p>&copy; 2024 BookBazaar</p>
    </footer>

    <script src="scripts.js"></script>
</body>
</html>
# app.py
# ... (previous code)

from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'
db = SQLAlchemy(app)

# ... (previous code)

@app.route('/rent/<int:book_id>', methods=['POST'])
def rent_book(book_id):
    book = Book.query.get(book_id)

    if book.is_available:
        book.is_available = False
        db.session.commit()

    return redirect(url_for('index'))

if __name__ == '__main__':
    app.run(debug=True)
// scripts.js
document.addEventListener('DOMContentLoaded', function () {
    const rentButtons = document.querySelectorAll('.rent-button');

    rentButtons.forEach(button => {
        button.addEventListener('click', function () {
            alert('Book rented successfully!');
        });
    });
});
