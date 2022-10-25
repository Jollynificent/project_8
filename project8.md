`sudo apt update`
`sudo apt install apache2 -y`
`sudo apt-get install libxml2-dev`
`sudo a2enmod rewrite`
`sudo a2enmod proxy`
`sudo a2enmod proxy_balancer`
`sudo a2enmod proxy_http`
`sudo a2enmod headers`
`sudo a2enmod lbmethod_bytraffic`
`sudo systemctl restart apache2`
`sudo systemctl status apache2`
`sudo vi /etc/apache2/sites-available/000-default.conf`

<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/

`sudo systemctl restart apache2`

http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php

on webserver 1
`sudo tail -f /var/log/httpd/access_log`

`sudo vi /etc/hosts`
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2

`sudo vi /etc/apache2/sites-available/000-default.conf`
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
`curl http://Web1`
`curl http://Web2`
