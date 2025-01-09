
![image](https://github.com/user-attachments/assets/7eced04e-df78-4b12-8647-a530c728e70b)



# Introduction

Minimalistic subscription page

# Installation

Connect to the server and upload the file:

Russian version:
```
sudo wget -O /var/lib/marzban/templates/subscription/index.html https://raw.githubusercontent.com/indie-master/marzban-subpage/refs/heads/master/subscription/index.html
```
English version:
```
sudo wget -O /var/lib/marzban/templates/subscription/index.html https://raw.githubusercontent.com/jomertix/marzban-tools/master/subscription/index-en.html
```

Enter these commands to automatically specify the file path to the subscription page:
```
echo 'CUSTOM_TEMPLATES_DIRECTORY="/var/lib/marzban/templates/"' | sudo tee -a /opt/marzban/.env
echo 'SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"' | sudo tee -a /opt/marzban/.env
```
Or specify them manually by editing the Marzban .env file 
```
CUSTOM_TEMPLATES_DIRECTORY="/var/lib/marzban/templates/"
SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"
```
Restart marzban
```
marzban restart
```
