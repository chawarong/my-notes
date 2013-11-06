how-to-heroku.md

Example:
First, you need to install Heroku toolbelt

		$ cd hw2_rottenpotatoes/
		$ wget -qO- https://toolbelt.heroku.com/install.sh | sh
		$ ll
		$ which heroku
		$ heroku login
		$ heroku stack  ;# list a web app
		$ heroku create --stack cedar ;# create a web app
		Notice: on Wed, 20 June, our default stack will change to Cedar. http://bit.ly/Lh0rM5

		Creating floating-flower-8535... done, stack is cedar
		http://floating-flower-8535.herokuapp.com/ | git@heroku.com:floating-flower-8535.git
		Git remote heroku added

		$ cd .git
		$ ls
		$ cat config  ;# see remote repos (notice heroku)
		$ heroku keys:add ~/.ssh/id_rsa.pub
		$ git push heroku master