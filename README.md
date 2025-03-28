- 👋 Hi, I’m @CC252525
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
CC252525/CC252525 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
# Install package 
pip install flask-login werkzeug

from werkzeug.security import generate_password_hash, check_password_hash

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    password_hash = db.Column(db.String(120), nullable=False)

    @property
    def password(self):
        raise AttributeError('Password is not readable')

    @password.setter
    def password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)
@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        hashed_password = generate_password_hash(password)
        
        new_user = User(username=username, password_hash=hashed_password)
        db.session.add(new_user)
        db.session.commit()

        return redirect(url_for('login'))
    return render_template('register.html')
    from flask_login import LoginManager, login_user, login_required, logout_user, current_user

login_manager = LoginManager(app)
login_manager.login_view = 'login'

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        
        user = User.query.filter_by(username=username).first()
        if user and user.check_password(password):
            login_user(user)
            return redirect(url_for('dashboard'))
        else:
            return 'Invalid credentials', 401
    return render_template('login.html')

@app.route('/dashboard')
@login_required
def dashboard():
    return f'Welcome {current_user.username}!'
    class Job(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(120), nullable=False)
    description = db.Column(db.Text, nullable=False)
    location = db.Column(db.String(120), nullable=False)
    salary = db.Column(db.Float, nullable=True)

    applications = db.relationship('Application', backref='job', lazy=True)
    @app.route('/apply/<int:job_id>', methods=['GET', 'POST'])
@login_required
# Job Application 
def apply_for_job(job_id):
    job = Job.query.get_or_404(job_id)

    if request.method == 'POST':
        application = Application(user_id=current_user.id, job_id=job.id)
        db.session.add(application)
        db.session.commit()
        return redirect(url_for('dashboard'))

    return render_template('apply.html', job=job)
  # HTML NEW FRONT END PAGE
    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Job Listings</title>
</head>
<body>
    <h1>Job Listings</h1>
    <div>
        {% for job in jobs %}
        <div>
            <h2>{{ job.title }}</h2>
            <p>{{ job.description }}</p>
            <p>{{ job.location }}</p>
            <p>{{ job.salary }}</p>
            <a href="{{ url_for('apply_for_job', job_id=job.id) }}">Apply Now</a>
        </div>
        {% endfor %}
    </div>
</body>
</html>

