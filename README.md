# flaskr
From https://github.com/mjhea0/flaskr-tdd

```bash
$ python3.8 -m pip install pipenv
$ pipenv install flask==1.1.1
```

## Hello World App

- Create the first test `app.test.py`.
  ```python
  import unittest

  from app import app


  class BasicTestCase(unittest.TestCase):

      def test_index(self):
          # testing whether the response that we get back is "200" and that "Hello, World!" is displayed.
          tester = app.test_client(self)
          response = tester.get('/', content_type='html/text')
          self.assertEqual(response.status_code, 200)
          self.assertEqual(response.data, b'Hello, World!')


  if __name__ == '__main__':
      unittest.main()

  ```
- Create `app.py`.
  ```python
  from flask import Flask


  app = Flask(__name__)


  @app.route('/')
  def hello():
      return 'Hello, World!'


  if __name__ == '__main__':
      app.run()
  ```
- Run the app with `python app.py`.
- Close the app and run the tests `python app.test.py`.

## SQL App

- Add two folders, `static` and `templates`, in the project root.
- Create a new file called `schema.sql` and set up a single table with three fields -- "id", "title", and "text".
  ```sql
  drop table if exists entries;
  create table entries (
    id integer primary key autoincrement,
    title text not null,
    text text not null
  );

  ```

- Modify `app.test.py` by replacing the two asserts by `self.assertEqual(response.status_code, 404)`. The test fails because we are expecting a 404, but we actually get a 200 back since the route exists.
- Update `app.py`. We add in all the required imports, create a configuration section for global variables, initialize the app, and then finally run the app.
  ```python
  # imports
  import sqlite3
  from flask import Flask, request, session, g, redirect, url_for, \
                    abort, render_template, flash, jsonify


  # configuration
  DATABASE = 'flaskr.db'
  DEBUG = True
  SECRET_KEY = 'my_precious'
  USERNAME = 'admin'
  PASSWORD = 'admin'


  # create and initialize app
  app = Flask(__name__)
  app.config.from_object(__name__)


  if __name__ == '__main__':
      app.run()
  ```
- Run the app with `python app.py`.
- Close the app and run the tests `python app.test.py`.

## Database Setup
We want to *open a database connection*, *create the database based on the schema if it doesn't already exist*, then *close the connection each time a test is ran*.
- Update `app.test.py` with `test_database`.
  ```python
  def test_database(self):
        tester = os.path.exists("flaskr.db")
        self.assertTrue(tester)
  ```
- Add `connect_db`, `init_db`, `get_db` and `close_db` functions. Add the `init_db()` function at the bottom of `app.py` to make sure we start the server each time with a fresh database.

## Templates and Views
We need to set up the templates and the associated views, which define the routes.
Users should be able to log in and out.
Once logged in, users should be able to post.
Finally, users should be able to view the posts.
- Update `app.test.py`.
  - Change the status code in `test_index` to `200` and `app` to `app.app`.
  - Add `import tempfile` and change `from app import app` to `import app`.
  - Add `FlaskrTestCase` class.

### Show Entries
- Add a view for displaying the entries to `app.py` in `show_entries` function.
- Add the `index.html` template to the `templates` folder.

### User Login and Logout
- Add `login` and `logout` function to `app.py`.
  In the above `login()` function, the decorator indicates that the route can accept either a GET or POST request. GET is used for accessing a webpage, while POST is used when information is sent to the server. Thus, when a user accesses the `/login` url, they are using a GET request, but when they attempt to log in, a POST request is used.
- Add the template `login.html`.
- Rename the `show_entries()` function to `index()` within `app.py`.
- Add a view `add_entry` for adding entries in `app.py`.

## Style
- Add `style.css` in the `static` folder.
- Run your app, log in (username/password = "admin"), add a post, log out.

## Javascript
- Open `index.html` and update the first `<li>`. Now we can use JavaScript to target each `<li>`. Add the `script` tag before the closing `body` tag.
  ```HTML
  <li class="entry">
    <h2 id="{{ entry.id }}">{{ entry.title }}</h2>
    {{ entry.text|safe }}
  </li>
  ```
- Create a `main.js` file in your `static` directory.
- Add a new function `delete_entry` in `app.py` to remove the post from the database.
- Add `test_delete_message` and `import json`.

Manually test this out by running the server and adding two new entries. Click on one of them. It should be removed from the *DOM* as well as the *database*.
