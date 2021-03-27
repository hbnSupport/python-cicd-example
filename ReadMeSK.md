git Installation
----------------
https://git-scm.com/book/en/v2/Getting-Started-Installing-Git


https://www.youtube.com/watch?v=WTofttoD2xg

Above video was linux based, below steps are windows based

Creating CI/CD Pipeline using GitHub Actions for Python Project (Heroku Deployment Example)
github.com (hbn8911@gmail.com/p@ssp@ss1)
>> new repository
>> python-cicd-heroku-example
https://github.com/hbnSupport/python-cicd-heroku-example.git

cd ws
mkdir cicd
cd cicd

D:\WS\cicd> git clone https://github.com/hbnSupport/python-cicd-heroku-example.git



cd python-cicd-heroku-example

python -m venv pyvenv

cd pyvenv

.\Scripts\activate

(myenv) D:\WS\cicd\python-cicd-heroku-example>

(myenv)> pip install flask pytest

--- flask is for micro web framework 
--- pytest is for python unit test

(myenv)> python.exe -m pip install --upgrade pip      ///incase pip need to be upgraded

typical requirements.txt file creation but we do not do it this way (steps below are just highlight the approach)
///----------------for reading/understanding purpose only Begin ------------------
(myenv)> pip freeze > requirements.txt

(myenv)> dir
myenv
requirements.txt


(myenv)> type requirements.txt   // or cat requirements.txt

	atomicwrites==1.4.0
	attrs==20.3.0
	click==7.1.2
	colorama==0.4.4
	Flask==1.1.2
	iniconfig==1.1.1
	itsdangerous==1.1.0
	Jinja2==2.11.3
	MarkupSafe==1.1.1
	packaging==20.9
	pluggy==0.13.1
	py==1.10.0
	pyparsing==2.4.7
	pytest==6.2.2
	toml==0.10.2
	Werkzeug==1.0.1
///----------------for reading/understanding purpose only End ------------------

add the following two lines into requirments.txt
(myenv)> vim requirements.txt
	flask
	pytest

(myenv)> mkdir src
(myenv)> cd src
(myenv)>  touch app.py

(myenv)>  vim app.py
<  code here >

(myenv)> cd ..
(myenv)>  mkdir tests
(myenv)>  cd tests
(myenv)>  touch test_app.py
(myenv)>  vim test_app.py
< index test code here >

cd ..
(myenv)> pytest
Error :  ModuleNotFoundError: No module named 'app'

(myenv)> set PYTHONPATH=src  //on linux export PYTHONPATH=src
(myenv)> echo %PYTHONPATH%  // on echo $PYTHONPATH
src

(myenv)>  pytest  or pytest -v // for verbose

(myenv)> vim .gitignore
pyvenv/
__pycache__
requirements.txt.bak

(myenv)>  git status
(myenv)>  git add .
(myenv)>  git status
(myenv)>  git commit -m "Initial Commit"
(myenv)>  git push

Go to github
Go to "Action" tab
Select "Python application" => click on "Set up this workflow"
Creates a file called "pythonapp.yml"


(myenv)>  git pull --rebase
(myenv)>  git status
(myenv)>  cd .github/workflows
(myenv)>  vim pythonapp.yml
(myenv)>  git add requirements.txt
(myenv)>  git commit -m "Update to requirements"
(myenv)>  git pull --rebase
(myenv)>  glo  /// git logs
(myenv)>  git push


 




Heroku Setup
------------

download & Install heroku
https://devcenter.heroku.com/articles/heroku-cli#download-and-install

check if heroku is installed
(myenv)> heroku

(myenv)> heroku login

(myenv)> heroku create 
creating app.... done,
https://salty-harbor-37125.herokuapp.com/ | https://git.heroku.com/salty-herbor-37125.git

(visit the above link)

(myenv)> git push heroku master   <-- this will push the git code manually 

to automate the deploy process i.e. whenever you sucessfully push the code on github it will push automatically to heroku... for which you need to provide heroku user login / password (authernitcation) specified in github.
for which you need a auth token... to get heroku token type in


(myenv)> heroku authorizations:create
Creating OAuth Authorization.... done
...
Description: Long-lived user authorization.
Scope: global
Token: 7dfd93ac-ea2c-ract-.......

copy this token into github repository...
instead of adding the token directly copy into github "Settings--> Secerts --> Add a new secret"

Name: HEROKU_API_TOKEN
Value: 7dfd93ac-ea2c-ract-.......

Add another secret name : say app name
Name: HEROKU_APP_NAME
Value: salty-harbor-37125




(myenv)> vim pythonapp.yml
...
...
	steps:
	- uses: actions/checkout@v2
	- run: |
		git fetch --prune --unshallow
...
...
  -	name: Test with pytest
	run: |
		pip install pytest
		export PYTHONPATH=src  <== added this export of PYTHONPATH
		pytest 

  -	name: Deploy to Heroku
	env: 
		HEROKU_API_TOKEN: ${{ secrets.HEROKU_API_TOKEN }}
		HEROKU_APP_NAME: ${{ secrets.HEROKU_APP_NAME }}
	if: github.ref == 'refs/heads/master' && job.status == 'success'
	run: |
		git remote add heroku https://heroku:$HEROKU_API_TOKEN@git.heroku.com/$HEROKU_APP_NAME.git
		git push heroku HEAD:master -f  /// HEAD is the latest commit from current branch, master is name of the default branch


/// Another file is required to deploy python or any other application to heroku
(myenv)> vim Procfile.yml  /// P is capital
web gunicorn --pythonpath src app:app

(myenv)> vim runtime.txt
python-3.7.6

(myenv)>  vim requirements.txt
flask
pytest
gunicorn   <== add this line

(myenv)> git status


(myenv)> git add .
(myenv)> git commit -m "Prepare for heroku deployment"

(myenv)> git push


goto github repository ... check if workflow is working fine or not






