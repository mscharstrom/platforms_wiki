## <b>Redirect HTTP to HTTPS on your own NGINX web server and setup SSL.</b>
This guide assumes that you have already setup NGINX, configured nginx.conf and have a DNS redirected to your server from your web domain. The redirection in this guide will be server-side and not on an application level. I have written/compiled this guide from multiple sources online and we are focusing only on an linux environment (I have found that many Raspberry Pi users seem to be asking questions about this).
<br><br>
<b>Other prerequisites:</b>
<br>

- [x] You've set an "A" (HTTP) redirect from your web domain to your own public IP of the web server.
- [x] Your web server is up and running and you have an example.com website on it (for eg. in /var/www/example.com/).
- [x] I'm assuming that you are using include /etc/nginx/conf.d/*.conf; over include /etc/nginx/sites-enabled/*;. If sites-enabled are your preferred choise, please adapt accordingly in the last section.

### <b>Why is SSL important?</b><br>
Without SSL, your website will show insecure to the visitors. SSL encrypts requests made between a web server and a visitors browser and it also make requests and responsens happen in a way so that they can't be intercepted. The certificates that is being authenticated makes sure that the certificate isn't intended for a different domain, if so the user will get alerted and the browser will most likely block the request. Users today expect a secure and private online experience when using a website. It is more or less default nowadays to use TLS/SSL (HTTPS).<br>

Google has written a [great article](https://support.google.com/webmasters/answer/6073543?hl=en) on this matter with a handful of useful tips. In short it provides three key layers of protection: <br>
1. Encryption — encrypting the exchanged data to keep it secure from eavesdroppers. That means that while the user is browsing a website, nobody can "listen" to their conversations, track their activities across multiple pages, or steal their information.<br>
2. Data integrity — data cannot be modified or corrupted during transfer, intentionally or otherwise, without being detected<br>
3. Authentication — proves that your users communicate with the intended website. It protects against man-in-the-middle attacks and builds user trust, which translates into other business benefits.<br>

### <b>Creating SSL certificates with Let's Encrypt!</b>
To be able to redirect your HTTP to HTTPS you will need valid certificates. To get this we are going to use Let's Encrypt which is a free service that allows you to create SSL certificates. Keep in mind that these certificates are kinda short lived. <br>

Start with making sure that you are all up to date:<br>
```
sudo apt-get update
sudo apt get upgrade
```

Next install Let's Encrypt:<br>
```
sudo apt-get install letsencrypt
````

Now let's create some free SSL certifications! Make sure to change out example.com to your specific folder and the file path /var/www/ if your web app is located elsewhere:<br>
```
letsencrypt certonly --webroot -w /var/www/example.com -d example.com -d www.example.com
```
We are also going to create some DH parameters. Here is a [post](https://security.stackexchange.com/questions/94390/whats-the-purpose-of-dh-parameters) that explains DH a bit more in detail. Start off with browsing to this directory:<br>
```
cd /etc/ssl/certs/
```
Generate the certificate (quite a long process):
```
openssl dhparam -out dhparam.pem 4096
```
Now lets get NGINX up running with these certificates!

### <b>Configuring NGINX for SSL</b>
Here is the code that you need to add to your example.com.conf file. If any of the following code is unclear, please check  the example file *example.com.conf* in this repository for further details and functions (such as ssl_session_timeout) <br>

```
server {
  listen 80;
  server_name example.com www.example.com;
  return 301 https://example.com$request_uri;
}

server {
  listen 443 ssl;
  server_name example.com www.example.com;

  # ssl configuration
  ssl on;
  ssl_certificate /path/to/certificate.crt;
  ssl_certificate_key /path/to/private.key;

  if ($http_host = www.example.com) {
    return 301 https://example.com$request_uri;
  }
}
```
Make sure to update all the example.com text with your own file paths and /path/to/ to where your created certificates are placed. Usually the certificates ends up in:
```
/etc/letsencrypt/live/your_example/fullchain.pem 
/etc/letsencrypt/live/your_example/privkey.pem 
```
<br>
<b>Done!</b><br><br>
Hope this guide has been helpful!<br>
Please let me know if you have any questions in the comment section. <br><br>

*Most of the material in this guide comes from both the two first references (huge thanks! :+1:):<br>
[Stewright.me](https://www.stewright.me/2017/01/add-ssl-nginx-site-free-lets-encrypt/)<br>
[dnsimple.com blog post](https://blog.dnsimple.com/2016/08/https-redirects/)<br>
[NGINX Docs](http://nginx.org/en/docs/)<br>*
