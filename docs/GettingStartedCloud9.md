# Getting Started with Personalens and Cloud9

In order to allow you to experiment with Amazon Personalize in a contained
environment we will use Amazon's cloud based IDE Cloud9. This environment
will serve as your platform for developing and running the Personalens application
as well as a hosting platform for Jupyter notebooks to configure Amazon Personalize.

## Creating a Cloud9 Instance

To begin you will need to create a Cloud9 instance, first open a new browser tab and sign
into your AWS console. 

Next, validate that you are working inside `us-east-1` looking in the top right corner and ensuring the location says
`N. Virginia`, if it does not, click the current location and change it to `N. Virginia`.

Once there, click inside the "Find Services" search box and enter `Cloud9`, then click the service to go to the Cloud9 homepage.

Now click the `Create Environment` button on the right side of the page, this will be an EC2 instance that will house everything other than the content
inside Amazon Personalize and a few database exports placed in Amazon S3.

Enter in any name that you desire as well as a description, then click `Next Step`.

Select `Create a new instance for environment (EC2)` and any instance type you would like to use. If you would like your instance to automatically shut down after a period of 
inactivity to save on costs, select the duration under `Cost-saving setting`. After this click `Next Step`.

Review all of the settings and then click `Create Environment`, this process will take a few minutes to complete. Once it has completed your Cloud9 IDE will load in your browser.

## Configuring Security Group Settings

Each Cloud9 instance running in AWS is powered by an EC2 Instance or virtual machine, you will need to open a few ports in order to view the application
and Jupyter notebooks later.

Open a new tab in your browser and visit https://console.aws.amazon.com/

From this page, again search for `Cloud9` and visit the service page. Click the `View Details` button on this page, note the section for `Security Groups` and
copy the value, for example `sg-076c818588c59b781`.

Click `Services` at the top of the page and search for `EC2` then click it. 

Click the link in the left column titled `Security Groups`. Paste your copied value in the search bar and press enter.

Once selected, click the `Inbound` tab at the bottom. Next, click the `Edit` button.

Add 2 rules with the following settings:

#### Rule 1

Custom TCP Rule, Port Range: 8000, Source: My IP, Description: Django Application

#### Rule 2

Custom TCP Rule, Port Range: 8888, Source: My IP, Description: Jupyter Notebooks

After adding them, click `Save`


## Configuring IAM

You will need an IAM access key in order to communicate with the AWS services inside your Cloud9 environment. Follow the instructions [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_CreateAccessKey)
 to generate a key for your account if you do not have one. Note the values as you will use them when you are working on the Cloud9 environment.

You may now close your tab and go back to the Cloud9 IDE window. You have enabled access for your computer. 

If you change locations and subsequently IP addresses you can always go back to this page and update the rules with your new
connection information.


## Configuring Your Cloud9 Instance for Development

Any commands in this session will be formatted like this:

```bash
pwd
```

These commands should be entered in a terminal session inside your Cloud9 IDE. You can start one by clicking the `+` tab and selecting `New Terminal`.



To begin, deploy the access key you created earlier, assuming your access key ID is `djaflkj3lkjr3rj9` and your secret access key is `380ouTOIH#IUH#KFJ#HKJH#` use the lines
below replacing the acces key ID and secret access key values with your own:

```bash
echo '# AWS Configuration' >> ~/.bashrc
echo 'export AWS_ACCESS_KEY_ID=djaflkj3lkjr3rj9' >> ~/.bashrc
echo 'export AWS_SECRET_ACCESS_KEY=380ouTOIH#IUH#KFJ#HKJH#' >> ~/.bashrc
echo 'export AWS_DEFAULT_REGION=us-east-1' >> ~/.bashrc
```


Now you are ready to clone the code and install its dependencies:

```bash
git clone https://github.com/chrisking/personalens.git
cd personalens
sudo yum install -y python36 python36-devel python36-pip python36-setuptools python36-virtualenv
sudo python3 -m pip install -r requirements.txt  
```

Once complete activate these new values in your environment with:

```bash
source ~/.bashrc
```


Given the movie posters are quite large, they are not placed inside the git repository and must be downloaded separately.

```bash
cd static
wget https://s3.amazonaws.com/chriskingpartnershare/img.zip
unzip img.zip
rm -f img.zip
cp -R img ../data/ml-100k/ 
```

The last thing to define before running the application is the Django configuration for this environment. Paste these lines to finish the configuration


```bash
echo '# Django Config' >> ~/.bashrc
echo 'export PYTHONPATH=.:/home/ec2-user/environment/personalens' >> ~/.bashrc
echo 'export DJANGO_SETTINGS_MODULE=personalens.settings' >> ~/.bashrc
source ~/.bashrc
```

You will need to create a database first and then a super user, do so with the following commands. Any username or passwords are your choice, but save them for later.

```bash
cd ~/environment/personalens
django-admin.py migrate
django-admin.py createsuperuser
```


In order to view your running application you will need to identify your instance's external IP address, to do that:

```bash
curl http://169.254.169.254/latest/meta-data/public-ipv4
```

Then run the server with:

```bash
django-admin.py runserver 0.0.0.0:8000
```

Once it is running, open another browser tab on your computer and visit: `http://the.externa.ip.address:8000`

For example if the curl command returned `54.144.226.110` you would visit: http://54.144.226.110:8000

## Next: Loading Data Into the Database

Continue [here:](LoadingDataIntotheDatabase.md)


