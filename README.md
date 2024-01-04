# Guide to deploy NextJS and Nodejs app on AWS EC2 instance

## Required Tools:

* PM2. (Process Manager For NodeJS)

* Nginx.

* AWS EC2 instance.

* Github Repository to clone updated code.

### Steps to follow to use EC2 instance:

1. Login in to your AWS Console < EC2 < Choose instance (Create one if you haven't already).
2. Connect to EC2 instance using SSH (for windows)
    1. Change your directory to the folder where you've download your SSH key for example `cd download` using your terminal
    2. While staying in your directory use this command to login to your instance `ssh -i yourkey.pm ubuntu@yourinstanceip`

> ### Steps to follow after getting access in your instance; install required packages and clone repository in your instance

1. Update packages of your ubuntu environment by running this command in your terminal `sudo apt-get update`.
2. Upgrade your packages as well `sudo apt-get upgrade` press `y` when asked **Do you want to continue?**.
3. Next we have to install **build-essential** to add all the required libraries such as **git, GNU debugger, g++ compiler** *required for compiling programs*. Command we need to run is: `sudo apt-get install build-essential`; press `Y` if asked.
4. Next install Nginx package to setup server and proxy to host our NextJS app. `sudo apt-get install nginx` press `Y` if asked.
5. Next we have to install **NVM to install NodeJS in our instance paste the command in your instance and hit enter `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash`**. Make sure you exit and login again to your instance just type `exit` in your terminal and use login command I mentioned above.
      1. Run `nvm` and check if you can see multiple command.
      2. Next hit `nvm install --lts` to install latest NodeJS version.
      3. Check if it's installed run `node -v` if you can see something like `v20.xx.x` it means you have successfully     
installed **NodeJS in your machine**.

> ### Cloning of repository and installing required NodeJS packages:

1. Next headup to your github repo and copy your repository HTTPS URL.
2. Next clone your repository in desired location (I usually does it in ~root directory) by using this command `git clone your-repo-url`.
3. Next change directory to your cloned project `cd aws-deployment` and install required package by running `npm i`.
4. Next we will create a production build to deploy; Run `npm run build`.

> ### Setting up PM2.

1. In your project directory install PM2 package `npm i -g pm2`.
2. Next run the project by using `pm2 start npm --name "deployed nextjs site" -- start`
3. Check running process by using `pm2 ls`. If you see your project running with id of 0 and name of "deployed nextjs site" it means your server is up and running.


## By default the app run on port **3000** which we have to change by passing the proxy to port **80, 443** (which is http and https) by using Nginx.

> ### Final steps to create a Nginx configuration file and deploy your site.

1. Change your directory to root by using this command `cd ~`.
2. Create a config file by running this command `sudo nano /etc/nginx/sites-available/yoursitename`.
     * Copy this code and paste it in your config file by pressing **CTRL + U**
       ```nginx
       server {
              listen 80;
              server_name your-domain-or-ip;

             location / {
                proxy_pass http://127.0.0.1:3000; //3000 because that's what React or NextJS uses if you are using Vite you can change it according to it.
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
             }
       }
       ```
     * Save by using `CTRL + 0` and exit nano by `CTRL + X`
3. Create a symbolic link for previously config file by running this command `sudo ln -s /etc/nginx/site-available/yoursitename /etc/nginx/sites-enabled/` **(Makes sure changes to name of yoursite)**
    * Test if the configuration was successful by running `sudo nginx -t`. If you see two logs of syntax is ok and test is successful congratulations you have successfully created your proxy server!

4. Restart nginx service by running `sudo service nginx restart`.
5. Now go to your domain or ip address in your browser where you will visit your deployed site.

