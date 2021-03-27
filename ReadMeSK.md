git Installation
----------------
https://git-scm.com/book/en/v2/Getting-Started-Installing-Git


https://www.youtube.com/watch?v=WTofttoD2xg

Above video was linux based, below steps are windows based

Creating CI/CD Pipeline using GitHub Actions for Python Project (Heroku Deployment Example)
github.com (hbn8911@gmail.com)
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

(pyvenv) D:\WS\cicd\python-cicd-heroku-example>

(pyvenv)> pip install flask pytest

--- flask is for micro web framework 
--- pytest is for python unit test

(pyvenv)> python.exe -m pip install --upgrade pip      ///incase pip need to be upgraded

typical requirements.txt file creation but we do not do it this way (steps below are just highlight the approach)
///----------------for reading/understanding purpose only Begin ------------------
(pyvenv)> pip freeze > requirements.txt

(pyvenv)> dir
pyvenv
requirements.txt


(pyvenv)> type requirements.txt   // or cat requirements.txt

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
(pyvenv)> vim requirements.txt
	flask
	pytest

(pyvenv)> mkdir src
(pyvenv)> cd src
(pyvenv)>  touch app.py

(pyvenv)>  vim app.py
<  code here >

(pyvenv)> cd ..
(pyvenv)>  mkdir tests
(pyvenv)>  cd tests
(pyvenv)>  touch test_app.py
(pyvenv)>  vim test_app.py
< index test code here >

cd ..
(pyvenv)> pytest
Error :  ModuleNotFoundError: No module named 'app'

(pyvenv)> set PYTHONPATH=src  //on linux export PYTHONPATH=src
(pyvenv)> echo %PYTHONPATH%  // on echo $PYTHONPATH
src

(pyvenv)>  pytest  or pytest -v // for verbose

(pyvenv)> vim .gitignore
pyvenv/
__pycache__/


(pyvenv)>  git status
(pyvenv)>  git add .
(pyvenv)>  git status
(pyvenv)>  git commit -m "Initial Commit"
(pyvenv)>  git push

remote: Permission to hbnSupport/python-cicd-heroku-example.git denied to kveni1976

git config --global --unset credential.helper <<- did not work

git config --global push.default simple << -- this also did not work


Control Panel ---> User Accounts ---> Manager your credentials ---> Windows Credentials
Then search for an entry like, git:https://github.com and remove it. I
This worked ... 
git push asked for github username and password ... 



Go to github
Go to "Action" tab
Select "Python application" => click on "Set up this workflow"
Creates a file called "pythonapp.yml"


(pyvenv)>  git pull --rebase
(pyvenv)>  git status
(pyvenv)>  cd .github/workflows
(pyvenv)>  vim pythonapp.yml

		...
		run: |
		pip install pytest
		export PYTHONPATH=src  <== added this export of PYTHONPATH
		pytest
		
(pyvenv)>  git status
	show modified: .github/workflows/pythonapp.yml
(pyvenv)>  git add .  //// there is only one thing to add
(pyvenv)>  git commit -m "Update workflow for export src"
(pyvenv)>  git push
(pyvenv)>  git pull --rebase
(pyvenv)>  glo  /// git logs


 




Heroku Setup
------------

download & Install heroku
https://devcenter.heroku.com/articles/heroku-cli#download-and-install

check if heroku is installed
(pyvenv)> heroku

(pyvenv)> heroku login

(pyvenv)> heroku create 
creating app.... done,
https://salty-harbor-37125.herokuapp.com/ | https://git.heroku.com/salty-herbor-37125.git

(visit the above link)

(pyvenv)> git push heroku master   <-- this will push the git code manually 

to automate the deploy process i.e. whenever you sucessfully push the code on github it will push automatically to heroku... for which you need to provide heroku user login / password (authernitcation) specified in github.
for which you need a auth token... to get heroku token type in


(pyvenv)> heroku authorizations:create
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




(pyvenv)> vim pythonapp.yml
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
(pyvenv)> vim Procfile.yml  /// P is capital
web gunicorn --pythonpath src app:app

(pyvenv)> vim runtime.txt
python-3.7.6

(pyvenv)>  vim requirements.txt
flask
pytest
gunicorn   <== add this line

(pyvenv)> git status


(pyvenv)> git add .
(pyvenv)> git commit -m "Prepare for heroku deployment"

(pyvenv)> git push


goto github repository ... check if workflow is working fine or not






