##Load balancer solution with Apache

#install apache2
`sudo apt update`
`sudo apt install apache2 -y`
`sudo apt-get install libxml2-dev`

#enable modules
`sudo a2enmod rewrite`
`sudo a2enmod proxy`
`sudo a2enmod proxy_balancer`
`sudo a2enmod proxy_http`
`sudo a2enmod headers`
`sudo a2enmod lbmethod_bytraffic`

#restart and check status apache2
`sudo systemctl restart apache2`
`sudo systemctl status apache2`
![alt text](./images/status apache.PNG)

configure load balancer
#open and add configuraton lines
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

 #restart apache
`sudo systemctl restart apache2`

on webserver 
`sudo tail -f /var/log/httpd/access_log`

on lb server
#open and add records
`sudo vi /etc/hosts`

<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2

#open and update lb config file with names
`sudo vi /etc/apache2/sites-available/000-default.conf`
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1

![alt text](./images/lb.PNG)
  
`curl http://Web1`
`curl http://Web2`
![alt text](./images/curl.PNG)
