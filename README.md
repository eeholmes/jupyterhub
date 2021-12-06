# Running the notebooks with mybinder.org

Anyone can click this badge to open the repo in MyBinder.org. You may have to click it twice because the installation takes awhile and might hang.

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/eeholmes/jupyterhub/main)

This will open the repo in JupyterLab and you can run the workbooks as we did in the workshop.

<img src="images/mybinder.png" width="500"/>

# Setting up a JupyterHub on AWS

I teach 1-3 day workshops with 10-40 people and getting all the software working is often a stumbling block and hinders students learning and progress. I am experimenting with using a JupyterHub on AWS to provide a virtual coding platform.

I was inspired by this post by someone who uses a JupyterHub for similar kinds of workshops. https://outpw.github.io/tutorials/JupyterHubSetup.html
Follow the instructions in the link aboveto set up your JupyterHub on AWS. My notes give my experience following the instructions and how I customized my hub.

Cost: The free tier, t2.micro, should be fine for set-up. I think you can leave it on without cost since you get 750 hours per month free. For a recent workshop, where we used t2.small and t2.medium, the cost came out to $100 per day for 65 users, so ca $1.55 per user per day. But that was on 2i2c.org. I have a t2.small instance (2 Gig RAM, 2 CPU, 30 gig storage) running continuously and it is costing 50 cents per day (so $15 per month). I'll stop it once I am done testing.


## Setting up my JupyterHub

Installing TLJH was easy on a t2.micro (but didn't work on non-free instances) and worked seamlessly. Customizing my hub to match the NASA hackathon environment was unsuccessful until I learned that a) 8 Gig storage was too small, b) 1 Gig RAM (t2.micro) was too small and c) I had to install TLJH on t2.micro and then upgrade to t2.small before doing anything else.

[Read my notes on how I successfully set up the hub on the wiki](https://github.com/eeholmes/jupyterhub/wiki)

## Basic steps

1. Follow the [TLJH instructions](https://tljh.jupyter.org/en/latest/install/amazon.html) to install TLJH on a t2.micro instance (I have not had success in installing on anything other than t2.micro). Set the storage to the max for the free tier (30 Gig).
2. Wait for 10-15 min, and then try to log into your JH. Use the public IP (you'll find that listed on the AWS dashboard under EC2 instance). `<public.ip>/hub/login` Use your AWS username and your root password. Log out.
3. Go back to the AWS dashboard. Stop your instance, and go to instance setting and upgrade to t2.small. Restart your instance. You'll have a new public IP. See note below for setting a fixed IP address.
4. Log into your JH with the new public IP. Copy [environment.yml](https://github.com/NASA-Openscapes/corn/blob/main/ci/environment.yml) into a file at the base level (not in a folder). Note this is the environment used for the NASA hackathon; alter as needed. Name the file `environment.yml`. At the top of the environment file, change the name to `name: base`
5. Open a terminal (in your JH). Run this code `sudo -E mamba env update -f environment.yml`. Might take awhile to install the environment. **Make sure you have at least 16 Gig of storage and have 2 Gig RAM**, i.e. don't try this on the default free tier of t2.micro with 8 Gig of storage.
6. Once the new environment is installed (into base), go to the hub main page, click the Admin tab and add some users. They will choose a password when they log in.
7. Go to the [wiki](https://github.com/eeholmes/jupyterhub/wiki) to see how to set up connection to GitHub.

Optional

*  If you want the default UI to be JupyterLab instead of notebook, run `sudo tljh-config set user_environment.default_app jupyterlab` from your JupyterHub admin account (meaning log into your hub with your admin username).
*  Every time you stop your instance, the IP address will change. You can assign a fixed address easily. See the [wiki page re managing your instance](https://github.com/eeholmes/jupyterhub/wiki/5.-Managing-the-instance)

Extras

Need to add `harmony-py` to the environment. `pip install -U harmony-py` works but maybe `conda-forge` too.

## To do

Learn how to use nbgitpuller to pull in notebooks into a users JH account
https://tljh.jupyter.org/en/latest/howto/content/nbgitpuller.html#howto-content-nbgitpuller


