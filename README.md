# Setting up a JupyterHub on AWS

I teach 1-3 day workshops with 10-40 people and getting all the software working is often a stumbling block and hinders students learning and progress. I am experimenting with using a JupyterHub on AWS to provide a virtual coding platform.

I was inspired by this post by someone who uses a JupyterHub for similar kinds of workshops. https://outpw.github.io/tutorials/JupyterHubSetup.html
Follow the instructions in the link aboveto set up your JupyterHub on AWS. My notes give my experience following the instructions and how I customized my hub.

Cost: The free tier, t2.micro, should be fine for set-up. I think you can leave it on without cost since you get 750 hours per month free. For a recent workshop, where we used t2.small and t2.medium, the cost came out to $100 per day for 65 users. But that was on 2i2c.org. 


## Setting up my JupyterHub

Installing TLJH was easy on a t2.micro (but didn't work on non-free instances) and worked seamlessly. Setting up my hub…. Not so much.

[Read my notes on how I successfully set up the hub on the wiki](https://github.com/eeholmes/jupyterhub/wiki)

## Basic steps

1. Follow the [TLJH instructions](https://tljh.jupyter.org/en/latest/install/amazon.html) to install TLJH on a t2.micro instance
2. Wait for 10-15 min, and then try to log into your JH. Use the public IP (you'll find that listed on the AWS dashboard under EC2 instance). `<public.ip>/hub/login` Use your AWS username and your root password
3. Go back to the AWS dashboard. Stop your instance, and go to instance setting and upgrade to t2.small. Restart your instance. You'll have a new public IP.
4. Log into your JH with the new public IP. Copy [environment.yml](https://github.com/NASA-Openscapes/corn/blob/main/ci/environment.yml) into a file at the base level (not in a folder). Name the file `environment.yml`. At the top of the file, change the name to `name: base`
5. Open a terminal (in your JH). Run this code `sudo -E mamba env update -f environment.yml`. Might take awhile to install the environment.
6. Once the new environment is installed (into base), go to the hub main page, click the Admin tab and add some users. They will choose a password when they log in.
7. If you want the default UI to be JupyterLab instead of notebook, run `sudo tljh-config set user_environment.default_app jupyterlab`
8. Go to the wiki to see how to set up connection to GitHub.

Extras

Need to add `harmony-py` to the environment. `pip install -U harmony-py` works but maybe `conda-forge` too.

## To do

Learn how to use nbgitpuller to pull in notebooks into a users JH account
https://tljh.jupyter.org/en/latest/howto/content/nbgitpuller.html#howto-content-nbgitpuller


