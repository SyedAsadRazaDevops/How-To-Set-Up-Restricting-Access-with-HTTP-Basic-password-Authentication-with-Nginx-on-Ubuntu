# Introduction
When setting up a web server, there are often sections of the site that you wish to restrict access to.
Web applications often provide their own authentication and authorization methods, but the web server itself can be used to restrict access if these are inadequate or unavailable.
Control access using HTTP Basic authentication, and optionally in combination with IP address-based access control.

# How to Create the Password File
To start out, we need to create the file that will hold our username and password combinations. 
You can do this by using the OpenSSL utilities that may already be available on your server.
Alternatively, you can use the purpose-made htpasswd utility included in the apache2-utils package (Nginx password files use the same format as Apache).

# -1 Generate a Password with Apache Utilities
If you are using Apache, you probably already have htpasswd installed. Nginx users will need to install this package with the apt package manager as follows.
```
apt install apache2-utils
```
Now you can create a username and password. If you are on Apache, swap out nginx in the command below with apache2.
```
htpasswd -c /etc/nginx/.htpasswd <USERNAME>
```
Next, add an encrypted password entry for the username by typing:
>Feel free to create multiple users this way if necessary.

You can repeat this process for additional usernames. You can see how the usernames and encrypted passwords are stored within the file by typing:
```
cat /etc/nginx/.htpasswd
```
# -2 Generate a Password with OpenSSL Utilities
If you have OpenSSL installed on your server, you can create a password file with no additional packages.
We will create a hidden file called .htpasswd in the /etc/nginx configuration directory to store our username and password combinations.

You can add a username to the file using this command. 
```
sudo sh -c "echo -n 'sammy:' >> /etc/nginx/.htpasswd"
```
Next, add an encrypted password entry for the username by typing:
```
sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"
```
You can see how the usernames and encrypted passwords are stored within the file by typing:
```
cat /etc/nginx/.htpasswd
```

# Configure Nginx Password Authentication
Now that we have a file with our users and passwords in a format that Nginx can read, we need to configure Nginx to check this file before serving our protected content.

Within this location block, use the auth_basic directive to turn on authentication and to choose a <name> to be displayed to the user when prompting for credentials. We will use the auth_basic_user_file directive to point Nginx to the password file we created:
```ruby
location / {
        try_files $uri $uri/ =404;
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
```
For laravel nginx configration; use this.
```ruby

server {
    
    listen 80;
    listen [::]:80;

    server_name <domain>;
    root /project-path/public;
    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location /api/subapiFolder {

        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }


}
```


Save and close the file when you are finished. Restart Nginx to implement your password policy:
```
sudo service nginx restart
```


https://tonyteaches.tech/basic-authentication/
        
https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/
        
https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-nginx-on-ubuntu-14-04
        
https://www.youtube.com/watch?v=_zoDkXyXrx4
