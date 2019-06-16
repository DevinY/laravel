
**[中文說明](https://github.com/DevinY/dlaravel/blob/master/README-zh_TW.md)**

# D-Laravel (English manual)


D-Laravel uses docker-composer micro service framework,

it helps you quickly to build basic Laravel development environment by using simple commands: console and create.

Using D-Laravel also means no longer to install mysql, nginx and php-fpm in Mac.

You can deploy the environment by self-defined docker-compose-custom.yml file.


### Why use D-Laravel

It Provides easy way to execute the docker-compose command, quickly generate a laravel project environment, and automatically build a database and adjust Laravel's .env database settings for you.

The sublime3 package of D-Laravel alias can be installed, and the artisan and composer commands in the container (MacOS and Linux environment) are executed via sublime.

You can execute the latest PHP version in Mac OS.

It helps you to generate self-signed certification and completes HTTPS setting.

No need to install DnsMasq. 

Port 80 won't be used when not start dlaravel.

Initial speed in seconds!!!

It can simulate different DB environment.

It also could open kinds of testing hosts.


Easy docker-compose v3.6 setting.

Using Dockerhub to remark official image to build basic Laravel environment.


It seems fashion if use docker. :P
<pre>
./console help (help, console parameter)
./create test1  (build test1.test)
./console down or ./console up (stop or start container)
./console restart    (first ./console down and then ./console up)
`./console alias`  
(If you think ./console command is too log, you could create c alias in terminal which simplifies command to "c info", "c up", or "c down")
./console alias (print console alias samples, it can be added to .bashrc or .zshrc)   
./console version

Generate self-signed certificate for all current projects. (MacOS and Linux)
./console secure
</pre>


### Main Directory structure
<pre>
etc/   (nginx、php.ini and mysql settings)
data/  (mysql data files, auto-generated by ./console up)
dockerfiles/ (some dockerfiles)
sites/ (project files, the project will be set in this folder when executes ./create test1)
create (Simplified bash, to fast build laravel project) 
console (Simplified bash file, to use kinds of docker-compose commands. ex. login mysql by "./console mysql")
docker-compose.yml (A soft link to connect different settings. ex. linking to docker-compose-custom.yml by "./console custom") 
</pre>


#### 1. Please install docker.
<pre>
Mac OS:
https://docs.docker.com/docker-for-mac/

Ubuntu:
https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-docker-ce

Windows 10: (required for laravel install in gitbash!!)
https://docs.docker.com/docker-for-windows/install/

To run console and create bash on Windows 10:
Gitbash is required.
Laravel installer have to run correctly in gitbash also.
</pre>


#### 2. git clone D-Laravel repo and execute "./console pull" in the folder
<pre>
git clone https://github.com/DevinY/dlaravel.git
cd dlaravel


You can ignore below's pull command. It will auto download the images according to docker-compose.yml when initials.
./console pull
The first time to download needed images from dockhub will take longer time. Be patient. :P
</pre>


#### 3. execute "./create ['project name']" to biuild new project
<pre>
ex.
./create test1
It will build http://test1.test website (can't be accessed from outside)
So you can use this command to build many different projects.

You need to provide privilege to modify /etc/hosts file, so D-Laravel will ask for your systme password during executing "./create project". (Mac OS using only)
</pre>


#### 4. executing "./console down" in D-Laravel foldee
<pre>
Attention:
D-Laravel is only used for development, Mysql root password is empty.
You may need to revise TZ in docker-compose.yml, default is Asia/Taipei.
</pre>


#### 5. Updating D-Laravel bash program and some settings
<pre>
git pull

.gitignore in D-Laravel already excludes dynamic files, ex. docker-comnpose.yml, docker-compose-custom.yml, sites folder etc.
You can git pull latest version and setting in D-Laravel folder.
</pre>

#### 6. alias and function (without entering the container can be implemented within the container composer or artisan instructions)

Get the console alias in your system.
<pre>
 ./console alias
</pre>
And then add the output to your ~/.bash_profile, or ~/.zshrc if you are using Z shell.
The OS X won't create .bash_profile for you. 
Type 'touch ~/.bash_profile' to create your new one.

Execute the laravel installer in the container using the identity of dlaravel
<pre>
alias laravel='docker-compose exec -u dlaravel php /home/dlaravel/.composer/vendor/bin/laravel'
</pre>

run composer.sh in project folder, for example: ce dump
<pre>
alias ce="../../composer.sh"
</pre>

We can add aliases to our computer so that you do not have to go into the container to execute the php artisan directive. Let artisan's instructions be more concise, which I call the alias directly as a. For example: a --version
<pre>
alias a="../../artisan.sh"
</pre>

<pre>
alias phpunit = 'docker-compose -f ../../docker-compose.yml exec -u dlaravel php $ (basename $ {PWD}) / vendor / bin / phpunit -c $ (basename $ {PWD}) / phpunit.xml '
</pre>
Join this alias can do simple phpunit.

#### Others
<pre>
./create [project name] will create and build laravel, it would set all configures automatically including DB.
But how do we do if project already exists and only needs nginx setting?

Using "--host" to update /etc/hosts file, for example: "127.0.0.1 project1.test". 
./create --host [project name] (./create --host project1)


You can move exist Laravel project to D-Laravel site folder.

Or manually executes "composer create-project" (not default version etc.) 

Ex. Create project manually by using lumen
./console exec  //It can execute php command in container.

./console exec php composer create-project --prefer-dist laravel/lumen project1
</pre>



#### Adjust images in setting
PHP: [Official repository rebuilds php to fit Laravel environment](https://hub.docker.com/r/deviny/fpm/tags/)
<pre>
 image: deviny/fpm:7.3.5
 image: deviny/fpm:7.2.18
 image: deviny/fpm:7.1.29
 image: deviny/fpm:5.6.39
</pre>

Nginx: [OFFICIAL REPOSITORY](https://hub.docker.com/r/library/nginx/)

Mysql: [OFFICIAL REPOSITORY](https://hub.docker.com/_/mysql/)

Remark: 
When you need to revise different mysql version and that D-Laravel/data already exist, you may need to reset DB name in D-Laravel.data to ensure mysql image works normally.  

Ex. Rename data to data_mysql8

<pre>
db:
  image: mysql:8
  ...
  volumes:
      - ./etc/mysql/my.cnf:/etc/mysql/my.cnf
      - ./data_mysql8:/var/lib/mysql
</pre>


#### Advandace

If you would like to rebuild your own php-fpm image.
You can do as below:
<pre>
cd dockerfiles/fpm/7.3
docker build -t myfpm .
</pre>

And then edit Your docker-compose.yml file.
<pre>
 php:
  network_mode: "service:web"
  image: myfpm
</pre>


### Docker commands and Devin/dlaravel "./console" bash command comparison table

|Docker official commands|simplified ./console Bash commands| Describtion|
|---|---|---|
| docker-compose pull  |./console pull   |pull latest images   |
| docker-compose up -d  |./console up   |start container   |
| docker-compose down  |./console down  |stop container   |
| docker-compose ps or docker ps|./console ps  |show docker-compose process   |
| docker-compose exec -u dlaravel php bash   |./console  |access php container   |
| docker-compose exec php command  |./console exec command |execute php container command，ex. ./console exec php -v|
| docker-compose exec db mysql   |./console mysql  |exectue mysql   |
| docker-compose exec web nginx -s reload   |./console reload  |reload nginx setting   |
| docker-compose logs -f [SERVICE]   |./console logs  |show nginx log，stop it with Ctrl+C |
|   |./create [ProjectName]|create a project with default settings   |
|   |Ex. ./create test1  |Ex. create a http://test1.test website   |
|   |./console restart  |restart container   |
|   |./console info  |show url info   |
|Switch model:|
|   |./console random  |using random port when initiate container|
|   |./console normal  |using local port 80 and 127.0.0.1:3306|
|   |./console custom  |using self-defined docker-compose-custom.yml|
|command below: -f: specific docker-compose.yml path。 up -d:start container in background|
|docker-compose -f docker-compose-normal.yml up -d| |using port 80 and port 3306|
|docker-compose -f docker-compose-random.yml up -d| |specific yml file with random port|

