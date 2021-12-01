# Setting up a JupyterHub on AWS

I teach 1-3 day workshops with 10-40 people and getting all the software working is often a stumbling block and hinders students learning and progress. I am experimenting with using a JupyterHub on AWS to provide a virtual coding platform.

I was inspired by this post by someone who uses a JupyterHub for similar kinds of workshops. https://outpw.github.io/tutorials/JupyterHubSetup.html
Follow the instructions in the link aboveto set up your JupyterHub on AWS. My notes give my experience following the instructions and how I customized my hub.

Cost: The free tier, t2.micro, should be fine for set-up. I think you can leave it on without cost since you get 750 hours per month free. For a recent workshop, where we used t2.small and t2.medium, the cost came out to $100 per day for 65 users. But that was on 2i2c.org. 


## Setting up my JupyterHub

Installing TLJH was easy on a t2.micro (but didn't work on non-free instances) and worked seamlessly. Setting up my hub…. Not so much.

[Read my notes on how I successfully set up the hub on the wiki](https://github.com/eeholmes/jupyterhub/wiki)

