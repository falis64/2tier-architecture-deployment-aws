# 2tier-architecture-deployment-aws

We use 2-tiered architecture in order to eliminate the risk of a single poing failure.

# To copy app to your AWS instance, follow the steps below:

1) Open 2 git bash terminal; one for to connect to your EC2 instance and one for your local machine
2) In the local machine type the following command ```scp -i "tech221.pem" -r /Users/Falis/OneDrive/Documents/Virtualisation/app ubuntu@ec2-52-19-182-1.eu-west-1.compute.amazonaws.com:/home/ubuntu/```. The purpose of these commands are:

```scp -i``` - runs a secure copy command

```"tech221.pem"``` - this is telling it to use an ssh key and be inside the .ssh folder

```/Users/Falis/OneDrive/Documents/Virtualisation/app``` - this is the destination to the app folder

``` ubuntu@ec2-52-19-182-1.eu-west-1.compute.amazonaws.com:/home/ubuntu/``` - this provides the destination to the EC2 machine

# Installing required dependencies:

```sudo apt-get install python-software-properties``` - this installs python software properties

```curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -``` - this downloads a newer version of nodejs which in this case is the 12

```sudo apt-get install nodejs -y``` - this installs nodejs on the EC2

```sudo npm install pm2 -g``` - this will install pm2

Before moving on to the next step, make sure your security group allows port 300. You can do this by:

1) Go on your instance and scroll down to the security group 

2) Click on "Edit Inbound Rules"

3) Add a new rule - set the port 3000 and select "IPV4 from anywhere" for the source. Now save the rule

You can install NPM from the app folder location by following the commands below:

1) Cd in to your app folder by

2) Use the command ```npm install``` which installs npm inside the app folder

3) Now use ```NPM start``` to start the NPM

4) Use the command ```node app.js``` to start the app

You should now see this in the terminal:

<img width="256" alt="image" src="https://user-images.githubusercontent.com/129381619/234062834-3c5fcc65-9131-4e9a-861f-f37da33c6975.png">

You can copy the IPv4 address from your instance into the browser to access the app.

<img width="821" alt="image" src="https://user-images.githubusercontent.com/129381619/234063033-10127fa9-8841-4402-a8b7-0508bcbbacd5.png">

# Creating new EC2 Instance for DB

Follow the steps below to create a new EC2 instance for database (DB)

1) Create a new EC2 Instance

2) Select Ubuntu Server 20.04 LTS (HVM), SSD Volume Type for the AMI (you might need to change it to the 18.04 version if this doesn't work for you

3) When selecting security group, edit the inboud rules to reflect below:

- Add SSH connection with "MyIP" rule
- Add "Custom TCP" connection with port "27017" for MondoDP and "IP from anywhere (0.0.0.0)"
- Add "Custom TCP" connection with port "3000" for SpartaApp and "IP from anywhere (0.0.0.0)"

4) Now launch instance

5) In git bash, connect to the instance using ssh connection

6) In order to install Mongodb, run the following commands

```
sudo apt update -y 

sudo apt upgrade -y

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927

echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

sudo apt update -y 

sudo apt upgrade -y

sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20

sudo systemctl start mongod
```
# Connecting App machine and DB machine

1) Open a git bash terminal and cd into the home folder

2) Now use the command ```sudo nano /etc/mongod.conf``` to open a mongod configuration file

3) Scroll all the way down till you see the bindip and change it to 0.0.0.0

4) Press control + x and then y to save the changes and enter to confirm

5) Use the command ```sudo systemctl restart mongod``` to restart mongod

6) Use ```sudo systemctl enable mongod``` to enable the autostart of mongod

7) Use ```sudo systemctl status mongod``` to make sure mongod is running

Now for the App instance:

1) Use cd to go to your home folder

2) Use ```sudo nano .bashrc``` to open to a .bashrc file where you can create an environment variable

3) Once your in there, scroll down and ```export DB_HOST=mongodb://<server>27017/posts```. To break this down:

``export DB_HOST``` creates an environment variable that's called DB_HOST

```mongodb://<server>:27017/posts``` tells the machine to connect to mongodb with the IP adress we have given in our DB instance and to the posts page

4) Now use ```source .bashrc``` to apply the changes we've made

5) cd in to the app folder

6) Use ```node seeds/seed.js``` to seed the data to database

7) Use ```node app.js``` to run the app

8) Copy the instance IP adress into the browser like so ```IPADDRESS/posts.


