# Setting up a JupyterHub on AWS

I teach 1-3 day workshops with 10-40 people and getting all the software working is often a stumbling block and hinders students learning and progress. I am experimenting with using a JupyterHub on AWS to provide a virtual coding platform.

I was inspired by this post by someone who uses a JupyterHub for similar kinds of workshops. https://outpw.github.io/tutorials/JupyterHubSetup.html
Follow the instructions in the link aboveto set up your JupyterHub on AWS. My notes give my experience following the instructions and how I customized my hub.

Cost: The free tier, t2.micro, should be fine for set-up. I think you can leave it on without cost since you get 750 hours per month free. For a recent workshop, where we used t2.small and t2.medium, the cost came out to $100 per day for 65 users. But that was on 2i2c.org. 

## Get an AWS account.

1. First you'll need an AWS account.  Record your username and the 12-digit account id. You'll use the 12 digit number in other places.  All the set-up is easy, just follow all the instructions on this link.

    The free tier for an E2C instance gets you 750 hours per month of t2.micro. Seems like it runs unless you shut down your instance. From the AWS console, you can go to billing (upper right under your username) and scroll down to see how many hours you have used.

## Install TLJH (The Littlest Jupyter Hub)

1. I followed these instructions to install The Littlest Jupyter Hub on AWS. There were a few steps but it was all easy to follow.
http://tljh.jupyter.org/en/latest/install/amazon.html

    When I installed TLJH (little jupyterhub), I set-up one user (me) with admin. I forget how. It was obvious.

## AWS Dashboard

Once you have things set-up, you can login to AWS and see the instances that you have running.  The instances are set up on EC2 (virtual servers). I set my instances up on us-west-2. You will see the EC2 service listed on your AWS dashboard, but if you don't, you can get to it from here:
https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2

Key thing that I have had to use is rebooting my instance. I KEEP crashing it and having to reboot, constantly. Then it takes 10-15 min for the JupyterHub to come back online.


## Setting up AWS IAM user.

*I haven't used this yet*

After set-up, you can sign into your AWS account here. You'll want to go to the EC2 services to get your instances.
https://signin.aws.amazon.com/signin

AWS says you shouldn't use your root account for normal admin. You should set up an IAM user.
https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console
Follow instructions above. It is fairly self-explanatory. I set my IAM account for Admin Access. Sign in to the AWS Management Console and open the IAM console at  https://console.aws.amazon.com/iam/
I haven't actually tried using my IAM account yet. It keeps complaining so I have to go back and log in as root.

You can access the AWS console with
 https://<12-digit-account-id>.signin.aws.amazon.com/console

## Logging into your JupyterHub

After installing TLJH, you'll have to wait abit (like 10-15 min) for everything to get installed. Look at your instance (dashboard above) and copy the public IP address. Your JupyterHub address will be
```
<public ip>/hub/login
```
When you login the first time, you'll log in with the admin account that you set up when you installed TLJH.

The default UI is classic Jupyter Notebook. You'll see that in the URL when you are logged in:
```
<public ip>/<username>/tree
```
If you change it to 
```
<public ip>/<username>/lab
```
You'll get the JupyterLab UI.

## Setting up my JupyterHub

Installing TLJH was easy and worked seamlessly. Setting up my hub…. Not so much.

### Environment

Next up I wanted to set-up the environment that all users will have access to with all the packages that they will need.
I didn't find any instructions for this so I adapted these instructions by guessing what would work. And I looked at the conda cheetsheet.
https://medium.com/swlh/installing-jupyter-notebook-and-using-your-own-environment-on-mac-fa41efd4639d

Below is what I did. It did NOT work.

I copied `enivronment.yml` from the NASA workshop and put it in a file called `environment_nasa.yml` on my computer. At the top of the file, I changed the name from openscapes to nasahackathon. I created a folder called environments and uploaded the yml file there.

Then I tried to create the environment. I opened up a terminal and issued this command 
```
sudo -E conda env create --file environments/environment_nasa.yml --name nasahackathon
```
This crashed my instance and gave errors about not being able to solve current_repodata.json. But I can create an environment with `conda create -n myenv` and I can install packages and it collects package data from current_repodata.json and solves the environment just fine. It is just the `conda env create` command that doesn't work.

I tried making a super small environment.yml file:
```
name: mini
dependecies:
   - python
```
Nope. I tried putting environment.yml at the base level and running
```
sudo -E conda env create
```
Nope. Failed.

I tried 
```
sudo -E conda create test python=3.9
```
That worked.

I tried creating an yml file by exporting the test environment creating above and using `conda env create -f test.yml`. That failed with the same error about not being able to collect package metadata from current_repodata.json and then it tries repodata.json and hangs...

At this point, I gave up on creating the environment from a file. And just manually installed packages. It took some trial and error to figure out how to install them. For most
```
sudo -E conda install -c conda-forge <packagename>
```
Worked. But for others I needed to use pip
```
sudo -E pip install <packagename>
```
Pip complained about 'sudo' but I wanted packages available at the root level. `sudo pip install <pkg>` (without the -E) seemed to crash the instance.

## Change default to JupyterLab

I followed the instruction here
https://tljh.jupyter.org/en/latest/howto/env/notebook-interfaces.html
To change the default to be JupyterLab UI. 
```
sudo tljh-config set user_environment.default_app jupyterlab
```
Then reboot the instance from the AWS dashboard.

## Add some users

Follow these instruction to add users. It describes adding admin users, but just uncheck the admin box to add regular users. https://tljh.jupyter.org/en/latest/howto/admin/admin-users.html

### Install jupyterlab-git

Login to the JupyterHub (as admin)

Install jupyterlab-git This is a UI to Git for JupyterLab. This installs Git too.
```
sudo conda install -c conda-forge jupyterlab-git
```

Set-up the GitHub account for a user (not admin). Logout of admin account and login to a regular user account. Then follow the instructions here https://nasa-openscapes.github.io/2021-Cloud-Hackathon/clinic/notebooks.html

1. Create a GitHub token for credentials https://github.com/settings/tokens/new. Copy the token because you won't see it again. You'll use it later.

2. Login to the JupyterHub not at admin and open a terminal (in notebook, it is in the top right under 'new').
```
git config --global user.name "eeholmes"
git config --global user.email "eli.holmes@noaa.gov"
```

3. Set up to store credentials
```
git config --global credential.helper store
```

4. Clone a repo (your OWN repo)
```
git clone <github repo url>
```

5. Open the repo (in JupyterLab say) and make a change to a file. Use the Git UI, to commit the change and push. When it asks for credentials, enter GitHub username and the TOKEN
