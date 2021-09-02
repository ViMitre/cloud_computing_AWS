# Cloud Computing AWS
![diagram1](https://user-images.githubusercontent.com/88156993/131873564-f9b3d906-f8b8-4fba-bde2-3408df043f9f.png)


## Launch instance

Click on **'Launch instances'** on 'Instances' page<br>
Select **'Ubuntu Server 16.04 LTS (HVM), SSD Volume Type'** machine<br>
Keep instance type as it is (t2.micro)<br>
### Configure instance details:
- Change subnet to **'default 1a'**<br>
- Enable auto-assign public IP<br>
- Add storage: keep as it is<br>
### Add tags:
- Add a tag<br>
- Set key to 'Name'<br>
- Set value to 'SRE_\<name\>_app
### Configure security group
- Change SSH rule's Source to 'My IP'<br>
- Add a rule for HTTP to have it accessible from anywhere

## Launch
- Choose **'sre_key'** and click **Launch**

- Make the key read-only:<br>
`chmod 400 sre_key.pem`

- Connect to the instance via ssh:<br>
`ssh -i "sre_key.pem" ubuntu@ec2-52-213-227-200.eu-west-1.compute.amazonaws.com`<br>

Set the machine up:
```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install nginx -y
sudo apt-get install python-software-properties -y
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y
```
Copy the app to the cloud (from own machine):<br>
`scp -ri ~/.ssh/sre_key.pem /app ubuntu@<IP>:/home/ubuntu/`

Go to 'app' directory and finish setting up
```
cd /home/ubuntu/app
sudo npm install pm2 -g -y
sudo npm install
sudo rm /etc/nginx/sites-available/default
sudo nano /etc/nginx/sites-available/default
```
Change nginx config to this:
```
server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```
sudo nginx -t 
sudo systemctl restart nginx
npm start
```
## The web-app should be functional

### Add firewall rule to allow connection to port 3000:
- On **Security** tab, click on appropriate security group<br>
- Edit inbound rules<br>
- Add rule: Custom TCP, port 3000, Source: anywhere<br>
- Save rules






