# Singularity Example

## Instructions to create the Singularity image and run the container!

1. Pull this Github repo
On a Jetstream instance,
```
git pull https://github.com/breakfast-carpentry/diamondsingularity.git
```
Go into the repo,
```
cd diamondsingularity
```

2. Build the Singularity image
```
sudo singularity build diamond.simg Singularity
```

3. Run the python script with the container.
```
singularity run diamond.simg
```

You can also go into the container and play with it interactively
```
sudo singularity shell diamond.simg
```

You can exit out of the shell by typing `exit` inside the container
```
Singularity diamond.simg:~> exit
```

## Overview of what we did this morning
This morning our goal was to build and run a Singularity image with our own script and data and get output from our code.

- We first used Jetstream (because we have root access there).
- We started with our diamond directory from yesterday - in my case `/home/aglad/diamond_blast`.
- I wrote a toy python script, `script.py`, which just prints "hello":
```
print('Hello')
```
- We then started to write our Singularity recipe. We started with only an empty ubuntu image just to check that we could create that simple image correctly. (to write the Singularity recipe I used a Jupyter text editor so I wouldn't need to use a command line editor)
```
Bootstrap: docker
From: ubuntu
```
- We built the Singularity image and called it diamond.simg.
```
sudo singularity build diamond.simg Singularity
```
- We ran the diamond.simg container interactively so that we could test our setup
```
sudo singularity shell diamond.simg
```
- After we installed python and checked that it worked, we copied those commands into our Singularity recipe:
```
Bootstrap: docker
From: ubuntu

%post
    apt-get update
    apt-get install -y python3
```
- Finally we ran our python script with our Singularity container
```
singularity exec diamond.simg python3 script.py
```
