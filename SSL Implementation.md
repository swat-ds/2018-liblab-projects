# SSL Implementation via Let's Encrypt

The library owns a lot of infrastructure that is not necessarily owned by the school, this provides more flexibility with what can be done with them in terms of customization. An issue that comes with this is that most of these domains acquired for independent projects or courses do not have SSL certification. This is reflected on the URL, which starts with `http` instead of `https`, and what this means is that the information stored in these websites that is transmitted from computer to computer through the internet is not encrypted. Here we outline how to obtain a Domain Validated-type SSL certificate from Let’s Encrypt, a nonprofit certification authority that provides SSL certification for free, and automate the process of renewing this certificate.

## Getting things ready

### Accessing the server (for Windows users)

If you are a Mac or Linux user, you should be able to access the library servers from the command line.

If you are a Windows user, there is an extra step you need to take:

1. Download PuTTY from [here](https://www.putty.org/) and install it. This will also install a feature called PuTTYgen.
2. Use PuTTYgen to generate a public and a private key pair in `.ppk` format. Save both keys somewhere you can find them later.
3. To connect to the library server, go on PuTTY and use `name@34.237.205.134` as the hostname (or IP address), where `name` is the user name that is created by the server administrator for you, and `34.237.205.134` is the IP address of the AWS server you want to connect to.
4. Go to SSH > Auth, use the private key generated through PuTTYgen.
5. Click Open to connect. You will be prompted to enter your PuTTY user name and password for login (not your AWS user name and password).
6. You are now connected to the library server! Admin privileges and permission to make changes need to be granted to you by the server administrator.

## How to obtain an SSL Certificate

### Certbot installation

Let’s Encrypt uses a client called Certbot to automate the process of installing SSL. Below are the specific steps to install Certbot:

1. Start by running the following commands to make sure the system has everything it needs and to download and install Certbot:

	`sudo apt-get update`
	
	`sudo apt-get install software-properties-common`
	
	`sudo add-apt-repository universe`
	
	`sudo add-apt-repository ppa:certbot/certbot`
	
	`sudo apt-get update`
	
	`sudo apt-get install certbot python-certbot-apache`

2. Now we are finally ready to get our certificate. Run `sudo certbot --apache`.
3. Your website now has an SSL certificate! SSL certificates from Let's Encrypt are valid for 90 days. You can use `sudo certbot renew` to manually renew your certificate.

## Automatic Renewal

### Using certbot-auto

Once we have our SSL certificate, it will be necessary to make sure the renewal process is automated so that the certificate gets renewed before it expires in 90 days from the installation date.

We will automate the renewal process for the certificates using the certbot-auto file, which should be obtained by running the following command `wget https://dl.eff.org/certbot-auto && chmod a+x certbot-auto` on the server.

Next, we need to move the file to the appropriate location. Use `sudo mv certbot-auto /etc/letsencrypt/`.

To specify the periodicity of the renewal, open the crontab file with `sudo crontab -e` and at the last line of the file (after the text found in the file) add the following: `45 2 * * 6 cd /etc/letsencrypt/ && ./certbot-auto renew && /etc/init.d/apache2 restart`. The numbers at the beginning specify the regularity with which the script will check to see if a renewal is needed. This specific line of code makes sure the certificate gets checked weekly and renewed before the 90 days expiration deadline.

Once this cron job file is modified, we just need to make sure that the permissions are right. Run `chmod 755 certbot-auto`. Additionally, the `certbot-auto` file needs to operate from root. Run `ls -l` to check, and if it's not operating from root, then run `sudo chown root:root certbot-auto`. Now the cron job file should be working fine and updating on a weekly basis.

## Links

* [PuTTY](https://www.putty.org/) - Reference on using the terminal emulator for Windows users.
* [Certbot](https://certbot.eff.org/) - Resource that guides you on the certbot installation process. Select the software and system that matches the server. In my case it was "I'm using `Apache` on `Ubuntu 16.04 (xenial)`".
* [OnePageZen](https://www.onepagezen.com/letsencrypt-auto-renew-certbot-apache/) - Good reference on going though the automatic renewal process.

## Authors

* **Francisco Verón Ferreira** - *Initial work* - [Github](https://github.com/whateveritis)
* **Nabil Kashyap** - *Initial work* - [Github](https://github.com/whateveritis)
