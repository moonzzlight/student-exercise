# Exercise 2: Running the tests

This exercise is about ensuring that your development environment is set up and working as expected, 
and to prove the end-to-end development process we will be following throughout subsequent exercises


## Running the tests locally

1. Install the project's Python dependencies by running the following commands from the root of the repository:
   ```shell
   pyenv install
   ```
   ```shell
   python -m pip install --upgrade pip
   ```
   ```shell
   pip install -r requirements_dev.txt
   ```
   ```shell
   pip install -r requirements.txt
   ```
   This step should only need to be done once and can be skipped on subsequent running of the tests.
2. Start the containers:
   ```shell
   docker compose watch
   ```
   NOTE: we are using `watch` instead of `up` (as we did previously) which will allow the running container to hot reload
   the app as changes are made so you don't have to keep stopping and restarting.

3. Run the following command (you'll have to open a new terminal/shell) to execute the project's functional acceptance tests: 
   ```shell
   python -m pytest tests/acceptance_tests/
   ```
4. Confirm that the tests all pass.
5. Visit the application in a browser (https://localhost/registers/) and observe that the data created by the tests is still present.


## Clearing the local database

Whilst being able to view the data created by the tests is useful, the database can fill up very quickly if you're running
the tests multiple times.  A simple script is provided in the root of the project that can be used to truncate the database,
execute it by running the following command from the root of the project (where `<username>` and `<password>` are the database
username and password you set in your .env file:
```shell
python truncate_db.py -u <username> -p <password>
```
**IMPORTANT NOTE:** executing this script will delete _all_ the rows in the associated database tables, and you won't be able to get them back!


## Breaking the tests

Now that we've proved the tests are passing, let's try breaking them.  The value of a good test suite is that it can help
defend against regressions in the expected behaviour (functionality).

1. Try making a change to the application to see if it causes any of the tests to fail.  The tests should be robust enough
   that simple/cosmetic changes to HTML do not causea failure, as long as the core functionality of the application is
   preserved.
2. Can you identify any missing tests? If so, make a note of them.
3. Remember when you're done to revert your changes and reconfirm that tests are all passing


## Configure the GitHub workflow to run tests

Now that we've proven the tests locally, we want to ensure they run in GitHub whenever a change is made to ensure 
nothing is broken.

1. This project includes some GitHub workflows, but they will not be enabled by default on your fork.
2. In a browser, log in to GitHub and find your fork repository.
3. Under the "Actions" tab, enable actions
4. Go to the repository's Settings, then "Secrets & variables" -> "Actions"
5. Under "Repository secrets" add the following secrets:
   1. `PG_USER`: a username for the database user to be created/used by the GitHub workflow (e.g. `db_user`)
   2. `PG_PASS`: a password for the database user (e.g. `db_password`)
   3. `SECRET_KEY`: repeat the step in exercise (under "Running the project") to generate a secret key and copy/paste the result


## Pushing changes back to the repository

Before you can push changes back to the remote repository (in GitHub), you'll need to set up an access token.  This
token should be used (instead of your actual GitHub password) if you are prompted for a password when trying to push
changes.

1. In a browser, log in to your GitHub account and form the menu go to "Settings" -> "Developer Settings" -> "Personal Access Tokens" -> "Tokens (Classic)"
   1. Generate a new "classic" token called "HMLR work experience" that expires at the end of the week and select the
      following scopes: `repo`, `workflow`, `write:packages`, `delete:packages`, `user`
   2. Click "Generate token"
   3. Copy the generated token and save it somewhere you'll be able to access it later
2. In your local copy of the repository, make a innocuous change to any file (e.g. add a new line to README.md)
3. Run the following command to stage your change:
   ```shell
   git add .
   ```
4. Run the following command to commit the change locally:
   ```shell
   git commit -m "<a short description of your change>"
   ```
5. Run the following command to push your committed change back to GitHub:
   ```shell
   git push
   ```
   (when prompted enter your github username and paste the access token your generated when prompted for your password)
6. In a browser, log in to GiHub and find your fork repository
7. Select the "Actions" tab and confirm that the workflows are running
8. Wait for the workflows to complete and confirm that they have all succeeded
