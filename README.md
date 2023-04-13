# static-site
Using nginx on Linux to host a static website.
Primary guidance came from [nginx article here](https://jgefroh.medium.com/a-guide-to-using-nginx-for-static-websites-d96a9d034940)

`<website-descriptor>` is used here to represent your directory name that contains assets for a specific website, something like `my-cool-python-package-docs`. 

### installing nginx on Linux
  - sudo apt-get update                #updates all packages
  - sudo apt-get install nginx         # installs nginx
  - command `nginx -v`                 #version of nginx installed
  
### helpful nginx commands
```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl status nginx
```

View active ports on machine
```bash
sudo lsof -i -P -n | grep LISTEN
```
  
## source directories
Create directory in `/var/www` to house the static website assets.

```bash
cd /var/www/
mkdir <website-descriptor>
```

## setup configuration
Connect the static website assets to nginx so the site can be exposed via ports on your machine.

  - sites-available (configuration for each website)
  - sites-enabled (points to the configuration files, this is what nginx picks up)

```bash
cd /etc/nginx/sites-available
touch <website-descriptor>
nano <website-descriptor>
```

add config details

```bash
server {
  listen 8088 default_server; # specify your desired port number, typically 8080 is the default port
  listen [::]:8088 default_server; # make sure port number matches in both lines.

  root /var/www/<website-descriptor>;

  index index.html; #point to the main entrypoint into your site, typically this is index.html.

  location / {
    try_files $uri $uri/ =404;
  }
}
```

IMPORTANT: If you change the default port to anything but 8080, you need to update the default config in `/etc/nginx/sites-available/default`
```bash
server {
        listen 80 default_server;
        listen [::]:88 default_server; # MAKE SURE THIS MATCHES THE PORT YOU'RE POINTING TO IN THE WEBSITE CONFIG!

        # SSL configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;

```


now connect it to `sites-enabled` by creating a link in the directory.

```bash
ln -s /etc/nginx/sites-available/<website-description> /etc/nginx/sites-enabled/<website-descriptor>
```
