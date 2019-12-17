

Sign in

Setup Node.js Application with PM2 and NGINX

Chai Phonbopit
May 6, 2018 · 3 min read
I’m annoying every time when I setup Basic or Demo Node.js Application, I have to Create Droplet, Install NGINX, Install Node.js and setup NGINX to be as a reverse proxy for my node application.
I want to reduce my process. It’s better if all tasks running automate. After I thought I have 2 ways:
    1. Create bash/shell script to install dependencies, setup NGINX and run Node.js as daemon service. when I want to setup Basic/Demo App, just run script as User Data when creating a new droplet or via Image and everything is working perfectly.
    2. Create docker file Only Dockerfile file, Lucky Digital Ocean provides One-Click Application for Docker, I can create a droplet with Docker within a minute.
and for this blog post, I choose the method 1.

Installing NGINX and Node.js
Today I am going to share you about how I setup Node.js Application to up and running on Production.
Stack version:
    • Ubuntu 16.04
    • Node.js v10.0.0
    • NGINX
    • PM2
After droplet was created, I install NGINX, Node.js and utility modules. ( g++ and node-gyp It’s optional, It’s required when you run some Node library that compiles to binary or depends on machine environment e.g. bcrypt, SASS, etc.
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -sudo apt-get install -y nodejs nginx build-essential g++ node-gyp

Setup NGINX
After NGINX installed, I create another NGINX config file, because of the defaultnginx.conf have a script to include all files that match pattern *.conf like this:
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
That’s mean I can create new file e.g. my-app.conf inside /etc/nginx/conf.d and everything works perfectly! and my file my-app.conf have only server name, port and match location that set proxy_pass to port that my Node is running (In my case I use port 5555)
server {
  listen 80;  server_name your_domain.com;  location / {
    proxy_pass http://localhost:5555;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
   }
}
Just restart NGINX and every is working!
# To verify nginx configuration
sudo nginx -t# Restart nginx
sudo service nginx restart

Setup PM2
When you want to long run Node.js, you need daemon process, and lucky Node.js has PM2 that can able to do for you, just Install PM2 via global
npm install -g pm2# If you use yarn
yarn add global pm2
Start PM2 is very easy! just go to Node.js project and run a command:
cd /path/to/node-project
pm2 start server.js --name="My Awesome App"
I save PM2 process for when a machine wasrestart, PM2 can running the same configuration
pm2 save
pm2 startup
Another option you can create server.config.js for running a script with PM2 like this:
module.exports = {
  apps: [
    {
      name: 'My Awesome App',
      script: './server.js',
      instances: 0,
      exec_mode: 'cluster',
      watch: true,
      env: {
        NODE_ENV: 'production',
        PORT: '5555'
      }
    }
  ]
};
and run PM2 with
pm2 start server.config.js
That’s it. 🎉
Today I Learned ❤️Chai
Chai's learning journey about Web development (Node.js + React), Lifestyle, Travel and Self Improvement.
    • Nodejs
    • Pm2
    • Nginx

WRITTEN BY
Chai Phonbopit
I make web stuff with JavaScript (Node & React), Blogger at https://devahoy.com 🤖. Coding by day blogging by night.

Today I Learned ❤️Chai
Chai's learning journey about Web development (Node.js + React), Lifestyle, Travel and Self Improvement.
See responses (4)
Discover Medium
Welcome to a place where words matter. On Medium, smart voices and original ideas take center stage - with no ads in sight. Watch
Make Medium yours
Follow all the topics you care about, and we’ll deliver the best stories for you to your homepage and inbox. Explore
Become a member
Get unlimited access to the best stories on Medium — and support writers while you’re at it. Just $5/month. Upgrade
AboutHelpLegal
