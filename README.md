# HiJack Tracking
Setup to disable browser tracking on a Mac.
This will intercept calls to tracking websites before they leave your computer by overriding DNS.
The calls will fail unless you have a server set up on your local machine. 

## Repository Structure

# Setup
This setup is for a Mac (verified on Catalina).

1. It's recommended to make a backup of your current hosts file, in case it gets corrupted.

```
cp /etc/hosts ~/hosts
```

2. Download this repository and then run this command in a terminal window to replace your hosts file

```
echo hosts > /etc/hosts
```

* You can augment your current hosts file, if you already have a modified hosts file

```
echo hosts >> /etc/hosts
```

## Add DNS to hosts File
The `/etc/hosts` file provides an easy way to override DNS resolution for domain names. This file is used by macOS prior to other steps as part of DNS resolution, so you're guaranteed to have a quick response while keeping the request contained to your machine.

If you identify a new domain name that's being used to track your actions the domain name can quickly be added to the file by executing this in your terminal

```
echo "127.0.0.1 [domain name]" >> /etc/hosts
```

## SSL/TLS for Secure Communication
You can set up a local web server in order to serve up responses to the requests (e.g., nginx). 
In order to not have warnings in your browser when hijacking these calls you'll need to install certificates.
You can generate certificates by following [Certificates](../../../certificates).

Once you've created your certificates, copy them to your local Apache folder for use by Apache2.

```
sudo cp certs/san.crt /private/etc/apache2/
sudo cp private/san_key.crt /private/etc/apache2/
sudo cp certs/CA.pem /private/etc/apache2/
```

Update your apache config file `httpd.conf` to make sure it enables HTTPS and sets a file location that you're comfortable with.
```
ServerRoot "/Library/WebServer/Documents"
# Secure (SSL/TLS) connections
Include /private/etc/apache2/extra/httpd-ssl.conf
```

Update your apache SSL config file `https-ssl.conf` to make sure it's using your certificates from above.
```
SSLCertificateFile "/private/etc/apache2/san.crt"
SSLCertificateKeyFile "/private/etc/apache2/san_key.crt"
SSLCertificateChainFile "/private/etc/apache2/CA.crt"
```

Start Apache
```
sudo apachectl start
```

